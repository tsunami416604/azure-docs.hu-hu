---
title: Az Azure Service Fabric parancssori felület - sfctl sa-fürt |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl önálló fürt parancsokat.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: ce10e2c24e89140357df3fa6b724a1f89f389a50
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275482"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-fürt
Önálló Service Fabric-fürtök kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| config | A Service Fabric önálló fürtkonfiguráció beolvasása. |
| konfiguráció frissítése | Indítsa el az önálló Service Fabric-fürt konfigurációjának frissítése. |
| frissítés – állapot | A fürt konfiguráció frissítési önálló Service Fabric-fürt állapotának lekérése. |

## <a name="sfctl-sa-cluster-config"></a>sfctl-sa-cluster config
A Service Fabric önálló fürtkonfiguráció beolvasása.

A fürt konfigurációját tartalmazza a fürt tulajdonságait, amely tartalmazza a különböző típusok fürtöt, biztonsági konfigurációk, hiba, és a frissítési tartomány topológiák, stb.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – configuration-api-version [kötelező] | Önálló fürtkonfiguráció json API verziója. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-frissítés
Indítsa el az önálló Service Fabric-fürt konfigurációjának frissítése.

Érvényesítse a megadott konfiguráció frissítési paramétereket, és indítsa el a fürtkonfiguráció frissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] fürt-config | A fürt konfigurációját. |
| – alkalmazás állapotházirendeket | JSON-kódolású szótár párok alkalmazástípus neve és a nem megfelelő, hiba kiváltása előtt maximális százalékos aránya. |
| – a különbözeti sérült csomópontok | A maximális százalékos aránya a különbözeti egészségügyi teljesítménycsökkenés a frissítés során engedélyezett. Megengedett értékek: 100 nulla egész számok. |
| --health-check-retry | Mennyi ideig állapotellenőrzéseket hajthat végre, ha az alkalmazás vagy a fürt nem kifogástalan való próbálkozások között.  Alapértelmezett\: PT0H0M0S. |
| – állapot-ellenőrzés – stabil | Mennyi ideig, hogy az alkalmazás vagy a fürt kell megfelelő állapotú marad a következő frissítési tartományra abból a frissítés előtt.  Alapértelmezett\: PT0H0M0S. <br><br> Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. |
| – állapot-ellenőrzés-wait | Az eltelt idő eltelte után a frissítési tartomány befejezése előtt a folyamat kezdési állapotát ellenőrzi.  Alapértelmezett\: PT0H0M0S. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |
| – nem megfelelő alkalmazások | A maximálisan engedélyezett sérült alkalmazások aránya a frissítés során. Megengedett értékek: 100 nulla egész számok. |
| – sérült csomópontok | A maximális százalékos aránya sérült csomópontok a frissítés során engedélyezett. Megengedett értékek: 100 nulla egész számok. |
| --upgrade-domain-delta-unhealthy-nodes | A maximális százalékos aránya a frissítési tartomány különbözeti egészségügyi teljesítménycsökkenés a frissítés során engedélyezett. Megengedett értékek: 100 nulla egész számok. |
| --frissítés-tartomány-időkorlátja | Mennyi ideig mindegyik frissítési tartományon van befejezését, mielőtt FailureAction hajtja végre.  Alapértelmezett\: PT0H0M0S. <br><br> Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. |
| --frissítés – időtúllépés | Mennyi ideig a teljes frissítés rendelkezik befejezését, mielőtt FailureAction hajtja végre.  Alapértelmezett\: PT0H0M0S. <br><br> Először kerül értelmezésre egy karakterlánc, amely az ISO 8601 időtartama. Ha ez nem sikerül, majd kerül értelmezésre egy számot jelölő ezredmásodperc teljes száma. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

### <a name="examples"></a>Példák

Indítsa el a fürt konfigurációjának frissítése

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-fürt frissítése – állapot
A fürt konfiguráció frissítési önálló Service Fabric-fürt állapotának lekérése.

A fürt konfigurációjának lekérése önálló Service Fabric-fürt frissítési állapotának részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |


## <a name="next-steps"></a>További lépések
- [Állítsa be a](service-fabric-cli.md) a Service Fabric parancssori felület.
- Ismerje meg, hogyan használható a Service Fabric parancssori felület használatával a [-szkript minták](/azure/service-fabric/scripts/sfctl-upgrade-application).