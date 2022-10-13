<script>
import { MetaTags } from 'svelte-meta-tags';
import { urlFor } from '$lib/sanity';
import { page } from '$app/stores';
import SectionMarquee from '../components/pages/sctn-marquee.svelte';
import SectionHeroHeader from '../components/pages/sctn-hero-header.svelte';
import SectionBrandStatement from '../components/pages/sctn-brand-statement.svelte';
import SectionFeaturedWork from '../components/pages/sctn-featured-work.svelte';
import SectionExpertise from '../components/pages/sctn-expertise.svelte';
import { fade } from 'svelte/transition';
import { onDestroy } from 'svelte';
import { onMount } from 'svelte';
import { cubicOut } from 'svelte/easing';
export let data;

let ready = false;

onDestroy(() => {
  ready = false;
});

onMount(() => (ready = true));
</script>

<svelte:head>
  <MetaTags
    title={data.siteHead.seo.title}
    description={data.siteHead.seo.description}
    canonical="https://www.canonical.ie/"
    siteName={data.siteHead.seo.title}
    openGraph={{
      url: 'https://www.url.ie/a',
      title: data.siteHead.seo.title,
      description: data.siteHead.seo.description,
      images: [
        {
          url: urlFor(data.siteHead.seo.banner_image.asset).width(1200),
          width: 800,
          height: 600,
          alt: data.siteHead.seo.banner_image.alt_text
            ? data.siteHead.seo.banner_image.alt_text
            : 'Missing Alt Text',
        },
      ],
      site_name: data.siteHead.seo.title,
    }}
    twitter={{
      handle: '@MaisonMatador',
      site: '@MaisonMatador',
      cardType: 'summary_large_image',
      title: data.siteHead.seo.title,
      description: data.siteHead.seo.description,
      image: urlFor(data.siteHead.seo.banner_image.asset).width(1200),
      imageAlt: data.siteHead.seo.banner_image.alt_text
        ? data.siteHead.seo.banner_image.alt_text
        : 'Missing Alt Text',
    }}
  />
</svelte:head>

<div>
  {#each data.content.page_layout as section}
    {#if section._type == 'page_hero_header'}
      <SectionHeroHeader {section} />
    {/if}
    {#if section._type == 'page_brand_statement'}
      <SectionBrandStatement {section} />
    {/if}

    {#if section._type == 'page_featured_work_list'}
      <SectionFeaturedWork {section} />
    {/if}

    {#if section._type == 'page_marquee'}
      <SectionMarquee {section} />
    {/if}

    {#if section._type == 'sctn_expertise'}
      <SectionExpertise {...section} />
    {/if}
  {/each}
</div>
