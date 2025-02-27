<template>
    <BootstrapAlert :show="!devInfoList.valid_data">
        <h4 class="alert-heading"><BIconInfoSquare class="fs-2" />&nbsp;{{ $t('devinfo.NoInfo') }}</h4>
        {{ $t('devinfo.NoInfoLong') }}
    </BootstrapAlert>
    <table v-if="devInfoList.valid_data" class="table table-hover">
        <tbody>
            <tr>
                <td>{{ $t('devinfo.Serial') }}</td>
                <td>{{ devInfoList.serial }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.ProdYear') }}</td>
                <td>{{ productionYear() }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.ProdWeek') }}</td>
                <td>{{ productionWeek() }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.Model') }}</td>
                <td v-if="devInfoList.hw_model_name != ''">{{ devInfoList.hw_model_name }}</td>
                <td v-else v-html="$t('devinfo.UnknownModel')"></td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.DetectedMaxPower') }}</td>
                <td>{{ $n(devInfoList.max_power, 'decimal') }} W</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.BootloaderVersion') }}</td>
                <td>{{ formatVersion(devInfoList.fw_bootloader_version) }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.FirmwareVersion') }}</td>
                <td>{{ formatVersion(devInfoList.fw_build_version) }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.FirmwareBuildDate') }}</td>
                <td>{{ devInfoList.fw_build_datetime }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.HardwarePartNumber') }}</td>
                <td>{{ devInfoList.hw_part_number }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.HardwareVersion') }}</td>
                <td>{{ devInfoList.hw_version }}</td>
            </tr>
            <tr>
                <td>{{ $t('devinfo.SupportsPowerDistributionLogic') }}</td>
                <td>
                    <StatusBadge :status="devInfoList.pdl_supported" true_text="devinfo.Yes" false_text="devinfo.No" />
                </td>
            </tr>
        </tbody>
    </table>
</template>

<script lang="ts">
import BootstrapAlert from '@/components/BootstrapAlert.vue';
import type { DevInfoStatus } from '@/types/DevInfoStatus';
import StatusBadge from '@/components/StatusBadge.vue';
import { BIconInfoSquare } from 'bootstrap-icons-vue';
import { defineComponent, type PropType } from 'vue';

export default defineComponent({
    components: {
        BootstrapAlert,
        BIconInfoSquare,
        StatusBadge,
    },
    props: {
        devInfoList: { type: Object as PropType<DevInfoStatus>, required: true },
    },
    computed: {
        formatVersion() {
            return (value: number) => {
                const version_major = Math.floor(value / 10000);
                const version_minor = Math.floor((value - version_major * 10000) / 100);
                const version_patch = Math.floor(value - version_major * 10000 - version_minor * 100);
                return version_major + '.' + version_minor + '.' + version_patch;
            };
        },
        productionYear() {
            return () => {
                return ((parseInt(this.devInfoList.serial, 16) >> (7 * 4)) & 0xf) + 2014;
            };
        },
        productionWeek() {
            return () => {
                return ((parseInt(this.devInfoList.serial, 16) >> (5 * 4)) & 0xff).toString(16);
            };
        },
    },
});
</script>
