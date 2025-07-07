// ==UserScript==
// @name               Auto Skip YouTube Ads
// @name:ar            تخطي إعلانات YouTube تلقائيًا
// @name:es            Saltar Automáticamente Anuncios De YouTube
// @name:fr            Ignorer Automatiquement Les Publicités YouTube
// @name:hi            YouTube विज्ञापन स्वचालित रूप से छोड़ें
// @name:id            Lewati Otomatis Iklan YouTube
// @name:ja            YouTube 広告を自動スキップ
// @name:ko            YouTube 광고 자동 건너뛰기
// @name:nl            YouTube-Advertenties Automatisch Overslaan
// @name:pt-BR         Pular Automaticamente Anúncios Do YouTube
// @name:ru            Автоматический Пропуск Рекламы На YouTube
// @name:vi            Tự Động Bỏ Qua Quảng Cáo YouTube
// @name:zh-CN         自动跳过 YouTube 广告
// @name:zh-TW         自動跳過 YouTube 廣告
// @namespace          https://github.com/tientq64/userscripts
// @version            7.3.100
// @description        Automatically skip YouTube ads instantly. Undetected by YouTube ad blocker warnings.
// @description:ar     تخطي إعلانات YouTube تلقائيًا على الفور. دون أن يتم اكتشاف ذلك من خلال تحذيرات أداة حظر الإعلانات في YouTube.
// @description:es     Omite automáticamente los anuncios de YouTube al instante. Sin que te detecten las advertencias del bloqueador de anuncios de YouTube.
// @description:fr     Ignorez automatiquement et instantanément les publicités YouTube. Non détecté par les avertissements du bloqueur de publicités YouTube.
// @description:hi     YouTube विज्ञापनों को स्वचालित रूप से तुरंत छोड़ दें। YouTube विज्ञापन अवरोधक चेतावनियों द्वारा पता नहीं लगाया गया।
// @description:id     Lewati iklan YouTube secara otomatis secara instan. Tidak terdeteksi oleh peringatan pemblokir iklan YouTube.
// @description:ja     YouTube 広告を即座に自動的にスキップします。YouTube 広告ブロッカーの警告には検出されません。
// @description:ko     YouTube 광고를 즉시 자동으로 건너뜁니다. YouTube 광고 차단 경고에 감지되지 않습니다.
// @description:nl     Sla YouTube-advertenties direct automatisch over. Ongemerkt door YouTube-adblockerwaarschuwingen.
// @description:pt-BR  Pule anúncios do YouTube instantaneamente. Não detectado pelos avisos do bloqueador de anúncios do YouTube.
// @description:ru     Автоматически пропускать рекламу YouTube мгновенно. Не обнаруживается предупреждениями блокировщиков рекламы YouTube.
// @description:vi     Tự động bỏ qua quảng cáo YouTube ngay lập tức. Không bị phát hiện bởi cảnh báo trình chặn quảng cáo của YouTube.
// @description:zh-CN  立即自动跳过 YouTube 广告。不会被 YouTube 广告拦截器警告检测到。
// @description:zh-TW  立即自動跳過 YouTube 廣告。 YouTube 廣告攔截器警告未被偵測到。
// @author             tientq64
// @icon               https://cdn-icons-png.flaticon.com/64/2504/2504965.png
// @match              https://www.youtube.com/*
// @match              https://m.youtube.com/*
// @match              https://music.youtube.com/*
// @exclude            https://studio.youtube.com/*
// @exclude            https://*.youtube.com/shorts/*
// @grant              none
// @license            MIT
// @compatible         firefox
// @compatible         chrome
// @compatible         opera
// @compatible         safari
// @compatible         edge
// @noframes
// @homepage           https://github.com/tientq64/userscripts/tree/main/scripts/Auto-Skip-YouTube-Ads
// ==/UserScript==

