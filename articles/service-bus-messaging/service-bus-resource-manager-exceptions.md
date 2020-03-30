---
title: Az Azure Service Bus Resource Manager kivételei | Microsoft dokumentumok
description: Az Azure Resource Manager által felszínre került Service Bus-kivételek listája és a javasolt műveletek.
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
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978273"
---
# <a name="service-bus-resource-manager-exceptions"></a>A Service Bus Resource Manager kivételei

Ez a cikk az Azure Resource Manager használatával az Azure Service Bus használatával – sablonok vagy közvetlen hívások használatával történő interakció során keletkező kivételeket sorolja fel.

> [!IMPORTANT]
> Ez a dokumentum gyakran frissül. Kérjük, látogasson vissza a frissítéseket.

Az alábbiakban az Azure Resource Manager által felszínre megjelenő különböző kivételek/hibák.

## <a name="error-bad-request"></a>Hiba: Hibás kérés

A "hibás kérelem" azt jelenti, hogy az Erőforrás-kezelő által kapott kérés nem sikerült az érvényesítéshez.

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Hibás kérés | 40000 | Alkód=40000. A *"tulajdonság neve"* tulajdonság nem állítható be várólista létrehozásakor, mert a *"névtér neve"* névtér az "Alapszintű" szintet használja. Ez a művelet csak a "Standard" vagy a "Prémium" szint támogatott. | Az Azure Service Bus alapszintjén az alábbi tulajdonságok nem állíthatók be és nem frissíthetők - <ul> <li> SzükségesDódeszkaészlelés </li> <li> Automatikus törlésOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> Duplikáltelem-észlelési előzményekablaka </li> <li> EnableExpress </li> <li> Továbbítás </li> <li> Témakörök </li> </ul> | A funkció használatához fontolja meg az alapszintűről a standard vagy prémium szintre való frissítést. |
| Hibás kérés | 40000 | Alkód=40000. Egy meglévő Várólista(vagy Topic) "requiresDuplicateDetection" tulajdonságának értéke nem módosítható. | A duplikáltelem-észlelést engedélyezni/letiltani kell az entitás létrehozásakor. A duplikáltelem-észlelési konfigurációs paraméter nem módosítható a létrehozás után. | A korábban létrehozott várólista/témakör duplikáltelem-észlelésének engedélyezéséhez hozzon létre egy új várólistát/témakört duplikáltelem-észleléssel, majd továbbítsa az eredeti várólistáról az új várólistára/témakörre. |
| Hibás kérés | 40000 | Alkód=40000. A megadott 16384 érték érvénytelen. A "MaxSizeInMegabytes" tulajdonságnak a következő értékek egyikének kell lennie: 1024;2048;3072;4096;5120. | A MaxSizeInMegabájt értéke érvénytelen. | Győződjön meg arról, hogy a MaxSizeInMegabytes az alábbiak egyike - 1024, 2048, 3072, 4096, 5120. |
| Hibás kérés | 40000 | Alkód=40000. A particionálás nem módosítható a várólistához/témakörhöz. | A particionálás nem módosítható az entitáshoz. | Hozzon létre egy új entitást (várólista vagy témakör), és engedélyezze a partíciókat. | 
| Hibás kérés | Nincs | A *"névtér neve"* névtér nem létezik. | A névtér nem létezik az Azure-előfizetésben. | A hiba megoldásához próbálkozzon az alábbi <ul> <li> Győződjön meg arról, hogy az Azure-előfizetés helyes. </li> <li> Ellenőrizze, hogy létezik-e névtér. </li> <li> Ellenőrizze, hogy helyes-e a névtér neve (nincshelyesírási hiba vagy null karakterlánc). </li> </ul> | 
| Hibás kérés | 40400 | Alkód=40400. Az automatikus továbbítási célentitás nem létezik. | Az automatikus továbbítás imátjelző entitás célja nem létezik. | A célentitásnak (várólista vagy témakör) a forrás létrehozása előtt léteznie kell. Próbálkozzon újra a célentitás létrehozása után. |
| Hibás kérés | 40000 | Alkód=40000. A mellékelt zárolási idő meghaladja a megengedett maximum "5" percet. | Az üzenet zárolásának időpontjának 1 perc (minimum) és 5 perc (maximum) között kell lennie. | Győződjön meg arról, hogy a mellékelt záridő 1 és 5 min között van. |
| Hibás kérés | 40000 | Alkód=40000. A delayedpersistence és a requiresduplicatedetection tulajdonság nem engedélyezhető együtt. | A duplikáltelem-észlelési engedélyezve lévő entitásoknak állandónak kell lenniük, ezért az adatmegőrzés nem késleltethető. | További információ a [duplikáltelem-észlelésről](duplicate-detection.md) |
| Hibás kérés | 40000 | Alkód=40000. Egy meglévő várólista RequiresSession tulajdonságának értéke nem módosítható. | A munkamenetek támogatását az entitás létrehozásakor engedélyezni kell. A létrehozás után nem engedélyezhet/nem tilthatja le a munkameneteket egy meglévő entitáson (várólista vagy előfizetés) | Új várólista (vagy előfizetés) törlése és újbóli létrehozása a "RequiresSession" tulajdonsággal. |
| Hibás kérés | 40000 | Alkód=40000. Az "URI_PATH" olyan karakter(eke)t tartalmaz, amelyet a Service Bus nem engedélyez. Az entitásszegmensek csak betűket, számokat, pontokat(.), kötőjeleket(-) és aláhúzásjeleket(_) tartalmazhatnak. | Az entitásszegmensek csak betűket, számokat, pontokat(.), kötőjeleket(-) és aláhúzásjeleket(_) tartalmazhatnak. Bármely más karakter a kérés sikertelensét okozza. | Győződjön meg arról, hogy nincsenek érvénytelen karakterek az URI elérési útban. |


