<script setup>
import store from '@/store/index'
import { getDataFromAPI } from '@/mixins/GetDataFromAPI'
import { checkHeightContent } from '@/mixins/CheckHeightContent'
import { getNextList } from '@/mixins/GetNextList'
import { showError } from '@/mixins/ShowError'
import { showModalContent } from '@/mixins/ShowModalContent'

</script>

<template>
  <div class="type_button">
    <button class="filter_type_button" :class="{ 'active' : filterActive }" @click="filterType">{{ pokeType.name }}</button>
  </div>
</template>

<script>
export default {
  name: 'PokemonCard',
  mixins: [getDataFromAPI, checkHeightContent, getNextList, showModalContent, showError],
  props: {
    pokeType: JSON
  },
  computed: {
    paginationData() {
      return store.getters['pokemon/getPaginationData']
    },
    filterActive() {
      const filterActive = store.getters['app/getFilterActive']
      let splittedStr = this.pokeType.url.split('/')
      const typeId = splittedStr[splittedStr.length-2]
      return filterActive === parseInt(typeId)
    }
  },  
  methods: {
    async filterType() {
      if (this.filterActive) {
        await store.dispatch('pokemon/LIST', {url: `?limit=${this.paginationData.limit}&offset=0`}, { root: true }).catch(err => { this.showError(err) })
        const mainWrapper = document.querySelector('main')
        const contentWrapper = document.querySelector('content')

        this.checkHeightContent(mainWrapper, contentWrapper)

        this.updateButton(0)
      } else {
        let splittedStr = this.pokeType.url.split('/')
        const typeId = splittedStr[splittedStr.length-2]
        await store.dispatch('pokemonType/TYPE', {url: `/${typeId}`}, { root: true }).then(async response => {
          await store.dispatch('pokemon/LOAD_FILTER_DATA', response.pokemon, { root: true }).catch(err => { this.showError(err) })
          this.updateButton(typeId)
        }).catch(err => { this.showError(err) })
      }
    },
    updateButton(filterId) {
      document.querySelector('dialog').removeAttribute('class')
      document.querySelector('.overlay').classList.remove('show')
      document.querySelector('main').removeAttribute('style')
      document.querySelectorAll('.filter_type_button').forEach(btn => {
        btn.classList.remove('active')
      })
      if (filterId === 0) store.dispatch('app/CLEAR_FILTER', true, { root: true }).catch(err => { this.showError(err) })
      else store.dispatch('app/SET_FILTER', filterId, { root: true }).catch(err => { this.showError(err) })
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.type_button {
  display:inline-block;
  text-align:center;
  margin:10px;
}

.filter_type_button {
  background-color: #f8f9fa;
  border: 1px solid #f8f9fa;
  border-radius: 4px;
  color: #3c4043;
  cursor: pointer;
  font-family: arial,sans-serif;
  font-size: 14px;
  height: 36px;
  line-height: 27px;
  min-width: 54px;
  padding: 0 16px;
  text-align: center;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  white-space: pre;

  &:hover {
    border-color: #dadce0;
    box-shadow: rgba(0, 0, 0, .1) 0 1px 1px;
    color: #202124;
  }

  &:focus {
    border-color: hsla(160, 100%, 37%, 1);
    outline: none;
  }

  &.active {
    background-color: hsla(160, 100%, 37%, 1);
    border-color: hsla(160, 100%, 37%, 1);
    box-shadow: rgba(0, 0, 0, .1) 0 1px 1px;
    color: #202124;
  }
}
</style>
