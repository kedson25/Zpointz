# 🚀 Sistema Inteligente de Expedição

![Firebase](https://img.shields.io/badge/Firebase-integrado-orange)
![React](https://img.shields.io/badge/React-frontend-blue)
![ESP32](https://img.shields.io/badge/ESP32-hardware-lightgrey)

> Projeto criado para **automatizar e otimizar o controle logístico de expedição**, reduzindo erros e aumentando a rastreabilidade.

---

## 📌 Objetivo

Criar um sistema inteligente para gerenciar toda a operação logística de expedição, com foco em:

- ✅ Aumentar o foco e atenção dos funcionários  
- ✅ Reduzir erros operacionais  
- ✅ Rastrear tarefas e status em tempo real  
- ✅ Controlar fluxo de cargas, abandonos e backups  
- ✅ Rastrear vagas livres e vagas com backup disponível  
- ✅ Automatizar a comunicação entre equipes  

---

## ⚙️ Tecnologias Utilizadas

### 🔥 Firebase

- **Realtime Database** → status de vagas, tarefas e comunicação  
- **Firestore** → histórico de tarefas e relatórios  
- **Authentication** → controle de acesso (opcional)  
- **Cloud Functions** → automações e lógica backend  

### ⚛️ React

- Painel web para coordenadores  
- Painel consultivo para funcionários  

### 📟 ESP32

- Interface física com botões, buzzer e LEDs  
- Conectado via Wi-Fi ao Firebase  
- Executa e confirma tarefas em tempo real  

### 📱 QR Code

- Interface mobile via navegador para motoristas  
- Ações disponíveis: `Cheguei`, `Solicitar Aduana`, `Remoção`, `Abandonar Carga`

---

## 👥 Papéis e Funções

### 🟢 Coordenador

- Cria operações e turnos  
- Atribui REPs às vagas  
- Monitora o status de todas as vagas  
- Visualiza vagas livres e vagas com backup  
- Gerencia cargas abandonadas e reatribuições  
- Autoriza tarefas especiais (aduana, remoção, etc.)

### 🔵 REP (Responsável pela Vaga)

- Recebe tarefas do Firebase  
- Confirma início e fim de tarefas no ESP32  
- Atende apenas sua vaga  
- Pode auxiliar em remoções com autorização  

### 🟠 PS (Equipe de Suporte)

- Atua se REP estiver ocupado  
- Executa tarefas como aduana, remoções e abandonos  
- Só age com autorização de um coordenador ou PS responsável  

### 🟣 PS Responsável pela Remoção (2 pessoas)

- Recebem solicitações via QR  
- Podem:
  1. Autorizar o REP
  2. Executar a tarefa
  3. Acionar o suporte

### 🟡 Motorista

Ações via QR Code:

- `Cheguei`: preenche placa e nome (opcional), envia notificação  
- `Solicitar Aduana`: dispara tarefa para REP ou PS  
- `Remoção de Pacotes`: vai para PS responsável ou REP  
- `Abandonar Carga`: registra abandono com dados da carga, placa e vaga  

---

## 🔄 Fluxo de Operação

### 1. Carga Normal ou Backup  
- REP recebe tarefa  
- Confirma no ESP32 → vaga muda para "ocupada"  
- Enquanto ocupada, não pode executar outras tarefas  

### 2. Finalização  
- Motorista sai  
- REP clica "OK" → vaga muda para "livre"  

### 3. Remoção de Pacotes  
- Motorista escaneia QR Code  
- PS responsável decide: autorizar REP, fazer a remoção ou solicitar PS  

### 4. Aduana  
- QR ativa tarefa de aduana  
- REP recebe (se livre), senão vai para suporte  
- Status do REP muda para `fazendo aduana`  

### 5. Carga Abandonada  
- Motorista recusa carga  
- REP registra abandono  
- Coordenador visualiza e reatribui para uma vaga com backup  
- REP da vaga de backup executa a tarefa

---

## 🧱 Estrutura de Dados Firebase (Exemplo)

```json
"vagas": {
  "vaga_14": {
    "status": "ocupada",
    "tipoOcupacao": "carga_normal",
    "responsavel": "REP_Kedson",
    "backupAtivo": true
  }
},
"cargas_abandonadas": {
  "CARGA_001": {
    "vaga_origem": "vaga_05",
    "status": "disponivel",
    "local": "zona_abandono_A",
    "registrada_por": "REP_Marcelo",
    "hora": "2025-07-05T14:30"
  }
},
"tarefas": {
  "vaga_14": {
    "tarefa": "puxar carga abandonada",
    "carga": "CARGA_001",
    "tipo": "reatribuicao_backup"
  }
}
