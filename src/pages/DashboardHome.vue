<template>
    <transition name="slide-fade" appear>
        <div v-if="$route.name === 'DashboardHome'">
            <h1 class="mb-3">
                {{ $t("Quick Stats") }}
            </h1>

            <div class="shadow-box big-padding text-center mb-4">
                <div class="row">
                    <div class="col">
                        <h3>{{ $t("Up") }}</h3>
                        <span class="num">{{ $root.stats.up }}</span>
                    </div>
                    <div class="col">
                        <h3>{{ $t("Down") }}</h3>
                        <span class="num text-danger">{{ $root.stats.down }}</span>
                    </div>
                    <div class="col">
                        <h3>{{ $t("Maintenance") }}</h3>
                        <span class="num text-maintenance">{{ $root.stats.maintenance }}</span>
                    </div>
                    <div class="col">
                        <h3>{{ $t("Unknown") }}</h3>
                        <span class="num text-secondary">{{ $root.stats.unknown }}</span>
                    </div>
                    <div class="col">
                        <h3>{{ $t("pauseDashboardHome") }}</h3>
                        <span class="num text-secondary">{{ $root.stats.pause }}</span>
                    </div>
                </div>
            </div>

            <div class="shadow-box table-shadow-box" style="overflow-x: hidden;">
                <div v-if="$root.selectedDevice">
                    <table class="table table-borderless table-hover">
                    <thead>
                        <tr>
                            <th>{{ $t("Name") }}</th>
                            <th>{{ $t("Tag") }}</th>
                            <th>{{ $t("Status") }}</th>
                            <th>{{ $t("DateTime") }}</th>
                            <th>{{ $t("Message") }}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item, index) in displayedRecords" :key="index" :class="{ 'shadow-box': $root.windowWidth <= 550}">
                            <td><router-link :to="`/dashboard/${item.id}`">{{ item.name }}</router-link></td>
                            <td>
                                <div class="tags">
                                    <Badge v-for="tag in item.tags" :key="tag" :item="tag" :size="'sm'"/>
                                </div>
                            </td>
                            <td><Status :status="item.beat[0].status" /></td>
                            <td :class="{ 'border-0':! item.beat[0].msg}"><Datetime :value="item.beat[0].time" /></td>
                            <td class="border-0">{{ item.beat[0].msg }}</td>
                        </tr>
                        <tr v-if="importantHeartBeatList.length === 0">
                            <td colspan="4">
                                {{ $t("No important events") }}
                            </td>
                        </tr>
                    </tbody>
                </table>
                </div>
                <div v-else>
                    <p>Select system to see details.</p>
                </div>

            </div>
        </div>
    </transition>
    <router-view ref="child" />
</template>

<script>
import Status from "../components/Status.vue";
import Datetime from "../components/Datetime.vue";
import Badge from "../components/Badge.vue";
import Pagination from "v-pagination-3";

export default {
    components: {
        Datetime,
        Status,
        Pagination,
        Badge,
    },
    data() {
        return {
            page: 1,
            perPage: 25,
            heartBeatList: [],
            paginationConfig: {
                hideCount: true,
                chunksNavigation: "scroll",
            },
            filteredList: [],
        };
    },
    computed: {

        importantHeartBeatList() {
            let result = [];

            for (let monitorID in this.$root.monitorList) {
                let list = this.$root.monitorList[monitorID];
                let beat = this.$root.importantHeartbeatList[monitorID];
                result = result.concat({...list, beat});
            }

            for (let beat of result) {
                let monitor = this.$root.monitorList[beat.monitorID];

                if (monitor) {
                    beat.name = monitor.name;
                }
            }

            result.sort((a, b) => {
                if (a.time > b.time) {
                    return -1;
                }

                if (a.time < b.time) {
                    return 1;
                }

                return 0;
            });

            // eslint-disable-next-line vue/no-side-effects-in-computed-properties
            this.heartBeatList = result;

            return result;
        },

        displayedRecords() {
            if (this.$root.selectedDevice !== "") {
                const loweredSelectedLocation = this.$root.selectedDevice.toLowerCase();
                this.filteredList = this.heartBeatList.filter(monitor => {
                    return monitor.name.toLowerCase() == loweredSelectedLocation ? monitor : null;
                });
            }
            return this.filteredList;
        },
    },
};
</script>

<style lang="scss" scoped>
@import "../assets/vars";

.num {
    font-size: 30px;
    color: $primary;
    font-weight: bold;
    display: block;
}

.shadow-box {
    padding: 20px;
}

table {
    font-size: 14px;

    tr {
        transition: all ease-in-out 0.2ms;
    }

    @media (max-width: 550px) {
        table-layout: fixed;
        overflow-wrap: break-word;
    }
}
</style>
