---
title: Azure Service Fabric CLI – sfctl | Microsoft Docs
description: Leírja, hogy az Service Fabric CLI-sfctl parancsai.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 9a09d8c36fd282450767880a9ad144e1192dcd2e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901571"
---
# <a name="sfctl-is"></a>sfctl értéke
Parancsok lekérdezése és küldése az infrastruktúra-szolgáltatásnak.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| command | Egy rendszergazdai parancsot hív meg a megadott infrastruktúra-szolgáltatási példányon. |
| lekérdezés | Egy írásvédett lekérdezést hív meg a megadott infrastruktúra-szolgáltatási példányon. |

## <a name="sfctl-is-command"></a>a sfctl parancs
Egy rendszergazdai parancsot hív meg a megadott infrastruktúra-szolgáltatási példányon.

Azon fürtök esetében, amelyeken az infrastruktúra-szolgáltatás egy vagy több példánya van konfigurálva, ez az API lehetővé teszi az infrastruktúra-specifikus parancsok küldését az infrastruktúra-szolgáltatás egy adott példányára. Az elérhető parancsok és a hozzájuk tartozó válaszok formátuma attól függően változik, hogy a fürt melyik infrastruktúrán fut. Ez az API támogatja a Service Fabric platformot; nem közvetlenül a kódból való használatra készült.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Command [kötelező] | A meghívni kívánt parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --Service-ID | Az infrastruktúra-szolgáltatás identitása. <br><br> Ez az infrastruktúra-szolgáltatás teljes neve a "Fabric\:" URI-séma nélkül. Ez a paraméter csak azon fürt esetében szükséges, amelynél az infrastruktúra-szolgáltatás egynél több példánya fut. |
| --időtúllépés-t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-is-query"></a>sfctl lekérdezés
Egy írásvédett lekérdezést hív meg a megadott infrastruktúra-szolgáltatási példányon.

Azon fürtök esetében, amelyeken az infrastruktúra-szolgáltatás egy vagy több példánya van konfigurálva, ez az API lehetővé teszi az infrastruktúra-specifikus lekérdezések küldését az infrastruktúra-szolgáltatás egy adott példányára. Az elérhető parancsok és a hozzájuk tartozó válaszok formátuma attól függően változik, hogy a fürt melyik infrastruktúrán fut. Ez az API támogatja a Service Fabric platformot; nem közvetlenül a kódból való használatra készült.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Command [kötelező] | A meghívni kívánt parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --Service-ID | Az infrastruktúra-szolgáltatás identitása. <br><br> Ez az infrastruktúra-szolgáltatás teljes neve a "Fabric\:" URI-séma nélkül. Ez a paraméter csak azon fürt esetében szükséges, amelynél az infrastruktúra-szolgáltatás egynél több példánya fut. |
| --időtúllépés-t | Alapértelmezett\: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Megengedett értékek: JSON, jsonc, Table, TSV\:.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat és példákat a http\://jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application)használatával.