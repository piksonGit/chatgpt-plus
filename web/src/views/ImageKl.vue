<template>
  <div class="page-mj">
	<p class="tip">本对话由重庆列星文化传播有限公司旋风图片生成大模型算法A生成</p>
    <div class="inner custom-scroll " style="padding:0px 20px;">
      <div class="mj-box hidden">
		<p class="tip">本对话由重庆列星文化传播有限公司旋风图片生成大模型算法A生成</p>
        <h2 class="title">旋风绘图创作中心</h2>
		

        <div class="mj-params hidden" :style="{ height: mjBoxHeight + 'px' }">
          <el-form :model="params" label-width="80px" label-position="left">
            <div class="param-line pt">
              <span>图片比例：</span>
              <el-tooltip effect="light" content="生成图片的尺寸比例" placement="right">
                <el-icon>
                  <InfoFilled/>
                </el-icon>
              </el-tooltip>
            </div>

            <div class="param-line pt">
              <el-row :gutter="10">
                <el-col :span="8" v-for="item in rates" :key="item.value">
                  <div class="flex-col items-center"
                       :class="item.value === params.rate ? 'grid-content active' : 'grid-content'"
                       @click="changeRate(item)">
                    <!--                    <div :class="'shape ' + item.css"></div>-->
                    <el-image class="icon" :src="item.img" fit="cover"></el-image>
                    <div class="text " style="padding:2px;">{{ item.text }}</div>
                  </div>
                </el-col>
              </el-row>
            </div>

            <div class="param-line hidden">
              <el-form-item label="影响程度">
                <template #default>
                  <div class="form-item-inner">
                    <el-slider v-model.number="params.scale" :max="30" :step="1"
                               style="width: 180px;--el-slider-main-bg-color:#47fff1"/>
                    <el-tooltip effect="light"
                                content="参数用法：scale 影响文本描述的程度
									<br />默认值：10 取值范围[1, 30] "
                                raw-content placement="right">
                      <el-icon>
                        <InfoFilled/>
                      </el-icon>
                    </el-tooltip>
                  </div>
                </template>
              </el-form-item>
            </div>

            <div class="param-line hidden">
              <el-form-item label="随机种子">
                <template #default>
                  <div class="form-item-inner">
                    <el-input v-model.number="params.seed" style="--el-input-focus-border-color:#47fff1"/>
                    <el-tooltip effect="light"
                                content="随机种子：seed，默认值0表示随机产生 <br/>使用相同的种子参数和描述将产生相似的图像"
                                raw-content
                                placement="right">
                      <el-icon>
                        <InfoFilled/>
                      </el-icon>
                    </el-tooltip>
                  </div>
                </template>
              </el-form-item>
            </div>
          </el-form>
        </div>
      </div>
      <div class="task-list-box" @scrollend="handleScrollEnd">
		 
		  <h2>旋风绘图创作中心</h2>
		  
        <div class="task-list-inner" :style="{ height: listBoxHeight + 'px' }">
          <div class="extra-params">
            <el-form>
              <el-tabs v-model="activeName" class="title-tabs" @tabChange="tabChange">
                <el-tab-pane label="文生图" name="txt2img">
                  <div class="prompt-box">
                    <div class="param-line pt">
                      <div class="flex-row justify-between items-center">
                        <div class="flex-row justify-start items-center">
                          <span>提示词：</span>
                          <el-tooltip effect="light" content="输入你想要的内容，用逗号分割" placement="right">
                            <el-icon>
                              <InfoFilled/>
                            </el-icon>
                          </el-tooltip>
                        </div>
                      </div>
                    </div>

                    <div class="param-line pt">
                      <el-input v-model="params.prompt" :autosize="{ minRows: 4, maxRows: 6 }" type="textarea"
                                ref="promptRef"
                                placeholder="请在此输入绘画提示词，我将为您生成图片。"/>
                    </div>

                
                  </div>
                </el-tab-pane>
                
				<el-tab-pane label="图片增强" name="enhance">
				  <div class="text">以某张图片为底稿来进行效果增强，支持 PNG 和 JPG 格式图片；</div>
				  <div class="img-inline">
				    <div class="img-list-box">
				      <div class="img-item" v-for="imgURL in imgList">
				        <el-image :src="imgURL" fit="cover"/>
				        <el-button type="danger" :icon="Delete" @click="removeUploadImage(imgURL)" circle/>
				      </div>
				
				    </div>
				    <el-upload v-if="imgList.length === 0" class="img-uploader" :auto-upload="true" :show-file-list="false"
				               :http-request="uploadImg" style="--el-color-primary:#47fff1">
				      <el-icon class="uploader-icon">
				        <Plus/>
				      </el-icon>
				    </el-upload>
				  </div>
				</el-tab-pane>
				
				
				<el-tab-pane label="图片修复" name="repair">
				  <div class="text">以某张图片为底稿来进行效果修复操作，支持 PNG 和 JPG 格式图片；</div>
				  <div class="img-inline">
				    <div class="img-list-box">
				      <div class="img-item" v-for="imgURL in imgListRepair">
				        <el-image :src="imgURL" fit="cover"/>
				        <el-button type="danger" :icon="Delete" @click="removeUploadImage(imgURL)" circle/>
				      </div>
				
				    </div>
				    <el-upload  v-if="imgListRepair.length === 0" class="img-uploader" :auto-upload="true" :show-file-list="false"
				               :http-request="uploadImg" style="--el-color-primary:#47fff1">
				      <el-icon class="uploader-icon">
				        <Plus/>
				      </el-icon>
				    </el-upload>
				  </div>
				</el-tab-pane>
				
				
              </el-tabs>
			  

            <!--  <el-row class="text-info hidden">
                <el-tag>每次绘图消耗{{ mjPower }}算力，U/V 操作消耗{{ mjActionPower }}算力</el-tag>
                <el-tag type="success">当前可用算力：{{ power }}</el-tag>
              </el-row> -->

              <div class="submit-btn">
                <el-button color="#47fff1" :dark="false" @click="generate" round v-if="activeName === 'txt2img'">立即生成</el-button>
              </div>
			  
            </el-form>
          </div>

          <div class="job-list-box ">
            <h2 class="hidden"> 任务列表</h2>
            <div class="running-job-list hidden">
              <ItemList :items="runningJobs" v-if="runningJobs.length > 0">
                <template #default="scope">
                  <div class="job-item">
                    <div v-if="scope.item.progress > 0" class="job-item-inner">
                      <el-image :src="scope.item['img_url']" :zoom-rate="1.2"
                                :preview-src-list="[scope.item['img_url']]" fit="cover" :initial-index="0"
                                loading="lazy">
                        <template #placeholder>
                          <div class="image-slot">
                            正在加载图片
                          </div>
                        </template>

                        <template #error>
                          <div class="image-slot">
                            <el-icon>
                              <Picture/>
                            </el-icon>
                          </div>
                        </template>
                      </el-image>

                      <div class="progress">
                        <el-progress type="circle" :percentage="scope.item.progress" :width="100"
                                     color="#47fff1"/>
                      </div>
                    </div>
                    <el-image fit="cover" v-else>
                      <template #error>
                        <div class="image-slot">
                          <i class="iconfont icon-quick-start"></i>
                          <span>任务正在排队中</span>
                        </div>
                      </template>
                    </el-image>
                  </div>
                </template>
              </ItemList>
              <el-empty :image-size="100" v-else/>
            </div>

            <h3>创作记录</h3>
            <div class="finish-job-list" v-loading="loading" element-loading-background="rgba(0, 0, 0, 0.5)">
              <div class="generating-status" v-if="isGenerating">
                <div class="image-slot " style="text-align: center;width: 260px;">
                  <img src="/images/loading.gif" alt="loading" style="width: 32px; height: 32px;" />
                  <br/>
                  <span style="margin-left: 10px; color: #47fff1;">任务正在处理中...</span>
                </div>
              </div>
              
              <div v-if="finishedJobs.length > 0">
                <ItemList :items="finishedJobs" :width="240" :gap="16">
                  <template #default="scope">
                    <div class="job-item">
                      <el-image
                          :src="scope.item['thumb_url']"
                          :class="scope.item['can_opt'] ? '' : 'upscale'" :zoom-rate="1.2"
                          :preview-src-list="[scope.item['img_url']]" fit="cover" :initial-index="scope.index"
                          loading="lazy" v-if="scope.item.progress > 0">
                        <template #placeholder>
                          <div class="image-slot">
                            正在加载图片
                          </div>
                        </template>

                        <template #error>
                          <div class="image-slot" v-if="scope.item['img_url'] === ''">
                            <i class="iconfont icon-loading"></i>
                            <span>正在下载图片</span>
                          </div>
                          <div class="image-slot" v-else>
                            <el-icon>
                              <Picture/>
                            </el-icon>
                          </div>
                        </template>
                      </el-image>

                      <div class="opt" style="margin-top:-2rem;padding-left:0.5rem;" v-if="scope.item['can_opt']">
                        <div class="opt-line">
                          <ul>
                            <li class="show-prompt">

                              <el-popover placement="left" title="提示词" :width="240" trigger="hover">
                                <template #reference>
                                  <el-icon>
                                    <ChromeFilled/>
                                  </el-icon>
                                </template>

                                <template #default>
                                  <div class="mj-list-item-prompt">
                                    <span>{{ scope.item.prompt }}</span>
                                    <el-icon class="copy-prompt-mj"
                                             :data-clipboard-text="scope.item.prompt">
                                      <DocumentCopy/>
                                    </el-icon>
                                  </div>
                                </template>
                              </el-popover>
                            </li>
                          </ul>
                        </div>

                       
                      </div>

                      <div class="remove">
                        <el-button type="danger" :icon="Delete" @click="removeImage(scope.item)" circle/>
                        <el-button type="warning" v-if="scope.item.publish" @click="publishImage(scope.item, false)"
                                   circle>
                          <i class="iconfont icon-cancel-share"></i>
                        </el-button>
                        <el-button class="hidden" type="success" v-else @click="publishImage(scope.item, true)" circle>
                          <i class="iconfont icon-share-bold"></i>
                        </el-button>
                      </div>
                    </div>
                  </template>
                </ItemList>
                
                <div class="load-more-container" v-if="!isOver">
                  <el-button 
                    color="#47fff1" 
                    :dark="false" 
                    @click="loadMoreImages" 
                    :loading="loading" 
                    round>
                    加载更多
                  </el-button>
                </div>
                
                <div class="no-more-data" v-if="isOver">
                  <span>没有更多数据了</span>
                  <i class="iconfont icon-face"></i>
                </div>
              </div>
              <el-empty :image-size="100" v-else/>
            </div> <!-- end finish job list-->
          </div>
        </div>

      </div><!-- end task list box -->
    </div>

    <login-dialog :show="showLoginDialog" @hide="showLoginDialog =  false" @success="initData"/>
  </div>
