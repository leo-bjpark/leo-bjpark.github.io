---
layout: about
title: 🇰🇷 About Me
permalink: /
subtitle: Bumjin Park 
order : 1 
profile:
  align: right
  image: me.png
  address: >
    <p text-align:center;> AI Researcher </p>

news: false  # includes a list of news items
selected_papers: false # includes a list of papers marked as "selected={true}"
social: false  # includes social icons at the bottom of the page
_styles: >

---
<div class="profile-container">
  <div>
    <img src="/assets/img/bumjini2.png" width="40%" height="auto" class="styled-image"/>
    <p>
      <strong style="padding-top:30px;">(Leo) Bumjin Park</strong>
    </p>
  </div>
  <div class="intro-text">
    <p>
      I study how AI systems maintain, revise, and reason over knowledge by analyzing their
      internal representations — the computational substrate where meaning, conflict, and
      decision-making emerge.
    </p>

    <p>
      My research centers on <strong>Non-Monotonic Reasoning</strong>, <strong>Interpretability</strong>,
      and <strong>Symbolic–Neural Architectures</strong>, with a particular focus on how models handle
      <strong>knowledge conflict, updates, and retraction</strong> in dynamic environments.
    </p>

    <p>
      By grounding learning-based models in explicit reasoning structures, I aim to design
      <strong>stable and aligned reasoning architectures</strong> that bridge human cognitive principles
      and machine intelligence — moving toward a more reliable and interpretable form of
      <strong class="shimmer">general intelligence</strong>.
    </p>
  </div>
</div>


