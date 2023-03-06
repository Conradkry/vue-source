<script setup>
import { ref, reactive, watch } from 'vue'
import { onMounted, onUpdated } from 'vue'
const usernameRef = ref(null)
const username = ref('')
const password = ref('')
const password2 = ref('')
const disabled = ref(true)
const validUsername = ref(false)
const validPassword = ref(false)
const passwordsMatch = ref(false)
const usernameErrorRef = ref(null)
const passwordErrorRef = ref(null)
const password2ErrorRef = ref(null)
const user = reactive({
    avatarImageFile: {}
})
function loadPreviewImg(evt) {
    user.avatarImageFile = evt.target.files[0]
    let reader = new FileReader()
    reader.onloadend = function () {
        let img = document.querySelector("#preview-img")
        img.src = reader.result;
    }
    reader.readAsDataURL(user.avatarImageFile)
}

let count = 0
watch(usernameRef, () => {
    console.log('watching usernameRef')
    console.log(usernameRef.value)
    console.log(count++)
    usernameRef.value.value = "ckrytusa"
})

function componentUpdate(elm) {
    console.log("password changed")
    console.log(elm.value)
    console.log(passwordErrorRef.value)
    if (passwordErrorRef.value != null && elm.value.length > 0) {
        validPassword.value = (elm.value.length >= 8)
        passwordErrorRef.value.innerHTML = (validPassword.value) ?
            "&nbsp;" : "Minimum length: 8 characters"
    }
}

watch(username, () => {
    validUsername.value = (username.value.length >= 4)
    usernameErrorRef.value.innerHTML = (validUsername.value) ?
        "&nbsp;" : "Minimum length: 4 characters"
    usernameRef.value.setAttribute("data", "hello")
})

watch([password, password2], () => {
    passwordsMatch.value = (password.value === password2.value)
    password2ErrorRef.value.innerHTML = (passwordsMatch.value || password2.value.length == 0) ?
        "&nbsp;" : "Passwords do not match"
})

watch([validUsername, validPassword, passwordsMatch], async () => {
    disabled.value = !(validUsername.value && validPassword.value && passwordsMatch.value)
    console.log(disabled.value)
    if (!disabled.value) usernameRef.value.value = ''
})
function submit() {
    let mssg = username.value + ", " + password.value
    console.log(mssg)
}
onMounted(() => {
    usernameRef.value.focus();
})
onUpdated(() => {
    console.log(usernameRef.value.value)
})
</script>


<template>
    <div id="main" class="container">
        <form class="rounded border border-primary border-2 border-opacity-25 py-3 px-5">
            <fieldset class="d-flex flex-column">
                <legend>Create Account</legend>

                <div class="form-floating mb-2">
                    <label for="username" class="form-label">Username</label><br>
                    <input ref="usernameRef" v-model="username" id="username" type="text" class="form-control" /><br>
                    <small ref="usernameErrorRef" class="text-danger">&nbsp;</small>
                </div>

                <div class="form-floating mb-2">
                    <label for="password" class="form-label">Password</label><br>
                    <input :ref="componentUpdate" v-model="password" id="password" type="password"
                        class="form-control" /><br>
                    <small ref="passwordErrorRef" class="text-danger">&nbsp;</small>
                </div>

                <div class="form-floating mb-2">
                    <label for="password2" class="form-label">Reenter password</label><br>
                    <input v-model="password2" id="password2" type="password" class="form-control" /><br>
                    <small ref="password2ErrorRef" class="text-danger">&nbsp;</small>
                </div>

                <div class="form-floating mb-2">
                    <label for="email">Email</label> <br>
                    <input class="form-control" type="email" id="email" placeholder="example@email.com" size="30"
                        required>
                </div>

                <div class="form-floating mb-2">
                    <label for="phone">Phone number</label> <br>
                    <input class="form-control" type="tel" id="phone" name="phone" placeholder="123-456-7890"
                        pattern="[0-9]{3}-[0-9]{3}-[0-9]{4}" required>
                </div>

                <div class="form-floating mb-2">
                    <label for="avatar" class="form-label">Profile picture (optional):</label><br>
                    <input id="avatar" class="form-control" type="file" name="avatar" accept="image/png, image/jpeg"
                        @change="loadPreviewImg">
                </div>
                <div id="images" class="row">
                    <div class="col-auto">
                        <figure id="fig1">
                            <img id="preview-img">
                            <figcaption>{{ user.avatarImageFile.name }}</figcaption>
                        </figure>
                    </div>

                </div>

                <div>
                    <button @click="submit" class="btn btn-primary" type="button"
                        v-bind:disabled="disabled">Create</button>
                </div>
            </fieldset>
        </form>
    </div>

</template>

<style>
legend {
    font-size: 25px;
}

#main {
    display: flex;
    justify-content: center;
    font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
}

div {
    padding: 5px;
}

#preview-img {
    width: 75px;
    height: 75px;
    border-radius: 100px;
}

input {
    border-color: rgb(136, 136, 255);
    border-radius: 5px;
}
</style>