# J.A. Móveis Planejados — Site institucional

Landing page de página única da J.A. Móveis Planejados, marcenaria de móveis sob medida em Cabedelo/PB. O objetivo do site é qualificar quem procura orçamento antes de a conversa chegar ao WhatsApp, reduzindo o vai-e-vem de perguntas básicas e encurtando o caminho até o agendamento da visita de medição.

**Produção:** https://ja-moveis-planejados.vercel.app

---

## Sumário

- [Visão geral](#visão-geral)
- [Stack](#stack)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como o formulário funciona](#como-o-formulário-funciona)
- [Executando localmente](#executando-localmente)
- [Deploy](#deploy)
- [Manutenção](#manutenção)
- [Design system](#design-system)
- [Acessibilidade e performance](#acessibilidade-e-performance)
- [Roadmap](#roadmap)
- [Direitos autorais](#direitos-autorais)

---

## Visão geral

O site é composto por sete seções em fluxo único:

| # | Seção | Função |
|---|-------|--------|
| 1 | Hero | Logo flutuante, proposta de valor e chamada principal |
| 2 | Sobre | Credibilidade e desenho técnico animado |
| 3 | Serviços | Quatro ambientes; cada card alimenta o formulário |
| 4 | Processo | As cinco etapas, do contato à montagem |
| 5 | Depoimentos | Prova social |
| 6 | Orçamento | Formulário de qualificação em quatro perguntas |
| 7 | Rodapé | Contatos e navegação |

Elementos fixos: cabeçalho com a marca, botão flutuante do WhatsApp e barra de progresso em formato de trena, que aparece quando o formulário entra na tela.

---

## Stack

- **HTML, CSS e JavaScript puros.** Sem framework, sem build, sem dependências instaladas.
- **Arquivo único.** Estilos e scripts são inline; as imagens da marca estão embutidas em base64.
- **Fontes:** Cormorant Garamond (títulos), Montserrat (marca), Inter (texto) e JetBrains Mono (rótulos técnicos), carregadas do Google Fonts.
- **Hospedagem:** Vercel, como site estático.

A escolha por arquivo único é deliberada: o site é institucional e muda pouco, então zero build significa zero quebra de deploy e edição direta pelo navegador do GitHub, inclusive pelo celular.

---

## Estrutura do repositório

```
.
├── index.html    # site completo (estilos, scripts e imagens embutidos)
└── README.md
```

Referência das principais âncoras internas do `index.html`:

| ID | Seção |
|----|-------|
| `topo` | Início da página |
| `servicos` | Serviços |
| `processo` | Processo |
| `depoimentos` | Depoimentos |
| `orcamento` | Formulário |

A navegação interna usa `<button data-goto="id">`, não links `<a href="#id">`. Isso evita que visualizadores embutidos e webviews interceptem o clique e tentem abrir o endereço em vez de rolar a página.

---

## Como o formulário funciona

O formulário não envia dados para nenhum servidor. Ele monta uma mensagem de texto e abre o WhatsApp já preenchido.

**Campos coletados**

| Campo | Tipo | Obrigatório |
|-------|------|-------------|
| Ambientes | múltipla escolha | sim |
| Situação do imóvel | escolha única | sim |
| Prazo de instalação | escolha única | sim |
| Nome | texto | sim |
| Bairro e cidade | texto | sim |
| Detalhes | texto livre | não |

**Mensagem gerada**

```
*Solicitação de orçamento — site J.A. Móveis*

Nome: Ana Paula
Local: Intermares, Cabedelo
Ambientes: Cozinha, Home office
Imóvel: Em obra / reforma
Prazo: 1 a 3 meses
Detalhes: cozinha em L, uns 3 metros de armário

Podemos agendar a visita de medição?
```

O texto é codificado com `encodeURIComponent` e anexado a `https://wa.me/<numero>?text=`. O botão de envio fica desabilitado até os cinco campos obrigatórios estarem preenchidos, e a barra inferior indica o que ainda falta.

**Detalhe de UX:** clicar em um card de serviço marca o ambiente correspondente, exibe uma confirmação e rola direto para o formulário.

---

## Executando localmente

Não há dependências nem servidor. Basta abrir o arquivo:

```bash
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

Se preferir um servidor local, para testar em outros aparelhos da mesma rede:

```bash
python3 -m http.server 8000
```

---

## Deploy

O projeto está conectado ao Vercel. Todo commit na branch `main` publica automaticamente em produção, em cerca de um minuto.

**Configuração no Vercel**

| Opção | Valor |
|-------|-------|
| Framework Preset | Other |
| Build Command | *(vazio)* |
| Output Directory | *(vazio)* |
| Install Command | *(vazio)* |

**Editando pelo navegador ou celular**

1. Abra o `index.html` no GitHub
2. Toque no ícone de lápis
3. Faça a alteração
4. Commit changes

O Vercel detecta o commit e republica sozinho.

---

## Manutenção

### Trocar o número do WhatsApp

Duas alterações são necessárias.

**1. Formulário** — no bloco `<script>`, próximo ao fim do arquivo:

```js
var FONE='5583988320978'; /* formato: 55 + DDD + número, só dígitos */
```

**2. Links diretos** — busque por `wa.me/5583988320978` e substitua em todas as ocorrências. São cinco: cabeçalho, botão flutuante, "Começar pela etapa 1", "Fale direto no WhatsApp" e rodapé.

### Alterar as cores

Todas as cores estão em variáveis no início do `<style>`:

```css
:root{
  --deep:#0A272E;      /* fundo principal (verde petróleo) */
  --deep-2:#0E343C;    /* fundo de blocos */
  --gold:#D9B45E;      /* dourado da marca */
  --paper:#F3EFE6;     /* texto claro */
  --muted:#93A9AE;     /* texto secundário */
  --wa:#25D366;        /* verde WhatsApp */
}
```

Trocar `--deep` e `--gold` muda a identidade da página inteira.

### Adicionar ou remover ambientes do formulário

Cada opção é um `<button class="chip">` dentro de `<div class="chips" id="ambientes">`. O texto visível do botão é o que aparece na mensagem do WhatsApp — copie a estrutura de um item existente, incluindo o `<span class="tick">`.

Para que um card de serviço alimente o formulário, o atributo `data-ambiente` do card precisa ser idêntico ao texto do chip correspondente.

### Atualizar depoimentos

Cada depoimento é um `<article class="quote">` na seção `#depoimentos`. Duplique o bloco para adicionar; o layout se ajusta sozinho.

### Trocar as imagens da marca

As três imagens estão embutidas em base64 (`data:image/png;base64,...`): monograma pequeno do cabeçalho, monograma grande do hero e lockup do rodapé. Para substituir, converta o PNG novo para base64 e troque a string correspondente. Alternativa mais simples: salvar os arquivos no repositório e trocar o `src` por um caminho relativo, como `src="logo.png"`.

---

## Design system

**Cores** — verde petróleo `#0A272E` e dourado `#D9B45E`, extraídos da identidade visual da marca.

**Tipografia**

| Papel | Fonte | Uso |
|-------|-------|-----|
| Títulos | Cormorant Garamond | Chamadas e números de destaque |
| Marca | Montserrat | Nome no cabeçalho, em caixa alta com tracking largo |
| Corpo | Inter | Parágrafos, botões e formulário |
| Técnica | JetBrains Mono | Rótulos, numeração e legendas |

**Elemento de assinatura** — a trena. Aparece na barra de progresso do formulário, com marcações estilo fita métrica, e nas marcas verticais douradas acima dos números do hero. É uma referência direta à medição sob medida, que é o núcleo do serviço.

**Motion** — a logo do hero flutua num ciclo de 6,5 s; o desenho técnico se traça ao carregar; a barra de progresso desliza a cada resposta. Tudo é desativado quando o sistema sinaliza `prefers-reduced-motion`.

---

## Acessibilidade e performance

- Estados de foco visíveis em todos os controles interativos
- Chips implementados como botões com `aria-pressed`, navegáveis por teclado
- `aria-live` nas confirmações de seleção
- Áreas de toque com no mínimo 44 px de altura
- Contraste conferido para texto sobre o fundo escuro
- Preferência de movimento reduzido respeitada
- Requisição única de HTML; as únicas chamadas externas são as fontes
- `preconnect` configurado para o Google Fonts

---

## Roadmap

- [ ] Galeria com fotos reais dos projetos entregues, entre Serviços e Depoimentos
- [ ] Link de agendamento na etapa 2, para o cliente escolher o horário da visita
- [ ] Depoimentos com foto do ambiente
- [ ] Registro de eventos de conversão (envios do formulário)
- [ ] Nota de LGPD no rodapé sobre o uso dos dados do formulário
- [ ] Domínio próprio

---

## Direitos autorais

© 2026 J.A. Móveis Planejados. Todos os direitos reservados.

Este repositório contém código, conteúdo e identidade visual proprietários da J.A. Móveis Planejados. É vedada a reprodução, distribuição ou uso, total ou parcial, do código, do layout, dos textos, da marca e da logotipo sem autorização expressa e por escrito do titular.

Não é software livre e não está licenciado para reutilização.

---

**J.A. Móveis Planejados** · Cabedelo, PB
WhatsApp (83) 98832-0978 · [@jamoveisplanejado10](https://www.instagram.com/jamoveisplanejado10/)
