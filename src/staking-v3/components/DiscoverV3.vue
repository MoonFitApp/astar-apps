<template>
  <div>
    <div class="wrapper--discover">
      <feature-dapp />
      <!-- <staking /> -->
      <register-banner />
      <leaderboard />
      <leaderboard-vote />
      <dynamic-ads-area />

      <div class="container--dapps-data">
        <div class="row--dapps-data-header">
          <toggle-buttons
            :captions="[$t('stakingV3.ourDapps'), $t('stakingV3.ourData')]"
            @button-selected="toggleDapps"
          />
          <input
            v-if="displayIndex === 0"
            v-model="searchText"
            type="text"
            :placeholder="$t('stakingV3.searchDapps')"
            class="input--search"
          />
        </div>
        <div v-if="displayIndex === 0" class="dapps">
          <dapps category="DeFi" :search="searchText" />
          <dapps category="NFT" :search="searchText" />
          <dapps category="Tooling" :search="searchText" />
          <dapps category="Utility" :search="searchText" />
          <dapps category="Others" :search="searchText" />
        </div>
        <data-list v-if="displayIndex === 1" />
      </div>
    </div>
    <div class="bg--discover" />
    <div v-if="isVotingPeriod" class="bg--voting-period">
      <img :src="require('/src/staking-v3/assets/vote_hero_bg.webp')" alt="" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import Dapps from './Dapps.vue';
import FeatureDapp from './FeatureDapp.vue';
import Leaderboard from './leaderboard/Leaderboard.vue';
import LeaderboardVote from './leaderboard/LeaderboardVote.vue';
// import Staking from './my-staking/Staking.vue';
import { useDappStaking } from '../hooks';
import DynamicAdsArea from './DynamicAdsArea.vue';
import ToggleButtons from './ToggleButtons.vue';
import DataList from './data/DataList.vue';
import RegisterBanner from './RegisterBanner.vue';

export default defineComponent({
  components: {
    FeatureDapp,
    Dapps,
    Leaderboard,
    LeaderboardVote,
    DataList,
    DynamicAdsArea,
    ToggleButtons,
    RegisterBanner,
  },
  setup() {
    const displayIndex = ref<number>(0);

    const toggleDapps = (index: number): void => {
      displayIndex.value = index;
    };

    const searchText = ref<string>('');

    const { isVotingPeriod } = useDappStaking();

    return { displayIndex, searchText, isVotingPeriod, toggleDapps };
  },
});
</script>

<style lang="scss" scoped>
@use './styles/discover-v3.scss';
</style>
