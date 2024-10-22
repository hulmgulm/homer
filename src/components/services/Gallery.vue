<template>
  <div>
    <div
      class="card"
      :style="`background-color:${item.background};`"
      :class="item.class"
    >
      <a :href="item.url" :target="item.target" rel="noreferrer">
        <div class="card-content">
          <div class="media">
            <div v-if="item.logo" class="media-left">
              <figure class="image is-48x48">
                <img :src="item.logo" :alt="`${item.name} logo`" />
              </figure>
            </div>
            <div class="media-content">
              <form
                name="galleryForm"
                v-on:submit.prevent="Gallery"
                autocomplete="off"
              >
                <p class="title is-4">{{ item.name }}</p>
                <p class="subtitle is-6 search-bar">
                  <input name="gallery" type="text" />
                </p>
              </form>
            </div>
          </div>
          <div class="tag" :class="item.tagstyle" v-if="item.tag">
            <strong class="tag-text">#{{ item.tag }}</strong>
          </div>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Gallery',
  props: {
    item: Object
  },
  computed: {},
  methods: {
    Gallery(event) {
      const code = event.target[0].value;
      document.location = `${this.item.targetUrl}${code}`;
    }
  }
};
</script>

<style scoped lang="scss">
.media-content {
  margin-top: -0.1em;

  .title {
    margin-bottom: 0.4em;
  }

  .subtitle {
    margin-top: -0.4em;
    width: 100%;
    padding-right: 4px;
  }

  input {
    padding: 3px;
    width: 100%;
    margin: 2px;
    font-size: 12px;
    height: 2em;
  }

  input:focus {
    width: 100%;
  }
}
</style>
