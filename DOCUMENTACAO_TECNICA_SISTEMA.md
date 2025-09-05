# 📋 Documentação Técnica - Sistema CRM B2B Hunter

## 🎯 Visão Geral do Sistema

Este é um **sistema CRM B2B** desenvolvido para prospecção e gestão de leads, focado em automação de cadência de mensagens. O sistema é uma **aplicação web single-page** (HTML + JavaScript) que utiliza JSONBin.io como banco de dados.

### 🏗️ Arquitetura

- **Frontend**: HTML5 + TailwindCSS + JavaScript Vanilla
- **Backend**: JSONBin.io (API REST JSON)
- **Persistência**: Banco de dados JSON na nuvem
- **Arquivos principais**: `index.html` (sistema principal), `GUIA_RAPIDO_ANALISTA.html` (documentação)

---

## 🗄️ Estrutura de Dados

### **Banco de Dados (JSONBin)**

```json
{
  "leads": [
    {
      "id": "unique_id",
      "nome": "Nome do Contato",
      "empresa": "Nome da Empresa", 
      "cargo": "Cargo do Contato",
      "linkedin": "URL do LinkedIn",
      "status": "Prospecção (Backlog)",
      "currentActionIndex": 0,
      "nextActionDate": "2025-09-03T00:00:00.000Z",
      "notes": "Observações do lead",
      "history": [],
      "createdAt": "2025-09-01T10:30:00.000Z",
      "isActive": true
    }
  ],
  "config": {
    "statusOptions": [
      "Prospecção (Backlog)",
      "Em Contato (Buscando uma resposta)",
      "Conexão/Interesse",
      "Reunião Agendada",
      "Negociação",
      "Parceria Fechada",
      "Parceria Perdida"
    ],
    "actions": [
      {
        "id": 1756420300825,
        "name": "MENSAGEM 1: (Oportunidade Direta)",
        "delayDays": 1,
        "setsStatusTo": "Em Contato (Buscando uma resposta)",
        "template": "Sou [Seu Nome], responsável por parcerias..."
      }
    ]
  }
}
```

### **Campos Críticos dos Leads:**

- **`currentActionIndex`**: Índice da próxima ação na cadência (0-4)
- **`nextActionDate`**: Data da próxima ação em formato ISO (`YYYY-MM-DDTHH:mm:ss.sssZ`)
- **`isActive`**: Se `false`, lead não participa do funil/calendário
- **`status`**: Status atual no funil de vendas
- **`history`**: Array com histórico de ações executadas

---

## 🎮 Funcionalidades Principais

### 1. **Gestão de Leads**
- **Importação**: Excel/CSV com mapeamento automático de colunas
- **Criação manual**: Formulário para adicionar leads individuais
- **Edição**: Modal para alterar dados do lead
- **Ativação/Desativação**: Controle de leads ativos no funil

### 2. **Cadência de Mensagens**
- **5 mensagens configuráveis** com templates personalizados
- **Delays configuráveis** entre mensagens (em dias)
- **Status automático** definido para cada ação
- **Execução manual** de ações pelos analistas

### 3. **Calendário Visual**
- **Próximos 14 dias úteis** com leads agendados
- **Visualização por data** da próxima ação de cada lead
- **Atualização em tempo real** após edições
- **Suporte a múltiplos formatos** de data

### 4. **Pipeline de Mensagens** 🆕
- **Funil visual** com 5 colunas (Mensagem 1-5)
- **Cards com iniciais** e informações do lead
- **Contador por etapa** no cabeçalho
- **Execução direta** de ações

### 5. **Relatórios e Analytics**
- **Métricas de produtividade** por período
- **Análise de conversão** por etapa
- **Exportação** em Excel/CSV
- **Envio por email** automático

---

## 🔧 Funções Técnicas Importantes

### **Principais Funções JavaScript:**

```javascript
// Carregamento e salvamento de dados
loadDataFromBin()          // Carrega dados do JSONBin
saveDataToBin()           // Salva dados no JSONBin

// Renderização da interface
renderTable()             // Renderiza tabela/cards dos leads
generateAgenda()          // Gera calendário visual
generatePipeline()        // Gera pipeline de mensagens
generateKanban()          // Gera funil kanban

// Manipulação de leads
window.app.executeAction(id)           // Executa ação da cadência
window.app.updateNextActionDate(id, date) // Atualiza data da próxima ação
window.app.editLead(id)               // Abre modal de edição
window.app.toggleLeadActive(id)       // Ativa/desativa lead

// Utilitários
parseDate(dateStr)        // Converte string para Date (com tratamento de fusos)
formatDate(dateStr)       // Formata data para exibição (DD/MM/AAAA)
getToday()               // Retorna data atual em UTC
addDays(date, days)      // Adiciona dias a uma data

// Ferramentas de manutenção
normalizeDates()          // Corrige formatos de data inconsistentes
distributeLeadsAcrossWorkingDays() // Distribui leads por dias úteis
window.debugDates()       // Debug rápido das datas dos leads
```

