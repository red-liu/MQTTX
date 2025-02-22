<template>
  <div class="settings-view rightbar">
    <h1 class="titlebar">{{ $t('settings.settings') }}</h1>
    <div class="settings-general">
      <div class="settings-title">{{ $t('settings.general') }}</div>

      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.language') }}</label>
        </el-col>
        <el-col :span="4">
          <el-select
            class="settings-options"
            v-model="currentLang"
            size="mini"
            @change="handleSelectChange('lang', $event)"
          >
            <el-option v-for="(lang, index) in langOptions" :key="index" :label="lang.label" :value="lang.value">
            </el-option>
          </el-select>
        </el-col>
      </el-row>

      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.automatically') }}</label>
        </el-col>
        <el-col :span="4">
          <el-switch
            v-model="autoCheck"
            active-color="#13ce66"
            inactive-color="#A2A9B0"
            @change="handleAutoCheckSwitchChange"
          >
          </el-switch>
        </el-col>
      </el-row>

      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.autoResub') }}</label>
          <el-tooltip
            placement="top"
            :effect="currentTheme !== 'light' ? 'light' : 'dark'"
            :open-delay="500"
            :content="$t('settings.autoResubDesc')"
          >
            <a href="javascript:;" class="icon-oper">
              <i class="el-icon-warning-outline"></i>
            </a>
          </el-tooltip>
        </el-col>
        <el-col :span="4">
          <el-switch
            v-model="autoResub"
            active-color="#13ce66"
            inactive-color="#A2A9B0"
            @change="handleAutoResubSwitchChange"
          >
          </el-switch>
        </el-col>
      </el-row>

      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.maxReconnectTimes') }}</label>
        </el-col>
        <el-col :span="4">
          <el-input-number size="mini" v-model="maxReconnectTimes" :min="1" @change="handleInputChage">
          </el-input-number>
        </el-col>
      </el-row>

      <el-divider></el-divider>
    </div>

    <div class="settings-appearance">
      <div class="settings-title">{{ $t('settings.appearance') }}</div>

      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.theme') }}</label>
        </el-col>
        <el-col :span="4">
          <el-select
            class="settings-options"
            v-model="currentTheme"
            size="mini"
            @change="handleSelectChange('theme', $event)"
          >
            <el-option v-for="(theme, index) in themeOptions" :key="index" :label="theme.label" :value="theme.value">
            </el-option>
          </el-select>
        </el-col>
      </el-row>

      <el-divider></el-divider>
    </div>

    <div class="settings-advanced">
      <div class="settings-title">{{ $t('settings.advanced') }}</div>
      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.dataRecovery') }}</label>
        </el-col>
        <el-col :span="4">
          <el-button
            class="data-manager-btn"
            type="primary"
            size="mini"
            icon="el-icon-upload2"
            @click="handleImportData"
          >
          </el-button>
        </el-col>
      </el-row>
      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.dataBackup') }}</label>
        </el-col>
        <el-col :span="4">
          <el-button
            class="data-manager-btn"
            type="primary"
            size="mini"
            icon="el-icon-printer"
            @click="handleExportData"
          >
          </el-button>
        </el-col>
      </el-row>
      <el-divider></el-divider>

      <el-row class="settings-item" type="flex" justify="space-between">
        <el-col :span="20">
          <label>{{ $t('settings.historyCleanup') }}</label>
        </el-col>
        <el-col :span="4">
          <el-button
            class="data-manager-btn"
            type="primary"
            size="mini"
            icon="el-icon-delete"
            @click="handleCleanupHistoryData"
          >
          </el-button>
        </el-col>
      </el-row>
      <el-divider></el-divider>

      <ImportData :visible.sync="showImportData" />
      <ExportData :visible.sync="showExportData" />
      <ClearUpHistoryData :visible.sync="showHistoryData" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import { Getter, Action } from 'vuex-class'
import { ipcRenderer } from 'electron'
import ImportData from '@/components/ImportData.vue'
import ExportData from '@/components/ExportData.vue'
import ClearUpHistoryData from '@/components/ClearUpHistoryData.vue'