<style>
.shimmer {
  display: inline-block;
  background: linear-gradient(270deg, #000, #A0A, #000);
  background-size: 200% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: shimmer 2.5s infinite linear;
  font-weight: 700;
}

@keyframes shimmer {
  0% { background-position: 200% 0; }
  100% { background-position: -200% 0; }
}

.profile-container {
    display: flex;
    align-items: center;
    gap: 30px;
    margin: 20px 0;
}

.styled-image {
    width: 200px;
    height: 240px;
    border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    transition: transform 0.3s ease;
    flex-shrink: 0;
    object-fit: cover;
}

.intro-text {
    flex: 1;
    font-size: 1.1rem;
    line-height: 1.6;
}

@media (max-width: 768px) {
    .profile-container {
        flex-direction: column;
        text-align: center;
    }
    
    .styled-image {
        width: 150px;
        height: 200px;
    }
}

.field {
    padding: 2px 6px;
    border-radius: 4px;
    font-weight: bold;
}

.cognitive { background-color: #FFE0E0; }
.mechanistic { background-color: #E0FFE0; }
.xai { background-color: #E0E0FF; }
.llm { background-color: #FFE0FF; }
.multiagent { background-color: #FFFFE0; }
.communication { background-color: #E0FFFF; }
.math { background-color: #FFE5CC; }
.programming { background-color: #E5CCFF; }

</style>


---

## News

- **October 1, 2025** — Started my three-month stay as a Visiting Scholar at **NYU**!  
  If you’re interested in discussing research ideas, feel free to reach out.

- **September 15, 2025** — My paper on **LLM Jailbreak** has been accepted at **CKIM 2025 [HCAI Workshop](https://xai.kaist.ac.kr/Workshop/hcai2025/#call)**! Excited to share our findings on trustworthy and safe AI.

---

## Education

#### Ph.D. Student in Artificial Intelligence (09/2023 – Present)
- Korea Advanced Institute of Science and Technology (**KAIST**), AI Graduate School
- Topic: Integrating Cognitive Architectures into Large Language Models [📂 Drive](https://1drv.ms/b/c/ae042a624064f8ca/EXbZ7D5yKjdOonSmwpe_60IBYzLog03lXGdDhi6Fy6WUhg?e=z6FWzK)

#### M.S. in Artificial Intelligence (08/2023)
- Korea Advanced Institute of Science and Technology (**KAIST**), AI Graduate School
- Thesis: Partitioned Channel Gradient for Reliable Saliency Map in Image Classification [📂 Drive](https://1drv.ms/b/c/ae042a624064f8ca/EWrkp660zT1BuTF8JjPcSa4B6IWTS5NT6V_URVY-WOKzgg?e=Y4GPkz)

#### B.S. in Mathematics (08/2020)
- Chung-Ang University, Korea
- Double Major in Software Engineering

<!-- ## 📄 Curriculum Vitae -->

--- 
<a href="assets/cv.pdf">
  🗂️ CV(October 16, 2025)
</a> |  📧 **Email:** [bumjin@kaist.ac.kr](mailto:bumjin@kaist.ac.kr) | [bumjini42@gmail.com](mailto:bumjini42@gmail.com)
<br>



<br>
<br>

<div class="research-pyramid">
  <div class="pyramid-level level-fire">
    <img src="/assets/img/fire.png" alt="Fire" class="fire-image" />
    <div class="level-content">
      <h4>Research Question</h4>
      <p>How can we design an architecture that enables stable knowledge maintenance, including conflict resolution, updating, and retraction?</p>
    </div>
  </div>
  
  <div class="pyramid-level level-wood level-3">
    <img src="/assets/img/wood1.png" alt="Wood Log 1" class="wood-image" />
    <div class="level-content">
      <h4>Non-Monotonic Reasoning (NMR)</h4>
    </div>
  </div>
  
  <div class="pyramid-level level-wood level-2">
    <img src="/assets/img/wood2.png" alt="Wood Log 2" class="wood-image" />
    <div class="level-content">
      <h4>Interpretability & Symbolic</h4>
    </div>
  </div>
  <div class="pyramid-level level-wood level-1">
    <img src="/assets/img/wood4.png" alt="Wood Log 3" class="wood-image" />
    <div class="level-content">
      <h4>Large Scale Knowledge Conflict</h4>
    </div>
  </div>
  
  <div class="pyramid-level level-wood level-0">
    <img src="/assets/img/wood4.png" alt="Wood Log 4" class="wood-image" />
    <div class="level-content">
      <h4>AI and Human Alignment</h4>
    </div>
  </div>
</div>

<style>
.research-pyramid {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 50px 0;
  gap: 0;
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
}

.pyramid-level {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0;
  transition: all 0.3s ease;
  position: relative;
  overflow: visible;
}


/* Fire Level */
.level-fire {
  width: 70%;
  background: transparent;
  color: white;
  border: none;
  padding: 0;
  z-index: 10;
  position: relative;
  overflow: visible;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom:-50px;
}

.fire-image {
  width: 50%;
  height: auto;
  display: block;
  filter: 
    drop-shadow(0 0 25px rgba(255, 165, 0, 0.8))
    drop-shadow(0 0 50px rgba(255, 100, 0, 0.6))
    drop-shadow(0 0 75px rgba(255, 50, 0, 0.4));
  animation: fire-glow 1.5s ease-in-out infinite;
  z-index: 1;
  position: relative;
}

.level-fire::before {
  content: '';
  position: absolute;
  top: 20%;
  left: 50%;
  transform: translateX(-50%);
  width: 60%;
  height: 60%;
  background: radial-gradient(ellipse at center, rgba(255, 200, 0, 0.3) 0%, transparent 70%);
  animation: fire-aura 2s ease-in-out infinite;
  pointer-events: none;
  z-index: 0;
}

@keyframes fire-glow {
  0%, 100% {
    filter: 
      drop-shadow(0 0 25px rgba(255, 165, 0, 0.8))
      drop-shadow(0 0 50px rgba(255, 100, 0, 0.6))
      drop-shadow(0 0 75px rgba(255, 50, 0, 0.4))
      brightness(1);
    transform: scale(1);
  }
  33% {
    filter: 
      drop-shadow(0 0 35px rgba(255, 200, 0, 1))
      drop-shadow(0 0 60px rgba(255, 120, 0, 0.8))
      drop-shadow(0 0 90px rgba(255, 60, 0, 0.6))
      brightness(1.15);
    transform: scale(1.02);
  }
  66% {
    filter: 
      drop-shadow(0 0 30px rgba(255, 150, 0, 0.9))
      drop-shadow(0 0 55px rgba(255, 90, 0, 0.7))
      drop-shadow(0 0 80px rgba(255, 40, 0, 0.5))
      brightness(1.05);
    transform: scale(0.98);
  }
}

@keyframes fire-aura {
  0%, 100% {
    opacity: 0.5;
    transform: scale(1);
  }
  50% {
    opacity: 0.8;
    transform: scale(1.1);
  }
}

/* Wood Levels */
.level-wood {
  border: none;
  box-shadow: none;
  position: relative;
  background: transparent;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow: visible;
  padding: 0;
}

.wood-image {
  width: 100%;
  height: auto;
  display: block;
  filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
}

.level-3 {
  width: 50%;
  margin-top: 0px;
  z-index: 9;
  transform: translateY(-20%);
}

.level-2 {
  width: 60%;
  margin-top: -40px;
  z-index: 8;
  transform: translateY(-30%);
}

.level-1 {
  width: 75%;
  margin-top: -40px;
  z-index: 7;
  transform: translateY(-40%);
}

.level-0 {
  width: 90%;
  margin-top: -40px;
  z-index: 6;
  transform: translateY(-50%);
}

.level-content {
  text-align: center;
  width: 100%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 2;
  padding: 20px;
  pointer-events: none;
}

.level-fire .level-content {
  background: transparent;
  backdrop-filter: none;
  border-radius: 0;
  max-width: 85%;
  top: 50%;
  transform: translate(-50%, -50%);
  padding: 15px;
}
.level-wood .level-content {
  background: transparent;
  backdrop-filter: none;
  border-radius: 0;
  max-width: 85%;
  top: 55%;
  transform: translate(-50%, -35%);
}

.level-content h4 {
  margin: 0 0 8px 0;
  font-size: 1.05em;
  font-weight: 600;
}

.level-fire .level-content h4 {
  color: #FFFFFF;
  font-size: 1.5em;
  text-shadow: 
    0 0 10px rgba(0, 0, 0, 0.9),
    0 0 20px rgba(0, 0, 0, 0.8),
    0 0 30px rgba(0, 0, 0, 0.7),
    2px 2px 4px rgba(0, 0, 0, 0.9),
    -2px -2px 4px rgba(0, 0, 0, 0.9),
    0 0 15px rgba(255, 255, 255, 0.5);
  font-weight: 700;
}

.level-wood .level-content h4 {
  color: #fff5e6;
  text-shadow: 
    2px 2px 6px rgba(0, 0, 0, 0.9),
    -2px -2px 6px rgba(0, 0, 0, 0.9),
    0 0 10px rgba(0, 0, 0, 0.7);
}

.level-content p {
  margin: 0;
  font-size: 0.9em;
  line-height: 1.6;
  color: rgba(255, 255, 255, 0.95);
  font-weight: 400;
  text-shadow: 
    2px 2px 6px rgba(0, 0, 0, 0.9),
    -2px -2px 6px rgba(0, 0, 0, 0.9),
    0 0 10px rgba(0, 0, 0, 0.7);
}

.level-fire .level-content p {
  color: #FFFFFF;
  font-size: 1.1em;
  text-shadow: 
    0 0 8px rgba(0, 0, 0, 0.9),
    0 0 15px rgba(0, 0, 0, 0.8),
    0 0 25px rgba(0, 0, 0, 0.7),
    1px 1px 3px rgba(0, 0, 0, 0.9),
    -1px -1px 3px rgba(0, 0, 0, 0.9),
    0 0 12px rgba(255, 255, 255, 0.4);
  font-weight: 500;
  line-height: 1.7;
}

@media (max-width: 768px) {
  .research-pyramid {
    margin: 30px 10px;
    max-width: 100%;
  }
  
  .level-fire {
    width: 70%;
    margin-bottom: -40px;
  }
  
  .fire-image {
    width: 50%;
  }
  
  .level-3 {
    width: 50%;
    margin-top: 0px;
    transform: translateY(-15%);
  }
  
  .level-2 {
    width: 60%;
    margin-top: -30px;
    transform: translateY(-25%);
  }
  
  .level-1 {
    width: 75%;
    margin-top: -30px;
    transform: translateY(-35%);
  }
  
  .level-0 {
    width: 90%;
    margin-top: -30px;
    transform: translateY(-45%);
  }
  
  .level-content {
    padding: 10px 8px;
  }
  
  .level-fire .level-content {
    max-width: 90%;
    top: 50%;
    transform: translate(-50%, -50%);
    padding: 10px 8px;
  }
  
  .level-wood .level-content {
    max-width: 90%;
    top: 55%;
    transform: translate(-50%, -35%);
  }
  
  .level-content h4 {
    font-size: 0.9em;
    margin: 0 0 4px 0;
  }
  
  .level-fire .level-content h4 {
    font-size: 1.2em;
    margin: 0 0 6px 0;
  }
  
  .level-content p {
    font-size: 0.8em;
    line-height: 1.4;
  }
  
  .level-fire .level-content p {
    font-size: 0.95em;
    line-height: 1.5;
  }
}

@media (max-width: 480px) {
  .research-pyramid {
    margin: 20px 5px;
  }
  
  .level-fire {
    width: 75%;
    margin-bottom: -35px;
  }
  
  .fire-image {
    width: 55%;
  }
  
  .level-3 {
    width: 55%;
    transform: translateY(-12%);
  }
  
  .level-2 {
    width: 65%;
    margin-top: -25px;
    transform: translateY(-22%);
  }
  
  .level-1 {
    width: 80%;
    margin-top: -25px;
    transform: translateY(-32%);
  }
  
  .level-0 {
    width: 95%;
    margin-top: -25px;
    transform: translateY(-42%);
  }
  
  .level-content {
    padding: 8px 6px;
  }
  
  .level-fire .level-content {
    padding: 8px 6px;
  }
  
  .level-content h4 {
    font-size: 0.85em;
  }
  
  .level-fire .level-content h4 {
    font-size: 1.1em;
  }
  
  .level-content p {
    font-size: 0.75em;
  }
  
  .level-fire .level-content p {
    font-size: 0.9em;
  }
}
</style>
