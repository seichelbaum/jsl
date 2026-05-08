<template>
    <a
        v-if="!disabled"
        @click="onClick"
        id="link"
        class="ma-0 pa-0"
        :class="{ colored: !unstyled, uncolored: unstyled, colorOverride: color }"
    >
        <slot>{{ titleOrHref }}</slot>
    </a>

    <span v-else>
        <slot>{{ titleOrHref }}</slot>
    </span>
</template>

<script setup>
import { computed } from "vue";

import { platform } from "jsl/Platform";
import { Test } from "jsl/Assert";

import { tt, Translatable } from "jsl/Localization";

const props = defineProps({
    href: { type: String, required: true },
    // An optional title to show instead of href
    text: { type: [String, Translatable], default: null },

    // Allows to override the browser's default link color. This is ignored if unstyled is true.
    color: { type: String, default: null },

    // disable any text color styling
    unstyled: { type: Boolean, required: false, default: false },
    // Opens the link externally. In Browsers, this is a new tab.
    tab: { type: Boolean, required: false, default: false },
    // Disables the link. No href will be opened, no click will be emitted.
    disabled: { type: Boolean, required: false, default: false },

    // If true, clicking the logo does not trigger the href link. Only click is emitted.
    clickOnly: { type: Boolean, required: false, default: false },
});

const emit = defineEmits([
    // Triggered on click, even if the href value is nullish
    "click",
]);

const titleOrHref = computed(() => {
    if (props.text) {
        return tt(props.text).toString();
    }
    return props.href;
});

// Handle Clicks
function onClick() {
    if (!props.clickOnly) {
        platform.openLink(props.href, props.tab);
    }
    emit("click");
}
</script>

<style scoped>
#link {
    cursor: pointer;
}

.colored {
    color: -webkit-link;
}

.colorOverride {
    color: v-bind("props.color") !important;
}

.uncolored {
    color: unset;
}
</style>
