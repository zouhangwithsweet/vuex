<template>
    <transition name="slide">
        <music-list :rank="rank" :title="title" :bg-image="bgImage" :songs="songs"></music-list>
    </transition>
</template>

<script>
    import musicList from 'components/music-list/music-list'
    import {mapGetters} from 'vuex'
    import {getMusicList} from 'api/rank'
    import {ERR_OK} from 'api/config'
    import {createSong} from 'common/js/song'
    export default {
        data() {
            return {
                songs: [],
                rank: true
            }
        },
        computed: {
            title() {
                return this.topList.topTitle
            },
            bgImage() {
                if (this.songs.length) {
                    return this.songs[0].image
                }
                return ''
            },
            ...mapGetters([
                'topList'
            ])
        },
        created() {
            this._getTopList()
        },
        mounted() {
            if(!this.topList.id) {
                this.$router.push('/rank')
            }
            return
        },
        methods: {
            _getTopList() {
                getMusicList(this.topList.id).then((res) => {
                    if (res.code === ERR_OK) {
                        this.songs = this._normalizeSongs(res.songlist)
                    }
                })
            },
            _normalizeSongs(list) {
                let ret = []
                list.forEach((item) => {
                    const musicData = item.data
                    if (musicData.songid && musicData.albummid) {
                        ret.push(createSong(musicData))
                    }
                })

                return ret
            }
        },
        components: {
            musicList
        }
    }
</script>

<style lang="stylus" scoped>
    .slide-enter-active, .slide-leave-active
        transition all .3s ease
    .slide-enter, .slide-leave-to
        transition translate3d(100%, 0, 0)
</style>

