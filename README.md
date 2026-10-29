# Poker Night 2 — Tradução PT-BR (Projeto Abandonado)

> ⚠️ **Este projeto foi descontinuado.** A tradução foi parcialmente concluída mas não pôde ser instalada com sucesso no jogo. O repositório é mantido para documentar o trabalho realizado e servir de referência para futuros tradutores.

---

## 📋 Sobre o projeto

Este projeto, assim como sua documentação, foram feitos inteiramente com o uso da plataforma CLAUDE.

O objetivo era traduzir os **1.934 linhas de diálogo** do jogo Poker Night 2 (Telltale Games, 2013) para o Português do Brasil, cobrindo os personagens principais:

- **CLAPTRAP** (Borderlands) — maioria das falas
- **ASH** (Evil Dead)
- **BROCK** (Venture Brothers)
- **SAM** (Sam & Max)
- **DEALER / HOST / WAITRESS**
- Expansões: Army of Darkness, Borderlands, Fables, Portal, Sam & Max, Venture Brothers, Walking Dead

**Cobertura atingida:** 943 textos únicos traduzidos (100% dos únicos), 1.874/1.934 linhas totais

**Versão testada:** Steam (PC)

---

## ✅ O que foi traduzido

| Arquivo | Descrição | Status |
|---|---|---|
| `banter_english.landb` | Diálogos gerais de mesa | ✅ Traduzido |
| `claptrap_english.landb` | Ações de pôquer do Claptrap | ✅ Traduzido |
| `ash_english.landb` | Falas do Ash | ✅ Traduzido |
| `brock_english.landb` | Falas do Brock | ✅ Traduzido |
| `sam_english.landb` | Falas do Sam | ✅ Traduzido |
| `dealer_english.landb` | Falas do dealer | ✅ Traduzido |
| `host_english.landb` | Falas do narrador | ✅ Traduzido |
| `responses_english.landb` | Respostas de reação | ✅ Traduzido |
| `expansion_*.landb` | Todas as 7 expansões | ✅ Traduzido |
| `ui_*.landb` | Interface do usuário | ⏭️ Não traduzido |

---

## ⚠️ Por que foi abandonado

O jogo usa o engine **Telltale Tool** com arquivos `.ttarch` (formato proprietário versão 9) com cifra **Blowfish7** customizada. Após engenharia reversa completa do formato e múltiplas tentativas de reempacotamento, o jogo iniciava mas **travava ao iniciar um torneio**.

Problema identificado mas não resolvido: os arquivos `.landb` modificados são carregados pelo engine, mas algo na estrutura binária causa um crash antes de renderizar os diálogos de jogo. Suspeita-se de incompatibilidade na estrutura de inter-entry dos `.landb` modificados.

A tradução russa de referência (Tolma4Team) funciona mas usa um formato `.ttarch` diferente (versão 8) e inclui fontes cirílicas e assets adicionais que estão fora do escopo deste projeto.

---

## 🛠️ Ferramentas desenvolvidas

O projeto produziu uma toolchain Python completa para o formato Telltale Tool:

- **`pn2_ttarch_tool.py`** — extrator, parser de `.landb` e gerador de CSV para tradução
- **`dialogos_pn2_ptbr.csv`** — banco de dados completo com todas as traduções

Estas ferramentas podem ser reutilizadas por futuros projetos de tradução para jogos Telltale.

---

## 📁 Arquivos do repositório

| Arquivo | Descrição |
|---|---|
| `pn2_ttarch_tool.py` | Toolchain Python completa |
| `dialogos_pn2_ptbr.csv` | CSV com 1.934 linhas traduzidas |
| `COMO_FUNCIONA.md` | Documentação técnica completa |
| `LICENSE` | Licença MIT |

---

## 🔧 Para quem quiser continuar

Se você quiser retomar este projeto, veja o `COMO_FUNCIONA.md` para entender toda a engenharia reversa realizada. O ponto de parada foi a bisecção para identificar qual `.landb` específico causa o crash ao iniciar o torneio.

**Ferramentas necessárias:**
- Python 3.10+
- [TTG Tools](https://github.com/HeitorSpectre/TTG-Tools) para reempacotar o `.ttarch`

---

## ⚠️ Avisos

- Tradução **não-oficial** — sem relação com a Telltale Games ou os detentores de IP dos personagens
- O jogo foi [removido da Steam](https://store.steampowered.com/news/4907) em 2018 por expiração de licenças
- Funciona apenas com cópias já adquiridas

---

## 📜 Licença

MIT License. Veja `LICENSE` para detalhes.

Poker Night 2 © Telltale Games. Todos os personagens pertencem aos seus respectivos detentores.