</template>

<script setup>
import {nextTick, onMounted, onUnmounted, ref} from "vue"
import {ChromeFilled, Delete, DocumentCopy, InfoFilled, Picture, Plus, UploadFilled} from "@element-plus/icons-vue";
import Compressor from "compressorjs";
import {httpGet, httpPost} from "@/utils/http";
import {ElMessage, ElMessageBox, ElNotification} from "element-plus";
import ItemList from "@/components/ItemList.vue";
import Clipboard from "clipboard";
import {checkSession} from "@/action/session";
import {useRouter} from "vue-router";
import {getSessionId} from "@/store/session";
import {copyObj, removeArrayItem} from "@/utils/libs";
import LoginDialog from "@/components/LoginDialog.vue";

const listBoxHeight = ref(window.innerHeight - 40)
const mjBoxHeight = ref(window.innerHeight - 150)
const showLoginDialog = ref(false)

window.onresize = () => {
  listBoxHeight.value = window.innerHeight - 40
  mjBoxHeight.value = window.innerHeight - 150
}
const rates = [
  {css: "square", option: {width:"512",height:"512"}, text: "1:1",value: "1:1", img: "/images/mj/rate_1_1.png"},
  {css: "size2-3", option: {width:"341",height:"512"}, text: "2:3",value: "2:3", img: "/images/mj/rate_3_4.png"},
  {css: "size3-2", option: {width:"512",height:"341"}, text: "3:2",value: "3:2", img: "/images/mj/rate_4_3.png"},
  {css: "size3-4", option: {width:"384",height:"512"}, text: "3:4",value: "3:4", img: "/images/mj/rate_3_4.png"},
  {css: "size4-3", option: {width:"512",height:"384"}, text: "4:3",value: "4:3", img: "/images/mj/rate_4_3.png"},
  {css: "size16-9", option: {width:"512",height:"288"}, text: "16:9",value: "16:9", img: "/images/mj/rate_16_9.png"},
  {css: "size9-16", option: {width:"288",height:"512"}, text: "9:16",value: "9:16", img: "/images/mj/rate_9_16.png"},
]
const models = [
  {text: "写实模式MJ-6.0", value: " --v 6", img: "/images/mj/mj-v6.png"},
  {text: "优质模式MJ-5.2", value: " --v 5.2", img: "/images/mj/mj-v5.2.png"},
  {text: "优质模式MJ-5.1", value: " --v 5.1", img: "/images/mj/mj-v5.1.jpg"},
  {text: "虚幻模式MJ-5", value: " --v 5", img: "/images/mj/mj-v5.jpg"},
  {text: "真实模式MJ-4", value: " --v 4", img: "/images/mj/mj-v4.jpg"},
  {text: "动漫风-niji4", value: " --niji 4", img: "/images/mj/nj4.jpg"},
  {text: "动漫风-niji5", value: " --niji 5", img: "/images/mj/mj-niji.png"},
  {text: "动漫风-niji5 可爱", value: " --niji 5 --style cute", img: "/images/mj/nj1.jpg"},
  {text: "动漫风-niji5 风景", value: " --niji 5 --style scenic", img: "/images/mj/nj2.jpg"},
  {text: "动漫风-niji6", value: " --niji 6", img: "/images/mj/nj3.jpg"},

]

