<!-- 
  Usually, for cleaner code, I'd include the minimum amount of comments 
  to explain certain parts. I also try to implement self documenting code.

  But for the purpose of this challenge, I added many comments to thoroughly
  explain my thought procress, and to provide detailed documentation.
 -->
<script setup lang="ts">
import { defineProps, computed, ref, onMounted, onUnmounted } from 'vue'
import RecipientsBadge from './RecipientsBadge.vue'

const props = defineProps<{
  recipients: string[]
}>()

// Any variable that relates to the DOM will have the el prefix
const elRecipientsDisplay = ref<HTMLElement>()

/**
 * The column width is tracked in a ref so it's reactive.
 * This allows us to leverage Vue's reactivity system to trigger computations
 * when the column width changes (e.g. window resize).
 */
const elRecipientsDisplayWidth = ref<number>()

/**
 * Initially, I used window.addEventListener('resize', ...) but I switched to
 * ResizeObserver for the following benefits:
 *
 * 1. Provides precise decimal values compared to the rounded integers in clientWidth/scrollWidth.
 * 2. Detects resize on specific elements, not limited to window resize. This also makes it future-proof
 *    to any app/design changes.
 * 3. As a bonus, ResizeObserver triggers the handler on initialization, saving us from manually calling it
 *    on "mounted" like we do with addEventListener('resize') (because no "resize" event is emitted).
 *
 * If this were a production codebase, I'd leverage VueUse for conciseness.
 */
let resizeObserver: ResizeObserver
onMounted(() => {
  resizeObserver = new ResizeObserver(([{ contentBoxSize }]) => {
    // Update width
    elRecipientsDisplayWidth.value = contentBoxSize[0].inlineSize
  })

  // Watch elRecipientsDisplay for resize
  resizeObserver.observe(elRecipientsDisplay.value!)
})

onUnmounted(() => {
  resizeObserver.disconnect()
})

/**
 * Ideally, I could assign a ref to RecipientsBadge to access the root element,
 * but it's not exported, so the element is directly queried via DOM.
 * While RecipientsBadge already has class="badge", I added another selector
 * to future-proof it.
 */
const elRecipientBadge = computed(() =>
  elRecipientsDisplay.value?.querySelector<HTMLElement>('.recipient-badge')
)
const elRecipients = ref<HTMLElement>()

/**
 * This function measures the width of the "element" with the given "text".
 * Layout shift is avoided by creating a clone and inserting it as a hidden sibling,
 * which is immediately removed after measurement.
 */
const measureElementWidth = (element: HTMLElement, text: string) => {
  const cloned = element.cloneNode() as HTMLElement
  cloned.textContent = text
  cloned.style.visibility = 'hidden'

  // Prevent layout flow disruption
  cloned.style.position = 'absolute'

  // The clone is inserted as a sibling so it has the same style for accurate measurement
  element.after(cloned)

  // getBoundingClientRect is used for a more precise decimal width
  const { width } = cloned.getBoundingClientRect()
  cloned.remove()
  return width
}

const MORE = ', ...'

/**
 * Compute the visible and hidden recipients.
 * This reacts to changes in both elRecipientsDisplayWidth and props.recipients (in case it changes).
 */
const emails = computed(() => {
  const elWidthElement = elRecipientsDisplayWidth.value
  if (!elWidthElement) {
    return {
      visibleEmails: [],
      hiddenEmails: []
    }
  }

  const [firstEmail, ...hiddenEmails] = props.recipients

  // The first recipient should always be shown
  const visibleEmails = [firstEmail]
  const elEmails = elRecipients.value!

  // Measure width of first recipient
  let visibleEmailsWidth = measureElementWidth(
    elEmails,
    visibleEmails + (hiddenEmails.length > 0 ? MORE : '')
  )

  // If the first recipient hasn't exceeded the container's width, show more recipients
  if (visibleEmailsWidth < elWidthElement) {
    while (hiddenEmails.length > 0) {
      // For each hidden recipient, add it to the array of visible recipients and measure the width
      const newWidth = measureElementWidth(
        elEmails,
        [...visibleEmails, hiddenEmails[0]].join(', ') + (hiddenEmails.length > 1 ? MORE : '')
      )

      // If the width exceeds the column width, don't add recipient and break the loop
      if (newWidth > elWidthElement) {
        break
      }

      // Otherwise, add it to visibleEmails
      visibleEmails.push(hiddenEmails.shift()!)
      visibleEmailsWidth = newWidth
    }

    /**
     * If a hidden recipients badge is rendered, its width must be accounted for
     * to detect if there's an overflow. If there is, hide the last recpient.
     * However, if there is only one visible recipient, leave as is.
     */
    if (hiddenEmails.length > 0 && visibleEmails.length > 1) {
      const badgeWidth = measureElementWidth(elRecipientBadge.value!, '+' + hiddenEmails.length)
      if (visibleEmailsWidth + badgeWidth > elWidthElement) {
        hiddenEmails.unshift(visibleEmails.pop()!)
      }
    }
  }
  return {
    visibleEmails,
    hiddenEmails
  }
})
</script>

<template>
  <div ref="elRecipientsDisplay" class="recipients-display">
    <div ref="elRecipients" class="recipients">
      {{ emails.visibleEmails.join(', ') }}{{ emails.hiddenEmails.length > 0 ? MORE : '' }}
    </div>
    <!-- RecipientsBadge already has class="badge" but we add our own selector for more resilient code -->
    <RecipientsBadge
      class="recipient-badge"
      :class="{ hidden: emails.hiddenEmails.length === 0 }"
      :num-truncated="emails.hiddenEmails.length"
    />
    <div class="recipients-tooltip">
      {{ recipients.join(', ') }}
    </div>
  </div>
</template>

<style scoped>
.recipients-display {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.recipients {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.recipient-badge.hidden {
  /*
  Initially, 'display: none' was used but it created a vertical shift when the visibility was toggled.

  'visibility: hidden' is used instead to reserve dimensions of the badge in the DOM, preventing shifts.
  */
  visibility: hidden;

  /* The width is zero'd so only the vertical height is reserved. */
  width: 0px;
  padding-left: 0;
  padding-right: 0;
}

.recipients-tooltip {
  display: none;
  align-items: center;
  position: fixed;
  top: 8px;
  right: 8px;
  background-color: #666;
  color: #f0f0f0;
  border-radius: 24px;
  padding: 8px 16px;
}

.recipient-badge:hover + .recipients-tooltip {
  display: flex;
}
</style>
