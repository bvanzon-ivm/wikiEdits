<template lang="pug">
  v-app
    .unauthorized
      .unauthorized-content
        img.animated.fadeIn(src='/_assets/svg/icon-delete-shield.svg', alt='Unauthorized')
        .headline {{$t('unauthorized.title')}}
        .subtitle-1.mt-3 {{$t('unauthorized.action.' + action)}}
        v-btn.mt-5(href='/login', x-large)
          v-icon(left) mdi-login
          span {{$t('unauthorized.login')}}
        v-btn.mt-5(color='red lighten-4', outlined, @click='goBack')
          v-icon(left) mdi-arrow-left
          span {{$t('unauthorized.goback')}}
        v-btn.mt-5(color='primary', @click='requestAccess')
          v-icon(left) mdi-lock-question
          span Toegang aanvragen
        p.mt-3.text-success(v-if="statusMessage") {{ statusMessage }}
</template>

<script>
export default {
  props: {
    action: {
      type: String,
      default: 'view'
    }
  },
  data() {
    return {
      statusMessage: ''
    }
  },
  methods: {
    goBack() {
      window.history.go(-1);
    },
    async requestAccess() {
      const userName = WIKI.$store.get('user/name') || 'Onbekende gebruiker';

      const pathParts = window.location.pathname.split('/').filter(Boolean);
      const pageTitleRaw = pathParts[pathParts.length - 1] || 'Onbekende pagina';
      const pageTitle = pageTitleRaw.replace(/[-_]/g, ' ').replace(/\b\w/g, c => c.toUpperCase());

      const payload = {
        text: `Gebruiker *${userName}* vraagt toegang tot de pagina: *${pageTitle}*`
      };

      try {
        const response = await fetch('https://hooks.slack.com/services/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', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });

        if (response.ok) {
          this.statusMessage = 'Toegangsverzoek is verstuurd!';
        } else {
          this.statusMessage = 'Fout bij versturen verzoek.';
        }
      } catch (error) {
        this.statusMessage = 'Er is een fout opgetreden.';
      }
    }

  }
}
</script>

<style lang="scss">
.unauthorized {
  text-align: center;
  margin-top: 50px;

  .unauthorized-content {
    max-width: 400px;
    margin: 0 auto;
  }
}
</style>
