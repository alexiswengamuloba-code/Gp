<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Alexis-Officiel AI</title>
    <style>
        :root { 
            --primary: #075E54;
            --rose: #FFD1D1; /* Salutations / VIP */
            --vert: #D1FFD1; /* Client */
            --jaune: #FFFDD1; /* Objectifs / Chef */
            --violet: #E1D1FF; /* Liens / Projets */
            --dark: #1a1a2e;
        }

        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: #2c3e50; margin: 0; display: flex; justify-content: center; height: 100vh; overflow: hidden; }
        #chat-container { width: 100%; max-width: 500px; height: 100%; background: var(--dark); display: flex; flex-direction: column; position: relative; box-shadow: 0 0 40px rgba(0,0,0,0.6); }

        /* HEADER AGRANDI ET ATTRayant */
        #header { 
            background: linear-gradient(135deg, #054d44, #075E54); 
            color: white; 
            padding: 25px 15px; 
            text-align: center; 
            border-bottom: 3px solid #FFD700;
        }
        #header h1 { 
            margin: 0; 
            font-size: 2.4em; 
            font-weight: 900; 
            color: #FFD700; 
            text-shadow: 3px 3px 6px rgba(0,0,0,0.5);
            text-transform: uppercase;
        }
        #header p { margin: 8px 0 0; font-size: 0.9em; font-weight: bold; opacity: 0.9; }

        /* ZONE DE CHAT */
        #chat-box { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 14px; background: #0f3460; }
        
        .msg { 
            padding: 15px; 
            border-radius: 20px; 
            font-size: 14.5px; 
            max-width: 85%; 
            line-height: 1.6; 
            cursor: pointer; 
            transition: 0.3s; 
            color: #1a1a2e; 
            animation: pop 0.3s ease;
            position: relative;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }

        .bot-salut { background: var(--rose); align-self: flex-start; border-bottom-left-radius: 0; }
        .user-msg { background: var(--vert); align-self: flex-end; border-bottom-right-radius: 0; }
        .info-obj { background: var(--jaune); align-self: flex-start; border-left: 5px solid #fbc02d; }
        .info-link { background: var(--violet); align-self: flex-start; border-left: 5px solid #7b1fa2; }

        .selected { transform: scale(1.04); border: 2px solid #FFD700 !important; z-index: 10; }

        #typing { color: #FFD700; font-size: 12px; margin: 5px 15px; display: none; font-style: italic; }

        /* BARRE DE SAISIE */
        #input-area { padding: 15px; background: #16213e; display: flex; gap: 10px; align-items: center; border-top: 1px solid #2a3a5a; }
        input { flex: 1; border: none; padding: 14px 20px; border-radius: 35px; outline: none; font-size: 15px; }
        
        .action-btn { width: 50px; height: 50px; border-radius: 50%; border: none; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: 0.2s; }
        .send-btn { background: var(--primary); color: white; font-size: 22px; }
        .del-btn { background: #ff4d4d; color: white; display: none; font-size: 18px; }

        .btn-action { display: block; background: #075E54; color: white; text-align: center; padding: 12px; border-radius: 12px; text-decoration: none; font-weight: bold; margin-top: 10px; font-size: 13px; border: 1px solid #FFD700; }

        @keyframes pop { from { opacity: 0; transform: scale(0.85); } to { opacity: 1; transform: scale(1); } }
    </style>
</head>
<body>

<div id="chat-container">
    <div id="header">
        <h1>Alexis-Officiel AI</h1>
        <p>Expert en Programmation & Solutions Numériques</p>
    </div>

    <div id="chat-box">
        <div class="msg bot-salut" onclick="toggleSelect(this)">
            Bonjour ! Bienvenue sur l'espace d'<b>Alexis-Officiel</b>. 👋<br><br>
            Je suis votre assistant personnel. Je suis ici pour vous présenter nos projets, nos objectifs et vous mettre en relation avec le Chef Alexis. En quoi puis-je vous aider ?
        </div>
        <div id="typing">Alexis est en train d'écrire...</div>
    </div>

    <div id="input-area">
        <button id="del-btn" class="action-btn del-btn" onclick="deleteMsg()">🗑️</button>
        <input type="text" id="user-input" placeholder="Posez votre question ici...">
        <button class="action-btn send-btn" onclick="handleChat()">➤</button>
    </div>
</div>

<script>
    const chatBox = document.getElementById('chat-box');
    const userInput = document.getElementById('user-input');
    const typing = document.getElementById('typing');
    const delBtn = document.getElementById('del-btn');
    let selectedMsg = null;

    function toggleSelect(el) {
        if(selectedMsg) selectedMsg.classList.remove('selected');
        selectedMsg = el;
        el.classList.add('selected');
        delBtn.style.display = 'flex';
    }

    function deleteMsg() {
        if(selectedMsg) {
            selectedMsg.remove();
            selectedMsg = null;
            delBtn.style.display = 'none';
        }
    }

    function addMessage(text, type) {
        const div = document.createElement('div');
        div.className = `msg ${type}`;
        div.innerHTML = text;
        div.onclick = () => toggleSelect(div);
        chatBox.insertBefore(div, typing);
        chatBox.scrollTop = chatBox.scrollHeight;
    }

    function handleChat() {
        const prompt = userInput.value.trim();
        if(!prompt) return;

        addMessage(prompt, 'user-msg');
        userInput.value = '';
        delBtn.style.display = 'none';
        typing.style.display = 'block';

        setTimeout(() => {
            typing.style.display = 'none';
            const val = prompt.toLowerCase();
            let res = "";
            let type = "bot-salut";

            // 1. GESTION DES PRÉNOMS VIP
            const VIP = ["anselme", "arthur", "annette", "justin"];
            const foundVIP = VIP.find(name => val.includes(name));
            if(foundVIP) {
                res = `🌟 Bienvenue chaleureusement, <b>${foundVIP.toUpperCase()}</b> ! Vous êtes une personne vraiment exceptionnelle par rapport aux autres pour la chaîne Alexis-Officiel.`;
            }
            
            // 2. OBJECTIF DE LA CHAÎNE
            else if(val.includes("objectif") || val.includes("pourquoi") || val.includes("but")) {
                res = `📣 <b>Objectif de notre chaîne :</b><br>Notre chaîne a pour objectif principal de partager des informations utiles, éducatives et inspirantes dans plusieurs domaines. Nous utilisons différentes plateformes afin de toucher un large public et de répondre aux besoins de chacun. 💵<br><br>Nous offrons un contenu de qualité qui aide à apprendre, découvrir et évoluer au quotidien. 💼`;
                type = "info-obj";
            }

            // 3. LE CHEF ALEXIS (ISC / BUKAVU / MAGALI)
            else if(val.includes("chef") || val.includes("alexis") || val.includes("responsable") || val.includes("vit")) {
                res = `Le chef de notre chaîne est <b>Alexis</b>. Il vit à <b>Bukavu</b>, précisément à <b>Magali</b>. Il étudie à l'<b>Institut Supérieur de Commerce de Bukavu (ISC)</b>. C'est lui qui crée les plateformes et publie les contenus (vidéos, images, textes) garantis <b>sans virus</b>. 🛡️`;
                type = "info-obj";
            }

            // 4. CONTACT / PROJET / INTÉRÉSSÉ
            else if(val.includes("intéressé") || val.includes("contact") || val.includes("projet") || val.includes("rencontrer")) {
                res = `Si notre contenu vous intéresse ou si vous avez un projet, n’hésitez pas à nous contacter via WhatsApp. Nous ferons de notre mieux pour vous accompagner et collaborer avec vous, car votre confiance est au cœur de notre engagement. 🤝<br><a href="https://wa.me/243990909080" class="btn-action">CONTACTER LE CHEF ALEXIS</a>`;
                type = "info-link";
            }

            // 5. LIEN DE LA CHAÎNE
            else if(val.includes("lien") || val.includes("rejoindre") || val.includes("canal")) {
                res = `Rejoignez notre communauté officielle pour suivre toutes nos activités :<br><a href="https://whatsapp.com/channel/0029Vb7heZb5a23yw3hT5X27" class="btn-action">ACCÉDER À LA CHAÎNE</a>`;
                type = "info-link";
            }

            // 6. POLITESSE
            else if(val.includes("merci") || val.includes("gratitude")) {
                res = "Je vous en prie ! C'est un réel plaisir de vous aider. Avez-vous une autre question ?";
            }
            else if(val.includes("bonjour") || val.includes("salut") || val.includes("bonsoir")) {
                res = "Bonjour ! Comment puis-je vous assister aujourd'hui concernant les services d'Alexis-Officiel ?";
            }
            else {
                res = "Je suis l'IA d'Alexis. Je peux vous parler de son parcours à l'ISC, de nos objectifs de partage de connaissances, ou vous donner le lien de notre chaîne.";
                type = "info-obj";
            }

            addMessage(res, type);
        }, 1200);
    }

    userInput.addEventListener("keypress", (e) => { if(e.key === "Enter") handleChat(); });
</script>

</body>
</html>
