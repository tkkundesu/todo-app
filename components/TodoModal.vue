<template>
    <div v-if="syncVisible" class="ns-modal">
        <div class="ns-modal-bg" />
        <div class="ns-modal-container" :style="{ width: width, height: height }">
            <div v-if="isHeader" class="ns-modal-container-title">
                {{ title }}
            </div>
            <div
                class="ns-modal-container-content"
                :class="{ 'ns-modal-container-content-none-button': !isButton, 'ns-modal-container-content-button': isButton }"
            >
                <slot />
            </div>
            <div v-if="isButton && !isOnlyClose" class="ns-modal-container-bottom">
                <todo-button class="ns-modal-container-bottom-button" label="キャンセル" @click="cancelButton()" />
                <todo-button class="ns-modal-container-bottom-button" :label="confirmButtonTitle" primary @click="confirmButton()" />
            </div>
            <div v-else-if="isOnlyClose" class="ns-modal-container-bottom">
                <todo-button class="ns-modal-container-bottom-button" label="閉じる" @click="cancelButton()" />
            </div>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Vue, Prop, PropSync, Emit } from 'vue-property-decorator'
import TodoButton from '~/components/TodoButton.vue'

interface modalContent {
    title: string
    body?: string
}

@Component({ components: {
    TodoButton,
} })
export default class TodoModal extends Vue {
    // モーダルの横幅(px)
    @Prop({ type: String, default: '512px' }) width!: string
    // モーダルの縦幅
    @Prop({ type: String, default: '90%' }) height!: string
    // ボタンの有無
    @Prop({ default: true }) isButton!: boolean
    // ボタンの有無
    @Prop({ default: false }) isOnlyClose!: boolean
    // ヘッダーの有無
    @Prop({ default: true }) isHeader!: boolean

    @PropSync('visible', { type: Boolean }) syncVisible!: boolean
    // モーダルタイトル
    @Prop(String) title!: string
    // 確定ボタン
    @Prop({ type: String, default: '確定' }) confirmButtonTitle?: string
    // キャンセルイベント
    @Emit('close')
    private cancelButton() {}
    // 確定イベント
    @Emit('confirm')
    private confirmButton() {}
}
</script>

<style lang="stylus">
.ns-modal
    z-index 999
    position fixed
    top 0
    left 0
    width 100%
    height 100%
    &-bg
        width: 100vw
        height: 100vh
        background: rgba(0, 0, 0, 0.7)
        position: fixed
        top: 0
        left: 0
        z-index: 100
    &-container
        position: absolute
        top: 50%
        left: 50%
        transform: translate(-50%, -50%)
        z-index: 1000
        background: #fff
        border-radius: 3px
        box-sizing: border-box
        text-align left
        color #333
        overflow: hidden
        // overflow-y scroll
        padding: 0
        // 下部ボタン
        &-bottom
            display flex
            justify-content center
            background-color: $light-bg-color
            padding-top 16px
            padding-bottom 16px
            &-button
                width 110px!important
                padding: 8px!important
                margin 0 8px
                display block
        &-title
            background: $primary-color-gradation
            text-align: center
            color: #fff
            font-size: 16px
            margin:0 auto
            height: 44px
            line-height: 44px
        &-content
            color #333
            padding: 24px
            height: calc(100% - 44px - 48px)
            overflow-y scroll
            // &-none-button
            //     max-height 70vh
            &-button
                height: calc(100% - 44px - 48px - 74px)
</style>