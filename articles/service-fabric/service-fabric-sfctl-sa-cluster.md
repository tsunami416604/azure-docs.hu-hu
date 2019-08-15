---
title: Azure Service Fabric CLI-sfctl SA-cluster | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl önálló fürtjének parancsait.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 902ebab5dc12d7649edd0ed6e594e663e5332ce3
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035233"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-fürt
Önálló Service Fabric-fürtök kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| config | A Service Fabric önálló fürt konfigurációjának beolvasása. |
| konfiguráció – frissítés | Service Fabric önálló fürt konfigurációjának frissítése megkezdődött. |
| upgrade-status | Service Fabric önálló fürthöz tartozó fürtkonfiguráció frissítési állapotának beolvasása. |

## <a name="sfctl-sa-cluster-config"></a>sfctl SA – fürt konfigurációja
A Service Fabric önálló fürt konfigurációjának beolvasása.

A fürtkonfiguráció olyan fürtöt tartalmaz, amely különböző csomópont-típusokat tartalmaz a fürtön, a biztonsági konfigurációk, a hibák és a frissítési tartomány topológiái stb.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Configuration-API-Version [kötelező] | Az önálló fürt JSON-konfigurációjának API-verziója. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl SA – fürt konfigurációja – frissítés
Service Fabric önálló fürt konfigurációjának frissítése megkezdődött.

Érvényesítse a megadott konfiguráció frissítési paramétereit, és indítsa el a fürt konfigurációjának frissítését, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --cluster-config [kötelező] | A fürtkonfiguráció. |
| --application-health-policies | A hiba kiemelése előtt JSON-kódolású, az alkalmazásnév és a maximális százalékos arányt tartalmazó szótár. |
| --Delta-sérült-csomópontok | A Delta állapot csökkenésének maximális megengedett százaléka a frissítés során. Az engedélyezett értékek a nulla és a 100 közötti egész értékek. |
| --health-check-retry | Az állapot-ellenőrzések elvégzésére irányuló kísérletek közötti időtartam, ha az alkalmazás vagy a fürt állapota nem kifogástalan.  Alapértelmezett\: PT0H0M0S. |
| --állapot-passzolás – stabil | Az az időtartam, ameddig az alkalmazásnak vagy a fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományba kerül.  Alapértelmezett\: PT0H0M0S. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --health-check-wait | Az a várakozási idő, ameddig a frissítési tartomány befejezése után meg kell várni az állapot-ellenőrzési folyamat megkezdése előtt.  Alapértelmezett\: PT0H0M0S. |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |
| – nem megfelelő állapotú alkalmazások | A nem kifogástalan állapotú alkalmazások maximális megengedett százalékos aránya a frissítés során. Az engedélyezett értékek a nulla és a 100 közötti egész értékek. |
| – nem megfelelő állapotú csomópontok | A nem kifogástalan állapotú csomópontok megengedett százalékos aránya a frissítés során. Az engedélyezett értékek a nulla és a 100 közötti egész értékek. |
| --upgrade-domain-delta-unhealthy-nodes | A frissítési tartomány változási állapotának maximális megengedett százaléka a frissítés során. Az engedélyezett értékek a nulla és a 100 közötti egész értékek. |
| – frissítés – tartomány – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a FailureAction végrehajtása előtt kell végrehajtani.  Alapértelmezett\: PT0H0M0S. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| – frissítés – időtúllépés | Az a időtartam, ameddig a teljes frissítésnek a FailureAction végrehajtása előtt el kell végeznie.  Alapértelmezett\: PT0H0M0S. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Fürt konfigurációs frissítésének indítása

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl SA – fürt frissítése – állapot
Service Fabric önálló fürthöz tartozó fürtkonfiguráció frissítési állapotának beolvasása.

A fürtkonfiguráció frissítési állapotának beolvasása egy Service Fabric önálló fürthöz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --időtúllépés-t | Kiszolgáló időtúllépése másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>További lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.