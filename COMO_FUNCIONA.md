# COMO_FUNCIONA.md — Poker Night 2 PT-BR

Documentação técnica completa do projeto de tradução PT-BR do Poker Night 2.

---

## 1. Estrutura do jogo

O Poker Night 2 usa o engine **Telltale Tool** com arquivos `.ttarch` na pasta:
```
<jogo>\Pack\default\
├── 1_cpoker2_pc_anichore.ttarch   ← animações e choreografia
├── 2_cpoker2_pc_data.ttarch       ← dados gerais (landb, lenc, prop, etc.)
├── 3_cpoker2_pc_voice.ttarch      ← áudio de vozes
├── 4_cpoker2_pc_ms.ttarch
├── 5_cpoker2_pc_txmesh.ttarch     ← texturas e meshes
└── 6_pc_shader.ttarch             ← shaders
```

Os diálogos traduzíveis estão em **`2_cpoker2_pc_data.ttarch`**, nos arquivos `.landb`.

---

## 2. Formato .ttarch (versão 9)

### Header
```
version(4)       = 9
info_mode(4)     = 1  ← info_table cifrada
type3(4)         = 2
files_mode(4)    = 2  ← chunks cifrados + comprimidos
tot_idx(4)       = número de chunks (ex: 273)
chunk_sizes[N](4 each)
total_content(4)
d1(4) d2(4)              ← v4 dummies
d3(4) d4(4) chunksz_kb(4) ← v7 (chunksz = 64KB)
d5(1)                    ← v8
d6(4)                    ← v9
info_size(4)
info_zsize(4)
```

### Info_table
- Comprimida com raw deflate (zlib sem header)
- Depois **cifrada com Blowfish7** (apenas blocos inteiros de 8 bytes; tail sem cifrar)
- Formato decifrado: `folders(4) + [folder_name] + files(4) + [name + zero(4) + offset(4) + size(4)] * N`

### Dados dos arquivos
- Divididos em chunks de 64KB
- Cada chunk: **Blowfish7** → **raw deflate nível 9**

### ⚠️ Bug crítico no repack (resolvido)
O campo `info_size` deve corresponder ao tamanho REAL do dado decifrado, incluindo o tail de bytes não cifrados. Cifrar apenas blocos inteiros e gravar `info_size = len(dado_original)` — não o tamanho paddiado.

---

## 3. Blowfish7 (variante Telltale Tool ≥ v7)

Diferença da implementação padrão:
- `init`: `S[0][118] = bswap(S[0][118])` antes do XOR com a chave
- `decipher7`: P-boxes reordenados: `i=4→P[2]`, `i=3→P[1]`, `i=2→P[4]`; final XOR `P[3]` e `P[0]`
- `encipher7`: inverso do decipher7

### Chave Blowfish7 do Poker Night 2 (cpoker2)
```
82 CE 99 99 86 DE 9C B7 EB BC E4 CF 97 D7 85 9A
CE 96 92 D9 AF DE AA E8 B5 90 DA BA AB 9E A4 99
CB AA 94 C1 CA D7 B2 BC E4 DF DD DE 69 75 B7 DB
AA C5 98 9C E4 EB 8F
```
*(55 bytes — extraída via ttarchext/Keyaku)*

---

## 4. Formato .landb (ERTM)

Cada arquivo `.landb` contém entradas de diálogo com estrutura:

```
[ERTM header 0x68 bytes]
Para cada entrada:
  key(4)
  0x10(4)           ← marcador fixo
  spk_len(4)        ← tamanho do nome do speaker
  speaker(spk_len)  ← ex: "CLAPTRAP"
  txt_total(4)      ← txt_nolen + 8
  txt_nolen(4)      ← tamanho do texto SEM null terminator
  text(txt_nolen)   ← texto em Latin-1/Windows-1252
  [inter-entry: sound hashes, .anm refs, etc.]
```

### ⚠️ Pontos críticos para modificação

1. **`txt_nolen` NÃO inclui `\x00`** — gravar apenas `len(texto_bytes)` sem null
2. **`txt_total = txt_nolen + 8`** — o footer de 8 bytes é fixo
3. **Encoding: Latin-1/Windows-1252** — acentos PT-BR são single-byte (á=0xE1, ã=0xE3, etc.)
4. **Os bytes inter-entry devem ser preservados exatamente** — qualquer alteração causa crash

---

## 5. Workflow de tradução

```bash
# 1. Extrair os .landb com TTG Tools (ou pn2_ttarch_tool.py)
python pn2_ttarch_tool.py extract 2_cpoker2_pc_data.ttarch -o extraido/ --ext landb

# 2. Exportar CSV para tradução
python pn2_ttarch_tool.py export-csv 2_cpoker2_pc_data.ttarch -o dialogos.csv

# 3. Preencher coluna 'traducao' no CSV

# 4. Aplicar traduções nos .landb
python pn2_ttarch_tool.py apply-csv dialogos.csv -p extraido/

# 5. Reempacotar com TTG Tools
#    Empacotador → versão 9, Compactar ✓, Criptografar ✓, Chave: 51. Poker Night 2
```

---

## 6. Ferramentas de referência

- **[TTG Tools](https://github.com/HeitorSpectre/TTG-Tools)** — ferramenta visual para .ttarch (recomendada para repack)
- **[ttarchext](https://github.com/Keyaku/ttarchext)** — referência de engenharia reversa do formato
- **Tradução russa de referência:** `1_cpoker2_Tolma4Team.ttarch` (Tolma4Team) — usa .ttarch versão 8

---

## 7. Status do problema não resolvido

O jogo **inicia e chega ao menu principal** com os `.landb` modificados, mas **trava ao iniciar um torneio**.

Hipóteses não descartadas:
- Algum `.landb` específico tem corrupção nos bytes inter-entry após o texto modificado
- O engine valida checksums dos textos que não replicamos
- Incompatibilidade no tamanho do buffer de alguma fala muito longa

**Próximo passo para quem quiser continuar:** bisecção binária dos 17 `.landb` modificados para identificar qual arquivo específico causa o crash. Com os grupos A (primeiros 9) e B (últimos 8), são necessários ~4 testes para isolar o arquivo.

---

## 8. Arquivos do projeto

| Arquivo | Descrição |
|---|---|
| `pn2_ttarch_tool.py` | Toolchain completa: list, extract, stats, export-csv, apply-csv, repack, build-overlay |
| `dialogos_pn2_ptbr.csv` | 1.934 linhas com traduções PT-BR (colunas: arquivo, offset, key, speaker, original, traducao) |

