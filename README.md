
# Predict and Win: Building a Prediction Market App on GenLayer

I built **Predict and Win** — a clean, modern prediction market demo running on GenLayer.  

The frontend was "vibecoded" in minutes using [Lovable.dev](https://lovable.dev) — just described what I wanted!


Right now you can:

- Browse fun markets (e.g. "Will GenLayer mainnet launch in 2026?" — currently ~78.5% YES)
- See live odds
- Place mock votes
- View AI-powered reasoning


Real betting is still demo-only (needs full Asimov testnet support + GEN tokens from the [Caldera faucet](https://genlayer-testnet.hub.caldera.xyz)).


Try the live demo: [https://predictandwin.lovable.app](https://predictandwin.lovable.app) (link from original post)

This guide shows how **you** can replicate or extend a similar app.


## What is GenLayer? (Quick Recap)

GenLayer = AI-native blockchain for subjective decisions on-chain.  
Validators use diverse LLMs → reach consensus without oracles/courts.  
→ Internet access in contracts  
→ Python-based Intelligent Contracts  
→ Perfect for prediction markets, DAOs, subjective escrow, etc.


Official site → https://www.genlayer.com  
Docs → https://docs.genlayer.com

## Prerequisites

- Node.js 18+  
- Python 3.8+  
- [GenLayer Studio/Simulator](https://github.com/genlayerlabs/genlayer-studio) running locally  
- Git

## Steps attached below:

**step 1 Start with the Official Boilerplate**

The best starting point is GenLayer's own project boilerplate (includes football prediction market example):

```bash
git clone https://github.com/genlayerlabs/genlayer-project-boilerplate
cd genlayer-project-boilerplate

#Then,

cp .env.example .env
pip install -r requirements.txt
# For frontend:
cd app
npm install

###Step 2: Create/Adapt an Intelligent Contract

```bash

from genlayer import Contract as gl
import typing, json

class GeneralPredictionMarket(gl.Contract):
    resolved: bool = False
    question: str
    resolution_date: str
    source_url: str
    outcome: str = ""

    def __init__(self, question: str, resolution_date: str, source_url: str):
        self.question = question
        self.resolution_date = resolution_date
        self.source_url = source_url

    @gl.public.write
    def resolve(self) -> typing.Any:
        if self.resolved:
            raise Exception("Already resolved")

        content = gl.get_webpage(self.source_url)

        prompt = f"""
        Event: {self.question}
        Date: on/after {self.resolution_date}
        Analyze webpage: {content[:4000]}...
        Return JSON: {{"outcome": "Yes"|"No", "reason": "short explanation"}}
        """

        raw = gl.exec_prompt(prompt)
        parsed = json.loads(raw)
        validated = gl.eq_principle_strict_eq(parsed["outcome"], ["Yes", "No"])

        if validated:
            self.outcome = parsed["outcome"]
            self.resolved = True
            return parsed
        raise Exception("Consensus failed")

###Step 3: Build Frontend Fast with Lovable.devGo to https://lovable.dev  

New project → chat with AI:
"Create dark-themed prediction market dashboard: cards with question, Yes/No percentages, volume, mock vote buttons, search bar, create market button. Integrate GenLayer data."
Export code → drop into /app folder of boilerplate  
Connect via GenLayerJS SDK (in boilerplate)

###Step 4: Test & RunRun simulator  

npm run dev in /app  
Deploy contract, update .env with address  
Test mock votes + resolution

Next Steps & ResourcesGet testnet GEN → https://genlayer-testnet.hub.caldera.xyz  
Official prediction example → https://docs.genlayer.com/developers/intelligent-contracts/examples/prediction  
Join GenLayer community for feedback  
Fork this repo/discussion and improve!




