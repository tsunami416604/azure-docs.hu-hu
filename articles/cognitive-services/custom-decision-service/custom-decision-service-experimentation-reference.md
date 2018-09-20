---
title: Kísérletezési – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Ez a cikk a Custom Decision Service a Kísérletezési készült útmutató.
services: cognitive-services
author: marco-rossi29
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: eec2c82b779fa5421bc9ac58107ef56f8c71bd1e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366555"
---
# <a name="experimentation"></a>Kísérletezés

A következő, az elméleti [környezetfüggő bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), Custom Decision Service többször is figyelembe veszi a környezet végrehajt egy műveletet és betartja a kiválasztott művelet egy fejében. Például, a tartalom személyre szabása: a környezet ismerteti egy felhasználói műveletek jelölt történetek, és a ellenszolgáltatás méri, hogy mekkora a felhasználó a javasolt történetet tetszését.

A Custom Decision Service a házirendet, hoz létre, mivel a környezetek műveletek vannak leképezve. Egy adott cél szabályzattal szeretné tudni, hogy a várt ellenszolgáltatás. Egy módszer a fejében, hogy online-házirend használata, és azt válassza ki a műveletek (például ajánlott történetek a felhasználók számára). Azonban az ilyen online értékelési lehet költséges két oka:

* Egy nem tesztelt, kísérleti házirend számára teszi elérhetővé.
* Ez nem méretezhető, több cél házirendek kiértékelése.

Szabályzat kiértékelés egy alternatív költségei. Ha egy meglévő online rendszer hajtsa végre a naplózási házirend naplóinak, a szabályzat kiértékelés megbecsülheti cél új szabályzatok a várt szüntelenül növekvő.

A naplófájl használatával Kísérletezési kívánja a házirendet a legmagasabb becsült, a várt ellenszolgáltatás található. Cél szabályzatok vannak paraméterezni [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumentumokat. Az alapértelmezett módban, a szkript úgy teszteli a Vowpal Wabbit argumentumok számos, úgy, hogy a `--base_command`. A szkript a következő műveleteket hajtja végre:

* Automatikusan észleli szolgáltatásokat névtereket az első `--auto_lines` a bemeneti fájl.
* Első ismétlés végrehajtja a hyper-paraméterek (`learning rate`, `L1 regularization`, és `power_t`).
* Szabályzat-kiértékelés tesztek `--cb_type` (más néven inverz upsell pontszám (`ips`) vagy kétszer robusztus (`dr`). További információkért lásd: [környezetfüggő Bandit példa](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Tesztek melléklet.
* Tesztek négyzetösszege az interaktív funkciókat:
   * **találgatásos fázis**: teszteli, az összes kombinációk `--q_bruteforce_terms` párokból vagy kevesebb.
   * **mohó fázis**: a legjobb pár ad hozzá, amíg be nem nincs javulás `--q_greedy_stop` kerekít.
* Végrehajtja a második ismétlés hiperparaméterekkel (`learning rate`, `L1 regularization`, és `power_t`).

A következők néhány Vowpal Wabbit argumentum a paramétereket, amelyek vezérlik a következő lépéseket:
- Példa adatkezelési lehetőségeket:
  - megosztott névterek
  - a művelet névterek
  - marginális névterek
  - négyzetösszege funkciók
- A szabály beállítások frissítése
  - tanulási ráta
  - L1 regularizációs
  - t energiaellátási érték

A fenti argumentum egy részletes ismertetése: [Vowpal Wabbit parancssori argumentumok](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Előfeltételek
- Vowpal Wabbit: Telepítve van, és az elérési útra.
  - Windows: [használja a `.msi` telepítő](https://github.com/eisber/vowpal_wabbit/releases).
  - Más platformok: [lekérése a forráskód](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Telepítve van, és az elérési útra.
- NumPy: Használja a Csomagkezelőt a választott.
- A *Microsoft/MOLWT-ds* tárházat: [klónozza az adattárat](https://github.com/Microsoft/mwt-ds).
- Döntési szolgáltatás JSON-naplófájl: alapértelmezés szerint tartalmazza az alapszintű parancs `--dsjson`, amely lehetővé teszi a döntési szolgáltatás JSON-elemzés, a bemeneti adatfájlt. [Ebben a formátumban például Get](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Használat
Lépjen a `mwt-ds/DataScience` , és futtassa `Experimentation.py` megfelelő argumentumokkal, ahogy az az alábbi kódot:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

Az eredmények naplózása a rendszer hozzáfűzi a *mwt-ds/DataScience/experiments.csv* fájlt.

### <a name="parameters"></a>Paraméterek
| Input (Bemenet) | Leírás | Alapértelmezett |
| --- | --- | --- |
| `-h`, `--help` | Súgóüzenet megjelenítése, és zárja be. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Adatok elérési útja (`.json` vagy `.json.gz` -formátum – minden sorban van egy `dsjson`). | Szükséges |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Alap Vowpal Wabbit parancsot.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Használandó párhuzamos folyamatok száma. | Logikai processzorok |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | A szolgáltatás névterek megosztott (például `abc` azt jelenti, hogy a névterek `a`, `b`, és `c`).  | Automatikus észlelésű adatfájlból |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | A művelet a szolgáltatás névterek. | Automatikus észlelésű adatfájlból |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Marginális funkció névterek. | Automatikus észlelésű adatfájlból |  
| `--auto_lines AUTO_LINES` | Automatikus észlelésű szolgáltatásokat névtereket beolvasni adatokat fájl sorainak száma. | `100` |  
| `--only_hp` | Szögtartomány csak hiperparaméterekkel keresztül (`learning rate`, `L1 regularization`, és `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Tanulási tartományt, pozitív értékek `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | A pozitív értékek L1 regularizációs tartományba `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | A pozitív értékek Power_t tartományba `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Találgatásos fázisban teszteléséhez négyzetösszege párok száma. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Kerekít nélkül fejlesztések, utána négyzetösszege mohó keresési fázisa véget ér. | `3` |  

### <a name="examples"></a>Példák
Az előre beállított alapértékeket használata:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Év, a Vowpal Wabbit is betöltheti `.json.gz` fájlok:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Szögtartomány csak keresztül hiperparaméterekkel való (`learning rate`, `L1 regularization`, és `power_t`, 2. lépés után leállítása):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
