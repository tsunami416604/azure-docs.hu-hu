---
title: Azure Service Fabric CLI-sfctl Mesh Service – replika | Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl Mesh Service-replika parancsait.
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
ms.openlocfilehash: 6819bb32eecf8477e2c0727b50641858db21c784
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035916"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh szolgáltatásreplika
A replika adatainak beolvasása és az adott szolgáltatás replikáinak listázása egy alkalmazás-erőforrásban.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| list | A szolgáltatás összes replikáját listázza. |
| megjelenítése | Egy alkalmazás szolgáltatásának adott replikájának beolvasása. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl Mesh Service – replika lista
A szolgáltatás összes replikáját listázza.

Lekéri egy szolgáltatás összes replikájának adatait. Az információk közé tartozik a szolgáltatás replikájának leírása és egyéb tulajdonságai.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek\: : JSON, jsonc, Table, TSV.  Alapértelmezett\: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca További információkat\:és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl Mesh Service – replika megjelenítés
Egy alkalmazás szolgáltatásának adott replikájának beolvasása.

Lekéri a szolgáltatás replikájának adatait a megadott névvel. Az információk közé tartozik a szolgáltatás replikájának leírása és egyéb tulajdonságai.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| --Name-n [kötelező] | A szolgáltatás replikájának neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |

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