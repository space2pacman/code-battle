<template>
	<div class="mb-4">
		<nav class="navbar navbar-expand-lg navbar-light bg-light">
			<div class="container">
				<router-link class="navbar-brand" to="/">Code battle</router-link>
                <div class="collapse navbar-collapse">
                    <ul class="navbar-nav ml-auto">
                        <li class="nav-item">
                            <router-link to="/" class="nav-link">Главная</router-link>
                        </li>
                        <li>
                            <router-link to="/tasks" class="nav-link">Задачи</router-link>
                        </li>
                        <li>
                            <router-link to="/registration" class="nav-link" v-if="!isAuthorized">Регистрация</router-link>
                        </li>
                        <li>
                            <router-link to="/login" class="nav-link" v-if="!isAuthorized">Войти</router-link>
                        </li>
                        <li class="nav-item dropdown" v-if="isAuthorized">
                            <dropdown-menu
                                :right="true"
                                :hover="true"
                                :hover_timeout="0"
                                transition="translate-fade-down"
                                v-model="dropdown"
                            >
                                <a class="nav-link dropdown-toggle" href="#" @click.prevent>
                                    <img :src="userpic" class="align-sub" width="20" alt="avatar">
                                    <span class="ml-2 mr-1">{{ getAuthUserName }}</span>
                                </a>
                                <div slot="dropdown">
                                    <router-link :to="`/user/${getAuthUserName}`" class="dropdown-item">Профиль</router-link>
                                    <router-link to="/admin/" class="dropdown-item" v-if="checkAccessLevel(100)">Панель администратора</router-link>
                                    <router-link to="/task/add/" class="dropdown-item" v-if="checkAccessLevel(100)">Добавить задачу</router-link>
                                    <router-link :to="`/user/${getAuthUserName}/settings`" class="dropdown-item">Настройки</router-link>
                                    <router-link to="/logout" class="dropdown-item">Выйти</router-link>
                                </div>
                            </dropdown-menu>
                        </li>
                    </ul>
                </div>
                <span class="d-lg-none" @click="toggleMenu">
                    <i class="fas fa-bars"></i>
                </span>
            </div>
        </nav>
        <transition name="mobile-nav">
            <div class="bg-light" v-if="showMobileMenu">
                <div class="container text-right p-0 d-lg-none">
					<ul class="navbar-nav ml-auto">
						<li class="nav-item">
							<router-link to="/" class="nav-link">Главная</router-link>
                        </li>
                        <li>
                            <router-link to="/tasks" class="nav-link">Задачи</router-link>
                        </li>
                        <li>
                            <router-link to="/registration" class="nav-link" v-if="!isAuthorized">Регистрация</router-link>
                        </li>
                        <li>
                            <router-link to="/login" class="nav-link" v-if="!isAuthorized">Войти</router-link>
                        </li>
                        <li class="nav-item dropdown" v-if="isAuthorized">
                            <a class="nav-link dropdown-toggle" href="#" @click.prevent="dropdown = !dropdown">
                                <img :src="userpic" class="align-sub" width="20" alt="avatar">
                                <span class="ml-2 mr-1">{{ getAuthUserName }}</span>
                            </a>
                            <transition name="mobile-nav">
                                <div v-if="dropdown" class="dropdown__mobile">
                                    <router-link :to="`/user/${getAuthUserName}`" class="dropdown-item">Профиль</router-link>
                                    <router-link to="/admin/" class="dropdown-item" v-if="checkAccessLevel(100)">Панель администратора</router-link>
                                    <router-link to="/task/add/" class="dropdown-item" v-if="checkAccessLevel(100)">Добавить задачу</router-link>
                                    <router-link :to="`/user/${getAuthUserName}/settings`" class="dropdown-item">Настройки</router-link>
                                    <router-link to="/logout" class="dropdown-item">Выйти</router-link>
                                </div>
                            </transition>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
import DropdownMenu from "@innologica/vue-dropdown-menu";

export default {
    data() {
        return {
            userpic: null,
            dropdown: false,
            showMobileMenu: false,
        }
    },
    methods: {
        onMouseover() {
            this.dropdown = true;
        },
        onMouseleave() {
            this.dropdown = false;
        },
        toggleMenu() {
            this.showMobileMenu = !this.showMobileMenu;
        }
    },
    watch: {
        $route() {
            this.dropdown = false;
            this.showMobileMenu = false;
        }
    },
    mounted() {
        this.$store.subscribe(mutation => {
            if(mutation.type === "user/auth") {
                this.userpic = mutation.payload.userpic;
            }
        })
    },
    components: {
        DropdownMenu
    }
}
</script>

<style>
.align-sub {
    vertical-align: sub;
}

.navbar-nav .nav-link {
    color: rgba(0,0,0,.5);
}
</style>