<template>
  <article class="post">
    <header class="post__header">
      <router-link
        :to="{ name: 'profile', params: { username: post.user.username } }"
        class="post__user"
      >
        <div class="post__profile">
          <ProfilePhoto :gravatar="post.user.gravatar" />
        </div>
        <div class="post__username">
          {{ post.user.username }}
        </div>
      </router-link>
      <div class="post__created-date">
        {{ createdDate }}
      </div>
    </header>
    <div class="post__image">
      <template v-if="imageTo">
        <router-link :to="imageTo">
          <PostImage
            :caption="post.caption"
            :image-url="post.imageUrl"
          />
        </router-link>
      </template>
      <template v-else>
        <PostImage
          :caption="post.caption"
          :image-url="post.imageUrl"
        />
      </template>
      <transition name="fade">
        <div
          v-if="isSharing"
          class="post__share"
        >
          <div
            ref="postPath"
            class="post__path"
          >
            {{ postPath }}
          </div>
        </div>
      </transition>
    </div>
    <footer class="post__footer">
      <div class="post__info">
        <div class="post__likes">
          {{ post.likes.length }} like{{ post.likes.length !== 1 ? 's' : '' }}
        </div>
        <div class="post__caption">
          {{ post.caption }}
        </div>
      </div>
      <div
        v-if="$store.state.auth.isAuthenticated"
        class="post__buttons"
      >
        <BaseButton
          :class="['post__share-button', { 'post__share-button--active': isSharing }]"
          @click="onShareClick"
        >
          <BaseIcon name="share" />
        </BaseButton>
        <BaseButton
          :class="['post__like-button', { 'post__like-button--liked': post.liked }]"
          @click="onLikeClick"
        >
          <BaseIcon name="heart" />
        </BaseButton>
      </div>
    </footer>
  </article>
</template>

<script>
import BaseButton from "./BaseButton";
import BaseIcon from "./BaseIcon";
import BaseInput from "./BaseInput";
import PostImage from "./PostImage";
import ProfilePhoto from "./ProfilePhoto";

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput,
    PostImage,
    ProfilePhoto
  },
  props: {
    imageTo: {
      default: null,
      type: Object
    },
    post: {
      required: true,
      type: Object
    }
  },
  data() {
    return {
      isSharing: false
    };
  },
  computed: {
    createdDate() {
      return this.post.createdAt.toLocaleDateString();
    },
    postPath() {
      return `${window.location.origin}/post/${this.post.id}`;
    }
  },
  methods: {
    onLikeClick() {
      this.$store.dispatch("toggleLike", this.post.id);
    },
    onShareClick() {
      if (this.isSharing) {
        this.isSharing = false;
      } else {
        this.isSharing = true;
        this.$nextTick(() => {
          const { postPath } = this.$refs;

          if (document.selection) {
            const range = document.body.createTextRange();
            range.moveToElementText(postPath);
            range.select();
          } else if (window.getSelection) {
            const range = document.createRange();
            range.selectNode(postPath);
            window.getSelection().removeAllRanges();
            window.getSelection().addRange(range);
          }
        });
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.post {
  &__header {
    align-items: center;
    display: flex;
    justify-content: space-between;
  }

  &__user {
    align-items: center;
    color: var(--color-purple);
    display: flex;
    font-weight: bold;
    text-decoration: none;
  }

  &__profile {
    margin-right: 0.5rem;
  }

  &__created-date {
    color: var(--color-gray);
  }

  &__image {
    margin-top: 1rem;
    position: relative;
  }

  &__footer {
    align-items: flex-start;
    display: flex;
    margin-top: 1rem;
  }

  &__info {
    flex: 1 1 auto;
    margin-right: 1rem;
  }

  &__likes {
    font-weight: bold;
    margin-bottom: 0.25rem;
  }

  &__caption {
    color: var(--color-gray);
  }

  &__buttons {
    display: flex;
    flex: 0 0 auto;

    & > *:not(:last-child) {
      margin-right: 1rem;
    }
  }

  &__share-button {
    color: var(--color-purple);

    &--active {
      color: var(--color-pink);
    }
  }

  &__like-button {
    color: var(--color-purple);

    &--liked {
      color: var(--color-pink);

      & /deep/ svg {
        fill: var(--color-pink);
      }
    }
  }

  &__share {
    align-items: center;
    background-color: rgba(255, 255, 255, 0.8);
    bottom: 0;
    display: flex;
    left: 0;
    padding: 1rem;
    position: absolute;
    right: 0;
    top: 0;
  }

  &__path {
    background-color: #fff;
    border-radius: 0.5rem;
    padding: 0.5rem;
    text-align: center;
    user-select: text;
    word-wrap: break-word;
    width: 100%;
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.1s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
