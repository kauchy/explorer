<template>
  <div class="app-container">
    <div v-show="$route.name == 'NodeList'">
      <q-table
        v-loading="listLoading"
        :data="list"
        element-loading-text="拼命加载中"
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(0, 0, 0, 0.8)">
        <template slot-scope="props">
          <q-column :row="props.row" label="id" width="380px">
            <template slot-scope="p">
              <router-link :to="{ name: 'NodeDetail', params: { nodeDetailId: props.row.node_info.id }}" class="primary">{{ props.row.node_info.id }}</router-link>
            </template>
          </q-column>
          <q-column :value="props.row.node_info.moniker" label="moniker" width="100px"/>
          <q-column :value="props.row.node_info.listen_addr" label="listen_addr" width="170px"/>
          <q-column :value="props.row.node_info.network" label="network" width="100px"/>
          <q-column :value="props.row.node_info.version" label="version" width="80px"/>
          <q-column :value="props.row.connection_status.SendMonitor.Start" label="Time"/>
        </template>
      </q-table>
    </div>
    <router-view/>
  </div>
</template>

<script>
import { getNetInfo } from '@/api/net_info'

export default {
  components: {
    qTable: () => import('@/components/common/qtable.vue'),
    qColumn: () => import('@/components/common/qcolumn.vue')
  },
  data() {
    return {
      list: null,
      listLoading: true
    }
  },
  activated() {
    this.fetchData()
  },
  methods: {
    async fetchData() {
      this.listLoading = true
      this.list = []
      const response = await getNetInfo()
      this.list = response.result.peers
      this.$store.dispatch('SaveAllNode', this.list)
      this.listLoading = false
    }
  }
}
</script>

<style lang="scss" scoped>
  @import "src/styles/variables.scss";

  .primary {
    color: $blue;
    text-decoration: underline;
  }

</style>
