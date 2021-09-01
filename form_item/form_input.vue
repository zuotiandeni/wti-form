<template>
    <!-- 普通输入框 -->
    <div :style="item.style||{}"
         :class="`form-unqiue-${item.key}`"
         class="form-input-box form-item-box">
        <el-input v-model.trim="val"
                  :placeholder="getPlaceholder(item)"
                  :disabled="getDisabled"
                  @blur="e => onBlur(item, e)"
                  @focus="e => onFocus(item, e)"
                  type="text"
                  :clearable="true"
                  v-if="!getTextModel"/>
        <div v-else :style="item.textStyle||{}">{{ val || '-' }}</div>
        <span class="extra-unit">{{ appendMsg }}</span>
    </div>
</template>

<script>
    import FormMixin from './mixin';

    export default {
        name: 'FormInput',
        mixins: [ FormMixin ],
        data () {
            return {
                prependMsg: '',
                appendMsg: ''
            };
        },
        watch: {
            item: {
                handler (n) {
                    if (n.prefixMsg) {
                        this.prependMsg = n.prefixMsg;
                    }
                    if (n.suffixMsg) {
                        this.appendMsg = n.suffixMsg;
                    }
                },
                immediate: true
            }
        }
    };
</script>

<style scoped lang="less">
@import '~common/less/config.less';

.form-input-box /deep/ .el-input {
    position: relative;
    width: 100%;
    height: 36px;
    .el-input__inner {
        position: absolute;
        width: 100%;
        height: 36px;
        line-height: 36px;
        padding-right: 50px;
        padding-left: 12px;
    }
}

.form-input-box /deep/ .el-input-group__prepend, .single-input .form-input-box /deep/ .el-input-group__append {
    padding: 0 10px;
}

.extra-unit {
    position: absolute;
    top: 1px;
    right: 7px;
    min-width: 39px;
    padding-right: 9px;
    height: 31px;
    line-height: 36px;
    font-family: PingFangSC-Regular;
    font-size: 14px;
    color: #12182A;
    text-align: center;
}
</style>
