# Mihon – Kemono migration build

An unofficial, modified build of [Mihon](https://github.com/mihonapp/mihon), the Android manga reader.

It exists to make moving a library between **Kemono** (`kemono.cr`) and **Pawchive** (`pawchive.pw`) painless.
Both sites archive the same creators and posts, but each shows creator names differently, and the stock app
loses read progress when you migrate between them. This build fixes that and adds a few quality-of-life features.

> Not affiliated with or endorsed by the Mihon project. The Kemono and Pawchive extensions are separate
> (from [keiyoushi/extensions](https://github.com/keiyoushi/extensions)) and are not included here.

## Download

Grab an APK from the [Releases](../../releases) page:

| File | Use it for |
| --- | --- |
| `app-arm64-v8a-nightly.apk` | Almost every modern phone (recommended) |
| `app-armeabi-v7a-nightly.apk` | Older 32-bit phones |
| `app-universal-nightly.apk` | Any device, if you're unsure (bigger file) |
| `app-x86_64-nightly.apk` / `app-x86-nightly.apk` | Emulators / x86 devices |

The app installs **alongside** the official Mihon (package `app.mihon.debug`), so your existing install is not touched.
Restore a Mihon backup into it to bring your library over.

## What's added

### 1. Read progress survives migration
When migrating an entry, read status, bookmarks and fetch dates are now carried over for posts, not just numbered chapters.
- **Post links first:** Kemono and Pawchive use identical post links (`/patreon/user/<id>/post/<id>`), so posts are matched by link.
- **Name + date as a fallback:** posts without a matching link are matched by name *and* posted date.
  Names are compared after normalizing hidden whitespace, case and invisible characters.
- **Duplicate titles are safe:** same-named posts (e.g. several "WIP" posts) are paired one-to-one,
  so reading one doesn't mark them all as read.

The stock app matched only on chapter numbers parsed from names, so post titles without numbers were skipped entirely.

### 2. Creators are found automatically when migrating
Kemono shows a creator's link name (e.g. `quanjiang`) while Pawchive shows their real name (e.g. `犬酱`),
so searching by name never finds them. Migration now looks the creator up by their shared link
(`/patreon/user/<id>`), both from an entry's **Migrate** button and in batch migration. No retyping.

### 3. Choose the name to keep
A new migration option, **Keep current title**:
- **Off:** the entry takes the target site's creator name.
- **On:** it keeps the old name.

### 4. Choose categories when migrating
Untick **Categories** in the migration options and you'll be asked which categories the migrated entries
should go in. For a batch, you pick once and it applies to all entries. Leave it ticked to copy the old categories as before.

### 5. Search chapters inside an entry
A 🔍 button on the entry screen filters the chapter list by title. While searching, "select all"
only selects the visible chapters, so bulk actions never hit hidden ones.

### 6. Add all results to your library
In Browse, after searching or applying a filter, **⋮ → Add all to library** adds every result in one go
and asks which categories to use. Combined with the extensions' **Favorites only** filter, this imports
all of your Kemono / Pawchive favorites at once. It skips entries already in your library and stops at 2,000 entries.

## Tips
- **Favorites import:** log in to the site once inside the app (Browse → source → ⋮ → Open in WebView).
  Your login stays on your phone.
- **Pawchive dates:** Kemono shows a post's *edited* date while Pawchive defaults to *published*.
  If posts without matching links aren't carrying over, set Pawchive's extension setting
  **Preferred Post Date → Edited**.
- Newly added entries show their posts after you open them or run a library update.

## Building it yourself
The changes are in [`changes.patch`](changes.patch), made against Mihon commit
[`972c9d4`](https://github.com/mihonapp/mihon/commit/972c9d4). It needs JDK 21 and the Android SDK.

```bash
git clone https://github.com/mihonapp/mihon.git && cd mihon
git checkout 972c9d4
git apply ../changes.patch
./gradlew assembleNightly
```

APKs end up in `app/build/outputs/apk/nightly/`.

## License
Mihon is licensed under the [Apache License 2.0](LICENSE). This build modifies the original source as
described above; the full set of modifications is in `changes.patch`.
