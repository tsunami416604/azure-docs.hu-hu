---
title: Azure Service Fabric CLI- sfctl sa-cluster
description: Ismerje meg az sfctl, az Azure Service Fabric parancssori felület. Az önálló fürtök kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904927"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-fürt
Önálló Service Fabric-fürtök kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Config | A Service Fabric önálló fürtkonfigurációjának beszerezni. |
| config-upgrade | Kezdje el frissíteni a Service Fabric önálló fürtkonfigurációját. |
| frissítési állapot | A Service Fabric önálló fürt fürtkonfigurációs frissítési állapotának beszereznie. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster konfiguráció
A Service Fabric önálló fürtkonfigurációjának beszerezni.

A fürtkonfiguráció tartalmazza a fürt tulajdonságait, amelyek különböző csomóponttípusokat tartalmaznak a fürtön, biztonsági konfigurációkat, hibát és frissítési tartomány-topológiákat stb.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --configuration-api-version [Kötelező] | Az önálló fürt json-konfiguráció API-verziója. |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster konfigurációs frissítés
Kezdje el frissíteni a Service Fabric önálló fürtkonfigurációját.

Ellenőrizze a megadott konfigurációfrissítési paramétereket, és indítsa el a fürtkonfiguráció frissítését, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --cluster-config [Kötelező] | A fürtkonfiguráció. |
| --alkalmazás-egészségügyi politikák | JSON kódolású szótár az alkalmazás típusneve párok és a maximális százalékos nem kifogástalan, mielőtt a hiba kiemelése. |
| --delta-nem kifogástalan-csomópontok | A különbözeti állapot romlásának maximális anamvaló-százaléka a frissítés során. Az engedélyezett értékek nullától 100-ig eltérőek. |
| --health-check-retry | Az állapot-ellenőrzések végrehajtása között az állapotellenőrzések végrehajtása között, ha az alkalmazás vagy a fürt nem kifogástalan.  Alapértelmezett\: PT0H0M0s. |
| --health-check-stable | Az az idő, amerre az alkalmazásnak vagy fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományra lépne.  Alapértelmezett\: PT0H0M0s. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --health-check-wait | A frissítési tartomány befejezése után a várakozási idő időtartama az állapotellenőrzési folyamat megkezdése előtt.  Alapértelmezett\: PT0H0M0s. |
| --idő-out -t | Alapértelmezett\: 60. |
| --egészségtelen alkalmazások | A nem megfelelő állapotú alkalmazások maximálisan engedélyezett százaléka a frissítés során. Az engedélyezett értékek nullától 100-ig eltérőek. |
| --nem kifogástalan-csomópontok | A nem megfelelő állapotú csomópontok maximálisan engedélyezett százaléka a frissítés során. Az engedélyezett értékek nullától 100-ig eltérőek. |
| --upgrade-domain-delta-unhealthy-csomópontok | A frissítési tartomány különbözeti állapotának romlása a frissítés során engedélyezett maximális százalékos aránya. Az engedélyezett értékek nullától 100-ig eltérőek. |
| --upgrade-domain-időout | Az egyes frissítési tartományoknak a FailureAction végrehajtása előtt be kell fejezniük az időt.  Alapértelmezett\: PT0H0M0s. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |
| --upgrade-időeltetés | A teljes frissítés befejezéséhez szükséges idő a FailureAction végrehajtása előtt.  Alapértelmezett\: PT0H0M0s. <br><br> Először egy ISO 8601 időtartamot képviselő karakterláncként értelmezik. Ha ez nem sikerül, akkor a rendszer a teljes ezredmásodpercszámot jelző számként értelmezi. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Fürtkonfigurációs frissítés indítása
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-cluster frissítési állapot
A Service Fabric önálló fürt fürtkonfigurációs frissítési állapotának beszereznie.

A fürt konfigurációs frissítési állapotának a Service Fabric önálló fürt részleteinek beszerezése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --idő-out -t | A művelet végrehajtásához a kiszolgáló időtúlideje másodpercben. Ez az időkérés azt az időtartamot adja meg, amelyet az ügyfél hajlandó megvárni a kért művelet befejezésére. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresés | Növelje a naplózás részletességét az összes hibakeresési napló megjelenítéséhez. |
| --segítség -h | A súgóüzenet megjelenítése és kilépés. |
| --kimenet -o | Kimeneti formátum.  Megengedett\: értékek json, jsonc, táblázat, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. További\:információt és példákat a http //jmespath.org/ című témakörben talál. |
| --bőbeszédű | A naplózás igézetének növelése. Használja a --debug fájlt a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI.Set up the Service Fabric CLI.
- Ismerje meg, hogyan használhatja a Service Fabric CLI-t a [mintaparancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.