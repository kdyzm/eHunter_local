<template>
<div ref="dialog" class="simple-dialog" @click.stop="" @wheel.stop="">
    <div class="background" @click="close"></div>
    <article>
        <h4>{{ data.title }}</h4>
        <p class="markdown" v-html="MdRenderer.render(data.text)"></p>
        <div class="operation-bar">
            <flat-button
                class="operation"
                v-for="operation in data.operations"
                :key="operation.name"
                :label="operation.name"
                :type="getType(operation.type)"
                mode="inline"
                @click="onClick(operation)">
            </flat-button>
        </div>
    </article>
</div>
</template>

<script>
import DialogBean from '../../bean/DialogBean';
import FlatButton from './FlatButton.vue';
import MdRenderer from '../../utils/MdRenderer';
import * as tags from '../../assets/value/tags';

export default {
    name: 'SimpleDialog',

    props: {
        data: {
            type: DialogBean
        }
    },

    mounted() {
      setTimeout(() => {
        document.addEventListener('keydown', this.enter);
      }, 500);
    },

    beforeDestroy() {
      document.removeEventListener('keydown', this.enter);
    },

    components: { FlatButton },

    data() {
        return {
            MdRenderer
        };
    },
  
    methods: {
        getType(tag) {
            switch (tag) {
                case tags.DIALOG_OPERATION_TYPE_PLAIN:
                    return 'plain';
                case tags.DIALOG_OPERATION_TYPE_POSITIVE:
                    return 'positive';
                case tags.DIALOG_OPERATION_TYPE_NEGATIVE:
                    return 'negative';
                case tags.DIALOG_OPERATION_TYPE_WARNING:
                    return 'warning';
            }
        },
        onClick(operation) {
            if (operation.onClick()) {
                this.$emit('close');
            }
        },
        close(isCompulsive) {
            if (this.data.type === tags.DIALOG_NORMAL) {
                this.$emit('close');
            }
        },
        enter(e) {
          if (e.key === 'Enter' && this.data.operations.length === 1) {
            this.data.operations[0].onClick();
            this.$emit('close');
          }
        }
    }
};
</script>

<style lang="scss" scoped>
@import "../../style/_responsive";
@import "../../style/_variables";

div {
    display: flex;
}

.simple-dialog {
    box-shadow: 1px 1px 5px 1px rgba(0, 0, 0, 0.1);
    > .background {
        flex: 1;
        background: $modal_view_bg;
    }
    > article {
        display: flex;
        flex-direction: column;
        position: absolute;
        background: white;
        border-radius: 3px;
        min-width: 430px;
        min-height: 110px;
        max-width: 50%;
        max-height: 88%;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        padding: 19px 22px;
        > h4 {
            box-sizing: border-box;
            font-size: 22px;
            text-align: left;
            padding-bottom: 10px;
            color: #000000;
            font-weight: lighter;
        }
        > p {
            color: rgba(0, 0, 0, 0.8);
            text-align: left;
            font-size: 16px;
            overflow: auto;
            flex: 1;
        }
        > .operation-bar {
            flex-direction: row-reverse;
            margin-top: 10px;
            flex-shrink: 0;
        }
    }
}
</style>