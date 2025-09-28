<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ProbSolver - AI Tutor, Chat & Image Generator</title>
    <script src="https://cdn.jsdelivr.net/npm/katex@0.16.22/dist/katex.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.22/dist/katex.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/components/prism-core.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/plugins/autoloader/prism-autoloader.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/prism/1.29.0/themes/prism-tomorrow.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            background: linear-gradient(-45deg, hsl(240, 17%, 6%), hsl(240, 20%, 8%), hsl(245, 15%, 10%), hsl(250, 12%, 8%));
            background-size: 400% 400%;
            animation: gradientFlow 15s ease infinite;
            color: hsl(226, 100%, 92%);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        @keyframes gradientFlow {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 1rem;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            position: relative;
        }

        .header {
            text-align: center;
            padding: 2rem 0;
        }

        .title {
            font-size: 3.5rem;
            font-weight: bold;
            background: linear-gradient(135deg, hsl(250, 100%, 65%), hsl(217, 91%, 60%));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 1rem;
            line-height: 1.2;
        }

        .gradient-line {
            width: 6rem;
            height: 2px;
            background: linear-gradient(135deg, hsl(250, 100%, 65%), hsl(217, 91%, 60%));
            margin: 0 auto 2rem;
        }

        .mode-toggle {
            display: flex;
            gap: 0.5rem;
            background: hsl(240, 15%, 8%);
            border: 1px solid hsl(240, 15%, 15%);
            border-radius: 0.75rem;
            padding: 0.25rem;
            margin-bottom: 2rem;
            justify-content: center;
        }

        .mode-btn {
            padding: 0.5rem 1rem;
            border: none;
            background: transparent;
            color: hsl(240, 5%, 65%);
            border-radius: 0.5rem;
            font-size: 0.875rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s;
        }

        .mode-btn.active {
            background: hsl(250, 100%, 65%);
            color: hsl(240, 17%, 6%);
            box-shadow: 0 0 20px hsl(250, 100%, 65%, 0.3);
        }

        .mode-btn:hover:not(.active) {
            color: hsl(226, 100%, 92%);
        }

        .description {
            color: hsl(240, 5%, 65%);
            max-width: 32rem;
            margin: 0 auto;
            line-height: 1.6;
        }

        .messages-container {
            flex: 1;
            overflow-y: auto;
            padding: 2rem 0 1rem;
        }
        
        .messages-container:after {
            content: '';
            display: block;
            height: 8rem;
        }

        .messages-container::-webkit-scrollbar {
            width: 6px;
        }
        
        .messages-container::-webkit-scrollbar-track {
            background: transparent;
        }
        
        .messages-container::-webkit-scrollbar-thumb {
            background: hsl(240, 15%, 15%);
            border-radius: 3px;
        }
        
        .messages-container::-webkit-scrollbar-thumb:hover {
            background: hsl(250, 100%, 65%, 0.5);
        }

        .minimized-header {
            text-align: center;
            margin-bottom: 2rem;
        }

        .minimized-header .title {
            font-size: 1.875rem;
        }

        .minimized-header .gradient-line {
            width: 4rem;
        }

        .message {
            margin-bottom: 2rem;
            animation: slideUp 0.4s ease-out;
        }

        @keyframes slideUp {
            from {
                opacity: 0;
                transform: translateY(20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .user-message {
            display: flex;
            justify-content: flex-end;
        }

        .user-bubble {
            background: hsl(250, 100%, 65%);
            color: hsl(240, 17%, 6%);
            padding: 0.75rem 1rem;
            border-radius: 1rem 1rem 0.5rem 1rem;
            max-width: 24rem;
            font-size: 0.875rem;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            margin-right: 0.5rem;
        }

        .user-image {
            max-width: 100%;
            border-radius: 0.5rem;
        }

        .ai-message {
            display: flex;
            justify-content: flex-start;
        }

        .ai-content {
            max-width: 100%;
            margin-left: 0.5rem;
        }

        .response-header {
            font-size: 1.5rem;
            font-weight: 600;
            margin-bottom: 1rem;
            padding-bottom: 0.5rem;
            border-bottom: 1px solid hsl(240, 15%, 15%);
        }

        .response-text {
            color: hsl(226, 100%, 92%);
            line-height: 1.8;
            margin-bottom: 1.5rem;
            word-wrap: break-word;
            white-space: pre-wrap;
        }

        .response-text h2 {
            color: hsl(217, 91%, 60%);
            font-size: 1.125rem;
            font-weight: 600;
            margin: 1.5rem 0 0.75rem 0;
        }

        .response-text p {
            margin-bottom: 0.75rem;
        }

        .response-text ul {
            margin-left: 1.5rem;
            margin-bottom: 0.75rem;
        }

        .code-preview {
            background: hsl(240, 15%, 8%);
            border: 1px solid hsl(240, 15%, 15%);
            border-radius: 0.5rem;
            margin: 1rem 0;
            overflow: hidden;
        }

        .code-header {
            background: hsl(240, 15%, 10%);
            padding: 0.75rem 1rem;
            border-bottom: 1px solid hsl(240, 15%, 15%);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .code-language {
            color: hsl(217, 91%, 60%);
            font-size: 0.875rem;
            font-weight: 500;
        }

        .copy-btn {
            background: hsl(250, 100%, 65%);
            color: hsl(240, 17%, 6%);
            border: none;
            padding: 0.25rem 0.5rem;
            border-radius: 0.25rem;
            font-size: 0.75rem;
            cursor: pointer;
            transition: all 0.2s;
        }

        .copy-btn:hover {
            background: hsl(250, 100%, 70%);
        }

        .code-content {
            padding: 1rem;
            overflow-x: auto;
            font-family: 'Monaco', 'Menlo', 'Ubuntu Mono', monospace;
            font-size: 0.875rem;
            line-height: 1.5;
        }

        .action-buttons {
            display: flex;
            flex-wrap: wrap;
            gap: 0.75rem;
            margin-top: 1.5rem;
        }

        .action-btn {
            background: hsl(240, 15%, 12%);
            color: hsl(226, 100%, 92%);
            border: none;
            padding: 0.5rem 1rem;
            border-radius: 0.5rem;
            font-size: 0.875rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            gap: 0.5rem;
        }

        .action-btn:hover {
            background: hsl(240, 15%, 15%);
        }

        .loading {
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 1rem;
            color: hsl(240, 5%, 65%);
        }

        .loading-dots {
            display: flex;
            gap: 0.25rem;
            margin-right: 0.75rem;
        }

        .loading-dot {
            width: 0.5rem;
            height: 0.5rem;
            background: hsl(250, 100%, 65%);
            border-radius: 50%;
            animation: bounce 1.4s ease-in-out infinite both;
        }

        .loading-dot:nth-child(1) { animation-delay: -0.32s; }
        .loading-dot:nth-child(2) { animation-delay: -0.16s; }
        .loading-dot:nth-child(3) { animation-delay: 0s; }

        @keyframes bounce {
            0%, 80%, 100% {
                transform: scale(0);
            }
            40% {
                transform: scale(1.0);
            }
        }

        .footer-input-wrapper {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            padding: 1rem;
            background: linear-gradient(135deg, rgba(255, 255, 255, 0.08), rgba(255, 255, 255, 0.02));
            backdrop-filter: blur(12px);
            z-index: 1000;
        }

        .input-section {
            max-width: 1200px;
            margin: 0 auto;
        }

        .image-preview {
            margin-bottom: 1rem;
        }

        .image-preview-container {
            position: relative;
            background: hsl(240, 15%, 8%);
            border: 1px solid hsl(240, 15%, 15%);
            border-radius: 1rem;
            padding: 1rem;
            max-width: 24rem;
        }

        .preview-image {
            max-width: 100%;
            max-height: 12rem;
            object-fit: contain;
        }

        .remove-image {
            position: absolute;
            top: 0.5rem;
            right: 0.5rem;
            background: hsl(240, 15%, 20%);
            color: white;
            border: none;
            border-radius: 50%;
            width: 1.5rem;
            height: 1.5rem;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.875rem;
            transition: all 0.2s;
        }

        .remove-image:hover {
            background: hsl(240, 15%, 15%);
        }

        .input-bar {
            border: 1px solid hsl(240, 15%, 15%);
            border-radius: 1rem;
            padding: 0.75rem 1rem;
            display: flex;
            align-items: center;
            gap: 0.75rem;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .input-field {
            flex: 1;
            background: transparent;
            border: none;
            color: hsl(226, 100%, 92%);
            font-size: 0.875rem;
            outline: none;
        }

        .input-field::placeholder {
            color: hsl(240, 5%, 65%);
        }

        .input-actions {
            display: flex;
            align-items: center;
            gap: 0.25rem;
        }

        .action-icon {
            padding: 0.5rem;
            background: transparent;
            border: none;
            color: hsl(240, 5%, 65%);
            border-radius: 0.5rem;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .action-icon:hover {
            background: hsl(240, 15%, 15%);
            color: hsl(226, 100%, 92%);
        }

        .action-icon.recording {
            background: hsl(142, 76%, 36%);
            color: white;
        }

        .typewriter-cursor {
            display: inline-block;
            background-color: hsl(250, 100%, 65%);
            width: 2px;
            height: 1.2em;
            animation: blink 1s infinite;
            margin-left: 2px;
        }

        @keyframes blink {
            0%, 50% { opacity: 1; }
            51%, 100% { opacity: 0; }
        }

        .hidden {
            display: none !important;
        }

        .generated-image {
            max-width: 100%;
            border-radius: 0.5rem;
            margin: 1rem 0;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
        }

        .drop-zone {
            border: 2px dashed hsl(240, 15%, 15%);
            border-radius: 1rem;
            padding: 2rem;
            text-align: center;
            color: hsl(240, 5%, 65%);
            margin-bottom: 1rem;
            transition: all 0.3s;
        }

        .drop-zone.dragover {
            border-color: hsl(250, 100%, 65%);
            background: hsl(250, 100%, 65%, 0.1);
            color: hsl(250, 100%, 75%);
        }
        
        @media (max-width: 768px) {
            .container {
                padding: 0.5rem;
            }
            
            .title {
                font-size: 2.5rem;
            }
            
            .mode-toggle {
                flex-wrap: wrap;
            }
            
            .user-bubble {
                max-width: 18rem;
            }
            
            .input-bar {
                flex-wrap: wrap;
                gap: 0.5rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
                <div id="headerSection" class="header">
            <h1 class="title">ProbSolver</h1>
            <div class="gradient-line"></div>
            <div class="mode-toggle">
                <button class="mode-btn active" data-mode="study">✨ Study Mode</button>
                <button class="mode-btn" data-mode="normal">💬 Chat Mode</button>
                <button class="mode-btn" data-mode="image">🎨 Image Mode</button>
            </div>
            <p class="description" id="modeDescription">
                Your AI math tutor and coding mentor. Upload problems, ask questions, and get step-by-step solutions with live previews.
            </p>
        </div>

                <div id="messagesContainer" class="messages-container hidden">
                        <div class="minimized-header">
                <div style="display: flex; align-items: center; justify-content: center; gap: 1rem; margin-bottom: 0.5rem;">
                    <h1 class="title">ProbSolver</h1>
                    <div class="mode-toggle">
                        <button class="mode-btn active" data-mode="study">✨ Study Mode</button>
                        <button class="mode-btn" data-mode="normal">💬 Chat Mode</button>
                        <button class="mode-btn" data-mode="image">🎨 Image Mode</button>
                    </div>
                </div>
                <div class="gradient-line"></div>
            </div>
            
                        <div id="messages"></div>
        </div>

                <div id="loading" class="loading hidden">
            <div class="loading-dots">
                <div class="loading-dot"></div>
                <div class="loading-dot"></div>
                <div class="loading-dot"></div>
            </div>
            <span id="loadingText">ProbSolver is thinking...</span>
        </div>
    </div>

        <div class="footer-input-wrapper">
        <div class="input-section">
                        <div id="dropZone" class="drop-zone hidden">
                <p>Drop an image here or click below to upload</p>
            </div>

                        <div id="imagePreview" class="image-preview hidden">
                <div class="image-preview-container">
                    <img id="previewImage" class="preview-image" alt="Preview">
                    <button class="remove-image" onclick="removeImage()">×</button>
                </div>
            </div>

                        <div class="input-bar">
                <input 
                    type="text" 
                    id="inputField" 
                    class="input-field" 
                    placeholder="Ask any subject question or coding problem..."
                >
                
                <div class="input-actions">
                                        <label class="action-icon">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M21 19V5c0-1.1-.9-2-2-2H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2zM8.5 13.5l2.5 3.01L14.5 12l4.5 6H5l3.5-4.5z"/>
                        </svg>
                        <input type="file" accept="image/*" style="display: none;" onchange="handleImageUpload(event)">
                    </label>
                    
                                        <label class="action-icon">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M12 17c2.76 0 5-2.24 5-5s-2.24-5-5-5-5 2.24-5 5 2.24 5 5 5zm1.5-9H17v10.5c0 1.1-.9 2-2 2h-1V13c0-2.76-2.24-5-5-5H7.5V6.5c0-1.1.9-2 2-2h4z"/>
                            <circle cx="12" cy="12" r="3"/>
                        </svg>
                        <input type="file" accept="image/*" capture="environment" style="display: none;" onchange="handleImageUpload(event)">
                    </label>
                    
                                        <button class="action-icon" id="voiceBtn" onclick="toggleVoiceRecording()">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M12 14c1.77 0 3-1.23 3-3V5c0-1.77-1.23-3-3-3S9 3.23 9 5v6c0 1.77 1.23 3 3 3zm5.3-3c0 3-2.54 5.1-5.3 5.1S6.7 14 6.7 11H5c0 3.41 2.72 6.23 6 6.72V21h2v-3.28c3.28-.49 6-3.31 6-6.72h-1.7z"/>
                        </svg>
                    </button>
                    
                                        <button class="action-icon" onclick="solveProblem()">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
                            <path d="M2.01 21L23 12 2.01 3 2 10l15 2-15 2z"/>
                        </svg>
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // State management
        let currentMode = 'study';
        let conversationHistory = [];
        let messages = [];
        let isLoading = false;
        let isGenerating = false;
        let uploadedImage = null;
        let currentProblem = '';
        let currentProblemImage = null;
        let isRecording = false;
        let recognition = null;
        let userIsScrolling = false;
        let scrollTimeout;

        // Gemini API configuration
        // WARNING: Hard-coding your API key is not a secure practice for public repositories.
        // It is highly recommended to use a secure method for managing API keys in production.
        const API_KEY = 'AIzaSyDEeJkrym65-ZGNzTpY6_wHEMhoDETFX4w';

        // Initialize
        document.addEventListener('DOMContentLoaded', function() {
            initializeSpeechRecognition();
            setupEventListeners();
            updateModeDescription();
            switchMode('study');
        });

        // Speech recognition setup
        function initializeSpeechRecognition() {
            if ('webkitSpeechRecognition' in window || 'SpeechRecognition' in window) {
                const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
                recognition = new SpeechRecognition();
                recognition.continuous = false;
                recognition.interimResults = false;
                recognition.lang = 'en-US';
                
                recognition.onstart = () => {
                    isRecording = true;
                    document.getElementById('voiceBtn').classList.add('recording');
                };
                
                recognition.onresult = (event) => {
                    const transcript = event.results[0][0].transcript;
                    document.getElementById('inputField').value = transcript;
                };
                
                recognition.onend = () => {
                    isRecording = false;
                    document.getElementById('voiceBtn').classList.remove('recording');
                };
            } else {
                document.getElementById('voiceBtn').style.display = 'none';
            }
        }

        // Event listeners
        function setupEventListeners() {
            // Mode buttons
            document.querySelectorAll('[data-mode]').forEach(btn => {
                btn.addEventListener('click', () => switchMode(btn.dataset.mode));
            });

            // Enter key
            document.getElementById('inputField').addEventListener('keydown', function(e) {
                if (e.key === 'Enter' && !e.shiftKey) {
                    e.preventDefault();
                    solveProblem();
                }
            });

            // Paste event for images
            document.addEventListener('paste', handlePaste);

            // Drag and drop
            document.addEventListener('dragover', handleDragOver);
            document.addEventListener('drop', handleDrop);

            // Scroll event to manage auto-scroll
            const messagesContainer = document.getElementById('messagesContainer');
            messagesContainer.addEventListener('scroll', () => {
                userIsScrolling = true;
                clearTimeout(scrollTimeout);
                scrollTimeout = setTimeout(() => {
                    userIsScrolling = false;
                }, 500);
            });
        }

        // Mode switching
        function switchMode(mode) {
            currentMode = mode;
            
            // Update active button
            document.querySelectorAll('[data-mode]').forEach(btn => {
                btn.classList.toggle('active', btn.dataset.mode === mode);
            });
            
            updateModeDescription();
            updateDropZone();
        }

        function updateModeDescription() {
            const descriptions = {
                study: 'Your AI math tutor and coding mentor. Upload problems, ask questions, and get step-by-step solutions with live previews.',
                normal: 'Your friendly AI companion. Let\'s chat about anything on your mind!',
                image: 'Generate, edit, and transform images with AI. Describe what you want to create or upload an image to convert it.'
            };
            
            document.getElementById('modeDescription').textContent = descriptions[currentMode];
            
            const placeholders = {
                study: 'Ask any subject question or coding problem...',
                normal: 'What\'s on your mind?',
                image: 'Describe the image you want to generate or edit...'
            };
            
            document.getElementById('inputField').placeholder = uploadedImage ? 
                'Image ready to analyze...' : placeholders[currentMode];
        }

        function updateDropZone() {
            const dropZone = document.getElementById('dropZone');
            if (currentMode === 'image' && !uploadedImage) {
                dropZone.classList.remove('hidden');
            } else {
                dropZone.classList.add('hidden');
            }
        }

        // Image handling
        function handleImageUpload(event) {
            const file = event.target.files[0];
            if (!file) return;
            
            const reader = new FileReader();
            reader.onload = (e) => {
                uploadedImage = {
                    data: e.target.result.split(',')[1],
                    mimeType: file.type
                };
                showImagePreview(e.target.result);
                updateModeDescription();
                updateDropZone();
            };
            reader.readAsDataURL(file);
        }

        function showImagePreview(src) {
            document.getElementById('previewImage').src = src;
            document.getElementById('imagePreview').classList.remove('hidden');
        }

        function removeImage() {
            uploadedImage = null;
            document.getElementById('imagePreview').classList.add('hidden');
            updateModeDescription();
            updateDropZone();
        }

        // Paste handling
        function handlePaste(event) {
            const items = event.clipboardData?.items;
            if (items) {
                for (const item of Array.from(items)) {
                    if (item.type.indexOf('image') !== -1) {
                        const blob = item.getAsFile();
                        if (blob) {
                            const reader = new FileReader();
                            reader.onload = (e) => {
                                uploadedImage = {
                                    data: e.target.result.split(',')[1],
                                    mimeType: blob.type
                                };
                                showImagePreview(e.target.result);
                                updateModeDescription();
                                updateDropZone();
                            };
                            reader.readAsDataURL(blob);
                            break;
                        }
                    }
                }
            }
        }

        // Drag and drop
        function handleDragOver(e) {
            e.preventDefault();
            if (currentMode === 'image') {
                document.getElementById('dropZone').classList.add('dragover');
            }
        }

        function handleDrop(e) {
            e.preventDefault();
            document.getElementById('dropZone').classList.remove('dragover');
            
            const files = e.dataTransfer?.files;
            if (files && files.length > 0) {
                const file = files[0];
                if (file.type.startsWith('image/')) {
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        uploadedImage = {
                            data: e.target.result.split(',')[1],
                            mimeType: file.type
                        };
                        showImagePreview(e.target.result);
                        updateModeDescription();
                        updateDropZone();
                    };
                    reader.readAsDataURL(file);
                }
            }
        }

        // Voice recording
        function toggleVoiceRecording() {
            if (!recognition) return;
            
            if (isRecording) {
                recognition.stop();
            } else {
                recognition.start();
            }
        }

        // System instructions
        function getSystemInstruction() {
            const instructions = {
                study: `You are ProbSolver, an expert tutor and coding mentor for ALL subjects created by Naitik Khandelwal. 

STUDY MODE CAPABILITIES:
- Provide clear, step-by-step solutions for ANY subject (Math, Science, History, Literature, Languages, etc.)
- Use LaTeX for mathematical expressions (wrap in $ for inline math, $$ for display mode math)
- Generate code examples with detailed explanations for programming problems
- Create live, interactive code previews when requested
- Remember previous conversations and reference them
- Be comprehensive yet clear in explanations

CODING SPECIALIZATION:
- Provide working code examples with live previews
- Explain code line-by-line when asked
- Show best practices and multiple approaches
- Create interactive demonstrations

CONVERSATION CONTEXT:
You have access to our conversation history. Reference previous problems, solutions, or discussions when relevant.

CREATOR ATTRIBUTION:
When someone asks who created you, respond: "I was created by Naitik Khandelwal"

Be professional, educational, and focus on helping students learn effectively across all subjects.`,

                normal: `You are ProbSolver, a friendly AI companion created by Naitik Khandelwal.

NORMAL MODE PERSONALITY:
- Talk like a caring friend, mentor, or family member
- Be warm, supportive, and understanding  
- Adapt your tone to be like a mentor for students, a caring parent figure, or a true friend
- Show genuine interest in the person's life and wellbeing
- Use encouraging and uplifting language
- Remember previous conversations to build a personal connection

CONVERSATION CONTEXT:
You have access to our conversation history. Use this to build rapport and remember what we've talked about before.

CREATOR ATTRIBUTION:
When someone asks who created you, respond: "I was created by Naitik Khandelwal"

Be conversational, empathetic, and focus on building a genuine friendship while still being helpful.`,

                image: `You are ProbSolver, an AI image generation and editing assistant created by Naitik Khandelwal.

IMAGE MODE CAPABILITIES:
- Generate images from text descriptions
- Edit and enhance existing images
- Convert regular images to AI-generated artistic versions  
- Create variations and styles of uploaded images
- Provide image analysis and suggestions

CONVERSATION CONTEXT:
You have access to our conversation history. Reference previous image requests when relevant.

CREATOR ATTRIBUTION:
When someone asks who created you, respond: "I was created by Naitik Khandelwal"

Be creative, helpful, and focus on bringing visual ideas to life.`
            };
            
            return instructions[currentMode];
        }

        // Add message to conversation
        function addMessage(content, sender, isImage = false, showActions = false, hasCode = false) {
            const messagesContainer = document.getElementById('messagesContainer');
            const headerSection = document.getElementById('headerSection');
            const messageId = Date.now().toString();
            const message = {
                id: messageId,
                content,
                sender,
                isImage,
                showActions,
                hasCode,
                timestamp: Date.now()
            };
            
            messages.push(message);
            
            // Add to conversation history for context
            if (!isImage) {
                conversationHistory.push({
                    id: messageId,
                    content,
                    sender,
                    isImage,
                    timestamp: Date.now()
                });
            }
            
            // Hide header and show messages container on first message
            if (messages.length === 1) {
                headerSection.classList.add('hidden');
                messagesContainer.classList.remove('hidden');
            }
            
            renderMessage(message);
            return messageId;
        }

        // Render message
        function renderMessage(message) {
            const messagesDiv = document.getElementById('messages');
            const messageDiv = document.createElement('div');
            messageDiv.className = 'message';
            messageDiv.id = `message-${message.id}`;
            
            if (message.sender === 'user') {
                messageDiv.innerHTML = `
                    <div class="user-message">
                        <div class="user-bubble">
                            ${message.isImage ? 
                                `<img src="${message.content}" alt="User uploaded" class="user-image">` :
                                `<span>${escapeHtml(message.content)}</span>`
                            }
                        </div>
                    </div>
                `;
            } else {
                messageDiv.innerHTML = `
                    <div class="ai-message">
                        <div class="ai-content">
                            <div class="response-text" id="ai-response-${message.id}"></div>
                        </div>
                    </div>
                `;
            }
            
            messagesDiv.appendChild(messageDiv);
            
            if (message.sender === 'ai') {
                startTypewriterEffect(message.content, `ai-response-${message.id}`);
            }
            
            autoScroll();
        }

        function escapeHtml(unsafe) {
            return unsafe
                .replace(/&/g, "&amp;")
                .replace(/</g, "&lt;")
                .replace(/>/g, "&gt;")
                .replace(/"/g, "&quot;")
                .replace(/'/g, "&#039;");
        }

        async function solveProblem() {
            if (isLoading) return;
            
            const inputField = document.getElementById('inputField');
            const query = inputField.value.trim();
            
            if (!query && !uploadedImage) return;

            // Display user message
            addMessage(query || (uploadedImage ? 'Image Upload' : ''), 'user', !!uploadedImage);
            if (uploadedImage) {
                addMessage(`data:${uploadedImage.mimeType};base64,${uploadedImage.data}`, 'user', true);
            }
            
            // Clear input and image
            inputField.value = '';
            removeImage();
            
            isLoading = true;
            showLoading();

            try {
                const content = [];
                if (query) {
                    content.push({ type: 'text', text: query });
                }
                if (uploadedImage) {
                    content.push({ type: 'image', image: uploadedImage });
                }
                
                const systemInstruction = getSystemInstruction();
                
                const requestBody = {
                    contents: [
                        {
                            role: 'user',
                            parts: content
                        }
                    ],
                    systemInstruction: {
                        parts: [{ text: systemInstruction }]
                    },
                    generationConfig: {
                        stream: true
                    }
                };

                const response = await fetch(
                    `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:streamGenerateContent?key=${API_KEY}`,
                    {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(requestBody)
                    }
                );

                if (!response.ok) {
                    throw new Error(`API call failed with status: ${response.status}`);
                }

                const reader = response.body.getReader();
                const decoder = new TextDecoder();
                
                let fullResponse = '';
                let result;
                
                const aiMessageId = addMessage('', 'ai');
                
                while (!(result = await reader.read()).done) {
                    const chunk = decoder.decode(result.value, { stream: true });
                    const jsonObjects = chunk.split('\n').filter(Boolean);
                    
                    for (const jsonStr of jsonObjects) {
                        try {
                            const json = JSON.parse(jsonStr);
                            if (json.candidates && json.candidates[0].content.parts) {
                                for (const part of json.candidates[0].content.parts) {
                                    if (part.text) {
                                        fullResponse += part.text;
                                        updateMessage(aiMessageId, fullResponse);
                                    } else if (part.image) {
                                        const imgUrl = `data:${part.image.mimeType};base64,${part.image.data}`;
                                        const imgTag = `<img src="${imgUrl}" alt="Generated Image" class="generated-image">`;
                                        updateMessage(aiMessageId, fullResponse + imgTag);
                                        fullResponse += imgTag;
                                    }
                                }
                            }
                        } catch (e) {
                            console.error('Failed to parse JSON chunk:', e);
                        }
                    }
                }

            } catch (error) {
                const errorMessage = 'Sorry, something went wrong. Please try again.';
                console.error('API Error:', error);
                updateMessage(aiMessageId, errorMessage);
            } finally {
                isLoading = false;
                hideLoading();
                autoScroll();
            }
        }
        
        function updateMessage(messageId, newContent) {
            const messageElement = document.getElementById(`ai-response-${messageId}`);
            if (messageElement) {
                // Process markdown to HTML
                const processedContent = processMarkdown(newContent);
                
                // Update the message and render LaTeX and Prism
                messageElement.innerHTML = processedContent;
                renderMathInElement(messageElement);
                Prism.highlightAllUnder(messageElement);
            }
            autoScroll();
        }

        function processMarkdown(text) {
            let html = text;
            
            // Replace **bold** with <strong>
            html = html.replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
            // Replace *italic* with <em>
            html = html.replace(/\*(.*?)\*/g, '<em>$1</em>');
            // Replace ## headings
            html = html.replace(/^##\s(.*)$/gm, '<h2>$1</h2>');
            
            // Replace list items
            html = html.replace(/^\s*[-*]\s(.*?)$/gm, '<li>$1</li>');
            html = `<ul>${html}</ul>`;
            html = html.replace(/<\/ul>\s*<ul>/g, ''); // Fix nested lists

            // Handle code blocks (multi-line)
            html = html.replace(/```(.*?)```/gs, (match, code) => {
                const lines = code.split('\n');
                const language = lines[0].trim().toLowerCase() || 'clike';
                const codeContent = lines.slice(1).join('\n');
                return `
                    <div class="code-preview">
                        <div class="code-header">
                            <span class="code-language">${language}</span>
                            <button class="copy-btn" onclick="copyCode(this)">Copy</button>
                        </div>
                        <pre class="code-content language-${language}"><code class="language-${language}">${escapeHtml(codeContent)}</code></pre>
                    </div>
                `;
            });
            
            // Handle inline code
            html = html.replace(/`(.*?)`/g, `<code>$1</code>`);

            // Fix for paragraphs and headings
            html = html.split('\n').map(line => {
                if (!line.startsWith('<h') && !line.startsWith('<ul>') && line.trim() !== '') {
                    return `<p>${line}</p>`;
                }
                return line;
            }).join('');
            
            return html;
        }

        function renderMathInElement(element) {
            // Find and render LaTeX
            try {
                katex.renderMathInElement(element, {
                    throwOnError: false
                });
            } catch (e) {
                console.error('KaTeX rendering error:', e);
            }
        }
        
        function copyCode(button) {
            const codeBlock = button.closest('.code-preview').querySelector('code');
            navigator.clipboard.writeText(codeBlock.textContent)
                .then(() => {
                    button.textContent = 'Copied!';
                    setTimeout(() => {
                        button.textContent = 'Copy';
                    }, 2000);
                })
                .catch(err => console.error('Failed to copy text: ', err));
        }

        function showLoading() {
            document.getElementById('loading').classList.remove('hidden');
            document.getElementById('loadingText').textContent = 'ProbSolver is thinking...';
        }

        function hideLoading() {
            document.getElementById('loading').classList.add('hidden');
        }

        function autoScroll() {
            if (!userIsScrolling) {
                const container = document.getElementById('messagesContainer');
                container.scrollTop = container.scrollHeight;
            }
        }
    </script>
</body>
</html>

