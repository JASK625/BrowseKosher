# BrowseKosher

An Android web browser that keeps pornography off the screen. Every page and picture is checked on the phone itself, before it is shown. Nothing about your browsing is sent anywhere.

This repository holds the **releases only**: signed APKs, signed filter-list updates and this page. The source code is not public.

## Who it's for

People who want a browser that stays clean: for themselves, for a family member, or for a phone given to a child or teenager. It is built to be paired with a device-owner MDM (see below), which keeps the browser in place and other browsers off the phone.

It blocks pornography and explicit sexual content. News, shopping, social media and everything else work as in any browser. Optional settings go further: blur revealing pictures or every picture of people, block social media, gambling, dating or proxy sites, or your own list of sites.

## Installing

Download the APK for your phone from the [Releases](../../releases) page:

| Your phone | APK |
| --- | --- |
| Almost every phone from the last several years (64-bit) | `…-arm64-v8a.apk` |
| Older or low-cost phones, flip phones (32-bit) | `…-armeabi-v7a.apk` |

If you aren't sure, try the 64-bit one; Android refuses it on a 32-bit phone. A 64-bit phone that has the 32-bit APK is moved to the 64-bit one by its first update.

Needs Android 11 or newer.

**Free space:** about twice the APK's size. Android unpacks the browser engine's libraries when it installs the app, so the installed app takes more room than the download.

### Why the APK is big

Most of it is two things every page needs, and both live on the phone. Sizes as stored in the 32-bit APK (about 162 MB in all):

- **The browser engine, about 86 MB.** BrowseKosher draws pages with GeckoView, Firefox's engine, the same size Firefox's own APK carries.
- **The filter, about 57 MB.** The domain lists (about 30 MB) and the picture and text models (about 28 MB). They are on the phone so that nothing about your browsing has to be sent anywhere to be checked.
- **The app itself, about 11 MB:** its code, screens and pictures.

## The MDM it needs

A browser can only filter what it shows. Someone who can uninstall it, clear its data or open another browser is past it in a few taps. BrowseKosher is meant to be paired with a **device-owner MDM** (a device management app that controls the phone) that applies these policies:

