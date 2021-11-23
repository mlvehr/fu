<template>
  <div class="wrapper scheduling-container">
    <!-- 线下排班  -->
    <div class="scheduling-wrapper offline-wrapper">
      <div class="title-wrapper">
        <div>线下排班</div>
        <el-link type="primary" :underline="false" @click="appendScheduling('offline')">
          <i class="el-icon-plus"></i> 新增排班
        </el-link>
      </div>
      <div class="empty-wrapper" v-if="offlineScheduling.length === 0">
        <el-link type="primary" :underline="false" @click="appendScheduling('offline')">
          <i class="el-icon-plus"></i> 新增排班
        </el-link>
      </div>
      <template v-else>
        <div class="form-wrapper" v-for="(schedule, index) in offlineScheduling" :key="index">
          <el-form ref="schedule" label-width="80px" :model="schedule" :rules="scheduleRules.common">
            <el-row :gutter="10" type="flex" align="middle">
              <el-col :span="6">
                <el-form-item label="门店属性" prop="cityName" :rules="scheduleRules.cityName">
                  <el-select v-model="schedule.cityName" placeholder="选择城市">
                    <el-option v-for="item in cityNameOptions"
                               :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label-width="0px" prop="storeCode" :rules="scheduleRules.storeCode">
                  <el-input v-model="schedule.storeCode" placeholder="门店编码"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label-width="0px">
                  <div class="btn btn-remove align-right">
                    <el-button icon="el-icon-delete" circle @click="removeScheduling('offline', index)"></el-button>
                  </div>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row :gutter="10" type="flex" align="middle">
              <el-col :span="12">
                <el-form-item label="时间属性" prop="dateRange">
                  <el-date-picker v-model="schedule.dateRange" type="datetimerange"
                                  range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label-width="0px" prop="cycleMode">
                  <el-select v-model="schedule.cycleMode" placeholder="循环周期">
                    <el-option v-for="item in cycleModeOptions"
                               :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row :gutter="10" type="flex" align="middle">
              <el-col :span="6">
                <el-form-item label="结束循环" prop="cycleEndMode">
                  <el-select class="full" v-model="schedule.cycleEndMode" placeholder="结束方式">
                    <el-option v-for="item in cycleEndModeOptions"
                               :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item v-if="schedule.cycleEndMode === 'frequency'"
                              label-width="0px" prop="cycleEndFrequency"
                              :rules="scheduleRules.cycleEndFrequency">
                  <el-select v-model="schedule.cycleEndFrequency" filterable placeholder="选择次数">
                    <el-option v-for="index in 99"
                               :key="index" :label="index" :value="index">
                    </el-option>
                  </el-select>
                </el-form-item>
                <el-form-item v-else-if="schedule.cycleEndMode === 'time'"
                              label-width="0px" prop="cycleEndTime"
                              :rules="scheduleRules.cycleEndTime">
                  <el-date-picker v-model="schedule.cycleEndTime" type="datetime" placeholder="选择日期时间"></el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="12" v-if="schedule.status">
                <el-form-item label-width="0px">
                  <div class="status align-right">{{ schedule.status }}</div>
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
        </div>
      </template>
    </div>
    <!-- 线上排班  -->
    <div class="scheduling-wrapper online-wrapper">
      <div class="title-wrapper">
        <div>线上排班</div>
        <el-link type="primary" :underline="false" @click="appendScheduling('online')">
          <i class="el-icon-plus"></i> 新增排班
        </el-link>
      </div>
      <div class="empty-wrapper" v-if="onlineScheduling.length === 0">
        <el-link type="primary" :underline="false" @click="appendScheduling('online')">
          <i class="el-icon-plus"></i> 新增排班
        </el-link>
      </div>
      <template v-else>
        <div class="form-wrapper" v-for="(schedule, index) in onlineScheduling" :key="index">
          <el-form ref="schedule" label-width="80px" :model="schedule" :rules="scheduleRules.common">
            <el-row :gutter="10" type="flex" align="middle">
              <el-col :span="12">
                <el-form-item label="时间属性" prop="dateRange">
                  <el-date-picker v-model="schedule.dateRange" type="datetimerange"
                                  range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期">
                  </el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label-width="0px" prop="cycleMode">
                  <el-select v-model="schedule.cycleMode" placeholder="循环周期">
                    <el-option v-for="item in cycleModeOptions"
                               :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label-width="0px">
                  <div class="btn btn-remove align-right">
                    <el-button icon="el-icon-delete" circle @click="removeScheduling('online', index)"></el-button>
                  </div>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row :gutter="10" type="flex" align="middle">
              <el-col :span="6">
                <el-form-item label="结束循环" prop="cycleEndMode">
                  <el-select class="full" v-model="schedule.cycleEndMode" placeholder="结束方式">
                    <el-option v-for="item in cycleEndModeOptions"
                               :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item v-if="schedule.cycleEndMode === 'frequency'"
                              label-width="0px" prop="cycleEndFrequency"
                              :rules="scheduleRules.cycleEndFrequency">
                  <el-select v-model="schedule.cycleEndFrequency" filterable placeholder="选择次数">
                    <el-option v-for="index in 99"
                               :key="index" :label="index" :value="index">
                    </el-option>
                  </el-select>
                </el-form-item>
                <el-form-item v-else-if="schedule.cycleEndMode === 'time'"
                              label-width="0px" prop="cycleEndTime"
                              :rules="scheduleRules.cycleEndTime">
                  <el-date-picker v-model="schedule.cycleEndTime" type="datetime" placeholder="选择日期时间"></el-date-picker>
                </el-form-item>
              </el-col>
              <el-col :span="12" v-if="schedule.status">
                <el-form-item label-width="0px">
                  <div class="status align-right">{{ schedule.status }}</div>
                </el-form-item>
              </el-col>
            </el-row>
          </el-form>
        </div>
      </template>
    </div>


    <el-button type="primary" @click="onSaveHandle()">保存</el-button>
  </div>
