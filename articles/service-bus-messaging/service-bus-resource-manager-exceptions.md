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
ms.openlocfilehash: 7b9d4099734af3a04f43d35d89f07f8b005c90f9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802515"
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
| Hibás kérelem | 40000 | Alkód = 40000. A *"tulajdonságnév"* tulajdonság nem állítható be várólista létrehozásakor, mert a névtér *neve* az "alapszintű" szintet használja. Ezt a műveletet csak a standard vagy a prémium szint támogatja. | Azure Service Bus alapszintű szinten az alábbi tulajdonságok nem állíthatók be vagy nem frissíthetők – <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> Továbbítás </li> <li> Témakörök </li> </ul> | A funkció használatához érdemes lehet alapszintről standard vagy prémium csomagra frissíteni. |
| Hibás kérelem | 40000 | Alkód = 40000. Egy meglévő várólista (vagy témakör) "requiresDuplicateDetection" tulajdonságának értéke nem módosítható. | Az ismétlődő észlelést engedélyezni/le kell tiltani az entitások létrehozásakor. A duplikált észlelés konfigurációs paramétere a létrehozás után nem módosítható. | Egy korábban létrehozott üzenetsor/témakör ismétlődő észlelésének engedélyezéséhez létrehozhat egy új üzenetsor/témakört ismétlődő észleléssel, majd továbbíthatja az eredeti várólistáról az új üzenetsor/témakörre. |
| Hibás kérelem | 40000 | Alkód = 40000. A megadott 16384 érték érvénytelen. A "MaxSizeInMegabytes" tulajdonságnak a következő értékek egyikének kell lennie: 1024; 2048; 3072; 4096; 5120. | A MaxSizeInMegabytes értéke érvénytelen. | Győződjön meg arról, hogy a MaxSizeInMegabytes a következők egyike: 1024, 2048, 3072, 4096, 5120. |
| Hibás kérelem | 40000 | Alkód = 40000. A particionálás nem módosítható üzenetsor/témakör esetében. | Az entitások particionálását nem lehet módosítani. | Hozzon létre egy új entitást (Üzenetsor vagy témakör), és engedélyezze a partíciókat. | 
| Hibás kérelem | nincs | A névtér *neve* nem létezik. | A névtér nem létezik az Azure-előfizetésében. | A hiba elhárításához próbálja meg az alábbi lépéseket <ul> <li> Győződjön meg arról, hogy az Azure-előfizetés helyes. </li> <li> Győződjön meg arról, hogy a névtér létezik. </li> <li> Ellenőrizze, hogy helyes-e a névtér neve (nincsenek helyesírási hibák vagy Null karakterláncok). </li> </ul> | 
| Hibás kérelem | 40400 | Alkód = 40400. Az automatikus továbbítás célhelyének entitása nem létezik. | Nem létezik az adattovábbítási célként szolgáló entitás célhelye. | A cél entitásnak (Üzenetsor vagy témakör) a forrás létrehozása előtt léteznie kell. Próbálkozzon újra a cél entitás létrehozása után. |
| Hibás kérelem | 40000 | Alkód = 40000. A megadott zárolási idő meghaladja az engedélyezett maximumot (5 perc). | Az az idő, ameddig egy üzenet zárolható, 1 perc (minimum) és 5 perc (maximum) közé kell esnie. | Győződjön meg arról, hogy a megadott zárolási idő 1 perc és 5 perc között van. |
| Hibás kérelem | 40000 | Alkód = 40000. A DelayedPersistence és a RequiresDuplicateDetection tulajdonság nem engedélyezhető együtt. | Az ismétlődő észleléssel rendelkező entitásoknak állandónak kell lenniük, ezért az adatmegőrzés nem késleltethető. | További információ az [ismétlődő észlelésről](duplicate-detection.md) |
| Hibás kérelem | 40000 | Alkód = 40000. Egy meglévő várólista RequiresSession tulajdonságának értéke nem módosítható. | A munkamenetek támogatását engedélyezni kell az entitások létrehozásakor. A létrehozást követően nem engedélyezheti vagy tilthatja le a munkameneteket egy meglévő entitáson (Üzenetsor vagy előfizetés) | Törölje, majd hozza létre újra az új üzenetsor (vagy előfizetés) beállítást a "RequiresSession" tulajdonsággal. |
| Hibás kérelem | 40000 | Alkód = 40000. A "URI_PATH" olyan karakter (eke) t tartalmaz, amelyet a Service Bus nem engedélyez. Az entitások szegmensei csak betűket, számokat, pontokat (.), kötőjeleket (-) és aláhúzást (_) tartalmazhatnak. | Az entitások szegmensei csak betűket, számokat, pontokat (.), kötőjeleket (-) és aláhúzást (_) tartalmazhatnak. Bármely más karakter miatt a kérelem sikertelen lesz. | Győződjön meg arról, hogy az URI elérési útja nem tartalmaz érvénytelen karaktereket. |


