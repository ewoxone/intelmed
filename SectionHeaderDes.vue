<template>
	<div
		:style="{
			...(headerWrapperHeight !== null && {
				height: headerWrapperHeight
			})
		}"
		ref="headerWrapper"
		v-if="header"
	>
		<header
			ref="header"
			class="header-des"
			:class="{
				'header-des_fixed': headerIsFixed
			}"
		>
			<container class="flex items-center justify-between">

				<a
					v-if="header.logo?.link?.url"
					:href="header.logo.link.url"
					:data-dlink-type="header.logo.link.type"
					data-dlink
					class="header-des__logo flex-shrink-0"
				>
					<img src="~/assets/images/logo.svg" width="72" height="39">
				</a>

				<div
					v-if="menu?.length"
					class="header-des__menu flex mr-auto"
				>

					<div
						v-for="(menuItem,menuItem_i) in menu"
						:key="menuItem_i"
						class="header-des__menu-item"
					>
						
						<a
							v-if="menuItem.category?.url"
							:href="menuItem.category.url"
							:data-dlink-type="menuItem.category.type"
							data-dlink
							class="hover:text-black transition-colors"
							:class="{
								'text-black': menuItem.active
							}"
						>{{menuItem.category.title}}</a>

						<div
							v-else-if="menuItem?.category?.title"
							class="hover:text-black transition-colors"
							:class="{
								'text-black': menuItem.active
							}"
						>{{menuItem.category.title}}</div>

						<div
							v-if="menuItem.items?.length"
							class="header-des__menu-sub"
						>
							<div class="header-des__menu-sub-inner">

								<template
									v-for="(subItem,subItem_i) in menuItem.items"
								>
									<a
										v-if="subItem.link?.url"
										:href="subItem.link.url"
										:data-dlink-type="subItem.link.type"
										data-dlink
										:key="subItem_i"
										class="header-des__menu-sub-item hover:text-black transition-colors"
										:class="{
											'text-black': subItem.active
										}"
									>{{subItem.link.title}}</a>
								</template>

							</div>
						</div>

					</div>

				</div>

				<a
					:href="`tel:${header.phone}`"
					class="ml-4 hover:text-black transition-colors"
					target="_blank"
				>{{ useFormatedPhone(header.phone) }}</a>

				<el-search
					class="ml-6"
				/>

				<el-switch-lang
					:switcher="header.lang_switcher"
					class="ml-8"
				/>

			</container>

		</header>
	</div>
</template>

<script>

export default {
	props: ['header','menu'],
	data: () => ({
		headerIsFixed: false
	}),
	computed: {
		fixedPoint() {
			return this.$refs.headerWrapper ? this.$refs.headerWrapper.offsetHeight : 0;
		},
		headerWrapperHeight () {
			if (this.headerIsFixed) {
				return this.$refs.headerWrapper.offsetHeight + 'px';
			} else {
				return null;
			}
		}
	},
	methods: {
		fixHeader() {
			if (window.scrollY > this.fixedPoint) {
				this.headerIsFixed = true;
			} else {
				this.headerIsFixed = false;
			}
			this.getHeaderVisibleHeight();
		},
		getHeaderVisibleHeight() {
			if (!this.$refs.header) return;
			document.documentElement.style.setProperty('--headerVisibleHeight', this.$refs.header.getBoundingClientRect().bottom + 'px');
		}
	},
	mounted () {
		window.addEventListener('scroll', this.fixHeader, {passive: true});
		this.fixHeader();
	},
	destroyed () {
		window.removeEventListener('scroll', this.fixHeader, {passive: true});
	},
	setup () {
		const { name } = usePage();
		return {
			pageName: name
		}
	}
}
</script>

<style lang="scss">

	.header-des {

		box-shadow: 0px 1px 0px 0px rgba(227, 227, 227, 1);
		@apply py-5 relative z-[100] text-[#808080];

		&_fixed{
			@apply fixed left-0 right-0 top-0 z-[100] bg-white;
		}

		&_fixed &__logo{
			width: 38px;
			height: 21px;
			transform: none;
		}
		&__logo{
			width: 72px;
			height: 39px;
			margin-right: 5.3rem;
			position: relative;
			transform: translate(0,4px);
			transition-duration: 0s;
		}
		&__menu{
			&-item{
				@apply select-none relative mr-8;
			}
			&-item:not(:hover) &-sub{
				opacity: 0;
				visibility: hidden;
			}
			&-sub{
				position: absolute;
				top: 100%;
				left: -20px;
				width: max-content;
				max-width: 258px;
				padding-top: 20px;
				transition-duration: .2s;
				&-inner{
					line-height: 1.2;
					font-size: 13px;
					background: #FFFFFF;
					box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.1);
					border-radius: 10px;
					padding: 14px 20px;
				}
				&-item{
					display: block;
					padding: .4em 0;
				}
			}
		}
	}

</style>