<template>
  <div>
    <right-bar></right-bar>
    <table>
      <tr v-for="(data, index) in xs" v-bind:key='index' v-on:click="onClick(data, index)" v-bind:class="{'table-danger':rowHeight == index && index !== 0}" class="library-rightpanel">
        <td v-for="(field, index) in data" v-bind:key='index'>{{data[index]}}</td>
      </tr>
    </table>
    <nav aria-label="Page navigation example" v-if="this.$store.state.Library.tableType === 'server'">
      <ul class="pagination">
        <li class="page-item" v-for= "(value, index) in page.pageList" v-bind:key="index" v-bind:class="{'disabled':value.page == page.page}" v-on:click="serverPage(value.page)"><a class="page-link" href="#">
          {{value.num}}
        </a></li>
      </ul>
    </nav>
  </div>
</template>

<script>
  import RightBar from './RightBar';
  import { getLibrary } from '../../utils/LibraryServerFile'
  export default {
    components: { RightBar },
    data() {
      return {
        // flag: null
      };
    },
    computed: {
      page: {
        get() {
          return { pageList: this.$store.state.Library.serverTable.pageList, page: this.$store.state.Library.serverTable.page }
        }
      },
      xs: {
        get() {
          let table = []
          switch (this.$store.state.Library.tableType) {
            case 'local': {
              table = this.$store.state.Library.localTable;
              break;
            }
            case 'server': {
              table = this.$store.state.Library.serverTable.data
              break;
            }
            default: {
              break;
            }
          }
          return table
        }
      },
      rowHeight: {
        get() {
          return this.$store.state.Library.rowHeight
        }
      }
    },
    methods: {
      onClick: function (data, index) {
        this.$store.commit('LIBRARY_GET_ROW', index);
        this.$store.commit('LIBRARY_GET_FIELD', data);
        this.$store.commit('LIBRARY_GET_FIELD_INDEX', index);
      },
      serverPage: function (data) {
        const page = parseInt(data, 10)
        this.$store.commit('LIBRARY_SET_TABLE_PAGE', page);
        getLibrary(this, [this.$store.state.System.server, this.$store.state.System.port, this.$store.state.Library.serverTable.tableName, page, this.$store.state.Library.dimensionType, this.$store.state.Library.dimensionServer])
      }
    },
  };
</script>

<style scoped>

</style>
