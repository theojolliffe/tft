<script>
    import {page} from '$app/stores'
    import { ord, ud, otherRank, otherEst, qui, cha, cur, figs, get_word } from "./utils";
	import { teams } from './fb-utils'
	import { csvParse, autoType } from 'd3-dsv';
	import { load } from "archieml";
	import robojournalist from 'robojournalist';

    import * as someJSON from '/Users/theojolliffe/Documents/Football/Football Python/tweets.json';

	console.log('someJSON', someJSON.default)

	var data, topics, leader;
	let teamLoad = false
    let topicsLoad = false

    $: teamName = $page.params.teamName
    $: teamName = teams.find(d => d.id==teamName).name
    $: fetch("https://raw.githubusercontent.com/theojolliffe/plpy/main/"+teamName+".json")
		.then(res => res.json())
		.then(json => {
			data = json;
			teamLoad = true;
		})

	$: tweets = someJSON.default[teamName]['data']
	$: console.log("TWEETS", tweets)
	var template;
    fetch("/template.pug")
        .then((res) => res.text())
        .then((txt) => (template = txt))

	let loaded = false
    const onRosaeNlgLoad = () => { 
        loaded = true }

	async function getAml(url) {
		fetch(url)
        .then((res) => res.text())
        .then((txt) => (topics = load(txt)))
		topicsLoad = true
		return topics;
	}

	topics = getAml("/archie.aml")

	async function getData(url) {
		let response = await fetch(url);
		let string = await response.text();
			let data = await csvParse(string, autoType);
		return data;
	}

	getData("https://raw.githubusercontent.com/theojolliffe/plpy/main/leaders_assists.csv")
	.then(res => {
		leader = res;
	})

	function iterate(obj, pname) {
		Object.keys(obj).forEach(key => {
			if (typeof obj[key] === 'object') {
				iterate(obj[key], pname)
			} else {
				obj[key] = robojournalist(obj[key], {
					plcname: pname,
				})
			}
		})
	}


    function results(data, topicsIn) {
		
		var topicLU = JSON.parse(JSON.stringify(topicsIn));
		iterate(topicLU, teamName)

		return rosaenlg_en_US.render(template, {
			language: 'en_UK',
			data: data,
			team: teamName,
			topics: topicLU,
			get_word: get_word,
			figs: figs,
			otherEst: otherEst,
			cur: cur,
			cha: cha,
			qui: qui,
			otherRank: otherRank,
			ud: ud,
			ord: ord,
		})
	}
</script>

<head>
	<script src="https://unpkg.com/rosaenlg@3.0.1/dist/rollup/rosaenlg_tiny_en_US_3.0.1_comp.js" on:load="{onRosaeNlgLoad}"></script>
</head>

<div style="width: 640px; margin:0 auto;">
    <div>
        <div style="width: 640px; margin: 50px auto;">
            <h1>Latest {teamName} match report</h1>
        </div>
    </div>
</div>
<div id="tweet-cont" style="width: 640px; margin:0 auto;">
    {#each tweets.reverse() as { id, text }, i}
        <div>
            <a class="tweets" href={"https://twitter.com/_Numbers_Game/status/"+id} target="_blank">{text}</a>
        </div>
        <br>
    {/each}
</div>

<!-- <div>
	{#if loaded&teamLoad&topicsLoad}
		<div id="sf">
			<div style="width: 640px; margin:0 auto;">
				<div>
					<div style="width: 640px; margin: 50px auto;">
						<h1>Latest {teamName} match report</h1>
					</div>
				</div>
			</div>
		</div>
		<main>
			{@html results(data, topics)}
			<hr style="width: 40%; margin: 60px auto 30px auto;"/>
		</main>
	{/if}
</div> -->

<style>
    a.tweets {
        color: var(--heading-color);
    }
    /* a:-webkit-any-link {
        color: var(--accent-color);
        cursor: pointer;
        text-decoration: underline;
    } */
    a:hover {
        background-color: yellow;
    }
</style>