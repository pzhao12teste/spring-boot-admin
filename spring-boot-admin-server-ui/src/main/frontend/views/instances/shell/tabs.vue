<!--
  - Copyright 2014-2018 the original author or authors.
  -
  - Licensed under the Apache License, Version 2.0 (the "License");
  - you may not use this file except in compliance with the License.
  - You may obtain a copy of the License at
  -
  -     http://www.apache.org/licenses/LICENSE-2.0
  -
  - Unless required by applicable law or agreed to in writing, software
  - distributed under the License is distributed on an "AS IS" BASIS,
  - WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  - See the License for the specific language governing permissions and
  - limitations under the License.
  -->

<template>
    <section class="hero is-primary instance-tabs">
        <div class="hero-foot">
            <div class="container">
                <div class="level">
                    <div class="level-left">
                        <div class="instance-tabs__name"
                             :class="{ 'is-active' : isStuck }">
                            <h1 class="title is-5" v-if="instance" v-text="instance.registration.name"></h1>
                            <h1 class="subtitle is-6" v-if="instance" v-text="instance.id"></h1>
                        </div>
                    </div>
                    <div class="level-right">
                        <nav class="tabs is-boxed is-right">
                            <ul>
                                <li v-if="instance" v-for="view in activeViews"
                                    :class="{'is-active' : $route.name === view.name}">
                                    <a v-if="view.href" :href="view.href({ 'instanceId' : instance.id })"
                                       target="_blank">
                                        <component :is="view.handle"></component>
                                    </a>
                                    <router-link v-else
                                                 :to="{ name: view.name, params: { 'instanceId' : instance.id } }">
                                        <component :is="view.handle"></component>
                                    </router-link>
                                </li>
                            </ul>
                        </nav>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
  export default {
    props: ['instance', 'views'],
    data: () => ({
      isStuck: false
    }),
    computed: {
      activeViews() {
        if (!this.instance || !this.views) {
          return [];
        }

        return this.views.filter(
          view => typeof view.isActive === 'undefined' || view.isActive({instance: this.instance})
        );
      }
    },
    methods: {
      onScroll() {
        this.isStuck = this.$el.getBoundingClientRect().top <= 52;
      }
    },
    mounted() {
      window.addEventListener('scroll', this.onScroll);
    },
    beforeDestroy() {
      window.removeEventListener('scroll', this.onScroll);
    },
  }
</script>

<style lang="scss">
    @import "~@/assets/css/utilities";

    .instance-tabs {
        z-index: 29;
        position: sticky;
        top: $navbar-height-px;
        overflow: hidden;

        &__name {
            transition: all $easing $speed;
            will-change: transform;
            transform: translateY(41px);
            visibility: hidden;

            &.is-active {
                transform: translateY(0px);
                visibility: visible;
            }
        }
    }
</style>