const options = [
  {
    value: 0,
    label: '默认'
  },
  {
    value: 0.25,
    label: '普通'
  },
  {
    value: 0.5,
    label: '清晰'
  },
  {
    value: 1,
    label: '高清'
  },
]

const router = useRouter()
const initParams = {
  device_id: "1",
  width: 512,
  height: 288,
  prompt: router.currentRoute.value.params["prompt"] ?? "",
  seed: 0,
  scale: 10,
  logo_info: {
    add_logo: true,
    position: 2,
    language: 1,
    opacity: 0.8,
    logo_text_content: "旋风图片生成大模型算法AI生成-"+generateRandomId()
  }
}
const params = ref(copyObj(initParams))
const baseUrl = "https://api-preview-chatgot-io.test690.com";

// 为不同标签页创建独立的图片列表
const imgList = ref([])  // enhance 标签页使用
const imgListRepair = ref([]) // repair 标签页使用

const activeName = ref('txt2img')

const runningJobs = ref([])
const finishedJobs = ref([])
const isGenerating = ref(false)

const socket = ref(null)
const power = ref(0)
const userId = ref(0)
const isLogin = ref(false)

const heartbeatHandle = ref(null)
const connect = () => {
  let host = process.env.VUE_APP_WS_HOST
  if (host === '') {
    if (location.protocol === 'https:') {
      host = 'wss://' + location.host;
    } else {
      host = 'ws://' + location.host;
    }
  }

  // 心跳函数
  const sendHeartbeat = () => {
    clearTimeout(heartbeatHandle.value)
    new Promise((resolve, reject) => {
      if (socket.value !== null) {
        socket.value.send(JSON.stringify({type: "heartbeat", content: "ping"}))
      }
      resolve("success")
    }).then(() => {
      heartbeatHandle.value = setTimeout(() => sendHeartbeat(), 5000)
    });
  }

  const _socket = new WebSocket(host + `/api/mj/client?user_id=${userId.value}`);
  _socket.addEventListener('open', () => {
    socket.value = _socket;

    // 发送心跳消息
    sendHeartbeat()
  });

  _socket.addEventListener('message', event => {
    if (event.data instanceof Blob) {
      fetchRunningJobs()
      isOver.value = false
      page.value = 1
      fetchFinishJobs(page.value)
    }
  });

  _socket.addEventListener('close', () => {
    if (socket.value !== null) {
      connect()
    }
  });
}

