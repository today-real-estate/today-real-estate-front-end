<template>
	<nav class="header__nav">
		<RouterLink to="/search" class="nav__link">지도</RouterLink>
		<RouterLink to="/news" class="nav__link">오늘의 뉴스</RouterLink>
		<RouterLink to="/account/liked" v-if="isLogin" class="nav__link">
			관심목록
		</RouterLink>
		<RouterLink to="/service/inquiry" class="nav__link">1:1 문의</RouterLink>
		<template v-if="!isLogin">
			<RouterLink to="/login" class="nav__link link--user">
				로그인 <span class="nav__bar"></span> 회원가입
			</RouterLink>
		</template>
		<template v-else>
			<div class="header__user">
				<ADropdown-button size="large">
					{{ getNickname }}
					<AMenu slot="overlay" style="padding: 0; border-radius: 3px">
						<AMenu-item
							key="1"
							style="
								padding: 16px 25px;
								border-bottom: 1px solid #e9e9e9;
								border-radius: 3px 3px 0 0;
							"
						>
							<RouterLink
								to="/account/edit"
								style="
									width: 200px;
									font-size: 16px;
									font-family: 'Spoqa Han Sans Neo', 'sans-serif';
								"
							>
								내 정보
							</RouterLink>
						</AMenu-item>
						<AMenu-item
							key="2"
							style="padding: 16px 25px; border-bottom: 1px solid #e9e9e9"
						>
							<RouterLink
								to="/account/inquiry-list"
								style="
									width: 200px;
									font-size: 16px;
									font-family: 'Spoqa Han Sans Neo', 'sans-serif';
								"
							>
								1:1 문의 내역
							</RouterLink>
						</AMenu-item>
						<AMenu-item
							key="3"
							style="
								padding: 16px 10px;
								border: none;
								border-radius: 0 0 3px 3px;
							"
						>
							<AButton
								@click="showConfirm"
								style="
									width: 200px;
									border: none;
									background-color: transparent;
									font-size: 16px;
									text-align: left;
									cursor: pointer;
									font-family: 'Spoqa Han Sans Neo', 'sans-serif';
								"
							>
								로그아웃
							</AButton>
						</AMenu-item>
					</AMenu>
					<AIcon slot="icon" type="user" />
				</ADropdown-button>
			</div>
		</template>
	</nav>
</template>

<script>
import { mapGetters } from 'vuex';
import { clearAllCookies } from '@/utils/cookies';

export default {
	computed: {
		...mapGetters('userStore', ['getNickname', 'isLogin']),
	},
	methods: {
		clickLogout() {
			this.$store.dispatch('userStore/LOGOUT');
			clearAllCookies();
			this.$router.history.current.fullPath === '/'
				? this.$router.go()
				: this.$router.push('/');
		},
		showConfirm() {
			const headerNavigation = this;

			this.$confirm({
				title: () => (
					<h1 style="font-size: 18px; font-weight: 200; font-family: 'Spoqa Han Sans Neo', 'sans-serif'; color: #000;">
						로그아웃 하시겠습니까?
					</h1>
				),
				okText: '확인',
				cancelText: '취소',
				onOk() {
					headerNavigation.userLogout();
				},
				onCancel() {},
				class: 'test',
			});
		},
		userLogout() {
			this.$store.dispatch('userStore/LOGOUT');
			clearAllCookies();
			this.$router.history.current.fullPath === '/'
				? this.$router.go()
				: this.$router.push('/');
		},
	},
};
</script>

<style lang="scss" scoped>
@import './scss/headerNavigation.scss';
</style>
