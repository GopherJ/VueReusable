<template>
    <div ref="returnTopBtn" class="return-top" @click.stop="returnTop">
        <i class="arrow up"></i>
    </div>
</template>

<script>
    export default {
        name: 'return-top',
        props: {
            threshold: {
                type: Number,
                default: 1 / 3
            },
            duration: {
                type: Number,
                default: 100
            }
        },
        methods: {
            returnTop() {
                const returnTopBtn = this.$refs.returnTopBtn,
                    docElem = document.documentElement,
                    // standard 60 fps
                    renderInterval = 1000 / 60;

                if (docElem.scrollTop === 0) {
                    return;
                }

                const step = docElem.scrollTop / this.duration * renderInterval;

                const callback = function () {
                    docElem.scrollTop -= step;

                    if (docElem.scrollTop !== 0) {
                        window.requestAnimationFrame(callback);
                    }
                };

                window.requestAnimationFrame(callback);
            }
        },
        mounted() {
            const returnTopBtn = this.$refs.returnTopBtn,
                docElem = document.documentElement || document.body,
                self = this;

            this.listener = function () {
                // percentage
                if (docElem.scrollTop / (docElem.scrollHeight - docElem.clientHeight) > self.threshold) {
                    if (!returnTopBtn.classList.contains('show')) {
                        returnTopBtn.classList.add('show');
                    }
                }

                else {
                    if (returnTopBtn.classList.contains('show')) {
                        returnTopBtn.classList.remove('show');
                    }
                }
            };

            window.addEventListener('scroll', this.listener);
        },
        beforeDestroy() {
            window.removeEventListener('scroll', this.listener);
        }
    }
</script>

<style>
    @keyframes show {
        from {
            opacity: 0;
            transform: translateY(5px);
        }

        to {
            opacity: 1;
            transform: translateY(-10px);
        }
    }

    .return-top {
        position: fixed;
        right: 5px;
        bottom: 8rem;

        display: flex;
        display: -webkit-flex;
        justify-content: center;
        align-items: center;

        width: 60px;
        height: 60px;

        border-radius: 30px;
        opacity: 0;

        background-color: #CDE201;

        z-index: 99999999
    }

    .return-top:hover {
        cursor: pointer;
        box-shadow: 0 2px 10px rgba(0, 0, 0, .2),
                    0 4px 40px rgba(0, 0, 0, .3);
    }

    .return-top.show {
        animation-name: show;
        animation-duration: 300ms;
        animation-fill-mode: forwards;
        animation-timing-function: cubic-bezier(0.215, 0.610, 0.355, 1.000);
    }

    .arrow {
        border: solid rgb(255, 255, 255);
        border-width: 0 6px 6px 0;
        display: inline-block;
        padding: 6px;
    }

    .arrow.up {
        transform: rotate(-135deg);
        -moz-transform: rotate(-135deg);
        -webkit-transform: rotate(-135deg);
    }
</style>
