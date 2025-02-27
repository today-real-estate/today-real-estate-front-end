<template>
	<div class="map__header">
		<div class="map__search">
			<div class="search__form">
				<input
					type="text"
					v-model="searchDong"
					@keyup.enter.prevent="searchAptListByDong"
					placeholder="동 이름을 검색하세요."
				/>
			</div>
		</div>
		<div class="map__filters">
			<div class="filters__select">
				<ASelect
					v-model="selectedSidoCode"
					@change="selectSido"
					style="
						position: relative;
						z-index: 30;
						width: 220px;
						height: 34px;
						margin-right: 8px;
					"
				>
					<ASelectOption v-for="sido in sidoList" :key="sido.code">
						{{ sido.sidoName }}
					</ASelectOption>
				</ASelect>
				<ASelect
					v-model="selectedGugunCode"
					@change="selectGugun"
					style="
						position: relative;
						z-index: 30;
						width: 220px;
						height: 34px;
						margin-right: 8px;
					"
				>
					<ASelectOption v-for="gugun in gugunList" :key="gugun.code">
						{{ gugun.gugunName }}
					</ASelectOption>
				</ASelect>
				<ASelect
					v-model="selectedDongCode"
					@change="selectDong"
					style="
						position: relative;
						z-index: 30;
						width: 220px;
						height: 34px;
						margin-right: 8px;
					"
				>
					<ASelectOption v-for="dong in dongList" :key="dong.code">
						{{ dong.dongName }}
					</ASelectOption>
				</ASelect>
			</div>
			<div class="filters__btns">
				<button>
					<a-icon type="control" />
				</button>
				<button class="btns__clear-btn" @click="clearSearch">
					<a-icon type="sync" />
				</button>
			</div>
		</div>
	</div>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from 'vuex';
import { putRecentSearch } from '@/api/user';
import EventBus from '@/utils/eventBus';
import Swal from 'sweetalert2';

export default {
	data() {
		return {
			searchDong: this.searchDongName,
			selectedSidoCode: '시를 선택하세요',
			selectedGugunCode: '구를 선택하세요',
			selectedDongCode: '동을 선택하세요',
		};
	},
	computed: {
		...mapState('searchStore', [
			'searchDongName',
			'sidoList',
			'gugunList',
			'dongList',
		]),
		...mapGetters('userStore', ['isLogin', 'getId']),
	},
	methods: {
		...mapMutations('userStore', ['SET_RECENT_SEARCH']),
		...mapMutations('searchStore', [
			'CLEAR_SIDO_LIST',
			'CLEAR_GUGUN_LIST',
			'CLEAR_DONG_LIST',
			'CLEAR_APT_LIST',
			'BACK_TO_ITEM_LIST',
			'OFF_ROAD_VIEW',
		]),
		...mapActions('searchStore', [
			'GET_SIDO_LIST',
			'GET_GUGUN_LIST',
			'GET_DONG_LIST',
			'GET_APT_LIST_BY_GUGUN',
			'GET_APT_LIST_BY_DONG',
			'GET_APT_LIST_BY_SEARCH',
			'GET_APT_LIST_BY_GUGUN_WITH_AUTH',
			'GET_APT_LIST_BY_DONG_WITH_AUTH',
			'GET_APT_LIST_BY_SEARCH_WITH_AUTH',
		]),
		async getSidoList() {
			try {
				await this.GET_SIDO_LIST();
			} catch (error) {
				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${error}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			}
		},
		async getGugunList(sidoCode) {
			const sidoData = {
				sido: sidoCode,
			};

			try {
				await this.GET_GUGUN_LIST(sidoData);
			} catch (error) {
				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${error}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			}
		},
		async getDongList(gugunCode) {
			const gugunData = {
				gugun: gugunCode,
			};

			try {
				await this.GET_DONG_LIST(gugunData);
			} catch (error) {
				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${error}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			}
		},
		async getAptListByGugun(gugunCode) {
			try {
				if (this.isLogin) {
					const gugunData = {
						userId: this.getId,
						gugun: gugunCode,
					};

					await this.GET_APT_LIST_BY_GUGUN_WITH_AUTH(gugunData);
				} else {
					const gugunData = {
						gugun: gugunCode,
					};

					await this.GET_APT_LIST_BY_GUGUN(gugunData);
				}

				this.BACK_TO_ITEM_LIST();
			} catch (error) {
				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${error}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			}
		},
		async getAptListByDong(dongCode) {
			try {
				if (this.isLogin) {
					const dongData = {
						userId: this.getId,
						dong: dongCode,
					};

					await this.GET_APT_LIST_BY_DONG_WITH_AUTH(dongData);
				} else {
					const dongData = {
						dong: dongCode,
					};

					await this.GET_APT_LIST_BY_DONG(dongData);
				}

				this.BACK_TO_ITEM_LIST();
			} catch (error) {
				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${error}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			}
		},
		async searchAptListByDong() {
			try {
				if (this.isLogin) {
					const searchData = {
						userId: this.getId,
						dongName: this.searchDong,
					};

					await this.GET_APT_LIST_BY_SEARCH_WITH_AUTH(searchData);
				} else {
					const searchData = {
						dongName: this.searchDong,
					};

					await this.GET_APT_LIST_BY_SEARCH(searchData);
				}

				this.CLEAR_GUGUN_LIST();
				this.CLEAR_DONG_LIST();
				this.selectedSidoCode = '시를 선택하세요';
				this.selectedGugunCode = '구를 선택하세요';
				this.selectedDongCode = '동을 선택하세요';
				EventBus.$emit('displayKakaoMapMarker');
				this.BACK_TO_ITEM_LIST();

				if (this.isLogin) {
					const recentSearchData = {
						id: this.getId,
						recentSearch: this.searchDongName,
					};

					putRecentSearch(recentSearchData);
					this.SET_RECENT_SEARCH(this.searchDongName);
				}
			} catch (error) {
				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${error}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			}
		},
		selectSido() {
			this.CLEAR_GUGUN_LIST();
			this.CLEAR_DONG_LIST();
			this.selectedGugunCode = '구를 선택하세요';
			this.selectedDongCode = '동을 선택하세요';

			if (!isNaN(this.selectedSidoCode)) {
				this.getGugunList(this.selectedSidoCode);
			}
		},
		async selectGugun() {
			this.CLEAR_DONG_LIST();
			this.selectedDongCode = '동을 선택하세요';

			if (!isNaN(this.selectedGugunCode)) {
				this.getDongList(this.selectedGugunCode);
				await this.getAptListByGugun(this.selectedGugunCode);
			}
		},
		async selectDong() {
			if (!isNaN(this.selectedDongCode)) {
				await this.getAptListByDong(this.selectedDongCode);
				EventBus.$emit('displayKakaoMapMarker');
			}
		},
		clearSearch() {
			this.CLEAR_APT_LIST();
			this.selectedSidoCode = '시를 선택하세요';
			this.selectedGugunCode = '구를 선택하세요';
			this.selectedDongCode = '동을 선택하세요';
		},
	},
	created() {
		this.getSidoList();
	},
};
</script>

<style lang="scss" scoped>
@import './scss/mapHeader.scss';
</style>