## <a name="error-code-429"></a>Hibakód: 429

Akárcsak a HTTP-ben, a "hibakód 429" kifejezés "túl sok kérést" jelez. Ez azt jelenti, hogy az adott erőforrást (névteret) az erőforráson túl sok kérelem (vagy az ütköző műveletek miatt) szabályozza a rendszer.

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Alkód = 50004. A kérés megszakadt, mert a névteret *a névtér* szabályozza. | Ez a hiba akkor jelenik meg, ha a bejövő kérések száma meghaladja az erőforrás korlátozását. | Várjon néhány másodpercig, és próbálkozzon újra. <br/> <br/> További információ a [kvóták](service-bus-quotas.md) és a [Azure Resource Manager kérelmek korlátairól](../azure-resource-manager/resource-manager-request-limits.md)|
| 429 | 40901 | Alkód = 40901. Egy másik ütköző művelet folyamatban van. | Egy másik ütköző művelet folyamatban van ugyanazon az erőforráson/entitáson. | Várjon, amíg a folyamatban lévő művelet befejeződik, és próbálkozzon újra. |
| 429 | 40900 | Alkód = 40900. Ütközés. Olyan műveletet kér, amely nem engedélyezett az erőforrás jelenlegi állapotában. | Ez az állapot akkor fordulhat elő, ha egyszerre több kérelem kerül végrehajtásra ugyanazon az entitáson (Üzenetsor, témakör, előfizetés vagy szabály). | Várjon néhány másodpercig, és próbálkozzon újra |
| 429 | nincs | Erőforrás-ütközés történt. Lehetséges, hogy folyamatban van egy másik ütköző művelet. Ha ez az újrapróbálkozási művelet sikertelen, a háttérben történő törlés továbbra is függőben van. Próbálkozzon újra később. | Ez az állapot akkor fordulhat elő, ha a függőben lévő művelet ugyanahhoz az entitáshoz kapcsolódik. | Várjon, amíg az előző művelet befejeződik, és próbálkozzon újra. |
| 429 | nincs | Az entitás *neve* elemre vonatkozó kérelem ütközik egy másik kéréssel | Egy másik ütköző művelet folyamatban van ugyanazon az erőforráson/entitáson. | Várjon, amíg az előző művelet befejeződik, és próbálkozzon újra |
| 429 | nincs | Folyamatban van egy másik frissítési kérelem az entitás *neve*elemnél. | Egy másik ütköző művelet folyamatban van ugyanazon az erőforráson/entitáson. | Várjon, amíg az előző művelet befejeződik, és próbálkozzon újra |


## <a name="error-code-not-found"></a>Hibakód: Nem található

Ez az osztály azt jelzi, hogy az erőforrás nem található.

| Hibakód | Hiba alkódja | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nincs találat | nincs | Az entitás *neve* nem található. | Az entitás, amely ellen a művelet nem található. | Ellenőrizze, hogy az entitás létezik-e, majd próbálja megismételni a műveletet. |
| Nincs találat | nincs | Nem található. A művelet nem létezik. | A végrehajtani kívánt művelet nem létezik. | Tekintse át a műveletet, és próbálkozzon újra. |
| Nincs találat | nincs | A bejövő kérelem nem ismerhető fel névtér-házirend Put kérelemként. | A bejövő kérelem törzse null értékű, ezért nem hajtható végre Put kérelemként. | Ellenőrizze a kérelem törzsét, és győződjön meg arról, hogy az nem null értékű. | 
| Nincs találat | nincs | A (z) *"Entity Name"* üzenetküldési entitás nem található. | Nem található a műveletet végrehajtani próbáló entitás. | Győződjön meg arról, hogy az entitás létezik, majd próbálja megismételni a műveletet. |
