<template>
    <div>
        <div class="col-md-8 control-section">
            <div class="content-wrapper">
                <ejs-circulargauge ref="circulargauge" style='display:block' align='center' id='gauge-container' :title='title' centerY='75%' :titleStyle='titleStyle' :legendSettings='legendSettings' :tooltipRender='tooltipRender' :tooltip='tooltip'>
                    <e-axes>
                        <e-axis :startAngle='startAngle' :endAngle='endAngle' :lineStyle='lineStyle' :labelStyle='labelStyle' :majorTicks='majorTicks' :minorTicks='minorTicks' 
                        :radius='radius' minimum=0 maximum=100 :annotations='annotations' :ranges='ranges'>
                            <e-pointers>
                                <!-- <e-pointer :value='value' :radius='pointerRadius' :color='color' :pointerWidth='pointerWidth' :animation='animation' :cap='cap' :needleTail="needleTail"></e-pointer> -->
                            </e-pointers>
                        </e-axis>
                    </e-axes>
                </ejs-circulargauge>
            </div>
        </div>
    </div>
</template>
<style scoped>
    #control-container {
        padding: 0px !important;
    }
</style>
<script>
import Vue from 'vue';
import { CircularGaugePlugin, GaugeTooltip, Annotations, Legend } from "@syncfusion/ej2-vue-circulargauge";
import { CheckBoxPlugin } from "@syncfusion/ej2-vue-buttons";
Vue.use(CircularGaugePlugin);
Vue.use(CheckBoxPlugin);

// let templateString = '<div id="templateWrap"><div class="des">'+
//                 '<div id="pointerannotation" style="width:90px;text-align:center;font-size:20px;font-family:Roboto">${pointers[0].value} km/h</div></div></div>';

let colors = ['#5b94ce', '#27af34', '#5a5b5b']

let radius = ['120%', '100%', '80%']

let chartData = [
    {
        name: 'Retail Value',
        value: 27626
    }, {
        name: 'Trade',
        value: 18626
    }, {
        name: 'Auction',
        value: 19626
    }
];

let maxValue = chartData.reduce(function(max, x) { return (x.value > max) ? x.value : max; }, 0);

chartData.forEach(function(data) {
    data['percentage'] = parseFloat((data.value / maxValue * 100).toFixed(2))
})

chartData.sort(function(a, b) {
    if (a.value > b.value) return -1;
    else if (a.value < b.value) return 1;
    else return 0;
});

let ranges = [];
chartData.forEach((value, index) => {
    let range = {
        start: 0,
        end: value.percentage,
        startWidth: 5, endWidth: 35,
        radius: radius[index],
        color: colors[index],
        legendText: value.name
    };
    ranges.push(range);
})

export default Vue.extend({
    name: 'Chart',
    props: {
        msg: String
    },
    data:function(){
        return {
            minimum: 0,
            maximum: 100,
            value: 40,
            radius: '120%',
            pointerRadius: '80%',
            lineStyle: { width: 0 },
            majorTicks: { width: 0, },
            minorTicks: { width: 0 },
            pointerWidth: 7,
            titleStyle: { size: '18px' },
            labelStyle: {
                size: '0px',
                visible: false,
                // show: false,
                // useRangeColor: false, 
                // position: 'Outside', 
                // autoAngle: true,
                // font: { size: '13px', fontFamily: 'Roboto' } 
            },
            startAngle: 270, 
            endAngle: 90,
            color: '#757575',
            title: 'Chart Example',
            cap: {
                radius: 8,
                color: '#757575',
                border: { width: 0 }
            },
            needleTail: {
                color: '#757575',
                length: '15%'
            },

            annotations: [
                {
                    angle: 0, zIndex: '1',
                    radius: '30%'
                }
            ],
            ranges: ranges,
            legendSettings: {
                visible: true,
                position: 'Bottom',
                shapeWidth: 20,
                shapeHeight: 20,
                padding: 20
            },
            tooltip: {
                type:['Pointer', 'Range', 'Annotation'],
                enable: true,
            },
        }
    },
    provide: {
        circulargauge: [GaugeTooltip, Annotations, Legend]
    },
    mounted : function(){
        this.customChartStyle();
    },
    beforeDestroy () {
        clearInterval(this.intervalid1)
    },
    methods: {
        customChartStyle: function() {
            document.getElementById('gauge-container_Axis_Labels_0').style.display = 'none';
            let elements = document.querySelectorAll("[id^='gauge-container_gauge_legend_Axis_0_text_']");
            elements.forEach(function(element) {
                element.style.fontSize = '20px';
            });
        },
        tooltipRender: function (args) {
            let percentage = args.range.end;
            let rowData = filterRowData(percentage);
            let price = rowData.value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
            args.tooltip.rangeSettings.template =  `<div id='templateWrap'><div class='des'><span>$${price}</span></div></div>`;
        }
    }
})

function filterRowData(percentage) {
    let rowData = chartData.filter(function(item) { return item.percentage === percentage; })
    return rowData[0]
}

</script>

<style>
    #templateWrap .des {
        float: right;
        padding-left: 10px;
        line-height: 30px;
        font-size: 22px;
        width: 100%;
        height: 35px;
        background-color: #efefef;
        opacity: 1;
        color: #000;
        border-radius: 10px;
        margin: auto;
        padding: 6px;
    }
</style>