<template>
	<router-link :to="`/task/${task.id}`" class="card mb-3 text-decoration-none text-muted task__card">
		<div class="card-body d-flex justify-content-between align-items-center">
			<div class="mr-auto">
				<div class="mb-1">
					{{ task.caption }}
					<router-link v-if="isAuthor" :to="`/task/${task.id}/edit/`">[Редактировать]</router-link>
				</div>
				<small class="text-muted">Уровень: {{ task.level }} | Решений: {{ task.solutions }} | Комментариев: 324</small>
			</div>
			<span class="badge badge-light p-2 font-weight-normal ml-3">
				Автор: {{ task.author }}
				<i v-if="task.company" class="fas fa-check"></i>
			</span>
			<span v-if="isCompleted" class="badge p-2 font-weight-normal badge-success ml-3">Выполнена</span>
		</div>
	</router-link>
</template>

<script>
export default {
	props: {
		task: Object
	},
	computed: {
		isAuthor() {
			return this.getAuthUserName === this.task.author;
		},
		isCompleted() {
			let user = this.$store.state["user/auth"];
			let id = this.task.id;

			if(user && this.isAuthorized && user.tasks.solved.includes(id)) {
				return true;
			} else {
				return false;
			}
		}
	}
}
</script>

<style scoped>
.task__card:last-child {
	margin-bottom: 0px !important;
}
</style>