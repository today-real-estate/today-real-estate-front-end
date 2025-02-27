<template>
	<div class="signup-form">
		<h1 class="signup-form__title">회원정보 입력</h1>
		<h2 class="signup-form__desc">
			오늘의 부동산 서비스 이용을 위해 정보를 입력해주세요.
		</h2>
		<div class="signup-form__input">
			<label for="signup-user-email-input">아이디</label>
			<div class="signup-form__input__main">
				<input
					type="text"
					id="signup-user-email-input"
					placeholder="이메일 주소 입력"
					v-model="userEmail"
				/>
				<button class="user-email-auth-btn" @click="checkEmail">
					중복 확인
				</button>
			</div>
		</div>
		<div class="signup-form__input">
			<label for="signup-user-name-input">이름</label>
			<input
				type="text"
				id="signup-user-name-input"
				placeholder="이름 입력"
				v-model="userName"
			/>
		</div>
		<div class="signup-form__input">
			<label for="signup-nickname-input">닉네임</label>
			<div class="signup-form__input__main">
				<input
					type="text"
					id="signup-nickname-input"
					placeholder="한글 또는 영문만 가능"
					v-model="nickname"
				/>
			</div>
		</div>
		<div class="signup-form__input">
			<label for="signup-password-input">비밀번호</label>
			<input
				type="password"
				id="signup-password-input"
				placeholder="8자리 이상 영문, 숫자, 특수문자 포함"
				v-model="password"
			/>
			<input
				type="password"
				id="signup-password-input--check"
				placeholder="비밀번호 확인"
				v-model="passwordConfirm"
			/>
			<span v-if="comparePassword" class="warning">
				비밀번호가 일치하지 않습니다.
			</span>
		</div>
		<button
			type="submit"
			class="signup-form__submit-btn"
			@click.prevent="submitSignupForm"
		>
			확인
		</button>
	</div>
</template>

<script>
import { registerUser, checkDuplicate } from '@/api/auth';
import Swal from 'sweetalert2';

export default {
	data() {
		return {
			userEmail: '',
			userName: '',
			nickname: '',
			password: '',
			passwordConfirm: '',
			checkDuplicateStatus: false,
		};
	},
	computed: {
		comparePassword() {
			return (
				this.passwordConfirm !== '' && this.password !== this.passwordConfirm
			);
		},
	},
	methods: {
		async submitSignupForm() {
			console.log('click');
			if (
				this.userEmail === '' ||
				this.userName === '' ||
				this.nickname === '' ||
				this.password === '' ||
				this.passwordConfirm === ''
			) {
				Swal.fire({
					position: 'center',
					icon: 'warning',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">회원가입 폼을 모두 입력하세요.<div>`,
					showConfirmButton: false,
					timer: 1500,
				});

				return;
			} else if (!this.checkDuplicateStatus) {
				Swal.fire({
					position: 'center',
					icon: 'warning',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">이메일 중복 확인을 진행하세요.<div>`,
					showConfirmButton: false,
					timer: 1500,
				});

				return;
			} else if (this.password !== this.passwordConfirm) {
				Swal.fire({
					position: 'center',
					icon: 'warning',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">비밀번호가 일치하지 않습니다.<div>`,
					showConfirmButton: false,
					timer: 1500,
				});

				return;
			}

			try {
				const signupUserData = {
					userEmail: this.userEmail,
					userName: this.userName,
					nickname: this.nickname,
					password: this.password,
				};

				await registerUser(signupUserData);
				Swal.fire({
					position: 'center',
					icon: 'success',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">회원가입 완료<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
				this.$router.push('/login');
			} catch (error) {
				const errorMessage = error.data;

				Swal.fire({
					position: 'center',
					icon: 'error',
					width: 350,
					title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">${errorMessage}<div>`,
					showConfirmButton: false,
					timer: 1500,
				});
			} finally {
				this.initForm();
			}
		},
		async checkEmail() {
			const emailData = {
				userEmail: this.userEmail,
			};

			try {
				const { data } = await checkDuplicate(emailData);
				this.checkDuplicateStatus = true;

				if (data) {
					Swal.fire({
						position: 'center',
						icon: 'success',
						width: 350,
						title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">사용 가능한 이메일입니다.<div>`,
						showConfirmButton: false,
						timer: 1500,
					});
				} else {
					Swal.fire({
						position: 'center',
						icon: 'error',
						width: 350,
						title: `<div style="font-size: 18px; font-family: "Spoqa Han Sans Neo", "sans-serif"; ">이미 등록된 이메일입니다.<div>`,
						showConfirmButton: false,
						timer: 1500,
					});

					this.userEmail = '';
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
		initForm() {
			this.userEmail = '';
			this.userName = '';
			this.nickname = '';
			this.password = '';
			this.passwordConfirm = '';
		},
	},
};
</script>

<style lang="scss" scoped>
@import './scss/signupForm.scss';
</style>
