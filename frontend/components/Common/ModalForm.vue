<template>
    <modal
        :id="id"
        ref="$modal"
        :size="size"
        :title="title"
        :busy="loading"
        @shown="onShown"
        @hidden="onHidden"
    >
        <template #default="slotProps">
            <div
                v-if="error != null"
                class="alert alert-danger"
            >
                {{ error }}
            </div>

            <form
                class="form vue-form"
                @submit.prevent="doSubmit"
            >
                <slot
                    name="default"
                    v-bind="slotProps"
                />
                <invisible-submit-button />
            </form>
        </template>

        <template #modal-footer="slotProps">
            <slot
                name="modal-footer"
                v-bind="slotProps"
            >
                <button
                    class="btn btn-secondary"
                    type="button"
                    @click="hide"
                >
                    {{ $gettext('Close') }}
                </button>
                <button
                    class="btn"
                    :class="(disableSaveButton) ? 'btn-danger' : 'btn-primary'"
                    type="submit"
                    @click="doSubmit"
                >
                    <slot name="save-button-name">
                        {{ $gettext('Save Changes') }}
                    </slot>
                </button>
            </slot>
        </template>

        <template
            v-for="(_, slot) of useSlotsExcept(['default', 'modal-footer'])"
            #[slot]="scope"
        >
            <slot
                :name="slot"
                v-bind="scope"
            />
        </template>
    </modal>
</template>

<script setup lang="ts">
import InvisibleSubmitButton from "~/components/Common/InvisibleSubmitButton.vue";
import {ref} from "vue";
import useSlotsExcept from "~/functions/useSlotsExcept";
import Modal from "~/components/Common/Modal.vue";
import {ModalTemplateRef, useHasModal} from "~/functions/useHasModal.ts";

const props = withDefaults(
    defineProps<{
        title: string,
        size?: string,
        centered?: boolean,
        id?: string,
        loading?: boolean,
        disableSaveButton?: boolean,
        noEnforceFocus?: boolean,
        error?: string,
    }>(),
    {
        size: 'lg',
        centered: false,
        id: 'edit-modal',
        loading: false,
        disableSaveButton: false,
        noEnforceFocus: false
    }
);

const emit = defineEmits(['submit', 'shown', 'hidden']);

const doSubmit = () => {
    emit('submit');
};

const onShown = () => {
    emit('shown');
};

const onHidden = () => {
    emit('hidden');
};

const $modal = ref<ModalTemplateRef>(null);
const {show, hide} = useHasModal($modal);

defineExpose({
    show,
    hide
});
</script>
