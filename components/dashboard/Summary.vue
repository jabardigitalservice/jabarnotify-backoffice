<template>
  <v-card class="statistic-new-active mx-auto height-100" outlined>
    <v-card-text class="card">
      <div class="head mb-3">{{ title }}</div>
      <div class="total mb-3">{{ total.sent + total.failed }}</div>
      <div class="information">
        <div>
          <span class="sub-total">sent</span>
          <span class="sub-label">{{ total.sent }}</span>
        </div>
        <div>
          <span class="sub-total">failed</span>
          <span class="sub-label">{{ total.failed }}</span>
        </div>
      </div>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: null
    },
    total: {
      type: Object,
      default: () => {
        return {
          sent: 0,
          failed: 0
        }
      }
    }
  }
}
</script>

<style lang="scss">
.statistic-new-active {
  .card {
    .head {
      font-size: 12px;
      font-weight: 500;
      text-transform: uppercase;
    }

    .total {
      color: #000000;
      font-size: 24px;
      font-weight: 500;
    }

    .information {
      .sub-total {
        font-weight: 150;
        color: #696866;
      }

      .sub-label {
        font-size: 11px;
      }
    }
  }
}
</style>

<style scoped>
.height-100 {
  height: 100%;
}
</style>