const clipboard = ref(null)
onMounted(() => {
  initData()
  clipboard.value = new Clipboard('.copy-prompt-mj');
  clipboard.value.on('success', () => {
    ElMessage.success("复制成功！");
  })

  clipboard.value.on('error', () => {
    ElMessage.error('复制失败！');
  })
  
  // 初始化时加载LocalStorage中的图片
  fetchFinishJobs(1)
})

onUnmounted(() => {
  socket.value = null
})

// 初始化数据
const initData = () => {
  checkSession().then(user => {
    power.value = user['power']
    userId.value = user.id
    isLogin.value = true

    fetchRunningJobs()
    fetchFinishJobs(1)
    connect()
  }).catch(() => {

  });
}

onUnmounted(() => {
  clipboard.value.destroy()
})

const mjPower = ref(1)
const mjActionPower = ref(1)
httpGet("/api/config/get?key=system").then(res => {
  mjPower.value = res.data["mj_power"]
  mjActionPower.value = res.data["mj_action_power"]
}).catch(e => {
  ElMessage.error("获取系统配置失败：" + e.message)
})

// 获取运行中的任务
const fetchRunningJobs = () => {
  httpGet(`/api/mj/jobs?status=0`).then(res => {
    const jobs = res.data
    const _jobs = []
    for (let i = 0; i < jobs.length; i++) {
      if (jobs[i].progress === -1) {
        ElNotification({
          title: '任务执行失败',
          dangerouslyUseHTMLString: true,
          message: `任务ID：${jobs[i]['task_id']}<br />原因：${jobs[i]['err_msg']}`,
          type: 'error',
          duration: 0,
        })
        if (jobs[i].type === 'image') {
          power.value += mjPower.value
        } else {
          power.value += mjActionPower.value
        }
        continue
      }
      _jobs.push(jobs[i])
    }
    runningJobs.value = _jobs
  }).catch(e => {
    ElMessage.error("获取任务失败：" + e.message)
  })
}

