---
title: Azure Service Bus Resource Manager-kivételek | Microsoft Docs
description: A Azure Resource Manager és a javasolt műveletek által felszínre kerülő Service Bus kivételek listája.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: e666503b9e8888e7d61445639fe0f3adeeffe55f
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329304"
---
# <a name="service-bus-resource-manager-exceptions"></a>Resource Manager-kivételek Service Bus

Ez a cikk azokat a kivételeket sorolja fel, amelyek akkor jönnek létre, amikor a Azure Service Bus a Azure Resource Manager-sablonok vagy a közvetlen hívások használatával történik.

> [!IMPORTANT]
> Ez a dokumentum gyakran frissül. Tekintse át a frissítéseket.

Alább láthatók a Azure Resource Managerban felszínre kerülő különböző kivételek/hibák.

## <a name="error-bad-request"></a>Hiba: Hibás kérelem

A "hibás kérelem" azt jelenti, hogy a Resource Manager által lekért kérelem ellenőrzése sikertelen volt.

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Hibás kérelem | 40000 | Alkód = 40000. A *"tulajdonságnév"* tulajdonság nem állítható be várólista létrehozásakor, mert a névtér *neve* az "alapszintű" szintet használja. Ezt a műveletet csak a standard vagy a prémium szint támogatja. | Azure Service Bus alapszintű szinten az alábbi tulajdonságok nem állíthatók be vagy nem frissíthetők – <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> Továbbítás </li> <li> Témakörök </li> </ul> | A funkcionalitás kihasználása érdekében érdemes lehet alapszintről standard vagy prémium csomagra frissíteni. |
| Hibás kérelem | 40000 | Alkód = 40000. Egy meglévő várólista "requiresDuplicateDetection" tulajdonságának értéke nem módosítható. | Az ismétlődő észlelést engedélyezni/le kell tiltani az entitások létrehozásakor. A létrehozást követően a duplikált észlelési konfigurációs paraméter nem módosítható. | Egy korábban létrehozott üzenetsor vagy előfizetés ismétlődő észlelésének engedélyezéséhez létrehozhat egy új, duplikált észleléssel rendelkező várólistát, majd továbbíthatja az eredeti várólistáról az új várólistára. |
| Hibás kérelem | 40000 | Alkód = 40000. A megadott 16384 érték érvénytelen. A "MaxSizeInMegabytes" tulajdonságnak a következő értékek egyikének kell lennie: 1024; 2048; 3072; 4096; 5120. | A MaxSizeInMegabytes értéke érvénytelen. | Győződjön meg arról, hogy a MaxSizeInMegabytes a következők egyike: 1024, 2048, 3072, 4096, 5120. |
| Hibás kérelem | 40000 | Alkód = 40000. Várólista esetében nem módosítható a particionálás. | Az entitások particionálását nem lehet módosítani. | Hozzon létre egy új entitást, és engedélyezze a partíciókat. | 
| Hibás kérelem | nincs | A névtér *neve* nem létezik. | A névtér nem létezik az Azure-előfizetésében. | A hiba elhárításához próbálja meg az alábbi lépéseket <ul> <li> Győződjön meg arról, hogy az Azure-előfizetés helyes. </li> <li> Győződjön meg arról, hogy a névtér létezik. </li> <li> Ellenőrizze, hogy helyes-e a névtér neve (nincsenek helyesírási hibák vagy Null karakterláncok). </li> </ul> | 
| Hibás kérelem | 40400 | Alkód = 40400. Az automatikus továbbítás célhelyének entitása nem létezik. | Nem létezik az adattovábbítási célként szolgáló entitás célhelye. | A cél entitásnak (Üzenetsor vagy témakör) a forrás létrehozása előtt léteznie kell. Próbálkozzon újra a cél entitás létrehozása után. |


## <a name="error-code-429"></a>Hibakód: 429

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Alkód = 50004. A kérés megszakadt, mert a névteret *a névtér* szabályozza. | Ez a hiba akkor jelenik meg, ha a bejövő kérések száma meghaladja az erőforrás korlátozását. | Várjon néhány másodpercig, és próbálkozzon újra. <br/> <br/> További információ a [kvóták](service-bus-quotas.md) és a [Azure Resource Manager kérelmek korlátairól](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Alkód = 40901. Egy másik ütköző művelet folyamatban van. | Egy másik ütköző művelet folyamatban van ugyanazon az erőforráson/entitáson. | Várjon, amíg a folyamatban lévő művelet befejeződik, és próbálkozzon újra. |
| 429 | 40900 | Alkód = 40900. Ütközés. Olyan műveletet kér, amely nem engedélyezett az erőforrás jelenlegi állapotában. | Ez az állapot akkor fordulhat elő, ha egyszerre több kérelem kerül végrehajtásra ugyanazon entitáson (Üzenetsor, témakör, előfizetés vagy szabály) egy időben. | Várjon néhány másodpercig, és próbálkozzon újra |
| 429 | nincs | Erőforrás-ütközés történt. Lehetséges, hogy folyamatban van egy másik ütköző művelet. Ha ez az újrapróbálkozási művelet sikertelen, a háttérben történő törlés továbbra is függőben van. Próbálkozzon újra később. | Ez az állapot akkor fordulhat elő, ha a függőben lévő művelet ugyanahhoz az entitáshoz kapcsolódik. | Várjon, amíg az előző művelet befejeződik, és próbálkozzon újra. |

