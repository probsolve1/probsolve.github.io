
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ProbSolver</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.css" xintegrity="sha384-GvrxHw0jGz5iG1P5D4z9z5E5q6p7c5O5N5P5q6a7x8E5" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.js" xintegrity="sha384-jM3l3g0B0s3k3q1g1r1p1d1s1t1u1v1w1x1y1z1=" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/contrib/auto-render.min.js" xintegrity="sha384-NlO2lJ+s3g2b1b1a1c1d1e1f1g1h1i1j1k1l1m1n1o1p1q1r1s1t1u1v1w1x1y1z1=" crossorigin="anonymous" onload="renderMathInElement(document.body);"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
        }
        .solver-container {
            background-color: #ffffff;
            border-radius: 1.5rem;
            padding: 2rem;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            width: 100%;
            max-width: 600px;
        }
        .input-area {
            width: 100%;
            min-height: 120px;
            padding: 1rem;
            border-radius: 0.75rem;
            background-color: #f9fafb;
            color: #1f2937;
            font-size: 1.125rem;
            resize: vertical;
            border: 2px solid transparent;
            transition: border-color 0.2s;
            outline: none;
        }
        .input-area:focus {
            border-color: #34d399;
        }
        .solve-button {
            width: 100%;
            margin-top: 1rem;
            padding: 1rem;
            color: white;
            font-weight: 700;
            border-radius: 0.5rem;
            background-color: #34d399; /* emerald-500 */
            transition-property: background-color, box-shadow;
            transition-duration: 0.2s, 0.3s;
            transition-timing-function: ease-in-out;
            box-shadow: 0 0 10px rgba(52, 211, 153, 0.4);
            cursor: pointer;
        }

        .solve-button:hover {
            background-color: #2e9f78; /* emerald-600 */
            box-shadow: 0 0 20px rgba(52, 211, 153, 0.6);
        }

        .solve-button:active {
            background-color: #298d6c; /* emerald-700 */
        }
        
        .result-box {
            margin-top: 1.5rem;
            padding: 1.5rem;
            background-color: #f3f4f6;
            border-radius: 0.75rem;
            min-height: 150px;
            color: #1f2937;
            overflow-y: auto;
            max-height: 500px;
            line-height: 1.6;
        }
        .result-box h2 {
            font-size: 1.5rem;
            font-weight: 600;
            margin-top: 1rem;
            margin-bottom: 0.5rem;
            color: #1f2937;
        }
        .result-box h2:first-of-type {
            margin-top: 0;
        }
        .result-box p {
            margin-bottom: 1rem;
        }
        .image-preview {
            max-width: 100%;
            margin-top: 1rem;
            border-radius: 0.5rem;
            border: 2px solid #e5e7eb;
        }
        .upload-button-container {
            display: flex;
            justify-content: center;
            margin-top: 1rem;
        }
        .upload-button {
            padding: 0.75rem 1.5rem;
            background-color: #e5e7eb;
            color: #4b5563;
            border-radius: 0.5rem;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .upload-button:hover {
            background-color: #d1d5db;
        }
    </style>
</head>
<body>
    <div class="solver-container">
        <h1 class="text-3xl font-bold mb-4 text-center text-gray-800">ProbSolver</h1>
        
        <div class="word-problem-solver mt-4">
            <p class="mb-4 text-center text-gray-600">Enter your math problem or upload a photo of it below and get a detailed solution.</p>
            <textarea id="wordProblemInput" placeholder="Type your math problem here..."
                class="input-area"></textarea>
            <div class="upload-button-container">
                <label for="imageUpload" class="upload-button">Upload Photo</label>
                <input type="file" id="imageUpload" class="hidden" accept="image/*" />
            </div>
            <img id="imagePreview" src="#" alt="Image Preview" class="image-preview hidden" />
            <button id="solveButton"
                class="solve-button">Solve Problem</button>
            <div id="wordProblemResult" class="result-box">
                <p class="text-gray-500 text-center">Your solution will appear here.</p>
            </div>
        </div>
    </div>

    <script>
        const wordProblemInput = document.getElementById('wordProblemInput');
        const imageUpload = document.getElementById('imageUpload');
        const imagePreview = document.getElementById('imagePreview');
        const solveButton = document.getElementById('solveButton');
        const wordProblemResult = document.getElementById('wordProblemResult');

        let uploadedImageBase64 = null;
        let uploadedImageMimeType = null;

        imageUpload.addEventListener('change', (event) => {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const base64String = e.target.result;
                    imagePreview.src = base64String;
                    imagePreview.classList.remove('hidden');
                    uploadedImageBase64 = base64String.split(',')[1];
                    uploadedImageMimeType = file.type;
                };
                reader.readAsDataURL(file);
            }
        });

        function parseAndRenderMarkdown(markdown) {
            const lines = markdown.split('\n');
            let html = '';
            let inList = false;
            let currentListType = '';

            lines.forEach(line => {
                if (line.startsWith('## ')) {
                    if (inList) {
                        html += `</${currentListType}>`;
                        inList = false;
                    }
                    html += `<h2>${line.substring(3).trim()}</h2>`;
                } else if (line.trim().startsWith('* ')) {
                    const listItem = line.trim().substring(2).trim();
                    if (!inList || currentListType !== 'ul') {
                        if (inList) html += `</${currentListType}>`;
                        html += `<ul>`;
                        inList = true;
                        currentListType = 'ul';
                    }
                    html += `<li>${listItem}</li>`;
                } else if (line.trim().startsWith('1. ')) {
                    const listItem = line.trim().substring(2).trim();
                    if (!inList || currentListType !== 'ol') {
                        if (inList) html += `</${currentListType}>`;
                        html += `<ol>`;
                        inList = true;
                        currentListType = 'ol';
                    }
                    html += `<li>${listItem}</li>`;
                } else if (line.trim() !== '') {
                    if (inList) {
                        html += `</${currentListType}>`;
                        inList = false;
                    }
                    html += `<p>${line.trim()}</p>`;
                }
            });
            if (inList) {
                html += `</${currentListType}>`;
            }
            return html;
        }

        solveButton.addEventListener('click', async () => {
            const userQuery = wordProblemInput.value.trim();
            if (!userQuery && !uploadedImageBase64) {
                wordProblemResult.innerHTML = '<p class="text-red-500 text-center">Please enter a word problem or upload an image.</p>';
                return;
            }

            wordProblemResult.innerHTML = '<p class="text-gray-500 text-center animate-pulse">Solving...</p>';

            const systemPrompt = "You are a helpful and expert math tutor. Your task is to solve the provided math word problem, which may be given as text, an image, or both. First, provide a clear, step-by-step solution. Use markdown headings (e.g., ## Step 1) to break down the process. Use LaTeX for all mathematical expressions and formulas. After the solution steps, create a final heading titled 'Final Answer' and clearly state the answer. Do not include any introductory or concluding sentences. The entire output should be a single block of text representing the formatted solution.";

            const apiKey = "";
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;

            const parts = [];
            if (userQuery) {
                parts.push({ text: userQuery });
            }
            if (uploadedImageBase64) {
                parts.push({
                    inlineData: {
                        mimeType: uploadedImageMimeType,
                        data: uploadedImageBase64
                    }
                });
            }

            const payload = {
                contents: [{ parts }],
                tools: [{ "google_search": {} }],
                systemInstruction: {
                    parts: [{ text: systemPrompt }]
                },
            };

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const result = await response.json();
                const text = result?.candidates?.[0]?.content?.parts?.[0]?.text || "Sorry, I couldn't solve that problem.";
                
                wordProblemResult.innerHTML = parseAndRenderMarkdown(text);
                
                renderMathInElement(wordProblemResult, {
                    delimiters: [
                        {left: "$$", right: "$$", display: true},
                        {left: "$", right: "$", display: false},
                        {left: "\\[", right: "\\]", display: true},
                        {left: "\\(", right: "\\)", display: false}
                    ]
                });

            } catch (error) {
                wordProblemResult.innerHTML = `<p class="text-red-500 text-center">An error occurred while solving the problem.</p>`;
                console.error('API Error:', error);
            }
        });

        // Add event listener for the Enter key on the textarea
        wordProblemInput.addEventListener('keydown', (event) => {
            if (event.key === 'Enter') {
                event.preventDefault(); // Prevents a new line from being added
                solveButton.click(); // Trigger the click event on the solve button
            }
        });
    </script>
</body>
</html>
