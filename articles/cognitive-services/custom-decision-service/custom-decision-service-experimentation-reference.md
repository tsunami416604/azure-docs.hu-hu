---
title: Kísérletezés – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Ez a cikk egy, a Custom Decision Servicesal való kísérletezéshez nyújt útmutatót.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: e6e8e7d0d5b969464ba9183ccae9080f58f786a0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707278"
---
# <a name="experimentation"></a>Kísérletezés

A [környezetfüggő banditák (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/)elméletét követve a Custom Decision Service ismételten betartja a kontextust, végrehajt egy műveletet, és betartja a kiválasztott műveletre vonatkozó jutalmat. Ilyen például a tartalom személyre szabása: a kontextus leírja a felhasználót, a műveletek a jelölt történetek, a jutalmak pedig azt mérik, hogy a felhasználó mennyire tetszett a javasolt történet.

Custom Decision Service létrehoz egy házirendet, mivel a kontextusokból a műveletekre mutat. Egy konkrét célként megadott házirenddel szeretné megismerni a várt jutalmat. A jutalmak becslésének egyik módja, ha az online házirendet használja, és lehetővé teszi a műveletek (például a felhasználóknak szóló történetek) használatát. Az ilyen online értékelés azonban két okból is költséges lehet:

* Lehetővé teszi a felhasználók számára a nem tesztelt, kísérleti szabályzatok betartását.
* Nem méretezhető több cél házirend kiértékelésére.

A házirenden kívüli kiértékelés egy alternatív paradigma. Ha olyan meglévő online rendszerből származó naplókat használ, amely egy naplózási házirendet követ, a szabályzaton kívüli kiértékeléssel megbecsülheti az új megcélzott házirendek várt nyereményeit.

A naplófájl használatával a kísérletezés arra törekszik, hogy megtalálja a szabályzatot a legmagasabb becsült, várható jutalommal. A [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumentumai a célként megadott házirendek paraméterei. Az alapértelmezett módban a parancsfájl különböző Vowpal Wabbit-argumentumokat tesztel, ehhez fűzze hozzá a `--base_command`következőhöz:. A szkript a következő műveleteket hajtja végre:

* Az automatikusan észleli a funkciók névtereit a bemeneti `--auto_lines` fájl első soraiból.
* Végrehajtja az első áthúzást a Hyper`learning rate`- `L1 regularization`parameters ( `power_t`,, és).
* Tesztelési házirend `--cb_type` kiértékelése (inverz felkészültség`ips`pontszáma () vagy kétszeresen robusztus (`dr`). További információ: [környezetfüggő bandita-példa](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Melléklet szélzetszámokra amelyeknek tesztek.
* Másodfokú interakciós funkciók tesztelése:
   * **találgatásos kényszerítés fázisa**: A páros vagy a `--q_bruteforce_terms` kevesebb kombinációt teszteli.
   * **kapzsi fázis**: Hozzáadja a legjobb párokat, amíg a `--q_greedy_stop` kerekítés nem javul.
* Végrehajt egy második lendületet a Hyper-Parameters`learning rate`( `L1 regularization`,, `power_t`és).

Az alábbi lépéseket vezérlő paraméterek tartalmaznak néhány Vowpal Wabbit argumentumot:
- Példa a manipulációs lehetőségekre:
  - megosztott névterek
  - műveleti névterek
  - marginális névterek
  - másodfokú funkciók
- Szabály beállításainak frissítése
  - tanulási arány
  - L1 regularizációs
  - t energiaellátási érték

A fenti argumentumok részletes ismertetését lásd: [Vowpal Wabbit parancssori argumentumok](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Előfeltételek
- Vowpal Wabbit: Az elérési útra.
  - Windows: [Használja a `.msi` telepítőt](https://github.com/eisber/vowpal_wabbit/releases).
  - Egyéb platformok: [A forráskód](https://github.com/JohnLangford/vowpal_wabbit/releases)beolvasása.
- Python 3: Az elérési útra.
- NumPy Használja az Ön által választott csomagkezelő eszközt.
- A *Microsoft/MWT-DS* adattár: [A](https://github.com/Microsoft/mwt-ds)tárház klónozása.
- Döntési szolgáltatás JSON-naplófájlja: Alapértelmezés szerint az alapparancs tartalmazza `--dsjson`, amely lehetővé teszi a döntési szolgáltatás JSON-elemzését a bemeneti adatfájlban. [Példa erre a formátumra](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Használat
Nyissa `mwt-ds/DataScience` meg a `Experimentation.py` parancsot, és futtassa a megfelelő argumentumokat a következő kódban részletezett módon:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Az eredmények naplóját a rendszer hozzáfűzi a *MWT-DS/DataScience/experiments. csv* fájlhoz.

### <a name="parameters"></a>Paraméterek
| Input (Bemenet) | Leírás | Alapértelmezett |
| --- | --- | --- |
| `-h`, `--help` | Súgó üzenet megjelenítése és kilépés. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Az adatfájl elérési `.json.gz` útja (`.json` vagy formátuma – `dsjson`minden sor a). | Kötelező |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Alap Vowpal Wabbit parancsa.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | A használandó párhuzamos folyamatok száma. | Logikai processzorok |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | A megosztott szolgáltatás `abc` névterei (például `a`névterek, `b`és `c`).  | Adatok automatikus észlelése adatfájlból |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Művelet funkcióinak névterei. | Adatok automatikus észlelése adatfájlból |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | A marginális funkció névterei. | Adatok automatikus észlelése adatfájlból |  
| `--auto_lines AUTO_LINES` | A funkciók névterének automatikus észlelésére szolgáló adatfájl-sorok száma. | `100` |  
| `--only_hp` | Csak a Hyper-parameters (`learning rate`, `L1 regularization`, és `power_t`) esetén legyen elsöpörve. | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | A tanulási sebesség tartománya pozitív `min,max,steps`értékként. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | Az L1 regularizációs pozitív értékként `min,max,steps`van megjelölve. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | A Power_t értéke pozitív érték `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | A találgatásos kényszerítési fázisban tesztelni kívánt másodfokú párok száma. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | A kilépések anélkül, hogy a másodfokú kapzsi keresési fázis leálljon. | `3` |  

### <a name="examples"></a>Példák
Az előre beállított alapértelmezett értékek használata:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Ezzel egyenértékű a Vowpal Wabbit is `.json.gz` képes a fájlok betöltésére:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Csak a Hyper-parameters (`learning rate`, `L1 regularization`, és `power_t`, a 2. lépés után történő leállítás) esetén:
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
