<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simeon's game: infektivno 10ka</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700;800&display=swap');

        body {
            font-family: 'Inter', sans-serif;
            background-color: #0c0a09; /* Darker background */
            background-image: linear-gradient(135deg, #1f2937 0%, #374151 100%);
            color: #d1d5db; /* Lighter text for contrast */
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
            overflow-y: auto;
            animation: background-pan 25s linear infinite alternate;
        }

        @keyframes background-pan {
            from {
                background-position: 0% 0%;
            }
            to {
                background-position: 100% 100%;
            }
        }

        .container {
            background-color: #1f2937;
            border: 1px solid #4b5563;
            border-radius: 1.5rem; /* More rounded corners */
            padding: 2.5rem; /* More padding */
            max-width: 800px;
            width: 100%;
            box-shadow: 0 10px 40px rgba(0, 0, 0, 0.5);
            text-align: center;
            transition: all 0.3s ease-in-out;
            position: relative;
            z-index: 10;
        }

        .button {
            transition: transform 0.2s ease-in-out, background-color 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
            touch-action: manipulation;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
            font-weight: bold;
            letter-spacing: 0.05em;
            text-transform: uppercase;
        }
        .button:hover:not([disabled]) {
            transform: scale(1.05);
            background-color: #1d4ed8;
            box-shadow: 0 8px 20px rgba(29, 78, 216, 0.6);
        }
        .button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }
        #game-container, #game-over-container {
            display: none;
        }
        #case-text, #feedback-text {
            font-size: 1.1rem;
            line-height: 1.6;
            margin-bottom: 2rem;
            white-space: pre-wrap;
            text-align: left;
            background-color: #374151;
            padding: 1.5rem;
            border-radius: 0.75rem;
            border: 1px solid #4b5563;
            box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.3);
        }
        .diagnosis-button {
            background-color: #4b5563;
            border: 1px solid #6b7280;
            border-radius: 0.75rem;
            padding: 1rem;
            margin: 0.5rem 0;
            width: 100%;
            text-align: left;
            font-weight: 500;
            transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out, box-shadow 0.2s ease-in-out;
            cursor: pointer;
        }
        .diagnosis-button:hover:not([disabled]) {
            background-color: #6b7280;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
            transform: translateY(-3px);
        }
        .correct {
            background-color: #15803d !important;
            border-color: #16a34a !important;
            color: white !important;
            box-shadow: 0 2px 8px rgba(21, 128, 61, 0.8) !important;
        }
        .incorrect {
            background-color: #dc2626 !important;
            border-color: #ef4444 !important;
            color: white !important;
            box-shadow: 0 2px 8px rgba(220, 38, 38, 0.8) !important;
        }
        .diagnosis-button:disabled {
            cursor: not-allowed;
            opacity: 1;
            box-shadow: none;
            transform: none;
        }
        #spinner-container {
            position: relative;
            width: 300px;
            height: 300px;
            margin: 2rem auto;
        }
        #wheel-canvas {
            border-radius: 50%;
            box-shadow: 0 0 30px rgba(255, 255, 255, 0.2);
            border: 5px solid #60a5fa;
        }
        #pointer {
            position: absolute;
            top: -20px;
            left: 50%;
            transform: translateX(-50%);
            width: 0;
            height: 0;
            border-left: 20px solid transparent;
            border-right: 20px solid transparent;
            border-top: 30px solid #f97316;
            z-index: 10;
        }
        #game-over-container {
            animation: fade-in 1s ease-out;
        }

        @keyframes fade-in {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="bg-gray-900 text-gray-200">
    <div class="container">
        <h1 class="text-3xl font-extrabold text-center mb-4 text-white">Simeon's game: infektivno 10ka</h1>
        <div id="intro-screen">
            <h2 class="text-2xl font-bold mb-4">The Rise of a Legend</h2>
            <p class="text-justify mb-6">Born in the small town of Kicevo, Simeon Mateski was not like other boys. While his friends played with soccer balls, he was mesmerized by the intricate world of biology and pathogens. His insatiable curiosity and brilliant mind led him on an incredible journey, through medical school and into the most complex cases. Today, he is known as the world's most brilliant infectious disease specialist, a true master of the diagnostic puzzle. The challenge you are about to face is a test of your knowledge and resolve, designed to prove your worthiness to stand alongside him. Can you master the grand rounds?</p>
            <div class="flex justify-center my-6">
                <img src="https://i.postimg.cc/DwhSQ1tJ/1c1afb12-576c-41ca-85c6-891feeef08fb.jpg" alt="A man in a white lab coat" class="rounded-lg shadow-lg w-1/2">
            </div>
            <div class="flex flex-col sm:flex-row justify-center items-center gap-4">
                <button id="start-button" class="button bg-blue-600 text-white py-3 px-8 rounded-full">
                    Start Grand Rounds
                </button>
            </div>
        </div>

        <div id="game-container" class="hidden">
            <h2 class="text-2xl font-bold mb-4" id="case-title">Case <span id="case-number">1</span> of 15</h2>
            <div id="spinner-container" class="flex flex-col items-center">
                <canvas id="wheel-canvas" width="300" height="300"></canvas>
                <div id="pointer"></div>
                <button id="spin-button" class="button bg-blue-600 text-white py-3 px-8 rounded-full mt-8">Spin for a Case!</button>
            </div>

            <div id="case-display" class="hidden">
                <p id="case-text" class="text-lg mb-8"></p>
                <div id="diagnosis-buttons" class="w-full"></div>
            </div>
            
            <div id="feedback-section" class="hidden flex-col">
                <p id="feedback-text" class="mt-4 text-justify"></p>
                <button id="next-case-button" class="button bg-green-600 text-white py-3 px-8 rounded-full mt-4 mx-auto">Next Case</button>
            </div>
        </div>

        <div id="game-over-container" class="hidden text-center">
            <h2 class="text-2xl font-bold mb-4 text-white">Challenge Complete!</h2>
            <p id="final-message" class="text-xl mb-4"></p>
            <button id="restart-button" class="button bg-green-600 text-white py-3 px-8 rounded-full">
                Start Over
            </button>
        </div>
    </div>

    <script>
        const cases = [
            {
                caseText: "A 45-year-old male with a history of travel to Southeast Asia presents with high fever, severe myalgia, and a maculopapular rash. His lab results show significant thrombocytopenia and elevated liver enzymes. Initial tests for dengue fever are negative. He develops sudden hypotension and plasma leakage. Which of the following is the most likely diagnosis?",
                diagnoses: [
                    { text: "Chikungunya fever", isCorrect: false },
                    { text: "Viral hemorrhagic fever (Ebola)", isCorrect: false },
                    { text: "Dengue hemorrhagic fever (DHF)", isCorrect: false },
                    { text: "Severe dengue shock syndrome (DENV infection from a different serotype)", isCorrect: true }
                ],
                solution: "This is a classic case of **Severe Dengue Shock Syndrome**. The initial negative test for dengue is a red herring; the severe symptoms (hypotension, plasma leakage) indicate a second infection with a different dengue serotype, which triggers a more severe, and often fatal, immune response known as antibody-dependent enhancement. Chikungunya and viral hemorrhagic fevers present differently, and a classic DHF diagnosis does not fully capture the severity of the shock observed."
            },
            {
                caseText: "A 60-year-old man from rural Idaho presents with altered mental status, high fever, and photophobia. He has a recent history of a tick bite but no classic rash. A lumbar puncture reveals lymphocytic pleocytosis. Initial tests for Lyme disease are negative. The patient develops progressive encephalomyelitis. What is the most likely diagnosis?",
                diagnoses: [
                    { text: "Rocky Mountain spotted fever", isCorrect: false },
                    { text: "Powassan virus disease", isCorrect: true },
                    { text: "West Nile virus encephalitis", isCorrect: false },
                    { text: "Bacterial meningitis (misdiagnosed)", isCorrect: false }
                ],
                solution: "The patient's presentation—specifically the tick bite, progressive encephalitis without a rash, and residence in the northeastern U.S. or Great Lakes region—is highly suggestive of **Powassan virus disease**. Unlike Lyme, it is a neuroinvasive arbovirus that can cause permanent neurological damage. Rocky Mountain Spotted Fever typically presents with a rash and is not neuroinvasive in the same way. West Nile virus is carried by mosquitoes, not ticks."
            },
            {
                caseText: "A young woman with a history of cystic fibrosis presents with worsening cough and dyspnea. Her sputum cultures consistently grow mucoid, non-fermenting, Gram-negative rods that are resistant to most standard antibiotics. A chest CT shows bronchiectasis. Which pathogen is responsible for this chronic, difficult-to-treat infection?",
                diagnoses: [
                    { text: "Staphylococcus aureus", isCorrect: false },
                    { text: "Burkholderia cepacia complex", isCorrect: true },
                    { text: "Pseudomonas aeruginosa (non-mucoid strain)", isCorrect: false },
                    { text: "Aspergillus fumigatus", isCorrect: false }
                ],
                solution: "While **Pseudomonas aeruginosa** is a common cause of chronic lung infections in cystic fibrosis patients, the description of a mucoid, non-fermenting Gram-negative rod that is resistant to most standard antibiotics and causes worsening lung function is characteristic of the **Burkholderia cepacia complex**. This pathogen is highly transmissible among CF patients and often leads to a rapid decline in lung function, making it a critical diagnosis."
            },
            {
                caseText: "A 35-year-old male with a history of intravenous drug use is admitted with fever, new-onset heart murmur, and Roth's spots on fundoscopy. Blood cultures are pending, but initial empiric therapy is started. What is the most likely underlying infectious condition and causative agent?",
                diagnoses: [
                    { text: "Bacterial pneumonia (Streptococcus pneumoniae)", isCorrect: false },
                    { text: "Meningitis (Neisseria meningitidis)", isCorrect: false },
                    { text: "Infective endocarditis (Staphylococcus aureus)", isCorrect: true },
                    { text: "Osteomyelitis (Pseudomonas aeruginosa)", isCorrect: false }
                ],
                solution: "This is a classic presentation of **Infective Endocarditis**, particularly in a patient with a history of intravenous drug use. The new-onset heart murmur, fever, and tell-tale Roth's spots (retinal hemorrhages) are key indicators. The most common causative agent in this population is **Staphylococcus aureus**, which often affects the tricuspid valve."
            },
            {
                caseText: "A 5-year-old child presents with a 'slapped-cheek' facial rash followed by a lacy, reticular rash on the trunk and extremities. The child is otherwise afebrile and has no other significant symptoms. This condition is most likely caused by which virus?",
                diagnoses: [
                    { text: "Human herpesvirus 6 (Roseola)", isCorrect: false },
                    { text: "Measles virus", isCorrect: false },
                    { text: "Parvovirus B19", isCorrect: true },
                    { text: "Varicella-zoster virus", isCorrect: false }
                ],
                solution: "The 'slapped-cheek' rash followed by a lacy, reticular rash is the hallmark presentation of **Erythema Infectiosum**, or Fifth Disease, which is caused by **Parvovirus B19**. This is a classic pediatric infectious disease vignette."
            },
            {
                caseText: "An elderly patient with a recent hip fracture repair presents with fever, productive cough, and a cavitary lesion on chest X-ray. Sputum cultures grow a non-motile, lactose-fermenting, encapsulated gram-negative rod that produces a thick, mucoid colony. Which organism is the most probable cause?",
                diagnoses: [
                    { text: "Escherichia coli", isCorrect: false },
                    { text: "Klebsiella pneumoniae", isCorrect: true },
                    { text: "Pseudomonas aeruginosa", isCorrect: false },
                    { text: "Haemophilus influenzae", isCorrect: false }
                ],
                solution: "The combination of the patient's age and recent hospitalization, along with a cavitary lesion on chest X-ray and the distinct mucoid, encapsulated, lactose-fermenting Gram-negative rod in sputum culture, points directly to **Klebsiella pneumoniae**. This organism is known for causing severe, destructive pneumonia, particularly in hospitalized or immunocompromised patients."
            },
            {
                caseText: "A 25-year-old male presents to the emergency department with profuse, watery diarrhea described as 'rice-water stools.' He has no fever or abdominal pain, but is severely dehydrated. He recently returned from a trip to India. The causative agent of this disease acts by producing a toxin that permanently activates adenylate cyclase. Which pathogen is it?",
                diagnoses: [
                    { text: "Shigella dysenteriae", isCorrect: false },
                    { text: "Salmonella typhi", isCorrect: false },
                    { text: "Clostridium difficile", isCorrect: false },
                    { text: "Vibrio cholerae", isCorrect: true }
                ],
                solution: "This clinical picture is a classic presentation of **cholera**. The 'rice-water stools,' severe dehydration without fever or abdominal pain, and recent travel to an endemic area (India) are key features. The cholera toxin produced by **Vibrio cholerae** irreversibly activates adenylate cyclase in intestinal cells, leading to massive fluid and electrolyte secretion."
            },
            {
                caseText: "A patient with AIDS, with a CD4+ T-cell count of 50 cells/mm³, presents with headache, fever, and neck stiffness. An India ink stain of the CSF reveals encapsulated budding yeasts. Which opportunistic pathogen is the cause of this presentation?",
                diagnoses: [
                    { text: "Pneumocystis jirovecii", isCorrect: false },
                    { text: "Toxoplasma gondii", isCorrect: false },
                    { text: "Cryptococcus neoformans", isCorrect: true },
                    { text: "Candida albicans", isCorrect: false }
                ],
                solution: "In a patient with advanced AIDS (CD4+ count <100), this presentation of meningoencephalitis with encapsulated budding yeasts on India ink stain of CSF is pathognomonic for **Cryptococcus neoformans**. While other pathogens are common in AIDS, the specific lab finding of encapsulated yeast confirms the diagnosis. "
            },
            {
                caseText: "A 40-year-old male with a history of extensive travel in Africa presents with intermittent fevers, chills, and sweats that occur in a regular cycle (every 48-72 hours). A Giemsa-stained peripheral blood smear reveals intracellular parasites in red blood cells. What is the most likely cause of his symptoms?",
                diagnoses: [
                    { text: "Leishmania donovani", isCorrect: false },
                    { text: "Trypanosoma brucei", isCorrect: false },
                    { text: "Plasmodium falciparum", isCorrect: true },
                    { text: "Toxoplasma gondii", isCorrect: false }
                ],
                solution: "The cyclical fever, chills, and sweats (paroxysms) coupled with travel history to Africa and the presence of intracellular parasites in red blood cells on a blood smear are classic findings for **malaria**. The most virulent species, and a common cause of such a presentation, is **Plasmodium falciparum**."
            },
            {
                caseText: "A 7-year-old child presents with a sandpaper-like rash, fever, and a 'strawberry tongue.' A throat swab culture is pending, but a rapid strep test is positive. Which bacterial species is responsible for this condition?",
                diagnoses: [
                    { text: "Streptococcus pyogenes", isCorrect: true },
                    { text: "Staphylococcus aureus", isCorrect: false },
                    { text: "Corynebacterium diphtheriae", isCorrect: false },
                    { text: "Neisseria meningitidis", isCorrect: false }
                ],
                solution: "The triad of a 'sandpaper rash', fever, and 'strawberry tongue' is the definitive clinical presentation of **Scarlet Fever**, which is caused by a toxin-producing strain of **Streptococcus pyogenes**. The positive rapid strep test confirms this suspicion."
            },
            {
                caseText: "A patient develops severe watery diarrhea and pseudomembranous colitis following a course of clindamycin for a skin infection. The causative agent is an anaerobic, spore-forming, Gram-positive rod. What is the most likely pathogen?",
                diagnoses: [
                    { text: "Enterococcus faecalis", isCorrect: false },
                    { text: "Bacteroides fragilis", isCorrect: false },
                    { text: "Clostridium difficile", isCorrect: true },
                    { text: "Escherichia coli O157:H7", isCorrect: false }
                ],
                solution: "This is a classic case of **Clostridium difficile** infection (C. diff). It is a common nosocomial infection that follows antibiotic use (particularly clindamycin, fluoroquinolones, and cephalosporins), and its spores are notoriously difficult to eliminate. The hallmark findings are severe watery diarrhea and the formation of pseudomembranes in the colon."
            },
            {
                caseText: "A patient with a history of kidney transplantation and immunosuppressive therapy presents with fever, non-productive cough, and ground-glass opacities on a chest CT scan. A bronchoalveolar lavage (BAL) is performed, and PCR confirms the presence of a double-stranded DNA virus. Which virus is the most likely cause?",
                diagnoses: [
                    { text: "Influenza virus", isCorrect: false },
                    { text: "Adenovirus", isCorrect: false },
                    { text: "Cytomegalovirus (CMV)", isCorrect: true },
                    { text: "Respiratory syncytial virus (RSV)", isCorrect: false }
                ],
                solution: "In an immunosuppressed transplant recipient, the constellation of fever, cough, and characteristic 'ground-glass' opacities on chest CT is highly suggestive of **Cytomegalovirus (CMV)** pneumonitis. CMV is a herpesvirus that lies dormant and can reactivate in patients with compromised immune systems, causing severe organ-specific disease."
            },
            {
                caseText: "A patient who works in a sheep-shearing facility develops a painless black eschar on his forearm, surrounded by edema and vesicles. A Gram stain of the vesicular fluid reveals large, Gram-positive rods in chains. What is the most likely pathogen?",
                diagnoses: [
                    { text: "Clostridium perfringens", isCorrect: false },
                    { text: "Bacillus anthracis", isCorrect: true },
                    { text: "Corynebacterium diphtheriae", isCorrect: false },
                    { text: "Listeria monocytogenes", isCorrect: false }
                ],
                solution: "The combination of the patient's occupation (exposure to livestock/animal products), the characteristic painless black eschar (a cutaneous lesion), and the microscopic finding of large, Gram-positive rods in chains is pathognomonic for cutaneous **anthrax**, caused by **Bacillus anthracis**."
            },
            {
                caseText: "A 30-year-old male presents with sudden onset of high fever, headache, and myalgia, with a history of recent exposure to a bat in a cave. CSF analysis shows lymphocytic pleocytosis. The patient rapidly progresses to paralysis and coma. Which virus is the most likely cause?",
                diagnoses: [
                    { text: "Rabies virus", isCorrect: true },
                    { text: "Hantavirus", isCorrect: false },
                    { text: "Ebola virus", isCorrect: false },
                    { text: "West Nile virus", isCorrect: false }
                ],
                solution: "The history of bat exposure, rapid progression from neurologic symptoms to coma, and eventual paralysis is a classic description of a **Rabies virus** infection. Once symptoms appear, rabies is almost universally fatal. The high mortality rate distinguishes it from many other viral diseases."
            },
            {
                caseText: "A patient with a history of diabetes mellitus is admitted with diabetic ketoacidosis (DKA). They subsequently develop a severe sinus infection with black, necrotic tissue in the nasal cavity. A biopsy reveals broad, non-septate hyphae with right-angle branching. What is the most likely diagnosis?",
                diagnoses: [
                    { text: "Aspergillosis", isCorrect: false },
                    { text: "Candidiasis", isCorrect: false },
                    { text: "Cryptococcosis", isCorrect: false },
                    { text: "Mucormycosis", isCorrect: true }
                ],
                solution: "The combination of uncontrolled diabetes (DKA is a predisposing factor) and the presence of invasive rhinocerebral infection with necrotic black tissue is a classic presentation of **mucormycosis**, an aggressive and often fatal fungal infection. The characteristic microscopic finding of broad, non-septate hyphae with right-angle branching confirms the diagnosis."
            }
        ];

        const wrongAnswerMessages = [
            "mnogu si glup",
            "boo prost kako tocak",
            "jas bi se ubila ova ako ne go znaev...",
            "kako ne ti e sram ?",
            "fli go indeksot vo kanta ... sramota kako ova ne go znaes ????!!!!"
        ];

        let shuffledCases;
        let currentCaseIndex = 0;
        let score = 0;
        let wrongAnswersCount = 0;
        let spinning = false;

        // Utility function to shuffle an array (Fisher-Yates algorithm)
        function shuffleArray(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        const startButton = document.getElementById('start-button');
        const introScreen = document.getElementById('intro-screen');
        const gameContainer = document.getElementById('game-container');
        const spinnerContainer = document.getElementById('spinner-container');
        const wheelCanvas = document.getElementById('wheel-canvas');
        const ctx = wheelCanvas.getContext('2d');
        const spinButton = document.getElementById('spin-button');
        const caseTitle = document.getElementById('case-title');
        const caseNumber = document.getElementById('case-number');
        const caseDisplay = document.getElementById('case-display');
        const caseTextElement = document.getElementById('case-text');
        const diagnosisButtonsContainer = document.getElementById('diagnosis-buttons');
        const feedbackSection = document.getElementById('feedback-section');
        const feedbackTextElement = document.getElementById('feedback-text');
        const nextCaseButton = document.getElementById('next-case-button');
        const gameOverContainer = document.getElementById('game-over-container');
        const finalMessageElement = document.getElementById('final-message');
        const restartButton = document.getElementById('restart-button');

        function drawWheel(angle = 0) {
            const centerX = wheelCanvas.width / 2;
            const centerY = wheelCanvas.height / 2;
            const radius = wheelCanvas.width / 2 - 10;
            const colors = ['#f97316', '#ef4444', '#f59e0b', '#22c55e', '#14b8a6', '#06b6d4', '#3b82f6', '#8b5cf6', '#ec4899', '#f43f5e'];
            const segmentAngle = (2 * Math.PI) / cases.length;

            ctx.clearRect(0, 0, wheelCanvas.width, wheelCanvas.height);
            ctx.save();
            ctx.translate(centerX, centerY);
            ctx.rotate(angle);

            for (let i = 0; i < cases.length; i++) {
                ctx.beginPath();
                ctx.moveTo(0, 0);
                ctx.arc(0, 0, radius, i * segmentAngle, (i + 1) * segmentAngle);
                ctx.closePath();
                ctx.fillStyle = colors[i % colors.length];
                ctx.fill();
            }

            ctx.restore();
            ctx.beginPath();
            ctx.arc(centerX, centerY, 60, 0, 2 * Math.PI);
            ctx.fillStyle = '#1f2937';
            ctx.fill();
            ctx.font = 'bold 24px sans-serif';
            ctx.fillStyle = '#d1d5db';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText('SPIN', centerX, centerY);
        }

        function startGame() {
            introScreen.style.display = 'none';
            gameContainer.style.display = 'block';
            shuffledCases = shuffleArray([...cases]);
            currentCaseIndex = 0;
            score = 0;
            wrongAnswersCount = 0;
            showSpinner();
        }

        function showSpinner() {
            spinnerContainer.style.display = 'flex';
            caseDisplay.style.display = 'none';
            feedbackSection.style.display = 'none';
            drawWheel();
        }

        function spinWheelAndShowCase() {
            if (spinning) return;
            spinning = true;
            spinButton.disabled = true;

            let currentAngle = 0;
            const finalAngle = 360 * 5 + Math.random() * 360;
            const duration = 2500;
            const startTime = performance.now();

            function animate(timestamp) {
                const elapsed = timestamp - startTime;
                const progress = Math.min(elapsed / duration, 1);
                const easedProgress = 1 - Math.pow(1 - progress, 3);
                currentAngle = easedProgress * finalAngle;
                drawWheel(currentAngle * Math.PI / 180);

                if (progress < 1) {
                    requestAnimationFrame(animate);
                } else {
                    spinning = false;
                    setTimeout(() => {
                        spinnerContainer.style.display = 'none';
                        caseDisplay.style.display = 'block';
                        showCase();
                    }, 500);
                }
            }
            requestAnimationFrame(animate);
        }

        function showCase() {
            resetState();
            const currentCase = shuffledCases[currentCaseIndex];
            caseNumber.textContent = currentCaseIndex + 1;
            caseTextElement.textContent = currentCase.caseText;
            spinButton.disabled = false;

            currentCase.diagnoses.forEach(diagnosis => {
                const button = document.createElement('button');
                button.textContent = diagnosis.text;
                button.classList.add('diagnosis-button');
                if (diagnosis.isCorrect) {
                    button.dataset.correct = true;
                }
                button.addEventListener('click', selectDiagnosis);
                diagnosisButtonsContainer.appendChild(button);
            });
        }

        function resetState() {
            feedbackSection.style.display = 'none';
            feedbackTextElement.textContent = '';
            nextCaseButton.style.display = 'none';
            while (diagnosisButtonsContainer.firstChild) {
                diagnosisButtonsContainer.removeChild(diagnosisButtonsContainer.firstChild);
            }
        }

        function selectDiagnosis(e) {
            const selectedButton = e.target;
            const isCorrect = selectedButton.dataset.correct === "true";
            
            Array.from(diagnosisButtonsContainer.children).forEach(button => {
                button.disabled = true;
                if (button.dataset.correct === "true") {
                    button.classList.add('correct');
                } else if (button === selectedButton) {
                    button.classList.add('incorrect');
                }
            });
            
            if (isCorrect) {
                score++;
                showFeedback(true);
            } else {
                wrongAnswersCount++;
                showFeedback(false);
            }

            nextCaseButton.style.display = 'block';
        }

        function showFeedback(isCorrect) {
            const currentCase = shuffledCases[currentCaseIndex];
            let feedbackMessage = '';

            if (isCorrect) {
                feedbackMessage = `Correct Answer: ${currentCase.diagnoses.find(d => d.isCorrect).text}\n\n${currentCase.solution}`;
            } else {
                const messageIndex = Math.min(wrongAnswersCount - 1, wrongAnswerMessages.length - 1);
                feedbackMessage = wrongAnswerMessages[messageIndex] + "\n\n" + currentCase.solution;
            }

            feedbackTextElement.textContent = feedbackMessage;
            feedbackSection.style.display = 'flex';
        }

        function goToNextCase() {
            currentCaseIndex++;
            if (currentCaseIndex < shuffledCases.length) {
                showSpinner();
            } else {
                endGame();
            }
        }
        
        function endGame() {
            gameContainer.style.display = 'none';
            gameOverContainer.style.display = 'block';

            finalMessageElement.textContent = `You finished all 15 cases! You got ${score} out of ${shuffledCases.length} correct.`;
        }

        function restartGame() {
            gameOverContainer.style.display = 'none';
            startGame();
        }

        window.onload = function() {
            drawWheel();
        };

        startButton.addEventListener('click', startGame);
        spinButton.addEventListener('click', spinWheelAndShowCase);
        nextCaseButton.addEventListener('click', goToNextCase);
        restartButton.addEventListener('click', restartGame);
    </script>
</body>
</html>
