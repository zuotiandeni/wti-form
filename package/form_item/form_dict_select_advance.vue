<template>
    <div :style="item.style||{}"
         :class="`form-unqiue-${item.key} ${getTextModel ? '' : 'wti-untext-box'}`"
         class="form-item-box">
        <el-select style="width:100%"
                   v-model="val"
                   :disabled="getDisabled"
                   v-bind="bindOptions"
                   :clearable="getClearableStatus(false)"
                   :placeholder="getPlaceholder(item)"
                   v-if="!getTextModel">
            <el-option v-for="option in optionsList"
                       :key="option.value"
                       :label="option.label"
                       :disabled="option.disabled"
                       :value="option.value"/>
        </el-select>
        <div v-else :class="exposeSpecificClass(parentKey, childFormIndex, item.key)" :style="item.textStyle || {}"
             class="form-input-text">
            {{ textModelValue || '-' }}
        </div>
    </div>
</template>

<script>
    import FormMixin from './mixin';

    //
    export default {
        name: 'FormDynamicSelectAdvance',
        mixins: [ FormMixin ],
        created () {
            if (this.item.querySearchAsync) {
                this.isUserDefaultDict = false;
                const res = this.item.querySearchAsync(this);
                if (res) {
                    this.customDictList = res;
                }
                return;
            }

            if (this.item.dynamicSelectOption && this.item.dynamicSelectOption.dictUrl) {
                this.isUserDefaultDict = false;
                this.customQuery();
            }
        },
        data () {
            return {
                customDictList: [],

                isUserDefaultDict: true,
            };
        },
        computed: {
            optionsList () {
                if (this.isUserDefaultDict) {
                    const list = this.dynamicDict[this.item.parentKey].map(oldItem => {
                        const o = {
                            value: oldItem[this.dynamicSelectOption.value],
                            label: oldItem[this.dynamicSelectOption.label],
                            disabled: oldItem[this.dynamicSelectOption.disabled],
                        };
                        if (this.item.ignoreValueList && this.item.ignoreValueList.indexOf(o.value) > -1) {
                            o.disabled = true;
                        }
                        return o;
                    }).filter(oldItem => {
                        if (this.item.filterModel === 'hidden' && oldItem.disabled) {
                            return false;
                        }
                        return true;
                    });

                    return list;
                }

                const list = this.customDictList.map(dictItem => {
                    if (this.item.ignoreValueList && this.item.ignoreValueList.indexOf(dictItem.value) > -1) {
                        dictItem.disabled = true;
                    }
                    return dictItem;
                }).filter(dictItem => {
                    if (this.item.filterModel === 'hidden' && dictItem.disabled) {
                        return false;
                    }
                    return true;
                });

                return list;
            },

            textModelValue () {
                if (this.isUserDefaultDict) {
                    const content = this.dynamicDict[this.item.parentKey] && this.dynamicDict[this.item.parentKey].find((item) => {
                        return item[this.dynamicSelectOption.value] === this.val;
                    });
                    return content && content[this.dynamicSelectOption.label] || '';
                }
                let text = '';
                this.customDictList.forEach(item => {
                    if (item.value === this.val) {
                        text = item.label;
                    }
                });
                return text || '';
            },

            val: {
                get () {
                    return this.value;
                },
                set (v) {
                    this.$emit('input', v);
                    this._valueLink(v);
                    if (this.formItemType !== 'childForm') {
                        this.statusChangeFn.valueUpdateEvent({
                            [this.item.key]: v,
                        });
                    } else {
                        this.childChangeData.valueUpdateEvent({
                            [this.item.key]: v,
                        }, this.childFormIndex);
                    }
                }
            },
        },
        methods: {
            customQuery () {
                let payload = {};
                if (this.item.dynamicSelectOption.queryKey) {
                    payload[this.item.dynamicSelectOption.queryKey] = this.item.dynamicSelectOption.queryData;
                } else {
                    payload = this.item.dynamicSelectOption.queryData;
                }

                this.getSpecialAxios().post(this.item.dynamicSelectOption.dictUrl, payload).then(res => {
                    let data;
                    if (res.request && res.headers) {
                        data = res.data;
                    } else {
                        data = res;
                    }
                    if (data.code === 200) {
                        if (data.data) {
                            let list = data.data;
                            this.item.dynamicSelectOption.dataKeyPath.split('.').forEach(key => {
                                list = list[key];
                            });

                            const dictList = list.map(item => {
                                return {
                                    label: item[this.item.dynamicSelectOption.label],
                                    value: item[this.item.dynamicSelectOption.value],
                                    disabled: item.disabled,
                                };
                            });
                            this.customDictList = dictList;
                        } else {
                            this.$message.error('【' + this.item.label + '】字典数据获取失败');
                        }
                    } else {
                        this.$message.error(data.msg || '【' + this.item.label + '】字典数据获取失败');
                    }
                }).catch(() => {
                    this.$message.error('【' + this.item.label + '】字典数据获取失败');
                });
            }
        }
    };
</script>

<style scoped lang="less">

    .form-item-box {
        /deep/ .el-input {
            position: relative;
            width: 100%;
            height: 36px;

            .el-input__inner {
                position: absolute;
                width: 100%;
                height: 36px;
                line-height: 36px;
                padding-right: 10px;
                padding-left: 12px;
            }
        }
    }
</style>
