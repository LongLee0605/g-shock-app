<template>
	<Page>
    <ActionBar title="Trang Đăng Nhập" verticalAlignment="center"/>
      <NavigationButton
        text="Go back"
        android.systemIcon="ic_menu_back"
        @tap="$navigateBack"
        />
		<FlexboxLayout class="page">
			<StackLayout class="form">
				<Image class="logo" src="~/images/logo.png" />
				<Label class="header" text="Đăng Nhập" />

				<StackLayout class="input-field" marginBottom="25">
					<TextField class="input" hint="Email" keyboardType="email" autocorrect="false" autocapitalizationType="none" v-model="user.email"
					 returnKeyType="next" @returnPress="focusPassword" fontSize="18" />
					<StackLayout class="hr-light" />
				</StackLayout>

				<StackLayout class="input-field" marginBottom="25">
					<TextField ref="password" class="input" hint="Password" secure="true" v-model="user.password" :returnKeyType="isLoggingIn ? 'done' : 'next'"
					 @returnPress="focusConfirmPassword" fontSize="18" />
					<StackLayout class="hr-light" />
				</StackLayout>

				<StackLayout v-show="!isLoggingIn" class="input-field">
					<TextField ref="confirmPassword" class="input" hint="Confirm password" secure="true" v-model="user.confirmPassword" returnKeyType="done"
					 fontSize="18" />
					<StackLayout class="hr-light" />
				</StackLayout>

				<Button :text="isLoggingIn ? 'Đăng nhập' : 'Đăng ký'" @tap="submit" class="btn btn-primary m-t-20" />
				<Label v-show="isLoggingIn" text="Quên mật khẩu?" class="login-label" @tap="forgotPassword" />
			</StackLayout>

			<Label class="login-label sign-up-label" @tap="toggleForm">
	          <FormattedString>
	            <Span :text="isLoggingIn ? 'Không có tài khoản?' : 'Trở về trang đăng nhập'" />
	            <Span :text="isLoggingIn ? 'Đăng kí' : ''" class="bold" />
	          </FormattedString>
	        </Label>
		</FlexboxLayout>
	</Page>
</template>
<script>
import App from './App';
// A stub for a service that authenticates users.
const userService = {
  register(user) {
    return Promise.resolve(user);
  },
  login(user) {
    return Promise.resolve(user);
  },
  resetPassword(email) {
    return Promise.resolve(email);
  }
};
// A stub for the main page of your app. In a real app you’d put this page in its own .vue file.

export default {
  data() {
    return {
      isLoggingIn: true,
      user: {
        email: "",
        password: "",
        confirmPassword: ""
      }
    };
  },
  methods: {
    toggleForm() {
      this.isLoggingIn = !this.isLoggingIn;
    },
    submit() {
      if (!this.user.email || !this.user.password) {
        this.alert("Vui lòng cung cấp cả địa chỉ email và mật khẩu.");
        return;
      }
      if (this.isLoggingIn) {
        this.login();
      } else {
        this.register();
      }
    },
    login() {
      userService
        .login(this.user, this.password)
        .then(() => {
          this.$navigateTo(App);
        })
        .catch(() => {
          this.alert("Rất tiếc, chúng tôi không thể tìm thấy tài khoản của bạn.");
        });
    },
    register() {
      if (this.user.password != this.user.confirmPassword) {
        this.alert("Mật khẩu của bạn không phù hợp.");
        return;
      }
      userService
        .register(this.user)
        .then(() => {
          this.alert("Tài khoản của bạn đã được tạo thành công.");
          this.isLoggingIn = true;
        })
        .catch(() => {
          this.alert("Rất tiếc, chúng tôi không thể tạo tài khoản của bạn.");
        });
    },
    forgotPassword() {
      prompt({
        title: "Quên mật khẩu",
        message:
          "Nhập địa chỉ email bạn đã sử dụng để đăng ký Shop để đặt lại mật khẩu của bạn.",
        inputType: "email",
        defaultText: "",
        okButtonText: "Ok",
        cancelButtonText: "Cancel"
      }).then(data => {
        if (data.result) {
          userService
            .resetPassword(data.text.trim())
            .then(() => {
              this.alert(
                "Mật khẩu của bạn đã được đặt lại thành công. Vui lòng kiểm tra email của bạn để biết hướng dẫn về cách chọn mật khẩu mới."
              );
            })
            .catch(() => {
              this.alert(
                "Rất tiếc, đã xảy ra lỗi khi đặt lại mật khẩu của bạn."
              );
            });
        }
      });
    },
    focusPassword() {
      this.$refs.password.nativeView.focus();
    },
    focusConfirmPassword() {
      if (!this.isLoggingIn) {
        this.$refs.confirmPassword.nativeView.focus();
      }
    },
    alert(message) {
      return alert({
        title: "Shop",
        okButtonText: "OK",
        message: message
      });
    }
  }
};
</script>
	
<style scoped>
	.page {
		align-items: center;
		flex-direction: column;
	}

	.form {
		margin-left: 30;
		margin-right: 30;
		flex-grow: 2;
		vertical-align: middle;
	}

	.logo {
		margin-bottom: 12;
		height: 90;
		font-weight: bold;
	}

	.header {
		/* horizontal-align: center; */
		font-size: 25;
		font-weight: 600;
		margin-bottom: 70;
		text-align: center;
		color: #D51A1A;
	}

	.input-field {
		margin-bottom: 25;
	}

	.input {
		font-size: 18;
		/* placeholder-color: #A8A8A8; */
	}

	.input-field .input {
		font-size: 54;
	}

	.btn-primary {
		height: 50;
		margin: 30 5 15 5;
		background-color: #D51A1A;
		border-radius: 5;
		font-size: 20;
		font-weight: 600;
	}

	.login-label {
		/* horizontal-align: center; */
		color: #A8A8A8;
		font-size: 16;
	}

	.sign-up-label {
		margin-bottom: 20;
	}

	.bold {
		color: #000000;
	}
</style>