# Responsive layout

## Mobile

Move layout on textarea focus

- https://www.bram.us/2021/09/13/prevent-items-from-being-hidden-underneath-the-virtual-keyboard-by-means-of-the-virtualkeyboard-api/
- https://medium.com/@sruthisreemenon/avoid-ui-distortions-during-keyboard-display-for-a-mobile-friendly-webpage-86eb99590a13
- https://stackoverflow.com/questions/54575309/how-to-prevent-mobile-keyboard-from-covering-html-input

- scroll the elements into view when focused by touch.
- in chrome actually the keyboard reduces the viewport, not overlays on top
- but looking at average mobiles, it covers roughly half of it.
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Browser_detection_using_the_user_agent#mobile_device_detection
- if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent))
- listen on the touchstart event on form elements and set a variable something like hasTouchKeyboard, and then scroll element to top of page (or at least first half that is likely not covered) when focused if this variable is true.

Partial solution in Vue.js

```JavaScript

methods: {
    deviceHasTouchscreen: function () {
        let hasTouchScreen = false

        if ('maxTouchPoints' in navigator) {
            hasTouchScreen = navigator.maxTouchPoints > 0
        } else if ('msMaxTouchPoints' in navigator) {
            hasTouchScreen = navigator.msMaxTouchPoints > 0
        } else {
            const mQ = matchMedia?.('(pointer:coarse)')
            if (mQ?.media === '(pointer:coarse)') {
                hasTouchScreen = !!mQ.matches
            } else if ('orientation' in window) {
                hasTouchScreen = true // deprecated, but good fallback
            } else {
                // Only as a last resort, fall back to user agent sniffing
                const UA = navigator.userAgent
                hasTouchScreen =
                /\b(BlackBerry|webOS|iPhone|IEMobile)\b/i.test(UA) ||
                /\b(Android|Windows Phone|iPad|iPod)\b/i.test(UA)
            }
        }

        return hasTouchScreen
    },

    const scrollIntoView: function (element) {
        if (this.theme === 'default-2-0' && this.deviceHasTouchscreen()) {
        try {
            // Check if the element is last on the page which we are focusing
            // Then add .keyboard-visible to the current .page to make space for virtual keyboard
            const page = document.querySelector('.page-' + this.pageId)
            const pageElements = page?.querySelector('.page-elements').children

            if (pageElements) {
                const listArray = Array.from(pageElements)

                if (
                    listArray[listArray.length - 1]?.attributes['data-id']?.textContent === this.element.element_id
                ) {
                    const surveyPage = document.querySelector('.page-' + this.pageId)
                    if (surveyPage) {
                    surveyPage.classList.add('keyboard-visible')
                    }
                }
            }
        } catch (err) {
            // Do nothing
        }

        setTimeout(() => {
            element.scrollIntoView({ behavior: 'smooth', block: 'start', inline: 'nearest' })
        }, 100)
        }
    },

    textFieldFocused: function (event) {
      const input = event.target
      input.classList.add('focused')

      const label = document.querySelector('.text-question-label-' + this.element.element_id)
      if (label) {
        label.classList.add('focused')

        this.scrollIntoView(label)
      }

      const descr = document.querySelector('.text-question-description-' + this.element.element_id)
      if (descr) {
        descr.classList.add('focused')
      }

      document.body.classList.add('input-focused')
    },
}
```

## Hover

- https://stackoverflow.com/questions/23885255/how-to-remove-ignore-hover-css-style-on-touch-devices
