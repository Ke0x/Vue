<template>
  <div v-if="totalPages() > 0" class="pagination-wrapper">
    <span v-if="showPreviousLink()" class="pagination-btn" v-on:click="updatePage(pageNumber - 1)"> Retour </span>
    {{ pageNumber + 1 }} / {{ totalPages() }}
    <span v-if="showNextLink()" class="pagination-btn" v-on:click="updatePage(pageNumber + 1)"> Suivant </span>
  </div>
</template>

<script>
export default {
    name: 'pagination',
    props: ['item', 'pageNumber', 'Size', 'page:update'],
    methods: {
      updatePage(pageNumber) {
        
        this.$emit('page:update', pageNumber);
      },
      totalPages() {
        return Math.ceil(this.item.length / this.Size);
      },
      showPreviousLink() {
        return this.pageNumber == 0 ? false : true;
      },
      showNextLink() {
        return this.pageNumber == (this.totalPages() - 1) ? false : true;
      }
    }
  }
</script>

<style>
.pagination-btn {
  cursor: pointer;
  font-weight: bold;
}
.pagination-btn:hover {
  opacity: 0.8;
}
.pagination-wrapper {
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Old versions of Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently */
}
</style>