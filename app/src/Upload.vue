<template lang="pug">
  .upload-app#uploadApp
    a.btn.btn-sm.btn-info.btn-new-session(@click='newSession()', title='New Upload')
      icon.fa-fw(name="cloud-upload-alt")
      span.hidden-xs  Novo slanje
    .alert.alert-danger(v-show="error")
      strong
        icon.fa-fw(name="exclamation-triangle")
        |  {{ error }}
    .well(v-show="state === 'uploaded'")
      h3.text-success
        icon.fa-fw(name="check")
        |  Datoteke su uspješno poslane
      div.share-link
        span.title Poveznica za preuzimanje:
        |
        a(:href='shareUrl') {{ shareUrl }}
    .row.overall-process(v-show="state === 'uploading'")
      .col-xs-12
        icon.pull-left(name="spinner", scale="2", spin="")
        .progress
          .progress-bar.progress-bar-success.progress-bar-striped.active(:style="{width: percentUploaded+'%'}")
            span(v-show='percentUploaded>10') {{ percentUploaded }}%
    .row
      .col-sm-12
        files
    .row
      .col-sm-12
        .text-right(v-show='files.length && !disabled')
          button.btn.btn-lg.btn-success(@click="$store.dispatch('upload/upload')")
            icon.fa-fw(name="upload")
            |  Pošalji
        .text-right(v-show="state === 'uploadError'")
          button.btn.btn-lg.btn-success(@click="$store.dispatch('upload/upload')")
            icon.fa-fw(name="upload")
            |  Pokušaj ponovo
</template>

<script type="text/babel">
  "use strict";
  import { mapState, mapGetters } from 'vuex';

  import Settings from './Upload/Settings.vue';
  import Files from './Upload/Files.vue';
  import Clipboard from './common/Clipboard.vue'
  import 'vue-awesome/icons/cloud-upload-alt';
  import 'vue-awesome/icons/upload';
  import 'vue-awesome/icons/check';
  import 'vue-awesome/icons/spinner';
  import 'vue-awesome/icons/envelope';
  import 'vue-awesome/icons/exclamation-triangle';


  export default {
    name: 'App',
    components: {
      Settings,
      Files,
      Clipboard,
    },

    computed: {
      ...mapState(['error', 'disabled', 'state']),
      ...mapState('upload', ['sid', 'files']),
      ...mapGetters('upload', ['percentUploaded', 'shareUrl']),
      mailLnk: function() {
        return this.$store.state.config
          && this.$store.state.config.mailTemplate
          && this.$store.state.config.mailTemplate.replace('%%URL%%', this.shareUrl);
      }
    },

    watch: {
      state: function(val) {
        if (val === 'uploaded' || val === 'uploadError') {
          const el = document.getElementById('uploadApp');
          if (!el || !el.scrollIntoView) return;
          el.scrollIntoView(true);
        }
      }
    },

    methods: {
      newSession() {
        if (!confirm('Želiš li poslati nove datoteke?')) return;
        document.location.reload();
      }
    }

  }
</script>