// 添加加载更多图片的方法
const loadMoreImages = () => {
  page.value += 1
  fetchFinishJobs(page.value)
}

// 移除原来的滚动加载函数或保留但不使用
const handleScrollEnd = () => {
  // 不再使用滚动加载
  // 如果想保留滚动加载功能，可以取消下面的注释
  // if (isOver.value === true) {
  //   return
  // }
  // page.value += 1
  // fetchFinishJobs(page.value)
}

const page = ref(1)
const pageSize = ref(15)
const isOver = ref(false)
const loading = ref(false)
const fetchFinishJobs = (page) => {
  loading.value = true
  // 从LocalStorage获取图片列表
  const savedImages = JSON.parse(localStorage.getItem('generatedImages') || '[]')
  const start = (page - 1) * pageSize.value
  const end = start + pageSize.value
  const pageImages = savedImages.slice(start, end)
  
  if (pageImages.length < pageSize.value) {
    isOver.value = true
  }
  
  if (page === 1) {
    finishedJobs.value = pageImages
  } else {
    finishedJobs.value = finishedJobs.value.concat(pageImages)
  }
  
  nextTick(() => loading.value = false)
}

// 切换图片比例
const changeRate = (item) => {
  params.value.rate = item.value
  // 根据选择的比例设置对应的宽高
  const selectedRate = rates.find(rate => rate.value === item.value)
  if (selectedRate) {
    params.value.width = parseInt(selectedRate.option.width)
    params.value.height = parseInt(selectedRate.option.height)
  }
}
// 切换模型
const changeModel = (item) => {
  params.value.model = item.value
}

const imgKey = ref("")
const beforeUpload = (key) => {
  imgKey.value = key
}

