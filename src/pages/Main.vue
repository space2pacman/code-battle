<template>
	<div class="mt-5">
		<main-section v-for="(section, index) in sections" :section="section" :index="index">
			<template v-if="checkSlot(section.slot, 'caption')" v-slot:caption>
				<h2 class="display-4 mr-4">
					<vue-typer
						:text="section.caption"
						:repeat="Infinity"
						:shuffle="false"
						:pre-type-delay="70"
						:type-delay="70"
						:pre-erase-delay="2000"
						:erase-delay="250"
						:erase-on-complete="false"
						initial-action="typing"
						erase-style="clear"
						caret-animation="blink"
						class="mr-4"
						v-if="showCaption"
					></vue-typer>
				</h2>
			</template>
			<template v-if="checkSlot(section.slot, 'image')" v-slot:image>
				<div class="position-relative">
					<Ace lang="javascript" theme="monokai" height="250" v-model="code" />
					<Preloader v-if="preloader" :overlay="true" />
				</div>
			</template>
			<template v-if="checkSlot(section.slot, 'bottom')" v-slot:bottom>
				<div class="text-right mb-4">
					<button type="button" class="btn btn-success mt-3 mr-2" @click="run">Запуск</button>
				</div>
			</template>
		</main-section>
	</div>
</template>

<script>
import MainSection from "@/components/MainSection";
import Preloader from "@/components/Preloader";
import Ace from "vue2-ace-editor";
import { VueTyper } from "vue-typer";
import "brace/mode/javascript";
import "brace/theme/monokai";

export default {
	data() {
		return {
			code: 'function greeting() { \n\treturn [ \n\t\t"Совершенствуйте", \n\t\t"свои", \n\t\t"навыки", \n\t\t"программирования" \n\t].join(" "); \n}',
			preloader: false,
			showCaption: true,
			sections: [
				{
					caption: "Совершенствуйте свои навыки программирования",
					slot: ["caption", "image", "bottom"]
				},
				{
					caption: "Выполняйте задания на качество/скорость и зарабатывайте очки",
					image: {
						link: require("@/assets/brain.png")
					}
				},
				{
					caption: "Создавайте свои задания",
					image: {
						link: require("@/assets/lamp.png")
					}
				},
				{
					caption: "Обсуждайте решения других пользователей",
					image: {
						link: require("@/assets/chat.png")
					}
				}
			]
		} 
	},
	methods: {
		checkSlot(slot, value) {
			if(Array.isArray(slot)) {
				return slot.includes(value);
			} else {
				return slot === value;
			}
		},
		addDots(string) {
			let MAX_LENGTH = 50;

			if(string.length >= MAX_LENGTH) {
				return string.slice(0, MAX_LENGTH) + "...";
			} else {
				return string;
			}
		},
		run() {
			let func = new Function(`
				${this.code}

				return greeting();
			`);

			this.showCaption = false;
			this.showCaption = true;
			this.preloader = true;
			setTimeout(() => {
				let result = func();

				if(result === undefined) {
					this.sections[0].caption = "¯\\_(ツ)_/¯";
				} else {
					this.sections[0].caption = this.addDots(result.toString());
				}

				this.preloader = false;
			}, 200)
		}
	},
	components: {
		MainSection,
		Preloader,
		Ace,
		VueTyper
	}
}
</script>