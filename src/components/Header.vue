<script setup>
import store from '@/store/index'
import { showError } from '@/mixins/ShowError'
import { showModalContent } from '@/mixins/ShowModalContent'
</script>

<template>
  <header>
    <div class="d-flex space-between">
      <div class="d-flex">
        <div class="no-sidebar">
          <button class="btn-clear" @click="openSidebar">
            <vue-feather type="menu"></vue-feather>
          </button>
          <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="20" @click="easter" />
          <span class="self-center">PokeView - Vite</span>
        </div>
        <div class="page-title">{{ currentRouteName }}</div>
      </div>
      <div class="search-container">
        <button class="btn-clear" @click="openFilter">
          <vue-feather type="filter"></vue-feather>
        </button>
      </div>
    </div>
  </header>
</template>

<script>
export default {
  mixins: [showModalContent, showError],
  computed: {
    currentRouteName() {
      return this.$route.name;
    }
  },
  methods: {
    openSidebar() {
      document.getElementById('sidebar').classList.add('show')
      document.querySelector('.overlay').classList.add('show')
      document.querySelector('main').style.overflowY = 'hidden'
    },
    openModal() {
      document.querySelector('dialog').classList.add('d-block', 'normal')
      document.querySelector('.overlay').classList.add('show')
      document.querySelector('main').style.overflowY = 'hidden'
    },
    async openFilter() {
      await store.dispatch('app/SET_FILTER', false, { root: true }).then(() => {
        this.showModalContent()
      }).catch( err => {
        this.showError(err)
      })
      this.openModal()
    },
    async easter() {
      await store.dispatch('app/SET_MESSAGE', '', { root: true }).then( async () => {
        await store.dispatch('app/SET_MESSAGE_DATA', {type: 'success', title: 'Hello There', message: 'You found the easter egg'}, { root: true })
        this.showModalContent()
      }).catch( err => {
        this.showError(err)
      })
      this.openModal()
    }
  }
}
</script>

<style lang="scss" scoped>
  header {
    width: -webkit-fill-available;
    min-height: 55px;
    border-bottom: 2px solid #333333;
    display: flex;
    flex-flow: column;
    justify-content: center;
    padding: 0.7rem;
    background-color: var(--color-background);
    position: fixed;
    z-index: 2;

    .logo {
      margin: 0 .4rem 0 .6rem
    }

    button {
      background-color: transparent;
      background-repeat: no-repeat;
      border: none;
      cursor: pointer;
      overflow: hidden;
      outline: none;
      color: var(--color-heading);
    }

    .d-flex {
      display: flex;
      flex-flow: row;
      align-items: center;
    }
    input.input-search {
      border-radius: 4px;
      min-width: 120px;
      max-width: 180px;
      line-height: 1.5;
    }

    .no-sidebar {
      display: inherit;
      margin-right: .2rem;
      .logo-container {
        margin: auto .6rem auto 1rem;
      }
    }
  }

  @media (min-width: 1024px) {
    .no-sidebar {
      display: none !important;
    }
  }
</style>
