<script>
import { trapFocus, removeTrapFocus } from '$lib/trapFocus';
import { page } from '$app/stores';
import ModalTrigger from './modal-trigger.svelte';
import { onMount } from 'svelte';

export let main_nav;

let navLinks = main_nav.items;

let container;

//
// Menu Drawer JS
//
let navIsOpen = false;
let toggleText = 'Open Menu';
let drawerMenuContainer;
let menuDrawerToggle;
let showNavItems = false;

$: navIsOpen;
$: toggleText;
$: showNavItems;

$: pathName = $page.url.pathname;

let toggleMenuDrawer = function (event) {
  navIsOpen = !navIsOpen;

  if (navIsOpen) {
    openMenuDrawer(event, drawerMenuContainer);
  } else {
    event.preventDefault();
    closeMenuDrawer(event, menuDrawerToggle);
  }
};

let openMenuDrawer = function (event, elementToFocus = false) {
  drawerMenuContainer.querySelector('aside').style.height = `calc(101vh - ${
    document.querySelector('header').clientHeight + 'px'
  })`;

  drawerMenuContainer.querySelector('aside').style.top =
    document.querySelector('header').clientHeight + 1 + 'px';

  setTimeout(() => {
    showNavItems = !showNavItems;
  }, 100);

  trapFocus(elementToFocus);
};

let closeMenuDrawer = function (event, elementToFocus = false) {
  navIsOpen = navIsOpen ? !navIsOpen : !!navIsOpen;
  setTimeout(() => {
    showNavItems = !showNavItems;
  }, 0);
  removeTrapFocus(elementToFocus);
  menuDrawerCloseAnim(drawerMenuContainer);
};

let closeSubMenu = function (pElement) {};

let onKeyUp = (event) => {
  if (event.code.toUpperCase() !== 'ESCAPE') return;

  const openDetailsElement = event.target.closest('details[open]');
  if (!openDetailsElement) return;

  openDetailsElement === drawerMenuContainer
    ? closeMenuDrawer(event, menuDrawerToggle)
    : closeSubMenu(openDetailsElement);
};

let menuDrawerCloseAnim = function (pDetailsElement) {
  let animationStart;

  const handleAnimation = (time) => {
    if (animationStart === undefined) {
      animationStart = time;
    }

    const elapsedTime = time - animationStart;

    if (elapsedTime < 400) {
      window.requestAnimationFrame(handleAnimation);
    } else {
      pDetailsElement.removeAttribute('open');
      if (pDetailsElement.closest('details[open]')) {
        trapFocus(
          pDetailsElement.closest('details[open]'),
          pDetailsElement.querySelector('summary')
        );
      }
    }
  };

  window.requestAnimationFrame(handleAnimation);
};

onMount(() => {
  container.querySelectorAll('a').forEach((event) => {
    event.addEventListener('click', (e) => {});
  });
});
</script>

