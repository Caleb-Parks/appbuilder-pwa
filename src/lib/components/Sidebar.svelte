<!--
@component
The sidebar/drawer.
-->
<script>
    import {
        AccountIcon,
        SearchIcon,
        BibleIcon,
        HistoryIcon,
        BookmarkIcon,
        NoteIcon,
        HighlightIcon,
        ShareIcon,
        SettingsIcon,
        TextAppearanceIcon,
        AboutIcon
    } from '$lib/icons';
    import { base } from '$app/paths';
    import config from '$lib/data/config';
    import { firebaseConfig } from '$lib/data/firebase-config';
    import {
        s,
        t,
        themeColors,
        language,
        languageDefault,
        showDesktopSidebar,
        direction,
        modal,
        MODAL_TEXT_APPERANCE,
        MODAL_COLLECTION
    } from '$lib/data/stores';
    const drawerId = 'sidebar';
    let menuToggle = false;

    function closeDrawer() {
        menuToggle = false;
    }
    function closeOnEscape(event) {
        event.key === 'Escape' && closeDrawer();
    }

    const menuItems = config?.menuItems;
    const showLayouts =
        config.mainFeatures['layout-config-change-nav-drawer-menu'] &&
        config.bookCollections.length > 1;
    const showSearch = config.mainFeatures['search'];
    const showHistory = config.mainFeatures['history'];
    const showBookmarks = config.mainFeatures['annotation-bookmarks'];
    const showNotes = config.mainFeatures['annotation-notes'];
    const showHighlights = config.mainFeatures['annotation-highlights'];
    const showShare =
        config.mainFeatures['share-app-link'] ||
        config.mainFeatures['share-download-app-link'] ||
        config.mainFeatures['share-apk-file'] ||
        config.mainFeatures['share-apple-app-link'];
    const showAccount = firebaseConfig && config.mainFeatures['user-accounts'];

    $: textColor = $s['ui.drawer.item.text']['color'];
    $: iconColor = $s['ui.drawer.item.icon']['color'];
    $: contentBackgroundColor = $s['ui.background']['background-color'];
    $: drawerBackgroundColor = $s['ui.drawer']['background-color'];
    $: contentTextColor = $themeColors['TextColor'];
</script>

<svelte:window on:keydown={closeOnEscape} />

<div
    class="dy-drawer"
    class:dy-drawer-mobile={$showDesktopSidebar}
    class:dy-drawer-end={$direction === 'rtl'}
>
    <input id={drawerId} type="checkbox" class="dy-drawer-toggle" bind:checked={menuToggle} />
    <div class="dy-drawer-content flex flex-col" style:background-color={contentBackgroundColor}>
        <!-- Page content here -->
        <slot />
    </div>
    <div class="dy-drawer-side" on:click={closeDrawer} on:keydown={closeDrawer}>
        <div class="dy-drawer-overlay" />
        <ul
            class="dy-menu p-1 w-3/4 sm:w-80 text-base-content"
            style:background-color={drawerBackgroundColor}
            style:direction={$direction}
        >
            <!-- Sidebar content here -->
            <a class="fill" href="{base}/">
                <picture>
                    <source srcset="{base}/images/nav_drawer@2x.png 2x" />
                    <img
                        src="{base}/images/nav_drawer.png"
                        alt="Drawer Header"
                        style="width:auto;"
                    />
                </picture>
            </a>
            {#if showAccount}
                <li>
                    <a href="{base}/account" style:color={textColor}>
                        <AccountIcon color={iconColor} />{$t['Account_Page_Title']}
                    </a>
                </li>
            {/if}
            {#if showSearch}
                <li>
                    <a href="{base}/search" style:color={textColor}>
                        <SearchIcon color={iconColor} />{$t['Menu_Search']}
                    </a>
                </li>
            {/if}
            {#if showLayouts}
                <li>
                    <!-- svelte-ignore a11y-missing-attribute -->
                    <button
                        style:color={textColor}
                        class="btn"
                        on:click={() => modal.open(MODAL_COLLECTION)}
                    >
                        <BibleIcon color={iconColor} />{$t['Menu_Layout']}
                    </button>
                </li>
            {/if}
            {#if showAccount || showSearch || showLayouts}
                <div class="dy-divider m-1" />
            {/if}
            {#if showHistory}
                <li>
                    <a href="{base}/history" style:color={textColor}>
                        <HistoryIcon color={iconColor} />{$t['Menu_History']}
                    </a>
                </li>
            {/if}
            {#if showBookmarks}
                <li>
                    <a href="{base}/bookmarks" style:color={textColor}>
                        <BookmarkIcon color={iconColor} />{$t['Annotation_Bookmarks']}
                    </a>
                </li>
            {/if}
            {#if showNotes}
                <li>
                    <a href="{base}/notes" style:color={textColor}>
                        <NoteIcon color={iconColor} />{$t['Annotation_Notes']}
                    </a>
                </li>
            {/if}
            {#if showHighlights}
                <li>
                    <a href="{base}/highlights" style:color={textColor}>
                        <HighlightIcon color={iconColor} />{$t['Annotation_Highlights']}
                    </a>
                </li>
            {/if}
            {#if showHistory || showBookmarks || showNotes || showHighlights}
                <div class="dy-divider m-1" />
            {/if}
            {#if showShare}
                <li>
                    <a href="{base}/share" style:color={textColor}>
                        <ShareIcon color={iconColor} />{$t['Menu_Share_App']}
                    </a>
                </li>
                <div class="dy-divider m-1" />
            {/if}
            <li>
                <a href="{base}/settings" style:color={textColor}>
                    <SettingsIcon color={iconColor} />{$t['Menu_Settings']}
                </a>
            </li>
            <!-- svelte-ignore a11y-missing-attribute -->
            <li>
                <button
                    style:color={textColor}
                    class="btn"
                    on:click={() => modal.open(MODAL_TEXT_APPERANCE)}
                >
                    <TextAppearanceIcon color={iconColor} />{$t['Menu_Text_Appearance']}
                </button>
            </li>
            <div class="dy-divider m-1" />
            {#if menuItems}
                {#each menuItems as item}
                    <li>
                        <a
                            href={item.link['default']}
                            style:color={textColor}
                            target="_blank"
                            rel="noreferrer"
                        >
                            <picture>
                                <source
                                    srcset="{base}/icons/menu-items/{item.images[1]
                                        .file} 2x, {base}/icons/menu-items/{item.images[2].file} 3x"
                                />
                                <img
                                    src="{base}/icons/menu-items/{item.images[0].file}"
                                    height="24"
                                    width="24"
                                    alt={item.title[$language] || item.title[languageDefault]}
                                />
                            </picture>{item.title[$language] || item.title[languageDefault]}
                        </a>
                    </li>
                {/each}
            {/if}
            <li>
                <a href="{base}/about" style:color={textColor}>
                    <AboutIcon color={iconColor} />{$t['Menu_About']}
                </a>
            </li>
        </ul>
    </div>
</div>

<style>
    .dy-menu {
        position: absolute;
        top: 4rem;
        min-height: calc(100vh - 4rem);
        min-height: calc(100dvh - 4rem);
    }
    /* fixes the text items in rtl so that they are next to the icon */
    .dy-menu li {
        flex-direction: row;
    }
    a {
        text-decoration: none;
    }
    .fill {
        display: flex;
        justify-content: center;
        align-items: center;
        overflow: hidden;
    }
    .fill img {
        flex-shrink: 0;
        min-width: 100%;
        min-height: 100%;
    }
</style>