---

## 🐛 Problemas Comuns e Soluções

### **1. Leads não aparecem no calendário na data correta**

**Causa:** Formatos de data inconsistentes no BD
**Solução:** 
```
Relatórios → 🔧 Normalizar Datas → Confirmar
```

### **2. Todos os leads têm a mesma data**

**Causa:** Importação ou criação em lote no mesmo dia
**Soluções:**
- **Distribuição automática**: `Relatórios → 📅 Distribuir Leads`
- **Edição manual**: Clicar na data do lead e alterar

### **3. Calendário não atualiza após editar data**

**Causa:** Função `generateAgenda()` não foi chamada
**Solução:** A função `updateNextActionDate()` já chama automaticamente

### **4. Leads duplicados na interface**

**Causa:** Problemas de renderização ou dados duplicados
**Solução:** Sistema tem limpeza automática na função `saveDataToBin()`

### **5. Erro ao carregar dados**

**Causa:** Problemas com API Key ou Bin ID do JSONBin
**Verificar:**
```javascript
CONFIG.JSONBIN_API_KEY  // Deve começar com $2a$ ou $2b$
CONFIG.JSONBIN_BIN_ID   // Deve ter pelo menos 10 caracteres
```

---

## 🎨 Interface e Componentes

### **Estrutura da UI:**

1. **Header**: Logo, título, botões (Exportar, Relatórios, Guia Rápido)
2. **Configurações**: Status do funil e ações da cadência (collapsible)
3. **Adicionar Lead**: Formulário para criação manual (collapsible)
4. **Dashboard**: Métricas rápidas (Total, Ativos, Ações Hoje, Conversão)
5. **Filtros**: Busca, Status, Atividade
6. **Visualização**: Cards agrupados por empresa (padrão)
7. **Modal de Relatórios**: Analytics, calendário, pipeline, kanban

### **Estados dos Leads:**

- **Ativo** (`isActive: true`): Participa do funil e calendário
- **Inativo** (`isActive: false`): Fica opaco, não permite ações
- **Com próxima ação**: Tem `nextActionDate` definida
- **Sem próxima ação**: `nextActionDate: null` (ciclo finalizado)

---

## 📊 Fluxo de Dados

### **1. Inicialização:**
```
loadDataFromBin() → Validar CONFIG → Carregar dados → Renderizar interface
```

### **2. Importação de Leads:**
```
Arquivo Excel/CSV → Parsing → Validação → Criação de objetos lead → saveDataToBin()
```

### **3. Execução de Ação:**
```
executeAction(id) → Avançar currentActionIndex → Calcular próxima data → 
Atualizar status → Salvar histórico → saveDataToBin() → Atualizar UI
```

### **4. Edição de Data:**
```
updateNextActionDate(id, date) → Validar lead ativo → Converter data → 
Salvar no array → saveDataToBin() → generateAgenda()
```

---

## 🔍 Debugging e Manutenção

### **Funções de Debug Disponíveis:**

```javascript
// No console do navegador:
debugDates()                    // Mostra distribuição de datas dos leads
window.app.debugLead(id)       // Debug específico de um lead
console.log(leads)             // Array completo de leads
console.log(cadenceConfig)     // Configurações do sistema
```

### **Logs Importantes:**

- **`[DEBUG AGENDA]`**: Logs do calendário e agrupamento de datas
- **`[DEBUG updateNextActionDate]`**: Logs de edição de datas
- **`[DEBUG]`**: Logs gerais de operações
- **`🔧`, `📅`, `✅`**: Logs de ferramentas de manutenção

### **Comandos de Manutenção:**

```javascript
// Normalizar datas com formatos inconsistentes
normalizeDates()

// Distribuir leads pelos próximos 10 dias úteis
distributeLeadsAcrossWorkingDays()

// Forçar atualização do calendário
generateAgenda()

// Recarregar dados do JSONBin
loadDataFromBin()
```

---

## ⚙️ Configuração e Deploy

