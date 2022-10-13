<script>
	import { urlFor } from '$lib/sanity';
	import { MetaTags } from 'svelte-meta-tags';
	import { page } from '$app/stores';
	import SctnPageProjectList from '../../components/pages/sctn-proj-list.svelte';
	import SectionFeaturedWork from '../../components/pages/sctn-featured-work.svelte';
	import SectionJobsList from '../../components/pages/sctn-jobs-list.svelte';
	import { fade } from 'svelte/transition';
	import { onDestroy, onMount } from 'svelte';
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
		title="{data.content.seo.title}"
		description="{data.content.seo.description}"
		canonical="https://www.canonical.ie/"
		openGraph="{{
			url: 'https://www.url.ie/a',
			title: data.content.seo.title,
			description: data.content.seo.description,
			images: [
				{
					url: urlFor(data.content.seo.banner_image.asset),
					width: 800,
					height: 600,
					alt: data.content.seo.banner_image.alt_text
						? data.content.seo.banner_image.alt_text
						: 'Missing Alt Text'
				}
			],
			site_name: 'SiteName'
		}}"
		twitter="{{
			handle: '@MaisonMatador',
			site: '@MaisonMatador',
			cardType: 'summary_large_image',
			title: data.content.seo.title,
			description: data.content.seo.description,
			image: urlFor(data.content.seo.banner_image.asset),
			imageAlt: data.content.seo.banner_image.alt_text
				? data.content.seo.banner_image.alt_text
				: 'Missing Alt Text'
		}}"
	/>
</svelte:head>

{#if ready}
	<div
		in:fade="{{ duration: 500, delay: 150, easing: cubicOut }}"
		out:fade="{{ duration: 150, easing: cubicOut }}"
	>
		<section class="no-padding">
			<div>
				<h1 class="visually-hidden">{data.content.page_title}</h1>
			</div>
		</section>

		{#each data.content.page_layout as section}
			{#if section._type == 'project_list'}
				<SctnPageProjectList section="{section}" />
			{/if}

			{#if section._type == 'page_featured_work_list'}
				<SectionFeaturedWork section="{section}" />
			{/if}

			{#if section._type == 'sctn_jobs_list'}
				<SectionJobsList {...section} />
			{/if}
		{/each}
	</div>
{/if}
