---
title: Kísérletezhet - Azure kognitív szolgáltatások |} Microsoft Docs
description: Ez a cikk útmutatást adnak Azure egyéni döntési szolgáltatás kísérletezhet.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349254"
---
# <a name="experimentation"></a>Kísérletezés

A következő a elvből [környezetfüggő bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), egyéni döntési szolgáltatás ismételten figyelembe veszi a környezetet, végrehajt egy műveletet, és figyelembe veszi a kiválasztott művelet egy ellenszolgáltatás. Példa: a tartalom személyre szabása: a környezet ismerteti egy felhasználói műveletek jelölt szövegek, és a ellenszolgáltatás méri, hogy mennyi a felhasználó a javasolt szövegegység tetszését.

Egyéni döntési szolgáltatás egy házirendet, hoz létre, mivel műveletek hozzárendeli őket a környezetben. Egy adott cél házirendnek meg szeretné ismerni a várt ellenszolgáltatás. Egy becsléséhez a ellenszolgáltatás módja a házirend online használatához, és hagyja, hogy válassza a műveletek (például: javasoljuk szövegek a felhasználók számára). Azonban az ilyen online értékelési költséges két oka lehet:

* Azt mutatja, hogy a felhasználók egy nem tesztelt, kísérleti házirendet.
* Azt nem méretezhető a több cél házirendek kiértékelése.

Házirend értékelés egy alternatív paradigma. Ha egy meglévő online rendszerből naplókat, naplózási házirend az alábbi, házirend értékelés megbecsülheti új cél házirendek várható megtérüléséhez.

A naplófájl használatával kísérletezhet kívánja a házirendet a legmagasabb becsült, a várt ellenszolgáltatás található. Cél házirendek paraméterezni vannak [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argumentumokat. Az alapértelmezett módban, a parancsfájl Vowpal Wabbit argumentumok számos tesztek történő hozzáfűzésével a `--base_command`. A parancsfájl a következő műveleteket hajtja végre:

* Automatikusan észleli az első névterek szolgáltatások `--auto_lines` a bemeneti fájl.
* Első ismétlés végez keresztül a hyper-paraméterek (`learning rate`, `L1 regularization`, és `power_t`).
* Teszteli a házirendek értékelésének `--cb_type` (más néven inverz innovációkká alakuljon pontszám (`ips`), vagy kétszer robusztus (`dr`). További információkért lásd: [környezetfüggő Bandit példa](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Tesztek melléklet.
* Tesztek négyzetösszege az interaktív funkciókat:
   * **találgatásos fázis**: teszteli, az összes kombinációk `--q_bruteforce_terms` párok vagy kisebb értékre.
   * **mohó fázis**: a legjobb pár ad hozzá, amíg nincs javulás `--q_greedy_stop` kerekít.
* Egy második ismétlés végez keresztül a hyper-paraméterek (`learning rate`, `L1 regularization`, és `power_t`).

Ezeket a lépéseket szabályozó paraméter tartalmaz néhány Vowpal Wabbit argumentum:
- Példa adatkezelési lehetőségeket:
  - megosztott névterekről
  - a művelet névterek
  - marginális névterek
  - négyzetösszege szolgáltatások
- A szabály beállításainak frissítése
  - tanulási rátát
  - 1. szintű exportügyletek
  - t power érték

Egy részletes ismertetése a fenti argumentumok [Vowpal Wabbit parancssori argumentumok](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Előfeltételek
- Vowpal Wabbit: Telepítve és az elérési úton.
  - Windows: [használja a `.msi` installer](https://github.com/eisber/vowpal_wabbit/releases).
  - Más platformokon: [beolvasása a forráskód](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Telepítve és az elérési úton.
- NumPy: A package manager, az Ön által választott használja.
- A *Microsoft/MOLWT-ds* tárház: [a tárház klónozása](https://github.com/Microsoft/mwt-ds).
- Döntési szolgáltatás JSON-naplófájl: alapértelmezés szerint az alap parancs tartozik `--dsjson`, amely lehetővé döntési szolgáltatás JSON elemzése a bemeneti adatfájlt. [Ezt a formátumot például Get](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Használat
Ugrás a `mwt-ds/DataScience` , és futtassa `Experimentation.py` vonatkozó argumentumok, ahogy az az alábbi kódban:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

A napló az eredményeket a rendszer hozzáfűzi a *mwt-ds/DataScience/experiments.csv* fájlt.

### <a name="parameters"></a>Paraméterek
| Input (Bemenet) | Leírás | Alapértelmezett |
| --- | --- | --- |
| `-h`, `--help` | Egy súgóüzenet és kilépési megjelenítése. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Fájl elérési útja (`.json` vagy `.json.gz` - soronként formátuma egy `dsjson`). | Szükséges |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Alap Vowpal Wabbit parancsot.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Száma párhuzamos folyamatok használatára. | Logikai processzorok |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Megosztott szolgáltatás névtereket (például `abc` azt jelenti, hogy a névterek `a`, `b`, és `c`).  | Az automatikus észlelés adatfájlból |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | A művelet a szolgáltatás névtereket. | Az automatikus észlelés adatfájlból |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Marginális szolgáltatás névtereket. | Az automatikus észlelés adatfájlból |  
| `--auto_lines AUTO_LINES` | Az automatikus észlelés szolgáltatások névterek beolvasni adatok fájl sorok száma. | `100` |  
| `--only_hp` | Csak a hyper-paraméterek keresztül Sweep (`learning rate`, `L1 regularization`, és `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Tanulási tartományt pozitív értékként `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | 1. szintű exportügyletek tartományba pozitív értékek `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Pozitív értéket Power_t tartományba `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Találgatásos fázisban teszteléséhez négyzetösszege párok száma. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | A kerekítés fejlesztései, mely után négyzetösszege mohó keresési fázis megáll nélkül. | `3` |  

### <a name="examples"></a>Példák
Az előre beállított alapértelmezett értékeket szeretné használni:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Év, is fogadására képes a Vowpal Wabbit `.json.gz` fájlok:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Csak a hyper-paraméterek keresztül továbbítsa (`learning rate`, `L1 regularization`, és `power_t`, leállítása után a 2. lépés):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
