---
title: Az Azure Service Fabric CLI - sfctl van |} Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl parancsok.
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
ms.openlocfilehash: aa1e4d588f367351f0bed10370c7d67d50dd9927
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763051"
---
# <a name="sfctl-is"></a>sfctl értéke
Lekérdezi és parancsainak elküldését az infrastruktúra-szolgáltatás.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| a parancs | Meghívja az adott infrastruktúra szolgáltatáspéldányon egy felügyeleti parancs. |
| lekérdezés | Meghívja az adott infrastruktúra szolgáltatáspéldányon írásvédett lekérdezést. |

## <a name="sfctl-is-command"></a>sfctl parancs
Meghívja az adott infrastruktúra szolgáltatáspéldányon egy felügyeleti parancs.

Fürtöknél az infrastruktúra-szolgáltatás, konfigurálva egy vagy több példányát Ez az API biztosítja az infrastruktúra-specifikus parancsokat küldhet az adott példányára. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] parancsot | Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --service-id | Az infrastruktúra-szolgáltatás identitásának. <br><br> Ez az a teljes név nélkül "fabric" URI-séma infrastruktúra-szolgáltatás. Ez a paraméter csak a fürt, amely rendelkezik az infrastruktúra-szolgáltatás fut egynél több példánya szükséges. |
| – időtúllépés -t | Időtúllépését másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése | Naplózási növelése az összes hibakeresési naplók megjelenítése. |
| – Súgó -h | Ez egy súgóüzenet és kilépési megjelenítése. |
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek\: json, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| --lekérdezés | JMESPath lekérdezési karakterlánc. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési. |

## <a name="sfctl-is-query"></a>sfctl Ez a lekérdezés
Meghívja az adott infrastruktúra szolgáltatáspéldányon írásvédett lekérdezést.

Fürtöknél az infrastruktúra-szolgáltatás, konfigurálva egy vagy több példányát Ez az API biztosítja az infrastruktúra-specifikus lekérdezéseket küldjön az infrastruktúra-szolgáltatás adott példányára. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] parancsot | Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --service-id | Az infrastruktúra-szolgáltatás identitásának. <br><br> Az infrastruktúra-szolgáltatás nélkül teljes neve a "fabric\:" URI-séma. Ez a paraméter csak a fürt, amely rendelkezik az infrastruktúra-szolgáltatás fut egynél több példánya szükséges. |
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