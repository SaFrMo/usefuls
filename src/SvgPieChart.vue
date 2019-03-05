<template>
    <svg
        class="svg-pie-chart"
        viewBox="-1 -1 2 2"
        style="transform: rotate(-0.25turn)"
        width="100"
        height="100"
    >
        <defs>
            <mask id="svg-pie-chart-mask">
                <circle cx="0" cy="0" r="1" fill="#fff" />
                <circle cx="0" cy="0" :r="cutout" fill="#000" />
            </mask>
        </defs>
        <g class="pie"><path v-for="(val, i) in values" :d="buildPath(i)" /></g>
    </svg>
</template>

<script>
// built using this article: https://hackernoon.com/a-simple-pie-chart-in-svg-dbdd653b6936

export default {
    props: {
        values: {
            type: Array,
            default: () => [0.12, 0.34, 0.54]
        },
        cutout: {
            type: Number,
            default: 0.7
        }
    },
    methods: {
        getCoordinatesForPercent(percent) {
            const x = Math.cos(2 * Math.PI * percent)
            const y = Math.sin(2 * Math.PI * percent)

            return [x, y]
        },
        buildPath(index = 0) {
            let start = 0
            const percent = this.values[index]

            for (let i = 0; i < index; i++) {
                start += this.values[i]
            }

            const startX = this.getCoordinatesForPercent(start)[0]
            const startY = this.getCoordinatesForPercent(start)[1]
            const endX = this.getCoordinatesForPercent(start + percent)[0]
            const endY = this.getCoordinatesForPercent(start + percent)[1]

            const largeArcFlag = percent > 0.5 ? 1 : 0

            return (
                `M ${startX} ${startY}` +
                `A 1 1 0 ${largeArcFlag} 1 ${endX} ${endY}`
            )
        }
    }
}
</script>

<style lang="scss">
$count: 3;

.svg-pie-chart {
    .pie {
        mask: url(#svg-pie-chart-mask);
    }

    path {
        fill: transparent;
        stroke-width: 2;

        @for $i from 1 through $count {
            &:nth-of-type(#{$i}) {
                stroke: hsl((360 / $count) * $i, 50%, 50%);
            }
        }
    }
}
</style>
