<template>
	<div
		v-if="content"
		class="section-sticky-gallery-des bg-firm-dark py-section"
	>

		<container class="flex">

			<div class="relative pr-5">
				<div
					class="section-sticky-gallery-des__content text-white"
					ref="servicesStickyContent"
				>

					<el-caption
						:caption="content.title"
						:descriptor="content.descriptor"
						h2
						class="mb-9"
					/>

					<div
						v-if="content.items && content.items.length > 0"
						class="section-sticky-gallery-des__content-bottom"
					>

						<el-gallery-counter
							v-if="stickyContent && content.items.length > 1"
							:count="currentSlide"
							:totalCount="content.items.length"
							class="mb-4"
						/>

						<section-text
							v-if="stickyContent"
							:html="content.items[currentSlide].content"
							class="section-sticky-gallery-des__content-text pr-24"
						/>

						<section-text
							v-else
							v-for="textItem in content.items"
							:html="textItem.content"
							class="section-sticky-gallery-des__content-text pr-24 mt-5 first:mt-0"
						/>

					</div>

				</div>
			</div>

			<div
				v-if="content.items?.length > 0"
				class="section-sticky-gallery-des__images"
			>

				<div
					class="sticky top-[--headerVisibleHeight] top-0 -mt-5 pt-5"
				>

					<div
						v-for="(imageItem,imageItem_i) in content.items"
						:key="imageItem_i"
						class="section-sticky-gallery-des__image-wrapper"
						ref="servicesGalleryImages"
					>
						<c-image
							v-if="imageItem.image"
							class="section-sticky-gallery-des__image ratio rounded-firm overflow-hidden bg-firm-gray"
							:source="{
								...imageItem.image,
								media: [ '438x626c', '438x626c', '438x626c', '438x626c' ]
							}"
						/>
					</div>

				</div>

			</div>

			<div
				v-if="content.items?.length > 1 && stickyContent"
				class="relative flex-shrink-0"
			>
				<div class="section-sticky-gallery-des__dots">

					<div
						v-for="(dot,dot_i) in content.items"
						:key="dot_i"
						class="section-sticky-gallery-des__dots-item bg-white"
						:class="{
							'section-sticky-gallery-des__dots-item_active': dot_i === currentSlide
						}"
					></div>

				</div>
			</div>

		</container>

	</div>
</template>

<script>

export default {
	props: ['content'],
	data: () => ({
		stickyContent: true,
		currentSlide: 0
	}),
	methods: {
		getCurrentSlide() {

			const images = this.$refs.servicesGalleryImages;

			if (!images) return;

			images.forEach((image,image_i) => {

				if (image.getBoundingClientRect().top < document.documentElement.clientHeight / 2) {
					this.currentSlide = image_i;
				}

			});

		},
		getStickyContentHeight() {

			if (!this.$refs.servicesStickyContent) return;

			if (this.$refs.servicesStickyContent.offsetHeight < (document.documentElement.clientHeight * 4/5)) {

				document.documentElement.style.setProperty('--stickyContentHeight', this.$refs.servicesStickyContent.offsetHeight + 'px');

			} else {

				this.stickyContent = false;

			}

		}
	},
	mounted () {

		this.getStickyContentHeight();

		if (this.stickyContent === true) {

			this.getCurrentSlide();

			window.addEventListener('scroll', this.getCurrentSlide, {passive: true});

		}

	},
	destroyed () {

		if (this.stickyContent === true) {

			window.removeEventListener('scroll', this.getCurrentSlide, {passive: true});

		}

	}
}

</script>

<style lang="scss">

	.section-sticky-gallery-des{
		&__content{
			@apply sticky overflow-hidden flex flex-col;
			top: 1.5rem;
			top: calc((100vh - var(--stickyContentHeight)) / 2 + var(--headerVisibleHeight) / 2);
			&-text{
				@apply font-light;
				*:last-child{
					margin-bottom: 0;
				}
			}
		}
		&__images{
			flex: 438px 0 0;
			margin-left: auto;
			margin-right: 2rem;
			@screen xl {
				margin-right: 5.6rem;
			}
		}
		&__image{
			&:before{
				padding-top: 143%;
			}
			&-wrapper{
				position: relative;
				& + &{
					margin-top: 1.25rem;
				}
			}
		}
		&__dots{
			position: sticky;
			top: var(--headerVisibleHeight);
			height: calc(100vh - var(--headerVisibleHeight));
			display: flex;
			flex-direction: column;
			justify-content: center;
			&-item{
				width: 6px;
				height: 6px;
				border-radius: 100px;
				opacity: .1;
				& + &{
					margin-top: 9px;
				}
				&_active{
					opacity: 1;
					height: 10px;
				}
			}
		}
	}

</style>
