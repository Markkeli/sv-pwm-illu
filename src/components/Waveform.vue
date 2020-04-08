<template>
   <div class="Waveform">
     <apexchart width="500" type="line" :options="chartOptions" :series="series"></apexchart>
   </div>
</template>

<script>

    export default {
        name: "Waveform",
        props: {
            newSignal: Array
        },
        data: function() {  
                return {
                    chartOptions: {
                        chart: {
                            id: 'realtime',
                            height: 350,
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
                            }
                            
                        },
                        dataLabels: {
                            enabled: false
                        },
                        stroke: {
                            curve: 'stepline'
                        },
                        title: {
                            text: 'Output voltage',
                            align: 'left'
                        },
                        xaxis: {
                            type: 'numeric',
                            range: 100,
                            tickAmount: 5
                        },
                        yaxis: {
                            max: 100,
                            min: -100
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
                    ]
                };
            },
        mounted() { 
       
        },
        watch: {
            
            newSignal: {
                handler: function(val) {
                    this.updateSignal(val);
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
                    }                                        
                ]
            }
        }
    }

</script>
