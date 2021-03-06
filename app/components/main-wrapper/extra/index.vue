<template>
    <section id="extra" :class="{ showing: prefs.showExtraPanel }">
        <div class="tabs">
            <div class="header clear">
                <a @click.prevent="currentView = 'lyrics'"
                    :class="{ active: currentView === 'lyrics' }">Lyrics</a>
                <a @click.prevent="currentView = 'artistInfo'"
                    :class="{ active: currentView === 'artistInfo' }">Artist</a>
                <a @click.prevent="currentView = 'albumInfo'"
                    :class="{ active: currentView === 'albumInfo' }">Album</a>
            </div>

            <div class="panes">
                <lyrics :song="song" v-ref:lyrics v-show="currentView === 'lyrics'"></lyrics>
                <artist-info :artist="song.album.artist" v-ref:artist-info v-show="currentView === 'artistInfo'"></artist-info>
                <album-info :album="song.album" v-ref:album-info v-show="currentView === 'albumInfo'"></album-info>
            </div>
        </div>
    </section>
</template>

<script>
    import _ from 'lodash';
    import $ from 'jquery';

    import lyrics from './lyrics.vue';
    import artistInfo from './artist-info.vue';
    import albumInfo from './album-info.vue';
    import preferenceStore from '../../../stores/preference';
    import songStore from '../../../stores/song';

    export default {
        components: { lyrics, artistInfo, albumInfo },

        data() {
            return {
                song: songStore.stub,
                prefs: preferenceStore.state,
                currentView: 'lyrics',
            };
        },

        watch: {
            /**
             * Watch the "showExtraPanel" property to add/remove the corresponding class
             * to/from the html tag.
             * Some element's CSS can then be controlled based on this class.
             */
            'prefs.showExtraPanel': function (newVal) {
                if (newVal) {
                    $('html').addClass('with-extra-panel');
                } else {
                    $('html').removeClass('with-extra-panel');
                }
            },
        },

        ready() {
            // On ready, add 'with-extra-panel' class.
            $('html').addClass('with-extra-panel');
        },

        methods: {
            /**
             * Reset all self and applicable child components' states.
             */
            resetState() {
                this.currentView = 'lyrics';
                this.song = songStore.stub;
                _.invoke(this.$refs, 'resetState');
            },
        },

        events: {
            'song:played': function (song) {
                songStore.getInfo(this.song = song);
            },

            'koel:teardown': function () {
                this.resetState();
            },
        },
    };
</script>

<style lang="sass">
    @import "../../../sass/partials/_vars.scss";
    @import "../../../sass/partials/_mixins.scss";

    #extra {
        flex: 0 0 $extraPanelWidth;
        padding: 24px 16px $footerHeight;
        background: $colorExtraBgr;
        max-height: calc(100vh - #{$headerHeight + $footerHeight});
        display: none;
        color: $color2ndText;
        overflow: auto;

        &.showing {
            display: block;
        }

        h1 {
            font-weight: $fontWeight_UltraThin;
            font-size: 28px;
            margin-bottom: 16px;
            line-height: 36px;

            @include vertical-center();
            align-items: initial;

            span {
                flex: 1;
                margin-right: 12px;
            }

            a {
                font-size: 14px;

                &:hover {
                    color: $colorHighlight;
                }
            }
        }

        .more {
            margin-top: 8px;
            border-radius: 3px;
            background: $colorBlue;
            color: #fff;
            padding: 4px 8px;
            display: inline-block;
            text-transform: uppercase;
            font-size: 80%;
        }

        footer {
            margin-top: 24px;
            font-size: 90%;

            a {
                color: #fff;
                font-weight: $fontWeight_Normal;

                &:hover {
                    color: #b90000;
                }
            }
        }
    }
</style>