<header bind:this={container} class:menu-opening={navIsOpen}>
  <div class="header__inner">
    <div class="header__logo">
      {#if pathName == '/'}
        <a href="/" title="Maison Matador home" rel="internal">
          <h1 class="header__logo__inner">
            <span class="visually-hidden"> Maison Matador </span>
            <svg role="presentation" aria-hidden="true"
              ><use xlink:href="#maison-logo" /></svg
            >
          </h1>
        </a>
      {:else}
        <a
          class="header__logo__inner"
          href="/"
          title="Maison Matador home"
          rel="internal"
        >
          <span class="visually-hidden"> Maison Matador </span>
          <svg role="presentation" aria-hidden="true"
            ><use xlink:href="#maison-logo" /></svg
          >
        </a>
      {/if}
    </div>

    <div class="header__menu-drawer">
      <details
        id="menu-drawer-container"
        class="header-menu-drawer-container"
        class:menu-opening={navIsOpen}
        class:menu-closing={!navIsOpen}
        on:keyup={onKeyUp}
        bind:this={drawerMenuContainer}
      >
        <summary
          class="header__icon--menu"
          aria-label="Main navigation"
          aria-expanded={navIsOpen}
          data-header-drawer-toggle
          role="button"
          bind:this={menuDrawerToggle}
          on:click={toggleMenuDrawer}
        >
          <span class="header__icon__inner" />
          <span class="header__icon__text visually-hidden" data-toggle-text>
            {#if navIsOpen}
              Close Menu
            {:else}
              Open Menu
            {/if}
          </span>
        </summary>
        <aside tabindex="-1" class="header-menu-drawer">
          <nav class="menu-drawer">
            <div class="menu-drawer__inner">
              <ul
                class="menu-drawer__items"
                class:show-nav-items={showNavItems}
              >
                {#each navLinks as navItem, index}
                  <li
                    class="menu-drawer__item"
                    style="transition-delay: {0.1 * index + 's'}"
                  >
                    {#if navItem.navigationItemUrl.linkType == 'external'}
                      <a
                        on:click={closeMenuDrawer}
                        class="menu-drawer__link"
                        href={navItem.navigationItemUrl.href}
                        target={navItem.navigationItemUrl.blank
                          ? '_blank'
                          : null}
                        rel="external"
                        title="Go to external link at {navItem.navigationItemUrl
                          .href}"
                      >
                        {navItem.text}
                      </a>
                    {/if}

                    {#if navItem.navigationItemUrl.linkType == 'internal'}
                      <a
                        on:click={closeMenuDrawer}
                        class="menu-drawer__link"
                        href={navItem.navigationItemUrl.internalLink.handle
                          .current}
                        rel="internal"
                        title="Go to {navItem.text.toLowerCase()} page"
                      >
                        {navItem.text}
                      </a>
                    {/if}

                    {#if navItem.navigationItemUrl.linkType == 'anchor'}
                      <a
                        on:click={closeMenuDrawer}
                        class="menu-drawer__link"
                        href={navItem.navigationItemUrl.anchorLink}
                        rel="internal"
                        title="Go to {navItem.text.toLowerCase()} section"
                      >
                        {navItem.text}
                      </a>
                    {/if}
                  </li>
                {/each}
              </ul>
            </div>
          </nav>
        </aside>
      </details>
    </div>

    <div class="header__menu-list">
      <nav class="menu-list">
        <div class="menu-list__inner">
          <ul class="menu-list__items" class:show-nav-items={showNavItems}>
            {#each navLinks as navItem, index}
              <li
                class="menu-list__item"
                style="transition-delay: {0.1 * index + 's'}"
              >
                {#if navItem.navigationItemUrl.linkType == 'external'}
                  <a
                    on:click={closeMenuDrawer}
                    class="menu-list__link"
                    href={navItem.navigationItemUrl.href}
                    target={navItem.navigationItemUrl.blank ? '_blank' : null}
                    rel="external"
                    title="Go to external link at {navItem.navigationItemUrl
                      .href}"
                  >
                    {navItem.text}
                  </a>
                {/if}

                {#if navItem.navigationItemUrl.linkType == 'internal'}
                  <a
                    on:click={closeMenuDrawer}
                    class="menu-list__link"
                    href="/{navItem.navigationItemUrl.internalLink.handle
                      .current}"
                    rel="internal"
                    title="Go to {navItem.text.toLowerCase()} page"
                  >
                    {navItem.text}
                  </a>
                {/if}

                {#if navItem.navigationItemUrl.linkType == 'anchor'}
                  <a
                    on:click={closeMenuDrawer}
                    class="menu-list__link"
                    href={navItem.navigationItemUrl.anchorLink}
                    rel="internal"
                    title="Go to {navItem.text.toLowerCase()} section"
                  >
                    {navItem.text}
                  </a>
                {/if}
              </li>
            {/each}
          </ul>
        </div>
      </nav>
    </div>
  </div>
</header>

<style>
/*------------------------------------*\
    	#Header
	\*------------------------------------*/

header {
  border-bottom: 1px solid black;
  padding-top: var(--level3);
  padding-bottom: var(--level3);
  z-index: 100;
  position: fixed;
  width: 100%;
  left: 0;
  right: 0;
  top: 0;
  background-color: var(--tertiary-color);
  transition: all var(--duration-default) cubic-bezier(0.5, 1, 0.89, 1);
}

header.menu-opening {
  background: #fff;
}

@media screen and (min-width: 975px) {
  header {
    padding-top: var(--level4);
    padding-bottom: var(--level4);
  }

  header.menu-opening {
    background-color: var(--tertiary-color);
  }
}

.header__inner {
  padding-left: var(--level4);
  padding-right: var(--level4);
  display: grid;
  grid-template-areas: 'logo navigation';
  grid-template-columns: 2fr 1fr;
  margin-bottom: 0;
}

@media screen and (min-width: 975px) {
  .header__inner {
    max-width: 90rem;
    margin-right: auto;
    margin-left: auto;
    grid-template-columns: 1fr 2fr;
  }
}

.header__logo {
  position: relative;
  max-width: 12rem;
  grid-area: logo;
  justify-self: start;
  width: 100%;
  display: flex;
  align-items: center;
  z-index: 1;
}

@media screen and (min-width: 975px) {
  .header__logo {
    max-width: 13rem;
  }
}

.header__logo a {
  display: block;
  width: 100%;
  transition: all var(--duration-default) cubic-bezier(0.5, 1, 0.89, 1);
}

.header__logo a:hover,
.header__logo a:focus {
  opacity: 0.5;
}

.header__logo__inner {
  position: relative;
  overflow: hidden;
  padding-bottom: 20%;
  width: 100%;
  margin-bottom: 0;
}

.header__logo__inner svg {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  object-fit: cover;
  height: 100%;
  width: 100%;
}

.header__menu-drawer {
  grid-area: navigation;
  justify-self: end;
}

@media only screen and (min-width: 975px) {
  .header__menu-drawer {
    display: none;
  }
}

/*------------------------------------*\
    	#Menu Drawer  
	\*------------------------------------*/

@keyframes headerDrawerOpen {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}

@keyframes headerDrawerClose {
  0% {
    opacity: 1;
  }

  100% {
    opacity: 0;
  }
}

.header-menu-drawer-container.menu-opening .header-menu-drawer {
  animation: headerDrawerOpen 0.3s cubic-bezier(0.5, 1, 0.89, 1) forwards;
  pointer-events: all;
}

.header-menu-drawer-container.menu-closing .header-menu-drawer {
  animation: headerDrawerClose var(--duration-default)
    cubic-bezier(0.11, 0, 0.5, 0) forwards;
  pointer-events: none;
}

.header-menu-drawer-container.menu-closing * {
  transition-delay: 0 !important;
}

.header-menu-drawer {
  position: absolute;
  width: 100vw;
  left: 0;
  right: 0;
  background: white;
  z-index: 0;
  height: 100vh;
  opacity: 0;
}

.menu-drawer__inner {
  padding-left: var(--level5);
  padding-right: var(--level5);
  padding-top: var(--level4);
}

.menu-drawer__item {
  font-size: var(--h4);
  margin-bottom: var(--level3);
  flex-basis: 0;
  opacity: 0;
  will-change: opacity;
  transition: all 0.4s cubic-bezier(0.5, 1, 0.89, 1);
}

.menu-drawer__items.show-nav-items .menu-drawer__item {
  opacity: 1;
}

:global(.menu-drawer__link) {
  position: relative;
  padding: var(--level0);
  padding-right: var(--level3);
  padding-left: var(--level3);
  display: inline-block;
  text-align: center;
  margin-left: calc(var(--level3) * -1);
  background-color: transparent;
  border: none;
}

:global(button.menu-drawer__link) {
  padding-top: var(--level1);
  padding-bottom: var(--level1);
}

:global(.menu-drawer__link):hover:before,
:global(.menu-drawer__link):focus:before,
:global(.menu-drawer__link):active:before {
  opacity: 1;
}

:global(.menu-drawer__link):before {
  content: ' ';
  border: 1px solid black;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  bottom: 0;
  border-radius: 50px;
  position: absolute;
  opacity: 0;
  transition: all var(--duration-default) cubic-bezier(0.5, 1, 0.89, 1);
}

/*------------------------------------*\
    	#Menu Drawer  Icon
	\*------------------------------------*/

.header__icon--menu {
  background: transparent;
  position: relative;
  border: none;
  outline: 0;
  z-index: 1;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 2.5rem;
  height: 3rem;
}

.header__icon--menu:hover {
  cursor: pointer;
}
/* 
	.header__icon--menu.is-close {
		outline: none;
	} */

.header__icon--menu .header__icon__inner {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  opacity: 1;
  transform: scale(1);
  transition: transform 150ms ease, opacity 150ms ease;
  width: 100%;
}

.header__icon--menu:after,
.header__icon--menu .header__icon__inner:after,
.header__icon--menu .header__icon__inner:before {
  content: ' ';
  width: 70%;
  height: 1px;
  display: block;
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  background-color: black;
  margin: auto;
  transition: all var(--duration-default) ease;
}

.header__icon--menu .header__icon__inner:after {
  top: -17px;
}

.header__icon--menu .header__icon__inner:before {
  top: 9px;
}

.header-menu-drawer-container.menu-opening .header__icon--menu:after {
  transform: translateX(-2px);
  opacity: 0;
}

.header-menu-drawer-container.menu-opening .header__icon__inner:after {
  transform: rotate(45deg);
  top: 0;
}

.header-menu-drawer-container.menu-opening .header__icon__inner:before {
  transform: rotate(-45deg);
  top: 0;
}

.menu-list__items {
  display: flex;
  align-items: center;
}

.header__menu-list {
  align-items: center;
  justify-content: space-between;
  grid-area: navigation;
  justify-self: end;
  justify-self: end;
  min-width: 38rem;
  display: none;
}

@media screen and (min-width: 975px) {
  .header__menu-list {
    display: flex;
  }
}

.menu-list {
  width: 86%;
  margin-right: 0;
  margin-left: auto;
}

.menu-list__inner,
.menu-list__items {
  width: 100%;
}

.menu-list__items {
  display: flex;
  justify-content: flex-end;
}

.menu-list__item {
  margin-right: var(--level5);
}

.menu-list__items .menu-list__item:last-child {
  margin-right: 0;
}

:global(.menu-list__link) {
  padding-left: var(--level2);
  padding-right: var(--level2);
  padding-top: var(--level1);
  padding-bottom: var(--level1);
  font-size: var(--h5);
  position: relative;
  background: transparent;
  border: none;
}

:global(.menu-list__link):before {
  content: ' ';
  border: 1px solid black;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  bottom: 0;
  border-radius: 50px;
  position: absolute;
  opacity: 0;
  transition: all var(--duration-default) cubic-bezier(0.5, 1, 0.89, 1);
}

:global(.menu-list__link):hover:before,
:global(.menu-list__link):focus:before {
  opacity: 1;
}
</style>