// 添加水印到图片
const addWatermark = (imageUrl) => {
  // 创建一个新的图片对象
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.crossOrigin = "anonymous"; // 允许跨域获取图片
    
    img.onload = () => {
      // 创建canvas
      const canvas = document.createElement('canvas');
      const ctx = canvas.getContext('2d');
      
      // 设置canvas大小与图片一致
      canvas.width = img.width;
      canvas.height = img.height;
      
      // 先在canvas上绘制图片
      ctx.drawImage(img, 0, 0);
      
      // 生成水印文本
      const watermarkText = "旋风图片生成大模型算法AI生成-" + generateRandomId();
      
      // 设置水印样式
      ctx.font = '16px Arial';
      ctx.fillStyle = `rgba(255, 255, 255, ${params.value.logo_info.opacity})`;
      ctx.globalAlpha = params.value.logo_info.opacity;
      
      // 在左上角位置绘制水印
      ctx.fillText(watermarkText, 10, 30);
      
      // 转换为base64或blob供后续使用
      const watermarkedImage = canvas.toDataURL('image/jpeg');
      resolve(watermarkedImage);
    };
    
    img.onerror = (err) => {
      console.error('加载图片失败:', err);
      reject(err);
    };
    
    img.src = imageUrl;
  });
};

// 上传带水印的图片到服务器
const uploadWatermarkedImage = async (imageUrl) => {
  try {
    // 添加水印
    const watermarkedImage = await addWatermark(imageUrl);
    
    // 将base64转换为blob
    const response = await fetch(watermarkedImage);
    const blob = await response.blob();
    
    // 创建FormData对象上传
    const formData = new FormData();
    formData.append('file', blob, 'watermarked-image.jpg');
    
    // 上传到您的服务器
    const uploadResponse = await fetch('/api/upload', {
      method: 'POST',
      body: formData
    });
    
    const result = await uploadResponse.json();
    return result.url; // 返回上传后的URL
  } catch (error) {
    console.error('处理水印图片失败:', error);
    throw error;
  }
};

// 修改上传图片函数
const uploadImg = (file) => {
  if (!isLogin.value) {
    showLoginDialog.value = true
    return
  }

  // 根据当前活动的标签页选择对应的图片列表
  const currentImgList = activeName.value === 'enhance' ? imgList.value : imgListRepair.value;

  // 确保只能上传一张图片
  if (currentImgList.length >= 1) {
    ElMessage.warning('只能上传一张图片')
    return
  }
  isGenerating.value = true

  const tempUrl = URL.createObjectURL(file.file);
  
  if (activeName.value === 'enhance') {
    imgList.value.push(tempUrl);
  } else if (activeName.value === 'repair') {
    imgListRepair.value.push(tempUrl);
  }
  
  // 压缩图片
  new Compressor(file.file, {
    quality: 0.6,
    success(result) {
      // 创建一个 FileReader 来读取压缩后的图片
      const reader = new FileReader();
      reader.onload = function(e) {
        const img = new Image();
        img.onload = function() {
          // 创建 canvas 来添加水印
          const canvas = document.createElement('canvas');
          const ctx = canvas.getContext('2d');
          
          // 设置 canvas 大小与图片一致
          canvas.width = img.width;
          canvas.height = img.height;
          
          // 绘制原图
          ctx.drawImage(img, 0, 0);
          
          // 添加水印
          const watermarkText = "旋风图片生成大模型算法AI生成-" + generateRandomId();
          ctx.font = '16px Arial';
          ctx.fillStyle = `rgba(255, 255, 255, ${params.value.logo_info.opacity})`;
          ctx.globalAlpha = params.value.logo_info.opacity;
          ctx.fillText(watermarkText, 10, 30);
          
          // 转换为 Blob
          canvas.toBlob(function(watermarkedBlob) {
            // 创建带有水印的文件对象
            const watermarkedFile = new File([watermarkedBlob], result.name, {
              type: 'image/jpeg',
              lastModified: new Date().getTime()
            });
            
            // 创建 FormData，使用带水印的图片
            const formData = new FormData();
            formData.append('file', watermarkedFile, result.name);
            formData.append('device_id', getSessionId()+"22");
            
            let Gurl = "/api/v1/xuanfeng/definition-enhance";
            if (activeName.value == "repair") {
                Gurl = "/api/v1/xuanfeng/color-enhance";
            }
            
			
            // 发送带水印的图片到服务器
            fetch(baseUrl + Gurl, {
              method: 'POST',
              body: formData,
              mode: 'cors',
              credentials: 'omit'
            })
            .then(response => response.json())
            .then(res => {
              if (res.code === 0) {  // 检查返回状态码
                const imageUrl = res.data.url;
                
                // 这里直接创建一个 Data URL，保存带水印的图片
                const watermarkedUrl = canvas.toDataURL('image/jpeg');
                
                if (imgKey.value === '') {
                  // 添加到上传图片列表
                  // imgList.value.push(watermarkedUrl);
                  
                  // 保存到本地缓存
                  const savedImages = JSON.parse(localStorage.getItem('generatedImages') || '[]');
                  const newImage = {
                    id: res.data.id || Date.now(),
                    img_url: watermarkedUrl,
                    thumb_url: watermarkedUrl, // 使用水印图片作为缩略图
                    progress: 100,
                    can_opt: true,
                    publish: false
                  };
                  savedImages.unshift(newImage);
                  if (savedImages.length > 15) {
                    savedImages.pop(); 
                  }
				  
                  
                  localStorage.setItem('generatedImages', JSON.stringify(savedImages));
                  
                  // 更新显示列表
                  finishedJobs.value = savedImages;
                } else {
                  // 单张图片上传的情况
                  params.value[imgKey.value] = watermarkedUrl;
                  imgKey.value = '';
                }
                
                ElMessage.success('处理成功');
              } else {
				  // 特殊处理错误码 40006
				  if (res.code === 40006) {
				    ElMessage.error("今天额度已用完，请明天再试")
				  }else if(res.code === 40004) {
				    ElMessage.error("无法生成图片")
				  } else {
				    ElMessage.error(res.message || "任务推送失败")
				  }
				  
              }
			  isGenerating.value = false
            })
            .catch((e) => {
              ElMessage.error('处理失败: ' + (e.message || '网络错误'));
			  isGenerating.value = false
            });
          }, 'image/jpeg', 0.9); // 设置 JPEG 质量为 0.9
        };
        
        img.onerror = function() {
          ElMessage.error('图片加载失败');
        };
        
        img.src = e.target.result;
      };
      
      reader.onerror = function() {
        ElMessage.error('读取压缩后的图片失败');
      };
      
      reader.readAsDataURL(result);
    },
    error(err) {
      console.log(err.message);
      ElMessage.error('图片压缩失败: ' + err.message);
    },
  });
  
};

