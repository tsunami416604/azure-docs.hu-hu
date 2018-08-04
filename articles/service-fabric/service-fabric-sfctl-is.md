---
title: Az Azure Service Fabric parancssori felület - sfctl értéke |} A Microsoft Docs
description: Ismerteti a Service Fabric parancssori felület sfctl értéke parancsokat.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 53e49099fd3486d51f021528c9354cf32f4952d2
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492789"
---
# <a name="sfctl-is"></a>sfctl értéke
Lekérdezés, és parancsokat küldjön az infrastruktúra-szolgáltatás.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| command | Hívja meg az adott példányon infrastruktúra-szolgáltatás-rendszergazdai parancs. |
| lekérdezés | Hívja meg az adott infrastruktúra szolgáltatáspéldány lekérdezés csak olvasható. |

## <a name="sfctl-is-command"></a>sfctl értéke parancs
Hívja meg az adott példányon infrastruktúra-szolgáltatás-rendszergazdai parancs.

Az infrastruktúra-szolgáltatás konfigurált egy vagy több példányban fürtök esetén az API-t biztosítja az infrastruktúra-specifikus parancsokat küldenek egy adott példányt az infrastruktúra-szolgáltatás. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, hogy az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] parancsot | Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --service-id | Az infrastruktúra-szolgáltatás identitásának. <br><br> Ez az, hogy az infrastruktúra-szolgáltatás a "fabric" URI-séma nélküli teljes nevét. Ez a paraméter csak a fürt, amelyben több példányát futtató infrastruktúra-szolgáltatás szükséges. |
| --időkorlát -t | Kiszolgálói időtúllépés másodpercben.  Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug | Növelése a naplózás az összes hibakeresési naplók megjelenítése. |
| --help -h | A súgóüzenetet és kilépési jelennek meg. |
| --kimeneti -o | Kimeneti formátum.  Megengedett értékek\: JSON-t, jsonc, tábla, tsv.  Alapértelmezett\: json. |
| – lekérdezés | JMESPath lekérdezési karakterláncot. Tekintse meg a http\://jmespath.org/ további információt és példákat. |
| – részletes | Növelése a naplózást. Használja a--debug teljes hibakeresési naplók. |

## <a name="sfctl-is-query"></a>sfctl értéke lekérdezés
Hívja meg az adott infrastruktúra szolgáltatáspéldány lekérdezés csak olvasható.

Az infrastruktúra-szolgáltatás konfigurált egy vagy több példányban fürtök esetén az API-t biztosítja az infrastruktúra-specifikus lekérdezéseket küldeni egy adott példányt az infrastruktúra-szolgáltatás. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, hogy az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric platformot; azt nem jelenti, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [kötelező] parancsot | Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --service-id | Az infrastruktúra-szolgáltatás identitásának. <br><br> Az infrastruktúra-szolgáltatás nélkül teljes neve a "fabric\:" URI-séma. Ez a paraméter csak a fürt, amelyben több példányát futtató infrastruktúra-szolgáltatás szükséges. |
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