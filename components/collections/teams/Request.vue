<template>
  <div>
    <div class="transition duration-150 ease-in-out row-wrapper">
      <div>
        <button
          class="icon"
          @click="!doc ? selectRequest() : {}"
          v-tooltip="!doc ? $t('use_request') : ''"
        >
          <i v-if="isSelected" class="mx-3 text-green-400 material-icons">check_circle</i>

          <span v-else :class="getRequestLabelColor(request.method)">{{ request.method }}</span>
          <span>{{ request.name }}</span>
        </button>
      </div>
      <v-popover v-if="!saveRequest">
        <button
          v-if="collectionsType.selectedTeam.myRole !== 'VIEWER'"
          class="tooltip-target icon"
          v-tooltip="$t('more')"
        >
          <i class="material-icons">more_vert</i>
        </button>
        <template slot="popover">
          <div>
            <button
              class="icon"
              @click="
                $emit('edit-request', {
                  collectionIndex,
                  folderIndex,
                  folderName,
                  request,
                  requestIndex,
                })
              "
              v-close-popover
            >
              <i class="material-icons">edit</i>
              <span>{{ $t("edit") }}</span>
            </button>
          </div>
          <div>
            <button class="icon" @click="confirmRemove = true" v-close-popover>
              <i class="material-icons">delete</i>
              <span>{{ $t("delete") }}</span>
            </button>
          </div>
        </template>
      </v-popover>
    </div>
    <SmartConfirmModal
      :show="confirmRemove"
      :title="$t('are_you_sure_remove_request')"
      @hide-modal="confirmRemove = false"
      @resolve="removeRequest"
    />
  </div>
</template>

<script>
export default {
  props: {
    request: Object,
    collectionIndex: Number,
    folderIndex: Number,
    folderName: String,
    requestIndex: [Number, String],
    doc: Boolean,
    saveRequest: Boolean,
    collectionsType: Object,
    picked: Object,
  },
  data() {
    return {
      dragging: false,
      requestMethodLabels: {
        get: "text-green-400",
        post: "text-yellow-400",
        put: "text-blue-400",
        delete: "text-red-400",
        default: "text-gray-400",
      },
      confirmRemove: false,
    }
  },
  computed: {
    isSelected() {
      return (
        this.picked &&
        this.picked.pickedType === "teams-request" &&
        this.picked.requestID === this.requestIndex
      )
    },
  },
  methods: {
    selectRequest() {
      if (this.$props.saveRequest)
        this.$emit("select", {
          picked: {
            pickedType: "teams-request",
            requestID: this.requestIndex,
          },
        })
      else this.$store.commit("postwoman/selectRequest", { request: this.request })
    },
    removeRequest() {
      this.$emit("remove-request", {
        collectionIndex: this.$props.collectionIndex,
        folderName: this.$props.folderName,
        requestIndex: this.$props.requestIndex,
      })
    },
    getRequestLabelColor(method) {
      return this.requestMethodLabels[method.toLowerCase()] || this.requestMethodLabels.default
    },
  },
}
</script>
