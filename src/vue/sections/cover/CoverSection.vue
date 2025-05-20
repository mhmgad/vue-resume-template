<template>
    <SectionTemplate :section-data="props.sectionData">
      

        <!-- Title -->
        <h1 class="cover-title display-1" v-html="coverTitle"/>

        <!-- Search Bar under welcome message -->
        <div :class="['cover-search-bar', { 'cover-search-bar-scrolled': scrolled }]">
            <SearchBar :large="!scrolled" />
        </div>

        <!-- Divider -->
        <hr class="solid-divider ms-1 me-1">

        <!-- Info Items -->
        <InlineList class="info-list" :items="props.sectionData.content['items']['contactListItems']"/>

        <!-- Description -->
        <p class="cover-description lead text-normal mb-4 mb-md-5"
           v-html="props.sectionData.content['locales']['bio']"/>

        <!-- Social Links -->
        <SocialLinks :items="props.sectionData.content['items']['socialCircles']"/>
    </SectionTemplate>
</template>

<script setup>
import SectionTemplate from "../_templates/SectionTemplate.vue"
import {computed} from "vue"
import {useData} from "../../../composables/data.js"
import {useNavigation} from "../../../composables/navigation.js"
import InlineList from "../../widgets/InlineList.vue"
import SocialLinks from "../../widgets/SocialLinks.vue"
import SearchBar from '../../widgets/SearchBar.vue'

const data = useData()
const navigation = useNavigation()

/**
 * @property {Object} sectionData
 */
const props = defineProps({
    sectionData: Object,
    scrolled: Boolean
})

/**
 * @type {ComputedRef<String>}
 */
const coverTitle = computed(() => {
    if(navigation.isAllAtOnceMode()) {
        return props.sectionData.content['locales']['welcome']
    }
    else {
        return props.sectionData.content['locales']['welcomeShort']
    }
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.cover-search-bar {
    display: flex;
    justify-content: left;
    align-items: left;
    width: 100%;
    margin-top: 2rem;
    margin-bottom: 2rem;
    position: relative;
    z-index: 10;
    transition: all 0.3s cubic-bezier(.4,2,.6,1);

    .search-bar {
        font-size: 1.5rem;
        padding: 1rem 1.5rem;
        width: 100%;
        border-width: 2px;
        border-radius: 1rem;
        transition: all 0.3s cubic-bezier(.4,2,.6,1);
    }
}

.cover-search-bar-scrolled {
    justify-content: flex-end;
    align-items: flex-start;
    margin-top: 0;
    margin-bottom: 0;
    position: fixed;
    top: 1.2rem;
    right: 2.5rem;
    width: auto;
    z-index: 1200;
    background: none;
    .search-bar {
        font-size: 1.1rem;
        padding: 0.5rem 1.2rem;
        width: 220px;
        border-width: 1px;
        border-radius: 1.2rem;
    }
}

.cover-title {
    margin-bottom: 1rem;
    text-transform: uppercase;
    font-weight: bold;
}

.solid-divider {
    @include media-breakpoint-up($navigation-sidebar-breakpoint) {
        display: none;
    }
}

.info-list {
    @include generate-dynamic-styles-with-hash((
        xxxl: (margin-bottom: 2.5rem),
        lg: (margin-bottom: 2rem),
        md: (margin-bottom: 1.2rem)
    ))
}
</style>