<script>
    import Header from './Header/Header.vue';
    import { api } from '@/utils/api';

    function getRandomElement(arr) {
        const randomIndex = Math.floor(Math.random() * arr.length);
        return arr[randomIndex];
    }

    export default {
        components: {
            Header
        },
        data() {
            return {
                api,
                getRandomElement,
                dictStr: "",
                dict: [],
                passwordDicts: [
                    {
                        name: "russian_letters_sm",
                        pass: "абвгдеёжзийклмнопрстуфхцчшщъыьэюя"
                    },
                    {
                        name: "russian_letters_bg",
                        pass: "АБВГДЕЁЖЗИЙКЛМНОПРСТУФХЦЧШЩЪЫЬЭЮЯ"
                    },
                    {
                        name: "english_letters_sm",
                        pass: "abcdefghijklmnopqrstuvwxyz"
                    },
                    {
                        name: "english_letters_bg",
                        pass: "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
                    },
                    {
                        name: "digits",
                        pass: "0123456789"
                    },
                    {
                        name: "special_chars_basic",
                        pass: "!@#$%^&*()_+-=[]{}|;:,.<>?"
                    }
                ],

                russian_letters_sm: false,
                russian_letters_bg: false,
                english_letters_sm: true,
                english_letters_bg: true,
                digits: true,
                special_chars_basic: true,
                count: 5,
                countOfLetters: 64,

                disabledButton: false,

                passwords: []
            }
        },
        watch: {
            russian_letters_sm() {
                this.makeDict();
            },
            russian_letters_bg() {
                this.makeDict();
            },
            english_letters_sm() {
                this.makeDict();
            },
            english_letters_bg() {
                this.makeDict();
            },
            digits() {
                this.makeDict();
            },
            special_chars_basic() {
                this.makeDict();
            },
        },
        methods: {
            makeDict() {
                const config = {
                    russian_letters_sm: this.russian_letters_sm,
                    russian_letters_bg: this.russian_letters_bg,
                    english_letters_sm: this.english_letters_sm,
                    english_letters_bg: this.english_letters_bg,
                    digits: this.digits,
                    special_chars_basic: this.special_chars_basic
                };

                const dictNames = Object.keys(config);
                this.dict = this.dict.filter(item => !dictNames.includes(item.name));

                dictNames.forEach(name => {
                    if (config[name]) {
                        const dictToAdd = this.passwordDicts.find(item => item.name === name);
                        if (dictToAdd) {
                            this.dict.push(dictToAdd);
                        }
                    }
                });

                const tmpArr = this.dict.map(item => item.pass);
                this.dictStr = tmpArr.join("");

                if (!russian_letters_sm && !russian_letters_bg && !english_letters_sm && !english_letters_bg && !digits && !special_chars_basic) {
                    this.disabledButton = true
                }
            },
            makePasswords() {
                this.passwords = [];

                const dictStrArr = this.dictStr.split("");
                for (let i = 0; i < this.count; i++) {
                    const tmpArrOfStr = []
                    for (let j = 0; j < this.countOfLetters; j++) {
                        tmpArrOfStr[j] = this.getRandomElement(dictStrArr)
                    };
                    this.passwords[i] = tmpArrOfStr.join("")
                }
            },
            async copyToClipboard(password) {
                await navigator.clipboard.writeText(password);
            }
        },
        mounted() {
            this.makeDict();
        }
    }
</script>

<template>
    <Header title="Настройки генерации">
        <div class="row">
            <div class="col px-3 pb-3">
                <div class="bg-body-tertiary rounded-3 h-100 px-5 py-3">
                    <h2>
                        Алфавит, из которого генерируется пароль:
                    </h2>
                    <p class="mx-auto fs-5 text-muted m-0 p-0 text-break">
                        {{ dictStr }}
                    </p>
                </div>
            </div>
            <div class="col">
                <h3 class="fs-4 text-muted">
                    Количество символов
                </h3>
                <input type="range" class="form-range" min="8" max="64" v-model="countOfLetters">
                <p class="text-muted">{{ countOfLetters }}</p>
                <hr>

                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" role="switch" id="russian_letters_sm" v-model="russian_letters_sm">
                    <label class="form-check-label" for="russian_letters_sm">Русские маленькие буквы</label>
                </div>
                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" role="switch" id="russian_letters_bg" v-model="russian_letters_bg">
                    <label class="form-check-label" for="russian_letters_bg">Русские большие буквы</label>
                </div>
                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" role="switch" id="english_letters_sm" v-model="english_letters_sm">
                    <label class="form-check-label" for="english_letters_sm">Английские маленькие буквы</label>
                </div>
                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" role="switch" id="english_letters_bg" v-model="english_letters_bg">
                    <label class="form-check-label" for="english_letters_bg">Английские большие буквы</label>
                </div>
                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" role="switch" id="digits" v-model="digits">
                    <label class="form-check-label" for="digits">Цифры</label>
                </div>
                <div class="form-check form-switch">
                    <input class="form-check-input" type="checkbox" role="switch" id="special_chars_basic" v-model="special_chars_basic">
                    <label class="form-check-label" for="special_chars_basic">Спецсимволы</label>
                </div>

                <hr>
                <h3 class="fs-4 text-muted">
                    Количество паролей
                </h3>
                <input type="range" class="form-range" min="1" max="25" v-model="count">
                <p class="text-muted">{{ count }}</p>
            </div>
        </div>

        <hr>
        <div class="d-flex gap-2 mb-3 mx-2">
            <button class="btn btn-primary btn-lg px-4 fw-bold" type="button" @click="makePasswords()" :disabled="disabledButton">
                <i class="fa-solid fa-lock"></i>
                Сгенерировать пароли
            </button>
        </div>
    </Header>


    <div class="container">
        <div v-for="password in passwords" class="alert alert-dark d-flex justify-content-between">
            <p class="fs-5 p-0 m-0">
                {{ password }}
            </p>
            <button class="btn btn-primary btn-sm px-3 fw-bold" type="button" @click="copyToClipboard(password)">
                <i class="fa-solid fa-clipboard"></i>
            </button>
        </div>
    </div>
</template>

<style>

</style>