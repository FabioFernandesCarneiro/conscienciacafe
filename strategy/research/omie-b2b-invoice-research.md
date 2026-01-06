# Pesquisa: Emissão de NF-e de ICMS via Módulo B2B

**Data:** 2026-01-05
**Solicitante:** Consciência Café
**Contexto:** Atualmente utilizando ERP Omie para emissão de NF-e B2B. Mensalidade aumentou e o ERP está subutilizado. Avaliar viabilidade de integrar emissão de NF-e no módulo B2B existente.
**Localização:** Paraná (SEFAZ-PR)

---

## 1. Resumo Executivo

### Viabilidade: ✅ VIÁVEL

A integração de emissão de NF-e de ICMS no módulo B2B é **tecnicamente viável** através de três abordagens:

| Abordagem | Complexidade | Custo Mensal Estimado | Tempo Implementação |
|-----------|--------------|----------------------|---------------------|
| **A) Manter Omie (via API)** | Baixa | R$ 199-499/mês (mensalidade ERP) | 2-4 semanas |
| **B) Migrar para API especializada** | Média | R$ 50-200/mês (por volume) | 4-8 semanas |
| **C) Integração direta SEFAZ** | Alta | R$ 0 (apenas certificado) | 3-6 meses |

**Recomendação:** Opção **B (API especializada)** oferece o melhor custo-benefício, eliminando a mensalidade do ERP Omie enquanto mantém simplicidade de implementação.

---

## 2. Situação Atual do Sistema

### 2.1 Infraestrutura Existente

O módulo B2B (`/apps/financeiro/src/b2b/`) já possui:

- ✅ **CRM completo** com pipeline de vendas
- ✅ **Gestão de pedidos** (`Order`, `OrderItem`)
- ✅ **Catálogo de produtos** (`CoffeeProduct`, `CoffeePackagingPrice`)
- ✅ **Integração Omie** (`omie_client.py`) para:
  - Clientes/fornecedores
  - Conta corrente
  - Categorias contábeis
  - Lançamentos financeiros
- ✅ **Dashboard B2B** com métricas

### 2.2 Gap Identificado

❌ **Não há integração para emissão de NF-e**

O `omie_client.py` atual utiliza métodos financeiros do Omie, mas não os endpoints de faturamento:
- `IncluirPedido` - Criar pedido de venda
- `FaturarPedidoVenda` - Faturar e emitir NF-e
- `ConsultarNF` - Consultar status da NF-e

---

## 3. Opções de Implementação

### 3.1 Opção A: Manter Omie (Usar API de Faturamento)

**Descrição:** Continuar usando Omie, mas automatizando a emissão via API.

#### Fluxo Proposto:
```
[Pedido B2B] → [IncluirPedido] → [FaturarPedidoVenda] → [NF-e emitida]
     ↓              ↓                    ↓
  Sistema      API Omie             SEFAZ-PR
```

#### Endpoints Omie Relevantes:
- `POST https://app.omie.com.br/api/v1/produtos/pedido/` - Pedidos de venda
  - `IncluirPedido` - Criar pedido
  - `TrocarEtapaPedido` - Alterar status
  - `FaturarPedidoVenda` - Faturar e emitir NF-e
- `POST https://app.omie.com.br/api/v1/produtos/nfconsultar/` - Consultar NF-e

#### Vantagens:
- ✅ Menor esforço de desenvolvimento (cliente já existe)
- ✅ Configuração fiscal já está no Omie
- ✅ Certificado digital já configurado

#### Desvantagens:
- ❌ Continua pagando mensalidade Omie (R$ 199-499+/mês)
- ❌ Dependência de plataforma terceira
- ❌ ERP permanece subutilizado

#### Custo:
- Desenvolvimento: ~40h
- Mensalidade Omie: R$ 199-499/mês (plano atual)

---

### 3.2 Opção B: Migrar para API Especializada de NF-e

**Descrição:** Substituir Omie por uma API focada exclusivamente em emissão de documentos fiscais.

#### Principais Provedores:

