<template>
    <div class="nav-header">
        <!-- Search Bar at the very top (only when scrolled) -->
        <div v-if="scrolled" class="header-search-bar-top">
            <SearchBar />
        </div>

        <!-- Language Picker -->
        <div class="language-picker-container">
            <LanguagePicker />
        </div>

        <!-- Main Content -->
        <div class="nav-header-container">
            <!-- Profile Card -->
            <NavProfileCard v-if="data.getProfile()" :profile-data="data.getProfile()"/>

            <!-- Nav Pills -->
            <NavPills class="nav-pills" id="nav-pills" :sections="categorySections" @link-clicked="_onLinkClicked"/>
        </div>

        <!-- Nav Pills: fixed navigation for sections where the header is scrolled outside of the screen bounds. -->
        <div v-if="categorySections.length >= 2" class="nav-pills-fixed-container nav-pills-fixed-container-shrink" id="nav-pills-fixed-container">
            <!-- Pills -->
            <NavPills class="nav-pills-fixed" id="nav-pills-fixed" :sections="categorySections" @link-clicked="_onLinkClicked"/>
        </div>
    </div>
</template>

<script setup>
import {computed, onMounted, onUnmounted, watch, ref} from "vue"
import LanguagePicker from "../../widgets/LanguagePicker.vue"
import NavProfileCard from "../partials/NavProfileCard.vue"
import {useData} from "../../../composables/data.js"
import {useLayout} from "../../../composables/layout.js"
import {useNavigation} from "../../../composables/navigation.js"
import {useUtils} from "../../../composables/utils.js"
import NavPills from "./NavPills.vue"
import SearchBar from '../../widgets/SearchBar.vue'

const data = useData()
const layout = useLayout()
const navigation = useNavigation()
const utils = useUtils()

const emit = defineEmits(['linkClicked'])

const scrolled = ref(false)

const handleScroll = () => {
  scrolled.value = window.scrollY > 50
}

/**
 * @type {ComputedRef<Array>}
 */
const categorySections = computed(() => {
    const activeCategoryId = navigation.getActiveCategoryId()
    return data.getCategorySections(activeCategoryId)
})

/**
 * @private
 */
onMounted(() => {
    window.addEventListener('resize', _update)
    window.addEventListener('scroll', _update)
    window.addEventListener('scroll', handleScroll)
    handleScroll()
    _update()
})

/**
 * @private
 */
onUnmounted(() => {
    window.removeEventListener('resize', _update)
    window.removeEventListener('scroll', _update)
    window.removeEventListener('scroll', handleScroll)
})

/**
 * @private
 */
watch(() => navigation.getActiveSectionId(), () => {
    const navPillsFixedContainer = document.getElementById('nav-pills-fixed-container')
    if(!navPillsFixedContainer)
        return

    navPillsFixedContainer.classList.add('nav-pills-fixed-container-no-transition')
    setTimeout(() => {
        navPillsFixedContainer.classList.remove('nav-pills-fixed-container-no-transition')
    }, 100)
})

/**
 * @private
 */
const _update = () => {
    const navPills = document.getElementById('nav-pills')
    const navPillsFixedContainer = document.getElementById('nav-pills-fixed-container')

    if (!navPills || !navPillsFixedContainer) {
        return
    }

    const isOutsideBounds = layout.isElementOutsideBounds(navPills)
    if(!isOutsideBounds) {
        navPillsFixedContainer.classList.add('nav-pills-fixed-container-shrink')
    }
    else {
        navPillsFixedContainer.classList.remove('nav-pills-fixed-container-shrink')
    }
}

/**
 * @param {Object} section
 * @private
 */
const _onLinkClicked = (section) => {
    emit('linkClicked', section['id'])
}
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.language-picker-container {
    display: flex;
    justify-content: flex-end;
    position: absolute;
    width: 100%;
    padding: 0.5rem;
    z-index: 99;
}

.nav-pills {
    display: flex;
    width: 100%;
    background-color: $nav-background-color;
}

.nav-pills-fixed-container {
    position: fixed;
    z-index: 99;
    top:0;
    width: 100%;
    transition: 0.2s all;

    background-color: $nav-background-color;
    color: $white;
    text-align: center;
}

.nav-pills-page-title {
    display: none;
    text-transform: uppercase;

    @include media-breakpoint-down(sm) {
        display: block;
        padding:0.75rem 0 0.9rem;
    }
}

.nav-pills-fixed-container-shrink {
    @include generate-dynamic-styles-with-hash((
        xxxl: (top: -8rem),
        sm:   (top: -7.5rem)
    ))
}

.nav-pills-fixed-container-no-transition {
    transition:none;
}

.header-search-bar-top {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0.5rem 0 0.5rem 0;
  background: rgba(255,255,255,0.95);
  border-bottom: 1px solid #eee;
  z-index: 1201;
  position: sticky;
  top: 0;
  left: 0;
}

.header-search-bar-top .search-bar {
  width: 100vw;
  max-width: 100vw;
  border-radius: 0;
  font-size: 1.2rem;
  padding-left: 2vw;
  padding-right: 2vw;
  box-sizing: border-box;
  background: #fff;
}
</style>