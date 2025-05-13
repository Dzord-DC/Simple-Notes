<template>
    <form @submit.prevent="acceptLogIn" class="login-screen">
        <input v-model="login" type="text" placeholder="Account" class="note-input note-input-text">
        <input v-model="password" type="password" placeholder="Password" class="note-input note-input-text">
        <button @click="acceptLogIn" :disabled="!login || !password">Log in</button>
        <div>{{ loginError }}</div>
    </form>
</template>
<script>
export default {
  name: "note-сard",
  props: {
    users: Array,
  },
  emits: ["identifyUser"],
  data() {
        return {
            login: "",
            password: "",
            loginError: "",
        };
    },
    methods: {
        acceptLogIn() {
            this.loginError = "";
            const buffUser = this.users.find( item => item.name === this.login );
            if( !buffUser ) {
                this.loginError = "The user was not found";
                return;
            }
            if( buffUser.password !== this.password ) {
                this.loginError = "The password is not correct";
                return;
            }
            this.$emit("identifyUser", buffUser.name)
            this.login = "";
            this.password = "";
        },
    }
}
</script>
<style lang="scss" scoped>
.login-screen {
    display: flex;
    flex-direction: column;
    align-items: center;
}
</style>