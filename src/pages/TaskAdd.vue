<template>
	<div>
		<Task caption="Новая задача" :tabs="tabs">
			<template v-slot:tabs-content>
				<task-fields v-show="tabs.active === 'description'" @data="onTaskFields" />
				<Result v-show="tabs.active === 'result'" @onResultSuccess="onResultSuccess" @onResultUnsuccess="onResultUnsuccess" />
			</template>
			<template v-slot:buttons>
				<div class="text-right">
					<button type="button" class="btn btn-success ml-3 mt-3 mr-2" @click="test">Тест</button>
					<button type="button" class="btn btn-success mt-3" @click="add" :disabled="!success">Добавить</button>
				</div>
			</template>
		</Task>
	</div>
</template>

<script>
import Task from "@/components/Task";
import TaskFields from "@/components/TaskFields";
import Result from "@/components/Result";

export default {
	data() {
		return {
			tabs: {
				list: [
					{
						caption: "Описание",
						type: "description"
					},
					{
						caption: "Результат проверки",
						type: "result"
					}
				],
				active: "description"
			},
			code: null,
			fields: null,
			success: false
		}
	},
	methods: {
		switchTab(tab) {
			this.tabs.active = tab;
		},
		test() {
			let payload = {
				data: {
					func: {
						name: this.fields.func.name,
						body: this.code
					},
					tests: this.fields.tests
				}
			}

			if(this.code === null || this.code.length === 0) {
				this.$root.$emit("onSwitchTab", "result");
				this.$root.$emit("onTestEnd", "Отсутствует код");

				return false;
			}

			this.$root.$emit("onSwitchTab", "result");
			this.$root.$emit("onTestStart");
			this.send("task/check", payload).then(response => {
				if(response.status === "success") {
					this.$root.$emit("onTestEnd", null, response.data);
				}

				if(response.status === "error") {
					this.$root.$emit("onTestEnd", response.error);
				}
			});
		},
		add() {
			let payload = {
				data: {
					fields: this.fields,
					func: this.code,
					author: this.getAuthUserName
				}
			}
			
			if(this.success) {
				this.$root.$emit("onPreloaderStart");
				setTimeout(() => {
					this.send("task/add", payload).then(response => {
						if(response.status === "success") {
							this.$root.$emit("onPreloaderEnd");
						}

						if(response.status === "error") {
							this.$root.$emit("onPreloaderEnd");
							this.$root.$emit("onSwitchTab", "result");
							this.$root.$emit("onTestEnd", response.error);
						}
					});
				}, 1000)
			}
		},
		onTaskFields(data) {
			this.fields = data;
		},
		onCodeInput(code) {
			this.code = code;
		},
		onResultSuccess() {
			this.success = true;
		},
		onResultUnsuccess() {
			this.success = false;
		}
	},
	mounted() {
		this.$root.$on("onCodeInput", this.onCodeInput);
	},
	components: {
		Task,	
		TaskFields,
		Result
	}
}
</script>