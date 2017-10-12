---
title: "Az Azure Security Center platform migrációja| Microsoft Docs"
description: "Ez a dokumentum az Azure Security Center adatgyűjtési módjának néhány változását ismerteti."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-platform-migration"></a>Az Azure Security Center platform migrációja

2017 júniusának elejével kezdődően az Azure Security Center fontos változásokat vezet be a biztonsági adatok gyűjtési és tárolási módjával kapcsolatban.  Ezek a változások új képességek zárolását oldják fel, például a biztonsági adatok könnyen kereshetővé válnak, és a szolgáltatás jobban fog igazodni más Azure felügyeleti és figyelési szolgáltatásokhoz.

> [!NOTE]
> A platform migrációja nem érinti az éles erőforrásait, és az Ön részéről nincs szükség beavatkozásra.


## <a name="whats-happening-during-this-platform-migration"></a>Mi történik a platform migrációja során?

Régebben a Security Center az Azure Monitoring Agent ügynököt használta a biztonsági adatok virtuális gépekről való gyűjtésére. Ez magában foglal biztonsági konfigurációkra vonatkozó adatokat, amelyek a biztonsági rések azonosítására és biztonsági eseményeket, amelyek a veszélyek észlelésére használatosak. Ezeket az adatokat az Azure Storage-fiókjában tároltuk.

A későbbiekben a Security Center a Microsoft Monitoring Agent ügynököt használja – ez ugyanaz az ügynök, amelyet az Operations Management Suite és a Log Analytics szolgáltatás használ. Az ettől az ügynöktől gyűjtött adatok tárolása vagy egy már meglévő, az Azure-előfizetéséhez társított *Log Analytics*-[munkaterületen](../log-analytics/log-analytics-manage-access.md) történik, vagy új munkaterülete(ke)n, a virtuális gép földrajzi helyének figyelembevételével.

## <a name="agent"></a>Ügynök

Az átmenet részeként a Microsoft Monitoring Agent ([Windows](../log-analytics/log-analytics-windows-agents.md) vagy [Linux](../log-analytics/log-analytics-linux-agents.md) rendszerhez) minden olyan Azure virtuális gépre telepítve lesz, amelyről jelenleg adatgyűjtés folyik.  Ha a virtuális gépre már telepítve van a Microsoft Monitoring Agent, a Security Center a jelenleg telepített ügynököt fogja használni.

Egy ideig (általában néhány napig), mindkét ügynök futni fog párhuzamosan, hogy biztosított legyen az adatveszteség nélküli, zökkenőmentes átmenet. Ez lehetővé teszi a Microsoft számára, hogy a jelenlegi adatcsatorna használatának megszüntetése előtt ellenőrizze az új adatcsatorna működését. Az ellenőrzést követően az Azure Monitoring Agent el lesz távolítva a virtuális gépeiről. Az Ön részéről nincs szükség beavatkozásra. Az összes ügyfél migrálásának befejeződéséről e-mailben fog értesítést kapni.
 
Az Azure Monitoring Agent migráció közbeni manuális eltávolítása nem ajánlott, mert rések keletkezhetnek a biztonsági adatokban. Ha további segítségre van szüksége, forduljon a [Microsoft támogatási és ügyfélszolgálatához](https://support.microsoft.com/contactus/). 

A Windowshoz készült Microsoft Monitoring Agent megköveteli a 443-as port használatát, további információért olvassa el az [Azure Security Center Hibaelhárítási útmutatóját](security-center-troubleshooting-guide.md).


> [!NOTE] 
> Mivel a Microsoft Monitoring Agent ügynököt az Azure más felügyeleti és figyelési szolgáltatásai is használhatják, az ügynök nem lesz automatikusan eltávolítva, amikor kikapcsolja az adatgyűjtést a Security Centerben. Szükség esetén azonban manuálisan eltávolíthatja az ügynököt.

## <a name="workspace"></a>Munkaterület

Az előzőekben leírtaknak megfelelően a Microsoft Monitoring Agenttől (a Security Center számára) gyűjtött adatokat a rendszer vagy az Azure-előfizetéséhez társított, már meglévő Log Analytics-munkaterület(ek)en tárolja, vagy új munkaterület(ek)en, a virtuális gép földrajzi helyének figyelembevételével.

Az Azure Portalon tallózással megkeresheti Log Analytics-munkahelyeinek listáját, beleértve a Security Center által létrehozottakat is. Az új munkaterületekhez kapcsolódó erőforráscsoport fog létrejönni. Mindkettő ezt az elnevezési konvenciót követi:

- Munkaterület: *AlapértelmezettMunkaterület-[előfizetés-azonosító]-[geo]*
- Erőforráscsoport: *AlapértelmezettErőforráscsoport-[geo]* 
 
A Security Center által létrehozott munkaterületeken az adatok 30 napig őrződnek meg. A meglévő munkaterületeknél a megőrzési idő a munkaterület tarifacsomagjától függ.

> [!NOTE]
> A Security Center által korábban gyűjtött adatok a Storage-fiók(ok)ban maradnak. A migráció befejeződése után ezeket a Storage-fiókokat törölheti.

### <a name="oms-security-solution"></a>OMS biztonsági megoldás 

Azok számára a meglévő ügyfelek számára, akiknél nincs telepítve az OMS biztonsági megoldás, a Microsoft telepíti azt a munkaterületükre, de csak az Azure virtuális gépeket célozva. Ne távolítsa el ezt a megoldást, mert nincs automatikus javítási lehetőség, ha ezt az OMS kezelési konzoljáról teszi.


## <a name="other-updates"></a>Egyéb frissítések

A platformmigrálással összefüggésben néhány további kisebb frissítést bocsátunk ki:

- További operációsrendszer-verziók lesznek támogatva. Tekintse meg a listát [itt](security-center-faq.md#virtual-machines).
- Az operációs rendszerek sebezhetőségeinek listája bővülni fog. Tekintse meg a listát [itt](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- A [Díjszabás](https://azure.microsoft.com/pricing/details/security-center/) óránkénti arányosítású lesz (régebben napi volt), ami bizonyos ügyfeleknél költségmegtakarítást fog eredményezni.
- A standard tarifacsomagú ügyfelek számára az adatgyűjtés kötelező és automatikusan engedélyezett lesz.
- Az Azure Security Center meg fogja kezdeni az olyan kártevőirtó megoldások észlelését, amelyek nem az Azure-bővítményeken keresztül lettek telepítve. Elsőként a Symantec Endpoint Protection és a Defender for Windows 2016 lesz elérhető.
- A megelőzési szabályzatok és az értesítések csak az *Előfizetés* szintjén konfigurálhatók, de a díjszabás az *Erőforráscsoport* szintjén is beállítható