// 修改生成图片函数
const promptRef = ref(null)
const generate = () => {
  if (!isLogin.value) {
    showLoginDialog.value = true
    return
  }

  if (params.value.prompt === '') {
    promptRef.value.focus()
    return ElMessage.error("请输入绘画提示词！")
  }
  
  if (imgList.value.length !== 2 && params.value.task_type === "swapFace") {
    return ElMessage.error("换脸操作需要上传两张图片")
  }
  params.value.device_id = getSessionId()+"22"
  
  params.value.user_id   = userId.value
  Reflect.deleteProperty(params.value, "rate")
  
  isGenerating.value = true
  
  fetch(baseUrl + "/api/v1/xuanfeng/text2img", {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(params.value),
    mode: 'cors',
    credentials: 'omit'
  }).then(response => response.json())
    .then(res => {
      isGenerating.value = false
      if (res.code === 0 && res.data.status === "done") {
        // 为图片添加水印
        addWatermark(res.data.imageUrls[0]).then(watermarkedImage => {
          // 保存图片到LocalStorage
          const savedImages = JSON.parse(localStorage.getItem('generatedImages') || '[]')
          const newImage = {
            id: res.data.id || Date.now(),
            prompt: params.value.prompt,
            img_url: watermarkedImage,
            thumb_url: watermarkedImage, // 使用水印图片作为缩略图
            progress: 100,
            can_opt: true,
            publish: false
          }
          savedImages.unshift(newImage)
    
          if (savedImages.length > 15) {
            savedImages.pop(); // 移除数组最后一项（最老的记录）
          }
		  
          
          localStorage.setItem('generatedImages', JSON.stringify(savedImages))
          
          // 更新finishedJobs
          finishedJobs.value = savedImages
          
          ElMessage.success("绘画完成！")
          power.value -= mjPower.value
          params.value = copyObj(initParams)
          imgList.value = []
        }).catch(err => {
		  console.log(ElMessage.error(err.message))
          ElMessage.error('图片无法生成');
        });
      } else {
        // 特殊处理错误码 40006
        if (res.code === 40006) {
          ElMessage.error("今天额度已用完，请明天再试")
        }else if(res.code === 40004) {
          ElMessage.error("无法生成图片")
        } else {
          ElMessage.error(res.message || "任务推送失败")
        }
      }
    })
    .catch(e => {
      isGenerating.value = false
      ElMessage.error("任务推送失败：" + e.message)
    })
}


