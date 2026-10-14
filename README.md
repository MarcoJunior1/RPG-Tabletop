# Casaro's World

Plataforma web para campanhas de **D&D 5e**: fichas de personagem, campanhas, bestiário com ameaças personalizadas, combate por iniciativa e rolagem de dados. A estrutura de telas é inspirada no CRIS (Ordem Paranormal), adaptada para a 5ª edição, com visual maximalista de fantasia medieval. A mesa virtual é feita no Owlbear Rodeo, aberta a partir da campanha.

> **Status:** protótipo de front-end. Tudo roda no navegador e os dados ficam salvos só nele. Ainda não há contas, servidor nem campanha compartilhada entre jogadores. Veja [Limitações](#limitações) e [Roadmap](#roadmap).

---

## Funcionalidades

### Landing e acesso
- Página de apresentação com d20 clicável, faixa animada e cards de funcionalidades.
- Login simples por nome (sem senha, apenas identifica o usuário no navegador).

### Personagens
- Até **15 personagens**, com retrato, nome, classe, raça, origem e nível.
- Ficha completa:
  - **6 atributos** (FOR, DES, CON, INT, SAB, CAR) em medalhões; o modificador é clicável e rola o teste.
  - **18 perícias** da 5e com 3 estados de proficiência (nenhuma, proficiente, expertise) e campo "Outros".
  - Bônus de proficiência calculado pelo nível, CA, iniciativa, deslocamento e percepção passiva.
  - Barras de **Vida**, **Dados de Vida** e **Espaços de Magia** com botões `« ‹ › »`; clique no número para ajustar valor atual e máximo.
  - Abas: **Combate** (ataques com rolagem de acerto e dano, filtro e rolador livre), **Habilidades**, **Magias**, **Inventário** e **Descrição**.

### Campanhas
- Até **6 campanhas**, com capa e descrição, no papel de Mestre ou Jogador.
- Página da campanha com abas **Personagens**, **Jogadores**, **Combates**, **Aventura** (anotações) e **Relatórios** (sessões).
- Painel **Resultados** com o histórico de todas as rolagens.
- **Combates:** rastreador de iniciativa com turnos. Aceita combatente avulso, ameaça (do SRD ou sua) ou personagem da campanha, puxando nome e PV da origem.
- **Escudo do Mestre:** referência rápida de CDs, condições e cobertura.
- **Jogar na Mesa Virtual:** guarda o link da sua sala do Owlbear Rodeo e a abre em nova aba.
- **Convidar Jogadores:** exibe um código de campanha (só funcional quando houver servidor).

### Ameaças e Homebrew
- Bestiário com 8 criaturas do SRD 5.1 (Goblin, Esqueleto, Lobo, Zumbi, Ogro, Cubo Gelatinoso, Aboleth, Dragão Vermelho Jovem), com filtro por tipo e busca.
- **Ameaças personalizadas:** crie criaturas próprias com imagem, tipo, tamanho, ND, CA, PV, deslocamento, 6 atributos e texto livre. Dá para editar, excluir e usá-las no combate. Aparecem no bestiário com o filtro **Homebrew**.
- Homebrew também para **Habilidades**, **Magias** e **Itens** (limite total de **50** itens).

### Patente
- Título de progressão (Plebeu, Aventureiro, Herói, Campeão, Lenda) conforme o maior nível entre seus personagens.

### Dados
Notação aceita no rolador livre: `1d20`, `2d8+3`, `1d6+1d4-1`, etc. Cada rolagem aparece num aviso, no histórico da campanha e como um d20 animado na tela (20 natural em verde, 1 natural em vermelho).

---

## Como rodar

O projeto inteiro é **um único arquivo HTML**, sem build e sem dependências instaláveis.

```bash
# opção 1: abrir direto no navegador
# clique duas vezes em index.html

# opção 2: servir localmente
npx serve .
# ou
python3 -m http.server 8000
```

Requer conexão com a internet apenas para carregar as fontes do Google Fonts (Cinzel, Cinzel Decorative e EB Garamond). Sem elas, o site usa fontes serifadas do sistema.

### Publicar
Como é um arquivo estático, serve qualquer hospedagem: GitHub Pages, Netlify, Vercel, Cloudflare Pages. Basta enviar o arquivo renomeado para `index.html`.

---

## Estrutura do código

Todo o código está em um único `.html`, organizado em blocos:

| Bloco | O que faz |
|---|---|
| `<style>` | Tokens de cor (tema escuro por padrão e variação clara pelo sistema), componentes, animações e a camada de estilo maximalista |
| Constantes | Perícias, atributos, criaturas do SRD, patentes e ícone do d20 |
| Estado (`S`) | Carregado e salvo no `localStorage` |
| Utilitários | `roll()` (dados), `lg()` (registra rolagem), `ask()` (modais), `rm()` (remoção animada), `rf()` (d20 animado), `upd()` (atualiza números da ficha sem redesenhar) |
| Páginas | Funções que devolvem HTML: `land`, `pChars`, `pCamps`, `pCamp`, `pMon`, `pHb`, `pRank`, `pFicha` |
| Ações (`H`) | Um objeto com um manipulador para cada `data-a` do HTML (delegação de eventos) |
| Final | Camada de brasas e círculo arcano, e a primeira renderização |

A renderização funciona por `R()`, que recria o HTML da tela atual; a ficha evita isso ao editar campos e atualiza só os números afetados.

---

## Modelo de dados

Tudo fica em `localStorage`, na chave `cw`:

```jsonc
{
  "user": { "n": "Marco" },
  "chars": [{
    "id": "ab12cd", "n": "Rick", "cl": "Mago", "race": "Elfo", "bg": "Sábio",
    "pl": "Marco", "lv": 3, "img": "data:image/jpeg;base64,...", "date": "13/10/26",
    "a": { "FOR": 10, "DES": 14, "CON": 12, "INT": 18, "SAB": 10, "CAR": 8 },
    "hp": [20, 30], "hd": [3, 3], "sl": [2, 2],
    "ca": 12, "desl": "9 m / 6 q",
    "sk": { "Arcanismo": 1 },          // 0 nenhuma, 1 proficiente, 2 expertise
    "o": { "Arcanismo": 2 },           // bônus extra
    "atk": [], "hab": [], "mag": [], "inv": [], "desc": ""
  }],
  "camps": [{
    "id": "...", "n": "...", "desc": "...", "role": "Mestre", "img": "...",
    "ch": ["<ids de personagens>"], "cb": [], "turn": 0,
    "notes": "", "rp": [], "ob": "<link da sala Owlbear>", "code": "CW-XXXXXX"
  }],
  "hb": { "Ameaças": [], "Habilidades": [], "Magias": [], "Itens": [] },
  "log": []                              // últimas 40 rolagens
}
```

Imagens são reduzidas no navegador antes de salvar (retrato e ameaça: 400 px; capa de campanha: 700 px; JPEG, qualidade 0,8) para caber no limite do `localStorage` (cerca de 5 MB).

---

## Acessibilidade e desempenho

- Navegação por teclado com foco visível; modais fecham com `Esc` e confirmam com `Enter`.
- Botões de ícone com rótulos para leitores de tela.
- Com "reduzir movimento" ativo no sistema, as animações, as brasas e os círculos giratórios são desligados.
- As animações usam principalmente `transform` e `opacity`. Em aparelhos mais antigos, remova a camada `.fx` (brasas e círculo de fundo) para ganhar fluidez.

---

## Limitações

- **Dados só no navegador:** trocar de navegador ou limpar os dados do site apaga tudo. Faça backup antes de limpar o cache.
- **Sem multijogador:** jogadores não veem as fichas uns dos outros; o Mestre ainda não enxerga as fichas dos jogadores; o código de convite não funciona.
- **Integração com o Owlbear Rodeo é só um link** para a sala. O envio de retratos e tokens direto ao mapa ainda não existe.
- A ficha não calcula PV, CA, espaços de magia por classe nem regras de classe automaticamente: os valores são digitados.
- Barras animadas usam `@property` do CSS, que exige navegador recente.

---

## Roadmap

1. **Servidor e contas:** login real, banco de dados, campanha compartilhada, painel do Mestre com acesso às fichas, convites e rolagens em tempo real.
2. **Extensão do Owlbear Rodeo:** a plataforma carregada dentro da sala como painel (via `manifest.json` hospedado publicamente e o SDK `@owlbear-rodeo/sdk`), para enviar retratos e criaturas ao mapa como tokens e sincronizar rolagens.
3. **Ficha mais inteligente:** cálculo automático por classe e nível, espaços de magia por nível, salvaguardas, inventário com peso e descanso curto/longo.
4. **Mais conteúdo:** magias, classes e mais criaturas do SRD, com importação e exportação (JSON) de personagens e homebrew.
5. **Trilha sonora:** player de áudio para ambientação (existiu na primeira versão e foi removido para focar no resto).

---

## Créditos e avisos legais

- Estrutura de telas inspirada no **CRIS** (plataforma de fichas de Ordem Paranormal), no **Owlbear Rodeo** (mesa virtual) e no **Bardify** (trilhas sonoras). Este projeto **não tem afiliação** com nenhum deles.
- Dungeons & Dragons é marca da Wizards of the Coast. Este projeto não é afiliado nem endossado por eles.
- Este trabalho inclui material do **System Reference Document 5.1** ("SRD 5.1") da Wizards of the Coast LLC, disponível em <https://dnd.wizards.com/resources/systems-reference-document>. O SRD 5.1 é licenciado sob a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/legalcode).
- Fontes: Cinzel, Cinzel Decorative e EB Garamond (Google Fonts, licença SIL Open Font).

## Licença

Defina aqui a licença do seu código (por exemplo, MIT) antes de publicar o repositório.
