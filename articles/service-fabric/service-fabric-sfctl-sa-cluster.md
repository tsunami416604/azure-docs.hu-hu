---
title: Az Azure Service Fabric CLI - sfctl sa-fürt |} Microsoft Docs
description: A Service Fabric CLI sfctl önálló fürt parancsok ismerteti.
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
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764021"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-fürt
Különálló Service Fabric-fürtök kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Config | A Service Fabric önálló fürtkonfiguráció beolvasása. |
| Config – frissítése | Indítsa el a Service Fabric önálló fürt konfigurációjának frissítése. |
| frissítés-állapot | A fürt konfigurációs verziófrissítő különálló Service Fabric-fürt állapotának beolvasása. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-fürtkonfiguráció
A Service Fabric önálló fürtkonfiguráció beolvasása.

A Service Fabric önálló fürtkonfiguráció beolvasása. A fürtkonfiguráció, amelyek tartalmazzák a fürt, biztonsági beállításokkal, hiba, és a frissítési tartomány topológiák stb a különböző csomóponttípusok a fürt tulajdonságait tartalmazza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --konfigurációs-api-verziója [szükséges] | A különálló fürt json konfigurációs API-verzió. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-config-Fürtfrissítés
Indítsa el a Service Fabric önálló fürt konfigurációjának frissítése.

Érvényesítse a megadott konfiguráció frissítési paramétereket, és indítsa el a fürtkonfiguráció frissítése, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – fürtkonfiguráció [szükséges] | A fürtkonfiguráció. |
| --különbözeti nem kifogástalan csomópontokat | Az engedélyezett maximális százalékát különbözeti állapotfigyelő teljesítménycsökkenést a frissítés során. Megengedett értékek értékei egész szám nullával 100. |
| --health-check-retry | Elvégezhesse a kísérletek között eltelt idő hosszát ellenőrzi, hogy az alkalmazás vagy a fürt nem működik megfelelően.  Alapértelmezett\: PT0H0M0S. |
| – rendszerállapot-ellenőrzés-stabil | Mennyi ideig, hogy az alkalmazás vagy a fürt kell maradnia kifogástalan.  Alapértelmezett\: PT0H0M0S. |
| --állapot-ellenőrzés-várakozási | Mennyi ideig várjon a frissítési tartományok állapotát elindítása előtt befejezése után ellenőrzi a folyamat.  Alapértelmezett\: PT0H0M0S. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |
| – a nem megfelelő alkalmazások | Az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások a frissítés során. Megengedett értékek értékei egész szám nullával 100. |
| --nem kifogástalan csomópontokat | Az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat a frissítés során. Megengedett értékek értékei egész szám nullával 100. |
| --upgrade-domain-delta-unhealthy-nodes | Az engedélyezett maximális százalékos aránya a frissítési tartomány különbözeti állapotfigyelő teljesítménycsökkenést a frissítés során. Megengedett értékek értékei egész szám nullával 100. |
| --frissítés-tartományi-időtúllépés | A frissítési tartomány időkorlátjának.  Alapértelmezett\: PT0H0M0S. |
| --frissítés – időtúllépés | A frissítési időkorlát.  Alapértelmezett\: PT0H0M0S. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-sa-cluster-upgrade-status"></a>sfctl sa-fürt frissítése – állapot
A fürt konfigurációs verziófrissítő különálló Service Fabric-fürt állapotának beolvasása.

A fürtkonfiguráció beolvasása különálló Service Fabric-fürtök frissítési állapotának részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).