<template>
    <todo-modal
        :visible.sync="modalVisible"
        width="80%"
        height="80%"
        :title="syncModel.id === null || syncModel.id === undefined ? '新しい課題を登録' : syncModel.title + 'を編集'"
        confirm-button-title="登録"
        class="todo-edit"
        @confirm="saveHandler"
        @close="closeHandler"
    >
    <div class="container">
        <div class="contents"><h3 class="edit-title">Title</h3><input type="test" v-model="syncModel.title"></div>
        <div class="contents"><h3 class="edit-title">Content</h3><textarea v-model="syncModel.content"></textarea></div>
        <div class="contents"><h3 class="edit-title">Deadline</h3><vue-ctk-date-time-picker label="日時を選択" v-model="syncModel.deadline_at"></vue-ctk-date-time-picker></div>
        <div class="contents"><h3 class="edit-title">Status</h3>
        <select class="select-drop" v-model="syncModel.status">
            <option v-for="(data, index) in statusList" :key="index" :value="data.value">
                <span class="select-drop-item">{{ data.label }}</span>
            </option>
        </select>
        </div>
    </div>
    </todo-modal>
</template>
<script lang="ts">

import { Component, Vue, PropSync } from 'vue-property-decorator'
import VueCtkDateTimePicker from 'vue-ctk-date-time-picker'
import 'vue-ctk-date-time-picker/dist/vue-ctk-date-time-picker.css'
import axios from 'axios'
import TodoModal from '~/components/TodoModal.vue'
import _ from 'lodash'

@Component({ components: {
    TodoModal, VueCtkDateTimePicker,
} })
export default class TodoEdit extends Vue {
    @PropSync('visible') modalVisible!: boolean
    @PropSync('model') syncModel!: any
    errors: any = null

    // キャンセルボタンハンドラ
    closeHandler() {
        this.modalVisible = false
        this.$emit('todo-edit-finished', null)
    }
    // バリデーション
    validate(syncModel: any) {
        let message: string | null = null
        if (syncModel.title === null || syncModel.title.length === 0 || syncModel.title === undefined) {
            message = 'タイトルは必須項目です'
            return message
        }
        return null
    }
    // 保存ボタンハンドラ
    async saveHandler() {
        try {
            this.errors = []
            // バリデーション
            const validate = this.validate(this.syncModel)
            if (validate !== null) {
                alert(validate)
                return
            }
            // 保存処理
            let todo: any | null = null
            const sendData: any = _.cloneDeep(this.syncModel)

            if (this.syncModel.id) {
                todo = await this.$axios.$put('http://localhost:1337/todos/' + this.syncModel.id, sendData).catch((e: any) => {
                    alert(e)
                })
            } else {
                todo = await this.$axios.$post('http://localhost:1337/todos/', sendData).catch((e: any) => {
                   alert(e)
                })
            }
            this.modalVisible = false
            this.$emit('todo-edit-finished', todo)
        } catch (e) {
            this.errors.push(e)
        }
    }
    statusList: any[] = [
        { label: '未実行', value: 0 },
        { label: '実行中', value: 1 },
        { label: '実施済', value: 2 },
    ]
}
</script>
<style lang="stylus">
.edit-title
    margin-bottom 5px
.container
    width 70%
    margin 0 auto
.contents
    margin-bottom 20px
</style>