## <a name="error-code-429"></a>Hibakód: 429

Csakúgy, mint a HTTP,"Hibakód 429" jelzi a "túl sok kérés". Ez azt jelenti, hogy az adott erőforrás (névtér) szabályozás alatt áll, mert túl sok kérés (vagy ütköző műveletek miatt) az adott erőforrás.

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Alkód=50004. A kérés megszakadt, mert a *névtér ben lévő névtér* szabályozásalatt áll. | Ez a hibafeltétel akkor jelenik meg, ha a bejövő kérelmek száma meghaladja az erőforrás korlátozását. | Várjon néhány másodpercet, majd próbálkozzon újra. <br/> <br/> További információ a [kvótákról](service-bus-quotas.md) és az [Azure Resource Manager-kérelmek korlátairól](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Alkód=40901. Egy másik ütköző művelet van folyamatban. | Egy másik ütköző művelet van folyamatban ugyanazon az erőforráson/entitáson | Várja meg, amíg az aktuális folyamatban lévő művelet befejeződik, mielőtt újra próbálkozna. |
| 429 | 40900 | Alkód=40900. Konfliktus. Olyan műveletet kér, amely nem engedélyezett az erőforrás aktuális állapotában. | Ez a feltétel akkor fordulhat elő, ha egyszerre több, ugyanazon az entitáson (várólista, témakör, előfizetés vagy szabály) végzett műveletek végrehajtására irányuló kérelmek érkeznek. | Várjon néhány másodpercet, majd próbálkozzon újra |
| 429 | 40901 | Az entitás *"entitásnevére"* vonatkozó kérelem ütközik egy másik kéréssel | Egy másik ütköző művelet van folyamatban ugyanazon az erőforráson/entitáson | Várja meg, amíg az előző művelet befejeződik, mielőtt újra próbálkozna |
| 429 | 40901 | Egy másik frissítési kérelem van folyamatban az entitás *"entitás nevéhez"*. | Egy másik ütköző művelet van folyamatban ugyanazon az erőforráson/entitáson | Várja meg, amíg az előző művelet befejeződik, mielőtt újra próbálkozna |
| 429 | Nincs | Erőforrás-ütközés történt. Egy másik ütköző művelet is folyamatban lehet. Ha ez egy sikertelen művelet újrapróbálkozása, a háttér karbantartása még mindig függőben van. Próbálkozzon újra később. | Ez a feltétel akkor fordulhat elő, ha ugyanazon entitás ellen függőben lévő művelet van. | Várja meg, amíg az előző művelet befejeződik, mielőtt újra próbálkozna. |


## <a name="error-code-not-found"></a>Hibakód: Nem található

Ez a hibaosztály azt jelzi, hogy az erőforrás nem található.

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nem található | Nincs | Az *entitás "entitásneve"* nem található. | Az a szervezet, amely ellen a művelet nem található. | Ellenőrizze, hogy az entitás létezik-e, és próbálja meg újra a műveletet. |
| Nem található | Nincs | Nem található. A művelet nem létezik. | A végrehajtani kívánt művelet nem létezik. | Ellenőrizze a műveletet, majd próbálkozzon újra. |
| Nem található | Nincs | A bejövő kérelmet a rendszer nem ismeri fel névtér-házirend-előküldési kérelemként. | A bejövő kérelem törzse null értékű, ezért nem hajtható végre put kérésként. | Ellenőrizze a kérelem törzsében, hogy nem null. | 
| Nem található | Nincs | Az üzenetküldő entitás *"entitásneve"* nem található. | Az entitás, amely en a műveletet végrehajtani próbálja végrehajtani, nem található. | Ellenőrizze, hogy az entitás létezik-e, és próbálja meg újra a műveletet. |

## <a name="error-code-internal-server-error"></a>Hibakód: Belső kiszolgálóhiba

Ez a hibaosztály azt jelzi, hogy belső kiszolgálóhiba történt

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Belső kiszolgálóhiba | 50000 | Alkód=50000. Belső kiszolgálóhiba| Különböző okok miatt történhet. Néhány tünet... <ul> <li> Az ügyfélkérés/törzs sérült, és hibához vezet. </li> <li> Az ügyfélkérelem a szolgáltatás feldolgozási problémái miatt időbeli elévül. </li> </ul> | A probléma megoldásához <ul> <li> Győződjön meg arról, hogy a kérelmek paraméterei nem null értékűek vagy hibásak. </li> <li> Próbálkozzon újra a kéréssel. </li> </ul> |

## <a name="error-code-unauthorized"></a>Hibakód: Nem engedélyezett

Ez a hibaosztály a parancs futtatására vonatkozó engedély hiányát jelzi.

| Hibakód | Hiba alkód | Hibaüzenet | Leírás | Ajánlás |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Nem engedélyezett | Nincs | Érvénytelen művelet a másodlagos névtérben. A másodlagos névtér írásvédett. | A művelet a másodlagos névtéren történt, amely csak olvasható névtérként van beállítva. | Próbálja meg újra a parancsot az elsődleges névtérrel szemben. További információ a [másodlagos névtérről](service-bus-geo-dr.md) |
| Nem engedélyezett | Nincs | MissingToken: Az engedélyezési fejléc nem található. | Ez a hiba akkor fordul elő, ha az engedélyezés értéke null értékű vagy helytelen. | Győződjön meg arról, hogy az engedélyezési fejlécben említett tokenérték helyes és nem null. |