function skipAd() {
    ///////////// added beginning
    let warn = document.querySelector('.ytd-enforcement-message-view-model')
    if (warn) {
        warn.hidden = 1;
        setTimeout(()=>{
            warn.remove();
            window.location.reload()
        }, 300);
    }
    ////////////// added ending
    //   if (checkIsYouTubeShorts()) return

    // This element appears when a video ad appears.
    const adShowing = document.querySelector('.ad-showing')

    // Timed pie countdown ad.
    const pieCountdown = document.querySelector('.ytp-ad-timed-pie-countdown-container')

    // Survey questions in video player.
    const surveyQuestions = document.querySelector('.ytp-ad-survey-questions')

    if (adShowing === null && pieCountdown === null && surveyQuestions === null) return

    const moviePlayerEl = document.querySelector('#movie_player')
    let playerEl
    let player

    if (isYouTubeMobile || isYouTubeMusic) {
        playerEl = moviePlayerEl
        player = playerEl
    } else {
        playerEl = document.querySelector('#ytd-player')
        player = playerEl && playerEl.getPlayer()
    }

    if (playerEl === null || player === null) {
        console.log({
            message: 'Player not found',
            timeStamp: getCurrentTimeString()
        })
        return
    }

    // ad.classList.remove('ad-showing')

    let adVideo = null

    if (pieCountdown === null && surveyQuestions === null) {
        adVideo = document.querySelector('.ad-showing video')
        // document.querySelector('#ytd-player video.html5-main-video, #song-video video.html5-main-video')

        console.table({
            message: 'Ad video is playing ...',
            video: adVideo !== null,
            src: adVideo?.src,
            paused: adVideo?.paused,
            currentTime: adVideo?.currentTime,
            duration: adVideo?.duration,
            timeStamp: getCurrentTimeString()
        })

        if (adVideo !== null) {
            adVideo.muted = true
            // added beginning
            adVideo.hidden = 1 // add
            if(adVideo.currentTime > 0.5){ // 0.5
                adVideo.playbackRate = 10 // 10
                if(adVideo.currentTime >= 5.7){ //5.3
                    if(adVideo.currentTime < adVideo.duration - 0.3) adVideo.currentTime = adVideo.duration - 0.3 //  0.4
                    skipAd()
                }
            }
            //// added ending
        }
        if (adVideo === null || !adVideo.src || adVideo.paused || isNaN(adVideo.duration)) {
            return
        }

        console.log({
            message: 'Ad video has finished loading',
            timeStamp: getCurrentTimeString()
        })
    }

    if (isYouTubeMusic && adVideo !== null) {
        if(adVideo.currentTime >= adVideo.duration - 0.4){
            console.table({
                message: 'Ad is playing',
                timeStamp: getCurrentTimeString(),
                adShowing: adShowing !== null,
                pieCountdown: pieCountdown !== null,
                surveyQuestions: surveyQuestions !== null
            })
        }
    } else {
        const videoData = player.getVideoData()
        const videoId = videoData.video_id
        const start = Math.floor(player.getCurrentTime())

        if (moviePlayerEl !== null && moviePlayerEl.isSubtitlesOn()) {
            window.setTimeout(moviePlayerEl.toggleSubtitlesOn, 1000)
        }

        if ('loadVideoWithPlayerVars' in playerEl) {
            playerEl.loadVideoWithPlayerVars({ videoId, start })
        } else {
            playerEl.loadVideoByPlayerVars({ videoId, start })
        }

        console.table({
            message: 'Ad skipped',
            videoId,
            start,
            title: videoData.title,
            timeStamp: getCurrentTimeString(),
            adShowing: adShowing !== null,
            pieCountdown: pieCountdown !== null,
            surveyQuestions: surveyQuestions !== null
        })
    }
}
/*
function checkIsYouTubeShorts() {
    return location.pathname.startsWith('/shorts/')
}
*/

function getCurrentTimeString() {
    return new Date().toTimeString().split(' ', 1)[0]
}

function addCss() {
    const adsSelectors = [
        // Ad banner in the upper right corner, above the video playlist.
        '#player-ads',
        '#panels > ytd-engagement-panel-section-list-renderer[target-id="engagement-panel-ads"]',

        // Masthead ad on home page.
        '#masthead-ad',

        // Sponsored ad video items on home page.
        // 'ytd-ad-slot-renderer',

        // '.ytp-suggested-action',
        '.yt-mealbar-promo-renderer',

        // Featured product ad banner at the bottom left of the video.
        '.ytp-featured-product',

        // Products shelf ad banner below the video description.
        'ytd-merch-shelf-renderer',

        // YouTube Music Premium trial promotion dialog, bottom left corner.
        'ytmusic-mealbar-promo-renderer',

        // YouTube Music Premium trial promotion banner on home page.
        'ytmusic-statement-banner-renderer'
    ]
    const adsSelector = adsSelectors.join(',')
    const css = `${adsSelector} { display: none !important; }`
    const style = document.createElement('style')
    style.textContent = css
    document.head.appendChild(style)
}