@Component({
  components: { ImportData, ExportData, ClearUpHistoryData },
})
export default class Settings extends Vue {
  @Action('TOGGLE_THEME') private actionTheme!: (payload: { currentTheme: string }) => void
  @Action('TOGGLE_LANG') private actionLang!: (payload: { currentLang: string }) => void
  @Action('TOGGLE_AUTO_CHECK') private actionAutoCheck!: (payload: { autoCheck: boolean }) => void
  @Action('TOGGLE_AUTO_RESUB') private actionAutoResub!: (payload: { autoResub: boolean }) => void
  @Action('SET_MAX_RECONNECT_TIMES') private actionMaxReconnectTimes!: (payload: { maxReconnectTimes: number }) => void
  @Getter('currentTheme') private getterTheme!: Theme
  @Getter('currentLang') private getterLang!: Language
  @Getter('autoCheck') private getterAutoCheck!: boolean
  @Getter('autoResub') private getterAutoResub!: boolean
  @Getter('maxReconnectTimes') private getterMaxReconnectTimes!: number

  private currentTheme: Theme = 'light'
  private currentLang: Language = 'en'
  private autoCheck = false
  private autoResub = true
  private maxReconnectTimes = 10
  private langOptions: Options[] = [
    { label: '简体中文', value: 'zh' },
    { label: 'English', value: 'en' },
    { label: '日本語', value: 'ja' },
  ]
  private themeOptions: Options[] = [
    { label: 'Light', value: 'light' },
    { label: 'Dark', value: 'dark' },
    { label: 'Night', value: 'night' },
  ]
  private showImportData = false
  private showExportData = false
  private showHistoryData = false

  private handleSelectChange(type: 'lang' | 'theme', value: string | number | boolean): void {
    if (type === 'theme') {
      this.actionTheme({ currentTheme: value as string })
    } else if (type === 'lang') {
      this.actionLang({ currentLang: value as string })
    }
    ipcRenderer.send('setting', type, value)
  }

  private handleAutoCheckSwitchChange(value: boolean) {
    this.actionAutoCheck({ autoCheck: value })
  }

  private handleAutoResubSwitchChange(value: boolean) {
    this.actionAutoResub({ autoResub: value })
  }

  private handleInputChage(value: number) {
    this.actionMaxReconnectTimes({ maxReconnectTimes: value })
  }

  private handleImportData() {
    this.showImportData = true
  }

  private handleExportData() {
    this.showExportData = true
  }

  private handleCleanupHistoryData() {
    this.showHistoryData = true
  }

  private created() {
    this.autoCheck = this.getterAutoCheck
    this.autoResub = this.getterAutoResub
    this.currentTheme = this.getterTheme
    this.currentLang = this.getterLang
    this.maxReconnectTimes = this.getterMaxReconnectTimes
  }
}
</script>

<style lang="scss" scope>
@import '@/assets/scss/variable.scss';

.settings-view {
  position: relative;
  padding: 0 16px;
  -moz-user-select: none;
  -khtml-user-select: none;
  user-select: none;

  .settings-general {
    margin-top: 30px;
  }

  [class$='general'],
  [class$='appearance'] {
    margin-bottom: 80px;
  }

  .el-divider--horizontal {
    margin: 15px 0;
  }

  .settings-title {
    color: var(--color-text-light);
    margin-bottom: -5px;
  }

  .settings-item {
    label {
      color: var(--color-text-title);
    }
  }

  .el-col-4,
  .el-col-6 {
    text-align: right;
  }

  .settings-options {
    .el-input__inner {
      border: none;
      background: transparent;
      font-size: $font-size--body;
      color: var(--color-text-default);
    }

    &.el-select {
      width: 108px;
    }

    &.el-select .el-input .el-select__caret {
      color: var(--color-text-default);
    }
  }

  .el-input-number__increase,
  .el-input-number__decrease {
    background: var(--color-bg-input_btn);
  }
  .el-input-number__decrease {
    border-right: 1px solid var(--color-border-default);
  }
  .el-input-number__increase {
    border-left: 1px solid var(--color-border-default);
  }
  .el-input-number--mini .el-input__inner {
    text-align: center;
  }

  i {
    font-size: 16px;
  }
  .data-manager-btn {
    width: 90px;
  }
  .icon-oper {
    position: relative;
    top: 1px;
    left: 5px;
    color: var(--color-text-default);
  }
}
</style>
