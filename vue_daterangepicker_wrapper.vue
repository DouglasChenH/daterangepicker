<script>
/**
 * FormDateRangePicker
 * Form DateRangePicker component
 */
var daterangepicker = require('bootstrap-daterangepicker')

var vue = {
    name: 'FormDateRangePicker',
    props: {
        showRanges: {
            type: Boolean,
            default: true
        },
        startTime: {
            default: function () {
                return moment().subtract(1, 'days');
            }
        },
        endTime: {
            default: function () {
                return moment();
            }
        },
        label: {
            type: String,
            default: 'Today'
        },
        minDate: {
            default: false
        },
        maxDate: {
            default: false
        },
        opens: {
            default: 'right'
        },  
        autoApply: {
            default: false
        },
        FORMAT: {
            type: String,
            default: 'llll'
        }
    },
    data: function () {
        return {
            start: this.startTime,
            end: this.endTime,
             // label: null
        };
    },
    computed: {
        dateRange: function () {
            return this.label + ': ' + this.start.format(this.FORMAT) + ' - ' + this.end.format(this.FORMAT)
            
            // var today = moment();
            // if (
            //     start.format('LL') === end.format('LL') &&
            //     today.format('LL') === start.format('LL')
            // ) {
            //     return 'Today';
            // } else if (start.format('MM-DD-YYYY') === end.format('MM-DD-YYYY')) {
            //     return start.format('LL');
            // }

            // return start.format('LL') + ' - ' + end.format('LL');
        }
    },
    methods: {
        callback: function (start, end, label) {
            var endTime = this.endTime
            // daterangepicker bug. Returned end values are incorrect in these cases
            if (label == 'Last 10 Minutes') {
                start = endTime.clone().subtract(10, 'minutes')
                end = endTime
            }
            if (label === 'Last 1 Hour') {
                start = endTime.clone().subtract(1, 'hours')
                end = endTime
            }
            if (label === 'Today') {
                start = endTime.clone().startOf('day')
                end = endTime
            }
            if (label === 'This Month') {
                start = endTime.clone().startOf('month')
                end = endTime
            }
            if (label === 'Last 1 Year') {
                start = endTime.clone().subtract(1, 'years').startOf('year')
                end = endTime
            }

            this.$emit('apply', start, end, label);
            this.start = start
            this.end = end
            this.label = label
        }
    },
    ready: function () {
        // this.start = moment(this.start);
        // this.end = moment(this.end);
        this.$nextTick(function () {
            var options = { 
                linkedCalendars: false,
                showDropdowns: true,
                alwaysShowCalendars: false,
                // minDate: ,
                maxDate: this.startTime,
                // minYear: 2018,
                // maxYear: 2019,
                timePicker: true,
                timePickerSeconds: true,
                timePicker24Hour: true,
                showCustomRangeLabel: this.showRanges,
                opens: this.opens,
                startDate: this.start,
                endDate: this.end,
                autoApply: this.autoApply,
                alwaysShowCalendars: true
            };

            if (this.minDate) {
                options.minDate = this.minDate
            }
            if (this.maxDate) {
                options.maxDate = this.maxDate
            }

            if (this.showRanges) {
                var endTime = this.endTime.clone()
                options.ranges = {
                    'Last 10 Minutes': [endTime.clone().subtract(10, 'minutes'),  endTime],
                    'Last 1 Hour': [endTime.clone().subtract(1, 'hours'), endTime],
                    'Today': [endTime.clone().startOf('day'), endTime],
                    'Yesterday': [endTime.clone().subtract(1, 'days').startOf('day'), endTime.clone().subtract(1, 'days').endOf('day')],
                    'Last 7 Days': [endTime.clone().subtract(7, 'days').startOf('day'), endTime.clone().subtract(1, 'days').endOf('day')],
                    'Last 30 Days': [endTime.clone().subtract(30, 'days').startOf('day'), endTime.clone().subtract(1, 'days').endOf('day')],
                    'This Month': [endTime.clone().startOf('month'), endTime],
                    'Last Month': [endTime.clone().subtract(1, 'months').startOf('month'), endTime.clone().subtract(1, 'months').endOf('month')],
                    'Last 1 Year': [endTime.clone().subtract(1, 'years').startOf('year'), endTime]
                }
            }

            $(this.$el).daterangepicker(options, this.callback)
            // $(this.$el).on('apply.daterangepicker', (ev, picker) =>{
            //     this.start = picker.startDate
            //     this.end = picker.endDate
            //     this.label = picker.chosenLabel
            // });
        })
    },
    events: {
        updateRangePicker: function (updatedStartTime, updatedEndTime) {
            if (updatedStartTime && updatedEndTime) {
                $(this.$el).data('daterangepicker').setStartDate(updatedStartTime)
                $(this.$el).data('daterangepicker').setEndDate(updatedEndTime)
            }
        }
    }
}

module.exports = vue
</script>

<template lang="jade">
div(style='overflow:auto')
    div.btn-group
        a.btn.btn-default.btn-rounded.calendar-picker(data-toggle="collapse",aria-expand="true")
            span.i.fa.fa-calendar &nbsp;&nbsp;
            span.date-range-label {{ dateRange }}
            span.caret 
</template>

<style type="text/css" >
</style>

