<template>
        
        <div class="note-container">
                <div class="note" :style="{'background-color': color}">
                    <input
                        class="delete-btn" 
                        type="button" 
                        value="×" 
                        @click="deleteHendler(index)"
                    />
                    
                    <div 
                        v-if="modelValue && index !== isEditingIndex"
                        class="note-text"
                        @dblclick="startEditing(note)"
                    >
                        {{ modelValue }}
                    </div>
                    <textarea
                        v-else-if="isEditingIndex == index"
                        type="text"
                        :value="modelValue"
                        @blur="stopEditing"
                        @keyup.enter.prevent="stopEditing"
                        @keyup.escape="cancelEditing"
                        v-focus
                        class="cnt"
                        maxlength='250'
                        placeholder="Enter note description here"
                        @input="$emit('update:modelValue', $event.target.value.trim())"
                    />
                    <span 
                        v-else
                        @click="startEditing"
                        >
                        {{ modelValue }}
                        <span class="edit-icon">✏️</span>
                    </span>
                    
                </div>
        </div>
</template>
<script>
export default {
  name: "note-сard",
  props: {
    modelValue: {
      type: String,
      default: ''
    },
    index: String,
    color: String,
  },
  emits: ['deleteNote', 'updateNote', 'update:modelValue'],
  data() {
        return {
            isEditingIndex: "",
            originalTitle: "",
        };
    },
    methods: {
        deleteHendler(id) {
            this.$emit("deleteNote", id);
        },
        startEditing() {
            this.originalTitle = this.modelValue;
            this.isEditingIndex = this.index;
        },
        stopEditing() {
            this.isEditingIndex = "";
            //note.title= note.title.trim();
            this.$emit("updateNote");
        },
        cancelEditing() {
            this.$emit('update:modelValue', this.originalTitle);
            this.isEditingIndex = "";
        }
    }
}
</script>
<style lang="scss" scoped>
.note {
    font-family: "Zhuan", sans-serif;
    font-weight: normal;
    font-size: 18px;
    display: flex;
    width: 180px;
    min-height: 200px;
    justify-content: center;
    align-items: center;
    text-align: center;
    margin: 8px;
    box-shadow: 4px 4px 0.4em lightslategray;
    overflow: auto;
    height: 200px;
}
.note:hover { 
    .delete-btn {
        display: flex;
    }
}
.note-text {
    word-break: break-word;
}
.delete-btn {
    display: none;
    color: red;
    right: 24px;
    position: absolute;
    top: 12px;
    border-radius: 15px;
    width: 25px;
    height: 25px;
    border: none;
    font-size: 20px;
    cursor: pointer;
    transition: all 0.4s;
}
.delete-btn:hover {
    background-color: rgb(194, 191, 189);
}
.edit-icon {
    cursor: pointer;
}
textarea {
    background-color: transparent;
    border: none;
    resize: vertical;
    font-family: "Gloria Hallelujah", cursive;
    width: 100%;
    padding: 5px;
    &:focus {
      outline: none;
      border: none;
      box-shadow: 0 0 5px 1px rgba(0,0,0,.2) inset;
    }
    &.cnt {
      min-height: 190px;
      font-size: 16px;
    }
  }

</style>