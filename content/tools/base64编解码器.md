+++
date = '2026-05-16T23:00:06+08:00'
draft = false
title = 'Base64编解码器'
summary = 'Base64编解码工具。编码支持输入文本，上传文件；解码支持查看解码文本，查看解码二进制，预览解码图片'
tags = [ 'Base64' ]
categories = [ '工具' ]
+++

{{< rawhtml >}}
    <style>
        /* 卡片通用样式 */
        .card {
            <!-- background: rgba(255, 255, 255, 0.9); -->
            backdrop-filter: blur(2px);
            border-radius: 32px;
            <!-- box-shadow: 0 20px 35px -12px rgba(0, 0, 0, 0.1), 0 1px 2px rgba(0,0,0,0.02); -->
            border: 1px solid rgba(255,255,255,0.7);
            transition: all 0.2s ease;
            width: 100%;
        }

        .action-card {
            padding: 28px 32px;
            margin-bottom: 28px;
        }

        .result-card {
            padding: 28px 32px;
            min-height: 500px;
            display: flex;
            flex-direction: column;
        }


        * {
            box-sizing: border-box;
        }

        /* 表单网格布局 */
        .form-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 24px 32px;
            margin-bottom: 28px;
        }

        /* 修复 Safari / Grid 宽度不对称 */
        .field {
            display: flex;
            flex-direction: column;
            min-width: 0;
        }

        .field.full-width {
            grid-column: span 2;
        }

        .field label {
            display: block;
            margin-bottom: 8px;
            font-size: 13px;
            font-weight: 600;
            color: #2c4a6e;
            letter-spacing: 0.3px;
        }

        /* 自定义下拉框 */
        .custom-select {
            position: relative;
            width: 100%;
            user-select: none;
        }

        /* 触发器 */
        .select-trigger {
            width: 100%;
            padding: 12px 16px;
            background: #ffffff;
            border: 1px solid #ced8e2;
            border-radius: 20px;

            font-size: 14px;
            font-family: inherit;
            color: #1a2a3a;

            cursor: pointer;

            display: flex;
            align-items: center;
            justify-content: space-between;

            transition: 0.2s;

            box-sizing: border-box;
        }

        .select-trigger:hover {
            border-color: #2c9b5e;
            background: #fefefe;
        }

        .select-trigger.active {
            border-color: #2c9b5e;
            box-shadow: 0 0 0 3px rgba(44, 155, 94, 0.2);
        }

        .select-trigger .selected-text {
            flex: 1;
            min-width: 0;
        }

        .select-arrow {
            font-size: 12px;
            color: #7e9ab5;
            transition: transform 0.2s;
            margin-left: 10px;
            flex-shrink: 0;
        }

        .select-trigger.active .select-arrow {
            transform: rotate(180deg);
        }

        /* 下拉菜单 */
        .select-dropdown {
            position: absolute;
            top: calc(100% + 8px);
            left: 0;

            width: 100%;

            background: #ffffff;
            border: 1px solid #e2e8f0;
            border-radius: 20px;

            box-shadow: 0 12px 28px rgba(0, 0, 0, 0.12);

            z-index: 100;

            max-height: 240px;
            overflow-y: auto;

            opacity: 0;
            visibility: hidden;
            transform: translateY(-8px);

            transition: all 0.2s ease;

            box-sizing: border-box;
        }

        .select-dropdown.open {
            opacity: 1;
            visibility: visible;
            transform: translateY(0);
        }

        /* 选项 */
        .select-option {
            padding: 12px 16px;
            font-size: 14px;
            cursor: pointer;
            transition: 0.15s;
            color: #2a3e54;
        }

        .select-option:hover {
            background: #eef3fc;
        }

        .select-option.selected {
            background: #e3f0ea;
            color: #1a6e3c;
            font-weight: 600;
        }

        .select-option:first-child {
            border-radius: 20px 20px 0 0;
        }

        .select-option:last-child {
            border-radius: 0 0 20px 20px;
        }

        /* 隐藏原生 select */
        .hidden-native {
            display: none;
        }

        .textarea {
            width: 100%;
            border-radius: 20px;
            border: 1px solid #ced8e2;
            background: #ffffff;
            padding: 12px 16px;
            font-size: 14px;
            outline: none;
            transition: 0.2s;
            font-family: inherit;
            resize: vertical;
            line-height: 1.5;
        }

        .textarea:focus {
            border-color: #2c9b5e;
            box-shadow: 0 0 0 3px rgba(44, 155, 94, 0.2);
        }

        .textarea {
            min-height: 120px;
        }

        /* 上传区域 */
        .upload-box {
            background: #f8fafd;
            border-radius: 24px;
            padding: 20px 20px;
            text-align: center;
            border: 1.5px dashed #bdd3e8;
            transition: all 0.2s;
            cursor: pointer;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        .upload-box:hover {
            background: #f1f7fe;
            border-color: #5f9b7a;
        }

        .upload-box input {
            margin-top: 14px;
            font-size: 13px;
        }

        .run-btn {
            background: linear-gradient(105deg, #1f6e43, #14805e);
            color: white;
            border: none;
            border-radius: 40px;
            padding: 14px 28px;
            font-size: 16px;
            font-weight: 700;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(0,80,50,0.2);
            transition: 0.2s;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            width: auto;
            min-width: 180px;
        }

        .run-btn:hover {
            transform: translateY(-2px);
            background: linear-gradient(105deg, #188350, #0e6e4f);
            box-shadow: 0 8px 20px rgba(0,80,50,0.25);
        }

        .btn-wrapper {
            display: flex;
            justify-content: flex-end;
            margin-top: 8px;
        }

        /* 结果区域 */
        .result-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .result-header h2 {
            font-size: 22px;
            font-weight: 700;
            color: #1f3b4c;
        }

        .status {
            padding: 6px 16px;
            border-radius: 100px;
            font-size: 12px;
            font-weight: 700;
            background: #e5f4e9;
            color: #1c6e3c;
        }

        .tag-group {
            margin-bottom: 18px;
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
        }

        .tag {
            display: inline-flex;
            align-items: center;
            background: #eef2fa;
            padding: 6px 14px;
            border-radius: 32px;
            font-size: 12px;
            font-weight: 500;
            color: #2b5b7a;
            gap: 6px;
        }

        .result-box {
            overflow: auto; 
            flex: 1;
            background: #fefefe;
            border-radius: 28px;
            padding: 2px;
            overflow: auto;
            border: 1px solid #eef2f8;
            box-shadow: inset 0 1px 3px #0001, 0 2px 4px #fff6;
            font-family: 'JetBrains Mono', 'SF Mono', monospace;
            min-height: 320px;
            max-height: 500px;
        }

        .result-box pre {
            white-space: pre-wrap;
            font-size: 16px;
            line-height: 1.6;
            color: #1e2e3e;
            margin: 0;
            padding: 5px;
        }

        .toolbar {
            display: flex;
            gap: 14px;
            flex-wrap: wrap;
            margin-top: 20px;
        }

        .action-btn {
            background: #ffffffea;
            backdrop-filter: blur(4px);
            border: 1px solid #cddfe8;
            border-radius: 40px;
            padding: 10px 20px;
            font-weight: 600;
            font-size: 13px;
            cursor: pointer;
            text-decoration: none;
            color: #226653;
            transition: 0.18s;
            display: inline-flex;
            align-items: center;
            gap: 6px;
        }

        .action-btn:hover {
            background: #e7f3ef;
            border-color: #54b28b;
            transform: translateY(-1px);
        }

        .preview-image {
            margin-top: 8px;
            border-radius: 24px;
            overflow: hidden;
            background: #f2f5f9;
            text-align: center;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }

        .preview-image img {
            max-width: 100%;
            max-height: 360px;
            object-fit: contain;
            border-radius: 20px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.1);
        }

        .preview-info {
            margin-top: 12px;
            font-size: 13px;
            color: #4a6f8a;
            background: #eef3fc;
            padding: 6px 14px;
            border-radius: 40px;
            display: inline-block;
        }

        .hidden {
            display: none !important;
        }

        .empty {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
            padding: 40px 20px;
            color: #7b98ae;
        }

        .empty-icon {
            font-size: 64px;
            opacity: 0.6;
            margin-bottom: 20px;
        }

        .info {
            font-size: 12px;
            margin-top: 12px;
            color: #5f7f9a;
        }
        .dark-mode label {
            color: #0e83cc90
        }
        .dark-mode .tag {
            color: #0197f5af;
            background: #414040;
        }
        .dark-mode .select-trigger,
        .dark-mode .select-dropdown,
        .dark-mode textarea,
        .dark-mode .upload-box,
        .dark-mode .preview-image {
            background: #333;
        }
        .dark-mode .result-box,
        .dark-mode .result-box pre {
            background: #333;
            color: #aaa
        }
        .dark-mode .select-option {
            color: #aaa
        }
        
        .dark-mode .select-option.selected,
        .dark-mode .select-option:hover {
            background: #6a6a6a;
        }
        .dark-mode .action-btn {
            background: #333;
            color: #aaa
        }

        
        <!-- @media (max-width: 760px) {
            body { padding: 20px 16px; }
            .form-grid { grid-template-columns: 1fr; gap: 18px; }
            .field.full-width { grid-column: span 1; }
            .mode-section { flex-direction: column; align-items: stretch; }
            .segment { justify-content: center; }
            .btn-wrapper { justify-content: center; }
            .action-card, .result-card { padding: 20px; }
        } -->
    </style>

<div class="glass-container">
    

    <!-- 操作卡片 -->
    <div class="card action-card">
         <div class="field">
                <p style="color: red; font-size: 12px;">⚠️ 本工具纯前端运行，不会保存您的任何信息</p>
                <label>🕹️ 模式选择 </label>
                <div class="custom-select" id="modeSelect">
                    <div class="select-trigger">
                        <span class="selected-text">🔐 编码 (Encode)</span>
                        <span class="select-arrow">▼</span>
                    </div>
                    <div class="select-dropdown">
                        <div class="select-option" data-value="encode">🔐 编码 (Encode)</div>
                        <div class="select-option" data-value="decode">🔓 解码 (Decode)</div>
                    </div>
                </div>
                <select id="optionMode" class="hidden-native">
                    <option value="encode">编码</option>
                    <option value="decode">解码</option>
                </select>
            </div>
        <div class="form-grid">
            <!-- 输入来源 - 自定义下拉框 -->
            <div class="field">
                <label>📂 输入来源</label>
                <div class="custom-select" id="inputTypeSelect">
                    <div class="select-trigger">
                        <span class="selected-text">✍️ 文本输入</span>
                        <span class="select-arrow">▼</span>
                    </div>
                    <div class="select-dropdown">
                        <div class="select-option" data-value="text">✍️ 文本输入</div>
                        <div class="select-option" data-value="file">📎 上传文件</div>
                    </div>
                </div>
                <select id="inputType" class="hidden-native">
                    <option value="text">文本输入</option>
                    <option value="file">上传文件</option>
                </select>
            </div>

            <!-- 解码输出类型 - 自定义下拉框 -->
            <div class="field hidden" id="decodeTargetWrap">
                <label>🎯 解码输出类型</label>
                <div class="custom-select" id="decodeTargetSelect">
                    <div class="select-trigger">
                        <span class="selected-text">📄 纯文本</span>
                        <span class="select-arrow">▼</span>
                    </div>
                    <div class="select-dropdown">
                        <div class="select-option" data-value="text">📄 纯文本</div>
                        <div class="select-option" data-value="file">🗃️ 二进制</div>
                        <div class="select-option" data-value="image">🖼️ 图片预览</div>
                    </div>
                </div>
                <select id="decodeTarget" class="hidden-native">
                    <option value="text">纯文本</option>
                    <option value="file">二进制</option>
                    <option value="image">图片预览</option>
                </select>
            </div>

            <!-- 图片格式 - 自定义下拉框 -->
            <div class="field hidden" id="imageTypeWrap">
                <label>🖌️ 图片格式 (解码图片时)</label>
                <div class="custom-select" id="imageTypeSelect">
                    <div class="select-trigger">
                        <span class="selected-text">🤖 自动识别</span>
                        <span class="select-arrow">▼</span>
                    </div>
                    <div class="select-dropdown">
                        <div class="select-option" data-value="auto">🤖 自动识别</div>
                        <div class="select-option" data-value="png">PNG</div>
                        <div class="select-option" data-value="jpg">JPEG</div>
                        <div class="select-option" data-value="jpeg">JPEG</div>
                        <div class="select-option" data-value="webp">WebP</div>
                        <div class="select-option" data-value="gif">GIF</div>
                    </div>
                </div>
                <select id="imageType" class="hidden-native">
                    <option value="auto">自动识别</option>
                    <option value="png">PNG</option>
                    <option value="jpg">JPEG</option>
                    <option value="jpeg">JPEG</option>
                    <option value="webp">WebP</option>
                    <option value="gif">GIF</option>
                </select>
            </div>

            <div class="field full-width" id="textInputWrap">
                <label>📝 输入内容</label>
                <textarea id="textInput" class="textarea" placeholder="输入文本"></textarea>
            </div>

            <div class="field full-width hidden" id="fileInputWrap">
                <label>📁 上传文件</label>
                <div class="upload-box" id="uploadClickArea">
                    <div style="font-size: 38px; margin-bottom: 6px;">📂</div>
                    <div style="font-weight: 600;">拖放或点击选取文件</div>
                    <div class="info">最大支持 500MB</div>
                    <input type="file" id="fileInput" style="margin-top: 12px; font-size: 13px;">
                </div>
            </div>
        </div>
        <div class="btn-wrapper">
            <button class="run-btn" id="runBtn">
                <span>⚡</span> 执行转换
            </button>
        </div>
    </div>

    <!-- 结果卡片 -->
    <div class="card result-card">
        <div class="result-header">
            <h2>📋 输出面板</h2>
            <div class="status" id="statusBadge">✓ 就绪</div>
        </div>
        <div class="tag-group">
            <span class="tag">🔹 Base64</span>
            <span class="tag">📎 文件提取</span>
            <span class="tag">🖼️ 实时预览</span>
            <span class="tag">🔢 HexDump</span>
        </div>
        <div class="result-box" id="resultBox">
            <div class="empty" id="emptyState">
                <div class="empty-icon">⚙️</div>
                <div style="font-size: 20px; font-weight: 600;">等待指令</div>
                <div style="margin-top: 10px;">选择编码/解码模式，配置参数后点击「执行转换」</div>
            </div>
            <pre id="resultText" class="hidden"></pre>
            <div id="imagePreview"></div>
        </div>
        <div class="toolbar" id="toolbar"></div>
    </div>
</div>

<script>
    // 自定义下拉框管理器
    class CustomSelect {
        constructor(container, nativeSelectId, onChange) {
            this.container = container;
            this.trigger = container.querySelector('.select-trigger');
            this.dropdown = container.querySelector('.select-dropdown');
            this.selectedTextSpan = this.trigger.querySelector('.selected-text');
            this.options = container.querySelectorAll('.select-option');
            this.nativeSelect = document.getElementById(nativeSelectId);
            this.onChange = onChange;
            this.isOpen = false;
            
            this.init();
        }
        
        init() {
            // 设置初始值
            const currentValue = this.nativeSelect.value;
            const currentOption = Array.from(this.options).find(opt => opt.dataset.value === currentValue);
            if (currentOption) {
                this.selectedTextSpan.textContent = currentOption.textContent;
            }
            
            // 点击触发器
            this.trigger.addEventListener('click', (e) => {
                e.stopPropagation();
                this.toggle();
            });
            
            // 选项点击
            this.options.forEach(opt => {
                opt.addEventListener('click', (e) => {
                    e.stopPropagation();
                    const value = opt.dataset.value;
                    const text = opt.textContent;
                    this.setValue(value, text);
                    this.close();
                    if (this.onChange) this.onChange(value);
                });
            });
            
            // 点击外部关闭
            document.addEventListener('click', () => this.close());
        }
        
        toggle() {
            if (this.isOpen) {
                this.close();
            } else {
                this.open();
            }
        }
        
        open() {
            this.dropdown.classList.add('open');
            this.trigger.classList.add('active');
            this.isOpen = true;
        }
        
        close() {
            this.dropdown.classList.remove('open');
            this.trigger.classList.remove('active');
            this.isOpen = false;
        }
        
        setValue(value, text) {
            this.selectedTextSpan.textContent = text;
            this.nativeSelect.value = value;
            // 更新高亮
            this.options.forEach(opt => {
                if (opt.dataset.value === value) {
                    opt.classList.add('selected');
                } else {
                    opt.classList.remove('selected');
                }
            });
        }
        
        getValue() {
            return this.nativeSelect.value;
        }
    }
    
    // DOM 元素

    const inputTypeNative = document.getElementById("inputType");
    const decodeTargetNative = document.getElementById("decodeTarget");
    const imageTypeNative = document.getElementById("imageType");
    const decodeTargetWrap = document.getElementById("decodeTargetWrap");
    const imageTypeWrap = document.getElementById("imageTypeWrap");
    const textInputWrap = document.getElementById("textInputWrap");
    const fileInputWrap = document.getElementById("fileInputWrap");
    const textInput = document.getElementById("textInput");
    const fileInput = document.getElementById("fileInput");
    const resultText = document.getElementById("resultText");
    const imagePreviewDiv = document.getElementById("imagePreview");
    const toolbarDiv = document.getElementById("toolbar");
    const emptyState = document.getElementById("emptyState");
    const statusBadge = document.getElementById("statusBadge");
    const runBtn = document.getElementById("runBtn");
    const uploadBox = document.getElementById("uploadClickArea");
    
    // 初始化自定义下拉框

    const modeSelect = new CustomSelect(
        document.getElementById("modeSelect"),
        "optionMode",
        (val) => { setMode(); }
    );

    const inputTypeSelect = new CustomSelect(
        document.getElementById("inputTypeSelect"),
        "inputType",
        (val) => { updateUI(); }
    );
    
    const decodeTargetSelect = new CustomSelect(
        document.getElementById("decodeTargetSelect"),
        "decodeTarget",
        (val) => { updateUI(); }
    );
    
    const imageTypeSelect = new CustomSelect(
        document.getElementById("imageTypeSelect"),
        "imageType",
        null
    );
    
    let currentMode = "encode";
    let currentBlobUrl = null;
    
    function revokeCurrentImageUrl() {
        if (currentBlobUrl) {
            URL.revokeObjectURL(currentBlobUrl);
            currentBlobUrl = null;
        }
    }
    
    function updateUI() {
        const isTextSource = (inputTypeSelect.getValue() === "text");
        textInputWrap.classList.toggle("hidden", !isTextSource);
        fileInputWrap.classList.toggle("hidden", isTextSource);
        
        const isDecodeMode = (currentMode === "decode");
        decodeTargetWrap.classList.toggle("hidden", !isDecodeMode);
        
        const showImageType = (isDecodeMode && decodeTargetSelect.getValue() === "image");
        imageTypeWrap.classList.toggle("hidden", !showImageType);
    }
    
    function setMode() {
        mode = currentMode = modeSelect.getValue();
        clearFullResult();
        updateUI();
        statusBadge.innerText = mode === "encode" ? "🔐 编码模式" : "🔓 解码模式";
        statusBadge.style.background = mode === "encode" ? "#e3f0ec" : "#f0e9e3";
    }
    
    function clearFullResult() {
        emptyState.classList.remove("hidden");
        resultText.classList.add("hidden");
        resultText.textContent = "";
        imagePreviewDiv.innerHTML = "";
        revokeCurrentImageUrl();
        toolbarDiv.innerHTML = "";
    }
    
    function showResultAsText(text, useWrap) {
        
        emptyState.classList.add("hidden");
        resultText.classList.remove("hidden");
        imagePreviewDiv.innerHTML = "";
        revokeCurrentImageUrl();
         if (useWrap) {
            // 自动换行模式（无横向滚动条）
            resultText.style.whiteSpace = 'pre-wrap';
            resultText.style.wordBreak = 'break-all';
            resultText.style.overflowX = 'hidden';
        } else {
            // 保留格式模式（有横向滚动条）
            resultText.style.whiteSpace = 'pre';
            resultText.style.wordBreak = 'normal';
            resultText.style.overflowX = 'auto';
        }
        resultText.textContent = text;
    }
    
    function createDownloadButton(blob, fileName, buttonText) {
        const url = URL.createObjectURL(blob);
        const a = document.createElement("a");
        a.href = url;
        a.download = fileName;
        a.className = "action-btn";
        a.innerHTML = `💾 ${buttonText}`;
        a.onclick = () => { setTimeout(() => URL.revokeObjectURL(url), 200); };
        toolbarDiv.appendChild(a);
        return url;
    }

    function createCopyButton(copyText, buttonText, copyResultInfo) {
        const copyBtn = document.createElement("button");
        copyBtn.className = "action-btn";
        copyBtn.innerHTML = buttonText;
        copyBtn.onclick = () => {
            navigator.clipboard.writeText(copyText);
            copyBtn.innerText = copyResultInfo;
            setTimeout(() => copyBtn.innerHTML = buttonText, 400);
        };
        toolbarDiv.appendChild(copyBtn);
    }
    
    function truncateLongText(str, limit = 15000) {
        if (str.length <= limit) return { text: str, truncated: false };
        return { text: str.slice(0, limit) + "\n\n…… 内容过长已截断 (前" + limit + "字符) ……", truncated: true };
    }
    
    function arrayBufferToBase64(buffer) {
        let binary = '';
        const bytes = new Uint8Array(buffer);
        const chunkSize = 0x8000;
        for (let i = 0; i < bytes.length; i += chunkSize) {
            const chunk = bytes.subarray(i, i + chunkSize);
            binary += String.fromCharCode.apply(null, chunk);
        }
        return btoa(binary);
    }
    
    function base64ToBytes(base64) {
        const binary = atob(base64);
        const len = binary.length;
        const bytes = new Uint8Array(len);
        for (let i = 0; i < len; i++) {
            bytes[i] = binary.charCodeAt(i);
        }
        return bytes;
    }
    
    function hexdump(bytes, limit = 4096) {
        let out = "";
        const len = Math.min(limit, bytes.length);
        for (let i = 0; i < len; i += 16) {
            let hexPart = "";
            let asciiPart = "";
            for (let j = 0; j < 16; j++) {
                if (i + j < len) {
                    const b = bytes[i + j];
                    hexPart += b.toString(16).padStart(2, '0') + " ";
                    asciiPart += (b >= 32 && b <= 126) ? String.fromCharCode(b) : ".";
                } else {
                    hexPart += "   ";
                }
            }
            out += i.toString(16).padStart(8, '0') + "  " + hexPart.padEnd(48, ' ') + " |" + asciiPart + "|\n";
        }
        if (bytes.length > limit) out += `\n... 内容超过 ${limit} 字节，仅显示开头部分 ...`;
        return out;
    }
    
    function detectImageMime(bytes) {
        if (bytes.length < 4) return { mime: "image/png", ext: "png" };
        if (bytes[0] === 0x89 && bytes[1] === 0x50 && bytes[2] === 0x4E && bytes[3] === 0x47) return { mime: "image/png", ext: "png" };
        if (bytes[0] === 0xFF && bytes[1] === 0xD8) return { mime: "image/jpeg", ext: "jpg" };
        if (bytes[0] === 0x47 && bytes[1] === 0x49 && bytes[2] === 0x46) return { mime: "image/gif", ext: "gif" };
        if (bytes[0] === 0x52 && bytes[1] === 0x49 && bytes[2] === 0x46 && bytes[3] === 0x46) return { mime: "image/webp", ext: "webp" };
        return { mime: "image/png", ext: "png" };
    }
    
    async function encodeFlow() {
        if (inputTypeSelect.getValue() === "text") {
            let rawText = textInput.value;
            if (!rawText) throw new Error("请输入需要编码的文本");
            const utf8Bytes = new TextEncoder().encode(rawText);
            const base64Result = btoa(String.fromCharCode(...utf8Bytes));
            const { text, truncated } = truncateLongText(base64Result, 1024 * 32);
            showResultAsText(text, 1);
            if (base64Result.length < 1024 * 32) {
                createCopyButton(base64Result, "复制 Base64", "复制成功");
            } else {
                createDownloadButton(new Blob([base64Result]), "encoded_base64.txt", "下载完整 Base64");
            }
        } else {
            const file = fileInput.files[0];
            if (!file) throw new Error("请选择要编码的文件");
            
            if (file.size > 500 * 1024 * 1024) throw new Error("输入文件过大");
            const buffer = await file.arrayBuffer();
            const base64Str = arrayBufferToBase64(buffer);
            const { text, truncated } = truncateLongText(base64Str, 1024 * 32);
            showResultAsText(text, 1);
            if(base64Str.length < 1024 * 32) {
                createCopyButton(base64Str, "复制 Base64", "复制成功");
            } else {
                createDownloadButton(new Blob([base64Str]), `${file.name}.base64.txt`, "下载 Base64 文本");
            }
        }
    }
    
    async function decodeFlow() {
        let rawBase64 = "";
        if (inputTypeSelect.getValue() === "text") {
            rawBase64 = textInput.value.trim();
            if (!rawBase64) throw new Error("请输入 Base64 字符串");
        } else {
            const file = fileInput.files[0];
            if (!file) throw new Error("请上传包含 Base64 的文件");
            if (file.size > 500 * 1024 * 1024) throw new Error("输入文件过大");
            rawBase64 = await file.text();
        }
        
        let cleaned = rawBase64.replace(/^data:.*?;base64,/, "").replace(/\s/g, '');
        if (!cleaned) throw new Error("无效的 Base64 数据");
        
        let bytes;
        try {
            bytes = base64ToBytes(cleaned);
        } catch (e) {
            throw new Error("Base64 解析失败，内容可能不合法");
        }
        
        const target = decodeTargetSelect.getValue();
        
        if (target === "text") {
            const decoder = new TextDecoder("utf-8");
            let decodedText = "";
            try {
                decodedText = decoder.decode(bytes);
            } catch {
                decodedText = new TextDecoder("utf-8").decode(bytes);
            }
            const { text, truncated } = truncateLongText(decodedText, 1024 * 32);
            showResultAsText(text, 0);
            if (decodedText.length < 1024 * 32) {
                createCopyButton(decodedText, "复制文本", "复制成功");
            } else {
                createDownloadButton(new Blob([decodedText]), "decoded_text.txt", "下载完整文本");
            }
        }
        else if (target === "file") {
            const dump = hexdump(bytes, 4096);
            showResultAsText(dump, 0);
            createDownloadButton(new Blob([bytes]), "decoded_binary.bin", "下载解码文件");
            createCopyButton(dump, "📋 复制Hex摘要","📋 已复制摘要");
        } 
        else {
            let imgType = imageTypeSelect.getValue();
            let finalMime = "";
            let finalExt = "";
            
            if (imgType === "auto") {
                const detected = detectImageMime(bytes);
                finalMime = detected.mime;
                finalExt = detected.ext;
            } else {
                finalExt = imgType === "jpg" ? "jpg" : imgType;
                const mimeMap = { png: "image/png", jpg: "image/jpeg", jpeg: "image/jpeg", webp: "image/webp", gif: "image/gif" };
                finalMime = mimeMap[finalExt] || "image/png";
            }
            
            const blob = new Blob([bytes], { type: finalMime });
            const imgUrl = URL.createObjectURL(blob);
            currentBlobUrl = imgUrl;
            
            emptyState.classList.add("hidden");
            resultText.classList.add("hidden");
            
            const imgContainer = document.createElement("div");
            imgContainer.className = "preview-image";
            const img = document.createElement("img");
            img.src = imgUrl;
            img.style.maxWidth = "100%";
            img.style.maxHeight = "360px";
            img.style.borderRadius = "20px";
            img.style.boxShadow = "0 8px 20px rgba(0,0,0,0.1)";
            
            const infoSpan = document.createElement("div");
            infoSpan.className = "preview-info";
            infoSpan.innerHTML = `✅ 图片解码成功 · 格式: ${finalExt.toUpperCase()} · 大小: ${(bytes.length / 1024).toFixed(1)} KB`;
            
            imgContainer.appendChild(img);
            imgContainer.appendChild(infoSpan);
            imagePreviewDiv.innerHTML = "";
            imagePreviewDiv.appendChild(imgContainer);
            
            createDownloadButton(blob, `decoded_image.${finalExt}`, "保存图片");
        }
    }
    
    function updateDefaultStatus() {
        statusBadge.innerText = currentMode === "encode" ? "🔐 编码模式" : "🔓 解码模式";
    }
    
    async function runAction() {
        try {
            clearFullResult();
            currentMode = modeSelect.getValue();
            toolbarDiv.innerHTML = "";
            statusBadge.innerText = "🔄 处理中...";
            if (currentMode === "encode") {
                await encodeFlow();
            } else {
                await decodeFlow();
            }
            statusBadge.innerText = currentMode === "encode" ? "✅ 编码完成" : "✅ 解码完成";
            setTimeout(() => updateDefaultStatus(), 2000);
        } catch (err) {
            console.error(err);
            statusBadge.innerText = "❌ 错误";
            showResultAsText(`⚠️ 错误: ${err.message || "未知错误"}`, 1);
            setTimeout(() => updateDefaultStatus(), 2000);
        }
    }
    
    // 事件绑定
    runBtn.onclick = runAction;
    
    if (uploadBox && fileInput) {
        uploadBox.addEventListener("click", (e) => {
            if (e.target !== fileInput) fileInput.click();
        });
        fileInput.addEventListener("click", (e) => e.stopPropagation());
    }
    
    // 初始化
    updateUI();
    textInput.value = "";
</script>
{{< /rawhtml >}}