- Block uninstalling BrowseKosher (`com.browsekosher`).
- Block "Clear storage" and force stop for it (Android 11+).
- An app allowlist: no other browsers (disable the phone's own, too) and no torrent apps.
- Block installing apps from unknown sources.
- Disable USB debugging and developer options, safe mode, and adding users or guests.
- Set BrowseKosher as the default browser, so links from other apps open in it.
- Allow only apps that open links in the default browser, rather than showing web pages inside themselves.
- Block factory reset from Settings.

Optional: a filtering DNS (NextDNS, CleanBrowsing and similar) set as the phone's Private DNS and locked. BrowseKosher uses the phone's DNS, so it follows it too.

## How it works

Every page passes a series of checks, cheapest first. Most pages are settled by the first few in a moment, and the results are remembered so the next visit is faster.

**Before a page loads: the lists.** The address, and every address the page then asks for, is checked against a list of about 4.7 million known porn domains, built from public lists. Links to torrents are refused. You can add your own sites, or subscribe to a block list, and turn on ready-made categories (social media, gambling, dating, proxy and VPN sites).

**SafeSearch and site profiles.** Google, Bing, DuckDuckGo, Yahoo and Brave Search always run with SafeSearch on. YouTube runs in Restricted Mode. Reddit's adult communities are blocked, and X opens as text only. Each site can be given a stricter profile: every picture checked, text only, or no downloads.

**Adult signals.** As the page's first bytes arrive, it is checked for the labels adult sites put on themselves: rating tags, age-verification gates, the notices adult sites are required to carry, and explicit words in the title and address. A clear signal blocks the page.

**The text model.** A small model trained for this reads the page's visible text and scores it. Pages it is sure about are blocked. Pages it is unsure about are blocked too, but can be opened as text only, with no pictures or video.

**Pictures held until checked.** On sites the browser doesn't know to be clean, every picture is held back until a picture model on the phone has looked at it: images, backgrounds, video posters and pictures drawn by scripts. Clean ones show, explicit ones stay hidden, unsure ones stay blurred. A page with several explicit pictures is blocked. Optional levels also blur revealing pictures, or every picture with people in it.

**Video.** A video's poster is checked first. While it plays, frames are checked at random moments, so a video can't time its clean frames to the checks. An explicit frame pauses and hides the video and blocks the page.

**PDFs.** PDF files open in BrowseKosher's own viewer, which checks every page as a picture before showing it.

**Downloads.** Each download is checked by its address, its real file type and its first bytes. Pictures are checked before they are saved. Video downloads are off by default. Apps can't be installed from the browser.

**The ad blocker.** Ads are a common way unwanted pictures reach clean sites, so ads and trackers are blocked on every site, from EasyList and EasyPrivacy. Turning it off for a site needs the PIN.

**Fail closed.** If any check fails, times out or can't decide, the content stays hidden. A page that couldn't be checked is not shown.

**The PIN.** Tightening anything is free. Loosening anything (a setting, an allowed site, an unsure picture) needs a PIN, ideally one the person browsing doesn't know. Wrong guesses lock the PIN for longer and longer, and changing the phone's clock doesn't shorten the wait.

**Updates, signed and checked on the phone.** Filter lists and models are updated weekly and the app itself about monthly, all downloaded from this repository. Every update is signed, and the phone checks the signature, the size and the SHA-256 of every file before using it. It refuses anything older than what it has, anything that shrinks the lists a lot, and any app update that isn't signed with the same key. App updates never install by themselves: Android always asks first.

**Keep settings after a reset.** When a PIN is set, the owner can turn this on. The settings and PIN are then encrypted on the phone and a copy kept with the check-in service (see the [privacy note](PRIVACY.md)). The key that unlocks them never leaves the phone, so the service can't read them. If the app's data is cleared or the app reinstalled, it puts everything back, and until it has asked, nothing can be loosened. A factory reset ends it.

### The parts

- **GeckoView** (Mozilla), Firefox's engine, draws every page.
- **Mozilla Android Components** give the tabs, address bar, downloads and menus.
- **The guard**, an extension built into the browser that can't be removed. It sees every request, holds pictures back and reads page text, and asks the filter engine what to do.
- **The filter engine**, on the phone: the lists, the signals, the text and picture models, the caches and the rules. It remembers sites under a keyed hash, not their names, so it keeps no readable record of where you've been.

## Credits

BrowseKosher is built on the work of others:

- [GeckoView and Mozilla Android Components](https://github.com/mozilla-firefox/firefox) (MPL-2.0)
- Domain lists: [HaGeZi DNS blocklists](https://github.com/hagezi/dns-blocklists) (GPL-3.0), [StevenBlack hosts](https://github.com/StevenBlack/hosts) (MIT), [UT1 blacklists](https://dsi.ut-capitole.fr/blacklists/) (Université Toulouse Capitole, CC BY-SA 4.0), the [Tranco](https://tranco-list.eu/) ranking
- Ad rules: [EasyList and EasyPrivacy](https://easylist.to/)
- Picture models: [GantMan nsfw_model](https://github.com/GantMan/nsfw_model) (MIT), [Marqo nsfw-image-detection-384](https://huggingface.co/Marqo/nsfw-image-detection-384) (Apache-2.0), EfficientDet-Lite0 (Apache-2.0)
- [LiteRT](https://ai.google.dev/edge/litert) (Apache-2.0), [SQLCipher](https://www.zetetic.net/sqlcipher/) (BSD)

The full list of libraries and their licenses is on the app's About page.

## Privacy

Nothing about your browsing (addresses, page text, pictures or what was blocked) ever leaves the phone. What the app does send is in the [privacy note](PRIVACY.md).
