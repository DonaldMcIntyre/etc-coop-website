<template>
	<section class="newsletterSection">
		<div class="mainContainer newsletterContainer">
			<div class="row justify-content-between">
				<div class="col-lg-5 col-md-6 col-sm-12">
					<h2>{{ $t("homepage.sign_up_title") }}</h2>
					<p>{{ $t("homepage.sign_up_description") }}</p>
				</div>
				<div class="col-lg-5 col-md-6 col-sm-12 ps-lg-5">
					<div class="inputGroup">
						<input
							ref="email"
							type="email"
							data-inputmask=""
							name="fields[email]"
							autocomplete="email"
							placeholder="Enter your email"
						/>
						<button
							type="submit"
							@click="handleSubscribeNewsletter"
							:disabled="loading"
							:style="{ opacity: loading ? '0.8' : '1' }"
						>
							<template v-if="loading">
								{{ $t("homepage.sign_up_sending") }}
							</template>
							<template v-else>
								{{ $t("homepage.sign_up_subscribe") }}
							</template>
						</button>
					</div>
					<div v-if="showMessage">
						<div v-if="showMessageStatus" class="newsletter-success-message">
							{{ showMessageText }}
						</div>
						<div v-else class="newsletter-error-message">
							{{ showMessageText }}
						</div>
					</div>
					<span>
						{{ $t("homepage.sign_up_privacy_title") }}
						<a href="">{{ $t("homepage.sign_up_privacy_link_title") }}</a>
					</span>
				</div>
			</div>
		</div>
	</section>
</template>

<script>
export default {
	data() {
		return {
			showMessage: false,
			showMessageStatus: false,
			showMessageText: "",
			loading: false,
		};
	},
	watch: {
		showMessageStatus: function () {
			setTimeout(() => {
				this.showMessage = false;
				this.$refs.email.value = "";
			}, 3000);
		},
	},
	methods: {
		handleSubscribeNewsletter() {
			this.loading = true;
			const url = "https://static.mailerlite.com/webforms/submit/s3h0g4";

			const email = this.$refs.email.value;
			const timestamp = +new Date();
			const callback = `jQuery_${timestamp}`;

			fetch(
				`${url}?` +
					new URLSearchParams({
						callback: callback,
						"fields[email]": email,
						"gdpr[]": "Email",
						"gdpr[]": "Customized online advertising",
						"groups[]": 111979553,
						"ml-submit": 1,
						anticsrf: true,
						ajax: 1,
						guid: "f81786e5-a4fe-dfc6-2ef6-ffa1328292f2",
						_: timestamp,
					})
			)
				.then((response) => response.text())
				.then((response) => {
					this.loading = false;
					const data = JSON.parse(
						response
							.replaceAll(callback, "")
							.replaceAll("(", "")
							.replaceAll(")", "")
					);

					if (data.success === true) {
						this.showMessageText = this.$t("homepage.sign_up_success");
						this.showMessageStatus = true;
					} else if (data.success === false) {
						this.showMessageText = this.$t("homepage.sign_up_error");
						this.showMessageStatus = false;
					}

					this.showMessage = true;
				});
		},
	},
};
</script>
