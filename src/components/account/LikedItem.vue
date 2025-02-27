<template>
	<div class="list__apt">
		<div class="apt__images">
			<template v-if="isLogin">
				<AIcon
					v-if="apt.likedStatus"
					@click="removeLikedItem(index, apt.aptCode)"
					type="heart"
					theme="filled"
					style="color: #f6685e"
				/>
				<AIcon
					v-else
					@click="addLikedItem(index, apt.aptCode)"
					type="heart"
					style="color: #f6685e"
				/>
			</template>
			<img :src="apt.img" :alt="apt.aptName" />
		</div>
		<div class="apt__apt-info" @click="linkSearchPageWithData(apt)">
			<h2 class="apt-info__name">{{ apt.aptName }}</h2>
			<h1 class="apt-info__price">{{ apt.recentPrice | convertAptPrice }}</h1>
			<p class="apt-info__build-year">건축연도: {{ apt.buildYear }}</p>
			<p class="apt-info__address">
				{{ apt.sidoName }} {{ apt.gugunName }} {{ apt.dongName }}
				{{ apt.jibun }}
			</p>
		</div>
	</div>
</template>

<script>
import { mapGetters, mapMutations, mapActions } from 'vuex';
import { postLikedItem, deleteLikedItem } from '@/api/user';
import Swal from 'sweetalert2';

export default {
	props: {
		apt: {
			type: Object,
			default: () => ({}),
		},
		index: {
			type: Number,
		},
	},
	computed: {
		...mapGetters('userStore', ['isLogin', 'getId']),
	},
	created() {
		const userData = {
			userId: this.getId,
		};

		this.GET_LIKED_APT_CODES(userData);
	},
	methods: {
		...mapMutations('searchStore', ['SELECT_ITEM', 'ON_LIKED', 'OFF_LIKED']),
		...mapActions('searchStore', ['GET_APT_LIST_BY_SEARCH_WITH_AUTH']),
		...mapActions('userStore', ['ADD_LIKED_APT_CODES', 'GET_LIKED_APT_CODES']),
		async addLikedItem(index, aptCode) {
			const aptData = {
				userId: this.getId,
				aptCode: aptCode,
			};

			this.ON_LIKED(index);
			await postLikedItem(aptData);
		},
		async removeLikedItem(index, aptCode) {
			const aptData = {
				userId: this.getId,
				aptCode: aptCode,
			};

			this.OFF_LIKED(index);
			await deleteLikedItem(aptData);
		},
		async linkSearchPageWithData(apt) {
			try {
				const recentSearchData = {
					userId: this.getId,
					dongName: apt.dongName,
				};

				await this.GET_APT_LIST_BY_SEARCH_WITH_AUTH(recentSearchData);
				this.SELECT_ITEM(apt);
				this.$router.push('/search');
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
	},
};
</script>

<style lang="scss" scoped>
@import './scss/likedItem.scss';
</style>
