# Privacy note

BrowseKosher checks every page on the phone itself. Nothing about your browsing ever leaves the phone: not the addresses you visit, not page text, not pictures, and not what was blocked or why.

This note lists everything the app does send over the network, and to whom.

## 1. Your browsing

Pages you open are fetched from the sites themselves, as in any browser. Those sites see what they would see from any browser. BrowseKosher adds nothing to those requests, except asking search engines for SafeSearch and YouTube for Restricted Mode.

Address-bar suggestions come from your own history and bookmarks only. What you type is never sent to a search engine until you search.

## 2. Updates

The app downloads signed filter-list and app updates from this repository on GitHub. The requests carry no identifier of the phone or its owner. Like any download, they show the phone's internet address to GitHub, and GitHub counts downloads of each file.

## 3. The check-in service

The app talks to one small service run by BrowseKosher's maker on Cloudflare. It carries only the three things below.

**The phone's keys.** The app derives keys for the phone from Android's app ID for it (`ANDROID_ID`, which is random, different for every app, and changes on a factory reset). Only one-way values derived from it are sent, a different one for the check-in and for the kept settings, so the two can't be matched up; the ID itself never leaves the phone.

**a. A daily check-in.** Never anything you browse or choose in Settings. Once a day the app sends only its version, the Android version, the phone model and its CPU type, so updates and fixes reach the phones the app runs on. Only the latest is kept, with the first and last day it came (no times).

**b. Crash reports.** When the app crashes: the day, the app's version, the Android version, the phone model and the crash's stack trace (the list of places in the app's code where it happened). Never the error message, which could contain an address or text. Crash reports are stored apart from the check-ins, with no phone key, so a crash can't be tied to a phone.

**c. Kept settings (only if "Keep settings after a reset" is on).** The app's settings and PIN (stored as a hash), block lists, allowed sites, site settings and bookmarks, never history. They are encrypted on the phone with a key that never leaves it, so neither the service nor its maker nor Cloudflare can read them. They are used only to put the settings back after the app's data is cleared or the app is reinstalled. When the app starts fresh like that, it asks the service once whether the phone has kept settings, sending only the phone's key. Until it gets an answer, it filters at the default and nothing can be loosened.

**Turning it off.** "Send usage stats and crash reports" (Settings → Privacy and data, on at first) stops the check-ins and crash reports; it is free to turn off. Settings → About → "What this app sends" lists every field. "Keep settings after a reset" is a separate switch; it needs the PIN to turn off, since turning it off loosens the protection.

**What Cloudflare sees.** Cloudflare sees the phone's internet address on each request, as GitHub does for updates. The service doesn't store it.

**Deleting your record.** Turning off "Keep settings after a reset" deletes the kept settings from the service. Turning off check-ins deletes the phone's check-in record. A factory reset gives the phone a new key, after which its old record is never used again.

## 4. Nothing else

No analytics or advertising libraries, no advertising ID, no location, no account. The app never uploads files, pictures or page content.

## On the phone

- History, bookmarks, downloads and the optional block log are kept in an encrypted database. Private tabs write no history.
- The filter remembers sites it has checked under a keyed hash, so its memory can't be read back as a list of sites.
- The block log, if turned on, can be read only with the PIN.
