# finpec_fod_redesign

Refatoração do front-end do **FinPec On Demand (FOD)** no FINPEC Design System.

## Conteúdo

| Arquivo | O que é |
|---|---|
| `FOD Prototipo.html` | Protótipo navegável **offline** (arquivo único). Abra direto no navegador. Barra superior: layout (Web · PWA · Web responsive), largura, perfil (CEO · Diretor · Colaborador), tema escuro e simulação offline. |
| `FOD Prototipo.dc.html` | Fonte editável do protótipo (depende de `support.js`, `_ds/` e `assets/`; servir por HTTP). |
| `FOD Redesign.dc.html` | Canvas com as telas estáticas lado a lado (artboards 1a–1r). |
| `FOD_Especificacao_Implementacao.md` | Requisitos de implementação `FOD-IMP-NNNN` para guiar o desenvolvimento. |
| `_ds/` | Cópia do FINPEC Design System usada pelos arquivos `.dc.html`. |
| `assets/` | Logos e imagens. |

## Rodar localmente

```bash
npx serve .
# abrir http://localhost:3000/FOD%20Prototipo.dc.html
```

O `FOD Prototipo.html` não precisa de servidor.

## Referências

- Design system: https://github.com/FINPEC-JK/webresp_design-system
- Especificação funcional: FOD V1 (16/10/2026)
