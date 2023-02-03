<template>
    <div :style="item.style||{}"
         :class="`form-unqiue-${item.key} ${getTextModel ? '' : 'wti-untext-box'}`"
         class="form-item-box">
        <div v-if="!getTextModel" class="idcard-date">
            <el-select v-model="dateType"
                       :disabled="getDisabled"
                       @change="changeDateType"
                       :popper-append-to-body="false"
                       placeholder="是否长期"
                       :style="{ width: dateType === 'short' ? '40%' : '100%', float: 'left'}">
                <el-option label="长期" value="long"/>
                <el-option label="非长期" value="short"/>
            </el-select>
            <el-date-picker
                v-if="dateType === 'short'"
                v-model="val"
                class="date-picker"
                :placeholder="getPlaceholder(item)"
                popper-class="val-with-date"
                :disabled="getDisabled"
                value-format="yyyy-MM-dd"
                style="width: 60%; float:left;"
                align="right"
                :picker-options="pickerOptions"
                :editable="true"
                :clearable="getClearableStatus(true)"/>
        </div>
        <template v-else>
            <span v-if="!val" :style="item.textStyle||{}" class="form-input-text">-</span>
            <span v-else-if="val===longTypeValue" :style="item.textStyle||{}" class="form-input-text">长期</span>
            <span v-else :style="item.textStyle||{}" class="form-input-text">{{ val }}</span>
        </template>
    </div>
</template>

<script>
    import FormMixin from './mixin';

    export default {
        name: 'FormIDCardDate',
        mixins: [ FormMixin ],
        props: {
            value: {
                type: String,
                default: ''
            },
        },
        data () {
            return {
                LongTypeValue: '9999-12-31', // 身份证有效期为长期时，他的值
                DateLimitBegin: '', // 身份证有效期为非长期情况下，最早能选择的时间限制为今天
                dateType: '', // 长期是long，非长期是short
                dateValue: '',

                PickerOptions: {
                    disabledDate: (time) => {
                        // 默认情况只能选今天之前（不包括今天），除非设置 notLimitBegin 为 true
                        if (this.item.notLimitBegin) {
                            return false;
                        } else {
                            return time.getTime() < Date.now() - 1000 * 3600 * 24;
                        }
                    },
                }
            };
        },
        created () {
            // console.log('created', this.value);
            if (this.value === this.longTypeValue) {
                this.dateType = 'long';
            } else {
                this.dateType = 'short';
            }
        },
        computed: {
            // 身份证为长期时，其值（取默认值或用户自定义值）
            longTypeValue () {
                if (this.item.longTypeValue) {
                    return this.item.longTypeValue;
                } else {
                    return this.LongTypeValue;
                }
            },
            val: {
                get () {
                    // console.log('get', this.value);
                    if (this.value) {
                        return this.value;
                    } else {
                        return '';
                    }
                },
                set (newValue) {
                    let v = null;
                    if (newValue === null) {
                        v = '';
                    } else {
                        v = newValue;
                    }
                    this.$emit('input', v);
                    // 只有非子表单的情况下，才会冒泡上去数据变更
                    if (this.formItemType !== 'childForm') {
                        this.statusChangeFn.valueUpdateEvent({
                            [this.item.key]: v
                        });
                    } else {
                        // 如果是子表单的话，执行内置的变更
                        this.childChangeData.valueUpdateEvent({
                            [this.item.key]: v,
                        }, this.childFormIndex);
                    }
                }
            },
            pickerOptions () {
                return Object.assign({}, this.PickerOptions, this.item.pickerOptions);
            }
        },
        watch: {
            value: {
                // immediate: true,
                handler: function (newValue) {
                    // 如果非空值，则赋值
                    if (newValue) {
                        // 如果该身份证有效期是长期，那么设置数据类型为长期
                        if (newValue === this.longTypeValue) {
                            this.dateType = 'long';
                        } else {
                            this.dateType = 'short';
                            // 此时说明是非长期
                            if (this.val !== newValue) {
                                this.val = newValue;
                            }
                        }
                    }
                }
            },
        },
        methods: {
            // 当身份证类型变化时
            changeDateType () {
                if (this.dateType === 'long') {
                    const v = this.longTypeValue;
                    // 如果是长期，则设置该值为长期的值
                    this.$emit('input', v);
                    // 只有非子表单的情况下，才会冒泡上去数据变更
                    if (this.formItemType !== 'childForm') {
                        this.statusChangeFn.valueUpdateEvent({
                            [this.item.key]: v
                        });
                    } else {
                        // 如果是子表单的话，执行内置的变更
                        this.childChangeData.valueUpdateEvent({
                            [this.item.key]: v,
                        }, this.childFormIndex);
                    }
                } else {
                    // 非长期则置为空
                    this.$emit('input', '');
                }
            }
        }
    };
</script>


<style scoped lang="less">
    .idcard-date /deep/ .date-picker {
        position: relative;
        width: 100%;
        height: 36px;

        .el-input__inner {
            position: absolute;
            width: 100%;
            height: 36px;
            line-height: 36px;
            padding-right: 10px;
            padding-left: 32px;
        }
    }

    .form-input-text {
        position: relative;
        width: 100%;
        height: 36px;
        line-height: 36px;
        font-size: 14px;
        color: #12182A;
    }
</style>