### **Requisitos:**
- Conta no [JSONBin.io](https://jsonbin.io)
- API Key válida (formato `$2a$` ou `$2b$`)
- Bin ID criado no JSONBin

### **Configuração Inicial:**
1. Editar `CONFIG` no início do `index.html`
2. Inserir `JSONBIN_API_KEY` e `JSONBIN_BIN_ID`
3. Configurar status do funil e ações da cadência
4. Importar leads iniciais

### **Estrutura de Arquivos:**
```
hunter/
├── index.html                    # Sistema principal
├── GUIA_RAPIDO_ANALISTA.html    # Manual do usuário
├── DOCUMENTACAO_TECNICA_SISTEMA.md # Este documento
├── crm_*.html                   # Versões alternativas da UI
└── modelo importar.xlsx         # Template para importação
```

---

## 🎯 Casos de Uso Típicos

### **Analista de Prospecção:**
1. Abre o sistema diariamente
2. Consulta o calendário para ver ações do dia
3. Executa mensagens programadas
4. Atualiza status conforme retorno dos leads
5. Agenda próximas ações conforme necessário

### **Gestor/Supervisor:**
1. Acessa relatórios para métricas de produtividade
2. Analisa pipeline de mensagens para identificar gargalos
3. Exporta dados para análises externas
4. Configura cadência e templates de mensagens

### **Suporte Técnico:**
1. Usa funções de debug para investigar problemas
2. Executa normalização de datas quando necessário
3. Distribui leads quando há concentração em datas específicas
4. Monitora logs para identificar problemas de sincronização

---

## 📝 Notas Técnicas para Agentes

### **Padrões de Código:**
- JavaScript ES6+ com async/await
- Manipulação direta do DOM (sem frameworks)
- Debouncing para evitar renderizações excessivas
- Tratamento robusto de erros com try/catch

### **Convenções:**
- IDs únicos: `Date.now() + Math.random()`
- Datas sempre em UTC ISO format
- Logs prefixados por funcionalidade: `[DEBUG AGENDA]`, `[DEBUG updateNextActionDate]`
- Notificações visuais para feedback do usuário

### **Performance:**
- Renderização com debounce (200ms)
- Limpeza automática de duplicatas
- Lazy loading de componentes pesados
- Cache local com localStorage

### **Segurança:**
- API Key configurada no código (ambiente controlado)
- Validação de entrada em formulários
- Escape de HTML para prevenir XSS
- Confirmações para ações destrutivas

---

## 🚨 Problemas Conhecidos e Correções

### **Bug Histórico 1: Reset de datas na inicialização**
- **Problema**: Sistema resetava `nextActionDate` para data fixa ao carregar
- **Causa**: Rotina de "correção de emergência" no `loadDataFromBin()`
- **Solução**: Removida a rotina, sistema agora preserva datas originais do BD
- **Commit**: `b290501` - "🐛 Fix: Remove reset automático de nextActionDate na inicialização"

### **Bug Histórico 2: Calendário não atualizava após edição**
- **Problema**: Editar data do lead não atualizava o calendário visual
- **Causa**: `updateNextActionDate()` não chamava `generateAgenda()`
- **Solução**: Adicionada chamada automática para `generateAgenda()`
- **Commit**: `2c8c482` - "🐛 Fix: Calendario não atualizava após edição de nextActionDate"

### **Bug Histórico 3: Problema de fuso horário**
- **Problema**: Leads apareciam no dia anterior no calendário
- **Causa**: Função `parseDate()` aplicava UTC duplo
- **Solução**: Correção na lógica de parsing de datas ISO
- **Commit**: `db6e06c` - "🐛 Fix: Corrigir problema de fuso horário na função parseDate"

### **Bug Histórico 4: Formatos de data inconsistentes**
- **Problema**: Leads com formato `DD/MM/YYYY` não apareciam no calendário
- **Causa**: `parseDate()` não reconhecia formato brasileiro
- **Solução**: Adicionado suporte para múltiplos formatos + função `normalizeDates()`
- **Commits**: `942a662`, `a1d01ad` - Correções de formato de data

---

## 🛠️ Guia de Troubleshooting para Agentes

### **Quando um usuário reportar problema:**

1. **Identificar o tipo de problema:**
   - 🗓️ Calendário (leads não aparecem na data correta)
   - 💾 Persistência (dados não salvam)
   - 🎯 Funcionalidade (botões não funcionam)
   - 📊 Dados (duplicatas, inconsistências)

2. **Comandos de diagnóstico:**
   ```javascript
   // No console do navegador:
   debugDates()                    // Verificar distribuição de datas
   console.log(leads.length)       // Total de leads
   console.log(cadenceConfig)      // Configurações
   ```

3. **Soluções rápidas:**
   - **Problema de calendário**: `Relatórios → 🔧 Normalizar Datas`
   - **Dados não salvam**: Verificar console por erros de API
   - **Leads duplicados**: Sistema limpa automaticamente no save
   - **Performance lenta**: Verificar se há muitos leads (>1000)

### **Padrões de Logs para Investigação:**

```
[DEBUG AGENDA] 📊 Distribuição por data: {...}
[DEBUG updateNextActionDate] ✅ Salvamento concluído
🔧 Iniciando normalização de datas...
📅 Iniciando distribuição automática...
```

### **Arquivos de Código Relevantes:**

- **Linhas 1913-1940**: Função `parseDate()` (conversão de datas)
- **Linhas 2791-2834**: Função `updateNextActionDate()` (edição de datas)
- **Linhas 3552-3671**: Função `generatePipeline()` (pipeline visual)
- **Linhas 3673-3900**: Função `generateAgenda()` (calendário)
- **Linhas 1627-1695**: Função `saveDataToBin()` (persistência)

---

## 📈 Métricas e KPIs

### **Dashboard Principal:**
- **Total de Leads**: Contador geral
- **Leads Ativos**: Filtro `isActive !== false`
- **Ações Hoje**: Leads com `nextActionDate = hoje`
- **Taxa de Conversão**: Leads fechados / Total de leads

### **Pipeline de Mensagens:**
- **Distribuição por etapa**: Quantos leads em cada mensagem
- **Gargalos**: Etapas com acúmulo de leads
- **Progressão**: Movimento entre as etapas

### **Relatório Executivo:**
- **Leads adicionados** por período
- **Mensagens enviadas** por período  
- **Mudanças de status** por período
- **Análise de performance** por mensagem

---

## 🔄 Fluxo de Dados Críticos

### **Ciclo de Vida de um Lead:**

1. **Criação** → `currentActionIndex: 0`, `nextActionDate: hoje`
2. **Primeira ação** → `currentActionIndex: 1`, `nextActionDate: hoje + delayDays`
3. **Progressão** → Incrementa `currentActionIndex`, calcula nova data
4. **Finalização** → `currentActionIndex >= actions.length`, `nextActionDate: null`

### **Sincronização Calendário ↔ BD:**

```
BD (nextActionDate) → parseDate() → Agrupamento por data → Renderização visual
```

### **Persistência:**

```
Ação do usuário → Atualização local (array leads) → saveDataToBin() → JSONBin API
```

---

## 🎯 Pontos de Atenção para Agentes

### **❗ Sempre verificar:**
- Se `CONFIG.JSONBIN_API_KEY` e `CONFIG.JSONBIN_BIN_ID` estão configurados
- Se há erros no console relacionados à API do JSONBin
- Se `leads` e `cadenceConfig` estão carregados corretamente
- Se funções críticas como `parseDate()` estão funcionando

### **🔧 Ferramentas de manutenção disponíveis:**
- **Normalizar Datas**: Para problemas de calendário
- **Distribuir Leads**: Para redistribuição automática
- **Debug Agenda**: Para investigação detalhada
- **Atualizar Agenda**: Para forçar refresh

### **📋 Comandos úteis para debug:**
```javascript
// Verificar estado atual
console.log('Total leads:', leads.length)
console.log('Leads ativos:', leads.filter(l => l.isActive !== false).length)
console.log('Leads com ação hoje:', leads.filter(l => l.nextActionDate?.includes(new Date().toISOString().split('T')[0])).length)

// Verificar lead específico
const lead = leads.find(l => l.nome.includes('Roberto'))
console.log('Lead encontrado:', lead)

// Verificar configurações
console.log('Status configurados:', cadenceConfig.statusOptions)
console.log('Ações configuradas:', cadenceConfig.actions)
```

---

## 🎯 Objetivos do Sistema

O sistema foi desenvolvido para:

1. **Automatizar** o processo de prospecção B2B
2. **Organizar** leads em um funil visual claro
3. **Agendar** ações de forma eficiente
4. **Medir** produtividade dos analistas
5. **Facilitar** o acompanhamento gerencial
6. **Reduzir** trabalho manual repetitivo

---

*Documento criado para facilitar o suporte técnico e manutenção do sistema CRM B2B Hunter.*

**Última atualização**: Janeiro 2025
**Versão do sistema**: Com Pipeline de Mensagens e Calendário Visual
**Commits principais**: `b290501`, `2c8c482`, `db6e06c`, `942a662`, `bb97f04`
