<template data-edit-id="1">
  <div class="tw-relative tw-px-3 tw-pt-3 wizard-bg tw-flex tw-flex-col tw-items-center tw-w-full tw-h-full" ref="rootComponentContainer" data-edit-id="2">
    <div v-if="(isSolo || content.isSolo) && dataEditable" class="wizard-navbar tw-flex tw-items-center tw-justify-between tw-max-w-7xl tw-mx-auto tw-w-full" data-edit-id="100">
      <div :style="{ width: flag && step !== 0 ? '100%' : '' }" class="tw-flex tw-items-center tw-gap-3 logoInfoArea" data-edit-id="101">
        <h3 class="tw-text-2xl tw-text-gray-900 tw-tracking-tight tw-m-0 tw-font-medium logo-title tw-w-full" data-edit-id="102">
          <slot v-if="step === 0" data-edit-id="292">
            <div class="logo-badge tw-flex tw-items-center tw-justify-center" data-edit-id="409">
              <span class="logo-badge-icon" data-edit-id="410">{{
                icon || "∑"
              }}</span>
            </div>
            <span class="logo-text" data-edit-id="242">
              <span class="title-text" data-edit-id="368">{{ title }}</span>
              <span class="slogn-text" data-edit-id="369">{{ introduce }}</span>
            </span>
          </slot>
          <slot v-else data-edit-id="308">
            <span title="返回" class="goBackBtn" @click="goBack()" data-edit-id="295">
              <el-icon data-edit-id="384">
                <arrow-left-bold data-edit-id="385" />
              </el-icon>
            </span>
            <slot v-if="
                accountData && Object.keys(accountData).length && !isStreaming
              " data-edit-id="357">
              <el-dropdown @command="handleContentCommand" style="vertical-align: middle" :teleported="false" append-to="parent" trigger="click" data-edit-id="400">
                <el-button text type="primary" data-edit-id="312">
                  操作
                  <el-icon class="el-icon--right" data-edit-id="402">
                    <arrow-down data-edit-id="403" />
                  </el-icon>
                </el-button>

                <template #dropdown>
                  <el-dropdown-menu>
                    <el-dropdown-item command="htmlDownload">
                      存档 HTML
                    </el-dropdown-item>

                    <el-dropdown-item command="markdownDownload">
                      存档 Markdown
                    </el-dropdown-item>

                    <el-dropdown-item command="imageDownload">
                      导出图片
                    </el-dropdown-item>

                    <el-dropdown-item command="editContent">
                      {{ viewMode ? "查看内容" : "手动修改" }}
                    </el-dropdown-item>

                    <el-dropdown-item command="copyLink">
                      复制链接
                    </el-dropdown-item>

                    <el-dropdown-item command="readLocal">
                      本地读档
                    </el-dropdown-item>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <slot v-if="viewMode">
                <el-tag class="versionIndexSpan">最近第 {{ currentVersionIndex + 1 }} 个版本
                </el-tag>
                <el-button class="startModifyBtn" size="small" text type="primary" @click="startModify">开始修改</el-button>
              </slot>
              <el-input :style="{ width: flag ? '300px' : '300px' }" v-model="modifyInfo" v-else placeholder="输入变更描述..." class="custom-input-group" clearable @keyup.enter="handleConfirm" data-edit-id="342">
                <template #append>
                  <el-button :loading="modifyLoading" :disabled="modifyLoading" style="color: #fff" class="!bg-blue-500 !border-blue-500 !text-white hover:!bg-blue-600 transition-all duration-300 wizard-action-btn wizard-action-btn--primary" @click="handleConfirm">
                    <span class="font-medium px-2">
                      <i class="bi bi-magic"></i>
                    </span>
                  </el-button>
                </template>
              </el-input>
            </slot>
            <span v-else style="font-size: 14px"> {{ title }}</span>
            <span class="versionListArea" style="float: right; margin-right: 10px" v-if="versionList && versionList.length !== 0 && !isStreaming">
              <el-dropdown @command="handleVersionChange" trigger="click" :teleported="false" append-to="parent" style="vertical-align: middle" v-if="step === 1" data-edit-id="400">
                <el-button text type="primary" data-edit-id="312">
                  版本
                  <el-icon class="el-icon--right" data-edit-id="402">
                    <arrow-down data-edit-id="403" />
                  </el-icon>
                </el-button>
                <template #dropdown>
                  <el-dropdown-menu>
                    <slot v-for="(item, index) in versionList">
                      <el-dropdown-item :style="{
                          fontWeight: index === currentVersionIndex ? 800 : 300,
                        }" :command="index" class="tw-justify-between">
                        <slot v-if="index === currentVersionIndex">
                          {{ getVersionLabel(item) }}
                        </slot>
                        <slot v-else>
                          {{ getVersionLabel(item) }}
                          <el-button style="margin-left: 5px" @click.stop="checkDiff(index)" size="small">对比</el-button>
                        </slot>
                      </el-dropdown-item>
                    </slot>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <span v-if="!flag" class="versionLabel">{{
                getVersionLabel(versionList[currentVersionIndex])
              }}</span>
            </span>
          </slot>
        </h3>
      </div>
      <div class="navbar-actions" data-edit-id="103">
        <slot v-if="step === 0 && !flag">
          <el-button class="wizard-action-btn navbar-action-btn" @click="openFeedbackDialog()">
            问题反馈
          </el-button>
        </slot>
        <span class="navbar-divider" aria-hidden="true"></span>
        <div class="navbar-account">
          <div appName="loginRegItem" component="CardItem" :documentId="query ? query.documentId : ''" class="login-bar-card refined-user-card" data-edit-id="104"></div>
        </div>
      </div>
    </div>

    <div ref="loadingEffectArea" style="height: 100%; position: relative; z-index: 0" class="tw-max-w-7xl tw-y-full tw-mx-auto tw-w-full" data-edit-id="3">
      <!-- 输入控制面板 -->
      <div v-if="step === 0" class="wizard-input-panel tw-relative tw-bg-white tw-border tw-border-slate-100 tw-rounded-2xl tw-shadow-sm tw-p-5 tw-transition-all hover:tw-shadow-md" data-edit-id="4">
        <div class="tw-flex tw-flex-col md:tw-flex-row tw-gap-5 tw-items-stretch">
          <div class="tw-flex-1 tw-flex tw-flex-col tw-justify-between tw-min-w-0">
            <div class="input-spell-box tw-w-full" data-edit-id="122">
              <el-input :placeholder="inputPlaceholder" :rows="4" resize="none" v-model="content.inputText" type="textarea" class="feature-input tw-w-full" data-edit-id="131"></el-input>
            </div>

            <div class="tw-flex tw-items-center tw-flex-wrap tw-gap-2 tw-mt-4">
              <span v-if="!flag" class="tw-text-sm tw-text-slate-400 tw-font-medium tw-whitespace-nowrap">
                试试看：
              </span>
              <button v-for="d in exampleList" :key="d.title || d.name" @click="inputExampleText(d)" class="example-chip tw-group tw-flex tw-items-center tw-gap-1.5 tw-px-3 tw-py-1.5 tw-text-[13px] tw-bg-slate-50 hover:tw-bg-blue-50 hover:tw-text-blue-600 tw-text-slate-600 tw-rounded-lg tw-transition-colors tw-whitespace-nowrap active:tw-scale-95">
                <span class="chip-icon">{{ d.icon || "" }}</span>
                <span>{{ d.name }}</span>
              </button>
            </div>
          </div>

          <div class="tw-h-px md:tw-hidden tw-bg-slate-100 tw-w-full"></div>
          <div class="tw-hidden md:tw-block tw-w-px tw-bg-slate-100 tw-my-1 tw-self-stretch"></div>

          <div class="aigcOptionArea tw-flex tw-flex-row md:tw-flex-col tw-justify-between md:tw-justify-end tw-gap-3 tw-w-full md:tw-w-32 tw-shrink-0">
            <el-button :style="{ marginTop: flag ? '5px' : '' }" type="primary" :disabled="!content.inputText && !description" :loading="loading" @click="handleGenerate" class="cast-spell-btn tw-flex-1 md:tw-flex-none tw-mt-6 tw-h-10 tw-shadow-sm" data-edit-id="11">
              <i class="bi bi-lightning-charge-fill tw-mr-1.5" data-edit-id="12"></i>
              立即生成
            </el-button>

            <el-button v-if="!flag" text type="info" :loading="readingFile" :disabled="readingFile" @click="triggerFileInput" class="tw-flex-1 md:tw-flex-none tw-justify-center tw-h-10 hover:!tw-bg-slate-50 tw-text-slate-500 tw-mt-8">
              <i class="bi bi-folder2-open tw-mr-1"></i>
              本地读档
            </el-button>
          </div>
        </div>
      </div>
      <span v-if="!(isSolo || content.isSolo) && step === 1 && dataEditable" style="
          float: right;
          position: relative;
          width: 100%;
          text-align: right;
          margin-top: 10px;
          margin-bottom: 3px;
          right: 0px;
          display: inline-block;
        " data-edit-id="357">
        <span title="返回" class="goBackBtn" @click="goBack()" data-edit-id="295">
          <el-icon data-edit-id="384">
            <arrow-left-bold data-edit-id="385" />
          </el-icon>
        </span>
        <span class="versionListArea" style="float: left" v-if="versionList && versionList.length !== 0 && !isStreaming">
          <el-dropdown @command="handleVersionChange" trigger="click" :teleported="false" append-to="parent" v-if="step === 1" data-edit-id="400">
            <el-button text type="primary" data-edit-id="312">
              版本
              <el-icon class="el-icon--right" data-edit-id="402">
                <arrow-down data-edit-id="403" />
              </el-icon>
            </el-button>
            <template #dropdown>
              <el-dropdown-menu>
                <slot v-for="(item, index) in versionList">
                  <el-dropdown-item class="tw-justify-between" :command="index" :style="{
                      fontWeight: index === currentVersionIndex ? 800 : 300,
                    }">
                    <slot v-if="index === currentVersionIndex">
                      {{ getVersionLabel(item) }}
                    </slot>
                    <slot v-else>
                      {{ getVersionLabel(item) }}
                      <el-button style="margin-left: 5px" @click.stop="checkDiff(index)" size="small">对比</el-button>
                    </slot>
                  </el-dropdown-item>
                </slot>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <span v-if="!flag" class="versionLabel">{{
            getVersionLabel(versionList[currentVersionIndex])
          }}</span>
        </span>
        <slot v-if="step === 1 && !isStreaming">
          <el-dropdown @command="handleContentCommand" trigger="click" :teleported="false" append-to="parent" data-edit-id="400">
            <el-button text type="primary" data-edit-id="312">
              操作
              <el-icon class="el-icon--right" data-edit-id="402">
                <arrow-down data-edit-id="403" />
              </el-icon>
            </el-button>

            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item command="htmlDownload">
                  存档 HTML
                </el-dropdown-item>

                <el-dropdown-item command="markdownDownload">
                  存档 Markdown
                </el-dropdown-item>

                <el-dropdown-item command="imageDownload">
                  导出图片
                </el-dropdown-item>

                <el-dropdown-item command="editContent">
                  {{ viewMode ? "查看内容" : "手动修改" }}
                </el-dropdown-item>

                <el-dropdown-item command="copyLink">
                  复制链接
                </el-dropdown-item>

                <el-dropdown-item command="readLocal">
                  本地读档
                </el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
          <slot v-if="viewMode">
            <el-tag class="versionIndexSpan">最近第 {{ currentVersionIndex + 1 }} 个版本
            </el-tag>
            <el-button class="startModifyBtn" text type="primary" size="small" @click="startModify">开始修改</el-button>
          </slot>
          <el-input v-else :style="{ width: flag ? '260px' : '400px' }" v-model="modifyInfo" placeholder="输入变更描述..." class="custom-input-group" clearable @keyup.enter="handleConfirm" data-edit-id="342">
            <template #append>
              <el-button :loading="modifyLoading" :disabled="modifyLoading" style="color: #fff" class="!bg-blue-500 !border-blue-500 !text-white hover:!bg-blue-600 transition-all duration-300 wizard-action-btn wizard-action-btn--primary" @click="handleConfirm">
                <span class="font-medium px-2"><i class="bi bi-magic"></i></span>
              </el-button>
            </template>
          </el-input>
        </slot>
      </span>
      <!-- 结果展示面板 -->
      <div class="wizard-result-panel tw-overflow-hidden" :style="{
          height: resultPanelHeight,
          'margin-top': '0px',
          zIndex: 0,
        }" data-edit-id="110">
        <div v-if="step === 1" data-edit-id="117" class="resultComponentArea tw-w-full tw-relative" ref="resultComponentItem"></div>
        <div v-else class="tw-flex-1 tw-w-full tw-text-center tw-py-20 tw-flex tw-flex-col tw-items-center" data-edit-id="112">
          <div class="waiting-rune tw-mb-4" data-edit-id="118">
            <!-- 外环 -->
            <div class="rune-ring rune-ring--outer" data-edit-id="276"></div>
            <!-- 内环 -->
            <div class="rune-ring rune-ring--inner" data-edit-id="277"></div>
            <!-- 中心图标 -->
            <el-icon :size="32" class="rune-icon" data-edit-id="119">
              <i class="bi bi-hourglass-split"></i>
            </el-icon>
          </div>
          <p class="waiting-tips" data-edit-id="121">{{ currentTips }}</p>
          <!-- 装饰性点 -->
          <div class="magic-dots" data-edit-id="281">
            <span data-edit-id="282"></span><span data-edit-id="283"></span><span data-edit-id="284"></span>
          </div>
        </div>
      </div>
    </div>
    <el-dialog v-model="feedbackDialog.visible" title="反馈与建议" width="500" draggable>
      <el-input type="textarea" :placeholder="feedbackDialog.placeholder" v-model="feedbackDialog.message" resize="none"></el-input>
      <template #footer>
        <div class="dialog-footer">
          <el-button @click="feedbackDialog.visible = false">取消</el-button>
          <el-button type="primary" @click="confirmSendFeedback()">
            确认
          </el-button>
        </div>
      </template>
    </el-dialog>
    <input ref="hiddenFileInput" type="file" :accept="myAccept" class="tw-hidden" @change="onFileSelected($event)" data-edit-id="109" />
  </div>
</template>

<style scoped>
  /* ============================================
   RESET
   ============================================ */
  .el-card {
    border: none;
    box-shadow: none;
  }

  /* ============================================
   DESIGN TOKENS — 学术 · 精准 · 启发
   ============================================ */
  * {
    /* 主色：活力蓝（学术蓝） */
    --academic-primary: #0066CC;
    --academic-primary-hover: #0052A3;
    --academic-primary-active: #004C99;
    --academic-primary-soft: rgba(0, 102, 204, 0.08);
    --academic-primary-ring: rgba(0, 102, 204, 0.16);

    /* 背景：冰川灰 */
    --academic-bg: #EEF3F0;
    --academic-bg-deep: #F6F9F7;

    /* 文字：深石板青 */
    --academic-text: #1e293b;
    --academic-text-secondary: #475569;
    --academic-text-tertiary: #94a3b8;

    /* 强调：琥珀金 */
    --academic-accent: #f59e0b;
    --academic-accent-soft: rgba(245, 158, 11, 0.12);

    /* 辅助 */
    --academic-border: rgba(30, 41, 59, 0.08);
    --academic-border-strong: rgba(30, 41, 59, 0.14);

    /* 玻璃面板 */
    --glass-bg: rgba(255, 255, 255, 0.72);
    --glass-shadow: 0 1px 2px rgba(30, 41, 59, 0.04),
      0 8px 24px rgba(30, 41, 59, 0.06);
    --glass-shadow-hover: 0 2px 4px rgba(30, 41, 59, 0.05),
      0 12px 32px rgba(30, 41, 59, 0.09);

    /* 圆角 */
    --radius-card: 16px;
    --radius-control: 10px;

    --font-system: "Inter", -apple-system, BlinkMacSystemFont, "Segoe UI",
      "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", Arial, sans-serif;
    --font-mono: "JetBrains Mono", "Fira Code", "SF Mono", Menlo, "Cascadia Code",
      Consolas, monospace;
  }

  /* ============================================
   全局背景 — 冰川灰 + 学术点阵纹理
   ============================================ */
  .wizard-bg {
    font-family: var(--font-system);
    color: var(--academic-text);
    background-color: var(--academic-bg);
    background-image: radial-gradient(ellipse 80% 60% at 20% 0%,
        rgba(0, 102, 204, 0.06) 0%,
        transparent 55%),
      radial-gradient(ellipse 60% 50% at 100% 100%,
        rgba(245, 158, 11, 0.04) 0%,
        transparent 55%),
      radial-gradient(circle at 1px 1px,
        rgba(30, 41, 59, 0.07) 1px,
        transparent 0);
    background-size: auto, auto, 22px 22px;
    background-position: 0 0, 0 0, 0 0;
    position: relative;
  }

  .wizard-bg::before {
    content: "";
    position: absolute;
    inset: 0;
    background-image: linear-gradient(to right,
        rgba(0, 102, 204, 0.035) 1px,
        transparent 1px),
      linear-gradient(to bottom, rgba(0, 102, 204, 0.035) 1px, transparent 1px);
    background-size: 88px 88px;
    pointer-events: none;
    z-index: 0;
  }

  /* ============================================
   导航栏
   ============================================ */
  .wizard-navbar {
    position: relative;
    z-index: 1000;
    gap: 20px;
    padding: 10px 4px 18px;
    box-sizing: border-box;
  }

  .versionIndexSpan {
    font-size: 14px;
    font-weight: 900;
  }

  .wizard-navbar::after {
    content: "";
    position: absolute;
    bottom: -1px;
    left: 0;
    right: 0;
    height: 1px;
    background: linear-gradient(90deg,
        transparent 0%,
        rgba(0, 102, 204, 0.22) 30%,
        rgba(0, 102, 204, 0.22) 70%,
        transparent 100%);
  }

  /* 右侧操作区：按钮与账号区共用同一条垂直中心线 */
  .navbar-actions {
    display: flex;
    align-items: center;
    gap: 12px;
    flex: 0 0 auto;
  }

  .navbar-action-btn {
    height: 40px;
    padding: 0 16px;
  }

  .navbar-action-btn:focus-visible {
    outline: 2px solid var(--academic-primary-ring);
    outline-offset: 2px;
  }

  .navbar-divider {
    flex: 0 0 auto;
    width: 1px;
    height: 22px;
    background: var(--academic-border-strong);
    border-radius: 1px;
  }

  /* 登录 / 用户区：容纳 loginBar 44px 的切换按钮并垂直居中 */
  .navbar-account {
    display: flex;
    align-items: center;
    justify-content: flex-end;
    flex: 0 0 auto;
    width: 135px;
    height: 44px;
  }

  /* loginBar 由 CardItem 渲染，尺寸跟随账号区 */
  .login-bar-card {
    width: 100%;
    height: 100%;
  }

  .logoInfoArea {
    flex: 1 1 auto;
    min-width: 0;
  }

  .logo-title {
    display: flex;
    align-items: center;
    gap: 12px;
    min-width: 0;
    font-family: var(--font-system);
  }

  .logo-badge {
    flex: 0 0 auto;
    width: 40px;
    height: 40px;
    border-radius: 12px;
    background: linear-gradient(135deg, var(--academic-accent) 0%, #fbbf24 100%);
    color: #fff;
    box-shadow: 0 2px 8px rgba(245, 158, 11, 0.26),
      inset 0 1px 0 rgba(255, 255, 255, 0.34);
  }

  .logo-badge-icon {
    font-size: 20px;
    font-weight: 700;
    line-height: 1;
  }

  .refined-logo {
    width: 56px;
    height: 56px;
    border-radius: 14px;
    border: 1px solid var(--academic-border);
    background: #fff;
    box-shadow: 0 1px 2px rgba(30, 41, 59, 0.06),
      0 4px 12px rgba(0, 102, 204, 0.08);
    display: inline-block;
    transition: transform 0.25s ease, box-shadow 0.25s ease;
    overflow: hidden;
  }

  .refined-logo:hover {
    transform: translateY(-1px);
    box-shadow: 0 2px 4px rgba(30, 41, 59, 0.08),
      0 8px 20px rgba(0, 102, 204, 0.14);
  }

  .logo-text {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 3px;
    flex: 1 1 auto;
    min-width: 0;
    color: var(--academic-text);
    letter-spacing: -0.01em;
    line-height: 1.3;
  }

  .upload-btn {
    background: #fff !important;
    border: 1px solid var(--academic-border-strong) !important;
    color: var(--academic-text) !important;
    border-radius: 10px !important;
    font-size: 13px !important;
    transition: all 0.18s ease;
  }

  .upload-btn:hover {
    border-color: var(--academic-primary) !important;
    color: var(--academic-primary) !important;
    box-shadow: 0 3px 10px rgba(0, 102, 204, 0.14);
    transform: translateY(-1px);
  }

  .title-text {
    font-size: 16px;
    font-weight: 600;
    line-height: 1.35;
    color: var(--academic-text);
    letter-spacing: 0.01em;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .slogn-text {
    font-size: 12px;
    font-weight: 400;
    line-height: 1.4;
    color: var(--academic-text-secondary);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .refined-user-card {
    opacity: 1 !important;
  }

  .el-dropdown-menu__item {
    font-weight: 800;
  }

  /* ============================================
   面板 — 卡片式架构（2xl 圆角 + 磨砂玻璃）
   ============================================ */
  .wizard-input-panel {
    margin-top: -5px;
    margin-bottom: 5px;
  }

  .wizard-input-panel,
  .wizard-result-panel {
    position: relative;
    background: var(--glass-bg);
    border: 1px solid var(--academic-border);
    border-radius: var(--radius-card);
    box-shadow: var(--glass-shadow);
    backdrop-filter: saturate(160%) blur(18px);
    -webkit-backdrop-filter: saturate(160%) blur(18px);
    overflow: hidden;
    transition: box-shadow 0.25s ease, border-color 0.25s ease;
    z-index: 1;
  }

  .wizard-result-panel {
    position: relative;
    width: 100%;
    border-radius: var(--radius-card);
    overflow: hidden;
    transition: box-shadow 0.25s ease, border-color 0.25s ease;
    z-index: 1;
  }

  .wizard-input-panel:hover {
    box-shadow: var(--glass-shadow-hover);
  }

  /* 面板顶部细线强调 —— 学术精准感 */
  .wizard-input-panel::before {
    content: "";
    position: absolute;
    top: 0;
    left: 24px;
    right: 24px;
    height: 2px;
    background: linear-gradient(90deg,
        transparent 0%,
        var(--academic-primary) 20%,
        var(--academic-primary) 80%,
        transparent 100%);
    opacity: 0.55;
    border-radius: 2px;
  }

  .panel-glow-strip--result {
    background: linear-gradient(90deg,
        transparent 0%,
        rgba(0, 102, 204, 0.5) 25%,
        rgba(0, 102, 204, 0.55) 55%,
        rgba(245, 158, 11, 0.35) 80%,
        transparent 100%);
    background-size: 200% 100%;
  }

  @keyframes shimmerStrip {
    0% {
      background-position: 0% 0;
    }

    100% {
      background-position: 200% 0;
    }
  }

  /* ============================================
   上传区域（保留样式）
   ============================================ */
  .upload-zone {
    width: 80px;
    height: 80px;
    border: 1.5px dashed rgba(0, 102, 204, 0.35);
    border-radius: var(--radius-control);
    background: rgba(0, 102, 204, 0.04);
    cursor: pointer;
    transition: all 0.2s cubic-bezier(0.34, 1.3, 0.64, 1);
    position: relative;
    overflow: hidden;
  }

  .upload-zone::before {
    content: "";
    position: absolute;
    inset: 0;
    background: radial-gradient(circle at 50% 0%,
        rgba(0, 102, 204, 0.1),
        transparent 70%);
    opacity: 0;
    transition: opacity 0.2s ease;
  }

  .upload-zone:hover {
    border-color: var(--academic-primary);
    border-style: solid;
    background: rgba(0, 102, 204, 0.07);
    transform: translateY(-1px);
    box-shadow: 0 4px 14px rgba(0, 102, 204, 0.16);
  }

  .upload-zone:hover::before {
    opacity: 1;
  }

  .upload-zone__icon {
    color: var(--academic-primary) !important;
    transition: transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  .upload-zone:hover .upload-zone__icon {
    transform: scale(1.15) rotate(90deg);
  }

  .upload-zone__label {
    font-size: 11px;
    margin-top: 6px;
    color: var(--academic-primary);
    font-weight: 500;
    letter-spacing: 0.01em;
    font-family: var(--font-system);
  }

  .selected-image-wrap {
    display: inline-block;
  }

  .preview-thumbnail {
    width: 80px !important;
    height: 80px !important;
    border-radius: var(--radius-control) !important;
    border: 1px solid var(--academic-border) !important;
    box-shadow: 0 2px 8px rgba(30, 41, 59, 0.08), 0 1px 2px rgba(30, 41, 59, 0.04) !important;
    transition: all 0.2s ease;
  }

  .preview-thumbnail:hover {
    border-color: var(--academic-primary) !important;
    box-shadow: 0 4px 16px rgba(0, 102, 204, 0.18),
      0 1px 4px rgba(30, 41, 59, 0.06) !important;
    transform: translateY(-1px);
  }

  .removePictureBtn {
    position: absolute;
    background: #ffffff !important;
    border: 1px solid var(--academic-border-strong) !important;
    color: #dc2626 !important;
    right: -9px;
    top: -9px;
    z-index: 3;
    display: inline-block;
    box-shadow: 0 2px 6px rgba(30, 41, 59, 0.12),
      0 0 0 0.5px rgba(255, 255, 255, 0.8) inset !important;
    transition: all 0.18s ease !important;
  }

  .removePictureBtn:hover {
    background: #fee2e2 !important;
    border-color: rgba(220, 38, 38, 0.4) !important;
    box-shadow: 0 3px 10px rgba(220, 38, 38, 0.22) !important;
    transform: scale(1.08);
  }

  .el-popper {
    z-index: 10000 !important;
  }

  .versionLabel {
    font-size: 12px;
    color: #999;
    vertical-align: middle;
  }

  .startModifyBtn {
    margin-left: 12px;
  }

  /* ============================================
   文本输入区 — 对话驱动
   ============================================ */
  .input-spell-box {
    background: #ffffff;
    border: 1px solid var(--academic-border-strong);
    border-radius: var(--radius-control);
    padding: 2px;
    transition: all 0.2s ease;
    box-shadow: 0 1px 2px rgba(30, 41, 59, 0.03) inset,
      0 1px 2px rgba(255, 255, 255, 0.8);
  }

  .input-spell-box:focus-within {
    border-color: var(--academic-primary);
    box-shadow: 0 0 0 3px var(--academic-primary-ring),
      0 1px 2px rgba(30, 41, 59, 0.03) inset;
  }

  .feature-input .el-textarea__inner {
    height: 70px;
    padding: 12px 14px;
    background: transparent !important;
    color: var(--academic-text) !important;
    box-shadow: none !important;
    font-size: 14px !important;
    font-family: var(--font-system) !important;
    line-height: 1.6 !important;
    border: none !important;
    resize: none !important;
    letter-spacing: 0 !important;
  }

  .feature-input .el-textarea__inner::placeholder {
    color: var(--academic-text-tertiary);
    font-family: var(--font-system);
  }

  /* ============================================
   示例芯片
   ============================================ */
  .example-hint {
    color: var(--academic-text-tertiary);
    font-weight: 500;
  }

  .example-chip {
    background: rgba(255, 255, 255, 0.7);
    border: 1px solid var(--academic-border);
    color: var(--academic-text-secondary);
    border-radius: 999px;
    font-family: var(--font-system);
    font-weight: 500;
    backdrop-filter: blur(8px);
    transition: all 0.18s ease;
    cursor: pointer;
  }

  .example-chip:hover {
    background: #ffffff;
    border-color: var(--academic-primary);
    color: var(--academic-primary);
    box-shadow: 0 2px 8px rgba(0, 102, 204, 0.14);
    transform: translateY(-1px);
  }

  .example-chip .chip-icon {
    filter: grayscale(40%);
    transition: filter 0.2s ease;
  }

  .example-chip:hover .chip-icon {
    filter: grayscale(0%);
  }

  /* ============================================
   生成按钮 — 学术蓝 CTA
   ============================================ */
  .cast-spell-btn {
    position: relative;
    background: var(--academic-primary) !important;
    border: 1px solid var(--academic-primary) !important;
    border-radius: var(--radius-control) !important;
    font-weight: 600 !important;
    font-size: 14px !important;
    font-family: var(--font-system) !important;
    letter-spacing: 0.01em !important;
    color: #fff !important;
    padding: 10px 18px !important;
    box-shadow: 0 2px 6px rgba(0, 102, 204, 0.32), 0 1px 2px rgba(30, 41, 59, 0.1),
      inset 0 1px 0 rgba(255, 255, 255, 0.2) !important;
    transition: all 0.2s cubic-bezier(0.34, 1.2, 0.64, 1) !important;
    overflow: hidden;
  }

  .cast-spell-btn::before {
    content: "";
    position: absolute;
    top: 0;
    left: -100%;
    width: 55%;
    height: 100%;
    background: linear-gradient(90deg,
        transparent,
        rgba(255, 255, 255, 0.18),
        transparent);
    transition: left 0.6s ease;
  }

  .cast-spell-btn:hover::before {
    left: 160%;
  }

  .cast-spell-btn:hover {
    background: var(--academic-primary-hover) !important;
    border-color: var(--academic-primary-hover) !important;
    transform: translateY(-2px) !important;
    box-shadow: 0 6px 18px rgba(0, 102, 204, 0.4),
      0 2px 5px rgba(30, 41, 59, 0.12), inset 0 1px 0 rgba(255, 255, 255, 0.22) !important;
  }

  .cast-spell-btn[active="true"] {
    background: var(--academic-primary-active) !important;
  }

  .cast-spell-btn:active {
    transform: translateY(0px) scale(0.99) !important;
    background: var(--academic-primary-active) !important;
    box-shadow: 0 1px 3px rgba(0, 102, 204, 0.3),
      inset 0 1px 2px rgba(0, 0, 0, 0.1) !important;
  }

  /* ============================================
   等待状态 — 精密坐标圆环 + 十字准星
   ============================================ */
  .waiting-rune {
    position: relative;
    width: 112px;
    height: 112px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .waiting-rune::before,
  .waiting-rune::after {
    content: "";
    position: absolute;
    background: rgba(0, 102, 204, 0.15);
  }

  .waiting-rune::before {
    width: 100%;
    height: 1px;
    top: 50%;
  }

  .waiting-rune::after {
    width: 1px;
    height: 100%;
    left: 50%;
  }

  .rune-ring {
    position: absolute;
    border-radius: 50%;
    border: 1.5px solid transparent;
  }

  .rune-ring--outer {
    inset: 0;
    border-top-color: var(--academic-primary);
    border-right-color: rgba(0, 102, 204, 0.2);
    border-bottom-color: rgba(245, 158, 11, 0.4);
    border-left-color: transparent;
    animation: spinRune 2s linear infinite;
    box-shadow: 0 0 14px rgba(0, 102, 204, 0.14);
  }

  .rune-ring--inner {
    inset: 14px;
    border-top-color: transparent;
    border-right-color: rgba(245, 158, 11, 0.7);
    border-bottom-color: transparent;
    border-left-color: rgba(0, 102, 204, 0.4);
    animation: spinRune 1.4s linear infinite reverse;
  }

  @keyframes spinRune {
    from {
      transform: rotate(0deg);
    }

    to {
      transform: rotate(360deg);
    }
  }

  .rune-icon {
    color: var(--academic-primary) !important;
    filter: drop-shadow(0 0 6px rgba(0, 102, 204, 0.45)) drop-shadow(0 0 14px rgba(0, 102, 204, 0.18)) !important;
    animation: runeBreath 2.2s ease-in-out infinite;
    z-index: 1;
  }

  @keyframes runeBreath {

    0%,
    100% {
      transform: scale(0.95);
      opacity: 0.82;
    }

    50% {
      transform: scale(1.05);
      opacity: 1;
    }
  }

  .goBackBtn {
    cursor: pointer;
    font-size: 16px;
    display: flex;
    justify-content: center;
    align-items: center;
    line-height: 32px;
    height: 32px;
    width: 30px;
    text-align: center;
    float: left;
    vertical-align: middle;
    margin-right: 10px;
  }

  .waiting-tips {
    color: var(--academic-text-secondary);
    font-size: 14px;
    font-weight: 400;
    font-family: var(--font-system);
    letter-spacing: 0.01em;
    margin: 0 0 14px;
  }

  .magic-dots {
    display: flex;
    gap: 6px;
    align-items: center;
  }

  .magic-dots span {
    display: inline-block;
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--academic-primary);
    animation: dotBounce 1.2s ease-in-out infinite;
    opacity: 0.5;
  }

  .magic-dots span:nth-child(2) {
    animation-delay: 0.18s;
    background: var(--academic-accent);
  }

  .magic-dots span:nth-child(3) {
    animation-delay: 0.36s;
  }

  @keyframes dotBounce {

    0%,
    80%,
    100% {
      transform: translateY(0);
      opacity: 0.35;
    }

    40% {
      transform: translateY(-8px);
      opacity: 1;
    }
  }

  /* ============================================
   结果区域
   ============================================ */
  .resultComponentArea {
    position: relative;
    float: left;
    display: inline-block;
    width: 100%;
    height: 100%;
  }

  /* ============================================
   操作按钮组
   ============================================ */
  .action-bar {
    border-top: 1px solid var(--academic-border);
    background: rgba(248, 250, 252, 0.6);
    gap: 8px;
  }

  .wizard-action-btn {
    background: #ffffff !important;
    border: 1px solid var(--academic-border-strong) !important;
    color: var(--academic-text) !important;
    border-radius: 10px !important;
    font-size: 13px !important;
    font-weight: 500 !important;
    font-family: var(--font-system) !important;
    letter-spacing: 0.01em !important;
    box-shadow: 0 1px 3px rgba(30, 41, 59, 0.05),
      0 0 0 0.5px rgba(255, 255, 255, 0.8) inset !important;
    transition: all 0.18s ease !important;
  }

  .wizard-action-btn:hover {
    background: #ffffff !important;
    border-color: var(--academic-primary) !important;
    color: var(--academic-primary) !important;
    transform: translateY(-1px) !important;
    box-shadow: 0 3px 10px rgba(0, 102, 204, 0.14),
      0 1px 3px rgba(30, 41, 59, 0.04) !important;
  }

  .wizard-action-btn:active {
    transform: translateY(0) scale(0.99) !important;
    box-shadow: 0 1px 2px rgba(30, 41, 59, 0.08) !important;
  }

  .wizard-action-btn--primary {
    background: var(--academic-primary-soft) !important;
    border-color: rgba(0, 102, 204, 0.3) !important;
    color: var(--academic-primary) !important;
  }

  .wizard-action-btn--primary:hover {
    background: rgba(0, 102, 204, 0.14) !important;
    border-color: rgba(0, 102, 204, 0.45) !important;
    color: var(--academic-primary-active) !important;
    box-shadow: 0 3px 12px rgba(0, 102, 204, 0.2) !important;
  }

  /* ============================================
   Loading 遮罩
   ============================================ */
  .el-loading-mask {
    background: rgba(248, 250, 252, 0.82) !important;
    backdrop-filter: saturate(160%) blur(12px) !important;
    -webkit-backdrop-filter: saturate(160%) blur(12px) !important;
    border-radius: var(--radius-card) !important;
  }

  .el-loading-spinner .el-loading-text {
    color: var(--academic-text-secondary) !important;
    font-size: 12.5px !important;
    font-weight: 500 !important;
    font-family: var(--font-system) !important;
    letter-spacing: 0.01em !important;
    margin-top: 10px !important;
  }

  .el-loading-spinner .circular {
    width: 36px !important;
    height: 36px !important;
  }

  .el-loading-spinner .path {
    stroke: var(--academic-primary) !important;
    stroke-width: 2.5 !important;
  }

  /* ============================================
   布局辅助
   ============================================ */
  .topHeaderArea {
    position: relative;
    width: 100%;
  }

  .aigcOptionArea {
    position: relative;
    display: inline-block;
  }

  .designInfoArea {
    position: relative;
    display: inline-block;
  }

  /* ============================================
   Element Plus 覆盖 — 输入框 append
   ============================================ */
  .custom-input-group .el-input__wrapper {
    border-radius: 10px 0 0 10px !important;
    background: #ffffff !important;
    box-shadow: 0 0 0 1px var(--academic-border-strong) inset !important;
    transition: box-shadow 0.2s ease !important;
  }

  .custom-input-group .el-input__wrapper:hover {
    box-shadow: 0 0 0 1px var(--academic-primary) inset !important;
  }

  .custom-input-group .el-input__wrapper.is-focus {
    box-shadow: 0 0 0 1px var(--academic-primary) inset,
      0 0 0 3px var(--academic-primary-ring) !important;
  }

  .custom-input-group .el-input__inner {
    color: var(--academic-text) !important;
    font-family: var(--font-system) !important;
    font-size: 13.5px !important;
  }

  .custom-input-group .el-input__inner::placeholder {
    color: var(--academic-text-tertiary) !important;
  }

  .custom-input-group .el-input-group__append {
    background: transparent !important;
    padding: 0 !important;
    border-radius: 0 10px 10px 0 !important;
    box-shadow: none !important;
    border: none !important;
  }

  .custom-input-group .el-input-group__append .el-button {
    border-radius: 0 10px 10px 0 !important;
    background: var(--academic-primary) !important;
    border: 1px solid var(--academic-primary) !important;
    color: #fff !important;
    font-family: var(--font-system) !important;
    font-weight: 600 !important;
    margin: 0 !important;
    height: 100% !important;
    box-shadow: 0 1px 2px rgba(0, 102, 204, 0.25),
      inset 0 1px 0 rgba(255, 255, 255, 0.18) !important;
    transition: all 0.18s ease !important;
  }

  .custom-input-group .el-input-group__append .el-button:hover {
    background: var(--academic-primary-hover) !important;
    border-color: var(--academic-primary-hover) !important;
    box-shadow: 0 3px 8px rgba(0, 102, 204, 0.35),
      inset 0 1px 0 rgba(255, 255, 255, 0.2) !important;
  }

  /* ============================================
   Element Plus 覆盖 — 下拉弹层
   ============================================ */
  .el-select-dropdown {
    background: rgba(255, 255, 255, 0.96) !important;
    border: 1px solid var(--academic-border) !important;
    border-radius: 14px !important;
    box-shadow: 0 10px 32px rgba(30, 41, 59, 0.12),
      0 2px 8px rgba(30, 41, 59, 0.06) !important;
    backdrop-filter: saturate(160%) blur(18px) !important;
    -webkit-backdrop-filter: saturate(160%) blur(18px) !important;
    overflow: hidden !important;
  }

  .el-select-dropdown__item {
    color: var(--academic-text) !important;
    font-size: 13.5px !important;
    font-family: var(--font-system) !important;
    border-radius: 8px !important;
    margin: 2px 5px !important;
    padding: 8px 12px !important;
    transition: all 0.14s ease !important;
    letter-spacing: 0.01em !important;
  }

  .el-select-dropdown__item:hover,
  .el-select-dropdown__item.hover {
    background: var(--academic-primary-soft) !important;
    color: var(--academic-primary) !important;
  }

  .el-select-dropdown__item.selected {
    background: var(--academic-primary-soft) !important;
    color: var(--academic-primary) !important;
    font-weight: 600 !important;
  }

  /* ============================================
   图片预览弹层
   ============================================ */
  .el-image__inner {
    max-height: 80vh;
  }

  .el-image-viewer__wrapper {
    background: rgba(15, 23, 42, 0.85) !important;
    backdrop-filter: blur(12px) !important;
  }

  /* ============================================
   滚动条美化
   ============================================ */
  .wizard-result-panel ::-webkit-scrollbar {
    width: 6px;
    height: 6px;
  }

  .wizard-result-panel ::-webkit-scrollbar-track {
    background: transparent;
    border-radius: 6px;
  }

  .wizard-result-panel ::-webkit-scrollbar-thumb {
    background: rgba(30, 41, 59, 0.18);
    border-radius: 6px;
    transition: background 0.2s ease;
  }

  .wizard-result-panel ::-webkit-scrollbar-thumb:hover {
    background: var(--academic-primary);
  }

  /* ============================================
   响应式微调
   ============================================ */
  @media (max-width: 640px) {

    .wizard-input-panel,
    .wizard-result-panel {
      border-radius: 16px;
    }

    .cast-spell-btn {
      font-size: 13.5px !important;
      padding: 9px 14px !important;
    }

    /* 窄屏：操作区换行，避免登录按钮被挤出可视区 */
    .wizard-navbar {
      flex-wrap: wrap;
      row-gap: 10px;
      padding: 10px 4px 14px;
    }

    .navbar-actions {
      margin-left: auto;
      gap: 8px;
    }

    .logo-title {
      gap: 10px;
    }

    .logo-badge {
      width: 36px;
      height: 36px;
      border-radius: 10px;
    }

    .logo-badge-icon {
      font-size: 18px;
    }

    .logo-text {
      font-size: 15px;
    }

    .navbar-divider {
      height: 18px;
    }

    .refined-logo {
      width: 36px;
      height: 36px;
      border-radius: 10px;
    }

    .wizard-bg {
      background-size: auto, auto, 18px 18px;
    }
  }

  /* ============================================
   工具类补充
   ============================================ */
  .tw-bg-gray-50\/50 {
    background-color: rgba(246, 249, 247, 0.6);
  }

  .tw-w-800 {
    max-width: 800px;
  }

  .tw-margin-auto {
    margin: auto;
  }

  /* Muse 风格统一：品牌蓝控件、弹窗与表单 */
  :deep(.el-button--primary) {
    --el-button-bg-color: #0066CC;
    --el-button-border-color: #0066CC;
    --el-button-hover-bg-color: #0052A3;
    --el-button-hover-border-color: #0052A3;
    --el-button-active-bg-color: #004C99;
    --el-button-active-border-color: #004C99;
    border-radius: 999px;
  }

  :deep(.el-dialog) {
    border-radius: 16px !important;
    overflow: hidden;
    box-shadow: 0 25px 50px -12px rgba(15, 23, 42, 0.18) !important;
  }

  :deep(.el-dialog__header) {
    margin-right: 0;
    padding: 20px 24px 12px;
    border-bottom: 1px solid #EEF3F0;
  }

  :deep(.el-dialog__title) {
    font-weight: 700;
    font-size: 1.125rem;
    color: #0f172a;
  }

  :deep(.el-dialog__body) {
    padding: 20px 24px;
  }

  :deep(.el-dialog__footer) {
    padding: 12px 24px 20px;
    border-top: 1px solid #EEF3F0;
  }

  :deep(.el-select__wrapper),
  :deep(.el-input__wrapper) {
    border-radius: 10px;
  }

  :deep(.el-tag) {
    --el-tag-bg-color: #f0f9ff;
    --el-tag-border-color: #BAE6FD;
    --el-tag-text-color: #0052A3;
  }

  .tw-text-blue-600,
  .tw-text-blue-500 {
    color: #0066CC !important;
  }

  .tw-bg-blue-50 {
    background-color: #F0F9FF !important;
  }

  .tw-bg-blue-500,
  .tw-bg-blue-600 {
    background-color: #0066CC !important;
  }

  .tw-border-blue-200 {
    border-color: #BAE6FD !important;
  }
</style>

<script>export default 
          {"name":"","data":function(
) {
return {
      loading: false,
      loadingText: "已收指令...",
      waitingText: "等待输入描述...",
      gettedTips: "已输入描述，待点击 “立即生成” 确认...",
      modifyInfo: "",
      modifyLoading: false,
      modifyEnabled: false,
      readingFile: false,
      isStreaming: false,
      versionList: [],
      currentVersionIndex: 0,
      step: 0,
      currentAppItem: {
          className: "ElementVueItem",
          task: {
            style: {
              overflow: "auto",
            },
            template: "data:text/plain;charset=utf-8;base64,PHRlbXBsYXRlPgogIDwhLS0gMS4g5qC55a655Zmo5pS55Li65by55oCn55uS77yM56aB5q2i5YaF6YOo5rua5Yqo77yM5by65Yi26ZO65ruh54i257qnIC0tPgogIDxkaXYgY2xhc3M9InJvb3RDb250YWluZXJJdGVtIHR3LXctZnVsbCB0dy1oLWZ1bGwgdHctcC00IHR3LWZsZXggdHctZmxleC1jb2wgdHctb3ZlcmZsb3ctaGlkZGVuIHR3LWJveC1ib3JkZXIiPgoKICAgIDwhLS0g5qCH6aKY5Yy65Z2X77yI5Zu65a6a6auY5bqm77yM5oyJ6ZyA5pKR5byA77yJIC0tPgogICAgPGRpdiBjbGFzcz0ic3BlYy1oZWFkZXIgdHctZmxleCB0dy1mbGV4LXdyYXAgdHctaXRlbXMtc3RhcnQgdHctanVzdGlmeS1iZXR3ZWVuIHR3LWdhcC0zIHR3LXBiLTQgdHctYm9yZGVyLWIgdHctYm9yZGVyLXNsYXRlLTEwMCB0dy1zaHJpbmstMCI+CiAgICAgIDxkaXYgY2xhc3M9InR3LWZsZXggdHctZmxleC1jb2wgdHctZ2FwLTIgdHctbWluLXctMCI+CiAgICAgICAgPGgzIGNsYXNzPSJzcGVjLXRpdGxlIHR3LW0tMCB0dy10ZXh0LWxnIG1kOnR3LXRleHQteGwgdHctZm9udC1zZW1pYm9sZCB0dy1sZWFkaW5nLXNudWcgdHctdHJhY2tpbmctdGlnaHQgdHctdGV4dC1zbGF0ZS05MDAiPgogICAgICAgICAge3sgYWNjb3VudERhdGEudGl0bGUgfX0KICAgICAgICA8L2gzPgogICAgICAgIDwhLS0g5pGY6KaB6K+05piOIC0tPgogICAgICAgIDxwIGNsYXNzPSJzcGVjLXN1bW1hcnkgdHctbS0wIHR3LXRleHQteHMgbWQ6dHctdGV4dC1zbSB0dy1sZWFkaW5nLXJlbGF4ZWQgdHctdGV4dC1zbGF0ZS01MDAiPgogICAgICAgICAge3sgYWNjb3VudERhdGEuZGVzY3JpcHRpb24gfX0KICAgICAgICA8L3A+CiAgICAgIDwvZGl2PgoKICAgICAgPCEtLSDmiKrlm74gLyDlvZXliLblt6XlhbfmoI/vvJrmiYDmnInmjIfku6Tpg73pgJrov4cgcG9zdE1lc3NhZ2Ug5Y+R6YCB57uZIGlmcmFtZSDlhoXnmoTms6jlhaXohJrmnKzmiafooYwgLS0+CiAgICAgIDxkaXYgY2xhc3M9InRsLWNhcHR1cmUtdG9vbGJhciI+CiAgICAgICAgPHNwYW4gY2xhc3M9InRsLWNhcHR1cmUtc3RhdHVzIiBhcmlhLWxpdmU9InBvbGl0ZSI+CiAgICAgICAgICA8c3BhbiB2LWlmPSJpc1JlY29yZGluZyIgY2xhc3M9InRsLWNhcHR1cmUtZG90IiA6Y2xhc3M9InsgJ2lzLXBhdXNlZCc6IGlzUGF1c2VkIH0iPjwvc3Bhbj4KICAgICAgICAgIHt7IHN0YXR1c1RleHQgfX0KICAgICAgICA8L3NwYW4+CgogICAgICAgIDxidXR0b24gdi1pZj0icGFnZUVycm9ycy5sZW5ndGgiIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InRsLWNhcHR1cmUtYnRuIHRsLWNhcHR1cmUtYnRuLS1lcnJvciIgdGl0bGU9IuafpeeciyBpZnJhbWUg6aG16Z2i6L+Q6KGM5byC5bi4IiBAY2xpY2s9Im9wZW5FcnJvckRpYWxvZyI+CiAgICAgICAgICA8c3ZnIGNsYXNzPSJ0bC1jYXB0dXJlLWljb24iIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJjdXJyZW50Q29sb3IiIHN0cm9rZS13aWR0aD0iMS44IiBzdHJva2UtbGluZWNhcD0icm91bmQiIHN0cm9rZS1saW5lam9pbj0icm91bmQiIGFyaWEtaGlkZGVuPSJ0cnVlIj4KICAgICAgICAgICAgPHBhdGggZD0iTTEyIDMuNSAyMSAyMEgzeiIgLz4KICAgICAgICAgICAgPHBhdGggZD0iTTEyIDl2NSIgLz4KICAgICAgICAgICAgPGNpcmNsZSBjeD0iMTIiIGN5PSIxNyIgcj0iMC45IiBmaWxsPSJjdXJyZW50Q29sb3IiIHN0cm9rZT0ibm9uZSIgLz4KICAgICAgICAgIDwvc3ZnPgogICAgICAgICAgPHNwYW4+5byC5bi4IHt7IHBhZ2VFcnJvcnMubGVuZ3RoIH19PC9zcGFuPgogICAgICAgIDwvYnV0dG9uPgoKICAgICAgICA8YSB2LWlmPSJsYXN0U2NyZWVuc2hvdCIgY2xhc3M9InRsLWNhcHR1cmUtdGh1bWIiIDpocmVmPSJsYXN0U2NyZWVuc2hvdCIgOmRvd25sb2FkPSJsYXN0U2NyZWVuc2hvdE5hbWUiIHRpdGxlPSLkuIvovb3mnIDov5HkuIDmrKHmiKrlm74iPgogICAgICAgICAgPGltZyA6c3JjPSJsYXN0U2NyZWVuc2hvdCIgYWx0PSLmnIDov5HkuIDmrKHmiKrlm77pooTop4giPgogICAgICAgIDwvYT4KCiAgICAgICAgPGEgdi1pZj0ibGFzdFZpZGVvVXJsIiBjbGFzcz0idGwtY2FwdHVyZS1saW5rIiA6aHJlZj0ibGFzdFZpZGVvVXJsIiA6ZG93bmxvYWQ9Imxhc3RWaWRlb05hbWUiIHRpdGxlPSLkuIvovb3mnIDov5HkuIDmrKHlvZXliLbnmoTop4bpopEiPuacgOi/keinhumikTwvYT4KCiAgICAgICAgPGJ1dHRvbiB0eXBlPSJidXR0b24iIGNsYXNzPSJ0bC1jYXB0dXJlLWJ0biIgOmRpc2FibGVkPSIhaWZyYW1lUmVhZHkgfHwgIXNjZW5lUmVhZHkgfHwgc2NyZWVuc2hvdEJ1c3kiIDp0aXRsZT0iaWZyYW1lUmVhZHkgJiYgc2NlbmVSZWFkeSA/ICfmiKrlj5blvZPliY0gTGVhZmVySlMg55S76Z2i5bm26Ieq5Yqo5LiL6L29IFBORycgOiAn5Yqo55S755S75biD5Yqg6L295Lit4oCmJyIgQGNsaWNrPSJ0YWtlU2NyZWVuc2hvdCI+CiAgICAgICAgICA8c3ZnIGNsYXNzPSJ0bC1jYXB0dXJlLWljb24iIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJjdXJyZW50Q29sb3IiIHN0cm9rZS13aWR0aD0iMS44IiBzdHJva2UtbGluZWNhcD0icm91bmQiIHN0cm9rZS1saW5lam9pbj0icm91bmQiIGFyaWEtaGlkZGVuPSJ0cnVlIj4KICAgICAgICAgICAgPHBhdGggZD0iTTQgOC41QTIuNSAyLjUgMCAwIDEgNi41IDZoMS4yYTEgMSAwIDAgMCAuODItLjQzbC44NC0xLjE0QTEgMSAwIDAgMSAxMC4xOCA0aDMuNjRhMSAxIDAgMCAxIC44Mi40M2wuODQgMS4xNGExIDEgMCAwIDAgLjgyLjQzaDEuMkEyLjUgMi41IDAgMCAxIDIwIDguNXY4QTIuNSAyLjUgMCAwIDEgMTcuNSAxOWgtMTFBMi41IDIuNSAwIDAgMSA0IDE2LjV6IiAvPgogICAgICAgICAgICA8Y2lyY2xlIGN4PSIxMiIgY3k9IjEyLjUiIHI9IjMuMiIgLz4KICAgICAgICAgIDwvc3ZnPgogICAgICAgICAgPHNwYW4+e3sgc2NyZWVuc2hvdEJ1c3kgPyAn5oiq5Zu+5Lit4oCmJyA6ICfmiKrlm74nIH19PC9zcGFuPgogICAgICAgIDwvYnV0dG9uPgoKICAgICAgICA8YnV0dG9uIHYtaWY9IiFpc1JlY29yZGluZyIgdHlwZT0iYnV0dG9uIiBjbGFzcz0idGwtY2FwdHVyZS1idG4gdGwtY2FwdHVyZS1idG4tLXJlY29yZCIgOmRpc2FibGVkPSIhaWZyYW1lUmVhZHkgfHwgIXNjZW5lUmVhZHkgfHwgcmVjb3JkaW5nQnVzeSIgdGl0bGU9IuW8gOWni+W9leWItuW9k+WJjeWKqOeUu+eUu+mdouWPiumhtemdoumfs+mikSIgQGNsaWNrPSJzdGFydFJlY29yZGluZyI+CiAgICAgICAgICA8c3ZnIGNsYXNzPSJ0bC1jYXB0dXJlLWljb24iIHZpZXdCb3g9IjAgMCAyNCAyNCIgYXJpYS1oaWRkZW49InRydWUiPgogICAgICAgICAgICA8Y2lyY2xlIGN4PSIxMiIgY3k9IjEyIiByPSI2IiBmaWxsPSJjdXJyZW50Q29sb3IiIC8+CiAgICAgICAgICA8L3N2Zz4KICAgICAgICAgIDxzcGFuPnt7IHJlY29yZGluZ0J1c3kgPyAn5ZCv5Yqo5Lit4oCmJyA6ICflvIDlp4vlvZXliLYnIH19PC9zcGFuPgogICAgICAgIDwvYnV0dG9uPgoKICAgICAgICA8dGVtcGxhdGUgdi1lbHNlPgogICAgICAgICAgPGJ1dHRvbiB0eXBlPSJidXR0b24iIGNsYXNzPSJ0bC1jYXB0dXJlLWJ0biIgOmRpc2FibGVkPSIhaWZyYW1lUmVhZHkgfHwgcmVjb3JkaW5nQnVzeSIgOnRpdGxlPSJpc1BhdXNlZCA/ICfnu6fnu63lvZXliLYnIDogJ+aaguWBnOW9leWIticiIEBjbGljaz0idG9nZ2xlUmVjb3JkaW5nUGF1c2UiPgogICAgICAgICAgICA8c3ZnIHYtaWY9IiFpc1BhdXNlZCIgY2xhc3M9InRsLWNhcHR1cmUtaWNvbiIgdmlld0JveD0iMCAwIDI0IDI0IiBhcmlhLWhpZGRlbj0idHJ1ZSI+CiAgICAgICAgICAgICAgPHJlY3QgeD0iNyIgeT0iNSIgd2lkdGg9IjMuNCIgaGVpZ2h0PSIxNCIgcng9IjEuMiIgZmlsbD0iY3VycmVudENvbG9yIiAvPgogICAgICAgICAgICAgIDxyZWN0IHg9IjEzLjYiIHk9IjUiIHdpZHRoPSIzLjQiIGhlaWdodD0iMTQiIHJ4PSIxLjIiIGZpbGw9ImN1cnJlbnRDb2xvciIgLz4KICAgICAgICAgICAgPC9zdmc+CiAgICAgICAgICAgIDxzdmcgdi1lbHNlIGNsYXNzPSJ0bC1jYXB0dXJlLWljb24iIHZpZXdCb3g9IjAgMCAyNCAyNCIgYXJpYS1oaWRkZW49InRydWUiPgogICAgICAgICAgICAgIDxwYXRoIGQ9Ik04IDUuNXYxM2wxMS02LjV6IiBmaWxsPSJjdXJyZW50Q29sb3IiIC8+CiAgICAgICAgICAgIDwvc3ZnPgogICAgICAgICAgICA8c3Bhbj57eyBpc1BhdXNlZCA/ICfnu6fnu60nIDogJ+aaguWBnCcgfX08L3NwYW4+CiAgICAgICAgICA8L2J1dHRvbj4KCiAgICAgICAgICA8YnV0dG9uIHR5cGU9ImJ1dHRvbiIgY2xhc3M9InRsLWNhcHR1cmUtYnRuIHRsLWNhcHR1cmUtYnRuLS1zdG9wIiA6ZGlzYWJsZWQ9IiFpZnJhbWVSZWFkeSB8fCByZWNvcmRpbmdCdXN5IiB0aXRsZT0i5YGc5q2i5b2V5Yi25bm26Ieq5Yqo5L+d5a2Y6KeG6aKRIiBAY2xpY2s9InN0b3BSZWNvcmRpbmciPgogICAgICAgICAgICA8c3ZnIGNsYXNzPSJ0bC1jYXB0dXJlLWljb24iIHZpZXdCb3g9IjAgMCAyNCAyNCIgYXJpYS1oaWRkZW49InRydWUiPgogICAgICAgICAgICAgIDxyZWN0IHg9IjciIHk9IjciIHdpZHRoPSIxMCIgaGVpZ2h0PSIxMCIgcng9IjIiIGZpbGw9ImN1cnJlbnRDb2xvciIgLz4KICAgICAgICAgICAgPC9zdmc+CiAgICAgICAgICAgIDxzcGFuPnt7IHJlY29yZGluZ0J1c3kgPyAn5aSE55CG5Lit4oCmJyA6ICflgZzmraLlubbkv53lrZgnIH19PC9zcGFuPgogICAgICAgICAgPC9idXR0b24+CiAgICAgICAgPC90ZW1wbGF0ZT4KICAgICAgPC9kaXY+CiAgICA8L2Rpdj4KCiAgICA8IS0tIDIuIExlYWZlckpTIOWKqOeUu+mihOiniOWuueWZqO+8muS9v+eUqCB0dy1mbGV4LTEg6Ieq5Yqo5oqi5Y2g5Ymp5L2Z5omA5pyJ6auY5bqm77yM56Gu5L+d5pyA5aSn5YyW5bGV56S65LiU5LiN5rqi5Ye6IC0tPgogICAgPGRpdiBjbGFzcz0iaWZyYW1lLWNvbnRhaW5lciB0dy13LWZ1bGwgdHctZmxleC0xIHR3LW10LTMgdHctcm91bmRlZC14bCB0dy1vdmVyZmxvdy1oaWRkZW4gdHctYm9yZGVyIHR3LWJvcmRlci1zbGF0ZS0yMDAgdHctc2hhZG93LXNtIHR3LXJlbGF0aXZlIj4KICAgICAgPGlmcmFtZSByZWY9InByZXZpZXdJZnJhbWUiIDpzcmM9ImlmcmFtZVNyYyIgOnNyY2RvYz0iaWZyYW1lU3JjZG9jIiBjbGFzcz0idHctdy1mdWxsIHR3LWgtZnVsbCB0dy1ib3JkZXItbm9uZSB0dy1ibG9jayIgOnNhbmRib3g9ImlmcmFtZVNhbmRib3giIGFsbG93PSJhdXRvcGxheTsgZnVsbHNjcmVlbiIgYWxsb3dmdWxsc2NyZWVuIHRpdGxlPSJMZWFmZXJKUyBBbmltYXRpb24gUHJldmlldyIgQGxvYWQ9ImhhbmRsZUlmcmFtZUxvYWQiPjwvaWZyYW1lPgoKICAgICAgPCEtLSDliqjnlLvnlLvluIPliqDovb3mj5DnpLrvvJrnrKzkuIDluKfnnJ/mraPnu5jliLblh7rlhoXlrrnlkI7mt6Hlh7rvvIzpgb/lhY3lvq7kv6Hph4zlj6rnnIvliLDnmb3lsY8gLS0+CiAgICAgIDx0cmFuc2l0aW9uIG5hbWU9InRsLWxvYWRpbmctZmFkZSI+CiAgICAgICAgPGRpdiB2LWlmPSIhc2NlbmVSZWFkeSAmJiAhcGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4iIGNsYXNzPSJ0bC1zY2VuZS1sb2FkaW5nIiByb2xlPSJzdGF0dXMiIGFyaWEtbGl2ZT0icG9saXRlIj4KICAgICAgICAgIDxkaXYgY2xhc3M9InRsLXNjZW5lLWxvYWRpbmctY2FyZCI+CiAgICAgICAgICAgIDxzcGFuIGNsYXNzPSJ0bC1zY2VuZS1sb2FkaW5nLXNwaW5uZXIiIGFyaWEtaGlkZGVuPSJ0cnVlIj48L3NwYW4+CiAgICAgICAgICAgIDxkaXYgY2xhc3M9InRsLXNjZW5lLWxvYWRpbmctY29udGVudCI+CiAgICAgICAgICAgICAgPHN0cm9uZz57eyBzb3VyY2VGYWxsYmFja0ZhaWxlZCA/ICfmtY/op4jlmajml6Dms5XmiafooYzpooTop4johJrmnKwnIDogKHNjZW5lTG9hZFRpbWVkT3V0ID8gJ+WKqOeUu+eUu+W4g+S7jeacquWwsee7qicgOiAn5q2j5Zyo5Yqg6L29IExlYWZlckpTIOWKqOeUuycpIH19PC9zdHJvbmc+CiAgICAgICAgICAgICAgPHNwYW4+e3sgbG9hZGluZ0hpbnQgfX08L3NwYW4+CiAgICAgICAgICAgIDwvZGl2PgogICAgICAgICAgPC9kaXY+CiAgICAgICAgPC9kaXY+CiAgICAgIDwvdHJhbnNpdGlvbj4KICAgIDwvZGl2PgoKICAgIDwhLS0gaWZyYW1lIOmhtemdouW8guW4uOW8ueeqlyAtLT4KICAgIDx0cmFuc2l0aW9uIG5hbWU9InRsLWVycm9yLWZhZGUiPgogICAgICA8ZGl2IHYtaWY9ImVycm9yRGlhbG9nVmlzaWJsZSIgY2xhc3M9InRsLWVycm9yLW1hc2siIEBjbGljay5zZWxmPSJjbG9zZUVycm9yRGlhbG9nIj4KICAgICAgICA8ZGl2IHJlZj0iZXJyb3JEaWFsb2ciIGNsYXNzPSJ0bC1lcnJvci1kaWFsb2ciIHJvbGU9ImRpYWxvZyIgYXJpYS1tb2RhbD0idHJ1ZSIgYXJpYS1sYWJlbGxlZGJ5PSJ0bC1lcnJvci10aXRsZSIgdGFiaW5kZXg9Ii0xIiBAY2xpY2suc3RvcD4KICAgICAgICAgIDxkaXYgY2xhc3M9InRsLWVycm9yLWhlYWRlciI+CiAgICAgICAgICAgIDxkaXYgY2xhc3M9InRsLWVycm9yLWhlYWRpbmciPgogICAgICAgICAgICAgIDxzcGFuIGNsYXNzPSJ0bC1lcnJvci1iYWRnZSIgYXJpYS1oaWRkZW49InRydWUiPiE8L3NwYW4+CiAgICAgICAgICAgICAgPGRpdj4KICAgICAgICAgICAgICAgIDxoMyBpZD0idGwtZXJyb3ItdGl0bGUiIGNsYXNzPSJ0bC1lcnJvci10aXRsZSI+6aG16Z2i6L+Q6KGM5byC5bi4PC9oMz4KICAgICAgICAgICAgICAgIDxwIGNsYXNzPSJ0bC1lcnJvci1zdWJ0aXRsZSI+5Yqo55S76aKE6KeIIGlmcmFtZSDmjZXojrfliLAge3sgcGFnZUVycm9ycy5sZW5ndGggfX0g5p2h5byC5bi45L+h5oGvPC9wPgogICAgICAgICAgICAgIDwvZGl2PgogICAgICAgICAgICA8L2Rpdj4KICAgICAgICAgICAgPGJ1dHRvbiB0eXBlPSJidXR0b24iIGNsYXNzPSJ0bC1lcnJvci1jbG9zZSIgdGl0bGU9IuWFs+mXrSIgYXJpYS1sYWJlbD0i5YWz6Zet5byC5bi45by556qXIiBAY2xpY2s9ImNsb3NlRXJyb3JEaWFsb2ciPsOXPC9idXR0b24+CiAgICAgICAgICA8L2Rpdj4KCiAgICAgICAgICA8ZGl2IGNsYXNzPSJ0bC1lcnJvci1ib2R5Ij4KICAgICAgICAgICAgPGFydGljbGUgdi1mb3I9Iml0ZW0gaW4gcGFnZUVycm9ycyIgOmtleT0iaXRlbS5pZCIgY2xhc3M9InRsLWVycm9yLWl0ZW0iPgogICAgICAgICAgICAgIDxkaXYgY2xhc3M9InRsLWVycm9yLWl0ZW0taGVhZCI+CiAgICAgICAgICAgICAgICA8c3BhbiBjbGFzcz0idGwtZXJyb3ItdHlwZSI+e3sgaXRlbS50eXBlTGFiZWwgfX08L3NwYW4+CiAgICAgICAgICAgICAgICA8c3BhbiBjbGFzcz0idGwtZXJyb3ItdGltZSI+e3sgaXRlbS50aW1lTGFiZWwgfX08L3NwYW4+CiAgICAgICAgICAgICAgPC9kaXY+CiAgICAgICAgICAgICAgPHAgY2xhc3M9InRsLWVycm9yLW1lc3NhZ2UiPnt7IGl0ZW0ubWVzc2FnZSB9fTwvcD4KICAgICAgICAgICAgICA8cCB2LWlmPSJpdGVtLmxvY2F0aW9uIiBjbGFzcz0idGwtZXJyb3ItbG9jYXRpb24iPuS9jee9ru+8mnt7IGl0ZW0ubG9jYXRpb24gfX08L3A+CiAgICAgICAgICAgICAgPHByZSB2LWlmPSJpdGVtLnN0YWNrIiBjbGFzcz0idGwtZXJyb3Itc3RhY2siPnt7IGl0ZW0uc3RhY2sgfX08L3ByZT4KICAgICAgICAgICAgPC9hcnRpY2xlPgogICAgICAgICAgPC9kaXY+CgogICAgICAgICAgPGRpdiBjbGFzcz0idGwtZXJyb3ItZm9vdGVyIj4KICAgICAgICAgICAgPHNwYW4gdi1pZj0iY29weVN0YXRlID09PSAnY29waWVkJyIgY2xhc3M9InRsLWVycm9yLWNvcHktaGludCI+5bey5aSN5Yi25Yiw5Ymq6LS05p2/PC9zcGFuPgogICAgICAgICAgICA8c3BhbiB2LWVsc2UtaWY9ImNvcHlTdGF0ZSA9PT0gJ2ZhaWxlZCciIGNsYXNzPSJ0bC1lcnJvci1jb3B5LWhpbnQgaXMtZXJyb3IiPuWkjeWItuWksei0pe+8jOivt+aJi+WKqOmAieaLqeWGheWuuTwvc3Bhbj4KICAgICAgICAgICAgPGJ1dHRvbiB0eXBlPSJidXR0b24iIGNsYXNzPSJ0bC1lcnJvci1idG4iIDpkaXNhYmxlZD0iY29weVN0YXRlID09PSAnY29weWluZyciIEBjbGljaz0iY29weUVycm9ySW5mbyI+e3sgY29weVN0YXRlID09PSAnY29weWluZycgPyAn5aSN5Yi25Lit4oCmJyA6ICflpI3liLblvILluLjkv6Hmga8nIH19PC9idXR0b24+CiAgICAgICAgICAgIDxidXR0b24gdHlwZT0iYnV0dG9uIiBjbGFzcz0idGwtZXJyb3ItYnRuIHRsLWVycm9yLWJ0bi0tcHJpbWFyeSIgQGNsaWNrPSJjbG9zZUVycm9yRGlhbG9nIj7lhbPpl608L2J1dHRvbj4KICAgICAgICAgIDwvZGl2PgogICAgICAgIDwvZGl2PgogICAgICA8L2Rpdj4KICAgIDwvdHJhbnNpdGlvbj4KCiAgPC9kaXY+CjwvdGVtcGxhdGU+Cgo8c2NyaXB0PgogIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgKiDmiKrlm74gLyDlvZXliLbop4bpopHov5DooYzml7YKICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICog5Y6f5aeL55Sf5oiQ5Luj56CB77yIYWNjb3VudERhdGEuY29kZe+8ieacrOi6q+aYryBMZWFmZXJKUyDliqjnlLvpobXpnaLvvIzmsqHmnInku7vkvZXliqDovb3mj5DnpLrjgIEKICAgKiDmiKrlm77miJblvZXliLbog73lipvjgILlm6DmraTlnKggY29kZUJhc2U2NFVybCDorqHnrpflsZ7mgKfpk77ot6/ph4zvvIzlhYjosIPnlKgKICAgKiBpbmplY3RDYXB0dXJlUnVudGltZSgpIOaKiuOAjOWKoOi9veaPkOekuiArIOi/kOihjOaXtuS7o+eggeOAjeazqOWFpSBIVE1M77yI5bC96YeP5pS+5ZyoCiAgICogPGhlYWQ+IOacgOWJjemdou+8jOS/neivgeWcqOS4muWKoeiEmuacrOWIm+W7uiBDYW52YXMg55S75biD5LmL5YmN5a6M5oiQIOi/kOihjOaXtuahpeaOpemAu+i+ke+8ie+8jOWGjeaVtOS9k+i9rOaIkCBiYXNlNjQgZGF0YSBVUkzjgILms6jlhaXlv4Xpobvlj5HnlJ/lnKjovawgYmFzZTY0IOS5i+WJje+8jAogICAqIOWQpuWImSBpZnJhbWUg5ou/5Yiw55qE5piv5LiN5ZCr5o+Q56S65LiO5qGl5o6l6YC76L6R55qE5Y6f5aeL6aG16Z2i44CCCiAgICoKICAgKiDov5DooYzml7bku6PnoIHlhajpg6jkvY3kuo7nu4Tku7bmlrnms5XlhoXpg6jvvIzkuI3lnKjmqKHlnZfpobblsYLlo7DmmI7ku7vkvZXlh73mlbDjgIIKICAgKgogICAqIOazqOWFpeWQjueahCBpZnJhbWUg5YaF5Lya77yaCiAgICogICAxLiDpnZnmgIHlsZXnpLrliqDovb3mj5DnpLrvvIjljbPkvb/kuJrliqHohJrmnKzmiqXplJnmiJblvq7kv6HpmZDliLYgcG9zdE1lc3NhZ2Ug5Lmf6IO955yL5Yiw77yJ77ybCiAgICogICAyLiDnm5HlkKzniLbnqpflj6MgcG9zdE1lc3NhZ2Ug5oyH5Luk77yIc2NyZWVuc2hvdCAvIHN0YXJ0UmVjb3JkaW5nIC8gLi4u77yJ77ybCiAgICogICAzLiDlnKggaWZyYW1lIOWGhemDqOaJp+ihjOaIquWbvuOAgeW9leWItuOAgeaaguWBnOOAgeWBnOatouetieecn+WunumAu+i+ke+8mwogICAqICAgNC4g5b2V5Yi25pe25o6l566h6aG16Z2i5Lit55qEIDxhdWRpbz4vPHZpZGVvPiDkuI4gV2ViIEF1ZGlvIOi+k+WHuu+8jOaKiumfs+mikeWSjAogICAqICAgICAg55S76Z2i5pS+6L+b5ZCM5LiA5LiqIE1lZGlhUmVjb3JkZXLvvIzkv53or4Hpn7PnlLvlkIzmraXvvJsKICAgKiAgIDUuIOaNleiOt+mhtemdouiEmuacrOmUmeivr+OAgVByb21pc2Ug5byC5bi444CB6LWE5rqQ5Yqg6L295aSx6LSl44CBQ2FudmFzIOeUu+W4g+S4iuS4i+aWh+S4ouWkse+8jAogICAqICAgICAg6YCa6L+HIHBvc3RNZXNzYWdlIOS4iuaKpee7mee7hOS7tu+8jOeUsee7hOS7tuW8ueeql+Wxleekuu+8mwogICAqICAgNi4g6YCa6L+HIHBvc3RNZXNzYWdlIOaKiuaIquWbvi/lvZXliLbnu5PmnpzvvIhkYXRhVVJMIC8gQmxvYu+8ieS4jummluW4p+Wwsee7qgogICAqICAgICAg77yIc2NlbmUtcmVhZHnvvInnirbmgIHlm57kvKDnu5nnu4Tku7bjgIIKICAgKgogICAqIOW+ruS/oSAvIOaJi+acuuWGhee9rua1j+iniOWZqOeahOWFvOWuueWkhOeQhu+8mgogICAqICAgLSBDYW52YXMg55S75biD5LiK5LiL5paH5oyJ5Lia5Yqh5bGe5oCn5Yib5bu65aSx6LSl5pe277yM5Zue6YCA5Y6f5aeL5bGe5oCn6YeN6K+V5LiA5qyh77ybCiAgICogICAtIExlYWZlciDnlLvluIPlsLrlr7gg5pS25Yiw6Z2e5rOV5bC65a+45pe25Zue6YCA5YiwIERPTSAvIOeql+WPo+WwuuWvuO+8mwogICAqICAgLSDpmZDliLYgZGV2aWNlUGl4ZWxSYXRpb++8jOmBv+WFjemrmCBEUFIg5pKR54iG56e75Yqo56uv5pi+5a2Y77ybCiAgICogICAtIOinhuWPo+WPmOWMluaXtuS4u+WKqOihpeWPkSByZXNpemXvvIzkv67lpI3jgIznlLvluIPkuLogMCDlsLrlr7ggLyDlgZzlnKjml6flsLrlr7jjgI3pu5HlsY/vvJsKICAgKiAgIC0gZGF0YTogVVJMIGlmcmFtZSDoi6XohJrmnKzmnKrmiafooYzvvIznu4Tku7boh6rliqjpmY3nuqfkuLogQmxvYiBVUkwg4oaSIHNyY2RvY+OAggogICAqID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgZXhwb3J0IGRlZmF1bHQgewogICAgbmFtZTogIkxlYWZlckFuaW1hdGlvblNwZWNWaWV3ZXIiLAoKICAgIGVtaXRzOiBbCiAgICAgICdjYXB0dXJlLXNjcmVlbnNob3QnLAogICAgICAnY2FwdHVyZS1yZWNvcmRpbmctc3RhcnQnLAogICAgICAnY2FwdHVyZS1yZWNvcmRpbmctcGF1c2UnLAogICAgICAnY2FwdHVyZS1yZWNvcmRpbmctcmVzdW1lJywKICAgICAgJ2NhcHR1cmUtcmVjb3JkaW5nLXN0b3AnLAogICAgICAnY2FwdHVyZS1lcnJvcicKICAgIF0sCgogICAgZGF0YSgpIHsKICAgICAgcmV0dXJuIHsKICAgICAgICBhY2NvdW50RGF0YTogewogICAgICAgICAgdGl0bGU6ICLljZXmlofku7YgTGVhZmVySlMg5Yqo55S7572R6aG155Sf5oiQ6KeE6IyDIiwKICAgICAgICAgIGRlc2NyaXB0aW9uOiAi6Z2i5ZCRIExlYWZlckpTIDIueCDnmoTljZXmlofku7YgQ2FudmFzIOWKqOeUu+W6lOeUqOinhOiMg++8jOaUr+aMgeWIhumVnOaXtumXtOi9tOOAgeS6pOS6kumihOiniOOAgeaIquWbvuS4juW9leWxj+OAgiIsCiAgICAgICAgICBjb2RlOiBgCjwhRE9DVFlQRSBodG1sPgo8aHRtbCBsYW5nPSJ6aC1DTiI+CjxoZWFkPgo8bWV0YSBjaGFyc2V0PSJVVEYtOCI+CjxtZXRhIG5hbWU9InZpZXdwb3J0IiBjb250ZW50PSJ3aWR0aD1kZXZpY2Utd2lkdGgsaW5pdGlhbC1zY2FsZT0xIj4KPHRpdGxlPuaCrOa1ruWGsOaZtuaguOW/gzwvdGl0bGU+CjxzdHlsZT4KKntib3gtc2l6aW5nOmJvcmRlci1ib3h9Cmh0bWwsYm9keXttYXJnaW46MDt3aWR0aDoxMDAlO2hlaWdodDoxMDAlO292ZXJmbG93OmhpZGRlbjtiYWNrZ3JvdW5kOiMwNTBiMTg7Zm9udC1mYW1pbHk6QXJpYWwsc2Fucy1zZXJpZn0KI3N0YWdle3Bvc2l0aW9uOmFic29sdXRlO2luc2V0OjA7YmFja2dyb3VuZDpyYWRpYWwtZ3JhZGllbnQoZWxsaXBzZSBhdCA1MCUgNDUlLCMxNzNlNjAsIzA3MTQyNiA1NSUsIzAzMDcxMil9CiNodWR7cG9zaXRpb246YWJzb2x1dGU7Ym90dG9tOjIycHg7bGVmdDo1MCU7dHJhbnNmb3JtOnRyYW5zbGF0ZVgoLTUwJSk7ZGlzcGxheTpmbGV4O2dhcDoxMHB4O3BhZGRpbmc6MTJweCAxOHB4O2JvcmRlcjoxcHggc29saWQgIzRiOTFiOTY2O2JvcmRlci1yYWRpdXM6MTRweDtiYWNrZ3JvdW5kOiMxMDI0M2JjYztjb2xvcjojYzdlZGZmO2JhY2tkcm9wLWZpbHRlcjpibHVyKDEycHgpfQpidXR0b257Ym9yZGVyOjA7Ym9yZGVyLXJhZGl1czo3cHg7cGFkZGluZzo4cHggMTVweDtiYWNrZ3JvdW5kOiMxNjdkYjQ7Y29sb3I6d2hpdGU7Y3Vyc29yOnBvaW50ZXJ9CiNjYXB0aW9ue3Bvc2l0aW9uOmFic29sdXRlO3RvcDo4JTt3aWR0aDoxMDAlO3RleHQtYWxpZ246Y2VudGVyO2NvbG9yOiNiOWVhZmY7bGV0dGVyLXNwYWNpbmc6NXB4O3BvaW50ZXItZXZlbnRzOm5vbmV9Cjwvc3R5bGU+CjwvaGVhZD4KPGJvZHk+CjxkaXYgaWQ9InN0YWdlIj48L2Rpdj4KPGRpdiBpZD0iY2FwdGlvbiI+Q1JZU1RBTCBBV0FLRU5JTkc8L2Rpdj4KPGRpdiBpZD0iaHVkIj4KICA8c3BhbiBpZD0ic3RhdHVzIj4wMSAvIOa3seepuuiLj+mGkjwvc3Bhbj4KICA8YnV0dG9uIGlkPSJwbGF5Ij7mmoLlgZw8L2J1dHRvbj4KICA8YnV0dG9uIGlkPSJyZXN0YXJ0Ij7ph43mlrDmkq3mlL48L2J1dHRvbj4KPC9kaXY+CjxzY3JpcHQ+Cihhc3luYyBmdW5jdGlvbigpewogICd1c2Ugc3RyaWN0JzsKICBjb25zdCBBUFBfS0VZPSdfX0xFQUZFUl9BTklNQVRJT05fQVBQX18nOwogIGNvbnN0IFNDUklQVF9LRVk9J19fTEVBRkVSX1JVTlRJTUVfUFJPTUlTRV9fJzsKICBjb25zdCBwcmV2aW91cz13aW5kb3dbQVBQX0tFWV07CiAgaWYocHJldmlvdXMmJnR5cGVvZiBwcmV2aW91cy5kZXN0cm95PT09J2Z1bmN0aW9uJykgcHJldmlvdXMuZGVzdHJveSgpOwoKICBmdW5jdGlvbiBsb2FkTGVhZmVyKCl7CiAgICBpZih3aW5kb3cuTGVhZmVyVUkmJndpbmRvdy5MZWFmZXJVSS5MZWFmZXIpIHJldHVybiBQcm9taXNlLnJlc29sdmUod2luZG93LkxlYWZlclVJKTsKICAgIGlmKHdpbmRvd1tTQ1JJUFRfS0VZXSkgcmV0dXJuIHdpbmRvd1tTQ1JJUFRfS0VZXTsKICAgIHdpbmRvd1tTQ1JJUFRfS0VZXT1uZXcgUHJvbWlzZSgocmVzb2x2ZSxyZWplY3QpPT57CiAgICAgIGNvbnN0IHNjcmlwdD1kb2N1bWVudC5jcmVhdGVFbGVtZW50KCdzY3JpcHQnKTsKICAgICAgc2NyaXB0LnNyYz0naHR0cHM6Ly91bnBrZy5jb20vbGVhZmVyQDIuMy40L2Rpc3Qvd2ViLm1pbi5qcyc7CiAgICAgIHNjcmlwdC5hc3luYz10cnVlOwogICAgICBzY3JpcHQuZGF0YXNldC5sZWFmZXJSdW50aW1lPSd0cnVlJzsKICAgICAgc2NyaXB0Lm9ubG9hZD0oKT0+d2luZG93LkxlYWZlclVJJiZ3aW5kb3cuTGVhZmVyVUkuTGVhZmVyCiAgICAgICAgP3Jlc29sdmUod2luZG93LkxlYWZlclVJKTpyZWplY3QobmV3IEVycm9yKCdMZWFmZXJKUyDlt7LliqDovb3vvIzkvYbmnKrmib7liLAgTGVhZmVyVUkuTGVhZmVyJykpOwogICAgICBzY3JpcHQub25lcnJvcj0oKT0+cmVqZWN0KG5ldyBFcnJvcignTGVhZmVySlMgQ0ROIOWKoOi9veWksei0pScpKTsKICAgICAgZG9jdW1lbnQuaGVhZC5hcHBlbmRDaGlsZChzY3JpcHQpOwogICAgfSkuY2F0Y2goZXJyb3I9PnsKICAgICAgZGVsZXRlIHdpbmRvd1tTQ1JJUFRfS0VZXTsKICAgICAgdGhyb3cgZXJyb3I7CiAgICB9KTsKICAgIHJldHVybiB3aW5kb3dbU0NSSVBUX0tFWV07CiAgfQoKICB0cnl7CiAgICBjb25zdCBVST1hd2FpdCBsb2FkTGVhZmVyKCk7CiAgICBjb25zdCB7TGVhZmVyLEdyb3VwLFJlY3QsRWxsaXBzZSxUZXh0fT1VSTsKICAgIGNvbnN0IHN0YWdlPWRvY3VtZW50LmdldEVsZW1lbnRCeUlkKCdzdGFnZScpOwpjb25zdCBXPTEwMDAsSD01NjI7CmNvbnN0IGxlYWZlcj1uZXcgTGVhZmVyKHt2aWV3OnN0YWdlLHdpZHRoOlcsaGVpZ2h0Okh9KTsKY29uc3Qgd29ybGQ9bmV3IEdyb3VwKHt4OjAseTowfSk7CmxlYWZlci5hZGQod29ybGQpOwoKY29uc3QgJD1pZD0+ZG9jdW1lbnQuZ2V0RWxlbWVudEJ5SWQoaWQpOwpjb25zdCBjbGFtcD12PT5NYXRoLm1heCgwLE1hdGgubWluKDEsdikpOwpjb25zdCBlYXNlPXY9Pnt2PWNsYW1wKHYpO3JldHVybiB2KnYqKDMtMip2KX07CmNvbnN0IG1peD0oYSxiLHQpPT5hKyhiLWEpKnQ7CgpmdW5jdGlvbiBjaXJjbGUoeCx5LHIsY29sb3Isb3BhY2l0eT0xKXsKIGNvbnN0IGU9bmV3IEVsbGlwc2Uoe3g6eC1yLHk6eS1yLHdpZHRoOnIqMixoZWlnaHQ6cioyLGZpbGw6Y29sb3Isb3BhY2l0eX0pOwogd29ybGQuYWRkKGUpO3JldHVybiBlOwp9CmZ1bmN0aW9uIHJlY3QoeCx5LHcsaCxjb2xvcixyb3RhdGlvbj0wLG9wYWNpdHk9MSl7CiBjb25zdCBlPW5ldyBSZWN0KHt4LHksd2lkdGg6dyxoZWlnaHQ6aCxmaWxsOmNvbG9yLHJvdGF0aW9uLGFyb3VuZDonY2VudGVyJyxvcGFjaXR5fSk7CiB3b3JsZC5hZGQoZSk7cmV0dXJuIGU7Cn0KCi8vIOiDjOaZr+aYn+WwmApjb25zdCBzdGFycz1bXTsKZm9yKGxldCBpPTA7aTw4NTtpKyspewogY29uc3Qgcz1jaXJjbGUoTWF0aC5yYW5kb20oKSpXLE1hdGgucmFuZG9tKCkqSCxNYXRoLnJhbmRvbSgpKjEuOCswLjQsJyNhNmRjZmYnLE1hdGgucmFuZG9tKCkqMC41KzAuMTUpOwogc3RhcnMucHVzaCh7ZWw6cyxwaGFzZTpNYXRoLnJhbmRvbSgpKjYuMjgsc3BlZWQ6TWF0aC5yYW5kb20oKSoyKzAuNX0pOwp9CgovLyDmoLjlv4PlhYnmmZUKY29uc3QgZ2xvdz1jaXJjbGUoNTAwLDI4MCwxMjUsJyMxNjdjYjgnLDAuMTIpOwpjb25zdCBnbG93Mj1jaXJjbGUoNTAwLDI4MCw3NSwnIzYyZDdmZicsMC4xNik7CgovLyDnu5/kuIDop5LoibLnu4QKY29uc3QgY29yZT1uZXcgR3JvdXAoe3g6NTAwLHk6MjgwfSk7CndvcmxkLmFkZChjb3JlKTsKCmNvbnN0IGNyeXN0YWxQYXJ0cz1bXTsKZm9yKGxldCBpPTA7aTw4O2krKyl7CiBjb25zdCBhPWkqTWF0aC5QSS80OwogY29uc3QgcD1uZXcgUmVjdCh7CiAgeDpNYXRoLmNvcyhhKSoyOCwKICB5Ok1hdGguc2luKGEpKjI4LAogIHdpZHRoOjY2LAogIGhlaWdodDo2NiwKICBmaWxsOmklMj8nIzU3YmRlOCc6JyNjNGVmZmYnLAogIHN0cm9rZTonI2UyZjhmZicsCiAgc3Ryb2tlV2lkdGg6MS41LAogIHJvdGF0aW9uOjQ1K2kqNDUsCiAgYXJvdW5kOidjZW50ZXInLAogIG9wYWNpdHk6MC44CiB9KTsKIGNvcmUuYWRkKHApOwogY3J5c3RhbFBhcnRzLnB1c2gocCk7Cn0KY29uc3QgaW5uZXI9bmV3IEVsbGlwc2UoewogeDotMzYseTotMzYsd2lkdGg6NzIsaGVpZ2h0OjcyLAogZmlsbDonI2E2ZWRmZicsc3Ryb2tlOicjZmZmZmZmJyxzdHJva2VXaWR0aDozLAogb3BhY2l0eTowLjk1Cn0pOwpjb3JlLmFkZChpbm5lcik7CmNvbnN0IGNlbnRlcj1jaXJjbGUoNTAwLDI4MCwxOCwnI2ZmZmZmZicsMC45KTsKCi8vIOiDvemHj+eOrwpjb25zdCByaW5ncz1bXTsKZm9yKGxldCBpPTA7aTwzO2krKyl7CiBjb25zdCByaW5nPW5ldyBFbGxpcHNlKHsKICB4OjUwMC0oMTI1K2kqMjQpLHk6MjgwLSg0NStpKjEyKSwKICB3aWR0aDooMTI1K2kqMjQpKjIsaGVpZ2h0Oig0NStpKjEyKSoyLAogIHN0cm9rZTppJTI/JyM0MGNhZmYnOicjYjhmMmZmJywKICBzdHJva2VXaWR0aDoyLG9wYWNpdHk6MCwKICBhcm91bmQ6J2NlbnRlcicKIH0pOwogd29ybGQuYWRkKHJpbmcpO3JpbmdzLnB1c2gocmluZyk7Cn0KCi8vIOWGsOaZtueijueJhwpjb25zdCBmcmFnbWVudHM9W107CmZvcihsZXQgaT0wO2k8MTg7aSsrKXsKIGNvbnN0IGE9aSpNYXRoLlBJKjIvMTg7CiBjb25zdCByPTE1NStNYXRoLnNpbihpKjIuNCkqMzU7CiBjb25zdCBwPXJlY3QoNTAwK01hdGguY29zKGEpKnIsMjgwK01hdGguc2luKGEpKnIsCiAgMTAraSU0KjQsMjUraSUzKjcsJyNhN2VhZmYnLGEqMTgwL01hdGguUEksMCk7CiBmcmFnbWVudHMucHVzaCh7ZWw6cCxhLHIscGhhc2U6aSowLjd9KTsKfQoKLy8g54iG5Y+R57KS5a2QCmNvbnN0IHNwYXJrcz1bXTsKZm9yKGxldCBpPTA7aTw0ODtpKyspewogY29uc3QgYT1pKjIuMzk5OwogY29uc3QgcD1jaXJjbGUoNTAwLDI4MCwxLjUraSUzLCcjYjlmNWZmJywwKTsKIHNwYXJrcy5wdXNoKHtlbDpwLGEscjoxMDArKGklMTIpKjE2fSk7Cn0KCi8vIEhVRApjb25zdCB0aXRsZT1uZXcgVGV4dCh7CiB4OjM5MCx5OjQ2NSx0ZXh0OidJQ0UgQ09SRScsCiBmb250U2l6ZToxNSxmaWxsOicjOWJkZmZmJywKIGxldHRlclNwYWNpbmc6NSxvcGFjaXR5OjAuNwp9KTsKd29ybGQuYWRkKHRpdGxlKTsKCi8vIOWFqOWxgOaXtumXtOi9tApjb25zdCBkdXJhdGlvbj0xMjsKbGV0IGVsYXBzZWQ9MCxwbGF5aW5nPXRydWUsbGFzdD0wOwpsZXQgcmFmPTA7Cgpjb25zdCBzaG90cz1bCiBbMCwyLCcwMSAvIOa3seepuuiLj+mGkiddLAogWzIsNCwnMDIgLyDmmbbkvZPop4nphpInXSwKIFs0LDYsJzAzIC8g56KO54mH5YWx6bijJ10sCiBbNiw4LCcwNCAvIOiDvemHj+iBmumbhiddLAogWzgsMTAsJzA1IC8g5Yaw5pm257u95pS+J10sCiBbMTAsMTIsJzA2IC8g5rC45oGS5oKs5rWuJ10KXTsKCmZ1bmN0aW9uIHNlZ21lbnQodCxhLGIpe3JldHVybiBlYXNlKCh0LWEpLyhiLWEpKTt9CgpmdW5jdGlvbiB1cGRhdGUodCl7CiBjb25zdCBzMT1zZWdtZW50KHQsMCwyKTsKIGNvbnN0IHMyPXNlZ21lbnQodCwyLDQpOwogY29uc3QgczM9c2VnbWVudCh0LDQsNik7CiBjb25zdCBzND1zZWdtZW50KHQsNiw4KTsKIGNvbnN0IHM1PXNlZ21lbnQodCw4LDEwKTsKIGNvbnN0IHM2PXNlZ21lbnQodCwxMCwxMik7CgogLy8g6ZWc5aS077ya6YCa6L+H5LiW55WM5a655Zmo5qih5ouf5o6o6L+b5LiO5ouJ6L+cCiBjb25zdCB6b29tPXQ8Mj9taXgoMC43MiwxLjA1LHMxKToKICB0PDQ/bWl4KDEuMDUsMS4yMixzMik6CiAgdDw2P21peCgxLjIyLDAuOTgsczMpOgogIHQ8OD9taXgoMC45OCwxLjEyLHM0KToKICB0PDEwP21peCgxLjEyLDAuNzgsczUpOgogIG1peCgwLjc4LDAuODgsczYpOwogd29ybGQuc2NhbGVYPXpvb207CiB3b3JsZC5zY2FsZVk9em9vbTsKIHdvcmxkLng9KDEtem9vbSkqNTAwOwogd29ybGQueT0oMS16b29tKSoyODA7CgogLy8g5qC45b+D5rWu5Yqo5ZKM5peL6L2sCiBjb25zdCBjaGFyZ2U9c2VnbWVudCh0LDYuNSw4KTsKIGNvbnN0IGJ1cnN0PXNlZ21lbnQodCw4LDguMzUpKigxLXNlZ21lbnQodCw4LjM1LDkuNikpOwogY29yZS5yb3RhdGlvbj10KjE3OwogY29yZS55PTI4MCtNYXRoLnNpbih0KjEuNykqOTsKIGNvcmUuc2NhbGVYPTErY2hhcmdlKjAuMTIrYnVyc3QqMC4yODsKIGNvcmUuc2NhbGVZPTEtY2hhcmdlKjAuMDgrYnVyc3QqMC4yOwogaW5uZXIuc2NhbGVYPTErTWF0aC5zaW4odCo0KSowLjErY2hhcmdlKjAuNDU7CiBpbm5lci5zY2FsZVk9MStNYXRoLnNpbih0KjQpKjAuMStjaGFyZ2UqMC40NTsKIGlubmVyLm9wYWNpdHk9MC43NStjaGFyZ2UqMC4yNTsKCiBnbG93Lm9wYWNpdHk9MC4xK2NoYXJnZSowLjI1K2J1cnN0KjAuNTsKIGdsb3cuc2NhbGVYPTErYnVyc3QqMS4yOwogZ2xvdy5zY2FsZVk9MStidXJzdCoxLjI7CiBnbG93Mi5vcGFjaXR5PTAuMTUrY2hhcmdlKjAuNDsKCiAvLyDnoo7niYfvvJrlhaXlnLrjgIHnjq/nu5XjgIHniIblj5HjgIHlm57lvZIKIGZyYWdtZW50cy5mb3JFYWNoKChmLGkpPT57CiAgY29uc3Qgb3JiaXQ9c2VnbWVudCh0LDMuOCw1LjcpOwogIGNvbnN0IGV4cGxvZGU9c2VnbWVudCh0LDgsOC42KSooMS1zZWdtZW50KHQsOC42LDkuOCkpOwogIGNvbnN0IGFuZ2xlPWYuYSt0KigwLjI1K2klMyowLjA4KTsKICBjb25zdCByYWRpdXM9bWl4KGYucioxLjUsZi5yLG9yYml0KTsKICBjb25zdCB4PTUwMCtNYXRoLmNvcyhhbmdsZSkqcmFkaXVzOwogIGNvbnN0IHk9MjgwK01hdGguc2luKGFuZ2xlKSpyYWRpdXM7CiAgY29uc3Qgb3g9NTAwK01hdGguY29zKGFuZ2xlKSpmLnIqMi4yOwogIGNvbnN0IG95PTI4MCtNYXRoLnNpbihhbmdsZSkqZi5yKjIuMjsKICBmLmVsLng9bWl4KHgsb3gsZXhwbG9kZSk7CiAgZi5lbC55PW1peCh5LG95LGV4cGxvZGUpOwogIGYuZWwucm90YXRpb249YW5nbGUqNTcuMyt0KjEyOwogIGYuZWwub3BhY2l0eT1jbGFtcChvcmJpdCowLjg1K2V4cGxvZGUqMC4xNSk7CiB9KTsKCiAvLyDog73ph4/ovajpgZMKIHJpbmdzLmZvckVhY2goKHIsaSk9PnsKICByLnJvdGF0aW9uPXQqKGklMj8yNDotMTgpOwogIHIub3BhY2l0eT1zZWdtZW50KHQsMi4zLDMuNCkqMC42NTsKICBpZih0PjYpci5zY2FsZVg9ci5zY2FsZVk9MS1jaGFyZ2UqMC4yNSsoczUqMC43KTsKICBlbHNlIHIuc2NhbGVYPXIuc2NhbGVZPTE7CiB9KTsKCiAvLyDnspLlrZDvvJrog4zmma/lkbzlkLjkuI7kuK3lv4PniIblj5EKIHN0YXJzLmZvckVhY2gocz0+ewogIHMuZWwub3BhY2l0eT0wLjIrMC4zNSooMC41KzAuNSpNYXRoLnNpbih0KnMuc3BlZWQrcy5waGFzZSkpOwogfSk7CiBzcGFya3MuZm9yRWFjaCgocCxpKT0+ewogIGNvbnN0IGJ1cnN0PXNlZ21lbnQodCw4LDguMzUpKigxLXNlZ21lbnQodCw4LjM1LDkuOCkpOwogIGNvbnN0IGRpc3Q9cC5yKmJ1cnN0OwogIHAuZWwueD01MDArTWF0aC5jb3MocC5hKSpkaXN0OwogIHAuZWwueT0yODArTWF0aC5zaW4ocC5hKSpkaXN0OwogIHAuZWwub3BhY2l0eT1idXJzdDsKIH0pOwoKIHRpdGxlLm9wYWNpdHk9dD4xMD8wLjg1OjAuMjsKIHRpdGxlLnNjYWxlWD10aXRsZS5zY2FsZVk9dD4xMD8xOjAuOTsKIGNvbnN0IGN1cnJlbnQ9c2hvdHMuZmluZEluZGV4KHM9PnQ+PXNbMF0mJnQ8c1sxXSk7CiAkKCdzdGF0dXMnKS50ZXh0Q29udGVudD1zaG90c1tNYXRoLm1heCgwLGN1cnJlbnQpXVsyXTsKfQoKLy8g6Ieq6YCC5bqU55S75biDCmZ1bmN0aW9uIHJlc2l6ZSgpewogY29uc3Qgc2NhbGU9TWF0aC5taW4oc3RhZ2UuY2xpZW50V2lkdGgvVyxzdGFnZS5jbGllbnRIZWlnaHQvSCk7CiBsZWFmZXIuc2NhbGU9e3g6c2NhbGUseTpzY2FsZX07CiBsZWFmZXIueD0oc3RhZ2UuY2xpZW50V2lkdGgtVypzY2FsZSkvMjsKIGxlYWZlci55PShzdGFnZS5jbGllbnRIZWlnaHQtSCpzY2FsZSkvMjsKfQp3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigncmVzaXplJyxyZXNpemUpOwpyZXNpemUoKTsKCmZ1bmN0aW9uIGxvb3Aobm93KXsKIGlmKCFwbGF5aW5nKXJldHVybjsKIGNvbnN0IGR0PWxhc3Q/TWF0aC5taW4oKG5vdy1sYXN0KS8xMDAwLDAuMDUpOjA7CiBsYXN0PW5vdzsKIGVsYXBzZWQrPWR0OwogaWYoZWxhcHNlZD49ZHVyYXRpb24pewogIGVsYXBzZWQ9ZHVyYXRpb247CiAgdXBkYXRlKGVsYXBzZWQpOwogIHBsYXlpbmc9ZmFsc2U7CiAgJCgncGxheScpLnRleHRDb250ZW50PSfph43mlrDmkq3mlL4nOwogIHJldHVybjsKIH0KIHVwZGF0ZShlbGFwc2VkKTsKIHJhZj1yZXF1ZXN0QW5pbWF0aW9uRnJhbWUobG9vcCk7Cn0KZnVuY3Rpb24gcGxheSgpewogaWYoZWxhcHNlZD49ZHVyYXRpb24pZWxhcHNlZD0wOwogcGxheWluZz10cnVlO2xhc3Q9MDsKICQoJ3BsYXknKS50ZXh0Q29udGVudD0n5pqC5YGcJzsKIGNhbmNlbEFuaW1hdGlvbkZyYW1lKHJhZik7CiByYWY9cmVxdWVzdEFuaW1hdGlvbkZyYW1lKGxvb3ApOwp9CiQoJ3BsYXknKS5vbmNsaWNrPSgpPT57CiBpZihwbGF5aW5nKXsKICBwbGF5aW5nPWZhbHNlOwogIGNhbmNlbEFuaW1hdGlvbkZyYW1lKHJhZik7CiAgJCgncGxheScpLnRleHRDb250ZW50PSfnu6fnu60nOwogfWVsc2UgcGxheSgpOwp9OwokKCdyZXN0YXJ0Jykub25jbGljaz0oKT0+ewogcGxheWluZz1mYWxzZTsKIGNhbmNlbEFuaW1hdGlvbkZyYW1lKHJhZik7CiBlbGFwc2VkPTA7CiB1cGRhdGUoMCk7CiBwbGF5KCk7Cn07CnVwZGF0ZSgwKTsKcGxheSgpOwoKICAgIGNvbnN0IGFwcD17CiAgICAgIGxlYWZlciwKICAgICAgZGVzdHJveSgpewogICAgICAgIHBsYXlpbmc9ZmFsc2U7CiAgICAgICAgaWYocmFmKSBjYW5jZWxBbmltYXRpb25GcmFtZShyYWYpOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCdyZXNpemUnLHJlc2l6ZSk7CiAgICAgICAgaWYobGVhZmVyJiZ0eXBlb2YgbGVhZmVyLmRlc3Ryb3k9PT0nZnVuY3Rpb24nKSBsZWFmZXIuZGVzdHJveSgpOwogICAgICB9CiAgICB9OwogICAgd2luZG93W0FQUF9LRVldPWFwcDsKICB9Y2F0Y2goZXJyb3IpewogICAgY29uc3Qgc3RhdHVzPWRvY3VtZW50LmdldEVsZW1lbnRCeUlkKCdzdGF0dXMnKTsKICAgIGlmKHN0YXR1cykgc3RhdHVzLnRleHRDb250ZW50PSdMZWFmZXJKUyDliqDovb3lpLHotKXvvJonKyhlcnJvciYmZXJyb3IubWVzc2FnZT9lcnJvci5tZXNzYWdlOifmnKrnn6XplJnor68nKTsKICAgIGNvbnNvbGUuZXJyb3IoJ1tMZWFmZXJKUyBBbmltYXRpb25dJyxlcnJvcik7CiAgfQp9KSgpOwo8XC9zY3JpcHQ+CjwvYm9keT4KPC9odG1sPmAKICAgICAgICB9LAoKICAgICAgICAvKiAtLS0tLS0tLS0tIGlmcmFtZSAvIOaMh+S7pOeKtuaAgSAtLS0tLS0tLS0tICovCiAgICAgICAgaWZyYW1lUmVhZHk6IGZhbHNlLAogICAgICAgIC8qIGlmcmFtZSDliqDovb3mlrnlvI/vvJpkYXRhIFVSTO+8iOmmlumAie+8iSDihpIgYmxvYiDihpIgc3JjZG9j77yM5b6u5L+h6YeM6YCQ57qn6ZmN57qnICovCiAgICAgICAgaWZyYW1lU291cmNlTW9kZTogJ2RhdGEnLAogICAgICAgIGlmcmFtZUJsb2JVcmw6ICcnLAogICAgICAgIC8qIGlmcmFtZSDlhoXov5DooYzml7bmmK/lkKblt7LmiJDlip/miafooYzvvIznlKjkuo7liKTmlq3mmK/lkKbpnIDopoHliIfmjaLliqDovb3mlrnlvI8gKi8KICAgICAgICBicmlkZ2VBbGl2ZTogZmFsc2UsCiAgICAgICAgc291cmNlRmFsbGJhY2tGYWlsZWQ6IGZhbHNlLAogICAgICAgIHNjcmVlbnNob3RCdXN5OiBmYWxzZSwKICAgICAgICByZWNvcmRpbmdCdXN5OiBmYWxzZSwKICAgICAgICBpc1JlY29yZGluZzogZmFsc2UsCiAgICAgICAgaXNQYXVzZWQ6IGZhbHNlLAogICAgICAgIHJlY29yZGluZ1NlY29uZHM6IDAsCiAgICAgICAgcmVjb3JkaW5nSGFzQXVkaW86IGZhbHNlLAogICAgICAgIHN0YXR1c01lc3NhZ2U6ICcnLAoKICAgICAgICAvKiAtLS0tLS0tLS0tIOWKqOeUu+eUu+W4g+WKoOi9veeKtuaAgSAtLS0tLS0tLS0tICovCiAgICAgICAgc2NlbmVSZWFkeTogZmFsc2UsCiAgICAgICAgc2NlbmVMb2FkU2xvdzogZmFsc2UsCiAgICAgICAgc2NlbmVMb2FkVGltZWRPdXQ6IGZhbHNlLAogICAgICAgIHBhcmVudExvYWRpbmdPdmVybGF5SGlkZGVuOiBmYWxzZSwKCiAgICAgICAgLyogLS0tLS0tLS0tLSBpZnJhbWUg6aG16Z2i5byC5bi4IC0tLS0tLS0tLS0gKi8KICAgICAgICBwYWdlRXJyb3JzOiBbXSwKICAgICAgICBlcnJvckRpYWxvZ1Zpc2libGU6IGZhbHNlLAogICAgICAgIGNvcHlTdGF0ZTogJycsCgogICAgICAgIC8qIC0tLS0tLS0tLS0g5pyA6L+R5LiA5qyh57uT5p6c77yI5pa55L6/5YaN5qyh5LiL6L2977yM5paH5Lu25ZCN5ZyoIGNyZWF0ZWQg5Lit5oyJIHRpdGxlIOeUn+aIkO+8iSAtLS0tLS0tLS0tICovCiAgICAgICAgbGFzdFNjcmVlbnNob3Q6ICcnLAogICAgICAgIGxhc3RTY3JlZW5zaG90TmFtZTogJycsCiAgICAgICAgbGFzdFZpZGVvVXJsOiAnJywKICAgICAgICBsYXN0VmlkZW9OYW1lOiAnJwogICAgICB9OwogICAgfSwKCiAgICBjb21wdXRlZDogewogICAgICAvKioKICAgICAgICog5rOo5YWl5Yqg6L295o+Q56S65LiO5oiq5Zu+L+W9leWItui/kOihjOaXtuWQjueahOWujOaVtCBIVE1M44CCCiAgICAgICAqIOivpeWAvOWPquWcqOWOn+Wni+S7o+eggeWPmOWMluaXtumHjeeul++8jOaXoueUqOS6jiBkYXRhIFVSTO+8jOS5n+eUqOS6jiBCbG9iIC8gc3JjZG9jIOmZjee6p+OAggogICAgICAgKi8KICAgICAgaW5qZWN0ZWRIdG1sKCkgewogICAgICAgIGlmICghdGhpcy5hY2NvdW50RGF0YS5jb2RlKSByZXR1cm4gJyc7CiAgICAgICAgcmV0dXJuIHRoaXMuaW5qZWN0Q2FwdHVyZVJ1bnRpbWUodGhpcy5hY2NvdW50RGF0YS5jb2RlKTsKICAgICAgfSwKCiAgICAgIC8qKgogICAgICAgKiDms6jmhI/vvJrov5nph4zlv4XpobvlhYjlrozmiJDms6jlhaXjgIHlho3ovawgYmFzZTY044CCCiAgICAgICAqIOWOn+WniyBhY2NvdW50RGF0YS5jb2RlIOS4jeWMheWQq+S7u+S9leWKoOi9veaPkOekuuaIluaIquWbvi/lvZXliLbpgLvovpHjgIIKICAgICAgICovCiAgICAgIGNvZGVCYXNlNjRVcmwoKSB7CiAgICAgICAgaWYgKCF0aGlzLmluamVjdGVkSHRtbCkgcmV0dXJuICcnOwogICAgICAgIGNvbnN0IGJhc2U2NERhdGEgPSBidG9hKAogICAgICAgICAgZW5jb2RlVVJJQ29tcG9uZW50KHRoaXMuaW5qZWN0ZWRIdG1sKS5yZXBsYWNlKC8lKFswLTlBLUZdezJ9KS9nLCAobWF0Y2gsIHAxKSA9PgogICAgICAgICAgICBTdHJpbmcuZnJvbUNoYXJDb2RlKCcweCcgKyBwMSkKICAgICAgICAgICkKICAgICAgICApOwogICAgICAgIHJldHVybiBgZGF0YTp0ZXh0L2h0bWw7Y2hhcnNldD11dGYtODtiYXNlNjQsJHtiYXNlNjREYXRhfWA7CiAgICAgIH0sCgogICAgICBpZnJhbWVTcmMoKSB7CiAgICAgICAgaWYgKHRoaXMuaWZyYW1lU291cmNlTW9kZSA9PT0gJ2Jsb2InKSByZXR1cm4gdGhpcy5pZnJhbWVCbG9iVXJsIHx8IG51bGw7CiAgICAgICAgaWYgKHRoaXMuaWZyYW1lU291cmNlTW9kZSA9PT0gJ3NyY2RvYycpIHJldHVybiBudWxsOwogICAgICAgIHJldHVybiB0aGlzLmNvZGVCYXNlNjRVcmwgfHwgbnVsbDsKICAgICAgfSwKCiAgICAgIGlmcmFtZVNyY2RvYygpIHsKICAgICAgICByZXR1cm4gdGhpcy5pZnJhbWVTb3VyY2VNb2RlID09PSAnc3JjZG9jJyA/IHRoaXMuaW5qZWN0ZWRIdG1sIDogbnVsbDsKICAgICAgfSwKCiAgICAgIC8qKgogICAgICAgKiBCbG9iIC8gc3JjZG9jIOaooeW8j+WmguaenOS/neeVmSBhbGxvdy1zYW1lLW9yaWdpbu+8jGlmcmFtZSDkvJrnu6fmib/niLbpobXpnaLmupDvvIwKICAgICAgICog5a2Y5Zyo5a6J5YWo6aOO6Zmp77yb6L+Z5Lik56eN5YW85a655qih5byP5Y+q5byA5pS+6ISa5pys5LiO5by556qX6IO95Yqb44CCCiAgICAgICAqLwogICAgICBpZnJhbWVTYW5kYm94KCkgewogICAgICAgIHJldHVybiB0aGlzLmlmcmFtZVNvdXJjZU1vZGUgPT09ICdkYXRhJyA/CiAgICAgICAgICAnYWxsb3ctc2NyaXB0cyBhbGxvdy1tb2RhbHMgYWxsb3ctc2FtZS1vcmlnaW4nIDoKICAgICAgICAgICdhbGxvdy1zY3JpcHRzIGFsbG93LW1vZGFscyc7CiAgICAgIH0sCgogICAgICBzdGF0dXNUZXh0KCkgewogICAgICAgIGlmICh0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkKSByZXR1cm4gJ+a1j+iniOWZqOaXoOazleaJp+ihjOmihOiniOiEmuacrCc7CiAgICAgICAgaWYgKCF0aGlzLmlmcmFtZVJlYWR5KSByZXR1cm4gJ+mihOiniOWKoOi9veS4reKApic7CiAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHsKICAgICAgICAgIGlmICh0aGlzLnNjZW5lTG9hZFRpbWVkT3V0KSByZXR1cm4gJ+WKqOeUu+eUu+W4g+S7jeacquWwsee7qic7CiAgICAgICAgICByZXR1cm4gdGhpcy5zY2VuZUxvYWRTbG93ID8gJ+WKqOeUu+eUu+W4g+WKoOi9vei+g+aFouKApicgOiAn5Yqo55S755S75biD5Yqg6L295Lit4oCmJzsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMuaXNSZWNvcmRpbmcpIHsKICAgICAgICAgIHJldHVybiAodGhpcy5pc1BhdXNlZCA/ICflt7LmmoLlgZwgJyA6ICflvZXliLbkuK0gJykgKwogICAgICAgICAgICB0aGlzLmZvcm1hdER1cmF0aW9uKHRoaXMucmVjb3JkaW5nU2Vjb25kcykgKwogICAgICAgICAgICAodGhpcy5yZWNvcmRpbmdIYXNBdWRpbyA/ICcgwrcg5ZCr6Z+z6aKRJyA6ICcnKTsKICAgICAgICB9CiAgICAgICAgcmV0dXJuIHRoaXMuc3RhdHVzTWVzc2FnZSB8fCAn5bCx57uqJzsKICAgICAgfSwKCiAgICAgIGxvYWRpbmdIaW50KCkgewogICAgICAgIGlmICh0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkKSB7CiAgICAgICAgICByZXR1cm4gJ+W9k+WJjea1j+iniOWZqOaXoOazleaJp+ihjOmihOiniOiEmuacrO+8m+ivt+WwneivleWcqOezu+e7n+a1j+iniOWZqOS4reaJk+W8gO+8jOaIluajgOafpea1j+iniOWZqOeahCBDYW52YXMgMkQg5pSv5oyB44CCJzsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMuc2NlbmVMb2FkVGltZWRPdXQpIHsKICAgICAgICAgIHJldHVybiB0aGlzLnBhZ2VFcnJvcnMubGVuZ3RoID8KICAgICAgICAgICAgJ+WKoOi9vei2heaXtu+8jOivt+aJk+W8gOWPs+S4iuinkuKAnOW8guW4uOKAneafpeeci+WFt+S9k+mUmeivr+OAgicgOgogICAgICAgICAgICAn5Yqg6L295pe26Ze06L6D6ZW/77yM6K+35qOA5p+l572R57uc5oiWIExlYWZlckpTIENETiDmmK/lkKblj6/orr/pl67jgIInOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5zY2VuZUxvYWRTbG93KSByZXR1cm4gJ+i1hOa6kOi+g+Wkp+aIlue9kee7nOi+g+aFou+8jOivt+eojeWAmeKApic7CiAgICAgICAgcmV0dXJuICfmraPlnKjliJ3lp4vljJYgTGVhZmVySlMgQ2FudmFzIOeUu+W4g+KApic7CiAgICAgIH0KICAgIH0sCgogICAgd2F0Y2g6IHsKICAgICAgY29kZUJhc2U2NFVybCgpIHsKICAgICAgICB0aGlzLmlmcmFtZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5pc1JlY29yZGluZyA9IGZhbHNlOwogICAgICAgIHRoaXMuaXNQYXVzZWQgPSBmYWxzZTsKICAgICAgICB0aGlzLnJlY29yZGluZ1NlY29uZHMgPSAwOwogICAgICAgIHRoaXMucmVjb3JkaW5nSGFzQXVkaW8gPSBmYWxzZTsKICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAnJzsKICAgICAgICB0aGlzLnNjZW5lUmVhZHkgPSBmYWxzZTsKICAgICAgICB0aGlzLnJlc2V0U2NlbmVMb2FkaW5nKCk7CiAgICAgICAgdGhpcy5yZXNldFBhZ2VFcnJvcnMoKTsKICAgICAgICB0aGlzLnJlamVjdFBlbmRpbmdDb21tYW5kcygn6aKE6KeI5bey6YeN5paw5Yqg6L29Jyk7CiAgICAgICAgdGhpcy5jbGVhclNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgICB0aGlzLnJldm9rZUlmcmFtZUJsb2JVcmwoKTsKICAgICAgICB0aGlzLmlmcmFtZVNvdXJjZU1vZGUgPSAnZGF0YSc7CiAgICAgICAgdGhpcy5pZnJhbWVCbG9iVXJsID0gJyc7CiAgICAgICAgdGhpcy5icmlkZ2VBbGl2ZSA9IGZhbHNlOwogICAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tGYWlsZWQgPSBmYWxzZTsKICAgICAgICB0aGlzLnNjaGVkdWxlU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICB9CiAgICB9LAoKICAgIGNyZWF0ZWQoKSB7CiAgICAgIC8qIOmdnuWTjeW6lOW8j+WGhemDqOeKtuaAgSAqLwogICAgICB0aGlzLnBlbmRpbmdDb21tYW5kcyA9IE9iamVjdC5jcmVhdGUobnVsbCk7CiAgICAgIHRoaXMuY29tbWFuZFNlcSA9IDA7CiAgICAgIHRoaXMuc3RhdHVzVGltZXIgPSBudWxsOwogICAgICB0aGlzLmxhc3RWaWRlb09iamVjdFVybCA9ICcnOwogICAgICB0aGlzLmJyaWRnZVRvcm5Eb3duID0gZmFsc2U7CiAgICAgIHRoaXMuZXJyb3JTZXEgPSAwOwogICAgICB0aGlzLnNlZW5QYWdlRXJyb3JJZHMgPSBPYmplY3QuY3JlYXRlKG51bGwpOwogICAgICB0aGlzLmNvcHlSZXNldFRpbWVyID0gbnVsbDsKICAgICAgdGhpcy5lcnJvckRpYWxvZ1N1cHByZXNzZWRVbnRpbCA9IDA7CiAgICAgIHRoaXMuc2NlbmVTbG93VGltZXIgPSBudWxsOwogICAgICB0aGlzLnNjZW5lVGltZW91dFRpbWVyID0gbnVsbDsKICAgICAgdGhpcy5zY2VuZVBhcmVudE92ZXJsYXlUaW1lciA9IG51bGw7CiAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tUaW1lciA9IG51bGw7CgogICAgICAvKiDkuIvovb3lsZ7mgKflnKjpppbmrKHlr7zlh7rliY3kuZ/kv53mjIHkuI4gdGl0bGUg5LiA6Ie0ICovCiAgICAgIHRoaXMubGFzdFNjcmVlbnNob3ROYW1lID0gdGhpcy5idWlsZFRpdGxlRmlsZU5hbWUoJ3BuZycpOwogICAgICB0aGlzLmxhc3RWaWRlb05hbWUgPSB0aGlzLmJ1aWxkVGl0bGVGaWxlTmFtZSgnd2VibScpOwogICAgfSwKCiAgICBtb3VudGVkKCkgewogICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcignbWVzc2FnZScsIHRoaXMuaGFuZGxlTWVzc2FnZSk7CiAgICAgIHdpbmRvdy5hZGRFdmVudExpc3RlbmVyKCdrZXlkb3duJywgdGhpcy5oYW5kbGVLZXlkb3duKTsKICAgICAgdGhpcy5zY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgIH0sCgogICAgLyogVnVlIDIg5L2/55SoIGJlZm9yZURlc3Ryb3nvvJtWdWUgMyDkvb/nlKggYmVmb3JlVW5tb3VudO+8jOi/memHjOWQjOaXtuWFvOWuue+8jOWGhemDqOWBmuW5guetiSAqLwogICAgYmVmb3JlRGVzdHJveSgpIHsKICAgICAgdGhpcy50ZWFyZG93bkNhcHR1cmVCcmlkZ2UoKTsKICAgIH0sCgogICAgYmVmb3JlVW5tb3VudCgpIHsKICAgICAgdGhpcy50ZWFyZG93bkNhcHR1cmVCcmlkZ2UoKTsKICAgIH0sCgogICAgbWV0aG9kczogewogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgKiBpZnJhbWUg6L+Q6KGM5pe25rOo5YWlCiAgICAgICAqID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICAvKioKICAgICAgICog5p6E6YCg5rOo5YWlIGlmcmFtZSDnmoTmiKrlm74gLyDlvZXliLbov5DooYzml7bmupDnoIHjgIIKICAgICAgICogbGVhZmVyQ2FwdHVyZVJ1bnRpbWUg5b+F6aG75a6M5YWo6Ieq5YyF5ZCr77yI5LiN5byV55So57uE5Lu2IHRoaXPvvInvvIzlm6DkuLrlroPkvJoKICAgICAgICog6KKrIHRvU3RyaW5nKCkg5bqP5YiX5YyW5ZCO5pS+6L+bIGlmcmFtZSDmiafooYzjgILov5nph4zmiorlroPlo7DmmI7lnKjmlrnms5XlhoXpg6jvvIwKICAgICAgICog6YG/5YWN5Zyo5qih5Z2X6aG25bGC5Ye6546w5YWo5bGA5Ye95pWw5aOw5piO44CCCiAgICAgICAqLwogICAgICBidWlsZENhcHR1cmVSdW50aW1lU291cmNlKCkgewogICAgICAgIGZ1bmN0aW9uIGxlYWZlckNhcHR1cmVSdW50aW1lKCkgewogICAgICAgICAgJ3VzZSBzdHJpY3QnOwoKICAgICAgICAgIGlmICh3aW5kb3cuX19MRUFGRVJfQ0FQVFVSRV9JTlNUQUxMRURfXykgcmV0dXJuOwogICAgICAgICAgd2luZG93Ll9fTEVBRkVSX0NBUFRVUkVfSU5TVEFMTEVEX18gPSB0cnVlOwoKICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogMS4gTGVhZmVySlMgQ2FudmFzIOeUu+W4g+WHhuWkhwogICAgICAgICAgICogTGVhZmVySlMgV2ViIOeJiOS9v+eUqCBIVE1MQ2FudmFzRWxlbWVudCAvIENhbnZhcyAyRCDnu5jliLbvvIzkuI3kvp3otZYKICAgICAgICAgICAqIFRocmVlLmpzIFdlYkdMUmVuZGVyZXLvvIzkuZ/kuI3pnIDopoEgcHJlc2VydmVEcmF3aW5nQnVmZmVyIOihpeS4geOAggogICAgICAgICAgICog5oiq5Zu+5LiO5b2V5bGP55u05o6l6YeH6ZuGIExlYWZlckpTIOWIm+W7uueahOWPr+ingSBjYW52YXPjgIIKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KCiAgICAgICAgICAvKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0KICAgICAgICAgICAqIDIuIFdlYiBBdWRpbyDovpPlh7rml4Hot68KICAgICAgICAgICAqIOWKqOeUu+mhtemdouS4reeahOeoi+W6j+WMlumfs+aViOmAmuW4uOmAmui/hyBXZWIgQXVkaW8g5pKt5pS+77yMCiAgICAgICAgICAgKiDmsqHmnInlr7nlupTnmoQgPGF1ZGlvPi88dmlkZW8+IOWFg+e0oOWPr+S7pSBjYXB0dXJlU3RyZWFt44CC6L+Z6YeM5Zyo5Lia5Yqh5Luj56CB5Yib5bu6CiAgICAgICAgICAgKiBBdWRpb0NvbnRleHQg5LmL5ZCO44CB5bCG6IqC54K56L+e5o6l5YiwIGNvbnRleHQuZGVzdGluYXRpb24g5pe277yM6aKd5aSW5bm26IGU5LiA5LiqCiAgICAgICAgICAgKiBNZWRpYVN0cmVhbUF1ZGlvRGVzdGluYXRpb25Ob2Rl77yM5oqK5ZCM5LiA5Lu95aOw6Z+z5byV5Ye65p2l57uZIE1lZGlhUmVjb3JkZXLjgIIKICAgICAgICAgICAqIOivpeaXgei3r+S4jeS8muaUueWPmOWOn+acieaJrOWjsOWZqOi+k+WHuuOAggogICAgICAgICAgICogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLSAqLwogICAgICAgICAgdmFyIGF1ZGlvVGFwUmVjb3JkcyA9IFtdOwogICAgICAgICAgdmFyIGF1ZGlvVGFwcGVkTm9kZXMgPSBudWxsOwogICAgICAgICAgdmFyIHdlYkF1ZGlvTWVkaWFFbGVtZW50cyA9IG51bGw7CgogICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgaWYgKHR5cGVvZiB3aW5kb3cuV2Vha1NldCA9PT0gJ2Z1bmN0aW9uJykgewogICAgICAgICAgICAgIGF1ZGlvVGFwcGVkTm9kZXMgPSBuZXcgd2luZG93LldlYWtTZXQoKTsKICAgICAgICAgICAgICB3ZWJBdWRpb01lZGlhRWxlbWVudHMgPSBuZXcgd2luZG93LldlYWtTZXQoKTsKICAgICAgICAgICAgfQogICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgLyog5b+955WlICovIH0KCiAgICAgICAgICBmdW5jdGlvbiBmaW5kQXVkaW9UYXAoY29udGV4dCkgewogICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IGF1ZGlvVGFwUmVjb3Jkcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIGlmIChhdWRpb1RhcFJlY29yZHNbaV0uY29udGV4dCA9PT0gY29udGV4dCkgcmV0dXJuIGF1ZGlvVGFwUmVjb3Jkc1tpXTsKICAgICAgICAgICAgfQogICAgICAgICAgICByZXR1cm4gbnVsbDsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBlbnN1cmVBdWRpb1RhcChjb250ZXh0KSB7CiAgICAgICAgICAgIGlmICghY29udGV4dCB8fCAhY29udGV4dC5kZXN0aW5hdGlvbikgcmV0dXJuIG51bGw7CiAgICAgICAgICAgIHZhciBleGlzdGluZyA9IGZpbmRBdWRpb1RhcChjb250ZXh0KTsKICAgICAgICAgICAgaWYgKGV4aXN0aW5nKSByZXR1cm4gZXhpc3Rpbmc7CgogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIGlmICh0eXBlb2YgY29udGV4dC5jcmVhdGVNZWRpYVN0cmVhbURlc3RpbmF0aW9uICE9PSAnZnVuY3Rpb24nKSByZXR1cm4gbnVsbDsKICAgICAgICAgICAgICB2YXIgdGFwRGVzdGluYXRpb24gPSBjb250ZXh0LmNyZWF0ZU1lZGlhU3RyZWFtRGVzdGluYXRpb24oKTsKICAgICAgICAgICAgICB2YXIgcmVjb3JkID0gewogICAgICAgICAgICAgICAgY29udGV4dDogY29udGV4dCwKICAgICAgICAgICAgICAgIGRlc3RpbmF0aW9uOiB0YXBEZXN0aW5hdGlvbiwKICAgICAgICAgICAgICAgIG5vZGVzOiBbXQogICAgICAgICAgICAgIH07CiAgICAgICAgICAgICAgYXVkaW9UYXBSZWNvcmRzLnB1c2gocmVjb3JkKTsKICAgICAgICAgICAgICByZXR1cm4gcmVjb3JkOwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIHJldHVybiBudWxsOwogICAgICAgICAgICB9CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gbWFya0F1ZGlvTm9kZVRhcHBlZChub2RlKSB7CiAgICAgICAgICAgIGlmICghbm9kZSkgcmV0dXJuIGZhbHNlOwogICAgICAgICAgICBpZiAoYXVkaW9UYXBwZWROb2RlcykgewogICAgICAgICAgICAgIGlmIChhdWRpb1RhcHBlZE5vZGVzLmhhcyhub2RlKSkgcmV0dXJuIGZhbHNlOwogICAgICAgICAgICAgIGF1ZGlvVGFwcGVkTm9kZXMuYWRkKG5vZGUpOwogICAgICAgICAgICAgIHJldHVybiB0cnVlOwogICAgICAgICAgICB9CiAgICAgICAgICAgIGlmIChub2RlLl9fbGVhZmVyQ2FwdHVyZUF1ZGlvVGFwcGVkKSByZXR1cm4gZmFsc2U7CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgbm9kZS5fX2xlYWZlckNhcHR1cmVBdWRpb1RhcHBlZCA9IHRydWU7CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgcmV0dXJuIHRydWU7CiAgICAgICAgICB9CgogICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgdmFyIGF1ZGlvTm9kZVByb3RvdHlwZSA9IHdpbmRvdy5BdWRpb05vZGUgJiYgd2luZG93LkF1ZGlvTm9kZS5wcm90b3R5cGU7CiAgICAgICAgICAgIGlmIChhdWRpb05vZGVQcm90b3R5cGUgJiYgdHlwZW9mIGF1ZGlvTm9kZVByb3RvdHlwZS5jb25uZWN0ID09PSAnZnVuY3Rpb24nKSB7CiAgICAgICAgICAgICAgdmFyIG5hdGl2ZUF1ZGlvQ29ubmVjdCA9IGF1ZGlvTm9kZVByb3RvdHlwZS5jb25uZWN0OwogICAgICAgICAgICAgIGF1ZGlvTm9kZVByb3RvdHlwZS5jb25uZWN0ID0gZnVuY3Rpb24oZGVzdGluYXRpb24pIHsKICAgICAgICAgICAgICAgIHZhciByZXN1bHQgPSBuYXRpdmVBdWRpb0Nvbm5lY3QuYXBwbHkodGhpcywgYXJndW1lbnRzKTsKICAgICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICAgIHZhciBjb250ZXh0ID0gdGhpcy5jb250ZXh0OwogICAgICAgICAgICAgICAgICBpZiAoY29udGV4dCAmJiBkZXN0aW5hdGlvbiAmJiBkZXN0aW5hdGlvbiA9PT0gY29udGV4dC5kZXN0aW5hdGlvbikgewogICAgICAgICAgICAgICAgICAgIHZhciB0YXAgPSBlbnN1cmVBdWRpb1RhcChjb250ZXh0KTsKICAgICAgICAgICAgICAgICAgICBpZiAodGFwICYmIG1hcmtBdWRpb05vZGVUYXBwZWQodGhpcykpIHsKICAgICAgICAgICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICAgICAgICAgIHZhciBvdXRwdXRJbmRleCA9IGFyZ3VtZW50cy5sZW5ndGggPiAxID8gYXJndW1lbnRzWzFdIDogMDsKICAgICAgICAgICAgICAgICAgICAgICAgbmF0aXZlQXVkaW9Db25uZWN0LmNhbGwodGhpcywgdGFwLmRlc3RpbmF0aW9uLCBvdXRwdXRJbmRleCk7CiAgICAgICAgICAgICAgICAgICAgICAgIHRhcC5ub2Rlcy5wdXNoKHRoaXMpOwogICAgICAgICAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgICAgICAgICAgLyog5Y2V5Liq6IqC54K55peB6Lev5aSx6LSl5LiN5b2x5ZON5Y6f6Z+z6aKRICovIH0KICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CiAgICAgICAgICAgICAgICByZXR1cm4gcmVzdWx0OwogICAgICAgICAgICAgIH07CiAgICAgICAgICAgIH0KICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CgogICAgICAgICAgLyog6KKrIGNyZWF0ZU1lZGlhRWxlbWVudFNvdXJjZSDmjqXlhaUgV2ViIEF1ZGlvIOeahOWqkuS9k+WFg+e0oO+8jOWjsOmfs+W3sue7j+eUseS4iumdoueahAogICAgICAgICAgICog5peB6Lev6YeH6ZuG77yM6L+Z6YeM5YGa5Liq5qCH6K6w77yM6YG/5YWN5YaN6YCa6L+HIGNhcHR1cmVTdHJlYW0g6YeH6ZuG6YCg5oiQ5aOw6Z+z6YeN5aSN44CCICovCiAgICAgICAgICB0cnkgewogICAgICAgICAgICB2YXIgYmFzZUF1ZGlvQ29udGV4dFByb3RvdHlwZSA9IHdpbmRvdy5CYXNlQXVkaW9Db250ZXh0ICYmIHdpbmRvdy5CYXNlQXVkaW9Db250ZXh0LnByb3RvdHlwZTsKICAgICAgICAgICAgaWYgKCFiYXNlQXVkaW9Db250ZXh0UHJvdG90eXBlICYmIHdpbmRvdy5BdWRpb0NvbnRleHQpIHsKICAgICAgICAgICAgICBiYXNlQXVkaW9Db250ZXh0UHJvdG90eXBlID0gd2luZG93LkF1ZGlvQ29udGV4dC5wcm90b3R5cGU7CiAgICAgICAgICAgIH0KICAgICAgICAgICAgaWYgKGJhc2VBdWRpb0NvbnRleHRQcm90b3R5cGUgJiYKICAgICAgICAgICAgICB0eXBlb2YgYmFzZUF1ZGlvQ29udGV4dFByb3RvdHlwZS5jcmVhdGVNZWRpYUVsZW1lbnRTb3VyY2UgPT09ICdmdW5jdGlvbicpIHsKICAgICAgICAgICAgICB2YXIgbmF0aXZlQ3JlYXRlTWVkaWFFbGVtZW50U291cmNlID0gYmFzZUF1ZGlvQ29udGV4dFByb3RvdHlwZS5jcmVhdGVNZWRpYUVsZW1lbnRTb3VyY2U7CiAgICAgICAgICAgICAgYmFzZUF1ZGlvQ29udGV4dFByb3RvdHlwZS5jcmVhdGVNZWRpYUVsZW1lbnRTb3VyY2UgPSBmdW5jdGlvbihlbGVtZW50KSB7CiAgICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgICBpZiAod2ViQXVkaW9NZWRpYUVsZW1lbnRzICYmIGVsZW1lbnQpIHdlYkF1ZGlvTWVkaWFFbGVtZW50cy5hZGQoZWxlbWVudCk7CiAgICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgICAgICAgcmV0dXJuIG5hdGl2ZUNyZWF0ZU1lZGlhRWxlbWVudFNvdXJjZS5hcHBseSh0aGlzLCBhcmd1bWVudHMpOwogICAgICAgICAgICAgIH07CiAgICAgICAgICAgIH0KICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CgogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiAzLiDkuI7niLbnqpflj6PpgJrkv6EKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIHBvc3RUb1BhcmVudChldmVudCwgaWQsIGFjdGlvbiwgZGF0YSkgewogICAgICAgICAgICBpZiAoIXdpbmRvdy5wYXJlbnQgfHwgd2luZG93LnBhcmVudCA9PT0gd2luZG93KSByZXR1cm4gZmFsc2U7CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgd2luZG93LnBhcmVudC5wb3N0TWVzc2FnZSh7CiAgICAgICAgICAgICAgICBfX2xlYWZlckNhcHR1cmU6IHRydWUsCiAgICAgICAgICAgICAgICBzb3VyY2U6ICdsZWFmZXItY2FwdHVyZScsCiAgICAgICAgICAgICAgICBpZDogaWQgfHwgbnVsbCwKICAgICAgICAgICAgICAgIGFjdGlvbjogYWN0aW9uIHx8IG51bGwsCiAgICAgICAgICAgICAgICBldmVudDogZXZlbnQsCiAgICAgICAgICAgICAgICBkYXRhOiBkYXRhIHx8IG51bGwKICAgICAgICAgICAgICB9LCAnKicpOwogICAgICAgICAgICAgIHJldHVybiB0cnVlOwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIHJldHVybiBmYWxzZTsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHBvc3RFcnJvcihpZCwgYWN0aW9uLCBlcnJvciwgY29kZSkgewogICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ2Vycm9yJywgaWQsIGFjdGlvbiwgewogICAgICAgICAgICAgIG1lc3NhZ2U6IGVycm9yICYmIGVycm9yLm1lc3NhZ2UgPyBlcnJvci5tZXNzYWdlIDogU3RyaW5nKGVycm9yIHx8ICfmnKrnn6XplJnor68nKSwKICAgICAgICAgICAgICBjb2RlOiBjb2RlIHx8ICdDQVBUVVJFX0VSUk9SJwogICAgICAgICAgICB9KTsKICAgICAgICAgIH0KCiAgICAgICAgICAvKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0KICAgICAgICAgICAqIDMuMSBpZnJhbWUg5YaF5Yqg6L295o+Q56S6CiAgICAgICAgICAgKiDov5nmrrUgVUkg5piv6ZqPIEhUTUwg6Z2Z5oCB5rOo5YWl55qE77yI6KeBIGluamVjdENhcHR1cmVSdW50aW1l77yJ77yM5Y2z5L2/5Lia5Yqh6ISa5pysCiAgICAgICAgICAgKiDmiqXplJnjgIHmiJblvq7kv6HpmZDliLYgcG9zdE1lc3NhZ2XvvIznlKjmiLfkuZ/og73lnKggaWZyYW1lIOWGheeci+WIsOi/m+W6puS4juWksei0peWOn+WboO+8jAogICAgICAgICAgICog6ICM5LiN5piv5a+5552A5LiA54mH55m95bGP44CC6aaW5bin55yf5q2j57uY5Yi25Ye65YaF5a655ZCO5omN5reh5Ye656e76Zmk44CCCiAgICAgICAgICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tICovCiAgICAgICAgICB2YXIgc2NlbmVSZWFkeU5vdGlmaWVkID0gZmFsc2U7CiAgICAgICAgICB2YXIgbG9hZGluZ1Byb2JsZW1SZXBvcnRlZCA9IGZhbHNlOwoKICAgICAgICAgIGZ1bmN0aW9uIGdldEluamVjdGVkTG9hZGluZ092ZXJsYXkoKSB7CiAgICAgICAgICAgIHJldHVybiBkb2N1bWVudC5nZXRFbGVtZW50QnlJZCgnX19sZWFmZXJfbG9hZGluZ19vdmVybGF5X18nKTsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBzZXRJbmplY3RlZExvYWRpbmdDb250ZW50KHRpdGxlLCBoaW50KSB7CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgdmFyIHRpdGxlTm9kZSA9IGRvY3VtZW50LmdldEVsZW1lbnRCeUlkKCdfX2xlYWZlcl9sb2FkaW5nX3RpdGxlX18nKTsKICAgICAgICAgICAgICB2YXIgaGludE5vZGUgPSBkb2N1bWVudC5nZXRFbGVtZW50QnlJZCgnX19sZWFmZXJfbG9hZGluZ19oaW50X18nKTsKICAgICAgICAgICAgICBpZiAodGl0bGVOb2RlICYmIHRpdGxlKSB0aXRsZU5vZGUudGV4dENvbnRlbnQgPSB0aXRsZTsKICAgICAgICAgICAgICBpZiAoaGludE5vZGUgJiYgaGludCkgaGludE5vZGUudGV4dENvbnRlbnQgPSBoaW50OwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gc2hvd0luamVjdGVkTG9hZGluZ1Byb2JsZW0obWVzc2FnZSkgewogICAgICAgICAgICBpZiAoc2NlbmVSZWFkeU5vdGlmaWVkKSByZXR1cm47CiAgICAgICAgICAgIGxvYWRpbmdQcm9ibGVtUmVwb3J0ZWQgPSB0cnVlOwogICAgICAgICAgICB2YXIgaGludCA9IG1lc3NhZ2UgPyBTdHJpbmcobWVzc2FnZSkgOiAn6K+35qOA5p+l572R57uc44CBTGVhZmVySlMgQ0ROIOaYr+WQpuWPr+iuv+mXru+8jOS7peWPiua1j+iniOWZqOaYr+WQpuaUr+aMgSBDYW52YXMgMkTjgIInOwogICAgICAgICAgICBpZiAoaGludC5sZW5ndGggPiAxODApIGhpbnQgPSBoaW50LnNsaWNlKDAsIDE4MCkgKyAn4oCmJzsKICAgICAgICAgICAgc2V0SW5qZWN0ZWRMb2FkaW5nQ29udGVudCgn5Yqo55S755S75biD5Yqg6L296YGH5Yiw6Zeu6aKYJywgaGludCk7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gaGlkZUluamVjdGVkTG9hZGluZ092ZXJsYXkoKSB7CiAgICAgICAgICAgIHZhciBvdmVybGF5ID0gZ2V0SW5qZWN0ZWRMb2FkaW5nT3ZlcmxheSgpOwogICAgICAgICAgICBpZiAoIW92ZXJsYXkpIHJldHVybjsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICBvdmVybGF5LnN0eWxlLm9wYWNpdHkgPSAnMCc7CiAgICAgICAgICAgICAgb3ZlcmxheS5zdHlsZS5wb2ludGVyRXZlbnRzID0gJ25vbmUnOwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CiAgICAgICAgICAgIHdpbmRvdy5zZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGlmIChvdmVybGF5LnBhcmVudE5vZGUpIG92ZXJsYXkucGFyZW50Tm9kZS5yZW1vdmVDaGlsZChvdmVybGF5KTsKICAgICAgICAgICAgfSwgMzUwKTsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBzdGFydEluamVjdGVkTG9hZGluZ1dhdGNoKCkgewogICAgICAgICAgICAvKiA2IOenkuS7jeaXoOmmluW4p++8muaPkOekuui1hOa6kOi+g+Wkp++8mzEyIOenkuS7jeaXoOmmluW4p++8mue7meWHuiBMZWFmZXJKUyAvIENhbnZhcyDliJ3lp4vljJblpLHotKXmjIflvJXjgIIKICAgICAgICAgICAgICog5bey57uP5piO56Gu5oql5ZGK6L+H6Zeu6aKY77yI5aaCIENhbnZhcyDnlLvluIPkuI3lj6/nlKjvvInml7bkuI3lho3opobnm5bmm7TlhbfkvZPnmoTmj5DnpLrjgIIgKi8KICAgICAgICAgICAgd2luZG93LnNldFRpbWVvdXQoZnVuY3Rpb24oKSB7CiAgICAgICAgICAgICAgaWYgKCFzY2VuZVJlYWR5Tm90aWZpZWQgJiYgIWxvYWRpbmdQcm9ibGVtUmVwb3J0ZWQpIHsKICAgICAgICAgICAgICAgIHNldEluamVjdGVkTG9hZGluZ0NvbnRlbnQoJ+WKqOeUu+eUu+W4g+WKoOi9vei+g+aFoicsICdMZWFmZXJKUyDmiJbliqjnlLvotYTmupDku43lnKjliqDovb3vvIzor7fnqI3lgJnigKYnKTsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgIH0sIDYwMDApOwoKICAgICAgICAgICAgd2luZG93LnNldFRpbWVvdXQoZnVuY3Rpb24oKSB7CiAgICAgICAgICAgICAgaWYgKHNjZW5lUmVhZHlOb3RpZmllZCB8fCBsb2FkaW5nUHJvYmxlbVJlcG9ydGVkKSByZXR1cm47CiAgICAgICAgICAgICAgc2V0SW5qZWN0ZWRMb2FkaW5nQ29udGVudCgKICAgICAgICAgICAgICAgICfliqjnlLvnlLvluIPku43mnKrlsLHnu6onLAogICAgICAgICAgICAgICAgJ+WPr+iDveaYryBMZWFmZXJKUyDotYTmupDliqDovb3lpLHotKXjgIHliqjnlLvohJrmnKzlvILluLjmiJYgQ2FudmFzIOeUu+W4g+acquWIm+W7uuOAguivt+ajgOafpee9kee7nOS4jumhtemdouW8guW4uOS/oeaBr+OAgicKICAgICAgICAgICAgICApOwogICAgICAgICAgICB9LCAxMjAwMCk7CiAgICAgICAgICB9CgogICAgICAgICAgLyogTGVhZmVySlMg5L2/55SoIENhbnZhcyAyRO+8m+S4jeaJp+ihjCBXZWJHTCDmjqLmtYvjgIJDYW52YXMg5piv5ZCm5Y+v55So5Y+K6aaW5binCiAgICAgICAgICAgKiDmmK/lkKblrozmiJDvvIznlLHkuIvmlrnnmoTnnJ/lrp7nlLvluIPmo4DmtYvnu5/kuIDliKTmlq3jgIIgKi8KCiAgICAgICAgICAvKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0KICAgICAgICAgICAqIDQuIOmAmueUqOW3peWFtwogICAgICAgICAgICogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLSAqLwogICAgICAgICAgZnVuY3Rpb24gY2xhbXBOdW1iZXIodmFsdWUsIG1pbiwgbWF4LCBmYWxsYmFjaykgewogICAgICAgICAgICB2YXIgbnVtYmVyID0gdHlwZW9mIHZhbHVlID09PSAnbnVtYmVyJyA/IHZhbHVlIDogcGFyc2VGbG9hdCh2YWx1ZSk7CiAgICAgICAgICAgIGlmICghaXNGaW5pdGUobnVtYmVyKSkgcmV0dXJuIGZhbGxiYWNrOwogICAgICAgICAgICByZXR1cm4gTWF0aC5taW4obWF4LCBNYXRoLm1heChtaW4sIG51bWJlcikpOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGRhdGFVcmxCeXRlU2l6ZShkYXRhVXJsKSB7CiAgICAgICAgICAgIHZhciBjb21tYUluZGV4ID0gZGF0YVVybC5pbmRleE9mKCcsJyk7CiAgICAgICAgICAgIGlmIChjb21tYUluZGV4ID09PSAtMSkgcmV0dXJuIGRhdGFVcmwubGVuZ3RoOwogICAgICAgICAgICB2YXIgYmFzZTY0ID0gZGF0YVVybC5zbGljZShjb21tYUluZGV4ICsgMSk7CiAgICAgICAgICAgIHZhciBwYWRkaW5nID0gMDsKICAgICAgICAgICAgaWYgKGJhc2U2NC5jaGFyQXQoYmFzZTY0Lmxlbmd0aCAtIDEpID09PSAnPScpIHBhZGRpbmcgKz0gMTsKICAgICAgICAgICAgaWYgKGJhc2U2NC5jaGFyQXQoYmFzZTY0Lmxlbmd0aCAtIDIpID09PSAnPScpIHBhZGRpbmcgKz0gMTsKICAgICAgICAgICAgcmV0dXJuIE1hdGgubWF4KDAsIE1hdGguZmxvb3IoYmFzZTY0Lmxlbmd0aCAqIDMgLyA0KSAtIHBhZGRpbmcpOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGNvbGxlY3RDYW52YXNlcygpIHsKICAgICAgICAgICAgdmFyIG5vZGVzID0gZG9jdW1lbnQucXVlcnlTZWxlY3RvckFsbCgnY2FudmFzJyk7CiAgICAgICAgICAgIHZhciByZXN1bHQgPSBbXTsKICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBub2Rlcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIHZhciBjYW52YXMgPSBub2Rlc1tpXTsKICAgICAgICAgICAgICBpZiAoIWNhbnZhcyB8fCAhY2FudmFzLndpZHRoIHx8ICFjYW52YXMuaGVpZ2h0KSBjb250aW51ZTsKICAgICAgICAgICAgICB2YXIgcmVjdCA9IGNhbnZhcy5nZXRCb3VuZGluZ0NsaWVudFJlY3QoKTsKICAgICAgICAgICAgICBpZiAoIXJlY3QgfHwgcmVjdC53aWR0aCA8PSAwIHx8IHJlY3QuaGVpZ2h0IDw9IDApIGNvbnRpbnVlOwogICAgICAgICAgICAgIHZhciBzdHlsZSA9IHdpbmRvdy5nZXRDb21wdXRlZFN0eWxlID8gd2luZG93LmdldENvbXB1dGVkU3R5bGUoY2FudmFzKSA6IG51bGw7CiAgICAgICAgICAgICAgaWYgKHN0eWxlICYmIChzdHlsZS5kaXNwbGF5ID09PSAnbm9uZScgfHwgc3R5bGUudmlzaWJpbGl0eSA9PT0gJ2hpZGRlbicgfHwgcGFyc2VGbG9hdChzdHlsZS5vcGFjaXR5KSA9PT0gMCkpIGNvbnRpbnVlOwogICAgICAgICAgICAgIHJlc3VsdC5wdXNoKHsKICAgICAgICAgICAgICAgIGNhbnZhczogY2FudmFzLAogICAgICAgICAgICAgICAgcmVjdDogcmVjdAogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICB9CiAgICAgICAgICAgIHJldHVybiByZXN1bHQ7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gZG9taW5hbnRDYW52YXMoKSB7CiAgICAgICAgICAgIHZhciBsaXN0ID0gY29sbGVjdENhbnZhc2VzKCk7CiAgICAgICAgICAgIHZhciBiZXN0ID0gbnVsbDsKICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBsaXN0Lmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICAgICAgdmFyIGl0ZW0gPSBsaXN0W2ldOwogICAgICAgICAgICAgIHZhciBhcmVhID0gaXRlbS5yZWN0LndpZHRoICogaXRlbS5yZWN0LmhlaWdodDsKICAgICAgICAgICAgICBpZiAoIWJlc3QgfHwgYXJlYSA+IGJlc3QuYXJlYSkgewogICAgICAgICAgICAgICAgYmVzdCA9IHsKICAgICAgICAgICAgICAgICAgY2FudmFzOiBpdGVtLmNhbnZhcywKICAgICAgICAgICAgICAgICAgcmVjdDogaXRlbS5yZWN0LAogICAgICAgICAgICAgICAgICBhcmVhOiBhcmVhCiAgICAgICAgICAgICAgICB9OwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQogICAgICAgICAgICByZXR1cm4gYmVzdDsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBwYWdlQmFja2dyb3VuZENvbG9yKCkgewogICAgICAgICAgICB2YXIgbm9kZXMgPSBbZG9jdW1lbnQuYm9keSwgZG9jdW1lbnQuZG9jdW1lbnRFbGVtZW50XTsKICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBub2Rlcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIGlmICghbm9kZXNbaV0pIGNvbnRpbnVlOwogICAgICAgICAgICAgIHZhciBjb2xvciA9IHdpbmRvdy5nZXRDb21wdXRlZFN0eWxlID8gd2luZG93LmdldENvbXB1dGVkU3R5bGUobm9kZXNbaV0pLmJhY2tncm91bmRDb2xvciA6ICcnOwogICAgICAgICAgICAgIGlmIChjb2xvciAmJiBjb2xvciAhPT0gJ3RyYW5zcGFyZW50JyAmJiBjb2xvciAhPT0gJ3JnYmEoMCwgMCwgMCwgMCknKSByZXR1cm4gY29sb3I7CiAgICAgICAgICAgIH0KICAgICAgICAgICAgcmV0dXJuICcnOwogICAgICAgICAgfQoKICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogNS4g5oiq5Zu+5a6e546w77ya5oqK5omA5pyJ5Y+v6KeBIGNhbnZhcyDmjIkgRE9NIOmhuuW6j+WQiOaIkOWIsOS4gOW8oOeUu+W4gwogICAgICAgICAgICogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLSAqLwogICAgICAgICAgZnVuY3Rpb24gY2FwdHVyZVNjcmVlbnNob3QocGF5bG9hZCkgewogICAgICAgICAgICB2YXIgb3B0aW9ucyA9IHBheWxvYWQgfHwge307CiAgICAgICAgICAgIHZhciBpdGVtcyA9IGNvbGxlY3RDYW52YXNlcygpOwogICAgICAgICAgICBpZiAoIWl0ZW1zLmxlbmd0aCkgdGhyb3cgbmV3IEVycm9yKCflvZPliY3pobXpnaLmsqHmnInlj6/miKrlm77nmoQgY2FudmFzIOWFg+e0oCcpOwoKICAgICAgICAgICAgdmFyIG1pbkxlZnQgPSBJbmZpbml0eTsKICAgICAgICAgICAgdmFyIG1pblRvcCA9IEluZmluaXR5OwogICAgICAgICAgICB2YXIgbWF4UmlnaHQgPSAtSW5maW5pdHk7CiAgICAgICAgICAgIHZhciBtYXhCb3R0b20gPSAtSW5maW5pdHk7CiAgICAgICAgICAgIGZvciAodmFyIGkgPSAwOyBpIDwgaXRlbXMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICB2YXIgcmVjdCA9IGl0ZW1zW2ldLnJlY3Q7CiAgICAgICAgICAgICAgaWYgKHJlY3QubGVmdCA8IG1pbkxlZnQpIG1pbkxlZnQgPSByZWN0LmxlZnQ7CiAgICAgICAgICAgICAgaWYgKHJlY3QudG9wIDwgbWluVG9wKSBtaW5Ub3AgPSByZWN0LnRvcDsKICAgICAgICAgICAgICBpZiAocmVjdC5yaWdodCA+IG1heFJpZ2h0KSBtYXhSaWdodCA9IHJlY3QucmlnaHQ7CiAgICAgICAgICAgICAgaWYgKHJlY3QuYm90dG9tID4gbWF4Qm90dG9tKSBtYXhCb3R0b20gPSByZWN0LmJvdHRvbTsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgdmFyIHdpZHRoID0gbWF4UmlnaHQgLSBtaW5MZWZ0OwogICAgICAgICAgICB2YXIgaGVpZ2h0ID0gbWF4Qm90dG9tIC0gbWluVG9wOwogICAgICAgICAgICBpZiAoISh3aWR0aCA+IDApIHx8ICEoaGVpZ2h0ID4gMCkpIHRocm93IG5ldyBFcnJvcignY2FudmFzIOWwuuWvuOaXoOaViO+8jOaXoOazleaIquWbvicpOwoKICAgICAgICAgICAgdmFyIGRlZmF1bHRTY2FsZSA9IE1hdGgubWluKHdpbmRvdy5kZXZpY2VQaXhlbFJhdGlvIHx8IDEsIDIpOwogICAgICAgICAgICB2YXIgc2NhbGUgPSBjbGFtcE51bWJlcihvcHRpb25zLnNjYWxlLCAxLCAzLCBkZWZhdWx0U2NhbGUpOwogICAgICAgICAgICB2YXIgb3V0cHV0ID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgnY2FudmFzJyk7CiAgICAgICAgICAgIG91dHB1dC53aWR0aCA9IE1hdGgubWF4KDEsIE1hdGgucm91bmQod2lkdGggKiBzY2FsZSkpOwogICAgICAgICAgICBvdXRwdXQuaGVpZ2h0ID0gTWF0aC5tYXgoMSwgTWF0aC5yb3VuZChoZWlnaHQgKiBzY2FsZSkpOwoKICAgICAgICAgICAgdmFyIGNvbnRleHQgPSBvdXRwdXQuZ2V0Q29udGV4dCgnMmQnKTsKICAgICAgICAgICAgaWYgKCFjb250ZXh0KSB0aHJvdyBuZXcgRXJyb3IoJ+aXoOazleWIm+W7uiAyRCDnlLvluIPkuIrkuIvmlocnKTsKCiAgICAgICAgICAgIHZhciBiYWNrZ3JvdW5kID0gb3B0aW9ucy5iYWNrZ3JvdW5kIHx8IHBhZ2VCYWNrZ3JvdW5kQ29sb3IoKTsKICAgICAgICAgICAgaWYgKGJhY2tncm91bmQpIHsKICAgICAgICAgICAgICBjb250ZXh0LmZpbGxTdHlsZSA9IGJhY2tncm91bmQ7CiAgICAgICAgICAgICAgY29udGV4dC5maWxsUmVjdCgwLCAwLCBvdXRwdXQud2lkdGgsIG91dHB1dC5oZWlnaHQpOwogICAgICAgICAgICB9CgogICAgICAgICAgICBmb3IgKHZhciBqID0gMDsgaiA8IGl0ZW1zLmxlbmd0aDsgaiArPSAxKSB7CiAgICAgICAgICAgICAgdmFyIGl0ZW0gPSBpdGVtc1tqXTsKICAgICAgICAgICAgICB2YXIgb2Zmc2V0WCA9IChpdGVtLnJlY3QubGVmdCAtIG1pbkxlZnQpICogc2NhbGU7CiAgICAgICAgICAgICAgdmFyIG9mZnNldFkgPSAoaXRlbS5yZWN0LnRvcCAtIG1pblRvcCkgKiBzY2FsZTsKICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgY29udGV4dC5kcmF3SW1hZ2UoCiAgICAgICAgICAgICAgICAgIGl0ZW0uY2FudmFzLAogICAgICAgICAgICAgICAgICBvZmZzZXRYLAogICAgICAgICAgICAgICAgICBvZmZzZXRZLAogICAgICAgICAgICAgICAgICBpdGVtLnJlY3Qud2lkdGggKiBzY2FsZSwKICAgICAgICAgICAgICAgICAgaXRlbS5yZWN0LmhlaWdodCAqIHNjYWxlCiAgICAgICAgICAgICAgICApOwogICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICB0aHJvdyBuZXcgRXJyb3IoJ+ivu+WPliBjYW52YXMg5YOP57Sg5aSx6LSl77yaJyArIChlcnJvciAmJiBlcnJvci5tZXNzYWdlID8gZXJyb3IubWVzc2FnZSA6IGVycm9yKSk7CiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9CgogICAgICAgICAgICB2YXIgbWltZVR5cGUgPSBvcHRpb25zLm1pbWVUeXBlID09PSAnaW1hZ2UvanBlZycgPyAnaW1hZ2UvanBlZycgOiAnaW1hZ2UvcG5nJzsKICAgICAgICAgICAgdmFyIHF1YWxpdHkgPSBjbGFtcE51bWJlcihvcHRpb25zLnF1YWxpdHksIDAuMSwgMSwgMC45NSk7CiAgICAgICAgICAgIHZhciBkYXRhVXJsID0gb3V0cHV0LnRvRGF0YVVSTChtaW1lVHlwZSwgcXVhbGl0eSk7CgogICAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICAgIGRhdGFVcmw6IGRhdGFVcmwsCiAgICAgICAgICAgICAgbWltZVR5cGU6IG1pbWVUeXBlLAogICAgICAgICAgICAgIHdpZHRoOiBvdXRwdXQud2lkdGgsCiAgICAgICAgICAgICAgaGVpZ2h0OiBvdXRwdXQuaGVpZ2h0LAogICAgICAgICAgICAgIHNpemU6IGRhdGFVcmxCeXRlU2l6ZShkYXRhVXJsKQogICAgICAgICAgICB9OwogICAgICAgICAgfQoKICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogNi4g5b2V5Yi25a6e546w77yIY2FudmFzLmNhcHR1cmVTdHJlYW0gKyDpobXpnaLpn7PpopEgKyBNZWRpYVJlY29yZGVy77yJCiAgICAgICAgICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tICovCiAgICAgICAgICB2YXIgcmVjb3JkZXJTdGF0ZSA9IHsKICAgICAgICAgICAgcmVjb3JkZXI6IG51bGwsCiAgICAgICAgICAgIHN0cmVhbTogbnVsbCwKICAgICAgICAgICAgY2FudmFzU3RyZWFtOiBudWxsLAogICAgICAgICAgICBhdWRpb0NhcHR1cmU6IG51bGwsCiAgICAgICAgICAgIGNodW5rczogW10sCiAgICAgICAgICAgIG1pbWVUeXBlOiAnJywKICAgICAgICAgICAgc3RhcnRlZEF0OiAwLAogICAgICAgICAgICBwYXVzZWRBdDogMCwKICAgICAgICAgICAgcGF1c2VkRHVyYXRpb246IDAsCiAgICAgICAgICAgIHRpbWVySWQ6IDAsCiAgICAgICAgICAgIHN0b3BSZXF1ZXN0OiBudWxsLAogICAgICAgICAgICBmcHM6IDMwCiAgICAgICAgICB9OwoKICAgICAgICAgIGZ1bmN0aW9uIHJlY29yZGluZ0R1cmF0aW9uTXMoKSB7CiAgICAgICAgICAgIGlmICghcmVjb3JkZXJTdGF0ZS5yZWNvcmRlcikgcmV0dXJuIDA7CiAgICAgICAgICAgIHZhciBlbmQgPSByZWNvcmRlclN0YXRlLnBhdXNlZEF0IHx8IERhdGUubm93KCk7CiAgICAgICAgICAgIHJldHVybiBNYXRoLm1heCgwLCBlbmQgLSByZWNvcmRlclN0YXRlLnN0YXJ0ZWRBdCAtIHJlY29yZGVyU3RhdGUucGF1c2VkRHVyYXRpb24pOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHN0b3BQcm9ncmVzc1RpbWVyKCkgewogICAgICAgICAgICBpZiAocmVjb3JkZXJTdGF0ZS50aW1lcklkKSB7CiAgICAgICAgICAgICAgd2luZG93LmNsZWFySW50ZXJ2YWwocmVjb3JkZXJTdGF0ZS50aW1lcklkKTsKICAgICAgICAgICAgICByZWNvcmRlclN0YXRlLnRpbWVySWQgPSAwOwogICAgICAgICAgICB9CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gcG9zdFByb2dyZXNzKCkgewogICAgICAgICAgICBpZiAoIXJlY29yZGVyU3RhdGUucmVjb3JkZXIpIHJldHVybjsKICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdwcm9ncmVzcycsIG51bGwsICdyZWNvcmRpbmcnLCB7CiAgICAgICAgICAgICAgc3RhdGU6IHJlY29yZGVyU3RhdGUucGF1c2VkQXQgPyAncGF1c2VkJyA6ICdyZWNvcmRpbmcnLAogICAgICAgICAgICAgIGR1cmF0aW9uOiByZWNvcmRpbmdEdXJhdGlvbk1zKCkgLyAxMDAwCiAgICAgICAgICAgIH0pOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHN0YXJ0UHJvZ3Jlc3NUaW1lcigpIHsKICAgICAgICAgICAgc3RvcFByb2dyZXNzVGltZXIoKTsKICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS50aW1lcklkID0gd2luZG93LnNldEludGVydmFsKHBvc3RQcm9ncmVzcywgNTAwKTsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBzdG9wU3RyZWFtVHJhY2tzKHN0cmVhbSkgewogICAgICAgICAgICBpZiAoIXN0cmVhbSkgcmV0dXJuOwogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHZhciB0cmFja3MgPSBzdHJlYW0uZ2V0VHJhY2tzID8gc3RyZWFtLmdldFRyYWNrcygpIDogW107CiAgICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCB0cmFja3MubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICAgIHRyYWNrc1tpXS5zdG9wKCk7CiAgICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHN0b3BDYW52YXNTdHJlYW0oKSB7CiAgICAgICAgICAgIGlmICghcmVjb3JkZXJTdGF0ZS5jYW52YXNTdHJlYW0pIHJldHVybjsKICAgICAgICAgICAgc3RvcFN0cmVhbVRyYWNrcyhyZWNvcmRlclN0YXRlLmNhbnZhc1N0cmVhbSk7CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUuY2FudmFzU3RyZWFtID0gbnVsbDsKICAgICAgICAgIH0KCiAgICAgICAgICAvKioKICAgICAgICAgICAqIOmHh+mbhumhtemdouS4rSA8YXVkaW8+Lzx2aWRlbz4g5YWD57Sg55qE5aOw6Z+z44CCCiAgICAgICAgICAgKiDpgJrov4flhYPntKDnmoQgY2FwdHVyZVN0cmVhbSgpIOWPluW+l+mfs+mikei9qO+8jOS4jeaUueWPmOWFg+e0oOacrOi6q+eahOaSreaUvuS4jumfs+mHj+OAggogICAgICAgICAgICog5bey6KKrIGNyZWF0ZU1lZGlhRWxlbWVudFNvdXJjZSDmjqXlhaUgV2ViIEF1ZGlvIOeahOWFg+e0oOS8mui3s+i/h++8jOmBv+WFjemHjeWkjemHh+mbhuOAggogICAgICAgICAgICovCiAgICAgICAgICBmdW5jdGlvbiBjb2xsZWN0TWVkaWFFbGVtZW50QXVkaW8oYXVkaW9DYXB0dXJlKSB7CiAgICAgICAgICAgIHZhciBlbGVtZW50cyA9IGRvY3VtZW50LnF1ZXJ5U2VsZWN0b3JBbGwoJ2F1ZGlvLCB2aWRlbycpOwogICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IGVsZW1lbnRzLmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICAgICAgdmFyIGVsZW1lbnQgPSBlbGVtZW50c1tpXTsKICAgICAgICAgICAgICBpZiAod2ViQXVkaW9NZWRpYUVsZW1lbnRzICYmIHdlYkF1ZGlvTWVkaWFFbGVtZW50cy5oYXMoZWxlbWVudCkpIGNvbnRpbnVlOwoKICAgICAgICAgICAgICB2YXIgY2FwdHVyZSA9IGVsZW1lbnQuY2FwdHVyZVN0cmVhbSB8fCBlbGVtZW50Lm1vekNhcHR1cmVTdHJlYW07CiAgICAgICAgICAgICAgaWYgKHR5cGVvZiBjYXB0dXJlICE9PSAnZnVuY3Rpb24nKSBjb250aW51ZTsKCiAgICAgICAgICAgICAgdmFyIGVsZW1lbnRTdHJlYW07CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIGVsZW1lbnRTdHJlYW0gPSBjYXB0dXJlLmNhbGwoZWxlbWVudCk7CiAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgIGNvbnRpbnVlOwogICAgICAgICAgICAgIH0KCiAgICAgICAgICAgICAgdmFyIGF1ZGlvVHJhY2tzID0gZWxlbWVudFN0cmVhbS5nZXRBdWRpb1RyYWNrcyA/IGVsZW1lbnRTdHJlYW0uZ2V0QXVkaW9UcmFja3MoKSA6IFtdOwogICAgICAgICAgICAgIGlmICghYXVkaW9UcmFja3MubGVuZ3RoKSB7CiAgICAgICAgICAgICAgICBzdG9wU3RyZWFtVHJhY2tzKGVsZW1lbnRTdHJlYW0pOwogICAgICAgICAgICAgICAgY29udGludWU7CiAgICAgICAgICAgICAgfQoKICAgICAgICAgICAgICAvKiA8dmlkZW8+IOeahCB2aWRlbyDovajpgZPnlKjkuI3liLDvvIznq4vljbPlgZzmraLku6Xph4rmlL7otYTmupAgKi8KICAgICAgICAgICAgICB2YXIgdmlkZW9UcmFja3MgPSBlbGVtZW50U3RyZWFtLmdldFZpZGVvVHJhY2tzID8gZWxlbWVudFN0cmVhbS5nZXRWaWRlb1RyYWNrcygpIDogW107CiAgICAgICAgICAgICAgZm9yICh2YXIgaiA9IDA7IGogPCB2aWRlb1RyYWNrcy5sZW5ndGg7IGogKz0gMSkgewogICAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgICAgdmlkZW9UcmFja3Nbal0uc3RvcCgpOwogICAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgICB9CgogICAgICAgICAgICAgIGF1ZGlvQ2FwdHVyZS5tZWRpYVN0cmVhbXMucHVzaChlbGVtZW50U3RyZWFtKTsKICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUuc291cmNlcy5wdXNoKHsKICAgICAgICAgICAgICAgIHN0cmVhbTogZWxlbWVudFN0cmVhbSwKICAgICAgICAgICAgICAgIGtpbmQ6ICdtZWRpYScsCiAgICAgICAgICAgICAgICBlbGVtZW50OiBlbGVtZW50CiAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIH0KICAgICAgICAgIH0KCiAgICAgICAgICAvKioKICAgICAgICAgICAqIOmHh+mbhiBXZWIgQXVkaW8g5peB6Lev55qE6Z+z6aKR6L2o44CCCiAgICAgICAgICAgKiDljbPkvb/lvZPliY3ov5jmsqHmnInoioLngrnov57mjqXvvIzlj6ropoEgQXVkaW9Db250ZXh0IOW3suWcqOi/kOihjO+8jOS5n+WFiOe6s+WFpemHh+mbhu+8jAogICAgICAgICAgICog6L+Z5qC35b2V5Yi26L+H56iL5Lit5omN5byA5aeL5pKt5pS+55qE5aOw6Z+z5Lmf6IO96L+b5YWl5ZCM5LiA5Liq6Z+z6aKR6L2o44CCCiAgICAgICAgICAgKi8KICAgICAgICAgIGZ1bmN0aW9uIGNvbGxlY3RXZWJBdWRpb0F1ZGlvKGF1ZGlvQ2FwdHVyZSkgewogICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IGF1ZGlvVGFwUmVjb3Jkcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIHZhciByZWNvcmQgPSBhdWRpb1RhcFJlY29yZHNbaV07CiAgICAgICAgICAgICAgdmFyIGNvbnRleHQgPSByZWNvcmQuY29udGV4dDsKICAgICAgICAgICAgICBpZiAoIXJlY29yZC5ub2Rlcy5sZW5ndGggJiYgIShjb250ZXh0ICYmIGNvbnRleHQuc3RhdGUgPT09ICdydW5uaW5nJykpIGNvbnRpbnVlOwoKICAgICAgICAgICAgICB2YXIgdHJhY2tzID0gcmVjb3JkLmRlc3RpbmF0aW9uICYmCiAgICAgICAgICAgICAgICByZWNvcmQuZGVzdGluYXRpb24uc3RyZWFtICYmCiAgICAgICAgICAgICAgICByZWNvcmQuZGVzdGluYXRpb24uc3RyZWFtLmdldEF1ZGlvVHJhY2tzID8KICAgICAgICAgICAgICAgIHJlY29yZC5kZXN0aW5hdGlvbi5zdHJlYW0uZ2V0QXVkaW9UcmFja3MoKSA6CiAgICAgICAgICAgICAgICBbXTsKICAgICAgICAgICAgICBpZiAoIXRyYWNrcy5sZW5ndGgpIGNvbnRpbnVlOwoKICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUuc291cmNlcy5wdXNoKHsKICAgICAgICAgICAgICAgIHN0cmVhbTogcmVjb3JkLmRlc3RpbmF0aW9uLnN0cmVhbSwKICAgICAgICAgICAgICAgIGtpbmQ6ICd3ZWJhdWRpbycsCiAgICAgICAgICAgICAgICBjb250ZXh0OiBjb250ZXh0CiAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIH0KICAgICAgICAgIH0KCiAgICAgICAgICAvKioKICAgICAgICAgICAqIOWkmui3r+mfs+mikea3t+mfs++8mgogICAgICAgICAgICog5LyY5YWI5aSN55So6aG16Z2i5Lit5bey57uP6L+Q6KGM55qEIEF1ZGlvQ29udGV4dO+8iOmBv+WFjei3qOWfnyBpZnJhbWUg5paw5bu655qE5LiK5LiL5paH6KKrCiAgICAgICAgICAgKiDoh6rliqjmkq3mlL7nrZbnlaXmjILotbfvvInvvIzmiormr4/kuIDot6/pn7PpopHpgJrov4cgTWVkaWFTdHJlYW1BdWRpb1NvdXJjZU5vZGUg5rGH5YWlCiAgICAgICAgICAgKiDlkIzkuIDkuKogTWVkaWFTdHJlYW1BdWRpb0Rlc3RpbmF0aW9uTm9kZe+8jOacgOe7iOWPquWPluS4gOadoemfs+mikei9qOS6pOe7mQogICAgICAgICAgICogTWVkaWFSZWNvcmRlcu+8jOS7juiAjOWSjOinhumikei9qOWFseeUqOWQjOS4gOW9leWItuaXtumXtOi9tOOAggogICAgICAgICAgICovCiAgICAgICAgICBmdW5jdGlvbiBzZXR1cEF1ZGlvTWl4ZXIoYXVkaW9DYXB0dXJlKSB7CiAgICAgICAgICAgIHZhciBtaXhlckNvbnRleHQgPSBudWxsOwoKICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBhdWRpb1RhcFJlY29yZHMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICB2YXIgcmVjb3JkID0gYXVkaW9UYXBSZWNvcmRzW2ldOwogICAgICAgICAgICAgIGlmIChyZWNvcmQuY29udGV4dCAmJiByZWNvcmQuY29udGV4dC5zdGF0ZSA9PT0gJ3J1bm5pbmcnKSB7CiAgICAgICAgICAgICAgICBtaXhlckNvbnRleHQgPSByZWNvcmQuY29udGV4dDsKICAgICAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQoKICAgICAgICAgICAgdmFyIG93bnNDb250ZXh0ID0gZmFsc2U7CiAgICAgICAgICAgIGlmICghbWl4ZXJDb250ZXh0KSB7CiAgICAgICAgICAgICAgdmFyIEF1ZGlvQ29udGV4dEN0b3IgPSB3aW5kb3cuQXVkaW9Db250ZXh0IHx8IHdpbmRvdy53ZWJraXRBdWRpb0NvbnRleHQ7CiAgICAgICAgICAgICAgaWYgKEF1ZGlvQ29udGV4dEN0b3IpIHsKICAgICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICAgIG1peGVyQ29udGV4dCA9IG5ldyBBdWRpb0NvbnRleHRDdG9yKCk7CiAgICAgICAgICAgICAgICAgIG93bnNDb250ZXh0ID0gdHJ1ZTsKICAgICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICAgIG1peGVyQ29udGV4dCA9IG51bGw7CiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9CgogICAgICAgICAgICBpZiAoIW1peGVyQ29udGV4dCkgcmV0dXJuOwoKICAgICAgICAgICAgdmFyIG1peGVyRGVzdGluYXRpb24gPSBudWxsOwogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIG1peGVyRGVzdGluYXRpb24gPSBtaXhlckNvbnRleHQuY3JlYXRlTWVkaWFTdHJlYW1EZXN0aW5hdGlvbigpOwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIG1peGVyRGVzdGluYXRpb24gPSBudWxsOwogICAgICAgICAgICB9CgogICAgICAgICAgICBpZiAoIW1peGVyRGVzdGluYXRpb24pIHsKICAgICAgICAgICAgICBpZiAob3duc0NvbnRleHQpIHsKICAgICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICAgIG1peGVyQ29udGV4dC5jbG9zZSgpOwogICAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgICB9CgogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIGlmIChtaXhlckNvbnRleHQuc3RhdGUgPT09ICdzdXNwZW5kZWQnICYmIHR5cGVvZiBtaXhlckNvbnRleHQucmVzdW1lID09PSAnZnVuY3Rpb24nKSB7CiAgICAgICAgICAgICAgICB2YXIgcmVzdW1lUmVzdWx0ID0gbWl4ZXJDb250ZXh0LnJlc3VtZSgpOwogICAgICAgICAgICAgICAgaWYgKHJlc3VtZVJlc3VsdCAmJiB0eXBlb2YgcmVzdW1lUmVzdWx0LmNhdGNoID09PSAnZnVuY3Rpb24nKSB7CiAgICAgICAgICAgICAgICAgIHJlc3VtZVJlc3VsdC5jYXRjaChmdW5jdGlvbigpIHsKICAgICAgICAgICAgICAgICAgICAvKiDml6DnlKjmiLfmiYvlir/ml7blj6/og73nu6fnu63kv53mjIHmjILotbcgKi8gfSk7CiAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CgogICAgICAgICAgICB2YXIgbWl4ZXJOb2RlcyA9IFtdOwogICAgICAgICAgICBmb3IgKHZhciBqID0gMDsgaiA8IGF1ZGlvQ2FwdHVyZS5zb3VyY2VzLmxlbmd0aDsgaiArPSAxKSB7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIHZhciBzb3VyY2VOb2RlID0gbWl4ZXJDb250ZXh0LmNyZWF0ZU1lZGlhU3RyZWFtU291cmNlKGF1ZGlvQ2FwdHVyZS5zb3VyY2VzW2pdLnN0cmVhbSk7CiAgICAgICAgICAgICAgICBzb3VyY2VOb2RlLmNvbm5lY3QobWl4ZXJEZXN0aW5hdGlvbik7CiAgICAgICAgICAgICAgICBtaXhlck5vZGVzLnB1c2goc291cmNlTm9kZSk7CiAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgIC8qIOWNlei3r+Wksei0peS4jeW9seWTjeWFtuS7lumfs+mikSAqLyB9CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIHZhciBtaXhlZFRyYWNrcyA9IG1peGVyRGVzdGluYXRpb24uc3RyZWFtICYmIG1peGVyRGVzdGluYXRpb24uc3RyZWFtLmdldEF1ZGlvVHJhY2tzID8KICAgICAgICAgICAgICBtaXhlckRlc3RpbmF0aW9uLnN0cmVhbS5nZXRBdWRpb1RyYWNrcygpIDoKICAgICAgICAgICAgICBbXTsKCiAgICAgICAgICAgIGlmIChtaXhlck5vZGVzLmxlbmd0aCAmJiBtaXhlZFRyYWNrcy5sZW5ndGgpIHsKICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUudHJhY2sgPSBtaXhlZFRyYWNrc1swXTsKICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUubW9kZSA9ICdtaXhlZCc7CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1peGVyQ29udGV4dCA9IG1peGVyQ29udGV4dDsKICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUubWl4ZXJEZXN0aW5hdGlvbiA9IG1peGVyRGVzdGluYXRpb247CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1peGVyTm9kZXMgPSBtaXhlck5vZGVzOwogICAgICAgICAgICAgIGF1ZGlvQ2FwdHVyZS5vd25zQ29udGV4dCA9IG93bnNDb250ZXh0OwogICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgZm9yICh2YXIgayA9IDA7IGsgPCBtaXhlck5vZGVzLmxlbmd0aDsgayArPSAxKSB7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIG1peGVyTm9kZXNba10uZGlzY29ubmVjdCgpOwogICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgICB9CiAgICAgICAgICAgIHN0b3BTdHJlYW1UcmFja3MobWl4ZXJEZXN0aW5hdGlvbi5zdHJlYW0pOwogICAgICAgICAgICBpZiAob3duc0NvbnRleHQpIHsKICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgbWl4ZXJDb250ZXh0LmNsb3NlKCk7CiAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIHZhciBmYWxsYmFja1RyYWNrcyA9IGF1ZGlvQ2FwdHVyZS5zb3VyY2VzWzBdLnN0cmVhbS5nZXRBdWRpb1RyYWNrcyA/CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLnNvdXJjZXNbMF0uc3RyZWFtLmdldEF1ZGlvVHJhY2tzKCkgOgogICAgICAgICAgICAgIFtdOwogICAgICAgICAgICBpZiAoZmFsbGJhY2tUcmFja3MubGVuZ3RoKSB7CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLnRyYWNrID0gZmFsbGJhY2tUcmFja3NbMF07CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1vZGUgPSAnZGlyZWN0LWZhbGxiYWNrJzsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGNyZWF0ZVJlY29yZGluZ0F1ZGlvQ2FwdHVyZSgpIHsKICAgICAgICAgICAgdmFyIGF1ZGlvQ2FwdHVyZSA9IHsKICAgICAgICAgICAgICBtb2RlOiAnbm9uZScsCiAgICAgICAgICAgICAgdHJhY2s6IG51bGwsCiAgICAgICAgICAgICAgc291cmNlQ291bnQ6IDAsCiAgICAgICAgICAgICAgc291cmNlczogW10sCiAgICAgICAgICAgICAgbWVkaWFTdHJlYW1zOiBbXSwKICAgICAgICAgICAgICBtaXhlckNvbnRleHQ6IG51bGwsCiAgICAgICAgICAgICAgbWl4ZXJEZXN0aW5hdGlvbjogbnVsbCwKICAgICAgICAgICAgICBtaXhlck5vZGVzOiBbXSwKICAgICAgICAgICAgICBvd25zQ29udGV4dDogZmFsc2UsCiAgICAgICAgICAgICAgZGlzcG9zZWQ6IGZhbHNlLAogICAgICAgICAgICAgIGRpc3Bvc2U6IG51bGwKICAgICAgICAgICAgfTsKCiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgY29sbGVjdE1lZGlhRWxlbWVudEF1ZGlvKGF1ZGlvQ2FwdHVyZSk7CiAgICAgICAgICAgICAgY29sbGVjdFdlYkF1ZGlvQXVkaW8oYXVkaW9DYXB0dXJlKTsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAvKiDpn7PpopHph4fpm4blvILluLjkuI3lupTpmLvmlq3op4bpopHlvZXliLYgKi8gfQoKICAgICAgICAgICAgYXVkaW9DYXB0dXJlLnNvdXJjZUNvdW50ID0gYXVkaW9DYXB0dXJlLnNvdXJjZXMubGVuZ3RoOwoKICAgICAgICAgICAgaWYgKGF1ZGlvQ2FwdHVyZS5zb3VyY2VzLmxlbmd0aCA9PT0gMSkgewogICAgICAgICAgICAgIHZhciBzaW5nbGVUcmFja3MgPSBhdWRpb0NhcHR1cmUuc291cmNlc1swXS5zdHJlYW0uZ2V0QXVkaW9UcmFja3MgPwogICAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLnNvdXJjZXNbMF0uc3RyZWFtLmdldEF1ZGlvVHJhY2tzKCkgOgogICAgICAgICAgICAgICAgW107CiAgICAgICAgICAgICAgaWYgKHNpbmdsZVRyYWNrcy5sZW5ndGgpIHsKICAgICAgICAgICAgICAgIGF1ZGlvQ2FwdHVyZS50cmFjayA9IHNpbmdsZVRyYWNrc1swXTsKICAgICAgICAgICAgICAgIGF1ZGlvQ2FwdHVyZS5tb2RlID0gJ2RpcmVjdCc7CiAgICAgICAgICAgICAgfQogICAgICAgICAgICB9IGVsc2UgaWYgKGF1ZGlvQ2FwdHVyZS5zb3VyY2VzLmxlbmd0aCA+IDEpIHsKICAgICAgICAgICAgICBzZXR1cEF1ZGlvTWl4ZXIoYXVkaW9DYXB0dXJlKTsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgYXVkaW9DYXB0dXJlLmRpc3Bvc2UgPSBmdW5jdGlvbigpIHsKICAgICAgICAgICAgICBpZiAoYXVkaW9DYXB0dXJlLmRpc3Bvc2VkKSByZXR1cm47CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLmRpc3Bvc2VkID0gdHJ1ZTsKCiAgICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBhdWRpb0NhcHR1cmUubWl4ZXJOb2Rlcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1peGVyTm9kZXNbaV0uZGlzY29ubmVjdCgpOwogICAgICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1peGVyTm9kZXMgPSBbXTsKCiAgICAgICAgICAgICAgaWYgKGF1ZGlvQ2FwdHVyZS5taXhlckRlc3RpbmF0aW9uKSB7CiAgICAgICAgICAgICAgICBzdG9wU3RyZWFtVHJhY2tzKGF1ZGlvQ2FwdHVyZS5taXhlckRlc3RpbmF0aW9uLnN0cmVhbSk7CiAgICAgICAgICAgICAgfQoKICAgICAgICAgICAgICBpZiAoYXVkaW9DYXB0dXJlLm93bnNDb250ZXh0ICYmIGF1ZGlvQ2FwdHVyZS5taXhlckNvbnRleHQpIHsKICAgICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICAgIHZhciBjbG9zZVJlc3VsdCA9IGF1ZGlvQ2FwdHVyZS5taXhlckNvbnRleHQuY2xvc2UoKTsKICAgICAgICAgICAgICAgICAgaWYgKGNsb3NlUmVzdWx0ICYmIHR5cGVvZiBjbG9zZVJlc3VsdC5jYXRjaCA9PT0gJ2Z1bmN0aW9uJykgewogICAgICAgICAgICAgICAgICAgIGNsb3NlUmVzdWx0LmNhdGNoKGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgICAgICAgICAgLyog5b+955WlICovIH0pOwogICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgICAgIH0KCiAgICAgICAgICAgICAgZm9yICh2YXIgaiA9IDA7IGogPCBhdWRpb0NhcHR1cmUubWVkaWFTdHJlYW1zLmxlbmd0aDsgaiArPSAxKSB7CiAgICAgICAgICAgICAgICBzdG9wU3RyZWFtVHJhY2tzKGF1ZGlvQ2FwdHVyZS5tZWRpYVN0cmVhbXNbal0pOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUubWVkaWFTdHJlYW1zID0gW107CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1peGVyQ29udGV4dCA9IG51bGw7CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLm1peGVyRGVzdGluYXRpb24gPSBudWxsOwogICAgICAgICAgICAgIGF1ZGlvQ2FwdHVyZS50cmFjayA9IG51bGw7CiAgICAgICAgICAgIH07CgogICAgICAgICAgICByZXR1cm4gYXVkaW9DYXB0dXJlOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHJlc2V0UmVjb3JkZXJTdGF0ZSgpIHsKICAgICAgICAgICAgc3RvcFByb2dyZXNzVGltZXIoKTsKICAgICAgICAgICAgaWYgKHJlY29yZGVyU3RhdGUuYXVkaW9DYXB0dXJlKSB7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIHJlY29yZGVyU3RhdGUuYXVkaW9DYXB0dXJlLmRpc3Bvc2UoKTsKICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgICByZWNvcmRlclN0YXRlLmF1ZGlvQ2FwdHVyZSA9IG51bGw7CiAgICAgICAgICAgIH0KICAgICAgICAgICAgc3RvcENhbnZhc1N0cmVhbSgpOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLnJlY29yZGVyID0gbnVsbDsKICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS5zdHJlYW0gPSBudWxsOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLmNodW5rcyA9IFtdOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLm1pbWVUeXBlID0gJyc7CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUuc3RhcnRlZEF0ID0gMDsKICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS5wYXVzZWRBdCA9IDA7CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUucGF1c2VkRHVyYXRpb24gPSAwOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLnN0b3BSZXF1ZXN0ID0gbnVsbDsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBwaWNrTWltZVR5cGUocHJlZmVycmVkKSB7CiAgICAgICAgICAgIGlmICghd2luZG93Lk1lZGlhUmVjb3JkZXIpIHJldHVybiAnJzsKICAgICAgICAgICAgdmFyIGNhbmRpZGF0ZXMgPSBbXTsKICAgICAgICAgICAgaWYgKHByZWZlcnJlZCkgY2FuZGlkYXRlcy5wdXNoKHByZWZlcnJlZCk7CiAgICAgICAgICAgIGNhbmRpZGF0ZXMgPSBjYW5kaWRhdGVzLmNvbmNhdChbCiAgICAgICAgICAgICAgJ3ZpZGVvL3dlYm07Y29kZWNzPXZwOScsCiAgICAgICAgICAgICAgJ3ZpZGVvL3dlYm07Y29kZWNzPXZwOCcsCiAgICAgICAgICAgICAgJ3ZpZGVvL3dlYm0nLAogICAgICAgICAgICAgICd2aWRlby9tcDQ7Y29kZWNzPWF2YzEuNDJFMDFFJywKICAgICAgICAgICAgICAndmlkZW8vbXA0JwogICAgICAgICAgICBdKTsKCiAgICAgICAgICAgIGZvciAodmFyIGkgPSAwOyBpIDwgY2FuZGlkYXRlcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIHZhciBjYW5kaWRhdGUgPSBjYW5kaWRhdGVzW2ldOwogICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICBpZiAodHlwZW9mIHdpbmRvdy5NZWRpYVJlY29yZGVyLmlzVHlwZVN1cHBvcnRlZCAhPT0gJ2Z1bmN0aW9uJyB8fAogICAgICAgICAgICAgICAgICB3aW5kb3cuTWVkaWFSZWNvcmRlci5pc1R5cGVTdXBwb3J0ZWQoY2FuZGlkYXRlKSkgewogICAgICAgICAgICAgICAgICByZXR1cm4gY2FuZGlkYXRlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICAvKiDnu6fnu63lsJ3or5UgKi8gfQogICAgICAgICAgICB9CiAgICAgICAgICAgIHJldHVybiAnJzsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBibG9iVG9EYXRhVXJsKGJsb2IsIGNhbGxiYWNrKSB7CiAgICAgICAgICAgIGlmICghd2luZG93LkZpbGVSZWFkZXIpIHsKICAgICAgICAgICAgICBjYWxsYmFjaygnJyk7CiAgICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgICB9CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgdmFyIHJlYWRlciA9IG5ldyBGaWxlUmVhZGVyKCk7CiAgICAgICAgICAgICAgcmVhZGVyLm9ubG9hZCA9IGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgICAgY2FsbGJhY2sodHlwZW9mIHJlYWRlci5yZXN1bHQgPT09ICdzdHJpbmcnID8gcmVhZGVyLnJlc3VsdCA6ICcnKTsKICAgICAgICAgICAgICB9OwogICAgICAgICAgICAgIHJlYWRlci5vbmVycm9yID0gZnVuY3Rpb24oKSB7CiAgICAgICAgICAgICAgICBjYWxsYmFjaygnJyk7CiAgICAgICAgICAgICAgfTsKICAgICAgICAgICAgICByZWFkZXIucmVhZEFzRGF0YVVSTChibG9iKTsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICBjYWxsYmFjaygnJyk7CiAgICAgICAgICAgIH0KICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBmaW5hbGl6ZVJlY29yZGluZygpIHsKICAgICAgICAgICAgdmFyIHN0b3BSZXF1ZXN0ID0gcmVjb3JkZXJTdGF0ZS5zdG9wUmVxdWVzdDsKICAgICAgICAgICAgdmFyIG1pbWVUeXBlID0gcmVjb3JkZXJTdGF0ZS5taW1lVHlwZSB8fCAndmlkZW8vd2VibSc7CiAgICAgICAgICAgIHZhciBkdXJhdGlvbiA9IHJlY29yZGluZ0R1cmF0aW9uTXMoKSAvIDEwMDA7CiAgICAgICAgICAgIHZhciBjaHVua3MgPSByZWNvcmRlclN0YXRlLmNodW5rcy5zbGljZSgwKTsKICAgICAgICAgICAgdmFyIGJsb2IgPSBudWxsOwoKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICBibG9iID0gbmV3IEJsb2IoY2h1bmtzLCB7CiAgICAgICAgICAgICAgICB0eXBlOiBtaW1lVHlwZQogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIGJsb2IgPSBudWxsOwogICAgICAgICAgICB9CgogICAgICAgICAgICByZXNldFJlY29yZGVyU3RhdGUoKTsKCiAgICAgICAgICAgIC8qIOaWh+S7tuWQjeeUseeItue7hOS7tuaMiSBhY2NvdW50RGF0YS50aXRsZSDnu5/kuIDnlJ/miJDvvIzov5nph4zlj6rlm57kvKDmlbDmja7mnKzouqvjgIIgKi8KICAgICAgICAgICAgdmFyIHBheWxvYWQgPSB7CiAgICAgICAgICAgICAgbWltZVR5cGU6IG1pbWVUeXBlLAogICAgICAgICAgICAgIGR1cmF0aW9uOiBkdXJhdGlvbiwKICAgICAgICAgICAgICBzaXplOiBibG9iID8gYmxvYi5zaXplIDogMAogICAgICAgICAgICB9OwoKICAgICAgICAgICAgaWYgKCFzdG9wUmVxdWVzdCkgewogICAgICAgICAgICAgIC8qIOmdnue7hOS7tuS4u+WKqOWBnOatou+8iOS+i+WmgumhtemdoumUgOavge+8ieaXtu+8jOWPquWQjOatpeeKtuaAgSAqLwogICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnc3RhdGUnLCBudWxsLCAncmVjb3JkaW5nJywgewogICAgICAgICAgICAgICAgc3RhdGU6ICdzdG9wcGVkJywKICAgICAgICAgICAgICAgIGR1cmF0aW9uOiBkdXJhdGlvbiwKICAgICAgICAgICAgICAgIHNpemU6IHBheWxvYWQuc2l6ZQogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgaWYgKGJsb2IpIHsKICAgICAgICAgICAgICBwYXlsb2FkLmJsb2IgPSBibG9iOwogICAgICAgICAgICAgIGlmIChwb3N0VG9QYXJlbnQoJ3N1Y2Nlc3MnLCBzdG9wUmVxdWVzdC5pZCwgJ3N0b3BSZWNvcmRpbmcnLCBwYXlsb2FkKSkgcmV0dXJuOwoKICAgICAgICAgICAgICAvKiDmnoHnq6/mg4XlhrXkuIsgQmxvYiDml6Dms5Xnu5PmnoTljJblhYvpmobml7bvvIzlm57pgIDmiJAgZGF0YVVSTCAqLwogICAgICAgICAgICAgIGJsb2JUb0RhdGFVcmwoYmxvYiwgZnVuY3Rpb24oZGF0YVVybCkgewogICAgICAgICAgICAgICAgaWYgKCFkYXRhVXJsKSB7CiAgICAgICAgICAgICAgICAgIHBvc3RFcnJvcihzdG9wUmVxdWVzdC5pZCwgJ3N0b3BSZWNvcmRpbmcnLCBuZXcgRXJyb3IoJ+aXoOazleS8oOi+k+W9leWItuaVsOaNricpLCAnVFJBTlNGRVJfRkFJTEVEJyk7CiAgICAgICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgIGRlbGV0ZSBwYXlsb2FkLmJsb2I7CiAgICAgICAgICAgICAgICBwYXlsb2FkLmRhdGFVcmwgPSBkYXRhVXJsOwogICAgICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdzdWNjZXNzJywgc3RvcFJlcXVlc3QuaWQsICdzdG9wUmVjb3JkaW5nJywgcGF5bG9hZCk7CiAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgICB9CgogICAgICAgICAgICBwb3N0RXJyb3Ioc3RvcFJlcXVlc3QuaWQsICdzdG9wUmVjb3JkaW5nJywgbmV3IEVycm9yKCflvZXliLbmlbDmja7kuLrnqbonKSwgJ0VNUFRZX1JFQ09SRElORycpOwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHN0YXJ0UmVjb3JkaW5nKHBheWxvYWQpIHsKICAgICAgICAgICAgdmFyIG9wdGlvbnMgPSBwYXlsb2FkIHx8IHt9OwogICAgICAgICAgICBpZiAocmVjb3JkZXJTdGF0ZS5yZWNvcmRlcikgdGhyb3cgbmV3IEVycm9yKCflvZPliY3lt7LmnInlvZXliLbku7vliqHmraPlnKjov5vooYwnKTsKICAgICAgICAgICAgaWYgKCF3aW5kb3cuTWVkaWFSZWNvcmRlcikgdGhyb3cgbmV3IEVycm9yKCflvZPliY3mtY/op4jlmajkuI3mlK/mjIEgTWVkaWFSZWNvcmRlciDlvZXliLYnKTsKCiAgICAgICAgICAgIHZhciB0YXJnZXQgPSBkb21pbmFudENhbnZhcygpOwogICAgICAgICAgICBpZiAoIXRhcmdldCkgdGhyb3cgbmV3IEVycm9yKCflvZPliY3pobXpnaLmsqHmnInlj6/nlKjkuo7lvZXliLbnmoQgY2FudmFzIOWFg+e0oCcpOwogICAgICAgICAgICB2YXIgY2FudmFzID0gdGFyZ2V0LmNhbnZhczsKICAgICAgICAgICAgaWYgKHR5cGVvZiBjYW52YXMuY2FwdHVyZVN0cmVhbSAhPT0gJ2Z1bmN0aW9uJykgdGhyb3cgbmV3IEVycm9yKCflvZPliY3mtY/op4jlmajkuI3mlK/mjIEgY2FudmFzLmNhcHR1cmVTdHJlYW0nKTsKCiAgICAgICAgICAgIHZhciBmcHMgPSBNYXRoLnJvdW5kKGNsYW1wTnVtYmVyKG9wdGlvbnMuZnBzLCAxLCA2MCwgMzApKTsKICAgICAgICAgICAgdmFyIGJpdHJhdGUgPSBNYXRoLnJvdW5kKGNsYW1wTnVtYmVyKG9wdGlvbnMuYml0cmF0ZSwgMjUwMDAwLCA1MDAwMDAwMCwgMTIwMDAwMDApKTsKICAgICAgICAgICAgdmFyIGNhbnZhc1N0cmVhbTsKCiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgY2FudmFzU3RyZWFtID0gY2FudmFzLmNhcHR1cmVTdHJlYW0oZnBzKTsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICB0aHJvdyBuZXcgRXJyb3IoJ+aXoOazleWIm+W7uuinhumikea1ge+8micgKyAoZXJyb3IgJiYgZXJyb3IubWVzc2FnZSA/IGVycm9yLm1lc3NhZ2UgOiBlcnJvcikpOwogICAgICAgICAgICB9CgogICAgICAgICAgICAvKiDlvZXliLblvIDlp4vml7bkuIDmrKHmgKfmjqXlhaXpobXpnaLnjrDmnInpn7PpopHvvJvpn7PpopHovajlkozop4bpopHovajkuIDotbfkuqTnu5nlkIzkuIDkuKoKICAgICAgICAgICAgICogTWVkaWFSZWNvcmRlcu+8jOS6jOiAheWFseeUqOWQjOS4gOaXtumXtOi9tO+8jOmBv+WFjeWIhuWIq+W9leWItuWQjuWGjeWvuem9kOOAgiAqLwogICAgICAgICAgICB2YXIgYXVkaW9DYXB0dXJlID0gbnVsbDsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUgPSBjcmVhdGVSZWNvcmRpbmdBdWRpb0NhcHR1cmUoKTsKICAgICAgICAgICAgICBpZiAoYXVkaW9DYXB0dXJlICYmIGF1ZGlvQ2FwdHVyZS50cmFjayAmJiBhdWRpb0NhcHR1cmUudHJhY2sucmVhZHlTdGF0ZSA9PT0gJ2VuZGVkJykgewogICAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlLnRyYWNrID0gbnVsbDsKICAgICAgICAgICAgICAgIGF1ZGlvQ2FwdHVyZS5tb2RlID0gJ2VuZGVkJzsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgYXVkaW9DYXB0dXJlID0gbnVsbDsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgdmFyIGNvbWJpbmVkVHJhY2tzID0gW107CiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgdmFyIGNhbnZhc1ZpZGVvVHJhY2tzID0gY2FudmFzU3RyZWFtLmdldFZpZGVvVHJhY2tzID8gY2FudmFzU3RyZWFtLmdldFZpZGVvVHJhY2tzKCkgOiBbXTsKICAgICAgICAgICAgICBmb3IgKHZhciB0ID0gMDsgdCA8IGNhbnZhc1ZpZGVvVHJhY2tzLmxlbmd0aDsgdCArPSAxKSBjb21iaW5lZFRyYWNrcy5wdXNoKGNhbnZhc1ZpZGVvVHJhY2tzW3RdKTsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQoKICAgICAgICAgICAgaWYgKCFjb21iaW5lZFRyYWNrcy5sZW5ndGgpIHsKICAgICAgICAgICAgICBzdG9wU3RyZWFtVHJhY2tzKGNhbnZhc1N0cmVhbSk7CiAgICAgICAgICAgICAgaWYgKGF1ZGlvQ2FwdHVyZSkgYXVkaW9DYXB0dXJlLmRpc3Bvc2UoKTsKICAgICAgICAgICAgICB0aHJvdyBuZXcgRXJyb3IoJ+aXoOazleS7jiBjYW52YXMg6I635Y+W6KeG6aKR6L2o6YGTJyk7CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIGlmIChhdWRpb0NhcHR1cmUgJiYgYXVkaW9DYXB0dXJlLnRyYWNrKSBjb21iaW5lZFRyYWNrcy5wdXNoKGF1ZGlvQ2FwdHVyZS50cmFjayk7CgogICAgICAgICAgICB2YXIgc3RyZWFtOwogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHN0cmVhbSA9IG5ldyB3aW5kb3cuTWVkaWFTdHJlYW0oY29tYmluZWRUcmFja3MpOwogICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgIHN0b3BTdHJlYW1UcmFja3MoY2FudmFzU3RyZWFtKTsKICAgICAgICAgICAgICBpZiAoYXVkaW9DYXB0dXJlKSBhdWRpb0NhcHR1cmUuZGlzcG9zZSgpOwogICAgICAgICAgICAgIHRocm93IG5ldyBFcnJvcign5peg5rOV57uE5ZCI6Z+z6KeG6aKR5rWB77yaJyArIChlcnJvciAmJiBlcnJvci5tZXNzYWdlID8gZXJyb3IubWVzc2FnZSA6IGVycm9yKSk7CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIHZhciBtaW1lVHlwZSA9IHBpY2tNaW1lVHlwZShvcHRpb25zLm1pbWVUeXBlKTsKICAgICAgICAgICAgdmFyIHJlY29yZGVyT3B0aW9ucyA9IHsKICAgICAgICAgICAgICB2aWRlb0JpdHNQZXJTZWNvbmQ6IGJpdHJhdGUKICAgICAgICAgICAgfTsKICAgICAgICAgICAgaWYgKGF1ZGlvQ2FwdHVyZSAmJiBhdWRpb0NhcHR1cmUudHJhY2spIHsKICAgICAgICAgICAgICByZWNvcmRlck9wdGlvbnMuYXVkaW9CaXRzUGVyU2Vjb25kID0gTWF0aC5yb3VuZCgKICAgICAgICAgICAgICAgIGNsYW1wTnVtYmVyKG9wdGlvbnMuYXVkaW9CaXRyYXRlLCAzMjAwMCwgMzIwMDAwLCAxMjgwMDApCiAgICAgICAgICAgICAgKTsKICAgICAgICAgICAgfQogICAgICAgICAgICBpZiAobWltZVR5cGUpIHJlY29yZGVyT3B0aW9ucy5taW1lVHlwZSA9IG1pbWVUeXBlOwoKICAgICAgICAgICAgdmFyIHJlY29yZGVyID0gbnVsbDsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICByZWNvcmRlciA9IG5ldyB3aW5kb3cuTWVkaWFSZWNvcmRlcihzdHJlYW0sIHJlY29yZGVyT3B0aW9ucyk7CiAgICAgICAgICAgIH0gY2F0Y2ggKGZpcnN0RXJyb3IpIHsKICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgcmVjb3JkZXIgPSBuZXcgd2luZG93Lk1lZGlhUmVjb3JkZXIoc3RyZWFtKTsKICAgICAgICAgICAgICB9IGNhdGNoIChzZWNvbmRFcnJvcikgewogICAgICAgICAgICAgICAgc3RvcFN0cmVhbVRyYWNrcyhjYW52YXNTdHJlYW0pOwogICAgICAgICAgICAgICAgaWYgKGF1ZGlvQ2FwdHVyZSkgYXVkaW9DYXB0dXJlLmRpc3Bvc2UoKTsKICAgICAgICAgICAgICAgIHRocm93IG5ldyBFcnJvcign5b2V5Yi25Yid5aeL5YyW5aSx6LSl77yaJyArCiAgICAgICAgICAgICAgICAgIChzZWNvbmRFcnJvciAmJiBzZWNvbmRFcnJvci5tZXNzYWdlID8gc2Vjb25kRXJyb3IubWVzc2FnZSA6IFN0cmluZyhzZWNvbmRFcnJvciB8fCBmaXJzdEVycm9yKSkpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQoKICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS5yZWNvcmRlciA9IHJlY29yZGVyOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLnN0cmVhbSA9IHN0cmVhbTsKICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS5jYW52YXNTdHJlYW0gPSBjYW52YXNTdHJlYW07CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUuYXVkaW9DYXB0dXJlID0gYXVkaW9DYXB0dXJlOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLmNodW5rcyA9IFtdOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLm1pbWVUeXBlID0gcmVjb3JkZXIubWltZVR5cGUgfHwgbWltZVR5cGUgfHwgJ3ZpZGVvL3dlYm0nOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLnN0YXJ0ZWRBdCA9IERhdGUubm93KCk7CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUucGF1c2VkQXQgPSAwOwogICAgICAgICAgICByZWNvcmRlclN0YXRlLnBhdXNlZER1cmF0aW9uID0gMDsKICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS5zdG9wUmVxdWVzdCA9IG51bGw7CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUuZnBzID0gZnBzOwoKICAgICAgICAgICAgcmVjb3JkZXIub25kYXRhYXZhaWxhYmxlID0gZnVuY3Rpb24oZXZlbnQpIHsKICAgICAgICAgICAgICBpZiAoZXZlbnQuZGF0YSAmJiBldmVudC5kYXRhLnNpemUgPiAwKSByZWNvcmRlclN0YXRlLmNodW5rcy5wdXNoKGV2ZW50LmRhdGEpOwogICAgICAgICAgICB9OwoKICAgICAgICAgICAgcmVjb3JkZXIub25lcnJvciA9IGZ1bmN0aW9uKGV2ZW50KSB7CiAgICAgICAgICAgICAgdmFyIG1lc3NhZ2UgPSAn5b2V5Yi26L+H56iL5Lit5Y+R55Sf6ZSZ6K+vJzsKICAgICAgICAgICAgICBpZiAoZXZlbnQgJiYgZXZlbnQuZXJyb3IgJiYgZXZlbnQuZXJyb3IubWVzc2FnZSkgbWVzc2FnZSArPSAn77yaJyArIGV2ZW50LmVycm9yLm1lc3NhZ2U7CiAgICAgICAgICAgICAgdmFyIHN0b3BSZXF1ZXN0ID0gcmVjb3JkZXJTdGF0ZS5zdG9wUmVxdWVzdDsKICAgICAgICAgICAgICByZWNvcmRlci5vbnN0b3AgPSBudWxsOwogICAgICAgICAgICAgIHJlc2V0UmVjb3JkZXJTdGF0ZSgpOwogICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnZXJyb3InLCBzdG9wUmVxdWVzdCAmJiBzdG9wUmVxdWVzdC5pZCwgJ3N0b3BSZWNvcmRpbmcnLCB7CiAgICAgICAgICAgICAgICBtZXNzYWdlOiBtZXNzYWdlLAogICAgICAgICAgICAgICAgY29kZTogJ1JFQ09SREVSX0VSUk9SJwogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICB9OwoKICAgICAgICAgICAgcmVjb3JkZXIub25zdG9wID0gZnVuY3Rpb24oKSB7CiAgICAgICAgICAgICAgZmluYWxpemVSZWNvcmRpbmcoKTsKICAgICAgICAgICAgfTsKCiAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgcmVjb3JkZXIuc3RhcnQoMTAwMCk7CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgcmVzZXRSZWNvcmRlclN0YXRlKCk7CiAgICAgICAgICAgICAgdGhyb3cgbmV3IEVycm9yKCflvZXliLblkK/liqjlpLHotKXvvJonICsgKGVycm9yICYmIGVycm9yLm1lc3NhZ2UgPyBlcnJvci5tZXNzYWdlIDogZXJyb3IpKTsKICAgICAgICAgICAgfQoKICAgICAgICAgICAgc3RhcnRQcm9ncmVzc1RpbWVyKCk7CgogICAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICAgIG1pbWVUeXBlOiByZWNvcmRlclN0YXRlLm1pbWVUeXBlLAogICAgICAgICAgICAgIGZwczogZnBzLAogICAgICAgICAgICAgIGJpdHJhdGU6IGJpdHJhdGUsCiAgICAgICAgICAgICAgc3RhcnRlZEF0OiByZWNvcmRlclN0YXRlLnN0YXJ0ZWRBdCwKICAgICAgICAgICAgICBhdWRpbzogewogICAgICAgICAgICAgICAgaW5jbHVkZWQ6ICEhKGF1ZGlvQ2FwdHVyZSAmJiBhdWRpb0NhcHR1cmUudHJhY2spLAogICAgICAgICAgICAgICAgbW9kZTogYXVkaW9DYXB0dXJlID8gYXVkaW9DYXB0dXJlLm1vZGUgOiAnbm9uZScsCiAgICAgICAgICAgICAgICBzb3VyY2VDb3VudDogYXVkaW9DYXB0dXJlID8gYXVkaW9DYXB0dXJlLnNvdXJjZUNvdW50IDogMCwKICAgICAgICAgICAgICAgIGNvbnRleHRTdGF0ZTogYXVkaW9DYXB0dXJlICYmIGF1ZGlvQ2FwdHVyZS5taXhlckNvbnRleHQgPwogICAgICAgICAgICAgICAgICBhdWRpb0NhcHR1cmUubWl4ZXJDb250ZXh0LnN0YXRlIDoKICAgICAgICAgICAgICAgICAgbnVsbAogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfTsKICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBzdG9wUmVjb3JkaW5nKGlkLCBhY3Rpb24pIHsKICAgICAgICAgICAgaWYgKCFyZWNvcmRlclN0YXRlLnJlY29yZGVyKSB0aHJvdyBuZXcgRXJyb3IoJ+W9k+WJjeayoeacieato+WcqOi/m+ihjOeahOW9leWIticpOwogICAgICAgICAgICBpZiAocmVjb3JkZXJTdGF0ZS5zdG9wUmVxdWVzdCkgdGhyb3cgbmV3IEVycm9yKCfmraPlnKjlpITnkIblgZzmraLor7fmsYLvvIzor7fnqI3lgJknKTsKICAgICAgICAgICAgaWYgKHJlY29yZGVyU3RhdGUucmVjb3JkZXIuc3RhdGUgPT09ICdpbmFjdGl2ZScpIHRocm93IG5ldyBFcnJvcign5b2V5Yi25bey57uP57uT5p2fJyk7CgogICAgICAgICAgICByZWNvcmRlclN0YXRlLnN0b3BSZXF1ZXN0ID0gewogICAgICAgICAgICAgIGlkOiBpZCwKICAgICAgICAgICAgICBhY3Rpb246IGFjdGlvbgogICAgICAgICAgICB9OwogICAgICAgICAgICBzdG9wUHJvZ3Jlc3NUaW1lcigpOwoKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICByZWNvcmRlclN0YXRlLnJlY29yZGVyLnN0b3AoKTsKICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICByZWNvcmRlclN0YXRlLnN0b3BSZXF1ZXN0ID0gbnVsbDsKICAgICAgICAgICAgICBzdGFydFByb2dyZXNzVGltZXIoKTsKICAgICAgICAgICAgICB0aHJvdyBlcnJvcjsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHBhdXNlUmVjb3JkaW5nKCkgewogICAgICAgICAgICBpZiAoIXJlY29yZGVyU3RhdGUucmVjb3JkZXIpIHRocm93IG5ldyBFcnJvcign5b2T5YmN5rKh5pyJ5q2j5Zyo6L+b6KGM55qE5b2V5Yi2Jyk7CiAgICAgICAgICAgIGlmIChyZWNvcmRlclN0YXRlLnBhdXNlZEF0KSB7CiAgICAgICAgICAgICAgcmV0dXJuIHsKICAgICAgICAgICAgICAgIHBhdXNlZDogdHJ1ZSwKICAgICAgICAgICAgICAgIGR1cmF0aW9uOiByZWNvcmRpbmdEdXJhdGlvbk1zKCkgLyAxMDAwCiAgICAgICAgICAgICAgfTsKICAgICAgICAgICAgfQogICAgICAgICAgICBpZiAocmVjb3JkZXJTdGF0ZS5yZWNvcmRlci5zdGF0ZSAhPT0gJ3JlY29yZGluZycpIHRocm93IG5ldyBFcnJvcign5b2T5YmN5b2V5Yi254q25oCB5LiN5pSv5oyB5pqC5YGcJyk7CgogICAgICAgICAgICByZWNvcmRlclN0YXRlLnBhdXNlZEF0ID0gRGF0ZS5ub3coKTsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICByZWNvcmRlclN0YXRlLnJlY29yZGVyLnBhdXNlKCk7CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgcmVjb3JkZXJTdGF0ZS5wYXVzZWRBdCA9IDA7CiAgICAgICAgICAgICAgdGhyb3cgZXJyb3I7CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIHN0b3BQcm9ncmVzc1RpbWVyKCk7CiAgICAgICAgICAgIHBvc3RQcm9ncmVzcygpOwogICAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICAgIHBhdXNlZDogdHJ1ZSwKICAgICAgICAgICAgICBkdXJhdGlvbjogcmVjb3JkaW5nRHVyYXRpb25NcygpIC8gMTAwMAogICAgICAgICAgICB9OwogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIHJlc3VtZVJlY29yZGluZygpIHsKICAgICAgICAgICAgaWYgKCFyZWNvcmRlclN0YXRlLnJlY29yZGVyKSB0aHJvdyBuZXcgRXJyb3IoJ+W9k+WJjeayoeacieato+WcqOi/m+ihjOeahOW9leWIticpOwogICAgICAgICAgICBpZiAoIXJlY29yZGVyU3RhdGUucGF1c2VkQXQpIHsKICAgICAgICAgICAgICByZXR1cm4gewogICAgICAgICAgICAgICAgcGF1c2VkOiBmYWxzZSwKICAgICAgICAgICAgICAgIGR1cmF0aW9uOiByZWNvcmRpbmdEdXJhdGlvbk1zKCkgLyAxMDAwCiAgICAgICAgICAgICAgfTsKICAgICAgICAgICAgfQogICAgICAgICAgICBpZiAocmVjb3JkZXJTdGF0ZS5yZWNvcmRlci5zdGF0ZSAhPT0gJ3BhdXNlZCcpIHRocm93IG5ldyBFcnJvcign5b2T5YmN5b2V5Yi254q25oCB5LiN5pSv5oyB57un57utJyk7CgogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHJlY29yZGVyU3RhdGUucmVjb3JkZXIucmVzdW1lKCk7CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgdGhyb3cgZXJyb3I7CiAgICAgICAgICAgIH0KCiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUucGF1c2VkRHVyYXRpb24gKz0gRGF0ZS5ub3coKSAtIHJlY29yZGVyU3RhdGUucGF1c2VkQXQ7CiAgICAgICAgICAgIHJlY29yZGVyU3RhdGUucGF1c2VkQXQgPSAwOwogICAgICAgICAgICBzdGFydFByb2dyZXNzVGltZXIoKTsKICAgICAgICAgICAgcG9zdFByb2dyZXNzKCk7CiAgICAgICAgICAgIHJldHVybiB7CiAgICAgICAgICAgICAgcGF1c2VkOiBmYWxzZSwKICAgICAgICAgICAgICBkdXJhdGlvbjogcmVjb3JkaW5nRHVyYXRpb25NcygpIC8gMTAwMAogICAgICAgICAgICB9OwogICAgICAgICAgfQoKICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogNy4gaWZyYW1lIOmhtemdouW8guW4uOaNleiOtwogICAgICAgICAgICog55uR5ZCs6ISa5pys6ZSZ6K+v44CB5pyq5aSE55CGIFByb21pc2XjgIHotYTmupDliqDovb3lpLHotKXkuI4gQ2FudmFzIOeUu+W4g+S4iuS4i+aWh+S4ouWkse+8jAogICAgICAgICAgICog6YCa6L+HIHBvc3RNZXNzYWdlIOS4iuaKpee7mee7hOS7tu+8m+WQjOaXtue8k+WtmOacgOi/kSA1MCDmnaHkvpvnu4Tku7booaXmi4njgIIKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIHZhciBwYWdlRXJyb3JTZXEgPSAwOwogICAgICAgICAgdmFyIHBhZ2VFcnJvckhpc3RvcnkgPSBbXTsKICAgICAgICAgIHZhciBwYWdlRXJyb3JSZWNlbnQgPSB7fTsKICAgICAgICAgIHZhciBwYWdlVG9rZW4gPSAncC0nICsgRGF0ZS5ub3coKS50b1N0cmluZygzNikgKyAnLScgKyBNYXRoLnJhbmRvbSgpLnRvU3RyaW5nKDM2KS5zbGljZSgyLCA4KTsKCiAgICAgICAgICBmdW5jdGlvbiBzYW5pdGl6ZUVycm9yVGV4dCh2YWx1ZSwgbWF4TGVuZ3RoKSB7CiAgICAgICAgICAgIHZhciB0ZXh0ID0gdmFsdWUgPT09IG51bGwgfHwgdmFsdWUgPT09IHVuZGVmaW5lZCA/ICcnIDogU3RyaW5nKHZhbHVlKTsKICAgICAgICAgICAgdGV4dCA9IHRleHQucmVwbGFjZSgvZGF0YTpbXlxzIicpXSsvZywgZnVuY3Rpb24obWF0Y2gpIHsKICAgICAgICAgICAgICByZXR1cm4gbWF0Y2gubGVuZ3RoID4gNDggPyBtYXRjaC5zbGljZSgwLCAzNikgKyAn4oCmJyA6IG1hdGNoOwogICAgICAgICAgICB9KTsKICAgICAgICAgICAgdmFyIGxpbWl0ID0gbWF4TGVuZ3RoIHx8IDIwMDA7CiAgICAgICAgICAgIGlmICh0ZXh0Lmxlbmd0aCA+IGxpbWl0KSB0ZXh0ID0gdGV4dC5zbGljZSgwLCBsaW1pdCkgKyAn4oCmJzsKICAgICAgICAgICAgcmV0dXJuIHRleHQ7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gc2FuaXRpemVFcnJvclNvdXJjZSh2YWx1ZSkgewogICAgICAgICAgICB2YXIgdGV4dCA9IHNhbml0aXplRXJyb3JUZXh0KHZhbHVlLCA4MDApOwogICAgICAgICAgICBpZiAoL15kYXRhOi9pLnRlc3QodGV4dCkpIHJldHVybiAnZGF0YTp0ZXh0L2h0bWzvvIhpZnJhbWUg5YaF6IGU6ISa5pys77yJJzsKICAgICAgICAgICAgcmV0dXJuIHRleHQ7CiAgICAgICAgICB9CgogICAgICAgICAgZnVuY3Rpb24gcmVjb3JkUGFnZUVycm9yKHR5cGUsIGRldGFpbHMpIHsKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICB2YXIgaW5mbyA9IGRldGFpbHMgfHwge307CiAgICAgICAgICAgICAgdmFyIG1lc3NhZ2UgPSBzYW5pdGl6ZUVycm9yVGV4dChpbmZvLm1lc3NhZ2UsIDIwMDApIHx8ICfmnKrnn6XlvILluLgnOwogICAgICAgICAgICAgIHZhciBzb3VyY2UgPSBzYW5pdGl6ZUVycm9yU291cmNlKGluZm8uc291cmNlKTsKICAgICAgICAgICAgICB2YXIgbGluZSA9IE51bWJlcihpbmZvLmxpbmUpIHx8IDA7CiAgICAgICAgICAgICAgdmFyIGNvbHVtbiA9IE51bWJlcihpbmZvLmNvbHVtbikgfHwgMDsKICAgICAgICAgICAgICB2YXIgc3RhY2sgPSBzYW5pdGl6ZUVycm9yVGV4dChpbmZvLnN0YWNrLCA2MDAwKTsKICAgICAgICAgICAgICB2YXIgbm93ID0gRGF0ZS5ub3coKTsKCiAgICAgICAgICAgICAgdmFyIGRlZHVwZUtleSA9IHR5cGUgKyAnfCcgKyBtZXNzYWdlICsgJ3wnICsgc291cmNlICsgJ3wnICsgbGluZSArICd8JyArIGNvbHVtbjsKICAgICAgICAgICAgICB2YXIgbGFzdEF0ID0gcGFnZUVycm9yUmVjZW50W2RlZHVwZUtleV0gfHwgMDsKICAgICAgICAgICAgICBpZiAobm93IC0gbGFzdEF0IDwgMjAwMCkgcmV0dXJuOwogICAgICAgICAgICAgIHBhZ2VFcnJvclJlY2VudFtkZWR1cGVLZXldID0gbm93OwoKICAgICAgICAgICAgICAvKiDlrprmnJ/muIXnkIbov4fmnJ/nmoTljrvph43orrDlvZUgKi8KICAgICAgICAgICAgICB2YXIgcmVjZW50S2V5cyA9IE9iamVjdC5rZXlzKHBhZ2VFcnJvclJlY2VudCk7CiAgICAgICAgICAgICAgaWYgKHJlY2VudEtleXMubGVuZ3RoID4gMTAwKSB7CiAgICAgICAgICAgICAgICBmb3IgKHZhciBpID0gMDsgaSA8IHJlY2VudEtleXMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICAgICAgaWYgKG5vdyAtIHBhZ2VFcnJvclJlY2VudFtyZWNlbnRLZXlzW2ldXSA+IDEwMDAwKSBkZWxldGUgcGFnZUVycm9yUmVjZW50W3JlY2VudEtleXNbaV1dOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgIH0KCiAgICAgICAgICAgICAgaWYgKHBhZ2VFcnJvckhpc3RvcnkubGVuZ3RoID49IDUwKSByZXR1cm47CgogICAgICAgICAgICAgIHBhZ2VFcnJvclNlcSArPSAxOwogICAgICAgICAgICAgIHZhciBwYXlsb2FkID0gewogICAgICAgICAgICAgICAgcGFnZUVycm9ySWQ6IHBhZ2VFcnJvclNlcSwKICAgICAgICAgICAgICAgIHBhZ2VUb2tlbjogcGFnZVRva2VuLAogICAgICAgICAgICAgICAgdHlwZTogdHlwZSwKICAgICAgICAgICAgICAgIG1lc3NhZ2U6IG1lc3NhZ2UsCiAgICAgICAgICAgICAgICBzb3VyY2U6IHNvdXJjZSwKICAgICAgICAgICAgICAgIGxpbmU6IGxpbmUsCiAgICAgICAgICAgICAgICBjb2x1bW46IGNvbHVtbiwKICAgICAgICAgICAgICAgIHN0YWNrOiBzdGFjaywKICAgICAgICAgICAgICAgIHRpbWVzdGFtcDogbm93CiAgICAgICAgICAgICAgfTsKCiAgICAgICAgICAgICAgcGFnZUVycm9ySGlzdG9yeS5wdXNoKHBheWxvYWQpOwogICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgncGFnZS1lcnJvcicsIG51bGwsICdwYWdlRXJyb3InLCBwYXlsb2FkKTsKICAgICAgICAgICAgfSBjYXRjaCAoaWdub3JlKSB7CiAgICAgICAgICAgICAgLyog5byC5bi45o2V6I635pys6Lqr5LiN6IO95b2x5ZON6aG16Z2iICovIH0KICAgICAgICAgIH0KCiAgICAgICAgICBmdW5jdGlvbiBpbnN0YWxsUGFnZUVycm9ySGFuZGxlcnMoKSB7CiAgICAgICAgICAgIHdpbmRvdy5hZGRFdmVudExpc3RlbmVyKCdlcnJvcicsIGZ1bmN0aW9uKGV2ZW50KSB7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIHZhciB0YXJnZXQgPSBldmVudCAmJiBldmVudC50YXJnZXQ7CiAgICAgICAgICAgICAgICAvKiDmjZXojrfpmLbmrrXlj6/ku6XlkIzml7bmi7/liLDotYTmupDnsbvplJnor6/vvIhzY3JpcHQvbGluay9pbWcvYXVkaW8vdmlkZW8g562J77yJICovCiAgICAgICAgICAgICAgICBpZiAodGFyZ2V0ICYmIHRhcmdldCAhPT0gd2luZG93ICYmIHRhcmdldCAhPT0gZG9jdW1lbnQgJiYgdGFyZ2V0LnRhZ05hbWUpIHsKICAgICAgICAgICAgICAgICAgdmFyIHRhZ05hbWUgPSBTdHJpbmcodGFyZ2V0LnRhZ05hbWUpLnRvTG93ZXJDYXNlKCk7CiAgICAgICAgICAgICAgICAgIHZhciByZXNvdXJjZVVybCA9IHRhcmdldC5jdXJyZW50U3JjIHx8IHRhcmdldC5zcmMgfHwgdGFyZ2V0LmhyZWYgfHwgJyc7CiAgICAgICAgICAgICAgICAgIHJlY29yZFBhZ2VFcnJvcigncmVzb3VyY2UnLCB7CiAgICAgICAgICAgICAgICAgICAgbWVzc2FnZTogJ+i1hOa6kOWKoOi9veWksei0pe+8mjwnICsgdGFnTmFtZSArICc+ICcgKyBzYW5pdGl6ZUVycm9yVGV4dChyZXNvdXJjZVVybCwgNTAwKSwKICAgICAgICAgICAgICAgICAgICBzb3VyY2U6IHJlc291cmNlVXJsCiAgICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgICAgICByZXR1cm47CiAgICAgICAgICAgICAgICB9CgogICAgICAgICAgICAgICAgdmFyIGVycm9yID0gZXZlbnQgJiYgZXZlbnQuZXJyb3I7CiAgICAgICAgICAgICAgICByZWNvcmRQYWdlRXJyb3IoJ2Vycm9yJywgewogICAgICAgICAgICAgICAgICBtZXNzYWdlOiAoZXZlbnQgJiYgZXZlbnQubWVzc2FnZSkgfHwgKGVycm9yICYmIGVycm9yLm1lc3NhZ2UpIHx8ICfmnKrnn6XohJrmnKzplJnor68nLAogICAgICAgICAgICAgICAgICBzb3VyY2U6IChldmVudCAmJiBldmVudC5maWxlbmFtZSkgfHwgJycsCiAgICAgICAgICAgICAgICAgIGxpbmU6IChldmVudCAmJiBldmVudC5saW5lbm8pIHx8IDAsCiAgICAgICAgICAgICAgICAgIGNvbHVtbjogKGV2ZW50ICYmIGV2ZW50LmNvbG5vKSB8fCAwLAogICAgICAgICAgICAgICAgICBzdGFjazogZXJyb3IgJiYgZXJyb3Iuc3RhY2sgPyBlcnJvci5zdGFjayA6ICcnCiAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICB9IGNhdGNoIChpZ25vcmUpIHsKICAgICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CiAgICAgICAgICAgIH0sIHRydWUpOwoKICAgICAgICAgICAgd2luZG93LmFkZEV2ZW50TGlzdGVuZXIoJ3VuaGFuZGxlZHJlamVjdGlvbicsIGZ1bmN0aW9uKGV2ZW50KSB7CiAgICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICAgIHZhciByZWFzb24gPSBldmVudCAmJiBldmVudC5yZWFzb247CiAgICAgICAgICAgICAgICB2YXIgbWVzc2FnZSA9IHJlYXNvbiAmJiByZWFzb24ubWVzc2FnZSA/CiAgICAgICAgICAgICAgICAgIHJlYXNvbi5tZXNzYWdlIDoKICAgICAgICAgICAgICAgICAgU3RyaW5nKHJlYXNvbiA9PT0gdW5kZWZpbmVkID8gJ+epuueahCBQcm9taXNlIOaLkue7nScgOiByZWFzb24pOwogICAgICAgICAgICAgICAgcmVjb3JkUGFnZUVycm9yKCdwcm9taXNlJywgewogICAgICAgICAgICAgICAgICBtZXNzYWdlOiBtZXNzYWdlLAogICAgICAgICAgICAgICAgICBzdGFjazogcmVhc29uICYmIHJlYXNvbi5zdGFjayA/IHJlYXNvbi5zdGFjayA6ICcnCiAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICB9IGNhdGNoIChpZ25vcmUpIHsKICAgICAgICAgICAgICAgIC8qIOW/veeVpSAqLyB9CiAgICAgICAgICAgIH0pOwoKCiAgICAgICAgICB9CgogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiA4LiBMZWFmZXJKUyDpppbluKflsLHnu6rmo4DmtYvvvIhzY2VuZS1yZWFkee+8iQogICAgICAgICAgICog5LiN5L6d6LWWIExlYWZlciDlhoXpg6jnp4HmnInlr7nosaHmiJYgTGVhZmVySlMg5riy5p+T5ZmoIEhvb2vjgILpgJrov4cgRE9NIOS4reecn+WungogICAgICAgICAgICogY2FudmFzIOeahOWwuuWvuOOAgeWPr+ingeaAp+WPiiBDYW52YXMgMkQg5YOP57Sg5YaF5a655Yik5pat6aaW5bin77yM5YW85a65IExlYWZlckpTCiAgICAgICAgICAgKiAyLngg55qEIFVNRCAvIEVTTSDmnoTlu7rmlrnlvI/jgILnqbrnmb3nlLvluIPkuI3kvJrooqvor6/liKTkuLrlt7LlsLHnu6rjgIIKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGNhbnZhc0hhc1BhaW50ZWQoY2FudmFzKSB7CiAgICAgICAgICAgIGlmICghY2FudmFzIHx8ICFjYW52YXMud2lkdGggfHwgIWNhbnZhcy5oZWlnaHQpIHJldHVybiBmYWxzZTsKICAgICAgICAgICAgdmFyIHJlY3QgPSBjYW52YXMuZ2V0Qm91bmRpbmdDbGllbnRSZWN0ID8gY2FudmFzLmdldEJvdW5kaW5nQ2xpZW50UmVjdCgpIDogbnVsbDsKICAgICAgICAgICAgaWYgKHJlY3QgJiYgKHJlY3Qud2lkdGggPD0gMCB8fCByZWN0LmhlaWdodCA8PSAwKSkgcmV0dXJuIGZhbHNlOwogICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgIHZhciBjb250ZXh0ID0gY2FudmFzLmdldENvbnRleHQgJiYgY2FudmFzLmdldENvbnRleHQoJzJkJyk7CiAgICAgICAgICAgICAgaWYgKCFjb250ZXh0IHx8IHR5cGVvZiBjb250ZXh0LmdldEltYWdlRGF0YSAhPT0gJ2Z1bmN0aW9uJykgewogICAgICAgICAgICAgICAgLyog5peg5rOV6K+75Y+W5YOP57Sg5pe277yM5Y+q5Lul5bey5Yib5bu65LiU5Y+v6KeB55qE55S75biD5L2c5Li66ZmN57qn5p2h5Lu244CCICovCiAgICAgICAgICAgICAgICByZXR1cm4gISEocmVjdCAmJiByZWN0LndpZHRoID4gMCAmJiByZWN0LmhlaWdodCA+IDApOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgICB2YXIgcG9pbnRzID0gWwogICAgICAgICAgICAgICAgW01hdGguZmxvb3IoY2FudmFzLndpZHRoICogMC41KSwgTWF0aC5mbG9vcihjYW52YXMuaGVpZ2h0ICogMC41KV0sCiAgICAgICAgICAgICAgICBbTWF0aC5mbG9vcihjYW52YXMud2lkdGggKiAwLjI1KSwgTWF0aC5mbG9vcihjYW52YXMuaGVpZ2h0ICogMC4yNSldLAogICAgICAgICAgICAgICAgW01hdGguZmxvb3IoY2FudmFzLndpZHRoICogMC43NSksIE1hdGguZmxvb3IoY2FudmFzLmhlaWdodCAqIDAuNzUpXQogICAgICAgICAgICAgIF07CiAgICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBwb2ludHMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICAgIHZhciB4ID0gTWF0aC5tYXgoMCwgTWF0aC5taW4oY2FudmFzLndpZHRoIC0gMSwgcG9pbnRzW2ldWzBdKSk7CiAgICAgICAgICAgICAgICB2YXIgeSA9IE1hdGgubWF4KDAsIE1hdGgubWluKGNhbnZhcy5oZWlnaHQgLSAxLCBwb2ludHNbaV1bMV0pKTsKICAgICAgICAgICAgICAgIHZhciBwaXhlbCA9IGNvbnRleHQuZ2V0SW1hZ2VEYXRhKHgsIHksIDEsIDEpLmRhdGE7CiAgICAgICAgICAgICAgICBpZiAocGl4ZWxbM10gPiAwKSByZXR1cm4gdHJ1ZTsKICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgLyog5pyJ5Lqb5Yqo55S76IOM5pmv6YCP5piO77yM5oq95qC354K55Y+v6IO95oGw5aW96YCP5piO77yb55S75biD5bey5a6M5oiQ5biD5bGA5pe277yMCiAgICAgICAgICAgICAgICog5YaN6YCa6L+H5aSa5Liq5Yy65Z+f55qE5L2O5YiG6L6o546H6YeH5qC35qOA5p+l5piv5ZCm5a2Y5Zyo6Z2e6YCP5piO5YOP57Sg44CCICovCiAgICAgICAgICAgICAgdmFyIHNhbXBsZVcgPSBNYXRoLm1pbigyNCwgY2FudmFzLndpZHRoKTsKICAgICAgICAgICAgICB2YXIgc2FtcGxlSCA9IE1hdGgubWluKDI0LCBjYW52YXMuaGVpZ2h0KTsKICAgICAgICAgICAgICB2YXIgc2FtcGxlID0gY29udGV4dC5nZXRJbWFnZURhdGEoMCwgMCwgc2FtcGxlVywgc2FtcGxlSCkuZGF0YTsKICAgICAgICAgICAgICBmb3IgKHZhciBwID0gMzsgcCA8IHNhbXBsZS5sZW5ndGg7IHAgKz0gNCkgewogICAgICAgICAgICAgICAgaWYgKHNhbXBsZVtwXSA+IDApIHJldHVybiB0cnVlOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgICByZXR1cm4gZmFsc2U7CiAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgLyog5YOP57Sg6K+75Y+W5Y+X6ZmQ5pe25LiN6Zi75aGe6aKE6KeI77ya55S75biD5a2Y5Zyo5LiU5Y+v6KeB5Y2z5Y+v6ZmN57qn5bCx57uq44CCICovCiAgICAgICAgICAgICAgcmV0dXJuICEhKHJlY3QgJiYgcmVjdC53aWR0aCA+IDAgJiYgcmVjdC5oZWlnaHQgPiAwKTsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIG5vdGlmeVNjZW5lUmVhZHlJZk5lZWRlZCgpIHsKICAgICAgICAgICAgaWYgKHNjZW5lUmVhZHlOb3RpZmllZCkgcmV0dXJuOwogICAgICAgICAgICB2YXIgY2FudmFzZXMgPSBkb2N1bWVudC5xdWVyeVNlbGVjdG9yQWxsKCdjYW52YXMnKTsKICAgICAgICAgICAgZm9yICh2YXIgaSA9IDA7IGkgPCBjYW52YXNlcy5sZW5ndGg7IGkgKz0gMSkgewogICAgICAgICAgICAgIHZhciBjYW52YXMgPSBjYW52YXNlc1tpXTsKICAgICAgICAgICAgICBpZiAoIWNhbnZhc0hhc1BhaW50ZWQoY2FudmFzKSkgY29udGludWU7CiAgICAgICAgICAgICAgc2NlbmVSZWFkeU5vdGlmaWVkID0gdHJ1ZTsKICAgICAgICAgICAgICBoaWRlSW5qZWN0ZWRMb2FkaW5nT3ZlcmxheSgpOwogICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnc2NlbmUtcmVhZHknLCBudWxsLCAnc2NlbmVSZWFkeScsIHsKICAgICAgICAgICAgICAgIHJlYWR5OiB0cnVlLAogICAgICAgICAgICAgICAgZW5naW5lOiAnTGVhZmVySlMnLAogICAgICAgICAgICAgICAgcmVuZGVyZXI6ICdDYW52YXMgMkQnLAogICAgICAgICAgICAgICAgd2lkdGg6IGNhbnZhcy53aWR0aCwKICAgICAgICAgICAgICAgIGhlaWdodDogY2FudmFzLmhlaWdodAogICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgICAgfQogICAgICAgICAgfQoKICAgICAgICAgIGZ1bmN0aW9uIGluc3RhbGxMZWFmZXJDYW52YXNSZWFkeVdhdGNoKCkgewogICAgICAgICAgICB2YXIgYXR0ZW1wdHMgPSAwOwogICAgICAgICAgICB2YXIgbWF4QXR0ZW1wdHMgPSA2MDA7IC8vIOacgOmVv+e6piAzMCDnp5LvvIzmjIkgNTBtcyDmo4Dmn6UKICAgICAgICAgICAgdmFyIHRpbWVyID0gd2luZG93LnNldEludGVydmFsKGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGF0dGVtcHRzICs9IDE7CiAgICAgICAgICAgICAgbm90aWZ5U2NlbmVSZWFkeUlmTmVlZGVkKCk7CiAgICAgICAgICAgICAgaWYgKHNjZW5lUmVhZHlOb3RpZmllZCB8fCBhdHRlbXB0cyA+PSBtYXhBdHRlbXB0cykgewogICAgICAgICAgICAgICAgd2luZG93LmNsZWFySW50ZXJ2YWwodGltZXIpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfSwgNTApOwogICAgICAgICAgfQoKICAgICAgICAgIC8qIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLQogICAgICAgICAgICogOS4g56e75Yqo56uv6KeG5Y+j5bC65a+45a6I5oqkCiAgICAgICAgICAgKiBpT1MgLyBBbmRyb2lkIOeahOWcsOWdgOagj+OAgei9r+mUruebmOOAgeaWueWQkeWIh+aNouS8muiuqSBpZnJhbWUg6KeG5Y+j5Zyo6aaW5bin5LmL5ZCOCiAgICAgICAgICAgKiDmiY3nqLPlrprkuIvmnaXvvJvov5nph4zlnKjop4blj6Plj5jljJbml7YKICAgICAgICAgICAqIOS4u+WKqOa0vuWPkSByZXNpemUg5LqL5Lu277yM5bm25bu26L+f6KGl5Y+R5Yeg5qyh44CCCiAgICAgICAgICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tICovCiAgICAgICAgICBmdW5jdGlvbiBpbnN0YWxsVmlld3BvcnRXYXRjaGRvZygpIHsKICAgICAgICAgICAgdmFyIGxhc3RXaWR0aCA9IC0xOwogICAgICAgICAgICB2YXIgbGFzdEhlaWdodCA9IC0xOwoKICAgICAgICAgICAgZnVuY3Rpb24gZGlzcGF0Y2hSZXNpemUoZm9yY2UpIHsKICAgICAgICAgICAgICB2YXIgd2lkdGggPSB3aW5kb3cuaW5uZXJXaWR0aCB8fCAwOwogICAgICAgICAgICAgIHZhciBoZWlnaHQgPSB3aW5kb3cuaW5uZXJIZWlnaHQgfHwgMDsKICAgICAgICAgICAgICBpZiAoIWZvcmNlICYmIHdpZHRoID09PSBsYXN0V2lkdGggJiYgaGVpZ2h0ID09PSBsYXN0SGVpZ2h0KSByZXR1cm47CiAgICAgICAgICAgICAgbGFzdFdpZHRoID0gd2lkdGg7CiAgICAgICAgICAgICAgbGFzdEhlaWdodCA9IGhlaWdodDsKICAgICAgICAgICAgICB0cnkgewogICAgICAgICAgICAgICAgdmFyIGV2ZW50OwogICAgICAgICAgICAgICAgaWYgKHR5cGVvZiB3aW5kb3cuRXZlbnQgPT09ICdmdW5jdGlvbicpIHsKICAgICAgICAgICAgICAgICAgZXZlbnQgPSBuZXcgd2luZG93LkV2ZW50KCdyZXNpemUnKTsKICAgICAgICAgICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAgICAgICAgIGV2ZW50ID0gZG9jdW1lbnQuY3JlYXRlRXZlbnQoJ0V2ZW50Jyk7CiAgICAgICAgICAgICAgICAgIGV2ZW50LmluaXRFdmVudCgncmVzaXplJywgZmFsc2UsIGZhbHNlKTsKICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgIHdpbmRvdy5kaXNwYXRjaEV2ZW50KGV2ZW50KTsKICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgfQoKICAgICAgICAgICAgZnVuY3Rpb24gY2hlY2tDYW52YXNTaXplKCkgewogICAgICAgICAgICAgIHZhciBjYW52YXNlcyA9IGRvY3VtZW50LnF1ZXJ5U2VsZWN0b3JBbGwoJ2NhbnZhcycpOwogICAgICAgICAgICAgIGZvciAodmFyIGkgPSAwOyBpIDwgY2FudmFzZXMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgICAgIGlmICghY2FudmFzZXNbaV0ud2lkdGggfHwgIWNhbnZhc2VzW2ldLmhlaWdodCkgewogICAgICAgICAgICAgICAgICBkaXNwYXRjaFJlc2l6ZSh0cnVlKTsKICAgICAgICAgICAgICAgICAgcmV0dXJuOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfQoKICAgICAgICAgICAgaWYgKHdpbmRvdy5SZXNpemVPYnNlcnZlcikgewogICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICB2YXIgb2JzZXJ2ZXIgPSBuZXcgd2luZG93LlJlc2l6ZU9ic2VydmVyKGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgICAgICBkaXNwYXRjaFJlc2l6ZShmYWxzZSk7CiAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICAgIG9ic2VydmVyLm9ic2VydmUoZG9jdW1lbnQuZG9jdW1lbnRFbGVtZW50KTsKICAgICAgICAgICAgICAgIGlmIChkb2N1bWVudC5ib2R5KSBvYnNlcnZlci5vYnNlcnZlKGRvY3VtZW50LmJvZHkpOwogICAgICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgICB9CgogICAgICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcignb3JpZW50YXRpb25jaGFuZ2UnLCBmdW5jdGlvbigpIHsKICAgICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChmdW5jdGlvbigpIHsKICAgICAgICAgICAgICAgIGRpc3BhdGNoUmVzaXplKHRydWUpOwogICAgICAgICAgICAgIH0sIDI1MCk7CiAgICAgICAgICAgIH0pOwoKICAgICAgICAgICAgd2luZG93LmFkZEV2ZW50TGlzdGVuZXIoJ2xvYWQnLCBmdW5jdGlvbigpIHsKICAgICAgICAgICAgICBkaXNwYXRjaFJlc2l6ZSh0cnVlKTsKICAgICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChjaGVja0NhbnZhc1NpemUsIDMwMCk7CiAgICAgICAgICAgICAgd2luZG93LnNldFRpbWVvdXQoY2hlY2tDYW52YXNTaXplLCAxMjAwKTsKICAgICAgICAgICAgfSk7CgogICAgICAgICAgICB3aW5kb3cuc2V0VGltZW91dChmdW5jdGlvbigpIHsKICAgICAgICAgICAgICBkaXNwYXRjaFJlc2l6ZSh0cnVlKTsKICAgICAgICAgICAgfSwgMTAwKTsKICAgICAgICAgICAgd2luZG93LnNldFRpbWVvdXQoZnVuY3Rpb24oKSB7CiAgICAgICAgICAgICAgZGlzcGF0Y2hSZXNpemUodHJ1ZSk7CiAgICAgICAgICAgIH0sIDUwMCk7CiAgICAgICAgICAgIHdpbmRvdy5zZXRUaW1lb3V0KGZ1bmN0aW9uKCkgewogICAgICAgICAgICAgIGRpc3BhdGNoUmVzaXplKHRydWUpOwogICAgICAgICAgICB9LCAxNTAwKTsKICAgICAgICAgIH0KCiAgICAgICAgICAvKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0KICAgICAgICAgICAqIDEwLiDmjIfku6TliIblj5EKICAgICAgICAgICAqIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0gKi8KICAgICAgICAgIGZ1bmN0aW9uIGhhbmRsZUNvbW1hbmQoZXZlbnQpIHsKICAgICAgICAgICAgaWYgKCFldmVudCB8fCAhZXZlbnQuZGF0YSB8fCBldmVudC5kYXRhLl9fbGVhZmVyQ2FwdHVyZSAhPT0gdHJ1ZSkgcmV0dXJuOwogICAgICAgICAgICBpZiAoZXZlbnQuZGF0YS5jb21tYW5kICE9PSB0cnVlKSByZXR1cm47CiAgICAgICAgICAgIGlmICh3aW5kb3cucGFyZW50ICYmIGV2ZW50LnNvdXJjZSAhPT0gd2luZG93LnBhcmVudCkgcmV0dXJuOwoKICAgICAgICAgICAgdmFyIHJlcXVlc3QgPSBldmVudC5kYXRhOwogICAgICAgICAgICB2YXIgaWQgPSByZXF1ZXN0LmlkOwogICAgICAgICAgICB2YXIgYWN0aW9uID0gcmVxdWVzdC5hY3Rpb247CiAgICAgICAgICAgIHZhciBwYXlsb2FkID0gcmVxdWVzdC5wYXlsb2FkIHx8IHt9OwoKICAgICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgICBzd2l0Y2ggKGFjdGlvbikgewogICAgICAgICAgICAgICAgY2FzZSAncGluZyc6CiAgICAgICAgICAgICAgICAgIHBvc3RUb1BhcmVudCgnc3VjY2VzcycsIGlkLCBhY3Rpb24sIHsKICAgICAgICAgICAgICAgICAgICByZWFkeTogdHJ1ZQogICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICAgICAgYnJlYWs7CiAgICAgICAgICAgICAgICBjYXNlICdzY3JlZW5zaG90JzoKICAgICAgICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdzdWNjZXNzJywgaWQsIGFjdGlvbiwgY2FwdHVyZVNjcmVlbnNob3QocGF5bG9hZCkpOwogICAgICAgICAgICAgICAgICBicmVhazsKICAgICAgICAgICAgICAgIGNhc2UgJ3N0YXJ0UmVjb3JkaW5nJzogewogICAgICAgICAgICAgICAgICB2YXIgaW5mbyA9IHN0YXJ0UmVjb3JkaW5nKHBheWxvYWQpOwogICAgICAgICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ3N1Y2Nlc3MnLCBpZCwgYWN0aW9uLCBpbmZvKTsKICAgICAgICAgICAgICAgICAgcG9zdFByb2dyZXNzKCk7CiAgICAgICAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgY2FzZSAnc3RvcFJlY29yZGluZyc6CiAgICAgICAgICAgICAgICAgIHN0b3BSZWNvcmRpbmcoaWQsIGFjdGlvbik7CiAgICAgICAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgICAgICAgY2FzZSAncGF1c2VSZWNvcmRpbmcnOgogICAgICAgICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ3N1Y2Nlc3MnLCBpZCwgYWN0aW9uLCBwYXVzZVJlY29yZGluZygpKTsKICAgICAgICAgICAgICAgICAgYnJlYWs7CiAgICAgICAgICAgICAgICBjYXNlICdyZXN1bWVSZWNvcmRpbmcnOgogICAgICAgICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ3N1Y2Nlc3MnLCBpZCwgYWN0aW9uLCByZXN1bWVSZWNvcmRpbmcoKSk7CiAgICAgICAgICAgICAgICAgIGJyZWFrOwogICAgICAgICAgICAgICAgY2FzZSAnZ2V0U3RhdHVzJzoKICAgICAgICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdzdWNjZXNzJywgaWQsIGFjdGlvbiwgewogICAgICAgICAgICAgICAgICAgIHJlY29yZGluZzogISFyZWNvcmRlclN0YXRlLnJlY29yZGVyLAogICAgICAgICAgICAgICAgICAgIHBhdXNlZDogISFyZWNvcmRlclN0YXRlLnBhdXNlZEF0LAogICAgICAgICAgICAgICAgICAgIGR1cmF0aW9uOiByZWNvcmRpbmdEdXJhdGlvbk1zKCkgLyAxMDAwLAogICAgICAgICAgICAgICAgICAgIG1pbWVUeXBlOiByZWNvcmRlclN0YXRlLm1pbWVUeXBlCiAgICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgICAgICBicmVhazsKICAgICAgICAgICAgICAgIGNhc2UgJ2dldFBhZ2VFcnJvcnMnOgogICAgICAgICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ3N1Y2Nlc3MnLCBpZCwgYWN0aW9uLCB7CiAgICAgICAgICAgICAgICAgICAgcGFnZVRva2VuOiBwYWdlVG9rZW4sCiAgICAgICAgICAgICAgICAgICAgZXJyb3JzOiBwYWdlRXJyb3JIaXN0b3J5LnNsaWNlKDApCiAgICAgICAgICAgICAgICAgIH0pOwogICAgICAgICAgICAgICAgICBicmVhazsKICAgICAgICAgICAgICAgIGNhc2UgJ2dldFNjZW5lU3RhdHVzJzoKICAgICAgICAgICAgICAgICAgcG9zdFRvUGFyZW50KCdzdWNjZXNzJywgaWQsIGFjdGlvbiwgewogICAgICAgICAgICAgICAgICAgIHNjZW5lUmVhZHk6IHNjZW5lUmVhZHlOb3RpZmllZAogICAgICAgICAgICAgICAgICB9KTsKICAgICAgICAgICAgICAgICAgYnJlYWs7CiAgICAgICAgICAgICAgICBkZWZhdWx0OgogICAgICAgICAgICAgICAgICBwb3N0RXJyb3IoaWQsIGFjdGlvbiwgbmV3IEVycm9yKCfmnKrnn6XnmoTmiKrlm74v5b2V5Yi25oyH5Luk77yaJyArIGFjdGlvbiksICdVTktOT1dOX0FDVElPTicpOwogICAgICAgICAgICAgIH0KICAgICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgICBwb3N0RXJyb3IoaWQsIGFjdGlvbiwgZXJyb3IpOwogICAgICAgICAgICB9CiAgICAgICAgICB9CgogICAgICAgICAgLyogLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tCiAgICAgICAgICAgKiAxMS4g5Yid5aeL5YyWCiAgICAgICAgICAgKiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tICovCiAgICAgICAgICBmdW5jdGlvbiBhbm5vdW5jZVJlYWR5KCkgewogICAgICAgICAgICBwb3N0VG9QYXJlbnQoJ3JlYWR5JywgbnVsbCwgJ3JlYWR5JywgewogICAgICAgICAgICAgIHJlYWR5OiB0cnVlLAogICAgICAgICAgICAgIG1lZGlhUmVjb3JkZXI6ICEhd2luZG93Lk1lZGlhUmVjb3JkZXIsCiAgICAgICAgICAgICAgY2FwdHVyZVN0cmVhbTogISEod2luZG93LkhUTUxDYW52YXNFbGVtZW50ICYmIHdpbmRvdy5IVE1MQ2FudmFzRWxlbWVudC5wcm90b3R5cGUuY2FwdHVyZVN0cmVhbSksCiAgICAgICAgICAgICAgY2FudmFzQ291bnQ6IGRvY3VtZW50LnF1ZXJ5U2VsZWN0b3JBbGwoJ2NhbnZhcycpLmxlbmd0aCwKICAgICAgICAgICAgICBzY2VuZVJlYWR5OiBzY2VuZVJlYWR5Tm90aWZpZWQKICAgICAgICAgICAgfSk7CiAgICAgICAgICB9CgogICAgICAgICAgLyog5bC95pep5ZGK6K+J54i257uE5Lu26ISa5pys5bey57uP5omn6KGM77yb5b6u5L+h562J546v5aKD6Iul5oum5oiqIGRhdGE6IFVSTCDlhoXnmoTohJrmnKzvvIwKICAgICAgICAgICAqIOeItue7hOS7tuaUtuS4jeWIsOi/meadoea2iOaBr++8jOS8muiHquWKqOWIh+aNoiBpZnJhbWUg5Yqg6L295pa55byP44CCICovCiAgICAgICAgICBwb3N0VG9QYXJlbnQoJ2JyaWRnZS1hbGl2ZScsIG51bGwsICdicmlkZ2UnLCB7CiAgICAgICAgICAgIHJlYWR5OiB0cnVlLAogICAgICAgICAgICBtb2RlOiAnaWZyYW1lJwogICAgICAgICAgfSk7CgogICAgICAgICAgaW5zdGFsbExlYWZlckNhbnZhc1JlYWR5V2F0Y2goKTsKICAgICAgICAgIGluc3RhbGxWaWV3cG9ydFdhdGNoZG9nKCk7CiAgICAgICAgICBpbnN0YWxsUGFnZUVycm9ySGFuZGxlcnMoKTsKICAgICAgICAgIHN0YXJ0SW5qZWN0ZWRMb2FkaW5nV2F0Y2goKTsKCiAgICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcignbWVzc2FnZScsIGhhbmRsZUNvbW1hbmQsIGZhbHNlKTsKCiAgICAgICAgICB3aW5kb3cuYWRkRXZlbnRMaXN0ZW5lcigncGFnZWhpZGUnLCBmdW5jdGlvbigpIHsKICAgICAgICAgICAgaWYgKHJlY29yZGVyU3RhdGUucmVjb3JkZXIgJiYgcmVjb3JkZXJTdGF0ZS5yZWNvcmRlci5zdGF0ZSAhPT0gJ2luYWN0aXZlJykgewogICAgICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgICAgICByZWNvcmRlclN0YXRlLnJlY29yZGVyLnN0b3AoKTsKICAgICAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAgICAgLyog5b+955WlICovIH0KICAgICAgICAgICAgfQogICAgICAgICAgfSk7CgogICAgICAgICAgaWYgKGRvY3VtZW50LnJlYWR5U3RhdGUgPT09ICdsb2FkaW5nJykgewogICAgICAgICAgICBkb2N1bWVudC5hZGRFdmVudExpc3RlbmVyKCdET01Db250ZW50TG9hZGVkJywgYW5ub3VuY2VSZWFkeSwgZmFsc2UpOwogICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgYW5ub3VuY2VSZWFkeSgpOwogICAgICAgICAgfQogICAgICAgIH0KICAgICAgICByZXR1cm4gJygnICsgbGVhZmVyQ2FwdHVyZVJ1bnRpbWUudG9TdHJpbmcoKSArICcpKCk7JzsKICAgICAgfSwKCiAgICAgIC8qKgogICAgICAgKiDmiorjgIzliqDovb3mj5DnpLogKyDmiKrlm74v5b2V5Yi26L+Q6KGM5pe244CN5rOo5YWl5YiwIEhUTUwg5a2X56ym5Liy5Lit44CCCiAgICAgICAqIOi/kOihjOaXtuS7o+eggeS8mOWFiOaUvuWcqCA8aGVhZD4g5byA5aeL5aSE77yM56Gu5L+d5YWI5LqO5Lia5Yqh6ISa5pys5omn6KGM77yIQ2FudmFzIOeUu+W4g+S4iuS4i+aWhwogICAgICAgKiDooaXkuIHopoHmiqLlnKggTGVhZmVySlMg5Yib5bu6IHJlbmRlcmVyIOS5i+WJje+8ie+8m+WKoOi9veaPkOekuuaUvuWcqCA8Ym9keT4g5pyA5YmN6Z2i77yMCiAgICAgICAqIOS/neivgeS4muWKoeiEmuacrOaKpemUmeaXtuS5n+iDveWFiOeci+WIsOaPkOekuuOAggogICAgICAgKgogICAgICAgKiDov5nkuKrmlrnms5XlnKggY29kZUJhc2U2NFVybCDph4zooqvosIPnlKjvvIzkuZ/lsLHmmK/jgIzovawgYmFzZTY0IOS5i+WJjeOAjeWujOaIkOazqOWFpe+8jAogICAgICAgKiDlm6DmraQgZGF0YSBVUkwgLyBCbG9iIC8gc3JjZG9jIOS4ieenjeWKoOi9veaWueW8j+aLv+WIsOeahOmDveaYr+WQjOS4gOS7veW4puaPkOekuueahCBIVE1M44CCCiAgICAgICAqLwogICAgICBpbmplY3RDYXB0dXJlUnVudGltZShodG1sKSB7CiAgICAgICAgaWYgKHR5cGVvZiBodG1sICE9PSAnc3RyaW5nJyB8fCAhaHRtbC50cmltKCkpIHJldHVybiBodG1sOwoKICAgICAgICBjb25zdCBtYXJrZXIgPSAnZGF0YS1sZWFmZXItY2FwdHVyZSc7CiAgICAgICAgaWYgKGh0bWwuaW5kZXhPZihtYXJrZXIpICE9PSAtMSkgcmV0dXJuIGh0bWw7CgogICAgICAgIGNvbnN0IGxvYWRpbmdTdHlsZSA9IGAKI19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fIHsKICBwb3NpdGlvbjogZml4ZWQ7CiAgdG9wOiAwOwogIHJpZ2h0OiAwOwogIGJvdHRvbTogMDsKICBsZWZ0OiAwOwogIHotaW5kZXg6IDIxNDc0ODMwMDA7CiAgZGlzcGxheTogZmxleDsKICBhbGlnbi1pdGVtczogY2VudGVyOwogIGp1c3RpZnktY29udGVudDogY2VudGVyOwogIGJveC1zaXppbmc6IGJvcmRlci1ib3g7CiAgcGFkZGluZzogMjRweDsKICBiYWNrZ3JvdW5kOiByZ2JhKDgsIDEzLCAyMywgMC45NCk7CiAgY29sb3I6ICNlMmU4ZjA7CiAgZm9udC1mYW1pbHk6IHN5c3RlbS11aSwgLWFwcGxlLXN5c3RlbSwgIlBpbmdGYW5nIFNDIiwgIk1pY3Jvc29mdCBZYUhlaSIsIHNhbnMtc2VyaWY7CiAgdHJhbnNpdGlvbjogb3BhY2l0eSAwLjNzIGVhc2U7Cn0KI19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fIC5fX2xlYWZlcl9sb2FkaW5nX2NhcmRfXyB7CiAgZGlzcGxheTogZmxleDsKICBhbGlnbi1pdGVtczogY2VudGVyOwogIGdhcDogMTRweDsKICBib3gtc2l6aW5nOiBib3JkZXItYm94OwogIHdpZHRoOiBtYXgtY29udGVudDsKICBtYXgtd2lkdGg6IDQ0MHB4OwogIHBhZGRpbmc6IDE2cHggMThweDsKICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDE0OCwgMTYzLCAxODQsIDAuMjQpOwogIGJvcmRlci1yYWRpdXM6IDE0cHg7CiAgYmFja2dyb3VuZDogcmdiYSgzMCwgNDEsIDU5LCAwLjk0KTsKICBib3gtc2hhZG93OiAwIDI0cHggNjBweCAtMjBweCByZ2JhKDAsIDAsIDAsIDAuNjUpOwp9CiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ19zcGlubmVyX18gewogIGZsZXgtc2hyaW5rOiAwOwogIGJveC1zaXppbmc6IGJvcmRlci1ib3g7CiAgd2lkdGg6IDI4cHg7CiAgaGVpZ2h0OiAyOHB4OwogIGJvcmRlcjogM3B4IHNvbGlkIHJnYmEoMTQ4LCAxNjMsIDE4NCwgMC4yOCk7CiAgYm9yZGVyLXRvcC1jb2xvcjogIzIyZDNlZTsKICBib3JkZXItcmFkaXVzOiA5OTk5cHg7CiAgYW5pbWF0aW9uOiBfX2xlYWZlcl9sb2FkaW5nX3NwaW4gMC44NXMgbGluZWFyIGluZmluaXRlOwp9CiNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ19jb3B5X18gewogIGRpc3BsYXk6IGZsZXg7CiAgZmxleC1kaXJlY3Rpb246IGNvbHVtbjsKICBnYXA6IDNweDsKICBtaW4td2lkdGg6IDA7Cn0KI19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fIC5fX2xlYWZlcl9sb2FkaW5nX3RpdGxlX18gewogIGNvbG9yOiAjZjhmYWZjOwogIGZvbnQtc2l6ZTogMTNweDsKICBmb250LXdlaWdodDogNjAwOwogIGxpbmUtaGVpZ2h0OiAxLjU7Cn0KI19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fIC5fX2xlYWZlcl9sb2FkaW5nX2hpbnRfXyB7CiAgY29sb3I6ICM5NGEzYjg7CiAgZm9udC1zaXplOiAxMXB4OwogIGxpbmUtaGVpZ2h0OiAxLjY7CiAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7Cn0KI19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fIC5fX2xlYWZlcl9sb2FkaW5nX3dlY2hhdF9fIHsKICBjb2xvcjogIzY0NzQ4YjsKICBmb250LXNpemU6IDEwcHg7CiAgbGluZS1oZWlnaHQ6IDEuNjsKfQpAa2V5ZnJhbWVzIF9fbGVhZmVyX2xvYWRpbmdfc3BpbiB7CiAgdG8geyB0cmFuc2Zvcm06IHJvdGF0ZSgzNjBkZWcpOyB9Cn0KQG1lZGlhIChtYXgtd2lkdGg6IDY0MHB4KSB7CiAgI19fbGVhZmVyX2xvYWRpbmdfb3ZlcmxheV9fIHsgcGFkZGluZzogMTZweDsgfQogICNfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyAuX19sZWFmZXJfbG9hZGluZ19jYXJkX18geyBtYXgtd2lkdGg6IDEwMCU7IHBhZGRpbmc6IDE0cHggMTZweDsgfQp9CmA7CgogICAgICAgIGNvbnN0IGxvYWRpbmdPdmVybGF5ID0gWwogICAgICAgICAgJycsCiAgICAgICAgICAnPCEtLSBsZWFmZXIgbG9hZGluZyBvdmVybGF5IGluamVjdGVkIGJlZm9yZSBiYXNlNjQgZW5jb2RpbmcgLS0+JywKICAgICAgICAgICc8ZGl2IGlkPSJfX2xlYWZlcl9sb2FkaW5nX292ZXJsYXlfXyIgcm9sZT0ic3RhdHVzIiBhcmlhLWxpdmU9InBvbGl0ZSI+JywKICAgICAgICAgICcgIDxkaXYgY2xhc3M9Il9fbGVhZmVyX2xvYWRpbmdfY2FyZF9fIj4nLAogICAgICAgICAgJyAgICA8c3BhbiBjbGFzcz0iX19sZWFmZXJfbG9hZGluZ19zcGlubmVyX18iIGFyaWEtaGlkZGVuPSJ0cnVlIj48L3NwYW4+JywKICAgICAgICAgICcgICAgPGRpdiBjbGFzcz0iX19sZWFmZXJfbG9hZGluZ19jb3B5X18iPicsCiAgICAgICAgICAnICAgICAgPHN0cm9uZyBpZD0iX19sZWFmZXJfbG9hZGluZ190aXRsZV9fIiBjbGFzcz0iX19sZWFmZXJfbG9hZGluZ190aXRsZV9fIj7mraPlnKjliqDovb0gTGVhZmVySlMg5Yqo55S7PC9zdHJvbmc+JywKICAgICAgICAgICcgICAgICA8c3BhbiBpZD0iX19sZWFmZXJfbG9hZGluZ19oaW50X18iIGNsYXNzPSJfX2xlYWZlcl9sb2FkaW5nX2hpbnRfXyI+5q2j5Zyo5YeG5aSHIENhbnZhcyDmuLLmn5Pnjq/looPvvIzor7fnqI3lgJnigKY8L3NwYW4+JywKICAgICAgICAgICcgICAgICA8c3BhbiBjbGFzcz0iX19sZWFmZXJfbG9hZGluZ193ZWNoYXRfXyI+5b6u5L+h5Lit6Iul6ZW/5pe26Ze05peg5ZON5bqU77yM6K+354K55Y+z5LiK6KeSIMK3wrfCtyDpgInmi6nigJzlnKjmtY/op4jlmajmiZPlvIDigJ3jgII8L3NwYW4+JywKICAgICAgICAgICcgICAgPC9kaXY+JywKICAgICAgICAgICcgIDwvZGl2PicsCiAgICAgICAgICAnPC9kaXY+JywKICAgICAgICAgICcnCiAgICAgICAgXS5qb2luKCdcbicpOwoKICAgICAgICBjb25zdCBoZWFkSW5qZWN0aW9uID0gWwogICAgICAgICAgJycsCiAgICAgICAgICAnPCEtLSBsZWFmZXIgY2FwdHVyZSBicmlkZ2UgKyBsb2FkaW5nIFVJIGluamVjdGVkIGJlZm9yZSBiYXNlNjQgZW5jb2RpbmcgLS0+JywKICAgICAgICAgICc8c3R5bGUgaWQ9Il9fbGVhZmVyX2xvYWRpbmdfc3R5bGVfXyI+JyArIGxvYWRpbmdTdHlsZSArICc8L3N0eWxlPicsCiAgICAgICAgICAnPHNjcmlwdCAnICsgbWFya2VyICsgJz0iYnJpZGdlIj4nLAogICAgICAgICAgdGhpcy5idWlsZENhcHR1cmVSdW50aW1lU291cmNlKCksCiAgICAgICAgICAnPCcgKyAnL3NjcmlwdD4nLAogICAgICAgICAgJycKICAgICAgICBdLmpvaW4oJ1xuJyk7CgogICAgICAgIGxldCBvdXRwdXQgPSBodG1sOwoKICAgICAgICAvKiDlnKjmjIflrprlvIDmoIfnrb7kuYvlkI7mj5LlhaXniYfmrrXvvJvov5Tlm57mmK/lkKbmj5LlhaXmiJDlip/jgIIgKi8KICAgICAgICBjb25zdCBpbnNlcnRBZnRlclRhZyA9IChyZWdleCwgZnJhZ21lbnQpID0+IHsKICAgICAgICAgIGNvbnN0IG1hdGNoID0gcmVnZXguZXhlYyhvdXRwdXQpOwogICAgICAgICAgaWYgKCFtYXRjaCkgcmV0dXJuIGZhbHNlOwogICAgICAgICAgY29uc3QgaW5kZXggPSBtYXRjaC5pbmRleCArIG1hdGNoWzBdLmxlbmd0aDsKICAgICAgICAgIG91dHB1dCA9IG91dHB1dC5zbGljZSgwLCBpbmRleCkgKyBmcmFnbWVudCArIG91dHB1dC5zbGljZShpbmRleCk7CiAgICAgICAgICByZXR1cm4gdHJ1ZTsKICAgICAgICB9OwoKICAgICAgICAvKiDov5DooYzml7bku6PnoIHlv4XpobvlsL3ph4/ml6nmiafooYzvvIzkvJjlhYjmlL7lnKggaGVhZCDlhoXjgIIgKi8KICAgICAgICBpZiAoIWluc2VydEFmdGVyVGFnKC88aGVhZCg/OlxzW14+XSopPz4vaSwgaGVhZEluamVjdGlvbikpIHsKICAgICAgICAgIGlmICghaW5zZXJ0QWZ0ZXJUYWcoLzxodG1sKD86XHNbXj5dKik/Pi9pLCBoZWFkSW5qZWN0aW9uKSkgewogICAgICAgICAgICBpZiAoIWluc2VydEFmdGVyVGFnKC88IWRvY3R5cGVbXj5dKj4vaSwgaGVhZEluamVjdGlvbikpIHsKICAgICAgICAgICAgICBvdXRwdXQgPSBoZWFkSW5qZWN0aW9uICsgb3V0cHV0OwogICAgICAgICAgICB9CiAgICAgICAgICB9CiAgICAgICAgfQoKICAgICAgICAvKiDliqDovb3mj5DnpLrmlL7lnKggYm9keSDmnIDliY3pnaLvvIznoa7kv53ljbPkvb/kuJrliqHohJrmnKzmiqXplJnkuZ/og73lhYjnnIvliLDmj5DnpLrjgIIgKi8KICAgICAgICBpZiAoIWluc2VydEFmdGVyVGFnKC88Ym9keSg/OlxzW14+XSopPz4vaSwgbG9hZGluZ092ZXJsYXkpKSB7CiAgICAgICAgICBjb25zdCBib2R5Q2xvc2UgPSAvPFwvYm9keVxzKj4vaS5leGVjKG91dHB1dCk7CiAgICAgICAgICBpZiAoYm9keUNsb3NlKSB7CiAgICAgICAgICAgIG91dHB1dCA9IG91dHB1dC5zbGljZSgwLCBib2R5Q2xvc2UuaW5kZXgpICsgbG9hZGluZ092ZXJsYXkgKyBvdXRwdXQuc2xpY2UoYm9keUNsb3NlLmluZGV4KTsKICAgICAgICAgIH0gZWxzZSB7CiAgICAgICAgICAgIGNvbnN0IGh0bWxDbG9zZSA9IC88XC9odG1sXHMqPi9pLmV4ZWMob3V0cHV0KTsKICAgICAgICAgICAgaWYgKGh0bWxDbG9zZSkgewogICAgICAgICAgICAgIG91dHB1dCA9IG91dHB1dC5zbGljZSgwLCBodG1sQ2xvc2UuaW5kZXgpICsgbG9hZGluZ092ZXJsYXkgKyBvdXRwdXQuc2xpY2UoaHRtbENsb3NlLmluZGV4KTsKICAgICAgICAgICAgfSBlbHNlIHsKICAgICAgICAgICAgICBvdXRwdXQgKz0gbG9hZGluZ092ZXJsYXk7CiAgICAgICAgICAgIH0KICAgICAgICAgIH0KICAgICAgICB9CgogICAgICAgIHJldHVybiBvdXRwdXQ7CiAgICAgIH0sCgogICAgICB0ZWFyZG93bkNhcHR1cmVCcmlkZ2UoKSB7CiAgICAgICAgaWYgKHRoaXMuYnJpZGdlVG9ybkRvd24pIHJldHVybjsKICAgICAgICB0aGlzLmJyaWRnZVRvcm5Eb3duID0gdHJ1ZTsKCiAgICAgICAgd2luZG93LnJlbW92ZUV2ZW50TGlzdGVuZXIoJ21lc3NhZ2UnLCB0aGlzLmhhbmRsZU1lc3NhZ2UpOwogICAgICAgIHdpbmRvdy5yZW1vdmVFdmVudExpc3RlbmVyKCdrZXlkb3duJywgdGhpcy5oYW5kbGVLZXlkb3duKTsKICAgICAgICB0aGlzLnJlamVjdFBlbmRpbmdDb21tYW5kcygn57uE5Lu25bey6ZSA5q+BJyk7CiAgICAgICAgaWYgKHRoaXMuc3RhdHVzVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnN0YXR1c1RpbWVyKTsKICAgICAgICAgIHRoaXMuc3RhdHVzVGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgICBpZiAodGhpcy5jb3B5UmVzZXRUaW1lcikgewogICAgICAgICAgY2xlYXJUaW1lb3V0KHRoaXMuY29weVJlc2V0VGltZXIpOwogICAgICAgICAgdGhpcy5jb3B5UmVzZXRUaW1lciA9IG51bGw7CiAgICAgICAgfQogICAgICAgIHRoaXMuY2xlYXJTY2VuZUxvYWRpbmdUaW1lcnMoKTsKICAgICAgICB0aGlzLmNsZWFyU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICAgIHRoaXMucmV2b2tlSWZyYW1lQmxvYlVybCgpOwogICAgICAgIHRoaXMucmVsZWFzZVZpZGVvT2JqZWN0VXJsKCk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgKiBpZnJhbWUg6aG16Z2i5byC5bi45by556qXCiAgICAgICAqID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICByZXNldFBhZ2VFcnJvcnMoKSB7CiAgICAgICAgdGhpcy5wYWdlRXJyb3JzID0gW107CiAgICAgICAgdGhpcy5lcnJvckRpYWxvZ1Zpc2libGUgPSBmYWxzZTsKICAgICAgICB0aGlzLmNvcHlTdGF0ZSA9ICcnOwogICAgICAgIHRoaXMuZXJyb3JTZXEgPSAwOwogICAgICAgIHRoaXMuc2VlblBhZ2VFcnJvcklkcyA9IE9iamVjdC5jcmVhdGUobnVsbCk7CiAgICAgICAgdGhpcy5lcnJvckRpYWxvZ1N1cHByZXNzZWRVbnRpbCA9IDA7CgogICAgICAgIGlmICh0aGlzLmNvcHlSZXNldFRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5jb3B5UmVzZXRUaW1lcik7CiAgICAgICAgICB0aGlzLmNvcHlSZXNldFRpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgIH0sCgogICAgICAvKioKICAgICAgICogaWZyYW1lIOWcqOmhtemdouWKoOi9vei/h+eoi+S4reWPr+iDveW3sue7j+S6p+eUn+W8guW4uO+8jOS9hue7hOS7tueahCBtZXNzYWdlIOebkeWQrOWwmuacqgogICAgICAgKiDlu7rnq4vjgIJpZnJhbWUg5YaF5Lya57yT5a2Y5pyA6L+RIDUwIOadoeW8guW4uO+8jOi/memHjOWcqCBsb2FkIOS5i+WQjuihpeaLieS4gOasoe+8jAogICAgICAgKiDphY3lkIggcGFnZVRva2VuICsgcGFnZUVycm9ySWQg5Y676YeN44CCCiAgICAgICAqLwogICAgICBhc3luYyByZXF1ZXN0UGFnZUVycm9ycygpIHsKICAgICAgICB0cnkgewogICAgICAgICAgY29uc3QgcmVzdWx0ID0gYXdhaXQgdGhpcy5wb3N0Q29tbWFuZCgnZ2V0UGFnZUVycm9ycycsIHt9LCA1MDAwKTsKICAgICAgICAgIGlmICghcmVzdWx0IHx8ICFBcnJheS5pc0FycmF5KHJlc3VsdC5lcnJvcnMpKSByZXR1cm47CiAgICAgICAgICBmb3IgKGxldCBpID0gMDsgaSA8IHJlc3VsdC5lcnJvcnMubGVuZ3RoOyBpICs9IDEpIHsKICAgICAgICAgICAgdGhpcy5oYW5kbGVQYWdlRXJyb3IocmVzdWx0LmVycm9yc1tpXSk7CiAgICAgICAgICB9CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIC8qIGlmcmFtZSDlt7Lljbjovb3miJblsJrmnKrlsLHnu6rml7blv73nlaUgKi8KICAgICAgICB9CiAgICAgIH0sCgogICAgICBoYW5kbGVQYWdlRXJyb3IoZGF0YSkgewogICAgICAgIGlmICghZGF0YSkgcmV0dXJuOwoKICAgICAgICBjb25zdCB0eXBlID0gZGF0YS50eXBlIHx8ICdlcnJvcic7CiAgICAgICAgY29uc3QgbWVzc2FnZSA9IHRoaXMuc2FmZUVycm9yVGV4dChkYXRhLm1lc3NhZ2UsIDIwMDApIHx8ICfmnKrnn6XlvILluLgnOwogICAgICAgIGNvbnN0IHNvdXJjZSA9IHRoaXMuc2FmZUVycm9yVGV4dChkYXRhLnNvdXJjZSwgODAwKTsKICAgICAgICBjb25zdCBzdGFjayA9IHRoaXMuc2FmZUVycm9yVGV4dChkYXRhLnN0YWNrLCA2MDAwKTsKICAgICAgICBjb25zdCBsaW5lID0gTnVtYmVyKGRhdGEubGluZSkgfHwgMDsKICAgICAgICBjb25zdCBjb2x1bW4gPSBOdW1iZXIoZGF0YS5jb2x1bW4pIHx8IDA7CiAgICAgICAgY29uc3QgcGFnZVRva2VuID0gZGF0YS5wYWdlVG9rZW4gfHwgJ3Vua25vd24nOwogICAgICAgIGNvbnN0IGVycm9ySWQgPSBkYXRhLnBhZ2VFcnJvcklkOwoKICAgICAgICBsZXQgZGVkdXBlS2V5ID0gJyc7CiAgICAgICAgaWYgKGVycm9ySWQpIHsKICAgICAgICAgIGRlZHVwZUtleSA9IHBhZ2VUb2tlbiArICc6JyArIGVycm9ySWQ7CiAgICAgICAgfSBlbHNlIHsKICAgICAgICAgIGRlZHVwZUtleSA9ICdmYWxsYmFjazonICsgdHlwZSArICd8JyArIG1lc3NhZ2UgKyAnfCcgKyBzb3VyY2UgKyAnfCcgKyBsaW5lICsgJ3wnICsgY29sdW1uOwogICAgICAgIH0KCiAgICAgICAgaWYgKHRoaXMuc2VlblBhZ2VFcnJvcklkc1tkZWR1cGVLZXldKSByZXR1cm47CiAgICAgICAgdGhpcy5zZWVuUGFnZUVycm9ySWRzW2RlZHVwZUtleV0gPSB0cnVlOwoKICAgICAgICB0aGlzLmVycm9yU2VxICs9IDE7CiAgICAgICAgY29uc3QgaXRlbSA9IHsKICAgICAgICAgIGlkOiB0aGlzLmVycm9yU2VxLAogICAgICAgICAgdHlwZTogdHlwZSwKICAgICAgICAgIHR5cGVMYWJlbDogdGhpcy5lcnJvclR5cGVMYWJlbCh0eXBlKSwKICAgICAgICAgIG1lc3NhZ2U6IG1lc3NhZ2UsCiAgICAgICAgICBzdGFjazogc3RhY2ssCiAgICAgICAgICBsb2NhdGlvbjogdGhpcy5mb3JtYXRFcnJvckxvY2F0aW9uKHNvdXJjZSwgbGluZSwgY29sdW1uKSwKICAgICAgICAgIHRpbWVMYWJlbDogdGhpcy5mb3JtYXRFcnJvclRpbWUoZGF0YS50aW1lc3RhbXApLAogICAgICAgICAgdGltZXN0YW1wOiBOdW1iZXIoZGF0YS50aW1lc3RhbXApIHx8IERhdGUubm93KCkKICAgICAgICB9OwoKICAgICAgICB0aGlzLnBhZ2VFcnJvcnMgPSB0aGlzLnBhZ2VFcnJvcnMuY29uY2F0KGl0ZW0pLnNsaWNlKC01MCk7CgogICAgICAgIGlmICghdGhpcy5lcnJvckRpYWxvZ1Zpc2libGUgJiYgRGF0ZS5ub3coKSA+PSB0aGlzLmVycm9yRGlhbG9nU3VwcHJlc3NlZFVudGlsKSB7CiAgICAgICAgICB0aGlzLmVycm9yRGlhbG9nVmlzaWJsZSA9IHRydWU7CiAgICAgICAgICB0aGlzLmNvcHlTdGF0ZSA9ICcnOwogICAgICAgICAgdGhpcy4kbmV4dFRpY2soKCkgPT4gewogICAgICAgICAgICBpZiAodGhpcy4kcmVmcy5lcnJvckRpYWxvZyAmJiB0aGlzLiRyZWZzLmVycm9yRGlhbG9nLmZvY3VzKSB7CiAgICAgICAgICAgICAgdGhpcy4kcmVmcy5lcnJvckRpYWxvZy5mb2N1cygpOwogICAgICAgICAgICB9CiAgICAgICAgICB9KTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBvcGVuRXJyb3JEaWFsb2coKSB7CiAgICAgICAgaWYgKCF0aGlzLnBhZ2VFcnJvcnMubGVuZ3RoKSByZXR1cm47CiAgICAgICAgdGhpcy5lcnJvckRpYWxvZ1Zpc2libGUgPSB0cnVlOwogICAgICAgIHRoaXMuY29weVN0YXRlID0gJyc7CiAgICAgICAgdGhpcy4kbmV4dFRpY2soKCkgPT4gewogICAgICAgICAgaWYgKHRoaXMuJHJlZnMuZXJyb3JEaWFsb2cgJiYgdGhpcy4kcmVmcy5lcnJvckRpYWxvZy5mb2N1cykgewogICAgICAgICAgICB0aGlzLiRyZWZzLmVycm9yRGlhbG9nLmZvY3VzKCk7CiAgICAgICAgICB9CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICBjbG9zZUVycm9yRGlhbG9nKCkgewogICAgICAgIHRoaXMuZXJyb3JEaWFsb2dWaXNpYmxlID0gZmFsc2U7CiAgICAgICAgLyog55So5oi35Li75Yqo5YWz6Zet5ZCO55+t5pqC5oqR5Yi26Ieq5Yqo5by556qX77yM6YG/5YWN6aG16Z2i5oyB57ut5oql6ZSZ5pe25by556qX5Y+N5aSN5oqi54Sm54K577ybCiAgICAgICAgICog5paw5byC5bi45LuN5Lya57Sv6K6h5Yiw5bel5YW35qCP55qE5byC5bi46K6h5pWw5Lit44CCICovCiAgICAgICAgdGhpcy5lcnJvckRpYWxvZ1N1cHByZXNzZWRVbnRpbCA9IERhdGUubm93KCkgKyA1MDAwOwogICAgICB9LAoKICAgICAgaGFuZGxlS2V5ZG93bihldmVudCkgewogICAgICAgIGlmICghdGhpcy5lcnJvckRpYWxvZ1Zpc2libGUpIHJldHVybjsKICAgICAgICBpZiAoZXZlbnQua2V5ID09PSAnRXNjYXBlJyB8fCBldmVudC5rZXkgPT09ICdFc2MnIHx8IGV2ZW50LmtleUNvZGUgPT09IDI3KSB7CiAgICAgICAgICBldmVudC5wcmV2ZW50RGVmYXVsdCgpOwogICAgICAgICAgdGhpcy5jbG9zZUVycm9yRGlhbG9nKCk7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgYXN5bmMgY29weUVycm9ySW5mbygpIHsKICAgICAgICBpZiAoIXRoaXMucGFnZUVycm9ycy5sZW5ndGgpIHJldHVybjsKCiAgICAgICAgdGhpcy5jb3B5U3RhdGUgPSAnY29weWluZyc7CiAgICAgICAgY29uc3QgdGV4dCA9IHRoaXMuYnVpbGRFcnJvckNvcHlUZXh0KCk7CiAgICAgICAgbGV0IGNvcGllZCA9IGZhbHNlOwoKICAgICAgICB0cnkgewogICAgICAgICAgaWYgKG5hdmlnYXRvci5jbGlwYm9hcmQgJiYgdHlwZW9mIG5hdmlnYXRvci5jbGlwYm9hcmQud3JpdGVUZXh0ID09PSAnZnVuY3Rpb24nKSB7CiAgICAgICAgICAgIGF3YWl0IG5hdmlnYXRvci5jbGlwYm9hcmQud3JpdGVUZXh0KHRleHQpOwogICAgICAgICAgICBjb3BpZWQgPSB0cnVlOwogICAgICAgICAgfQogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICBjb3BpZWQgPSBmYWxzZTsKICAgICAgICB9CgogICAgICAgIGlmICghY29waWVkKSBjb3BpZWQgPSB0aGlzLmNvcHlUZXh0RmFsbGJhY2sodGV4dCk7CgogICAgICAgIHRoaXMuY29weVN0YXRlID0gY29waWVkID8gJ2NvcGllZCcgOiAnZmFpbGVkJzsKICAgICAgICBpZiAodGhpcy5jb3B5UmVzZXRUaW1lcikgY2xlYXJUaW1lb3V0KHRoaXMuY29weVJlc2V0VGltZXIpOwogICAgICAgIHRoaXMuY29weVJlc2V0VGltZXIgPSBzZXRUaW1lb3V0KCgpID0+IHsKICAgICAgICAgIHRoaXMuY29weVJlc2V0VGltZXIgPSBudWxsOwogICAgICAgICAgdGhpcy5jb3B5U3RhdGUgPSAnJzsKICAgICAgICB9LCAyNTAwKTsKICAgICAgfSwKCiAgICAgIGNvcHlUZXh0RmFsbGJhY2sodGV4dCkgewogICAgICAgIHRyeSB7CiAgICAgICAgICBjb25zdCB0ZXh0YXJlYSA9IGRvY3VtZW50LmNyZWF0ZUVsZW1lbnQoJ3RleHRhcmVhJyk7CiAgICAgICAgICB0ZXh0YXJlYS52YWx1ZSA9IHRleHQ7CiAgICAgICAgICB0ZXh0YXJlYS5zZXRBdHRyaWJ1dGUoJ3JlYWRvbmx5JywgJ3JlYWRvbmx5Jyk7CiAgICAgICAgICB0ZXh0YXJlYS5zdHlsZS5wb3NpdGlvbiA9ICdmaXhlZCc7CiAgICAgICAgICB0ZXh0YXJlYS5zdHlsZS50b3AgPSAnMCc7CiAgICAgICAgICB0ZXh0YXJlYS5zdHlsZS5sZWZ0ID0gJy05OTk5cHgnOwogICAgICAgICAgdGV4dGFyZWEuc3R5bGUub3BhY2l0eSA9ICcwJzsKICAgICAgICAgIGRvY3VtZW50LmJvZHkuYXBwZW5kQ2hpbGQodGV4dGFyZWEpOwogICAgICAgICAgdGV4dGFyZWEuZm9jdXMoKTsKICAgICAgICAgIHRleHRhcmVhLnNlbGVjdCgpOwogICAgICAgICAgdGV4dGFyZWEuc2V0U2VsZWN0aW9uUmFuZ2UoMCwgdGV4dGFyZWEudmFsdWUubGVuZ3RoKTsKICAgICAgICAgIGNvbnN0IHJlc3VsdCA9IGRvY3VtZW50LmV4ZWNDb21tYW5kKCdjb3B5Jyk7CiAgICAgICAgICB0ZXh0YXJlYS5yZW1vdmUoKTsKICAgICAgICAgIHJldHVybiByZXN1bHQ7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIHJldHVybiBmYWxzZTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBidWlsZEVycm9yQ29weVRleHQoKSB7CiAgICAgICAgY29uc3QgbGluZXMgPSBbXTsKICAgICAgICBsaW5lcy5wdXNoKCfpobXpnaLov5DooYzlvILluLjvvIjlhbEgJyArIHRoaXMucGFnZUVycm9ycy5sZW5ndGggKyAnIOadoe+8iScpOwogICAgICAgIGxpbmVzLnB1c2goJ+WvvOWHuuaXtumXtO+8micgKyB0aGlzLmZvcm1hdEVycm9yVGltZShEYXRlLm5vdygpKSk7CiAgICAgICAgbGluZXMucHVzaCgnJyk7CgogICAgICAgIGZvciAobGV0IGkgPSAwOyBpIDwgdGhpcy5wYWdlRXJyb3JzLmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICBjb25zdCBpdGVtID0gdGhpcy5wYWdlRXJyb3JzW2ldOwogICAgICAgICAgbGluZXMucHVzaCgnWycgKyAoaSArIDEpICsgJ10gJyArIGl0ZW0udGltZUxhYmVsICsgJyDCtyAnICsgaXRlbS50eXBlTGFiZWwpOwogICAgICAgICAgbGluZXMucHVzaChpdGVtLm1lc3NhZ2UpOwogICAgICAgICAgaWYgKGl0ZW0ubG9jYXRpb24pIGxpbmVzLnB1c2goJ+S9jee9ru+8micgKyBpdGVtLmxvY2F0aW9uKTsKICAgICAgICAgIGlmIChpdGVtLnN0YWNrKSB7CiAgICAgICAgICAgIGxpbmVzLnB1c2goJ+WghuagiO+8micpOwogICAgICAgICAgICBsaW5lcy5wdXNoKGl0ZW0uc3RhY2spOwogICAgICAgICAgfQogICAgICAgICAgbGluZXMucHVzaCgnLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLScpOwogICAgICAgIH0KCiAgICAgICAgcmV0dXJuIGxpbmVzLmpvaW4oJ1xuJyk7CiAgICAgIH0sCgogICAgICBlcnJvclR5cGVMYWJlbCh0eXBlKSB7CiAgICAgICAgc3dpdGNoICh0eXBlKSB7CiAgICAgICAgICBjYXNlICdlcnJvcic6CiAgICAgICAgICAgIHJldHVybiAn6ISa5pys6ZSZ6K+vJzsKICAgICAgICAgIGNhc2UgJ3Byb21pc2UnOgogICAgICAgICAgICByZXR1cm4gJ1Byb21pc2Ug5byC5bi4JzsKICAgICAgICAgIGNhc2UgJ3Jlc291cmNlJzoKICAgICAgICAgICAgcmV0dXJuICfotYTmupDliqDovb3lpLHotKUnOwogICAgICAgICAgY2FzZSAnY2FudmFzJzoKICAgICAgICAgICAgcmV0dXJuICdDYW52YXMg55S75biD5byC5bi4JzsKICAgICAgICAgIGRlZmF1bHQ6CiAgICAgICAgICAgIHJldHVybiAn6aG16Z2i5byC5bi4JzsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBmb3JtYXRFcnJvclRpbWUodGltZXN0YW1wKSB7CiAgICAgICAgY29uc3QgdmFsdWUgPSBOdW1iZXIodGltZXN0YW1wKTsKICAgICAgICBjb25zdCBkYXRlID0gbmV3IERhdGUoaXNGaW5pdGUodmFsdWUpICYmIHZhbHVlID4gMCA/IHZhbHVlIDogRGF0ZS5ub3coKSk7CiAgICAgICAgY29uc3QgcGFkID0gKG51bWJlcikgPT4gU3RyaW5nKG51bWJlcikucGFkU3RhcnQoMiwgJzAnKTsKICAgICAgICByZXR1cm4gcGFkKGRhdGUuZ2V0SG91cnMoKSkgKyAnOicgKyBwYWQoZGF0ZS5nZXRNaW51dGVzKCkpICsgJzonICsgcGFkKGRhdGUuZ2V0U2Vjb25kcygpKTsKICAgICAgfSwKCiAgICAgIGZvcm1hdEVycm9yTG9jYXRpb24oc291cmNlLCBsaW5lLCBjb2x1bW4pIHsKICAgICAgICBsZXQgbG9jYXRpb24gPSBzb3VyY2UgfHwgJyc7CiAgICAgICAgaWYgKGxpbmUpIGxvY2F0aW9uICs9IChsb2NhdGlvbiA/ICc6JyA6ICcnKSArIGxpbmU7CiAgICAgICAgaWYgKGNvbHVtbikgbG9jYXRpb24gKz0gKGxvY2F0aW9uID8gJzonIDogJycpICsgY29sdW1uOwogICAgICAgIHJldHVybiBsb2NhdGlvbjsKICAgICAgfSwKCiAgICAgIHNhZmVFcnJvclRleHQodmFsdWUsIG1heExlbmd0aCkgewogICAgICAgIGlmICh2YWx1ZSA9PT0gbnVsbCB8fCB2YWx1ZSA9PT0gdW5kZWZpbmVkKSByZXR1cm4gJyc7CiAgICAgICAgbGV0IHRleHQgPSBTdHJpbmcodmFsdWUpOwogICAgICAgIGNvbnN0IGxpbWl0ID0gbWF4TGVuZ3RoIHx8IDIwMDA7CiAgICAgICAgaWYgKHRleHQubGVuZ3RoID4gbGltaXQpIHRleHQgPSB0ZXh0LnNsaWNlKDAsIGxpbWl0KSArICfigKYnOwogICAgICAgIHJldHVybiB0ZXh0OwogICAgICB9LAoKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAqIOWKqOeUu+eUu+W4g+WKoOi9veeKtuaAgQogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgcmVzZXRTY2VuZUxvYWRpbmcoKSB7CiAgICAgICAgdGhpcy5zY2VuZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRTbG93ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRUaW1lZE91dCA9IGZhbHNlOwogICAgICAgIHRoaXMucGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4gPSBmYWxzZTsKICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgIH0sCgogICAgICBzdGFydFNjZW5lTG9hZGluZ1RpbWVycygpIHsKICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgICAgdGhpcy5zY2VuZVNsb3dUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHRoaXMuc2NlbmVMb2FkU2xvdyA9IHRydWU7CiAgICAgICAgfSwgNTAwMCk7CiAgICAgICAgdGhpcy5zY2VuZVRpbWVvdXRUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHsKICAgICAgICAgICAgdGhpcy5zY2VuZUxvYWRTbG93ID0gdHJ1ZTsKICAgICAgICAgICAgdGhpcy5zY2VuZUxvYWRUaW1lZE91dCA9IHRydWU7CiAgICAgICAgICB9CiAgICAgICAgfSwgMTIwMDApOwogICAgICAgIC8qIOiLpee7hOS7tuerr+eahCBzY2VuZS1yZWFkeSDmtojmga/ooqvlvq7kv6HnrYnnjq/looPmi6bmiKrvvIw0LjUg56eS5ZCO5YWI6K6p5Ye66YGu572p77yMCiAgICAgICAgICog5pi+56S6IGlmcmFtZSDlhoXpg6joh6rlt7Hms6jlhaXnmoTliqDovb0gLyDlpLHotKXmj5DnpLrjgIIgKi8KICAgICAgICB0aGlzLnNjZW5lUGFyZW50T3ZlcmxheVRpbWVyID0gc2V0VGltZW91dCgoKSA9PiB7CiAgICAgICAgICBpZiAoIXRoaXMuc2NlbmVSZWFkeSkgdGhpcy5wYXJlbnRMb2FkaW5nT3ZlcmxheUhpZGRlbiA9IHRydWU7CiAgICAgICAgfSwgNDUwMCk7CiAgICAgIH0sCgogICAgICBjbGVhclNjZW5lTG9hZGluZ1RpbWVycygpIHsKICAgICAgICBpZiAodGhpcy5zY2VuZVNsb3dUaW1lcikgewogICAgICAgICAgY2xlYXJUaW1lb3V0KHRoaXMuc2NlbmVTbG93VGltZXIpOwogICAgICAgICAgdGhpcy5zY2VuZVNsb3dUaW1lciA9IG51bGw7CiAgICAgICAgfQogICAgICAgIGlmICh0aGlzLnNjZW5lVGltZW91dFRpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5zY2VuZVRpbWVvdXRUaW1lcik7CiAgICAgICAgICB0aGlzLnNjZW5lVGltZW91dFRpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgICAgaWYgKHRoaXMuc2NlbmVQYXJlbnRPdmVybGF5VGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnNjZW5lUGFyZW50T3ZlcmxheVRpbWVyKTsKICAgICAgICAgIHRoaXMuc2NlbmVQYXJlbnRPdmVybGF5VGltZXIgPSBudWxsOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIG1hcmtTY2VuZVJlYWR5KCkgewogICAgICAgIHRoaXMuc2NlbmVSZWFkeSA9IHRydWU7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRTbG93ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZUxvYWRUaW1lZE91dCA9IGZhbHNlOwogICAgICAgIHRoaXMucGFyZW50TG9hZGluZ092ZXJsYXlIaWRkZW4gPSBmYWxzZTsKICAgICAgICB0aGlzLmNsZWFyU2NlbmVMb2FkaW5nVGltZXJzKCk7CiAgICAgIH0sCgogICAgICBhc3luYyByZXF1ZXN0U2NlbmVTdGF0dXMoKSB7CiAgICAgICAgaWYgKCF0aGlzLmlmcmFtZVJlYWR5KSByZXR1cm47CiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IHJlc3VsdCA9IGF3YWl0IHRoaXMucG9zdENvbW1hbmQoJ2dldFNjZW5lU3RhdHVzJywge30sIDQwMDApOwogICAgICAgICAgaWYgKHJlc3VsdCAmJiByZXN1bHQuc2NlbmVSZWFkeSkgdGhpcy5tYXJrU2NlbmVSZWFkeSgpOwogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAvKiBpZnJhbWUg5bCa5pyq6L+Q6KGM5oiW5bey5Y246L295pe25b+955WlICovCiAgICAgICAgfQogICAgICB9LAoKICAgICAgLyogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0KICAgICAgICogaWZyYW1lIOadpea6kOWFvOWuueaooeW8jwogICAgICAgKiDlvq7kv6HnrYnlhoXnva7mtY/op4jlmajlj6/og73muLLmn5MgZGF0YTogVVJMIGlmcmFtZSDnmoQgSFRNTO+8jOWNtOS4jeaJp+ihjOWFtuS4reeahAogICAgICAgKiDohJrmnKzvvIjooajnjrDkuLrnmb3lsY8gLyDnlLvluIPkuI3lh7rnjrDvvInjgILov5nph4znlKggYnJpZGdlLWFsaXZlIOaOoua1i+iEmuacrOaYr+WQpgogICAgICAgKiDmiafooYzvvIzlpLHotKXml7bkvp3mrKHpmY3nuqfvvJpkYXRhIFVSTCDihpIgQmxvYiBVUkwg4oaSIHNyY2RvY+OAggogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgY2xlYXJTb3VyY2VGYWxsYmFja0NoZWNrKCkgewogICAgICAgIGlmICh0aGlzLnNvdXJjZUZhbGxiYWNrVGltZXIpIHsKICAgICAgICAgIGNsZWFyVGltZW91dCh0aGlzLnNvdXJjZUZhbGxiYWNrVGltZXIpOwogICAgICAgICAgdGhpcy5zb3VyY2VGYWxsYmFja1RpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBzY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKSB7CiAgICAgICAgdGhpcy5jbGVhclNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgICB0aGlzLnNvdXJjZUZhbGxiYWNrVGltZXIgPSBzZXRUaW1lb3V0KCgpID0+IHsKICAgICAgICAgIHRoaXMuc291cmNlRmFsbGJhY2tUaW1lciA9IG51bGw7CiAgICAgICAgICBpZiAodGhpcy5icmlkZ2VBbGl2ZSB8fCB0aGlzLnNjZW5lUmVhZHkgfHwgdGhpcy5icmlkZ2VUb3JuRG93biB8fCB0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkKSB7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KICAgICAgICAgIHRoaXMudHJ5TmV4dElmcmFtZVNvdXJjZSgpOwogICAgICAgIH0sIDM1MDApOwogICAgICB9LAoKICAgICAgcmV2b2tlSWZyYW1lQmxvYlVybCgpIHsKICAgICAgICBpZiAodGhpcy5pZnJhbWVCbG9iVXJsKSB7CiAgICAgICAgICB0cnkgewogICAgICAgICAgICBVUkwucmV2b2tlT2JqZWN0VVJMKHRoaXMuaWZyYW1lQmxvYlVybCk7CiAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgdGhpcy5pZnJhbWVCbG9iVXJsID0gJyc7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgcHJlcGFyZUZvcklmcmFtZVNvdXJjZUNoYW5nZSgpIHsKICAgICAgICB0aGlzLmlmcmFtZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5zY2VuZVJlYWR5ID0gZmFsc2U7CiAgICAgICAgdGhpcy5icmlkZ2VBbGl2ZSA9IGZhbHNlOwogICAgICAgIHRoaXMuc3RhdHVzTWVzc2FnZSA9ICcnOwogICAgICAgIHRoaXMucmVzZXRTY2VuZUxvYWRpbmcoKTsKICAgICAgICB0aGlzLnJlc2V0UGFnZUVycm9ycygpOwogICAgICAgIHRoaXMuY2xlYXJTb3VyY2VGYWxsYmFja0NoZWNrKCk7CiAgICAgICAgdGhpcy5yZWplY3RQZW5kaW5nQ29tbWFuZHMoJ+ato+WcqOWIh+aNoumihOiniOWFvOWuueaooeW8jycpOwogICAgICB9LAoKICAgICAgdHJ5TmV4dElmcmFtZVNvdXJjZSgpIHsKICAgICAgICBpZiAodGhpcy5icmlkZ2VBbGl2ZSkgcmV0dXJuOwoKICAgICAgICBpZiAodGhpcy5pZnJhbWVTb3VyY2VNb2RlID09PSAnZGF0YScpIHsKICAgICAgICAgIHRoaXMuc3dpdGNoSWZyYW1lU291cmNlKCdibG9iJyk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQogICAgICAgIGlmICh0aGlzLmlmcmFtZVNvdXJjZU1vZGUgPT09ICdibG9iJykgewogICAgICAgICAgdGhpcy5zd2l0Y2hJZnJhbWVTb3VyY2UoJ3NyY2RvYycpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgdGhpcy5zb3VyY2VGYWxsYmFja0ZhaWxlZCA9IHRydWU7CiAgICAgICAgdGhpcy5wYXJlbnRMb2FkaW5nT3ZlcmxheUhpZGRlbiA9IGZhbHNlOwogICAgICAgIHRoaXMuaGFuZGxlUGFnZUVycm9yKHsKICAgICAgICAgIHBhZ2VUb2tlbjogJ3NvdXJjZS1mYWxsYmFjay0nICsgRGF0ZS5ub3coKSwKICAgICAgICAgIHBhZ2VFcnJvcklkOiAxLAogICAgICAgICAgdHlwZTogJ2Vycm9yJywKICAgICAgICAgIG1lc3NhZ2U6ICflvZPliY3mtY/op4jlmajml6Dms5XmiafooYzpooTop4johJrmnKzvvIjlt7LlsJ3or5UgZGF0YSBVUkzjgIFCbG9i44CBc3JjZG9j77yJ77yM6K+35bCd6K+V5Zyo57O757uf5rWP6KeI5Zmo5Lit5omT5byA44CCJywKICAgICAgICAgIHRpbWVzdGFtcDogRGF0ZS5ub3coKQogICAgICAgIH0pOwogICAgICB9LAoKICAgICAgc3dpdGNoSWZyYW1lU291cmNlKG1vZGUpIHsKICAgICAgICB0aGlzLmNsZWFyU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICAgIHRoaXMucHJlcGFyZUZvcklmcmFtZVNvdXJjZUNoYW5nZSgpOwoKICAgICAgICBpZiAobW9kZSA9PT0gJ2Jsb2InKSB7CiAgICAgICAgICB0aGlzLnJldm9rZUlmcmFtZUJsb2JVcmwoKTsKICAgICAgICAgIHRyeSB7CiAgICAgICAgICAgIGNvbnN0IGJsb2IgPSBuZXcgQmxvYihbdGhpcy5pbmplY3RlZEh0bWxdLCB7CiAgICAgICAgICAgICAgdHlwZTogJ3RleHQvaHRtbDtjaGFyc2V0PXV0Zi04JwogICAgICAgICAgICB9KTsKICAgICAgICAgICAgdGhpcy5pZnJhbWVCbG9iVXJsID0gVVJMLmNyZWF0ZU9iamVjdFVSTChibG9iKTsKICAgICAgICAgICAgdGhpcy5pZnJhbWVTb3VyY2VNb2RlID0gJ2Jsb2InOwogICAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgICAgdGhpcy5pZnJhbWVTb3VyY2VNb2RlID0gJ2RhdGEnOwogICAgICAgICAgICB0aGlzLnNvdXJjZUZhbGxiYWNrRmFpbGVkID0gdHJ1ZTsKICAgICAgICAgICAgdGhpcy5wYXJlbnRMb2FkaW5nT3ZlcmxheUhpZGRlbiA9IGZhbHNlOwogICAgICAgICAgfQogICAgICAgIH0gZWxzZSBpZiAobW9kZSA9PT0gJ3NyY2RvYycpIHsKICAgICAgICAgIHRoaXMucmV2b2tlSWZyYW1lQmxvYlVybCgpOwogICAgICAgICAgdGhpcy5pZnJhbWVTb3VyY2VNb2RlID0gJ3NyY2RvYyc7CiAgICAgICAgfQoKICAgICAgICB0aGlzLnNjaGVkdWxlU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICB9LAoKICAgICAgaGFuZGxlQnJpZGdlQWxpdmUoKSB7CiAgICAgICAgdGhpcy5icmlkZ2VBbGl2ZSA9IHRydWU7CiAgICAgICAgdGhpcy5zb3VyY2VGYWxsYmFja0ZhaWxlZCA9IGZhbHNlOwogICAgICAgIHRoaXMuY2xlYXJTb3VyY2VGYWxsYmFja0NoZWNrKCk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgKiBpZnJhbWUg5raI5oGv6YCa6YGTCiAgICAgICAqID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PSAqLwogICAgICBoYW5kbGVJZnJhbWVMb2FkKCkgewogICAgICAgIHRoaXMuaWZyYW1lUmVhZHkgPSB0cnVlOwogICAgICAgIHRoaXMuaXNSZWNvcmRpbmcgPSBmYWxzZTsKICAgICAgICB0aGlzLmlzUGF1c2VkID0gZmFsc2U7CiAgICAgICAgdGhpcy5yZWNvcmRpbmdTZWNvbmRzID0gMDsKICAgICAgICB0aGlzLnJlY29yZGluZ0hhc0F1ZGlvID0gZmFsc2U7CiAgICAgICAgdGhpcy5yZWplY3RQZW5kaW5nQ29tbWFuZHMoJ2lmcmFtZSDlt7Lph43mlrDliqDovb0nKTsKICAgICAgICB0aGlzLnJlcXVlc3RQYWdlRXJyb3JzKCk7CiAgICAgICAgaWYgKCF0aGlzLnNjZW5lUmVhZHkpIHRoaXMuc3RhcnRTY2VuZUxvYWRpbmdUaW1lcnMoKTsKICAgICAgICB0aGlzLnJlcXVlc3RTY2VuZVN0YXR1cygpOwogICAgICAgIGlmICh0aGlzLmJyaWRnZUFsaXZlKSB0aGlzLmNsZWFyU291cmNlRmFsbGJhY2tDaGVjaygpOwogICAgICAgIGVsc2UgdGhpcy5zY2hlZHVsZVNvdXJjZUZhbGxiYWNrQ2hlY2soKTsKICAgICAgfSwKCiAgICAgIGhhbmRsZU1lc3NhZ2UoZXZlbnQpIHsKICAgICAgICBjb25zdCBtZXNzYWdlID0gZXZlbnQgJiYgZXZlbnQuZGF0YTsKICAgICAgICBpZiAoIW1lc3NhZ2UgfHwgbWVzc2FnZS5fX2xlYWZlckNhcHR1cmUgIT09IHRydWUpIHJldHVybjsKICAgICAgICBpZiAobWVzc2FnZS5zb3VyY2UgIT09ICdsZWFmZXItY2FwdHVyZScpIHJldHVybjsKICAgICAgICBpZiAobWVzc2FnZS5jb21tYW5kID09PSB0cnVlKSByZXR1cm47CgogICAgICAgIGNvbnN0IGZyYW1lID0gdGhpcy4kcmVmcy5wcmV2aWV3SWZyYW1lOwogICAgICAgIGlmICghZnJhbWUgfHwgIWZyYW1lLmNvbnRlbnRXaW5kb3cgfHwgZXZlbnQuc291cmNlICE9PSBmcmFtZS5jb250ZW50V2luZG93KSByZXR1cm47CgogICAgICAgIGlmIChtZXNzYWdlLmV2ZW50ID09PSAnYnJpZGdlLWFsaXZlJykgewogICAgICAgICAgdGhpcy5oYW5kbGVCcmlkZ2VBbGl2ZSgpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgaWYgKG1lc3NhZ2UuZXZlbnQgPT09ICdyZWFkeScpIHsKICAgICAgICAgIHRoaXMuaWZyYW1lUmVhZHkgPSB0cnVlOwogICAgICAgICAgdGhpcy5oYW5kbGVCcmlkZ2VBbGl2ZSgpOwogICAgICAgICAgaWYgKG1lc3NhZ2UuZGF0YSAmJiBtZXNzYWdlLmRhdGEuc2NlbmVSZWFkeSkgdGhpcy5tYXJrU2NlbmVSZWFkeSgpOwogICAgICAgICAgcmV0dXJuOwogICAgICAgIH0KCiAgICAgICAgaWYgKG1lc3NhZ2UuZXZlbnQgPT09ICdzY2VuZS1yZWFkeScpIHsKICAgICAgICAgIHRoaXMuaGFuZGxlQnJpZGdlQWxpdmUoKTsKICAgICAgICAgIHRoaXMubWFya1NjZW5lUmVhZHkoKTsKICAgICAgICAgIHJldHVybjsKICAgICAgICB9CgogICAgICAgIGlmIChtZXNzYWdlLmV2ZW50ID09PSAncGFnZS1lcnJvcicpIHsKICAgICAgICAgIHRoaXMuaGFuZGxlUGFnZUVycm9yKG1lc3NhZ2UuZGF0YSk7CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBpZiAobWVzc2FnZS5ldmVudCA9PT0gJ3Byb2dyZXNzJyB8fCBtZXNzYWdlLmV2ZW50ID09PSAnc3RhdGUnKSB7CiAgICAgICAgICBjb25zdCBpbmZvID0gbWVzc2FnZS5kYXRhIHx8IHt9OwogICAgICAgICAgaWYgKHRoaXMuaXNSZWNvcmRpbmcgJiYgdHlwZW9mIGluZm8uZHVyYXRpb24gPT09ICdudW1iZXInKSB7CiAgICAgICAgICAgIHRoaXMucmVjb3JkaW5nU2Vjb25kcyA9IGluZm8uZHVyYXRpb247CiAgICAgICAgICB9CiAgICAgICAgICBpZiAodGhpcy5pc1JlY29yZGluZyAmJiAoaW5mby5zdGF0ZSA9PT0gJ3BhdXNlZCcgfHwgaW5mby5zdGF0ZSA9PT0gJ3JlY29yZGluZycpKSB7CiAgICAgICAgICAgIHRoaXMuaXNQYXVzZWQgPSBpbmZvLnN0YXRlID09PSAncGF1c2VkJzsKICAgICAgICAgIH0KICAgICAgICAgIGlmIChtZXNzYWdlLmV2ZW50ID09PSAnc3RhdGUnICYmIGluZm8uc3RhdGUgPT09ICdzdG9wcGVkJykgewogICAgICAgICAgICB0aGlzLmlzUmVjb3JkaW5nID0gZmFsc2U7CiAgICAgICAgICAgIHRoaXMuaXNQYXVzZWQgPSBmYWxzZTsKICAgICAgICAgICAgdGhpcy5yZWNvcmRpbmdIYXNBdWRpbyA9IGZhbHNlOwogICAgICAgICAgICB0aGlzLnNjaGVkdWxlU3RhdHVzUmVzZXQoMTIwMCk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICBjb25zdCBwZW5kaW5nID0gdGhpcy5wZW5kaW5nQ29tbWFuZHNbbWVzc2FnZS5pZF07CiAgICAgICAgaWYgKHBlbmRpbmcpIHsKICAgICAgICAgIGRlbGV0ZSB0aGlzLnBlbmRpbmdDb21tYW5kc1ttZXNzYWdlLmlkXTsKICAgICAgICAgIGNsZWFyVGltZW91dChwZW5kaW5nLnRpbWVyKTsKICAgICAgICAgIGlmIChtZXNzYWdlLmV2ZW50ID09PSAnc3VjY2VzcycpIHsKICAgICAgICAgICAgcGVuZGluZy5yZXNvbHZlKG1lc3NhZ2UuZGF0YSk7CiAgICAgICAgICB9IGVsc2UgewogICAgICAgICAgICBwZW5kaW5nLnJlamVjdChuZXcgRXJyb3IoCiAgICAgICAgICAgICAgbWVzc2FnZS5kYXRhICYmIG1lc3NhZ2UuZGF0YS5tZXNzYWdlID8gbWVzc2FnZS5kYXRhLm1lc3NhZ2UgOiAnaWZyYW1lIOaJp+ihjOaMh+S7pOWksei0pScKICAgICAgICAgICAgKSk7CiAgICAgICAgICB9CiAgICAgICAgICByZXR1cm47CiAgICAgICAgfQoKICAgICAgICAvKiDkuLvliqjmjqjpgIHnmoTlvZXliLbplJnor6/vvIjmsqHmnInlr7nlupTnmoQgcGVuZGluZyDmjIfku6TvvIkgKi8KICAgICAgICBpZiAobWVzc2FnZS5ldmVudCA9PT0gJ2Vycm9yJyAmJiB0aGlzLmlzUmVjb3JkaW5nKSB7CiAgICAgICAgICB0aGlzLmlzUmVjb3JkaW5nID0gZmFsc2U7CiAgICAgICAgICB0aGlzLmlzUGF1c2VkID0gZmFsc2U7CiAgICAgICAgICB0aGlzLnJlY29yZGluZ0hhc0F1ZGlvID0gZmFsc2U7CiAgICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAn5b2V5Yi25aSx6LSl77yaJyArIHRoaXMuZXJyb3JNZXNzYWdlKG1lc3NhZ2UuZGF0YSk7CiAgICAgICAgICB0aGlzLnNjaGVkdWxlU3RhdHVzUmVzZXQoNTAwMCk7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgLyoqCiAgICAgICAqIOWQkSBpZnJhbWUg5Y+R6YCB5oiq5Zu+IC8g5b2V5Yi25oyH5Luk77yM5bm2562J5b6FIGlmcmFtZSDlm57kvKDnu5PmnpzjgIIKICAgICAgICovCiAgICAgIHBvc3RDb21tYW5kKGFjdGlvbiwgcGF5bG9hZCwgdGltZW91dE1zKSB7CiAgICAgICAgcmV0dXJuIG5ldyBQcm9taXNlKChyZXNvbHZlLCByZWplY3QpID0+IHsKICAgICAgICAgIGNvbnN0IGZyYW1lID0gdGhpcy4kcmVmcy5wcmV2aWV3SWZyYW1lOwogICAgICAgICAgaWYgKCFmcmFtZSB8fCAhZnJhbWUuY29udGVudFdpbmRvdykgewogICAgICAgICAgICByZWplY3QobmV3IEVycm9yKCfpooTop4jlsJrmnKrliqDovb3lrozmiJAnKSk7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KICAgICAgICAgIGlmICghdGhpcy5pZnJhbWVSZWFkeSkgewogICAgICAgICAgICByZWplY3QobmV3IEVycm9yKCfpooTop4jlsJrmnKrliqDovb3lrozmiJDvvIzor7fnqI3lkI7lho3or5UnKSk7CiAgICAgICAgICAgIHJldHVybjsKICAgICAgICAgIH0KCiAgICAgICAgICBjb25zdCBpZCA9ICd0bC0nICsgRGF0ZS5ub3coKS50b1N0cmluZygzNikgKyAnLScgKyAoKyt0aGlzLmNvbW1hbmRTZXEpOwogICAgICAgICAgY29uc3QgdGltZXIgPSBzZXRUaW1lb3V0KCgpID0+IHsKICAgICAgICAgICAgY29uc3QgcGVuZGluZyA9IHRoaXMucGVuZGluZ0NvbW1hbmRzW2lkXTsKICAgICAgICAgICAgaWYgKCFwZW5kaW5nKSByZXR1cm47CiAgICAgICAgICAgIGRlbGV0ZSB0aGlzLnBlbmRpbmdDb21tYW5kc1tpZF07CiAgICAgICAgICAgIHBlbmRpbmcucmVqZWN0KG5ldyBFcnJvcign5oyH5Luk5omn6KGM6LaF5pe277yaJyArIGFjdGlvbikpOwogICAgICAgICAgfSwgdGltZW91dE1zIHx8IDIwMDAwKTsKCiAgICAgICAgICB0aGlzLnBlbmRpbmdDb21tYW5kc1tpZF0gPSB7CiAgICAgICAgICAgIHJlc29sdmUsCiAgICAgICAgICAgIHJlamVjdCwKICAgICAgICAgICAgdGltZXIKICAgICAgICAgIH07CgogICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgZnJhbWUuY29udGVudFdpbmRvdy5wb3N0TWVzc2FnZSh7CiAgICAgICAgICAgICAgX19sZWFmZXJDYXB0dXJlOiB0cnVlLAogICAgICAgICAgICAgIGNvbW1hbmQ6IHRydWUsCiAgICAgICAgICAgICAgaWQ6IGlkLAogICAgICAgICAgICAgIGFjdGlvbjogYWN0aW9uLAogICAgICAgICAgICAgIHBheWxvYWQ6IHBheWxvYWQgfHwge30KICAgICAgICAgICAgfSwgJyonKTsKICAgICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICAgIGNsZWFyVGltZW91dCh0aW1lcik7CiAgICAgICAgICAgIGRlbGV0ZSB0aGlzLnBlbmRpbmdDb21tYW5kc1tpZF07CiAgICAgICAgICAgIHJlamVjdChlcnJvcik7CiAgICAgICAgICB9CiAgICAgICAgfSk7CiAgICAgIH0sCgogICAgICByZWplY3RQZW5kaW5nQ29tbWFuZHMocmVhc29uKSB7CiAgICAgICAgY29uc3QgaWRzID0gT2JqZWN0LmtleXModGhpcy5wZW5kaW5nQ29tbWFuZHMgfHwge30pOwogICAgICAgIGZvciAobGV0IGkgPSAwOyBpIDwgaWRzLmxlbmd0aDsgaSArPSAxKSB7CiAgICAgICAgICBjb25zdCBwZW5kaW5nID0gdGhpcy5wZW5kaW5nQ29tbWFuZHNbaWRzW2ldXTsKICAgICAgICAgIGRlbGV0ZSB0aGlzLnBlbmRpbmdDb21tYW5kc1tpZHNbaV1dOwogICAgICAgICAgaWYgKCFwZW5kaW5nKSBjb250aW51ZTsKICAgICAgICAgIGNsZWFyVGltZW91dChwZW5kaW5nLnRpbWVyKTsKICAgICAgICAgIHBlbmRpbmcucmVqZWN0KG5ldyBFcnJvcihyZWFzb24gfHwgJ+aMh+S7pOW3suWPlua2iCcpKTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgKiDmiKrlm74KICAgICAgICogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIGFzeW5jIHRha2VTY3JlZW5zaG90KCkgewogICAgICAgIGlmICghdGhpcy5pZnJhbWVSZWFkeSB8fCAhdGhpcy5zY2VuZVJlYWR5IHx8IHRoaXMuc2NyZWVuc2hvdEJ1c3kpIHJldHVybjsKCiAgICAgICAgdGhpcy5zY3JlZW5zaG90QnVzeSA9IHRydWU7CiAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJ+ato+WcqOeUn+aIkOaIquWbvuKApic7CgogICAgICAgIHRyeSB7CiAgICAgICAgICBjb25zdCByZXN1bHQgPSBhd2FpdCB0aGlzLnBvc3RDb21tYW5kKCdzY3JlZW5zaG90JywgewogICAgICAgICAgICBtaW1lVHlwZTogJ2ltYWdlL3BuZycsCiAgICAgICAgICAgIHF1YWxpdHk6IDAuOTUKICAgICAgICAgIH0sIDIwMDAwKTsKCiAgICAgICAgICBpZiAoIXJlc3VsdCB8fCAhcmVzdWx0LmRhdGFVcmwpIHRocm93IG5ldyBFcnJvcign5oiq5Zu+5pWw5o2u5Li656m6Jyk7CgogICAgICAgICAgdGhpcy5sYXN0U2NyZWVuc2hvdCA9IHJlc3VsdC5kYXRhVXJsOwogICAgICAgICAgdGhpcy5sYXN0U2NyZWVuc2hvdE5hbWUgPSB0aGlzLmJ1aWxkVGl0bGVGaWxlTmFtZSgncG5nJyk7CgogICAgICAgICAgdGhpcy5kb3dubG9hZFVybChyZXN1bHQuZGF0YVVybCwgdGhpcy5sYXN0U2NyZWVuc2hvdE5hbWUpOwogICAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJ+aIquWbvuW3suS/neWtmO+8iCcgKwogICAgICAgICAgICByZXN1bHQud2lkdGggKyAnw5cnICsgcmVzdWx0LmhlaWdodCArICfvvIknOwoKICAgICAgICAgIHRoaXMuJGVtaXQoJ2NhcHR1cmUtc2NyZWVuc2hvdCcsIHsKICAgICAgICAgICAgZGF0YVVybDogcmVzdWx0LmRhdGFVcmwsCiAgICAgICAgICAgIHdpZHRoOiByZXN1bHQud2lkdGgsCiAgICAgICAgICAgIGhlaWdodDogcmVzdWx0LmhlaWdodCwKICAgICAgICAgICAgc2l6ZTogcmVzdWx0LnNpemUsCiAgICAgICAgICAgIGZpbGVuYW1lOiB0aGlzLmxhc3RTY3JlZW5zaG90TmFtZQogICAgICAgICAgfSk7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIHRoaXMuc3RhdHVzTWVzc2FnZSA9ICfmiKrlm77lpLHotKXvvJonICsgdGhpcy5lcnJvck1lc3NhZ2UoZXJyb3IpOwogICAgICAgICAgY29uc29sZS53YXJuKCdbTGVhZmVySlMtVmlld2VyXSBzY3JlZW5zaG90IGZhaWxlZDonLCBlcnJvcik7CiAgICAgICAgICB0aGlzLiRlbWl0KCdjYXB0dXJlLWVycm9yJywgewogICAgICAgICAgICBhY3Rpb246ICdzY3JlZW5zaG90JywKICAgICAgICAgICAgZXJyb3I6IGVycm9yCiAgICAgICAgICB9KTsKICAgICAgICB9IGZpbmFsbHkgewogICAgICAgICAgdGhpcy5zY3JlZW5zaG90QnVzeSA9IGZhbHNlOwogICAgICAgICAgdGhpcy5zY2hlZHVsZVN0YXR1c1Jlc2V0KDMwMDApOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIC8qID09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09CiAgICAgICAqIOW9leWItgogICAgICAgKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT0gKi8KICAgICAgYXN5bmMgc3RhcnRSZWNvcmRpbmcoKSB7CiAgICAgICAgaWYgKCF0aGlzLmlmcmFtZVJlYWR5IHx8ICF0aGlzLnNjZW5lUmVhZHkgfHwgdGhpcy5yZWNvcmRpbmdCdXN5IHx8IHRoaXMuaXNSZWNvcmRpbmcpIHJldHVybjsKCiAgICAgICAgdGhpcy5yZWNvcmRpbmdCdXN5ID0gdHJ1ZTsKICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAn5q2j5Zyo5ZCv5Yqo5b2V5Yi24oCmJzsKCiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IGluZm8gPSBhd2FpdCB0aGlzLnBvc3RDb21tYW5kKCdzdGFydFJlY29yZGluZycsIHsKICAgICAgICAgICAgZnBzOiAzMCwKICAgICAgICAgICAgYml0cmF0ZTogMTIwMDAwMDAKICAgICAgICAgIH0sIDIwMDAwKTsKCiAgICAgICAgICB0aGlzLmlzUmVjb3JkaW5nID0gdHJ1ZTsKICAgICAgICAgIHRoaXMuaXNQYXVzZWQgPSBmYWxzZTsKICAgICAgICAgIHRoaXMucmVjb3JkaW5nU2Vjb25kcyA9IDA7CiAgICAgICAgICB0aGlzLnJlY29yZGluZ0hhc0F1ZGlvID0gISEoaW5mbyAmJiBpbmZvLmF1ZGlvICYmIGluZm8uYXVkaW8uaW5jbHVkZWQpOwogICAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJyc7CgogICAgICAgICAgdGhpcy4kZW1pdCgnY2FwdHVyZS1yZWNvcmRpbmctc3RhcnQnLCBpbmZvIHx8IHt9KTsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgdGhpcy5pc1JlY29yZGluZyA9IGZhbHNlOwogICAgICAgICAgdGhpcy5pc1BhdXNlZCA9IGZhbHNlOwogICAgICAgICAgdGhpcy5yZWNvcmRpbmdIYXNBdWRpbyA9IGZhbHNlOwogICAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJ+W9leWItuWQr+WKqOWksei0pe+8micgKyB0aGlzLmVycm9yTWVzc2FnZShlcnJvcik7CiAgICAgICAgICBjb25zb2xlLndhcm4oJ1tMZWFmZXJKUy1WaWV3ZXJdIHN0YXJ0IHJlY29yZGluZyBmYWlsZWQ6JywgZXJyb3IpOwogICAgICAgICAgdGhpcy4kZW1pdCgnY2FwdHVyZS1lcnJvcicsIHsKICAgICAgICAgICAgYWN0aW9uOiAnc3RhcnRSZWNvcmRpbmcnLAogICAgICAgICAgICBlcnJvcjogZXJyb3IKICAgICAgICAgIH0pOwogICAgICAgICAgdGhpcy5zY2hlZHVsZVN0YXR1c1Jlc2V0KDUwMDApOwogICAgICAgIH0gZmluYWxseSB7CiAgICAgICAgICB0aGlzLnJlY29yZGluZ0J1c3kgPSBmYWxzZTsKICAgICAgICB9CiAgICAgIH0sCgogICAgICBhc3luYyB0b2dnbGVSZWNvcmRpbmdQYXVzZSgpIHsKICAgICAgICBpZiAoIXRoaXMuaWZyYW1lUmVhZHkgfHwgIXRoaXMuaXNSZWNvcmRpbmcgfHwgdGhpcy5yZWNvcmRpbmdCdXN5KSByZXR1cm47CgogICAgICAgIHRoaXMucmVjb3JkaW5nQnVzeSA9IHRydWU7CiAgICAgICAgY29uc3QgYWN0aW9uID0gdGhpcy5pc1BhdXNlZCA/ICdyZXN1bWVSZWNvcmRpbmcnIDogJ3BhdXNlUmVjb3JkaW5nJzsKCiAgICAgICAgdHJ5IHsKICAgICAgICAgIGNvbnN0IHJlc3VsdCA9IGF3YWl0IHRoaXMucG9zdENvbW1hbmQoYWN0aW9uLCB7fSwgMTAwMDApOwogICAgICAgICAgdGhpcy5pc1BhdXNlZCA9ICEhKHJlc3VsdCAmJiByZXN1bHQucGF1c2VkKTsKICAgICAgICAgIGlmICh0eXBlb2YgcmVzdWx0LmR1cmF0aW9uID09PSAnbnVtYmVyJykgdGhpcy5yZWNvcmRpbmdTZWNvbmRzID0gcmVzdWx0LmR1cmF0aW9uOwogICAgICAgICAgdGhpcy4kZW1pdCh0aGlzLmlzUGF1c2VkID8gJ2NhcHR1cmUtcmVjb3JkaW5nLXBhdXNlJyA6ICdjYXB0dXJlLXJlY29yZGluZy1yZXN1bWUnLCByZXN1bHQgfHwge30pOwogICAgICAgIH0gY2F0Y2ggKGVycm9yKSB7CiAgICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAodGhpcy5pc1BhdXNlZCA/ICfnu6fnu63lvZXliLblpLHotKXvvJonIDogJ+aaguWBnOW9leWItuWksei0pe+8micpICsKICAgICAgICAgICAgdGhpcy5lcnJvck1lc3NhZ2UoZXJyb3IpOwogICAgICAgICAgY29uc29sZS53YXJuKCdbTGVhZmVySlMtVmlld2VyXSB0b2dnbGUgcmVjb3JkaW5nIHBhdXNlIGZhaWxlZDonLCBlcnJvcik7CiAgICAgICAgICB0aGlzLiRlbWl0KCdjYXB0dXJlLWVycm9yJywgewogICAgICAgICAgICBhY3Rpb246IGFjdGlvbiwKICAgICAgICAgICAgZXJyb3I6IGVycm9yCiAgICAgICAgICB9KTsKICAgICAgICAgIHRoaXMuc2NoZWR1bGVTdGF0dXNSZXNldCg1MDAwKTsKICAgICAgICB9IGZpbmFsbHkgewogICAgICAgICAgdGhpcy5yZWNvcmRpbmdCdXN5ID0gZmFsc2U7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgYXN5bmMgc3RvcFJlY29yZGluZygpIHsKICAgICAgICBpZiAoIXRoaXMuaWZyYW1lUmVhZHkgfHwgIXRoaXMuaXNSZWNvcmRpbmcgfHwgdGhpcy5yZWNvcmRpbmdCdXN5KSByZXR1cm47CgogICAgICAgIHRoaXMucmVjb3JkaW5nQnVzeSA9IHRydWU7CiAgICAgICAgdGhpcy5zdGF0dXNNZXNzYWdlID0gJ+ato+WcqOeUn+aIkOinhumikeaWh+S7tuKApic7CgogICAgICAgIHRyeSB7CiAgICAgICAgICBjb25zdCBoYWRBdWRpbyA9IHRoaXMucmVjb3JkaW5nSGFzQXVkaW87CiAgICAgICAgICBjb25zdCByZXN1bHQgPSBhd2FpdCB0aGlzLnBvc3RDb21tYW5kKCdzdG9wUmVjb3JkaW5nJywge30sIDEyMDAwMCk7CgogICAgICAgICAgdGhpcy5pc1JlY29yZGluZyA9IGZhbHNlOwogICAgICAgICAgdGhpcy5pc1BhdXNlZCA9IGZhbHNlOwogICAgICAgICAgdGhpcy5yZWNvcmRpbmdTZWNvbmRzID0gMDsKICAgICAgICAgIHRoaXMucmVjb3JkaW5nSGFzQXVkaW8gPSBmYWxzZTsKCiAgICAgICAgICBhd2FpdCB0aGlzLmhhbmRsZVJlY29yZGluZ1Jlc3VsdChyZXN1bHQpOwoKICAgICAgICAgIHRoaXMuc3RhdHVzTWVzc2FnZSA9ICfop4bpopHlt7Lkv53lrZjvvIjml7bplb8gJyArCiAgICAgICAgICAgIHRoaXMuZm9ybWF0RHVyYXRpb24ocmVzdWx0ICYmIHJlc3VsdC5kdXJhdGlvbikgKwogICAgICAgICAgICAoaGFkQXVkaW8gPyAn77yM5ZCr6Z+z6aKRJyA6ICcnKSArCiAgICAgICAgICAgICfvvIknOwoKICAgICAgICAgIHRoaXMuJGVtaXQoJ2NhcHR1cmUtcmVjb3JkaW5nLXN0b3AnLCB7CiAgICAgICAgICAgIG1pbWVUeXBlOiByZXN1bHQgJiYgcmVzdWx0Lm1pbWVUeXBlLAogICAgICAgICAgICBkdXJhdGlvbjogcmVzdWx0ICYmIHJlc3VsdC5kdXJhdGlvbiwKICAgICAgICAgICAgc2l6ZTogcmVzdWx0ICYmIHJlc3VsdC5zaXplLAogICAgICAgICAgICBmaWxlbmFtZTogdGhpcy5sYXN0VmlkZW9OYW1lLAogICAgICAgICAgICBoYXNBdWRpbzogaGFkQXVkaW8sCiAgICAgICAgICAgIGJsb2I6IHJlc3VsdCAmJiByZXN1bHQuYmxvYgogICAgICAgICAgfSk7CiAgICAgICAgfSBjYXRjaCAoZXJyb3IpIHsKICAgICAgICAgIHRoaXMuaXNSZWNvcmRpbmcgPSBmYWxzZTsKICAgICAgICAgIHRoaXMuaXNQYXVzZWQgPSBmYWxzZTsKICAgICAgICAgIHRoaXMucmVjb3JkaW5nSGFzQXVkaW8gPSBmYWxzZTsKICAgICAgICAgIHRoaXMuc3RhdHVzTWVzc2FnZSA9ICflgZzmraLlvZXliLblpLHotKXvvJonICsgdGhpcy5lcnJvck1lc3NhZ2UoZXJyb3IpOwogICAgICAgICAgY29uc29sZS53YXJuKCdbTGVhZmVySlMtVmlld2VyXSBzdG9wIHJlY29yZGluZyBmYWlsZWQ6JywgZXJyb3IpOwogICAgICAgICAgdGhpcy4kZW1pdCgnY2FwdHVyZS1lcnJvcicsIHsKICAgICAgICAgICAgYWN0aW9uOiAnc3RvcFJlY29yZGluZycsCiAgICAgICAgICAgIGVycm9yOiBlcnJvcgogICAgICAgICAgfSk7CiAgICAgICAgfSBmaW5hbGx5IHsKICAgICAgICAgIHRoaXMucmVjb3JkaW5nQnVzeSA9IGZhbHNlOwogICAgICAgICAgdGhpcy5zY2hlZHVsZVN0YXR1c1Jlc2V0KDQwMDApOwogICAgICAgIH0KICAgICAgfSwKCiAgICAgIGFzeW5jIGhhbmRsZVJlY29yZGluZ1Jlc3VsdChyZXN1bHQpIHsKICAgICAgICBpZiAoIXJlc3VsdCkgdGhyb3cgbmV3IEVycm9yKCflvZXliLbnu5PmnpzkuLrnqbonKTsKCiAgICAgICAgbGV0IHVybCA9ICcnOwogICAgICAgIGNvbnN0IG1pbWVUeXBlID0gcmVzdWx0Lm1pbWVUeXBlIHx8ICd2aWRlby93ZWJtJzsKICAgICAgICBjb25zdCBleHRlbnNpb24gPSAvbXA0L2kudGVzdChtaW1lVHlwZSkgPyAnbXA0JyA6ICd3ZWJtJzsKICAgICAgICAvKiDlr7zlh7rmlofku7bnu5/kuIDku6UgYWNjb3VudERhdGEudGl0bGUg5ZG95ZCN77ybaWZyYW1lIOWGhemDqOeahOS4tOaXtuaWh+S7tuWQjeS4jeWGjeS9v+eUqO+8jAogICAgICAgICAqIOmBv+WFjeWHuueOsOS4pOWll+WRveWQjeinhOWImeOAgiAqLwogICAgICAgIGNvbnN0IGZpbGVuYW1lID0gdGhpcy5idWlsZFRpdGxlRmlsZU5hbWUoZXh0ZW5zaW9uKTsKCiAgICAgICAgdGhpcy5yZWxlYXNlVmlkZW9PYmplY3RVcmwoKTsKCiAgICAgICAgaWYgKHJlc3VsdC5ibG9iKSB7CiAgICAgICAgICB1cmwgPSBVUkwuY3JlYXRlT2JqZWN0VVJMKHJlc3VsdC5ibG9iKTsKICAgICAgICAgIHRoaXMubGFzdFZpZGVvT2JqZWN0VXJsID0gdXJsOwogICAgICAgIH0gZWxzZSBpZiAocmVzdWx0LmRhdGFVcmwpIHsKICAgICAgICAgIHVybCA9IHJlc3VsdC5kYXRhVXJsOwogICAgICAgIH0gZWxzZSB7CiAgICAgICAgICB0aHJvdyBuZXcgRXJyb3IoJ+W9leWItue7k+aenOe8uuWwkeinhumikeaVsOaNricpOwogICAgICAgIH0KCiAgICAgICAgdGhpcy5sYXN0VmlkZW9VcmwgPSB1cmw7CiAgICAgICAgdGhpcy5sYXN0VmlkZW9OYW1lID0gZmlsZW5hbWU7CgogICAgICAgIHRoaXMuZG93bmxvYWRVcmwodXJsLCBmaWxlbmFtZSk7CiAgICAgIH0sCgogICAgICAvKiA9PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PQogICAgICAgKiDkuIvovb0gLyDmuIXnkIYgLyDlhbbku5blt6XlhbcKICAgICAgICogPT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09PT09ICovCiAgICAgIGRvd25sb2FkVXJsKHVybCwgZmlsZW5hbWUpIHsKICAgICAgICBpZiAoIXVybCkgcmV0dXJuOwogICAgICAgIHRyeSB7CiAgICAgICAgICBjb25zdCBsaW5rID0gZG9jdW1lbnQuY3JlYXRlRWxlbWVudCgnYScpOwogICAgICAgICAgbGluay5ocmVmID0gdXJsOwogICAgICAgICAgbGluay5kb3dubG9hZCA9IGZpbGVuYW1lIHx8ICdkb3dubG9hZCc7CiAgICAgICAgICBsaW5rLnJlbCA9ICdub29wZW5lcic7CiAgICAgICAgICBsaW5rLnN0eWxlLmRpc3BsYXkgPSAnbm9uZSc7CiAgICAgICAgICBkb2N1bWVudC5ib2R5LmFwcGVuZENoaWxkKGxpbmspOwogICAgICAgICAgbGluay5jbGljaygpOwogICAgICAgICAgbGluay5yZW1vdmUoKTsKICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgY29uc29sZS53YXJuKCdbTGVhZmVySlMtVmlld2VyXSBhdXRvIGRvd25sb2FkIGZhaWxlZDonLCBlcnJvcik7CiAgICAgICAgfQogICAgICB9LAoKICAgICAgcmVsZWFzZVZpZGVvT2JqZWN0VXJsKCkgewogICAgICAgIGlmICh0aGlzLmxhc3RWaWRlb09iamVjdFVybCkgewogICAgICAgICAgdHJ5IHsKICAgICAgICAgICAgVVJMLnJldm9rZU9iamVjdFVSTCh0aGlzLmxhc3RWaWRlb09iamVjdFVybCk7CiAgICAgICAgICB9IGNhdGNoIChlcnJvcikgewogICAgICAgICAgICAvKiDlv73nlaUgKi8gfQogICAgICAgICAgdGhpcy5sYXN0VmlkZW9PYmplY3RVcmwgPSAnJzsKICAgICAgICB9CiAgICAgIH0sCgogICAgICAvKioKICAgICAgICog5Zu+54mH44CB6KeG6aKR57uf5LiA5L2/55SoIGFjY291bnREYXRhLnRpdGxlIOS9nOS4uuaWh+S7tuWQje+8mgogICAgICAgKiAgIOWNleaWh+S7tiBMZWFmZXJKUyDliqjnlLvnvZHpobXnlJ/miJDop4TojIMucG5nIC8gLndlYm0KICAgICAgICog5Y+q5pu/5o2i5pON5L2c57O757uf5LiN5YWB6K6455qE5a2X56ym77yM5L+d55WZ5Lit5paH5LiO56m65qC877yb6YeN5ZCN5pe255Sx5rWP6KeI5Zmo6Ieq5Yqo6L+95Yqg5bqP5Y+344CCCiAgICAgICAqLwogICAgICBidWlsZFRpdGxlRmlsZU5hbWUoZXh0ZW5zaW9uKSB7CiAgICAgICAgY29uc3QgcmF3VGl0bGUgPSB0aGlzLmFjY291bnREYXRhICYmIHRoaXMuYWNjb3VudERhdGEudGl0bGUgPwogICAgICAgICAgU3RyaW5nKHRoaXMuYWNjb3VudERhdGEudGl0bGUpIDoKICAgICAgICAgICfliqjnlLvkvZzlk4EnOwogICAgICAgIGNvbnN0IHNhZmVUaXRsZSA9IHJhd1RpdGxlCiAgICAgICAgICAucmVwbGFjZSgvW1xcLzoqPyI8PnxcclxuXSsvZywgJy0nKQogICAgICAgICAgLnJlcGxhY2UoL1xzKy9nLCAnICcpCiAgICAgICAgICAudHJpbSgpCiAgICAgICAgICAucmVwbGFjZSgvXlwuK3xcLiskL2csICcnKQogICAgICAgICAgLnNsaWNlKDAsIDgwKQogICAgICAgICAgLnRyaW0oKTsKICAgICAgICBjb25zdCBzYWZlRXh0ZW5zaW9uID0gU3RyaW5nKGV4dGVuc2lvbiB8fCAncG5nJykucmVwbGFjZSgvXlwuKy8sICcnKSB8fCAncG5nJzsKICAgICAgICByZXR1cm4gKHNhZmVUaXRsZSB8fCAn5Yqo55S75L2c5ZOBJykgKyAnLicgKyBzYWZlRXh0ZW5zaW9uOwogICAgICB9LAoKICAgICAgZm9ybWF0RHVyYXRpb24oc2Vjb25kcykgewogICAgICAgIGNvbnN0IHRvdGFsID0gTWF0aC5tYXgoMCwgTWF0aC5mbG9vcihOdW1iZXIoc2Vjb25kcykgfHwgMCkpOwogICAgICAgIGNvbnN0IG1pbnV0ZXMgPSBNYXRoLmZsb29yKHRvdGFsIC8gNjApOwogICAgICAgIGNvbnN0IHJlbWFpbiA9IHRvdGFsICUgNjA7CiAgICAgICAgcmV0dXJuIFN0cmluZyhtaW51dGVzKS5wYWRTdGFydCgyLCAnMCcpICsgJzonICsgU3RyaW5nKHJlbWFpbikucGFkU3RhcnQoMiwgJzAnKTsKICAgICAgfSwKCiAgICAgIGVycm9yTWVzc2FnZShlcnJvcikgewogICAgICAgIGlmICghZXJyb3IpIHJldHVybiAn5pyq55+l6ZSZ6K+vJzsKICAgICAgICBpZiAoZXJyb3IubWVzc2FnZSkgcmV0dXJuIGVycm9yLm1lc3NhZ2U7CiAgICAgICAgaWYgKGVycm9yLmRhdGEgJiYgZXJyb3IuZGF0YS5tZXNzYWdlKSByZXR1cm4gZXJyb3IuZGF0YS5tZXNzYWdlOwogICAgICAgIHJldHVybiBTdHJpbmcoZXJyb3IpOwogICAgICB9LAoKICAgICAgc2NoZWR1bGVTdGF0dXNSZXNldChkZWxheSkgewogICAgICAgIGlmICh0aGlzLnN0YXR1c1RpbWVyKSB7CiAgICAgICAgICBjbGVhclRpbWVvdXQodGhpcy5zdGF0dXNUaW1lcik7CiAgICAgICAgICB0aGlzLnN0YXR1c1RpbWVyID0gbnVsbDsKICAgICAgICB9CiAgICAgICAgdGhpcy5zdGF0dXNUaW1lciA9IHNldFRpbWVvdXQoKCkgPT4gewogICAgICAgICAgdGhpcy5zdGF0dXNUaW1lciA9IG51bGw7CiAgICAgICAgICBpZiAoIXRoaXMuaXNSZWNvcmRpbmcgJiYgIXRoaXMuc2NyZWVuc2hvdEJ1c3kgJiYgIXRoaXMucmVjb3JkaW5nQnVzeSkgewogICAgICAgICAgICB0aGlzLnN0YXR1c01lc3NhZ2UgPSAnJzsKICAgICAgICAgIH0KICAgICAgICB9LCBkZWxheSB8fCAzMDAwKTsKICAgICAgfQogICAgfQogIH07Cjwvc2NyaXB0PgoKPHN0eWxlPgogIC8qIOehruS/neeItue6p+WFg+e0oOWcqOWFqOWxj+W4g+WxgOS4reiDveWkn+eZvuWIhueZvuaSkei1t+mrmOW6piAqLwogIGh0bWwsCiAgYm9keSwKICAjYXBwIHsKICAgIGhlaWdodDogMTAwJTsKICAgIG1hcmdpbjogMDsKICB9CgogIC5yZXN1bHRSb290Q29udGFpbmVyIHsKICAgIHBvc2l0aW9uOiByZWxhdGl2ZTsKICAgIGJvcmRlci1yYWRpdXM6IDE2cHg7CiAgICBib3JkZXItY29sb3I6ICNlOGVjZjM7CiAgICBib3gtc2hhZG93OiAwIDFweCAycHggcmdiYSgxNSwgMjMsIDQyLCAwLjA0KSwgMCAxMnB4IDMycHggLTEycHggcmdiYSgxNSwgMjMsIDQyLCAwLjEyKTsKICB9CgogIC5yZXN1bHRSb290Q29udGFpbmVyOjpiZWZvcmUgewogICAgY29udGVudDogJyc7CiAgICBwb3NpdGlvbjogYWJzb2x1dGU7CiAgICB0b3A6IDA7CiAgICBsZWZ0OiAwOwogICAgcmlnaHQ6IDA7CiAgICBoZWlnaHQ6IDNweDsKICAgIGJhY2tncm91bmQ6IGxpbmVhci1ncmFkaWVudCg5MGRlZywgIzYzNjZmMSAwJSwgIzA2YjZkNCA1MCUsICNmNTllMGIgMTAwJSk7CiAgICBwb2ludGVyLWV2ZW50czogbm9uZTsKICAgIHotaW5kZXg6IDI7CiAgfQoKICAvKiAtLS0tLS0tLS0tIOWKqOeUu+eUu+W4g+WKoOi9vemBrue9qSAtLS0tLS0tLS0tICovCiAgLnRsLXNjZW5lLWxvYWRpbmcgewogICAgcG9zaXRpb246IGFic29sdXRlOwogICAgaW5zZXQ6IDA7CiAgICB6LWluZGV4OiAzMDsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBwYWRkaW5nOiAyNHB4OwogICAgYmFja2dyb3VuZDogcmdiYSgxNSwgMjMsIDQyLCAwLjkpOwogICAgLXdlYmtpdC1iYWNrZHJvcC1maWx0ZXI6IGJsdXIoM3B4KTsKICAgIGJhY2tkcm9wLWZpbHRlcjogYmx1cigzcHgpOwogIH0KCiAgLnRsLXNjZW5lLWxvYWRpbmctY2FyZCB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgYWxpZ24taXRlbXM6IGNlbnRlcjsKICAgIGdhcDogMTRweDsKICAgIG1heC13aWR0aDogNDQwcHg7CiAgICBwYWRkaW5nOiAxNnB4IDE4cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCByZ2JhKDE0OCwgMTYzLCAxODQsIDAuMjQpOwogICAgYm9yZGVyLXJhZGl1czogMTRweDsKICAgIGJhY2tncm91bmQ6IHJnYmEoMzAsIDQxLCA1OSwgMC45NCk7CiAgICBib3gtc2hhZG93OiAwIDI0cHggNjBweCAtMjBweCByZ2JhKDAsIDAsIDAsIDAuNjUpOwogIH0KCiAgLnRsLXNjZW5lLWxvYWRpbmctc3Bpbm5lciB7CiAgICBmbGV4LXNocmluazogMDsKICAgIHdpZHRoOiAyOHB4OwogICAgaGVpZ2h0OiAyOHB4OwogICAgYm9yZGVyOiAzcHggc29saWQgcmdiYSgxNDgsIDE2MywgMTg0LCAwLjI4KTsKICAgIGJvcmRlci10b3AtY29sb3I6ICMyMmQzZWU7CiAgICBib3JkZXItcmFkaXVzOiA5OTk5cHg7CiAgICBhbmltYXRpb246IHRsLXNjZW5lLWxvYWRpbmctc3BpbiAwLjg1cyBsaW5lYXIgaW5maW5pdGU7CiAgfQoKICAudGwtc2NlbmUtbG9hZGluZy1jb250ZW50IHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBmbGV4LWRpcmVjdGlvbjogY29sdW1uOwogICAgZ2FwOiAzcHg7CiAgICBtaW4td2lkdGg6IDA7CiAgfQoKICAudGwtc2NlbmUtbG9hZGluZy1jb250ZW50IHN0cm9uZyB7CiAgICBjb2xvcjogI2Y4ZmFmYzsKICAgIGZvbnQtc2l6ZTogMTNweDsKICAgIGZvbnQtd2VpZ2h0OiA2MDA7CiAgICBsaW5lLWhlaWdodDogMS41OwogIH0KCiAgLnRsLXNjZW5lLWxvYWRpbmctY29udGVudCBzcGFuIHsKICAgIGNvbG9yOiAjOTRhM2I4OwogICAgZm9udC1zaXplOiAxMXB4OwogICAgbGluZS1oZWlnaHQ6IDEuNjsKICB9CgogIEBrZXlmcmFtZXMgdGwtc2NlbmUtbG9hZGluZy1zcGluIHsKICAgIHRvIHsKICAgICAgdHJhbnNmb3JtOiByb3RhdGUoMzYwZGVnKTsKICAgIH0KICB9CgogIC50bC1sb2FkaW5nLWZhZGUtZW50ZXItYWN0aXZlLAogIC50bC1sb2FkaW5nLWZhZGUtbGVhdmUtYWN0aXZlIHsKICAgIHRyYW5zaXRpb246IG9wYWNpdHkgMC4ycyBlYXNlOwogIH0KCiAgLnRsLWxvYWRpbmctZmFkZS1lbnRlciwKICAudGwtbG9hZGluZy1mYWRlLWVudGVyLWZyb20sCiAgLnRsLWxvYWRpbmctZmFkZS1sZWF2ZS10byB7CiAgICBvcGFjaXR5OiAwOwogIH0KCiAgLyogLS0tLS0tLS0tLSDmiKrlm74gLyDlvZXliLblt6XlhbfmoI8gLS0tLS0tLS0tLSAqLwogIC50bC1jYXB0dXJlLXRvb2xiYXIgewogICAgZGlzcGxheTogZmxleDsKICAgIGZsZXgtd3JhcDogd3JhcDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IGZsZXgtZW5kOwogICAgZ2FwOiA4cHg7CiAgICBmbGV4LXNocmluazogMDsKICB9CgogIC50bC1jYXB0dXJlLXN0YXR1cyB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBnYXA6IDZweDsKICAgIG1pbi1oZWlnaHQ6IDI4cHg7CiAgICBmb250LXNpemU6IDEycHg7CiAgICBsaW5lLWhlaWdodDogMS40OwogICAgY29sb3I6ICM2NDc0OGI7CiAgICB3aGl0ZS1zcGFjZTogbm93cmFwOwogIH0KCiAgLnRsLWNhcHR1cmUtZG90IHsKICAgIGRpc3BsYXk6IGlubGluZS1ibG9jazsKICAgIHdpZHRoOiA4cHg7CiAgICBoZWlnaHQ6IDhweDsKICAgIGJvcmRlci1yYWRpdXM6IDk5OTlweDsKICAgIGJhY2tncm91bmQ6ICNlZjQ0NDQ7CiAgICBhbmltYXRpb246IHRsLWNhcHR1cmUtcHVsc2UgMS40cyBlYXNlLW91dCBpbmZpbml0ZTsKICB9CgogIC50bC1jYXB0dXJlLWRvdC5pcy1wYXVzZWQgewogICAgYmFja2dyb3VuZDogI2Y1OWUwYjsKICAgIGFuaW1hdGlvbjogbm9uZTsKICB9CgogIEBrZXlmcmFtZXMgdGwtY2FwdHVyZS1wdWxzZSB7CiAgICAwJSB7CiAgICAgIGJveC1zaGFkb3c6IDAgMCAwIDAgcmdiYSgyMzksIDY4LCA2OCwgMC41KTsKICAgIH0KCiAgICA3MCUgewogICAgICBib3gtc2hhZG93OiAwIDAgMCA2cHggcmdiYSgyMzksIDY4LCA2OCwgMCk7CiAgICB9CgogICAgMTAwJSB7CiAgICAgIGJveC1zaGFkb3c6IDAgMCAwIDAgcmdiYSgyMzksIDY4LCA2OCwgMCk7CiAgICB9CiAgfQoKICAudGwtY2FwdHVyZS1idG4gewogICAgZGlzcGxheTogaW5saW5lLWZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBnYXA6IDZweDsKICAgIGhlaWdodDogMzJweDsKICAgIHBhZGRpbmc6IDAgMTBweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkICNlMmU4ZjA7CiAgICBib3JkZXItcmFkaXVzOiA4cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmZmZmZmOwogICAgY29sb3I6ICMzMzQxNTU7CiAgICBmb250LXNpemU6IDEycHg7CiAgICBmb250LXdlaWdodDogNTAwOwogICAgbGluZS1oZWlnaHQ6IDE7CiAgICBjdXJzb3I6IHBvaW50ZXI7CiAgICB0cmFuc2l0aW9uOiBiYWNrZ3JvdW5kLWNvbG9yIDAuMTVzIGVhc2UsIGJvcmRlci1jb2xvciAwLjE1cyBlYXNlLCBjb2xvciAwLjE1cyBlYXNlLCBib3gtc2hhZG93IDAuMTVzIGVhc2U7CiAgfQoKICAudGwtY2FwdHVyZS1idG46aG92ZXI6bm90KDpkaXNhYmxlZCkgewogICAgYm9yZGVyLWNvbG9yOiAjYzdkMmZlOwogICAgYmFja2dyb3VuZDogI2VlZjJmZjsKICAgIGNvbG9yOiAjNDMzOGNhOwogIH0KCiAgLnRsLWNhcHR1cmUtYnRuOmZvY3VzLXZpc2libGUgewogICAgb3V0bGluZTogMnB4IHNvbGlkICM2MzY2ZjE7CiAgICBvdXRsaW5lLW9mZnNldDogMnB4OwogIH0KCiAgLnRsLWNhcHR1cmUtYnRuOmRpc2FibGVkIHsKICAgIG9wYWNpdHk6IDAuNTsKICAgIGN1cnNvcjogbm90LWFsbG93ZWQ7CiAgfQoKICAudGwtY2FwdHVyZS1idG4tLXJlY29yZCB7CiAgICBib3JkZXItY29sb3I6ICNmZWNhY2E7CiAgICBjb2xvcjogI2RjMjYyNjsKICB9CgogIC50bC1jYXB0dXJlLWJ0bi0tcmVjb3JkOmhvdmVyOm5vdCg6ZGlzYWJsZWQpIHsKICAgIGJvcmRlci1jb2xvcjogI2ZjYTVhNTsKICAgIGJhY2tncm91bmQ6ICNmZWYyZjI7CiAgICBjb2xvcjogI2I5MWMxYzsKICB9CgogIC50bC1jYXB0dXJlLWJ0bi0tc3RvcCB7CiAgICBib3JkZXItY29sb3I6ICNmZWNhY2E7CiAgICBiYWNrZ3JvdW5kOiAjZGMyNjI2OwogICAgY29sb3I6ICNmZmZmZmY7CiAgfQoKICAudGwtY2FwdHVyZS1idG4tLXN0b3A6aG92ZXI6bm90KDpkaXNhYmxlZCkgewogICAgYm9yZGVyLWNvbG9yOiAjYjkxYzFjOwogICAgYmFja2dyb3VuZDogI2I5MWMxYzsKICAgIGNvbG9yOiAjZmZmZmZmOwogIH0KCiAgLnRsLWNhcHR1cmUtaWNvbiB7CiAgICB3aWR0aDogMTRweDsKICAgIGhlaWdodDogMTRweDsKICAgIGZsZXgtc2hyaW5rOiAwOwogIH0KCiAgLnRsLWNhcHR1cmUtdGh1bWIgewogICAgZGlzcGxheTogYmxvY2s7CiAgICB3aWR0aDogNDRweDsKICAgIGhlaWdodDogMjhweDsKICAgIGJvcmRlcjogMXB4IHNvbGlkICNlMmU4ZjA7CiAgICBib3JkZXItcmFkaXVzOiA2cHg7CiAgICBvdmVyZmxvdzogaGlkZGVuOwogICAgYmFja2dyb3VuZDogI2Y4ZmFmYzsKICB9CgogIC50bC1jYXB0dXJlLXRodW1iIGltZyB7CiAgICBkaXNwbGF5OiBibG9jazsKICAgIHdpZHRoOiAxMDAlOwogICAgaGVpZ2h0OiAxMDAlOwogICAgb2JqZWN0LWZpdDogY292ZXI7CiAgfQoKICAudGwtY2FwdHVyZS1saW5rIHsKICAgIGZvbnQtc2l6ZTogMTJweDsKICAgIGNvbG9yOiAjNGY0NmU1OwogICAgdGV4dC1kZWNvcmF0aW9uOiB1bmRlcmxpbmU7CiAgICB0ZXh0LXVuZGVybGluZS1vZmZzZXQ6IDJweDsKICB9CgogIC50bC1jYXB0dXJlLWJ0bi0tZXJyb3IgewogICAgYm9yZGVyLWNvbG9yOiAjZmVjYWNhOwogICAgYmFja2dyb3VuZDogI2ZlZjJmMjsKICAgIGNvbG9yOiAjYjkxYzFjOwogIH0KCiAgLnRsLWNhcHR1cmUtYnRuLS1lcnJvcjpob3Zlcjpub3QoOmRpc2FibGVkKSB7CiAgICBib3JkZXItY29sb3I6ICNmY2E1YTU7CiAgICBiYWNrZ3JvdW5kOiAjZmVlMmUyOwogICAgY29sb3I6ICM5OTFiMWI7CiAgfQoKICAvKiAtLS0tLS0tLS0tIGlmcmFtZSDlvILluLjlvLnnqpcgLS0tLS0tLS0tLSAqLwogIC50bC1lcnJvci1tYXNrIHsKICAgIHBvc2l0aW9uOiBmaXhlZDsKICAgIGluc2V0OiAwOwogICAgei1pbmRleDogOTk5OTsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBwYWRkaW5nOiAyNHB4OwogICAgYmFja2dyb3VuZDogcmdiYSgxNSwgMjMsIDQyLCAwLjU4KTsKICAgIC13ZWJraXQtYmFja2Ryb3AtZmlsdGVyOiBibHVyKDNweCk7CiAgICBiYWNrZHJvcC1maWx0ZXI6IGJsdXIoM3B4KTsKICB9CgogIC50bC1lcnJvci1kaWFsb2cgewogICAgZGlzcGxheTogZmxleDsKICAgIGZsZXgtZGlyZWN0aW9uOiBjb2x1bW47CiAgICB3aWR0aDogbWluKDc2MHB4LCAxMDAlKTsKICAgIG1heC1oZWlnaHQ6IDg0dmg7CiAgICBvdmVyZmxvdzogaGlkZGVuOwogICAgYmFja2dyb3VuZDogI2ZmZmZmZjsKICAgIGJvcmRlcjogMXB4IHNvbGlkICNlMmU4ZjA7CiAgICBib3JkZXItcmFkaXVzOiAxNnB4OwogICAgYm94LXNoYWRvdzogMCAyOHB4IDcwcHggLTI0cHggcmdiYSgxNSwgMjMsIDQyLCAwLjU1KTsKICAgIG91dGxpbmU6IG5vbmU7CiAgfQoKICAudGwtZXJyb3ItaGVhZGVyIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogZmxleC1zdGFydDsKICAgIGp1c3RpZnktY29udGVudDogc3BhY2UtYmV0d2VlbjsKICAgIGdhcDogMTZweDsKICAgIHBhZGRpbmc6IDE4cHggMjBweDsKICAgIGJvcmRlci1ib3R0b206IDFweCBzb2xpZCAjZTJlOGYwOwogICAgYmFja2dyb3VuZDogbGluZWFyLWdyYWRpZW50KDE4MGRlZywgI2ZmZjVmNSAwJSwgI2ZmZmZmZiAxMDAlKTsKICB9CgogIC50bC1lcnJvci1oZWFkaW5nIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogZmxleC1zdGFydDsKICAgIGdhcDogMTJweDsKICAgIG1pbi13aWR0aDogMDsKICB9CgogIC50bC1lcnJvci1iYWRnZSB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IGNlbnRlcjsKICAgIGZsZXgtc2hyaW5rOiAwOwogICAgd2lkdGg6IDMwcHg7CiAgICBoZWlnaHQ6IDMwcHg7CiAgICBib3JkZXItcmFkaXVzOiA5OTk5cHg7CiAgICBiYWNrZ3JvdW5kOiAjZmVlMmUyOwogICAgY29sb3I6ICNiOTFjMWM7CiAgICBmb250LXNpemU6IDE2cHg7CiAgICBmb250LXdlaWdodDogNzAwOwogICAgbGluZS1oZWlnaHQ6IDE7CiAgfQoKICAudGwtZXJyb3ItdGl0bGUgewogICAgbWFyZ2luOiAwIDAgMnB4OwogICAgZm9udC1zaXplOiAxNnB4OwogICAgZm9udC13ZWlnaHQ6IDYwMDsKICAgIGxpbmUtaGVpZ2h0OiAxLjQ7CiAgICBjb2xvcjogIzBmMTcyYTsKICB9CgogIC50bC1lcnJvci1zdWJ0aXRsZSB7CiAgICBtYXJnaW46IDA7CiAgICBmb250LXNpemU6IDEycHg7CiAgICBsaW5lLWhlaWdodDogMS41OwogICAgY29sb3I6ICM2NDc0OGI7CiAgfQoKICAudGwtZXJyb3ItY2xvc2UgewogICAgZGlzcGxheTogaW5saW5lLWZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBjZW50ZXI7CiAgICBmbGV4LXNocmluazogMDsKICAgIHdpZHRoOiAzMHB4OwogICAgaGVpZ2h0OiAzMHB4OwogICAgcGFkZGluZzogMDsKICAgIGJvcmRlcjogMXB4IHNvbGlkIHRyYW5zcGFyZW50OwogICAgYm9yZGVyLXJhZGl1czogOHB4OwogICAgYmFja2dyb3VuZDogdHJhbnNwYXJlbnQ7CiAgICBjb2xvcjogIzY0NzQ4YjsKICAgIGZvbnQtc2l6ZTogMjJweDsKICAgIGxpbmUtaGVpZ2h0OiAxOwogICAgY3Vyc29yOiBwb2ludGVyOwogICAgdHJhbnNpdGlvbjogYmFja2dyb3VuZC1jb2xvciAwLjE1cyBlYXNlLCBjb2xvciAwLjE1cyBlYXNlOwogIH0KCiAgLnRsLWVycm9yLWNsb3NlOmhvdmVyIHsKICAgIGJhY2tncm91bmQ6ICNmMWY1Zjk7CiAgICBjb2xvcjogIzBmMTcyYTsKICB9CgogIC50bC1lcnJvci1jbG9zZTpmb2N1cy12aXNpYmxlLAogIC50bC1lcnJvci1idG46Zm9jdXMtdmlzaWJsZSB7CiAgICBvdXRsaW5lOiAycHggc29saWQgIzYzNjZmMTsKICAgIG91dGxpbmUtb2Zmc2V0OiAycHg7CiAgfQoKICAudGwtZXJyb3ItYm9keSB7CiAgICBkaXNwbGF5OiBmbGV4OwogICAgZmxleC1kaXJlY3Rpb246IGNvbHVtbjsKICAgIGdhcDogMTBweDsKICAgIHBhZGRpbmc6IDE2cHggMjBweDsKICAgIG92ZXJmbG93OiBhdXRvOwogICAgb3ZlcnNjcm9sbC1iZWhhdmlvcjogY29udGFpbjsKICAgIGJhY2tncm91bmQ6ICNmOGZhZmM7CiAgfQoKICAudGwtZXJyb3ItaXRlbSB7CiAgICBwYWRkaW5nOiAxMnB4IDE0cHg7CiAgICBib3JkZXI6IDFweCBzb2xpZCAjZmVjYWNhOwogICAgYm9yZGVyLXJhZGl1czogMTBweDsKICAgIGJhY2tncm91bmQ6ICNmZmZmZmY7CiAgfQoKICAudGwtZXJyb3ItaXRlbS1oZWFkIHsKICAgIGRpc3BsYXk6IGZsZXg7CiAgICBhbGlnbi1pdGVtczogY2VudGVyOwogICAganVzdGlmeS1jb250ZW50OiBzcGFjZS1iZXR3ZWVuOwogICAgZ2FwOiA4cHg7CiAgICBtYXJnaW4tYm90dG9tOiA2cHg7CiAgfQoKICAudGwtZXJyb3ItdHlwZSB7CiAgICBkaXNwbGF5OiBpbmxpbmUtZmxleDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBwYWRkaW5nOiAycHggOHB4OwogICAgYm9yZGVyLXJhZGl1czogOTk5OXB4OwogICAgYmFja2dyb3VuZDogI2ZlZTJlMjsKICAgIGNvbG9yOiAjYjkxYzFjOwogICAgZm9udC1zaXplOiAxMXB4OwogICAgZm9udC13ZWlnaHQ6IDYwMDsKICAgIGxpbmUtaGVpZ2h0OiAxLjY7CiAgfQoKICAudGwtZXJyb3ItdGltZSB7CiAgICBmbGV4LXNocmluazogMDsKICAgIGZvbnQtc2l6ZTogMTFweDsKICAgIGNvbG9yOiAjOTRhM2I4OwogIH0KCiAgLnRsLWVycm9yLW1lc3NhZ2UgewogICAgbWFyZ2luOiAwIDAgNnB4OwogICAgY29sb3I6ICMwZjE3MmE7CiAgICBmb250LXNpemU6IDEzcHg7CiAgICBsaW5lLWhlaWdodDogMS42OwogICAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7CiAgICB3aGl0ZS1zcGFjZTogcHJlLXdyYXA7CiAgfQoKICAudGwtZXJyb3ItbG9jYXRpb24gewogICAgbWFyZ2luOiAwIDAgNnB4OwogICAgY29sb3I6ICM2NDc0OGI7CiAgICBmb250LXNpemU6IDEycHg7CiAgICBsaW5lLWhlaWdodDogMS41OwogICAgb3ZlcmZsb3ctd3JhcDogYW55d2hlcmU7CiAgfQoKICAudGwtZXJyb3Itc3RhY2sgewogICAgbWF4LWhlaWdodDogMjIwcHg7CiAgICBtYXJnaW46IDA7CiAgICBwYWRkaW5nOiAxMHB4IDEycHg7CiAgICBvdmVyZmxvdzogYXV0bzsKICAgIGJvcmRlci1yYWRpdXM6IDhweDsKICAgIGJhY2tncm91bmQ6ICMwZjE3MmE7CiAgICBjb2xvcjogI2NiZDVlMTsKICAgIGZvbnQtZmFtaWx5OiB1aS1tb25vc3BhY2UsIFNGTW9uby1SZWd1bGFyLCBNZW5sbywgTW9uYWNvLCBDb25zb2xhcywgIkxpYmVyYXRpb24gTW9ubyIsIG1vbm9zcGFjZTsKICAgIGZvbnQtc2l6ZTogMTFweDsKICAgIGxpbmUtaGVpZ2h0OiAxLjY7CiAgICB3aGl0ZS1zcGFjZTogcHJlLXdyYXA7CiAgICBvdmVyZmxvdy13cmFwOiBhbnl3aGVyZTsKICB9CgogIC50bC1lcnJvci1mb290ZXIgewogICAgZGlzcGxheTogZmxleDsKICAgIGZsZXgtd3JhcDogd3JhcDsKICAgIGFsaWduLWl0ZW1zOiBjZW50ZXI7CiAgICBqdXN0aWZ5LWNvbnRlbnQ6IGZsZXgtZW5kOwogICAgZ2FwOiA4cHg7CiAgICBwYWRkaW5nOiAxMnB4IDIwcHg7CiAgICBib3JkZXItdG9wOiAxcHggc29saWQgI2UyZThmMDsKICAgIGJhY2tncm91bmQ6ICNmZmZmZmY7CiAgfQoKICAudGwtZXJyb3ItY29weS1oaW50IHsKICAgIG1hcmdpbi1yaWdodDogYXV0bzsKICAgIGNvbG9yOiAjMTZhMzRhOwogICAgZm9udC1zaXplOiAxMnB4OwogIH0KCiAgLnRsLWVycm9yLWNvcHktaGludC5pcy1lcnJvciB7CiAgICBjb2xvcjogI2RjMjYyNjsKICB9CgogIC50bC1lcnJvci1idG4gewogICAgaGVpZ2h0OiAzMnB4OwogICAgcGFkZGluZzogMCAxNHB4OwogICAgYm9yZGVyOiAxcHggc29saWQgI2NiZDVlMTsKICAgIGJvcmRlci1yYWRpdXM6IDhweDsKICAgIGJhY2tncm91bmQ6ICNmZmZmZmY7CiAgICBjb2xvcjogIzMzNDE1NTsKICAgIGZvbnQtc2l6ZTogMTJweDsKICAgIGZvbnQtd2VpZ2h0OiA1MDA7CiAgICBsaW5lLWhlaWdodDogMTsKICAgIGN1cnNvcjogcG9pbnRlcjsKICAgIHRyYW5zaXRpb246IGJhY2tncm91bmQtY29sb3IgMC4xNXMgZWFzZSwgYm9yZGVyLWNvbG9yIDAuMTVzIGVhc2UsIGNvbG9yIDAuMTVzIGVhc2U7CiAgfQoKICAudGwtZXJyb3ItYnRuOmhvdmVyOm5vdCg6ZGlzYWJsZWQpIHsKICAgIGJvcmRlci1jb2xvcjogI2E1YjRmYzsKICAgIGJhY2tncm91bmQ6ICNlZWYyZmY7CiAgICBjb2xvcjogIzQzMzhjYTsKICB9CgogIC50bC1lcnJvci1idG46ZGlzYWJsZWQgewogICAgb3BhY2l0eTogMC42OwogICAgY3Vyc29yOiBub3QtYWxsb3dlZDsKICB9CgogIC50bC1lcnJvci1idG4tLXByaW1hcnkgewogICAgYm9yZGVyLWNvbG9yOiAjNDMzOGNhOwogICAgYmFja2dyb3VuZDogIzQzMzhjYTsKICAgIGNvbG9yOiAjZmZmZmZmOwogIH0KCiAgLnRsLWVycm9yLWJ0bi0tcHJpbWFyeTpob3Zlcjpub3QoOmRpc2FibGVkKSB7CiAgICBib3JkZXItY29sb3I6ICMzNzMwYTM7CiAgICBiYWNrZ3JvdW5kOiAjMzczMGEzOwogICAgY29sb3I6ICNmZmZmZmY7CiAgfQoKICAudGwtZXJyb3ItZmFkZS1lbnRlci1hY3RpdmUsCiAgLnRsLWVycm9yLWZhZGUtbGVhdmUtYWN0aXZlIHsKICAgIHRyYW5zaXRpb246IG9wYWNpdHkgMC4xOHMgZWFzZTsKICB9CgogIC50bC1lcnJvci1mYWRlLWVudGVyLAogIC50bC1lcnJvci1mYWRlLWVudGVyLWZyb20sCiAgLnRsLWVycm9yLWZhZGUtbGVhdmUtdG8gewogICAgb3BhY2l0eTogMDsKICB9CgogIEBtZWRpYSAobWF4LXdpZHRoOiA2NDBweCkgewogICAgLnRsLXNjZW5lLWxvYWRpbmcgewogICAgICBwYWRkaW5nOiAxNnB4OwogICAgfQoKICAgIC50bC1zY2VuZS1sb2FkaW5nLWNhcmQgewogICAgICBtYXgtd2lkdGg6IDEwMCU7CiAgICAgIHBhZGRpbmc6IDE0cHggMTZweDsKICAgIH0KCiAgICAudGwtZXJyb3ItbWFzayB7CiAgICAgIHBhZGRpbmc6IDEycHg7CiAgICB9CgogICAgLnRsLWVycm9yLWRpYWxvZyB7CiAgICAgIG1heC1oZWlnaHQ6IDkwdmg7CiAgICB9CgogICAgLnRsLWVycm9yLWhlYWRlciwKICAgIC50bC1lcnJvci1ib2R5LAogICAgLnRsLWVycm9yLWZvb3RlciB7CiAgICAgIHBhZGRpbmctbGVmdDogMTRweDsKICAgICAgcGFkZGluZy1yaWdodDogMTRweDsKICAgIH0KICB9Cjwvc3R5bGU+"
          }
        },
      exampleList: [{
            "name": "光束折射",
            "description": "单文件光学解谜游戏：基于射线投射与反射/折射定律，拖拽旋转棱镜与分光器引导多色激光命中对应棱晶。",
            "icon": "🔦"
          },
          {
            "name": "重力迷宫",
            "description": "无依赖 2D 物理滚球解谜：旋转整座迷宫改变重力矢量，结合惯性阻尼、弹性机关与传送门将能量球送入终点。",
            "icon": "🧭"
          },
          {
            "name": "电路连线",
            "description": "程序化逻辑门拓扑谜题：旋转导线节点接通电流回路，实时演算与/或/非门信号电平，点亮全部核心节点。",
            "icon": "🔌"
          },
          {
            "name": "六边消融",
            "description": "极简六边形网格策略消除：拖拽组合多边形方块填入蜂窝棋盘，触发整行能量连消、连击倍率与平滑缓动动画。",
            "icon": "⬡"
          }
        ],
      loadingProgressApp: {
          className: "ElementVueItem",
          task: {
            template: "/webApps/document/aigcLoading.vue",
          },
        },
      feedbackDialog: {
          visible: false,
          placeholder: "输入问题描述或建议内容。",
          message: "",
        },
      interface: {
          sendFeedback: {
            url: webCpu.documentPrefix + "/user/sendFeedback",
            method: "post",
          },
        },
      styleIndex: "",
      content: {
          inputText: "",
          outputText: "",
        },
      inputPlaceholder: "输入游戏规则或需求描述...",
      description: "",
      query: {"documentId":"ac531ed5783afaa1bd0590a357539e5d"},
      accountData: {},
      flag: WebTool.adjustSize([false, false, true]),
      isSolo: WebTool.urlQuery(location.href, "solo"),
      defaultTemplate: "",
      dataEditable: true,
      title: "LeaferJS 2D 游戏",
      introduce: "生成基于 leafer.js 的交互式 2D 游戏网页。",
      icon: "🧊"
    };
},"computed":{"myAccept":function() {
        return `.html, .md, .txt, .doc, .docx, .pdf, .ppt, .pptx, .xlsx, .xls, .csv`;
      },"viewMode":function() {
        return !this.modifyEnabled && !!this.currentVersionIndex;
      },"resultPanelHeight":function() {
        let ret = "";
        if (this.isSolo || this.content.isSolo) {
          if (this.step === 1) {
            ret = "100%";
          } else {
            ret = "calc( 100% - 165px )";
          }
        } else {
          if (this.step === 1) {
            ret = "calc( 100% - 50px )";
          } else {
            ret = "calc( 100% - 170px )";
          }
        }

        return ret;
      },"documentId":function() {
        let documentId = this.query.documentId;
        if (
          !documentId &&
          webCpu.currentCard.task &&
          webCpu.currentCard.task.vueItem &&
          webCpu.currentCard.task.vueItem.documentMeta
        ) {
          documentId = webCpu.currentCard.task.vueItem.documentMeta.documentId;
        }
        return documentId;
      },"currentTips":function() {
        let ret = this.waitingText;
        if (this.loading) {
          ret = this.loadingText;
        } else if (this.description || this.content.inputText) {
          ret = this.gettedTips;
        } else {}

        return ret;
      }},"methods":{"startModify":function() {
        this.$confirm(
            "当前版本为历史版本，是否基于当前版本进行修改？",
            webCpu.messages[lang].confirmDialogTitle, {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          )
          .then(() => {
            this.modifyEnabled = true;
            // let tJson = WebTool.extractJSON(
            //   this.versionList[this.currentVersionIndex].result
            // );
            // this.updateResult(tJson);
          })
          .catch(() => {
            // 取消操作
          });
      },"checkDiff":function(index) {
        let e = window.event;
        e.stopPropagation();
        e.preventDefault();
        let style = {
          width: "100%",
          height: "100%",
        };
        let versionA = this.versionList[this.currentVersionIndex].result;
        let versionB = this.versionList[index].result;
        let baseContent = JSON.stringify(this.accountData);
        let elem = this.$refs.loadingEffectArea;
        let confirmCallback = () => {
          let content = diffCheckApp.card.task.vueItem.generateNewVersion();
          let currentId = MurmurHash.rule(baseContent, 31);
          let tId = MurmurHash.rule(JSON.stringify(content), 31);
          if (tId !== currentId) {
            this.confirmContent(content);
          } else {
            this.$message({
              type: "info",
              message: "内容没有变更。",
            });
          }
          webCpu.CardItem.dismissMask(elem);
        };
        let closeCallback = () => {
          let content = diffCheckApp.card.task.vueItem.generateNewVersion();
          let currentId = MurmurHash.rule(baseContent, 31);
          let tId = MurmurHash.rule(JSON.stringify(content), 31);
          if (tId !== currentId) {
            this.$confirm(
              "检测到有内容变更，是否生成新版本？",
              webCpu.messages[lang].confirmDialogTitle, {
                confirmButtonText: webCpu.messages[lang].confirmYes,
                cancelButtonText: webCpu.messages[lang].confirmNo,
                type: "warning",
              }
            ).then(() => {
              this.confirmContent(content);
            });
          }
          webCpu.CardItem.dismissMask(elem);
        };
        let diffCheckApp = {
          className: "ElementVueItem",
          task: {
            template: webCpu.documentPrefix + "/transweb/diffCheckApp.vue",
            data: {
              versionA: WebTool.extractJSON(versionA),
              versionB: WebTool.extractJSON(versionB),
            },
            style: {
              width: "100%",
              height: "100%",
              overflow: "auto",
            },
          },
        };

        webCpu.renderCardDialog(
          elem,
          diffCheckApp, {
            dialogName: "checkDiffDialog",
            title: "版本差异检查",
            closeType: "back",
            titleStyle: {
              background: "var(--el-color-primary-light-9)",
              color: "var(--el-color-primary)",
            },
            menu: [{
              label: "确认",
              action: confirmCallback,
            }, ],
            closeCallback: closeCallback,
          },
          style, {
            padding: "20px 30px 20px 30px",
          }
        );
      },"updateVersionList":function(versionId, callback) {
        versionId = versionId || JSON.stringify(this.accountData);
        aigcInterface.getHistoryVersion(versionId, (tData) => {
          if (tData && tData.ret === 0) {
            tData.data = tData.data || [];
            if (tData.data.length > 0) {
              this.versionList = tData.data.sort((a, b) => {
                return b._time - a._time;
              });
              for (let i = 0; i < this.versionList.length; i++) {
                let item = this.versionList[i];
                let jsonStr = JSON.stringify(WebTool.extractJSON(item.result));
                let temp = JSON.stringify(this.accountData);
                if (this.currentVersion === item.versionId || jsonStr === temp) {
                  this.currentVersionIndex = i;
                  this.currentVersion = "";
                  break;
                }
              }
              if (typeof callback === "function") {
                callback();
              }
            } else {
              this.accountData = this.accountData || {};
              aigcInterface.recordDataVersion(this.accountData, "", (tData) => {
                console.log(tData);
                let content = JSON.stringify(this.accountData);
                this.updateVersionList(content);
                this.currentVersionIndex = 0;
                if (typeof callback === "function") {
                  callback();
                }
              });
            }
          }
        });
      },"getVersionLabel":function(item) {
        if (!item || !item.versionId) {
          return false;
        }
        let str = item.versionId.slice(0, 5);
        str = `${str}@${new Date(item._time).toLocaleString()}`;
        return str;
      },"handleVersionChange":function(index) {
        this.versionList = this.versionList || [];
        console.log(index, this.versionList[index]);
        if (this.versionList[index] && this.versionList[index].result) {
          this.isStreaming = true;
          this.switchResultStatus(this.versionList[index].result, true);
          this.currentVersionIndex = index;
          this.modifyEnabled = false;
        }
      },"handleConfirm":function() {
        // 1. 获取并清理输入，防止纯空格绕过校验
        const requirement = this.modifyInfo?.trim();

        if (!requirement) {
          this.$message({
            type: "warning",
            message: "需要输入变更描述。",
          });
          return false;
        }

        // 2. 结构化 Prompt 模板，加入强制约束
        const promptContext = `
你是一个严谨的数据处理助手。请根据【修改需求】修改【原始数据】，并严格遵守【输出要求】。

【修改需求】
${requirement}

【原始数据】
${JSON.stringify(this.accountData)}

【输出要求】
1. 必须输出修改后的完整内容，保持原有的格式和层级结构完全一致。
2. 绝对不允许删减、折叠或省略【原始数据】中未要求修改的任何字段和细节。
3. 仅输出最终结果，不要包含任何开场白、解释性废话，也不要输出 Markdown 代码块标记（如 \`\`\` 等）。
`.trim();

        // 3. 执行生成
        this.modifyGenerate({
          documentId: this.query.documentId,
          context: promptContext,
        });
      },"triggerFileInput":function(e) {
        e = e || window.event;
        e.stopPropagation();
        e.preventDefault();
        this.$refs.hiddenFileInput.click();
      },"confirmTransfer":function(text) {
        this.$confirm(
          "文件中内容结构 Agent 所支持不一致，是否生成相应结构化内容？",
          webCpu.messages[lang].confirmDialogTitle, {
            confirmButtonText: webCpu.messages[lang].confirmYes,
            cancelButtonText: webCpu.messages[lang].confirmNo,
            type: "warning",
          }
        ).then(() => {
          this.content.inputText = text;
          this.handleGenerate();
        });
      },"checkDataStruct":function(json, template) {
        template = WebTool.base64ToUtf8(template);
        let obj = webCpu.getDslObj(template) || {};
        let data = typeof obj.data === "function" ? obj.data().accountData : {};
        let ret =
          Object.keys(json || {}).sort().join(",") ===
          Object.keys(data || {}).sort().join(",");
        return ret;
      },"onFileSelected":async function(event) {
        const file = event.target.files[0];
        if (!file) return;
        this.readingFile = true;
        let arr = file.name.split(".");
        let subfix = arr[arr.length - 1];

        if (subfix !== "html" && subfix !== "txt" && subfix !== "md") {
          let temp = await WebTool.extractTextFromFile(file);
          this.confirmTransfer(temp);
          this.readingFile = false;
          event.target.value = "";
          return true;
        }

        const reader = new FileReader();
        // 读取为文本
        reader.onload = (e) => {
          let text = e.target.result;

          if (subfix === "html") {
            let d = WebTool.extractAppTaskData(text);
            if (d && d.data && d.data.accountData) {
              this.isStreaming = true;
              let tData = d.data.accountData || d.data;
              this.switchResultStatus(tData);
            } else {
              this.confirmTransfer(text);
            }
          } else {
            let json = WebTool.markdownToObject(text);
            let ret = this.checkDataStruct(
              json,
              this.currentAppItem.task.template
            );
            if (json && ret) {
              this.isStreaming = true;
              this.switchResultStatus(json);
            } else {
              this.confirmTransfer(text);
            }
          }

          this.readingFile = false;
        };
        // ⚠️ 关键：按文本读取
        reader.readAsText(file, "utf-8");
        event.target.value = "";
      },"openFeedbackDialog":function() {
        this.feedbackDialog.visible = true;
      },"confirmSendFeedback":function() {
        let tempData = JSON.parse(JSON.stringify(this.accountData));
        if (!this.feedbackDialog.message.trim()) {
          this.$message({
            type: "warning",
            message: "需要输入有效问题描述。",
          });
          return false;
        }
        tempData.feedback = this.feedbackDialog.message;
        tempData.documentId = this.query.documentId;
        this.feedbackDialog.visible = false;
        this.adapter.sendFeedback(tempData, (tData) => {
          this.$message({
            type: "success",
            message: "感谢反馈！",
          });
        });
      },"handleContentCommand":function(type) {
        let params = this.accountData;
        if (type === "htmlDownload") {
          this.downloadHtmlItem({
            content: JSON.stringify(params),
            name: params.name || params.title,
            template: this.currentAppItem.task.template,
          });
        } else if (type === "markdownDownload") {
          this.downloadMarkdown(params);
        } else if (type === "readLocal") {
          this.triggerFileInput();
        } else if (type == "imageDownload") {
          this.downloadImageItem();
        } else if (type === "copyLink") {
          this.copyLink();
        } else {
          this.openContentEditor();
        }
      },"copyLink":function() {
        let documentId = this.documentId;
        let dataId = this.versionList[this.currentVersionIndex]._id;
        let url = location.origin + "/skill.html?solo=true&dataView=true&dataId=" + dataId + "&id=" + documentId;
        WebTool.copyString(url);
        this.$message({
          type: "success",
          message: "链接已复制到剪贴板。",
        });
      },"downloadImageItem":function() {
        let elem = $(this.$refs.resultComponentItem).find(
          ".ElementVueItem._task_container>div>div"
        )[0];
        if (!elem) {
          this.$message({
            type: "warning",
            message: "当前结果不支持导出图片。",
          });
          return false;
        } else {
          let name = this.accountData.name || this.accountData.title || "result";
          WebTool.transformImage(elem, name, "png", {
            download: true,
          });
        }
      },"searchContent":function(str) {
        let index = -1;
        if (typeof str === "object") {
          str = JSON.stringify(str);
        }
        for (let i = 0; i < this.versionList.length; i++) {
          let item = this.versionList[i];
          let result = JSON.stringify(WebTool.extractJSON(item.result));
          if (result === str) {
            index = i;
            break;
          }
        }
        return index;
      },"confirmContent":function(content) {
        let index = this.searchContent(content);
        let tContent = JSON.stringify(this.accountData);
        this.accountData = content;
        if (index === -1) {
          aigcInterface.recordDataVersion(content, tContent, (tData) => {
            console.log(tData);
            this.$message({
              type: "success",
              message: "已生成新版本。",
            });
            this.updateResult(content);
            this.updateVersionList(content);
            this.currentVersionIndex = 0;
          });
        } else {
          this.$message({
            type: "warning",
            message: "修改后与一个历史版本内容一致。",
          });
          this.$confirm(
            "修改后与一个历史版本内容一致，是否切换到该版本？",
            webCpu.messages[lang].confirmDialogTitle, {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          ).then(() => {
            let tJson = WebTool.extractJSON(this.versionList[index].result);
            this.updateResult(tJson);
            this.accountData = content;
            this.currentVersionIndex = index;
          });
        }
      },"openContentEditor":function() {
        let elem = this.$refs.loadingEffectArea;
        let mdString = WebTool.objectToMarkdown(this.accountData);
        let app = {
          className: "CodeEditor",
          dsl: {
            changeCallback: function(dd) {},
            data: mdString,
            options: {
              lineNumbers: true,
              value: "",
              mode: "markdown",
            },
          },
        };

        let baseContent = JSON.stringify(this.accountData);
        let confirmCallback = () => {
          let content = WebTool.markdownToObject(app.card.task.data);
          let currentId = MurmurHash.rule(baseContent, 31);
          let tId = MurmurHash.rule(content, 31);
          if (tId !== currentId) {
            this.confirmContent(content);
          } else {
            this.$message({
              type: "info",
              message: "内容没有变更。",
            });
          }
          webCpu.CardItem.dismissMask(elem);
        };
        let copyScript = () => {
          let script = app.card.task.data;
          WebTool.copyString(script);
          this.$message({
            type: "success",
            message: "复制成功",
          });
        };
        let menu = [{
            label: "复制",
            action: copyScript,
          },
          {
            label: "确认",
            action: confirmCallback,
          },
        ];
        if (this.viewMode) {
          menu.length = 1;
        }
        webCpu.CardItem.renderCardDialog(
          elem,
          app, {
            title: "编辑内容",
            titleStyle: {
              background: "var(--el-color-primary-light-9)",
              color: "var(--el-color-primary)",
            },
            closeType: "back",
            dialogName: "codeEditorDialog",
            menu: menu,
          }, {
            width: "100%",
            height: "100%",
          }
        );
      },"downloadMarkdown":function(params) {
        let md = WebTool.objectToMarkdown(params);
        let name = params.name || params.title || "未命名内容";
        if (!/\.md$/i.test(name)) {
          name += ".md";
        }
        WebTool.downloadString(md, name);
      },"downloadHtmlItem":function(params) {
        let adapter = webCpu.initAdapter({
          getTemplate: {
            url: webCpu.documentPrefix + "/transweb/components/ElementVueItem/example.html",
            method: "get",
            dataType: "text",
          },
        });
        adapter.getTemplate({}, (html) => {
          let documentId =
            this.query.documentId ||
            webCpu.currentCard.task.vueItem.documentMeta.documentId;
          let name = params.name || params.title || "未命名应用";

          // example.html 会把数据直接拼进 <script> 里：
          //   template: `{{template}}`  → JS 模板字符串
          //   accountData: {{content}}  → JS 对象字面量
          // 内容中只要出现 script 结束标签或 HTML 注释起始符，HTML 解析器就会提前结束脚本块；
          // 出现反引号 / ${ 则会截断模板字符串。这里按上下文分别转义。
          let escapeHtmlText = function(value) {
            return String(value === undefined || value === null ? "" : value)
              .replace(/&/g, "&amp;")
              .replace(/</g, "&lt;")
              .replace(/>/g, "&gt;");
          };
          let escapeHtmlAttr = function(value) {
            return escapeHtmlText(value).replace(/"/g, "&quot;");
          };

          let content = params.content;
          if (typeof content !== "string") {
            content = JSON.stringify(content === undefined ? {} : content);
          }
          // JSON 里的 < 统一转成 \u003c：脚本块不会被提前闭合，运行时解析后仍是原来的 <
          let safeContent = content
            .replace(/</g, "\\u003c")
            .replace(/\u2028/g, "\\u2028")
            .replace(/\u2029/g, "\\u2029");

          let template =
            typeof params.template === "string" ? params.template : "";
          // 先转义反斜杠，再处理反引号 / ${ / <，保证运行时拿到的模板与原始内容一致
          let safeTemplate = template
            .replace(/\\/g, "\\\\")
            .replace(/`/g, "\\`")
            .replace(/\$\{/g, "\\${")
            .replace(/</g, "\\u003c");

          let exportParams = Object.assign({}, params, {
            name: escapeHtmlText(name),
            content: safeContent,
            template: safeTemplate,
            agentName: escapeHtmlText(document.title),
            creator: escapeHtmlText(
              webCpu.currentCard.task.vueItem.currentSettings.creator ||
              "匿名用户"
            ),
            createTime: escapeHtmlText(new Date().toLocaleString()),
            userName: escapeHtmlText(localStorage.userName || "匿名用户"),
            agentUrl: escapeHtmlAttr(
              location.origin + "/skill.html?solo=true&id=" + documentId
            ),
          });

          html = html.bindData(exportParams);
          name = name + ".html";
          WebTool.downloadString(html, name, "text/html");
        });
      },"dismissLoadingStatus":function() {
        this.loading = false;
        let elem = this.$refs.loadingEffectArea;
        if (!this.isSolo && !this.content.isSolo) {
          elem = this.$refs.resultComponentItem;
        }
        webCpu.CardItem.dismissMask(elem);
      },"switchLoadingStatus":function(str) {
        this.step = 1;
        this.loading = true;
        this.$nextTick(() => {
          let elem = this.$refs.loadingEffectArea;
          if (!this.isSolo && !this.content.isSolo) {
            elem = this.$refs.resultComponentItem;
          }
          let app = JSON.parse(JSON.stringify(this.loadingProgressApp));
          webCpu.CardItem.renderCardDialog(
            elem,
            app, {
              dialogName: "progressDialog",
              headerHidden: true,
              style: {
                background: "transparent",
              },
            }, {
              background: "transparent",
              marginTop: "-5px",
            }
          );
          this.currentTipsItem = app;
        });
      },"switchResultStatus":function(fullContent, flag) {
        if (!this.isStreaming) {
          return false;
        }
        this.dismissLoadingStatus();
        this.loading = false;
        this.step = 1;
        this.abortController = null;
        this.isStreaming = false;
        this.$nextTick(() => {
          let count = 0;
          try {
            let parsed = fullContent;
            if (typeof fullContent === "string") {
              parsed = WebTool.extractJSON(fullContent);
            }
            this.accountData = parsed;
            this.content.outputText = JSON.stringify(parsed);
            this.$nextTick(() => {
              if (!flag && this.dataEditable) {
                this.updateVersionList(this.content.outputText, () => {
                  this.updateResult(parsed);
                });
              } else {
                this.updateResult(parsed);
              }
            });
          } catch (e) {
            this.goBack(true);
            if (count < 5) {
              this.handleGenerate();
              this.$message({
                type: "warning",
                message: "生成的内容格式不正确，正在重试...",
              });
              count++;
            } else {
              this.$message({
                type: "warning",
                message: "生成的内容格式不正确，请稍后再试",
              });
            }
          }
        });
      },"goBack":function(flag) {
        if (flag) {
          this._goBack();
        } else {
          this.$confirm(
            "退出后无法恢复，确定退出吗？",
            webCpu.messages[lang].confirmDialogTitle, {
              confirmButtonText: webCpu.messages[lang].confirmYes,
              cancelButtonText: webCpu.messages[lang].confirmNo,
              type: "warning",
            }
          ).then(() => {
            this._goBack();
          });
        }
      },"_goBack":function() {
        if (
          this.abortController &&
          typeof this.abortController.abort === "function"
        ) {
          this.abortController.abort();
        }
        this.step = 0;
        this.accountData = {};
        this.isStreaming = false;
        this.dismissLoadingStatus();
      },"updateResult":function(json) {
        let app = JSON.parse(JSON.stringify(this.currentAppItem));
        delete app.card;
        let elem = this.$refs.resultComponentItem;
        aigcInterface.updateResultView(json, app, elem);
      },"updateLoadingTips":function(result) {
        if (
          this.currentTipsItem &&
          this.currentTipsItem.card &&
          this.currentTipsItem.card.task
        ) {
          this.currentTipsItem.card.task.vueItem =
            this.currentTipsItem.card.task.vueItem || {};
          this.currentTipsItem.card.task.vueItem.result = result;
        }
      },"inputExampleText":function(item) {
        this.content.inputText = item.name || "";
        if (item.description) {
          this.content.inputText = item.name + ": " + item.description;
        }
      },"confirmGenerate":async function(query) {
        this.responseText = "";
        this.switchLoadingStatus();
        this.isStreaming = true;
        this.versionList = [];
        this.abortController = await aigcInterface.streamRequest(query, {
          immediateFinish: true,
          onMessage: (message) => {
            this.responseText = message;
            this.updateLoadingTips(message);
          },
          onStreamFinished: (result) => {
            this.switchResultStatus(result);
            this.content.outputText = result;
            this.versionList = [];
          },
          onError: (e) => {
            this.handleErrorCase();
          },
          onAction: (action) => {
            if (action === "stop") {
              this._goBack();
            } else if (action === "LOGIN") {
              aigcInterface.openLoginDialog(document.body, () => {
                this.confirmGenerate(query);
              });
              this.goBack(true);
            } else if (action === "RECHARGE") {
              aigcInterface.renderRechargeDialog(document.body);
              this.goBack(true);
            } else {}
          },
        });
      },"handleErrorCase":function() {
        this.$message({
          type: "warning",
          message: "请求出现异常，请重新尝试。",
        });
        this.goBack(true);
      },"handleGenerate":function() {
        if (!this.currentFile && !this.content.inputText) {
          this.$message({
            type: "warning",
            message: "需要输入具体需求描述。",
          });
          return false;
        }

        let query = this.query || {};
        query.context = this.content.inputText;
        query.documentId =
          query.documentId ||
          webCpu.currentCard.task.vueItem.documentMeta.documentId;
        if (this.currentFile) {
          WebTool.compressAndResizeBase64(
            this.currentFile,
            this.size,
            this.size,
            1
          ).then((base64) => {
            query.image = base64 || "";
            this.aigcRequest(query);
          });
        } else {
          if (location.host.includes("transweb.cn")) {
            this.confirmGenerate(query);
          } else {
            this.aigcRequest(query);
          }
        }
      },"aigcRequest":function(query, flag) {
        this.switchLoadingStatus();
        this.isStreaming = true;
        this.versionList = [];
        aigcInterface.requestPrompt(
          query.context,
          query.documentId,
          query,
          (content) => {
            this.responseText = content || "";
            this.content.outputText = this.responseText;
            this.switchResultStatus(this.responseText);
            this.versionList = [];
          },
          (e) => {
            this._goBack();
          }
        );
      },"modifyGenerate":function(query) {
        this.modifyLoading = true;
        query.documentId =
          query.documentId ||
          webCpu.currentCard.task.vueItem.documentMeta.documentId;
        query.baseContent = JSON.stringify(this.accountData);
        aigcInterface.requestPrompt(
          query.context,
          query.documentId,
          query,
          (content) => {
            this.modifyLoading = false;
            this.responseText = content || "";
            this.content.outputText = this.responseText;
            this.isStreaming = true;
            this.switchResultStatus(this.responseText);
          },
          (e) => {
            this.$message({
              type: "warning",
              message: "请求出现异常，请重新尝试。",
            });
            this.modifyLoading = false;
            this._goBack();
          }
        );
      }},"appMap":{"loginRegItem":{"url":"/mainApp/app/loginRegItem.js","key":"transweb_loginRegItem","cardName":"loginRegCard","dsl":{"data":{"position":"top","loginedMenu":[{"label":"⏱️ 历史记录","action":function(d, callback) {
                dialogMap.openDocumentApp(
                  webCpu.appConfig.aigcHistory, {
                    data: {
                      content: {
                        isSolo: true,
                      },
                      query: {
                        documentId: d.documentId || webCpu.currentVueItem.query.documentId,
                      },
                    },
                  }, {
                    title: "⏱️ 历史记录",
                    callback: callback,
                  }, {
                    width: "100%",
                    height: "100%",
                  }
                );
              }}]}}}},"mounted":async function() {
      this.content = this.content || {};
      this.adapter = webCpu.initAdapter(this.interface);
      let auto = WebTool.urlQuery(location.href, "auto");
      let caseUrl = WebTool.urlQuery(location.href, "caseUrl");
      let dataId = WebTool.urlQuery(location.href, "dataId");

      if (this.accountData && Object.keys(this.accountData).length !== 0) {
        this.isStreaming = true;
        this.switchResultStatus(this.accountData);
      } else if (caseUrl) {
        this.isStreaming = true;
        let d = await WebTool.fetchHtmlDataSmart(caseUrl);
        this.switchResultStatus(d.data);
      } else if (dataId) {
        aigcInterface.getDataItem(dataId, (result, tData) => {
          this.isStreaming = true;
          this.currentVersion = tData.versionId;
          this.dataEditable =
            tData.folderName + "" === localStorage.folderName &&
            !WebTool.urlQuery(location.href, "dataView");
          this.switchResultStatus(result);
        }, (err) => {
          this.$message({
            type: "warning",
            message: "获取数据失败，暂时没有权限访问。",
          });
        });
      } else {
        aigcInterface.handleAgentInput(location.href, (str) => {
          this.content.inputText = str;
          if (auto) {
            this.handleGenerate();
          }
        });
      }
    }};</script>