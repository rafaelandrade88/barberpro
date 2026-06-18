# BarberPro — Sistema de Agendamentos v1.0.0

PWA single-file para barbearia com Firebase Auth (telefone), Firestore e Cloudinary.

---

## 🗂 Estrutura

```
barberapp/
├── index.html              ← App completo (HTML + CSS + JS)
├── manifest.json           ← PWA manifest
├── firestore.rules         ← Regras de segurança do Firestore
├── firestore.indexes.json  ← Índices compostos do Firestore
└── README.md
```

---

## 🔧 Configuração em 5 passos

### 1. Firebase — Criar Projeto

1. Acesse https://console.firebase.google.com
2. Crie um novo projeto (ex: `barberpro-app`)
3. Ative o plano **Spark** (gratuito) ou **Blaze** (necessário para SMS em produção)

### 2. Firebase Auth — Ativar Telefone

1. No console Firebase → **Authentication** → **Sign-in method**
2. Habilite **Phone** (Telefone)
3. Para testes locais, adicione números de teste em "Phone numbers for testing"

### 3. Firebase Firestore — Criar banco

1. No console → **Firestore Database** → **Create database**
2. Escolha modo de **produção**
3. Selecione a região mais próxima (ex: `southamerica-east1` para Brasil)

### 4. Deploy das Regras e Índices

```bash
# Instale o Firebase CLI
npm install -g firebase-tools

# Login
firebase login

# Init no diretório do projeto
firebase init

# Deploy das regras
firebase deploy --only firestore:rules

# Deploy dos índices
firebase deploy --only firestore:indexes
```

### 5. Configurar o index.html

Substitua as variáveis de configuração no `index.html`:

```javascript
// ~linha 480 do index.html
const FIREBASE_CONFIG = {
  apiKey:            "SUA_API_KEY",          // Firebase Console → Projeto → Configurações
  authDomain:        "SEU_PROJECT.firebaseapp.com",
  projectId:         "SEU_PROJECT_ID",
  storageBucket:     "SEU_PROJECT.appspot.com",
  messagingSenderId: "SEU_SENDER_ID",
  appId:             "SEU_APP_ID"
};

const CLOUDINARY_CONFIG = {
  cloudName:    "SEU_CLOUD_NAME",            // https://cloudinary.com/console
  uploadPreset: "SEU_UPLOAD_PRESET_UNSIGNED" // Settings → Upload → Upload presets → Unsigned
};

// Telefone(s) do admin/barbeiro — formato internacional
const ADMIN_PHONES = ["+5511999999999"];
```

---

## ☁️ Cloudinary — Configurar Upload

1. Crie conta em https://cloudinary.com (plano gratuito tem 25GB)
2. No Dashboard → **Settings** → **Upload** → **Upload presets**
3. Clique em **Add upload preset**
4. Configure:
   - **Signing Mode**: `Unsigned`
   - **Folder**: `barberpro`
   - **Allowed formats**: `jpg, png, webp`
   - **Max file size**: 5MB
5. Copie o nome do preset e o **Cloud Name** do dashboard

---

## 🚀 Deploy no GitHub Pages

```bash
# Crie repositório no GitHub
# Upload dos arquivos (index.html, manifest.json)
# Em Settings → Pages → Source: Deploy from branch → main → / (root)
```

O app estará disponível em: `https://seuuser.github.io/barberpro/`

**Importante para GitHub Pages:** O Firebase Auth com telefone requer HTTPS — o GitHub Pages já fornece isso automaticamente.

---

## 👤 Funcionalidades por Perfil

### Cliente
- Login via número de telefone (SMS OTP)
- Ver calendário de disponibilidade
- Escolher dia, horário e serviço
- Ver valor do serviço antes de confirmar
- Tela de resumo do agendamento
- Visualizar e cancelar próprios agendamentos
- Galeria de fotos dos trabalhos

### Barbeiro/Admin
- Todas as funcionalidades do cliente
- **Dashboard**: estatísticas do dia e do mês
- **Serviços**: criar, editar, excluir (nome, categoria, preço, duração)
  - Categorias: Corte, Barba, Combo, Unhas, Pés
- **Produtos**: estoque com alerta de mínimo
- **Agendamentos**: visualizar todos, filtrar por status, confirmar/concluir/cancelar
- **Galeria**: upload de fotos via Cloudinary com legenda

---

## 📱 Categorias de Serviços

| Ícone | Categoria | Exemplos |
|-------|-----------|---------|
| ✂️ | Corte | Corte simples, Degradê, Navalhado |
| 🪒 | Barba | Barba completa, Acabamento |
| 💈 | Combo | Corte + Barba, Corte + Barba + Sobrancelha |
| 💅 | Unhas | Manicure masculina |
| 🦶 | Pés | Pedicure masculina |
| ⭐ | Outro | Sobrancelha, Pigmentação, etc |

---

## 🗃 Estrutura do Firestore

```
firestore/
├── users/{uid}
│   ├── phone, name, isAdmin, updatedAt
│
├── services/{id}
│   ├── name, category, description, price, duration
│   ├── createdAt, updatedAt
│
├── products/{id}
│   ├── name, brand, category, price, stock, minStock
│   ├── createdAt, updatedAt
│
├── bookings/{id}
│   ├── userId, userPhone, userName
│   ├── serviceId, serviceName, servicePrice, serviceDuration
│   ├── dateKey (YYYY-MM-DD), date (Timestamp), time (HH:MM)
│   ├── status (pending|confirmed|completed|cancelled)
│   ├── notes, createdAt, updatedAt
│
└── gallery/{id}
    ├── url, publicId (Cloudinary), caption
    ├── uploadedBy, createdAt
```

---

## ⚙️ Personalização

### Horários de funcionamento
No `index.html`, edite as constantes:

```javascript
const OPEN_DAYS  = [1,2,3,4,5,6]; // 0=Dom, 1=Seg ... 6=Sáb
const TIME_SLOTS = [
  '09:00','09:30','10:00', ... // adicione ou remova horários
];
```

### Múltiplos barbeiros
Para expandir para múltiplos profissionais, adicione um campo `barberId` nos agendamentos e filtre os slots por barbeiro.

---

## 🔒 Segurança

- Auth por telefone (SMS OTP) — sem senhas a vazar
- Admin definido por lista de números autorizados no código
- Regras do Firestore impedem usuários de ver dados alheios
- Cloudinary com preset unsigned (sem chave secreta no front)
- Clientes só podem cancelar os próprios agendamentos

---

## 🗺 Roadmap v2.0

- [ ] Notificações push (Firebase Cloud Messaging)
- [ ] WhatsApp reminder via n8n webhook
- [ ] Múltiplos barbeiros
- [ ] Relatórios financeiros com gráficos
- [ ] Lista de espera automática
- [ ] Cupons de desconto
- [ ] Avaliações / reviews dos clientes
- [ ] Integração com Google Calendar

---

*BarberPro v1.0.0 — Feito com ❤️ por Rafao*
