<template>
  <div>
    <div
      class="card bww"
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
              <form name="bwwForm" v-on:submit.prevent="BWW" autocomplete="off">
                <p class="title search-bar">
                  {{ item.title }}
                  <input name="bww" v-on:keyup="BWW" type="text" />
                </p>
              </form>
              <p class="result is-6" v-html="result"></p>
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
  name: 'BWW',
  props: {
    item: Object,
    result: {
      type: String,
      default: "",
    },
  },
  computed: {},
  methods: {
    BWW(event) {
      let txt = event.target.value;
      let buchstabe = '',
        quersumme = 0,
        quersummeEinstellig = 0,
        summe = 0,
        werte = '';

      txt = txt.toLowerCase();
      txt = txt.replace(/[^a-zäöüß0-9]/g, '');
      for (let i = 0; i < txt.length; i++) {
        buchstabe = txt.charAt(i);
        let buchstabenwert;

        switch (buchstabe) {
          case 'ä':
            buchstabenwert = 27;
            break;
          case 'ö':
            buchstabenwert = 28;
            break;
          case 'ü':
            buchstabenwert = 29;
            break;
          case 'ß':
            buchstabenwert = 30;
            break;
          default:
            buchstabenwert = txt.charCodeAt(i) - 96;
        }

        if (buchstabenwert < 0) {
          buchstabenwert += 48; // Zahlen
        }

        summe += buchstabenwert;
        werte += `${buchstabe}:<b>${buchstabenwert}</b> `;
      }

      for (let i = 0; i < summe.toString().length; i++) {
        quersumme += parseInt(summe.toString().charAt(i), 10);
      }

      if (quersumme > 9) {
        quersummeEinstellig = summe % 9 || 9;
        quersumme = `${quersumme} (${quersummeEinstellig})`;
      }

      this.result = werte ? `${werte}<br /> Σ ${summe} QS ${quersumme}` : '';
    }
  }
};
</script>

<style scoped lang="scss">
.media-content {
  margin-top: -0.1em;

  .title {
    width: 100%;
    padding-right: 4px;
    font-weight: 400;
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

  .result {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    font-size: 0.8em;
    color: var(--text-subtitle);
    padding-left: 2px;
  }
}
</style>