/**
 * Remove ad elements using JavaScript because these selectors require the use of the CSS
 * `:has` selector which is not supported in older browser versions.
 */
function removeAdElements() {
    const adSelectors = [
        // Sponsored ad video items on home page.
        // ['ytd-rich-item-renderer', '.ytd-ad-slot-renderer'],

        // ['ytd-rich-section-renderer', '.ytd-statement-banner-renderer'],

        // Ad videos on YouTube Shorts.
        ['ytd-reel-video-renderer', '.ytd-ad-slot-renderer'],

        // Ad blocker warning dialog.
        //['tp-yt-paper-dialog', '#feedback.ytd-enforcement-message-view-model'],

        // Survey dialog on home page, located at bottom right.
        // ['tp-yt-paper-dialog', ':scope > ytd-checkbox-survey-renderer'],

        // Survey to rate suggested content, located at bottom right.
        // ['tp-yt-paper-dialog', ':scope > ytd-single-option-survey-renderer']
    ]
    for (const adSelector of adSelectors) {
        const adEl = document.querySelector(adSelector[0])
        if (adEl === null) continue
        const neededEl = adEl.querySelector(adSelector[1])
        if (neededEl === null) continue
        adEl.remove()
    }
}

const isYouTubeMobile = location.hostname === 'm.youtube.com'
const isYouTubeDesktop = !isYouTubeMobile

const isYouTubeMusic = location.hostname === 'music.youtube.com'
const isYouTubeVideo = !isYouTubeMusic

addCss()

if (isYouTubeVideo) {
    window.setInterval(removeAdElements, 1000) //origin: 1000 no active
    removeAdElements()
}

window.setInterval(skipAd, 500) // origin: 500 no active
skipAd()

//////////
Thank to you. I've repair it and it run OK







## 📰 Introduction

Automatically skip YouTube ads instantly. Undetected by YouTube ad blocker warnings.

立即自动跳过 YouTube 广告。不会被 YouTube 广告拦截器警告检测到。

Tự động bỏ qua quảng cáo YouTube ngay lập tức. Không bị phát hiện bởi cảnh báo trình chặn quảng cáo của YouTube.

## 📑 Changelog

### 7.3.0 - 2025-06-20

