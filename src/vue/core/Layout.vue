<template>
    <div class="resume">
        <!-- Navigation (Large Screens) -->
        <div class="sidebar-column" :class="navigation.isSidebarExpanded() ? '' : 'sidebar-column-shrink'">
            <!-- Sidebar -->
            <NavSidebar @link-clicked="_navigateToSection"/>

            <!-- Toggle Button -->
            <NavToggleButton/>
        </div>

        <!-- Content -->
        <div class="content-column" :class="navigation.isSidebarExpanded() ? '' : 'content-column-expand'">
            <!-- Navigation Header (Small Screens) -->
            <NavHeader @link-clicked="_navigateToSection" :show-search="scrolled"/>

            <!-- Website Sections -->
            <slot :scrolled="scrolled" />
        </div>

        <!-- Navigation Category Tabs (Small Screens) -->
        <div class="nav-tabs-column">
            <NavTabs @link-clicked="_navigateToCategory"/>
        </div>
    </div>
</template>

<script setup>
import {useData} from "../../composables/data.js"
import {useLanguage} from "../../composables/language.js"
import {useNavigation} from "../../composables/navigation.js"
import {useRoute, useRouter} from "vue-router"
import {onMounted, onUnmounted, watch} from "vue"
import {useLayout} from "../../composables/layout.js"
import {useUtils} from "../../composables/utils.js"
import SearchBar from '../widgets/SearchBar.vue'
import {ref} from 'vue'

import NavSidebar from "../navigation/default/NavSidebar.vue"
import NavHeader from "../navigation/mobile/NavHeader.vue"
import NavTabs from "../navigation/mobile/NavTabs.vue"
import NavToggleButton from "/src/vue/navigation/partials/NavToggleButton.vue"

const data = useData()
const navigation = useNavigation()
const layout = useLayout()
const language = useLanguage()
const route = useRoute()
const router = useRouter()
const utils = useUtils()
const scrolled = ref(false)

/**
 * @private
 */
onMounted(() => {
    window.addEventListener('resize', _onWindowChangeEvent)
    window.addEventListener('scroll', _onWindowChangeEvent)
    watch(() => route.name, () => { _onWindowChangeEvent() })
    watch(() => language.getSelectedLanguage(), () => { _onLanguageChanged() })
    _onWindowChangeEvent()
    window.addEventListener('scroll', handleScroll)
    handleScroll()
})

/**
 * @private
 */
onUnmounted(() => {
    window.removeEventListener('resize', _onWindowChangeEvent)
    window.removeEventListener('scroll', _onWindowChangeEvent)
    window.removeEventListener('scroll', handleScroll)
})

/**
 * @public
 */
const init = () => {
    const isTouchDevice = utils.isTouchDevice()
    if(navigation.isAllAtOnceMode() && !isTouchDevice) {
        _onWindowChangeEvent()
        layout.instantScrollToElement(route.name, true)
    }
}

/**
 * Triggered whenever there's a change on the scroll status / windows size.
 * @private
 */
const _onWindowChangeEvent = () => {
    if(!route.name)
        return

    // current navigation status...
    const isNavigationModeAllAtOnce = navigation.isAllAtOnceMode()
    const activeSectionId = navigation.getActiveSectionId()

    navigation.update(route.name)

    // checks if there was a change on the navigation mode...
    if(isNavigationModeAllAtOnce !== navigation.isAllAtOnceMode()) {
        _onNavigationModeChanged(activeSectionId)
    }
}

/**
 * Triggered whenever the user selects a new language.
 * @private
 */
const _onLanguageChanged = () => {
    const activeSectionId = navigation.getActiveSectionId()
    if(navigation.isAllAtOnceMode()) {
        layout.instantScrollTo(window.scrollY - 100, true)
        layout.smoothScrollToElement(activeSectionId, true)
    }
}

/**
 * Triggered whenever there's a navigation mode change after a window resize.
 * @private
 * @param {string} previousActiveSectionId
 */
const _onNavigationModeChanged = (previousActiveSectionId) => {
    const feedbackView = layout.getFeedbackView()

    if(!utils.isTouchDevice() && feedbackView) {
        feedbackView.showActivitySpinner(data.getString('loading') + "...")
        setTimeout(() => {
            feedbackView.hideActivitySpinner()
        }, 300)
    }

    if(navigation.isOneAtOnceMode()) {
        router.push({name: previousActiveSectionId})
        layout.instantScrollTo(0, true)
    }
    else {
        layout.instantScrollToElement(previousActiveSectionId, true)
    }
}

/**
 * @param {String} sectionId
 * @private
 */
const _navigateToSection = (sectionId) => {
    navigation.registerSectionVisit(sectionId)
    if(navigation.isAllAtOnceMode()) {
        layout.smoothScrollToElement(sectionId, false)
        return
    }

    const routeName = route.name
    router.push({name: sectionId})
    navigation.update(routeName)
}

/**
 * @param {String} categoryId
 * @private
 */
const _navigateToCategory = (categoryId) => {
    const targetSectionId = navigation.getLastVisitedSectionOn(categoryId)
    _navigateToSection(targetSectionId)
}

const handleScroll = () => {
    scrolled.value = window.scrollY > 0
}

defineExpose({
    init
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.resume {
    display: flex;
    min-height: 100vh;
    background-color: $nav-background-color;
    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        background-color: $background-color;
    }
}

.sidebar-column {
    width: $nav-sidebar-column-size;
    min-height: 100vh;

    background-color: $dark;
    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        display: none;
    }
}

.sidebar-column-shrink {
    width: $nav-sidebar-column-size-shrink;
    transition: $nav-sidebar-transition;
}

.content-column {
    width: calc(100vw - $nav-sidebar-column-size);
    min-height: 100vh;

    background-color: $background-color;
    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        width: 100vw!important;
    }
}

.content-column-expand {
    width: calc(100vw - $nav-sidebar-column-size-shrink);
    transition: $nav-sidebar-transition;
}

.nav-header, .nav-tabs-column {
    display: none;
    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        display: block;
    }
}

.nav-tabs-column {
    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        position: fixed;
        bottom: 0;
        left: 0;
        z-index: 99;
    }
}

.centered-search-bar {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 1000;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  pointer-events: none;
}
.centered-search-bar > * {
  pointer-events: auto;
}
</style>