// 图片放大任务
const upscale = (index, item) => {
  send('/api/mj/upscale', index, item)
}

// 图片变换任务
const variation = (index, item) => {
  send('/api/mj/variation', index, item)
}

const send = (url, index, item) => {
  httpPost(url, {
    index: index,
    channel_id: item.channel_id,
    message_id: item.message_id,
    message_hash: item.hash,
    session_id: getSessionId(),
    prompt: item.prompt,
  }).then(() => {
    ElMessage.success("任务推送成功，请耐心等待任务执行...")
    power.value -= mjActionPower.value
  }).catch(e => {
    ElMessage.error("任务推送失败：" + e.message)
  })
}

const removeImage = (item) => {
  ElMessageBox.confirm(
      '此操作将会删除图片，继续操作吗?',
      '删除提示',
      {
        confirmButtonText: '确认',
        cancelButtonText: '取消',
        type: 'warning',
      }
  ).then(() => {
    // 从LocalStorage中删除图片
    const savedImages = JSON.parse(localStorage.getItem('generatedImages') || '[]')
    const newImages = savedImages.filter(img => img.id !== item.id)
    localStorage.setItem('generatedImages', JSON.stringify(newImages))
    
    // 更新finishedJobs
    finishedJobs.value = newImages
    
    ElMessage.success("图片删除成功")
  }).catch(() => {
  })
}

// 发布图片到作品墙
const publishImage = (item, action) => {
  let text = "图片发布"
  if (action === false) {
    text = "取消发布"
  }
  
  // 更新LocalStorage中的发布状态
  const savedImages = JSON.parse(localStorage.getItem('generatedImages') || '[]')
  const newImages = savedImages.map(img => {
    if (img.id === item.id) {
      return { ...img, publish: action }
    }
    return img
  })
  localStorage.setItem('generatedImages', JSON.stringify(newImages))
  
  // 更新finishedJobs
  finishedJobs.value = newImages
  
  ElMessage.success(text + "成功")
  item.publish = action
}

// 切换菜单
const tabChange = (tab) => {
  if (tab === "txt2img" || tab === "img2img" ) {
    params.value.task_type = "image"
  } else {
    params.value.task_type = tab
  }
  
  // 切换标签页时清空当前URL参数，避免错误
  imgKey.value = ""
}

// 删除已上传图片
const removeUploadImage = (url) => {
  // 根据当前活动的标签页选择对应的图片列表
  if (activeName.value === 'enhance') {
    imgList.value = removeArrayItem(imgList.value, url)
  } else if (activeName.value === 'repair') {
    imgListRepair.value = removeArrayItem(imgListRepair.value, url)
  }
}

function generateRandomId() {
  const chars = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz';
  let result = '';
  for (let i = 0; i < 6; i++) {
    result += chars.charAt(Math.floor(Math.random() * chars.length));
  }
  return Date.now().toString(36).substr(-2) + result; // 前2位基于时间
}

</script>

<style lang="stylus">
@import "@/assets/css/image-mj.styl"
@import "@/assets/css/custom-scroll.styl"
body{
	background: #25272d;
}
h2{
    font-weight: 700;
    font-size: 20px;
    color: #47fff1 !important;
}
.hidden{
	display:none;
}
.active{font-weight:500;}
.tip{
	background: #010714;
	border-radius: 10px;
	padding: 20px;
	color: #fff;
}

/* 添加加载更多按钮的样式 */
.load-more-container {
  display: flex;
  justify-content: center;
  margin: 20px 0;
}
</style>
