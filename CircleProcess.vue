<!-- 静态环形进度条 -->
<template>
    <canvas :width="size" :height="size"></canvas>
</template>

<script>
export default {
    name: 'circleProcess',
    props: {
        start: { // 起始点
            type: Number,
            default: 0
        },
        percentage: { // 控制进度的百分比，可以传百分比或小数
            type: Number,
            default: 0
        },
        size: { // 大小,单位px
            type: Number,
            default: 0,
            require: true
        },
        trackWidth: { // 进度条宽度
            type: Number,
            default: 6
        },
        showText: { // 显示文字
            type: Boolean,
            default: false
        },
        color: { // 进度条颜色
            type: String,
            default: '#6EC8D2'
        },
        trackColor: { // 轨道颜色
            type: String,
            default: '#E1EBED'
        },
        counterclockwise: { // false顺时针 true逆时针
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            context: null,
            centerX: 0,
            centerY: 0
        }
    },
    methods: {
        init() {
            let canvas = this.$el;
            this.context = canvas.getContext('2d');
            this.context.clearRect(0, 0, this.size, this.size);
            this.centerX = this.centerY = this.size / 2;
            this.drawTrack();
            this.drawProcess();
            this.showText && this.fillText();
        },
        drawTrack() {
            let context = this.context;
            context.lineWidth = this.trackWidth;
            context.strokeStyle = this.trackColor;
            context.beginPath();
            context.arc(this.centerX, this.centerY, this.radius, 0, 2 * Math.PI);
            context.stroke();
            context.closePath();
        },
        drawProcess() {
            let context = this.context;
            context.lineWidth = this.trackWidth;
            context.strokeStyle = this.color;
            context.beginPath();
            context.arc(this.centerX, this.centerY, this.radius, this.startPoint, this.endPoint, this.counterclockwise);
            context.stroke();
            context.closePath();
        },
        fillText() {
            let context = this.context;
            context.font="16px";
            context.textAlign = 'center';
            context.textBaseline='middle';
            context.fillText(this.percentText, this.centerX, this.centerY);
        }
    },
    computed: {
        percentText() {
            return parseInt(this.percentage) + '%';
        },
        startPoint() {
            return this.start / 360 * 2 * Math.PI;
        },
        endPoint() {
            let arc = this.percentage / 100 * 2 * Math.PI;
            return this.counterclockwise ? this.startPoint - arc : this.startPoint + arc;
        },
        radius() {
            return this.size / 2 - this.trackWidth / 2;
        }
    },
    watch: {
        percentage: {
            immediate: true,
            handler() {
                this.$nextTick(() => {
                    this.init();
                });
            }
        }
    }
}
</script>