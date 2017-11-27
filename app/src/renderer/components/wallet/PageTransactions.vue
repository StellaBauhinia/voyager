<template lang="pug">
page(title='Transactions')
  div(slot="menu"): tool-bar
    a(@click='setSearch(true)')
      i.material-icons search
      .label Search

  modal-search(v-if="filters.transactions.search.visible" type="transactions")

  part(title="All Transactions")
    card-transaction(
      v-for="i in filteredTransactions"
      :transaction-value="i")
    list-item(v-if='filteredTransactions.length === 0' dt="N/A" dd="None Available")
</template>

<script>
import { mapGetters } from 'vuex'
import { includes, orderBy } from 'lodash'
import Mousetrap from 'mousetrap'
import AnchorCopy from '../common/AnchorCopy'
import Btn from '@nylira/vue-button'
import ListItem from '../common/NiListItem'
import CardTransaction from './CardTransaction'
import ModalSearch from '../common/NiModalSearch'
import Page from '../common/NiPage'
import Part from '../common/NiPart'
import ToolBar from '../common/NiToolBar'
export default {
  name: 'page-transactions',
  components: {
    AnchorCopy,
    Btn,
    CardTransaction,
    ListItem,
    ModalSearch,
    Page,
    Part,
    ToolBar
  },
  computed: {
    ...mapGetters(['filters', 'transactions']),
    filteredTransactions () {
      let query = this.filters.transactions.search.query
      let list = orderBy(this.transactions, ['id', 'desc'])

      if (this.filters.transactions.search.visible) {
        return list.filter(i => includes(i.id.toLowerCase(), query))
      } else {
        return list
      }
    }
  },
  methods: {
    setSearch (bool) {
      this.$store.commit('setSearchVisible', ['transactions', bool])
    }
  },
  mounted () {
    Mousetrap.bind(['command+f', 'ctrl+f'], () => this.setSearch(true))
    Mousetrap.bind('esc', () => this.setSearch(false))
  }
}
</script>