| Provedor | NF-e | NFS-e | Municípios | Diferenciais |
|----------|------|-------|------------|--------------|
| [**Focus NFe**](https://focusnfe.com.br/) | ✅ | ✅ | 1.000+ | REST API, sem contrato mínimo |
| [**NFe.io**](https://nfe.io/) | ✅ | ✅ | Ampla | Alta performance, cálculo automático |
| [**eNotas**](https://enotas.com.br/) | ✅ | ✅ | Nacional | Integração com gateways de pagamento |
| [**Webmania**](https://webmania.com.br/) | ✅ | ✅ | Nacional | Cálculo automático ICMS/IPI/PIS/COFINS |

#### Estimativa de Preços (2025):

| Provedor | Modelo | Custo Estimado |
|----------|--------|----------------|
| **Focus NFe** | Por nota | ~R$ 0,15-0,50/nota |
| **NFe.io** | Planos | A partir de R$ 49/mês (+ excedente) |
| **eNotas** | Planos | Taxa adesão R$ 347 + mensalidade |
| **Webmania** | Planos | Planos variados |

> **Nota:** Para 50-200 notas/mês (volume B2B típico), custo ficaria entre R$ 25-100/mês, muito inferior à mensalidade do Omie.

#### Fluxo Proposto:
```
[Pedido B2B] → [API NF-e] → [XML assinado] → [SEFAZ-PR] → [NF-e autorizada]
     ↓              ↓              ↓              ↓              ↓
  Sistema      Focus/NFe.io   Certificado    Validação     Retorno XML
```

#### Vantagens:
- ✅ Custo significativamente menor
- ✅ APIs modernas (REST/JSON)
- ✅ Documentação clara
- ✅ Sem dependência de ERP
- ✅ Escala conforme demanda

#### Desvantagens:
- ⚠️ Precisa reconfigurar certificado digital
- ⚠️ Precisa mapear dados fiscais (NCM, CFOP, CST)
- ⚠️ Desenvolvimento mais extenso

#### Custo:
- Desenvolvimento: ~80-120h
- Mensalidade: R$ 50-150/mês (estimativa)
- Certificado A1: ~R$ 150-300/ano

---

### 3.3 Opção C: Integração Direta com SEFAZ-PR

**Descrição:** Desenvolver integração direta com o webservice da SEFAZ do Paraná.

#### Requisitos Técnicos:
1. **Credenciamento SEFAZ-PR**
   - Ambiente de homologação: Automático para inscritos no CAD/ICMS
   - Ambiente de produção: Requer autorização (NPF n. 101/2014)
   - Portal: https://receita.pr.gov.br/login

2. **Certificado Digital ICP-Brasil**
   - Tipo: A1 (recomendado para sistemas) ou A3
   - Deve conter CNPJ da empresa
   - Validade: 1-3 anos

3. **Desenvolvimento**
   - Geração de XML conforme leiaute NF-e 4.0
   - Assinatura digital (XMLDSig)
   - Comunicação SOAP/REST com SEFAZ
   - Tratamento de contingências (SCAN, SVC)

#### Bibliotecas Python Disponíveis:
```python
# Opções de bibliotecas
- python-nfelib  # Mais popular, completa
- nfelib         # Alternativa leve
- pynfe          # Outra alternativa
```

#### Vantagens:
- ✅ Custo zero de API (apenas certificado)
- ✅ Controle total do processo
- ✅ Sem dependência de terceiros

#### Desvantagens:
- ❌ Complexidade muito alta
- ❌ Manutenção contínua (atualizações de leiaute)
- ❌ Tratamento de contingências
- ❌ Tempo de desenvolvimento extenso

#### Custo:
- Desenvolvimento: ~300-500h
- Certificado A1: ~R$ 150-300/ano
- Manutenção: Contínua

---

## 4. Requisitos Legais e Fiscais (Paraná)

### 4.1 Credenciamento SEFAZ-PR

- **Base legal:** NPF n. 101/2014
- **Ambiente de homologação:** Automático para contribuintes ativos
- **Ambiente de produção:** Requer autorização formal
- **A partir de abril/2025:** Validação obrigatória do identificador do responsável técnico (Decreto 062/2023)

### 4.2 Certificado Digital

- **Tipo obrigatório:** ICP-Brasil (A1 ou A3)
- **Recomendação:** A1 para sistemas web (arquivo digital)
- **Custo:** R$ 150-300/ano
- **Uso:** Assinatura do XML e transmissão

### 4.3 Reforma Tributária 2025

A Lei Complementar 214/2025 introduz mudanças importantes:

- **Novos tributos:** IBS (substitui ICMS) e CBS (substitui PIS/COFINS)
- **Impacto:** Atualização de leiautes NF-e e NFC-e
- **Rejeição automática:** Inconsistências nos novos campos invalidam o documento

> **Atenção:** Qualquer solução escolhida deve estar preparada para a transição tributária.

---

## 5. Dados Fiscais Necessários

Para emitir NF-e de café (produto), são necessários:

### 5.1 Dados do Produto

| Campo | Descrição | Exemplo Café |
|-------|-----------|--------------|
| NCM | Nomenclatura Comum Mercosul | 0901.21.00 (café torrado, não descafeinado) |
| CFOP | Código Fiscal de Operações | 5102 (venda dentro do estado) / 6102 (interestadual) |
| CST ICMS | Código Situação Tributária | Depende do regime (Simples/Normal) |
| Alíquota ICMS | Imposto sobre Circulação | 18% (PR interno) / 12% (interestadual Sul) |
| CEST | Código Especificador ST | Verificar se há ST para café |

### 5.2 Dados do Cliente

- CNPJ
- Inscrição Estadual
- Endereço completo (CEP obrigatório)
- Regime tributário

### 5.3 Dados da Operação

- Natureza da operação
- Data emissão
- Valores (produtos, frete, seguro, desconto)
- Forma de pagamento
- Transportadora (se aplicável)

---

## 6. Arquitetura Proposta (Opção B)

### 6.1 Novos Componentes

```
apps/financeiro/src/
├── b2b/
│   ├── invoice_service.py      # Novo: Serviço de NF-e
│   ├── fiscal_calculator.py    # Novo: Cálculo de impostos
│   └── nfe_client.py           # Novo: Cliente API NF-e
├── models.py                   # Atualizar: Adicionar Invoice, InvoiceItem
└── templates/
    └── invoices/               # Novo: Templates de NF-e
```

### 6.2 Modelo de Dados Proposto

```python
class Invoice(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    order_id = db.Column(db.Integer, ForeignKey('order.id'))

    # Dados da NF-e
    numero_nfe = db.Column(db.String(20))
    serie = db.Column(db.String(3))
    chave_acesso = db.Column(db.String(44))

    # Status
    status = db.Column(db.String(20))  # pendente, autorizada, rejeitada, cancelada
    protocolo_autorizacao = db.Column(db.String(20))

    # XML
    xml_enviado = db.Column(db.Text)
    xml_autorizado = db.Column(db.Text)

    # Timestamps
    data_emissao = db.Column(db.DateTime)
    data_autorizacao = db.Column(db.DateTime)

    # Relacionamentos
    order = db.relationship('Order', backref='invoice')
    items = db.relationship('InvoiceItem', backref='invoice')
```

### 6.3 Fluxo de Integração

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────┐
│   Pedido    │────▶│   Invoice   │────▶│  API NF-e   │────▶│ SEFAZ-PR│
│    B2B      │     │   Service   │     │(Focus/NFe.io)│     │         │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────┘
                           │                   │                  │
                           ▼                   ▼                  ▼
                    ┌─────────────┐     ┌─────────────┐     ┌─────────┐
                    │   Fiscal    │     │     XML     │     │Protocolo│
                    │ Calculator  │     │  Assinado   │     │Autoriza │
                    └─────────────┘     └─────────────┘     └─────────┘
```

---

## 7. Plano de Implementação (Opção B)

### Fase 1: Preparação (1-2 semanas)
- [ ] Escolher provedor de API NF-e
- [ ] Adquirir certificado digital A1 (se não tiver)
- [ ] Criar conta no provedor
- [ ] Obter credenciais de API
- [ ] Configurar ambiente de homologação SEFAZ-PR

### Fase 2: Desenvolvimento Core (2-3 semanas)
- [ ] Criar modelos `Invoice` e `InvoiceItem`
- [ ] Implementar `nfe_client.py` (cliente da API escolhida)
- [ ] Implementar `fiscal_calculator.py` (NCM, CFOP, CST, alíquotas)
- [ ] Implementar `invoice_service.py` (orquestração)

### Fase 3: Integração B2B (1-2 semanas)
- [ ] Adicionar botão "Emitir NF-e" no pedido
- [ ] Criar tela de visualização de NF-e
- [ ] Implementar download DANFE (PDF)
- [ ] Adicionar NF-e ao dashboard B2B

### Fase 4: Testes e Homologação (1-2 semanas)
- [ ] Testar em ambiente de homologação
- [ ] Validar cálculos fiscais com contador
- [ ] Testar cenários de erro/rejeição
- [ ] Migrar para produção

### Fase 5: Migração do Omie (1 semana)
- [ ] Exportar histórico de NF-e do Omie
- [ ] Cancelar módulo de faturamento do Omie
- [ ] Manter Omie apenas para funcionalidades essenciais ou cancelar

---

## 8. Comparativo de Custos (12 meses)

| Item | Omie Atual | Opção A (Omie API) | Opção B (API Esp.) | Opção C (Direto) |
|------|------------|--------------------|--------------------|------------------|
| Mensalidade ERP | R$ 2.988-5.988 | R$ 2.988-5.988 | R$ 0 | R$ 0 |
| API NF-e | R$ 0 | R$ 0 | R$ 600-1.800 | R$ 0 |
| Certificado | R$ 0 (incluso) | R$ 0 (incluso) | R$ 200 | R$ 200 |
| Desenvolvimento | R$ 0 | R$ 4.000 | R$ 8.000-12.000 | R$ 30.000-50.000 |
| **Total 1º ano** | **R$ 2.988-5.988** | **R$ 6.988-9.988** | **R$ 8.800-14.000** | **R$ 30.200-50.200** |
| **Total anos seguintes** | **R$ 2.988-5.988/ano** | **R$ 2.988-5.988/ano** | **R$ 800-2.000/ano** | **R$ 200/ano** |

> **Análise:** A Opção B tem payback em ~2 anos, após isso gera economia de R$ 2.000-4.000/ano.

---

## 9. Recomendação Final

### Recomendação: **Opção B - API Especializada (Focus NFe ou NFe.io)**

**Justificativas:**
1. **Economia significativa** a médio/longo prazo
2. **Complexidade gerenciável** vs integração direta SEFAZ
3. **APIs modernas e bem documentadas**
4. **Independência do ERP Omie** (pode cancelar completamente)
5. **Escalabilidade** conforme crescimento B2B

### Provedor Sugerido: **Focus NFe**
- Maior cobertura de municípios (relevante para expansão futura)
- Sem taxa inicial, sem contrato mínimo
- REST API com documentação clara
- Suporte a NF-e, NFS-e, NFC-e, CT-e

### Próximos Passos:
1. **Validar com contador** os dados fiscais (NCM, CFOP, CST)
2. **Solicitar orçamento** Focus NFe e NFe.io
3. **Adquirir certificado A1** se não possuir
4. **Iniciar desenvolvimento** após escolha do provedor

---

## 10. Fontes e Referências

- [Sistema Omie para Nota Fiscal](https://www.omie.com.br/funcionalidades/nota-fiscal/)
- [Omie - Portal do Desenvolvedor](https://developer.omie.com.br/service-list/)
- [Focus NFe - API NF-e](https://focusnfe.com.br/produtos/nota-fiscal-eletronica-nfe/)
- [NFe.io - Sistema de Emissão](https://nfe.io/)
- [eNotas - Emissão Automática](https://enotas.com.br/)
- [SEFAZ-PR - Credenciamento NF-e](https://sped.fazenda.pr.gov.br/NFe/Pagina/Regras-de-Credenciamento)
- [Sebrae - Certificado Digital NF-e](https://sebrae.com.br/sites/PortalSebrae/artigos/preciso-ter-certificado-digital-para-emitir-nf-e)
- [Legislação NF-e 2025](https://www.omie.com.br/blog/legislacao-da-nota-fiscal-2025-entenda-o-que-mudou-e-o-que-fazer/)
- [Webmania - Documentação API](https://webmania.com.br/docs/rest-api-nfe/)
