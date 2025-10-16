# Guia de Rastreamento Avançado - 2025

## INTRODUÇÃO

**Por que rastreamento é crítico**:
- IA das plataformas precisa de dados de qualidade para otimizar
- "Lixo entra, lixo sai" (GIGO): tracking ruim = otimização ruim
- Rastreamento profissional pode melhorar ROAS em 30-50%

---

## 1. HIERARQUIA DE IMPLEMENTAÇÃO

### Nível 1: BÁSICO (Mínimo Viável)
- ✅ Google Analytics 4 instalado
- ✅ Meta Pixel instalado
- ✅ Conversões configuradas (pelo menos 1: "compra" ou "lead")

**Problema**: Rastreamento só de "cliques" ou conversões muito rasas.

### Nível 2: INTERMEDIÁRIO (Recomendado Mínimo)
- ✅ Eventos customizados:
  - AddToCart (adicionar ao carrinho)
  - InitiateCheckout (iniciar checkout)
  - Lead (formulário preenchido)
  - Purchase (compra finalizada)
- ✅ Valor do pedido sendo enviado
- ✅ Google Tag Manager implementado

### Nível 3: AVANÇADO (Performance Máxima)
- ✅ Conversions API (CAPI) - Meta
- ✅ Enhanced Conversions - Google
- ✅ Server-side tracking (GTM Server)
- ✅ Integração CRM → Plataformas de Ads
- ✅ Eventos de ciclo de vida do cliente

---

## 2. META PIXEL + CONVERSIONS API (CAPI)

### Por que usar os DOIS?

**Meta Pixel (Browser)**:
- Rastreamento client-side (navegador do usuário)
- ❌ Afetado por: Bloqueadores de ads, iOS 14.5+, cookies desabilitados
- Taxa de captura real: ~60-70% dos eventos

**Conversions API (Server)**:
- Rastreamento server-side (seu servidor → Meta)
- ✅ Não é bloqueado
- Taxa de captura: ~95%+

**Combinados**:
- Redundância: Se Pixel falha, CAPI captura
- Resultado: +30-40% mais eventos rastreados = melhor otimização da IA

### Eventos Essenciais (Meta)

```javascript
// Evento: Visualização de Conteúdo
fbq('track', 'ViewContent', {
  content_name: 'Nome do Produto/Serviço',
  content_category: 'Categoria',
  content_ids: ['123'],
  value: 99.90,
  currency: 'BRL'
});

// Evento: Adicionar ao Carrinho
fbq('track', 'AddToCart', {
  content_ids: ['123'],
  content_name: 'Nome do Produto',
  value: 99.90,
  currency: 'BRL'
});

// Evento: Iniciar Checkout
fbq('track', 'InitiateCheckout', {
  content_ids: ['123'],
  value: 99.90,
  currency: 'BRL',
  num_items: 1
});

// Evento: Compra (CRÍTICO)
fbq('track', 'Purchase', {
  value: 99.90,
  currency: 'BRL',
  content_ids: ['123'],
  content_type: 'product'
});
```

**Nota para o Agente**: Se o usuário só tem "PageView" e "Purchase", está perdendo dados de meio de funil. Recomendar implementar eventos intermediários.

---

## 3. GOOGLE ADS - ENHANCED CONVERSIONS

### O que é

- Envia dados hasheados do cliente (email, telefone) para Google
- Google faz match com usuários logados (Gmail, YouTube)
- Resultado: +5-15% mais conversões rastreadas (especialmente cross-device)

### Como Implementar

**Método 1: Google Tag Manager (Recomendado)**

```javascript
// 1. Criar variável "User Data" no GTM
// 2. Mapear campos do formulário:
{
  "email": "{{Form - Email}}",
  "phone_number": "{{Form - Telefone}}",
  "address": {
    "first_name": "{{Form - Nome}}",
    "last_name": "{{Form - Sobrenome}}",
    "city": "{{Form - Cidade}}",
    "postal_code": "{{Form - CEP}}"
  }
}

// 3. Adicionar à tag de conversão do Google Ads
```

**Método 2: API (Para desenvolvedores)**
- Google Ads API - Enhanced Conversions endpoint
- Envio server-side de dados de conversão

---

## 4. TROUBLESHOOTING COMUM

### Problema: "Pixel instalado mas não rastreia conversões"

**Diagnóstico**:
1. Usar Meta Pixel Helper (extensão Chrome)
2. Verificar se evento "Purchase" dispara na página de confirmação
3. Verificar se valor do pedido está sendo enviado

**Causas comuns**:
- Evento disparando na página errada
- JavaScript bloqueado por erro de código
- SPA (Single Page App) - evento dispara só no primeiro carregamento

### Problema: "iOS 14.5+ - Muitas conversões não rastreadas"

**Solução**: Implementar CAPI (prioridade alta)

### Problema: "Google diz 'Dados insuficientes' no Smart Bidding"

**Causa**: <30 conversões nos últimos 30 dias
**Solução Temporária**:
- Consolidar conversões (ao invés de rastrear "Lead", "Consulta", "Venda" separadamente, rastrear tudo como uma conversão "Lead Qualificado")
- Usar CPA alvo manual até atingir volume

---

## 5. QUANDO O AGENTE DEVE RECOMENDAR UPGRADE DE TRACKING

### Sinais de Tracking Insuficiente

| Sintoma | Problema | Recomendação |
|---------|----------|--------------|
| "Sei que tive 100 vendas, mas Meta reporta 60" | Perda de sinal (iOS) | Implementar CAPI |
| "Smart Bidding não otimiza bem" | Pouca conversão rastreada | Enhanced Conversions (Google) |
| "Não sei qual canal converte melhor" | Falta de UTMs | Implementar UTM em todos os links |
| "Remarketing não funciona" | Eventos intermediários ausentes | Implementar AddToCart, ViewContent |

### Priorização

**Se o usuário tem budget <R$5k/mês**:
1. Pixel/Tag básico bem configurado
2. Eventos essenciais (ViewContent, AddToCart, Purchase)
3. Enhanced Conversions (Google)

**Se budget >R$10k/mês**:
1. Tudo acima +
2. CAPI (Meta) - retorno do investimento justifica
3. Server-side tracking (GTM Server)
4. Integração CRM → Ads

---

## 6. TEMPLATE DE DIAGNÓSTICO DE TRACKING

**Quando usuário reporta problemas de performance, usar**:

```markdown
## 🔍 DIAGNÓSTICO DE RASTREAMENTO

Para entender se o problema é de tracking, preciso verificar:

**PERGUNTAS**:
1. Qual plataforma você usa para vender/converter? (Shopify, WooCommerce, WordPress, custom?)
2. Você tem Google Analytics instalado? Qual versão? (GA4 ou Universal?)
3. Meta Pixel está instalado? Em quais páginas?
4. Quais eventos você rastreia? (só "PageView"? ou também "Purchase", "Lead"?)
5. Você sabe quantas conversões REAIS teve no último mês?
6. Quantas conversões a plataforma de ads reporta?

**Se houver discrepância >20%**: Problema de tracking confirmado.

**PLANO DE AÇÃO**:
[Baseado nas respostas, recomendar upgrade específico]
```