</template>

<script>
export default {
  name: 'Scheduling',
  props: {},
  data() {
    return {
      cityNameOptions: [
        {label: '杭州', value: 'HZ'},
        {label: '长沙', value: 'CS'},
      ],
      cycleModeOptions: [
        {label: '一次性', value: '1'},
        {label: '每周', value: '2'},
        {label: '每月', value: '3'},
      ],
      cycleEndModeOptions: [
        {label: '永不', value: 'never'},
        {label: '时间', value: 'time'},
        {label: '次数', value: 'frequency'},
      ],
      offlineScheduling: [],
      onlineScheduling: [],

      scheduleRules: {
        common: {
          dateRange: [
            {
              validator: (rule, value, cb) => (!Array.isArray(value) || value.length !== 2) ? cb('请选择时间') : cb(),
              trigger: ['blur', 'change']
            },
          ],
          cycleMode: [{required: true, message: '请选择循环周期', trigger: ['blur', 'change']},],
          cycleEndMode: [{required: true, message: '请选择结束方式', trigger: ['blur', 'change']},]
        },
        cityName: [{required: true, message: '请选择城市', trigger: ['blur', 'change']},],
        storeCode: [
          {required: true, message: '请输入门店编码', trigger: ['blur', 'change']},
          {min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: ['blur', 'change']}
        ],
        cycleEndTime: [{required: true, message: '请选择结束日期时间', trigger: ['blur', 'change']}],
        cycleEndFrequency: [{required: true, message: '请选择结束次数', trigger: ['blur', 'change']}],
      },
    };
  },
  methods: {
    appendScheduling(type, value = {}) {
      switch (type) {
        case 'offline': {
          value = Object.assign(
              {},
              {
                status: '',
                cityName: null,
                storeCode: null,
                dateRange: [],
                cycleMode: null,
                cycleEndMode: 'never',
                cycleEndTime: null,
                cycleEndFrequency: null,
              },
              value
          );
          this.offlineScheduling.push(value);
          break;
        }
        case 'online': {
          value = Object.assign(
              {},
              {
                status: '',
                dateRange: [],
                cycleMode: null,
                cycleEndMode: null,
                cycleEndTime: null,
                cycleEndFrequency: null,
              },
              value
          );
          this.onlineScheduling.push(value);
          break;
        }
      }
    },
    removeScheduling(type, index) {
      switch (type) {
        case 'offline': {
          this.offlineScheduling.splice(index, 1);
          break;
        }
        case 'online': {
          this.onlineScheduling.splice(index, 1);
          break;
        }
      }
    },

    onSaveHandle() {
      console.log(this.$refs.schedule);

      Promise.all(Array.from(this.$refs.schedule, (ref) => ref.validate())).then(valid => {
        console.log('有效效验', valid);
      }).catch(invalid => {
        console.log('无效效验', invalid);
      });
    }
  },
  beforeMount() {
    this.appendScheduling('offline', {status: '已过期'});
  }
};
</script>

<style scoped lang="scss">
.wrapper {
  margin: 0 auto;
  width: 80%;

  .scheduling-wrapper {
    margin: 18px;

    &:first-of-type {
      margin-top: 0;
    }

    &:last-of-type {
      margin-bottom: 0;
    }

    .title-wrapper {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 24px;
      line-height: 1.5;
    }

    .empty-wrapper {
      display: flex;
      justify-content: center;
      align-items: center;
      margin: 10px 0;
      min-height: 150px;
      border-radius: 10px;
      border: 2px dashed #f5f5f5;
    }

    .form-wrapper {
      margin: 10px 0;
      padding: 12px;
      border-radius: 10px;
      background-color: #f5f5f5;
    }
  }

  .status {
    color: #ccc;
  }
}

.align-right {
  text-align: right;
}

.btn-remove {
  cursor: pointer;
}
</style>

<style lang="scss">
.scheduling-container {
  .el-select, .el-date-editor {
    width: 100%;
  }
}
</style>