-   Mute video player while skipping ads ([#15]).
-   Automatically re-enable subtitles after skipping ads.

### 7.2.0 - 2025-03-09

-   YouTube Music support is back ([#291321], [#12]).

### 7.1.0 - 2025-02-23

-   Experiment.
-   Fixed the issue where the chapters was not displayed on the right side of the video ([#10], [#279783]).

### 7.0.0 - 2025-02-15

-   Version 6 had many bug reports, reverting to the previous version 5 ([#6], [#279168]).

### 6.0.2 - 2025-02-02

-   Disable some unnecessary CSS.

### 6.0.0 - 2025-01-29

_Happy Lunar New Year!_

-   Completely rewritten way to skip ads, more efficient, not detected by YouTube ad blocker warning.
-   New way to skip ads is temporarily not working on YouTube Music.

### 5.3.0 - 2025-01-23

-   Supports older browser versions.

### 5.2.0 - 2025-01-21

-   Support for **YouTube mobile** version 🎉
-   Revisit fix for issue [#2]: Fully resolved the problem where videos couldn't be paused on mobile. The previous fix was incomplete.

### 5.1.3 - 2025-01-18

-   Fix ad skipping issue.

### 5.1.2 - 2025-01-17

-   Fix issue [#2] where video can't be paused on mobile.

### 5.1.1 - 2024-12-27

-   Hide the survey to rate suggested content, located at bottom right.

### 5.1.0 - 2024-12-26

-   Skip pie countdown ads 🎉

### 5.0.0 - 2024-12-25

_Merry Christmas!_

-   **No need to reload the page** when there is no way to skip the ad anymore 🤯
-   Configuration removed, no longer needed.

### 4.8.2 - 2024-12-21

-   Fix timestamp loss when reloading.

### 4.8.1 - 2024-12-03

-   Hide survey dialog on home page.

### 4.8.0 - 2024-11-26

-   The current video's timestamp will be preserved when the page is reloaded ([#267857]).

### 4.7.4 - 2024-11-20

-   Improved ad skipping.

### 4.7.0 - 2024-10-26

-   Add option "Don't reload while the user is busy" in Tampermonkey's menu to avoid reloading page when user is busy doing something, like reading comments, entering text. Enabled by default.

### 4.6.2 - 2024-10-13

-   Improve hiding of ad banners.

### 4.6.0 - 2024-10-07

-   Support skipping ads on **YouTube Music** (PR [#1]).

### 4.5.2 - 2024-09-30

-   Fix Shorts reload infinitely ([#258626], [#259545], [#261679]).

### 4.5.0 - 2024-09-26

-   Add option to enable/disable "Reload the page when there is no other way to skip ads" feature in Tampermonkey's menu. Enabled by default.\
    ![Screenshot-001]

### 4.4.0 - 2024-08-30

-   Automatically reload web page when ad blocker warnings appear.

### 4.3.13 - 2024-08-26

-   Fixed bug where video could not be paused using pause/play key on keyboard or media controls ([#257424]).
-   Improve the performance.

### 4.3.9 - 2024-08-21

-   Fix `@match` invalid syntax ([#256841]).

### 4.3.8 - 2024-08-20

-   Fix the issue of removing ad videos in Shorts.

### 4.3.6 - 2024-08-07

-   Fix bug where video rewinds a segment after skipping an ad ([#254113]).

### 4.3.4 - 2024-08-02

-   Improve the performance.

### 4.2.1 - 2024-07-30

-   Fixed video automatically replay when ended.

### 4.2.0 - 2024-07-30

-   Videos will now no longer occasionally pause due to ad blocker use.
-   Faster ad video skipping speed.

### 4.1.0 - 2024-07-10

-   No need to reload the page when the ad blocker warning dialog appears.

### 4.0.0 - 2024-07-09

-   The page will now reload if an ad blocker warning dialog appears. Because YouTube now pauses the video at first if an ad blocker is detected.
-   Write to the Console every time skip an ad video, etc. Purpose to help debug. To open the Console, press `Ctrl+Shift+J`.

### 3.1.2 - 2024-07-06

-   Playing video after clicking dismiss the ad blocker warning popup.

### 3.1.1 - 2024-07-04

-   Add a few CSS that hides the ads.

### 3.1.0 - 2024-07-02

-   Skip ads faster when the tab is active.
-   Fixed bug when set time to end of ad video without the video duration being available.
-   Change icon.

### 3.0.2 - 2024-06-28

-   Rewriting to only use `setInterval` simplifies things, and fix some bugs.

### 2.1.3 - 2024-06-21

-   Fix `popupContainer` not found error.

### 2.1.0 - 2024-06-20

-   Auto close YouTube's ad blocker warning popup.

### 2.0.1 - 2024-06-19

-   Improved skip ad button detection.
-   Fall back to `setInterval` when `MutationObserver` is not supported.

### 2.0.0 - 2024-06-18

-   Rewrite the entire code, use `MutationObserver` instead of `setInterval`.

### 1.0.0 - 2024-06-17

-   Stable release.

## 💳 Credits

<a href="https://www.flaticon.com/free-icons/youtube" title="youtube icons">Youtube icons created by Ruslan Babkin - Flaticon</a>.

[#15]: https://github.com/tientq64/userscripts/issues/15
[#12]: https://github.com/tientq64/userscripts/issues/12
[#10]: https://github.com/tientq64/userscripts/issues/10
[#6]: https://github.com/tientq64/userscripts/issues/6
[#2]: https://github.com/tientq64/userscripts/issues/2
[#1]: https://github.com/tientq64/userscripts/pull/1
[#291321]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/291321
[#279783]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/279783
[#279168]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/279168
[#267857]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/267857
[#261679]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/261679
[#259545]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/259545
[#258626]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/258626
[#257424]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/257424
[#256841]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/256841
[#254113]: https://greasyfork.org/scripts/498197-auto-skip-youtube-ads/discussions/254113
[Screenshot-001]: https://cdn.jsdelivr.net/gh/tientq64/userscripts/scripts/Auto-Skip-YouTube-Ads/assets/screenshot-001.png
