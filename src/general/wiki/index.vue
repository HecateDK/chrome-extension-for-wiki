<template>
    <div class="wiki" id="wiki">
        <h3>{{ isNeedSetting ? 'Wiki 设置' : '添加链接' }}</h3>
        <setting @settingChange="settingChange" v-show="isNeedSetting"></setting>
        <add-link @settingChange="settingChange" v-show="!isNeedSetting" :token.sync="token" :repo.sync="repo"></add-link>
    </div>
</template>

<script>
    import Setting from '../setting/index.vue';
    import AddLink from '../add-link/index.vue';

    export default {
        data () {
            return {
                isNeedSetting: false,
                token: '',
                repo: ''
            };
        },

        created () {
            chrome.storage.sync.get('token', (items) => {
                if (!items.token) {
                    this.isNeedSetting = true;
                    return;
                }
                this.token = items.token;
                window.eventBus.$emit('saveData', items.token, '');
                this.isNeedSetting = false;
            });

            chrome.storage.sync.get('repo', (items) => {
                if (!items.repo) {
                    this.isNeedSetting = true;
                    return;
                }
                this.repo = items.repo;
                window.eventBus.$emit('saveData', '', items.repo);
                this.isNeedSetting = false;
            });

            window.eventBus.$on('resetToken', (token, repo) => {
                this.isNeedSetting = true;
                this.token = token;
                this.repo = repo;
                window.token = token;
                window.repo = repo;
            });
        },

        methods: {
            settingChange (isNeedSetting) {
                this.isNeedSetting = isNeedSetting;
                if (!isNeedSetting) {
                    // 促使 add-link 更新
                    this.token = window.token;
                    this.repo = window.repo;
                } else {
                    this.token = '';
                    this.repo = '';
                }
            }
        },

        components: {
            Setting,
            AddLink
        }
    };
</script>
