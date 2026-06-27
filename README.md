# ◆ CardDex — Pokédex Digital de Colecionador

> Sistema completo de gerenciamento de coleção de cartas com Scanner IA (Gemini), Firebase e PWA.

---

## 🚀 Início Rápido

Este projeto é um **single-file HTML** (sem build, sem npm). Basta hospedar o `index.html` e abrir no navegador.

### Opção 1 — Firebase Hosting (Recomendado)

```bash
# 1. Instale o Firebase CLI
npm install -g firebase-tools

# 2. Login
firebase login

# 3. Inicialize no diretório do projeto
firebase init hosting

# 4. Deploy
firebase deploy --only hosting
```

### Opção 2 — Servidor local simples

```bash
# Python
python3 -m http.server 3000

# Node.js
npx serve .

# VSCode: extensão Live Server
```

---

## 🔑 Variáveis de Ambiente

O projeto usa as chaves diretamente no `index.html`. Para produção real, considere um proxy backend.

### Firebase (`index.html`, linha ~450)
```js
const firebaseConfig = {
  apiKey: "AIzaSyA8pCxi_DbzMh3nKZP7x2uzul2YMXLLv9k",
  authDomain: "carddex-br.firebaseapp.com",
  projectId: "carddex-br",
  storageBucket: "carddex-br.firebasestorage.app",
  messagingSenderId: "958568553030",
  appId: "1:958568553030:web:f13c336ab773db41698847"
};
```

### Gemini API (`index.html`, linha ~453)
```js
const GEMINI_KEY = "SUA_CHAVE_GEMINI_AQUI";
```

---

## 🔥 Firebase — Configuração

### 1. Autenticação
No console Firebase → **Authentication** → **Sign-in method**:
- ✅ Email/Password
- ✅ Google (opcional)
- ✅ Phone (opcional)

### 2. Firestore — Criar as coleções

As coleções são criadas automaticamente pelo app. Estrutura:

```
/usuarios/{uid}
  - uid, name, phone, email, createdAt, updatedAt

/colecao/{docId}
  - uid, nome, numero, colecao, idioma, hp, tipo
  - categoria, raridade, versao, subtipo, quantidade
  - condicao, precoPago, precoVenda, precoMinimo
  - observacoes, favorita, aVenda, imagemUrl
  - historico[], createdAt, updatedAt

/wishlist/{docId}
  - uid, nome, colecao, prioridade, precoEsperado
  - observacoes, createdAt, updatedAt

/vendas/{docId}
  - uid, cardId, nome, raridade, precoAnunciado
  - precoVendido, condicao, quantidade, status
  - imagemUrl, createdAt, updatedAt
```

### 3. Regras de Segurança Firestore
Copie o conteúdo de `firestore.rules` para:
Firebase Console → Firestore → **Rules**

### 4. Storage — Configurar CORS
```json
[{
  "origin": ["*"],
  "method": ["GET", "POST", "PUT", "DELETE"],
  "maxAgeSeconds": 3600
}]
```

---

## 🤖 Gemini AI — Configuração

1. Acesse [aistudio.google.com](https://aistudio.google.com)
2. Crie uma API Key
3. Cole no `index.html` na constante `GEMINI_KEY`

O sistema usa o modelo **gemini-1.5-flash** para:
- Identificar nome, número, coleção, raridade, tipo
- Detectar idioma (PT, EN, JP, ES)
- Calcular índice de confiança (0-100%)
- Reconhecer cartas inclinadas, com reflexo, parcialmente visíveis

---

## 📁 Estrutura de Arquivos

```
carddex/
├── index.html          ← Aplicação completa (single-file)
├── manifest.json       ← PWA manifest
├── sw.js               ← Service Worker (offline)
├── firestore.rules     ← Regras de segurança Firebase
├── README.md           ← Este arquivo
├── icon-192.png        ← Ícone PWA (adicionar)
└── icon-512.png        ← Ícone PWA (adicionar)
```

---

## ✨ Funcionalidades

### 🔐 Autenticação
- Login com telefone + senha
- Cadastro com nome, telefone, email
- Google Sign-In
- Recuperação de senha
- Sessão persistente por usuário

### 📊 Dashboard
- Total de cartas, únicas, repetidas
- Valor estimado da coleção
- Gráficos de raridade, tipo, coleção
- Últimas cartas adicionadas

### 📷 Scanner IA
- Upload de imagem (drag & drop)
- Câmera ao vivo
- URL de imagem
- Análise com Gemini 1.5 Flash
- Índice de confiança visual
- Confirmação manual se < 90%
- Auto-cadastro se ≥ 90%
- Detecção de duplicatas (incrementa quantidade)

### 🃏 Minha Coleção
- Grid visual com efeito holográfico
- Filtros: nome, raridade, tipo, idioma
- Chips: favoritas, à venda, repetidas
- Ações: favoritar, marcar à venda, +/- quantidade
- Histórico de ações por carta
- Página de detalhes completa
- Adicionar manualmente + upload de foto

### ⭐ Wishlist
- Prioridade (alta, média, baixa)
- Preço esperado
- Remoção automática ao cadastrar a carta
- Botão "Encontrei!" → abre scanner

### 💰 À Venda
- Listagem de cartas marcadas à venda
- Status: disponível, reservada, vendida
- Cálculo de lucro
- Resumo de valor anunciado

### 📈 Relatórios
- Valor investido vs. estimado
- Lucro/prejuízo
- Gráficos por raridade e coleção
- Top 5 mais valiosas e mais repetidas
- Exportação CSV e JSON

### ⚙️ Configurações
- Exportar/importar coleção
- Preferências de idioma e moeda
- Informações do sistema

---

## 🎨 Raridades e Animações

| Raridade | Cor | Efeito |
|----------|-----|--------|
| Common | Cinza | — |
| Uncommon | Verde | — |
| Rare | Azul | — |
| Double Rare | Roxo | — |
| Illustration Rare | Laranja | — |
| Special Illustration Rare | Vermelho | Gradiente animado |
| Hyper Rare / Gold | Dourado | Brilho metálico pulsante |
| Rainbow | Multicolor | Shift de matiz contínuo |
| Promo | Azul claro | — |
| Ace Spec | Magenta | — |

---

## 📱 PWA — Instalação

Para habilitar o Service Worker e instalação como app:

1. Adicione em `index.html` antes de `</body>`:
```html
<script>
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js');
}
</script>
```

2. Crie os ícones `icon-192.png` e `icon-512.png` na raiz

3. Acesse via HTTPS (Firebase Hosting já oferece isso)

---

## 🔒 Segurança

- Cada usuário só acessa seus próprios documentos (Firestore Rules)
- Upload limitado a imagens < 5MB
- Sem compartilhamento de dados entre usuários
- Autenticação obrigatória para todas as operações

---

## 🗺️ Roadmap Futuro

- [ ] Marketplace entre usuários
- [ ] Preço médio de mercado (TCGPlayer API)
- [ ] Avaliação PSA
- [ ] Scanner de múltiplas cartas
- [ ] Notificações push
- [ ] Sistema de conquistas
- [ ] Perfil público
- [ ] App iOS/Android (Capacitor)
- [ ] Modo torneio

---

## 📄 Scripts Úteis

```bash
# Deploy Firebase Hosting
firebase deploy --only hosting

# Deploy Firestore Rules
firebase deploy --only firestore:rules

# Deploy Storage Rules  
firebase deploy --only storage

# Deploy tudo
firebase deploy

# Emulador local
firebase emulators:start
```

---

**CardDex v1.0.0** — Desenvolvido com Firebase + Gemini AI
