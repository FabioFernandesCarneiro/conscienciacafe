# Consciência Café - Monorepo

Repositório centralizado com todos os projetos e estratégia do Consciência Café.

## Estrutura

```
ConscienciaCafe/
├── apps/
│   ├── site/           # Site institucional (Astro + Tailwind)
│   ├── financeiro/     # Sistema de gestão financeira (Python/Flask)
│   └── intellicoffee/  # App de gestão (Flutter + Firebase)
├── strategy/           # Documentação estratégica
│   ├── brand/          # Brandbook, cores, tom de voz
│   ├── okrs/           # Objetivos e resultados-chave
│   └── social-media/   # Estratégia de redes sociais
└── README.md
```

---

## Estratégia (`strategy/`)

Documentação centralizada de identidade, marca e operações.

| Documento | Descrição |
|-----------|-----------|
| [Missão, Visão, Valores](strategy/missao-visao-valores.md) | Identidade da empresa |
| [Brandbook](strategy/brand/brandbook.md) | Guia completo da marca |
| [Tom de Voz](strategy/brand/tom-de-voz.md) | Personalidade e linguagem |
| [Paleta de Cores](strategy/brand/paleta-cores.md) | Cores oficiais |
| [OKRs](strategy/okrs/) | Objetivos trimestrais |
| [Social Media](strategy/social-media/) | Estratégia de conteúdo |

---

## Aplicações (`apps/`)

### Site (`apps/site/`)

Site institucional do Consciência Café com blog sobre café.

**Tecnologias:** Astro, Tailwind CSS, TypeScript

```bash
cd apps/site
npm install
npm run dev      # Dev server em localhost:4321
npm run build    # Build para produção
```

### Financeiro (`apps/financeiro/`)

Sistema de gestão financeira com conciliação bancária inteligente, integração Omie ERP e CRM B2B.

**Tecnologias:** Python, Flask, SQLAlchemy, scikit-learn

```bash
cd apps/financeiro
python -m venv venv
source venv/bin/activate  # ou venv\Scripts\activate no Windows
pip install -r requirements.txt
cp .env.example .env      # Configurar variáveis de ambiente
python app.py
```

### IntelliCoffee (`apps/intellicoffee/`)

Aplicativo de gestão para cafeterias com suporte a iOS, Android e Web.

**Tecnologias:** Flutter, Dart, Firebase (Firestore, Auth, Functions)

```bash
cd apps/intellicoffee
flutter pub get
flutter run -d chrome     # Web
flutter run               # Mobile
```

---

## Desenvolvimento

Cada projeto mantém suas próprias dependências e pode ser desenvolvido independentemente.

### Para Agentes de IA

A pasta `strategy/` contém todo o contexto necessário para gerar conteúdo alinhado com a marca:
- Leia `strategy/brand/tom-de-voz.md` para entender como nos comunicamos
- Consulte `strategy/brand/paleta-cores.md` para cores da marca
- Siga `strategy/social-media/` para criar conteúdo de redes sociais

---

*Consciência Café - Foz do Iguaçu, PR*
