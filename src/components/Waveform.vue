<template>
    <div class="Waveform">
        <div>
            <apexchart width="400" height="200"  type="line" :options="chartOptions3" :series="series3" id="chart3"></apexchart>
        </div>
        <div class="charts">
            <apexchart width="400" height="200" type="line" :options="chartOptions" :series="series"></apexchart>
            <apexchart width="400" height="200"  type="line" :options="chartOptions2" :series="series2"></apexchart>
        </div>

    </div>
</template>

<script>

    export default {
        name: "Waveform",
        props: {
            newSignal: Array,
            voltages: Array
        },
        data: function() {  
                return {
                    momentaryVoltageArray: [],
                    chartOptions: {
                        chart: {
                            id: 'linevoltages',
                            type: 'line',
                            animations: {
                                enabled: true,
                                easing: 'linear',
                                dynamicAnimation: {
                                speed: 10
                                }
                            },
                            toolbar: {
                                show: false
                            },
                            zoom: {
                                enabled: true
                            },
                            fontFamily: 'Segoe UI'
                            
                        },
                        dataLabels: {
                            enabled: false
                        },
                        stroke: {
                            curve: 'stepline',
                            width: 2
                        },
                        title: {
                            text: 'Inverter voltages of one switching period',
                            align: 'center'
                        },
                        xaxis: {
                            type: 'numeric',
                            range: 100,
                            tickAmount: 5,
                            labels: {
                                show: false
                            }
                        },
                        yaxis: {
                            type: 'numeric',
                            max: 1.0,
                            min: -1.0,
                            allowDecimals: false,
                            tickAmount: 3,
                            labels: {
                                show: false
                            }
                        }
                    },
                    series: [
                        {
                            name: 'A-B Line-to-line voltage',
                            data: []
                        },
                        {
                            name: 'B-C Line-to-line voltage',
                            data: []
                        },
                        {
                            name: 'C-A Line-to-line voltage',
                            data: []
                        }                               
                    ],
                    chartOptions2: {
                        chart: {
                            id: 'phasevoltages',
                            type: 'line',
                            animations: {
                                enabled: true,
                                easing: 'linear',
                                dynamicAnimation: {
                                speed: 10
                                }
                            },
                            toolbar: {
                                show: false
                            },
                            zoom: {
                                enabled: false
                            },
                            fontFamily: 'Segoe UI'
                            
                        },
                        dataLabels: {
                            enabled: false
                        },
                        stroke: {
                            curve: 'stepline',
                            width: 2
                        },
                       /* title: {

                            text: 'Phase voltages',
                            align: 'left'
                        },*/
                        xaxis: {
                            type: 'numeric',
                            range: 100,
                            tickAmount: 5,
                            labels: {
                                show: false
                            }
                        },
                        yaxis: {
                            type: 'numeric',                            
                            max: 1.0,
                            min: -1.0,
                            allowDecimals: false,
                            tickAmount: 3,
                            labels: {
                                show: false
                            }
                        }

                    },
                    series2: [
                        {
                            name: 'Phase A voltage',
                            data: []
                        },
                        {
                            name: 'Phase B voltage',
                            data: []
                        },
                        {
                            name: 'Phase C voltage',
                            data: []
                        }                                  
                    ],
                    chartOptions3: {
                        chart: {
                            id: 'momentaryvoltages',
                            type: 'line',
                            animations: {
                                enabled: true,
                                easing: 'linear',
                                dynamicAnimation: {
                                speed: 10
                                }
                            },
                            toolbar: {
                                show: false
                            },
                            zoom: {
                                enabled: false
                            },
                            fontFamily: 'Segoe UI'
                            
                        },
                        dataLabels: {
                            enabled: false
                        },
                        stroke: {
                            curve: 'smooth',
                            width: 2
                        },
                        title: {

                            text: 'Real time updating reference voltages',
                            align: 'center'
                        },
                        xaxis: {
                            type: 'numeric',
                            range: 100,
                            tickAmount: 5,
                            labels: {
                                show: false
                            }
                        },
                        yaxis: {
                            type: 'numeric',                            
                            allowDecimals: false,
                            tickAmount: 3,
                            labels: {
                                show: false
                            }
                        }

                    },
                    series3: [
                        {
                            name: 'Phase A voltage',
                            data: []
                        },
                        {
                            name: 'Phase B voltage',
                            data: []
                        },
                        {
                            name: 'Phase C voltage',
                            data: []
                        }                                  
                    ]                   

                };
            },
        mounted() { 
            for (var i = 0; i < 100; ++i) {
                this.momentaryVoltageArray[i] = [0, 0, 0];
            }

        },
        watch: {
            
            newSignal: {
                handler: function(val) {
                    this.updateSignal(val);
                },
                deep: true
            },
            voltages: {
                handler: function(val) {
                    var newVoltageArray = [];

                    for (var i = 0; i < 99; ++i) {
                        newVoltageArray[i] = this.momentaryVoltageArray[i+1];
                    }
                    newVoltageArray[99] = val;

                    var v_an = [];
                    var v_bn = [];
                    var v_cn = [];

                    for ( i = 0; i < 100; ++i ) {
                        v_an[i] = newVoltageArray[i][0];
                        v_bn[i] = newVoltageArray[i][1];
                        v_cn[i] = newVoltageArray[i][2];
                    }

                    this.momentaryVoltageArray = newVoltageArray;
                    
                    this.series3 = [
                        {
                            data: v_an
                        },
                        {
                            data: v_bn
                        },
                        {
                            data: v_cn
                        } 
                    ]    
                },
                deep: true
            }
            
        },
        methods: {
            updateSignal(signal) {
                this.series = [
                    {
                        data: signal[0]
                    },
                    {
                        data: signal[1]
                    },
                    {
                        data: signal[2]
                    },                                                              
                ];

                this.series2 = [
                    {
                        data: signal[3]
                    },
                    {
                        data: signal[4]
                    },
                    {
                        data: signal[5]
                    }  
                ]; 
            }
        }
    }

</script>
<style scoped>

.charts {
    display: inline-block;
    position: static;

}

#chart3 {
    float: left;
}


</style>
