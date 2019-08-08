---
title: Rendszerfigyelés biztonsági funkciókhoz az Azure Ausztráliában
description: Útmutató a rendszerfigyelés az ausztráliai régiókban való konfigurálásához, hogy megfeleljenek az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok konkrét követelményeinek.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779325"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Rendszerfigyelés biztonsági funkciókhoz az Azure Ausztráliában

A valós idejű monitorozást és rutin biztonsági értékeléseket is tartalmazó robusztus biztonsági stratégiák kulcsfontosságúak az informatikai környezetek napi működési biztonságának fokozása érdekében, beleértve a felhőt is.

A Felhőbeli biztonság közös erőfeszítés az ügyfél és a felhőalapú szolgáltató között. Négy olyan szolgáltatás van, amely Microsoft Azure biztosítja ezeket a követelményeket, figyelembe véve az [ausztrál Cyber Security Center (ASCs) információs biztonsági manuális vezérlői](https://acsc.gov.au/infosec/ism/index.htm) (ISM) által foglalt ajánlásokat, pontosabban a központosított eseménynaplózás, az Eseménynapló-naplózás és a biztonsági sebezhetőségek felmérése és kezelése. A Microsoft Azure szolgáltatások a következők:

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

A ASCS azt javasolja, hogy ezeket a szolgáltatásokat a **védett** adatvédelemhez használja. Ezeknek a szolgáltatásoknak a használatával proaktív módon figyelheti és elemezheti az IT-környezeteket, és megalapozott döntéseket hozhat arról, hogy hol érdemes erőforrásokat kiosztani a biztonság növelése érdekében. Ezek a szolgáltatások egy kombinált megoldás részét képezik, amely lehetővé teszi a lehető legjobb betekintést, javaslatokat és védelmet.

## <a name="azure-security-center"></a>Azure Security Center

A [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) egy egységes biztonsági felügyeleti konzolt biztosít, amelyet az Azure-erőforrások és az üzemeltetett adatai biztonságának figyelésére és javítására használhat. Azure Security Center tartalmazza a biztonságos pontszámot, amely a Azure Advisor és a Azure Policy általános megfelelőségének elemzésén alapul.

A Azure Security Center az alábbi funkciókkal biztosítja az Azure-ügyfeleket:

* Biztonsági házirend, értékelés és javaslatok
* Biztonsági események összesítése és keresése
* Hozzáférés és alkalmazás-vezérlőelemek
* Összetett veszélyforrások észlelése
* Igény szerinti Virtual Machines hozzáférés-vezérlés
* Hibrid biztonság

A Azure Security Center által figyelt erőforrások hatókörét kiterjesztheti, hogy a támogatott helyszíni erőforrásokat egy hibrid felhős környezetben is felhasználja. Ide tartoznak a System Center Operations Manager támogatott verziói által jelenleg figyelt helyszíni erőforrások.

A "standard" Security Center az [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)által igényelt felhőalapú biztonsági ellenőrzéseket is biztosít. Ilyenek például az alkalmazás-engedélyezési lista és a rendszergazdai jogosultságok korlátozása az igény szerinti hozzáférés használatával.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) a központosított naplózási megoldás az összes Azure-erőforráshoz, és tartalmazza a log Analytics és a Application Insights. A rendszer két fő adattípust gyűjt az Azure-erőforrásokból: naplók és mérőszámok. A Azure Monitor begyűjtését követően számos eszköz és különböző célokra használhatja a naplózási adatokat.

![Azure Monitor – áttekintés](media/overview.png)

A Azure Monitor az "Azure Activity log"-t is tartalmazza. A SActivity napló az Azure-ban történt összes előfizetési szintű eseményt tárolja. Lehetővé teszi az Azure-ügyfelek számára, hogy az Azure-erőforrásokon végrehajtott műveletek mögött "ki, mit és mikor" lássák el. A Azure Monitor és az Azure Activity log eseményeinek eljuttatott erőforrás-alapú naplózás is a beépített Kusto lekérdezési nyelv használatával elemezhető. Ezeket a naplókat ezután exportálhatja, az egyéni irányítópultok és nézetek létrehozásához, valamint a riasztások és értesítések aktiválására konfigurált konfigurációhoz.

### <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) elemzi az Azure-előfizetésében támogatott Azure-erőforrásokat, a rendszer által létrehozott naplófájlokat és az aktuális erőforrás-konfigurációkat. A Azure Advisorban megadott elemzés valós időben jön létre, és a Microsoft javasolt ajánlott eljárásai alapján történik. A rendszer elemzi a környezethez hozzáadott összes támogatott Azure-erőforrást, és a megfelelő javaslatokat fogja biztosítani. Azure Advisor ajánlásokat négy ajánlott eljárási kategóriába sorolják:

* Biztonság
* Magas rendelkezésre állás
* Teljesítmény
* Költség

A Azure Advisor által létrehozott biztonsági javaslatok a Azure Security Center által biztosított általános biztonsági elemzés részét képezik.

A Azure Advisor által összegyűjtött információk a következőket biztosítják a rendszergazdáknak:

* Betekintést nyerhet az erőforrás-konfigurációba, amely nem felel meg a javasolt ajánlott eljárásoknak
* Útmutató adott szervizelési műveletekhez
* Rangsorok, amelyek azt jelzik, hogy mely szervizelési műveleteket magas prioritással kell elvégezni

### <a name="azure-policy"></a>Azure Policy

A [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) lehetővé teszi az Azure-erőforrások típusait és az engedélyezett konfigurációt szabályozó szabályok alkalmazását. Az erőforrás-létrehozás és-konfiguráció szabályozására, illetve a konfigurációs beállítások egy adott környezetben történő naplózására szolgáló szabályzat használható. Ezek a vizsgálati eredmények a Szervizelési tevékenységek alapjaként használhatók. A Azure Policy eltér a szerepköralapú hozzáférés-vezérléstől (RBAC). A Azure Policy az erőforrások és azok konfigurációjának korlátozására szolgál, a RBAC pedig az Azure-felhasználókra emelt szintű hozzáférés korlátozására szolgál.

Az adott házirend érvénybe léptetése vagy a házirend hatásának naplózása folyamatban van-e, a szabályzatok megfelelőségét folyamatosan figyelemmel kíséri, és az erőforrás-specifikus megfelelőségi információk a rendszergazdák számára is elérhetők. Azure Policy megfelelőségi információ Azure Security Center és a biztonságos pontszám részét képezi.

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

Az Eseménynapló-stratégia megvalósításakor a ASCS ISM kiemeli a következő szempontokat:

* Központosított naplózási létesítmények
* Naplózandó események
* Eseménynapló-védelem
* Eseménynapló megőrzése
* Eseménynapló naplózása

A naplók gyűjtésével és kezelésével kapcsolatban az ISM a szervezet informatikai környezetének rutin sebezhetőségi felmérését is javasolja.

### <a name="centralised-logging"></a>Központosított naplózás

Minden naplózási megoldásnak, ahol csak lehet, egyetlen adattárba kell összevonnia a rögzített naplókat. Ez nem csupán csökkenti a működési bonyolultságot, és meggátolja a több adatsiló létrehozását, lehetővé teszi a több forrásból gyűjtött adatok elemzését, valamint a korrelációs események azonosítását. Ez kritikus fontosságú a számítógépes biztonsági incidensek hatókörének észleléséhez és kezeléséhez.

Ezt a követelményt minden Azure Monitor rendelkező Azure-ügyfél esetében teljesíteni kell. Ez az ajánlat nem csupán központosított naplózási tárházat biztosít az Azure-ban az összes Azure-erőforráshoz, továbbá lehetővé teszi, hogy az Azure Event hub-ba továbbítsa az adatait. Az Azure Event Hubs teljes körűen felügyelt, valós idejű adatfeldolgozási szolgáltatást biztosít. Miután Azure Monitor adatokat adatfolyamként egy Azure Event hub-ba, az adatok könnyedén csatlakoztathatók a meglévő támogatott biztonsági információkhoz és az Event Management (SIEM) adattárakhoz, illetve további külső figyelési eszközökhöz is.

A Microsoft emellett saját Azure-beli natív SIEM-megoldást is kínál, amely az Azure Sentinel. Az Azure Sentinel számos adatösszekötőt támogat, és a biztonsági események figyelésére használható a teljes vállalaton belül. A támogatott adatösszekötők, [](https://docs.microsoft.com/azure/sentinel/connect-data-sources)az Azure Sentinel beépített gépi tanulás és a Kusto lekérdezési nyelv kombinálásával a biztonsági rendszergazdák egyetlen megoldást kapnak a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra, és a fenyegetésre adott válasz. A Sentinel egy vadászati és notebook funkciót is biztosít, amely lehetővé teszi, hogy a biztonsággal foglalkozó rendszergazdák egy szervezeten belül megosztható, újrahasznosítható forgatókönyvben rögzítsék a biztonsági vizsgálat részeként végrehajtott lépéseket. A biztonsági rendszergazdák a beépített [felhasználói elemzések](https://docs.microsoft.com/azure/sentinel/user-analytics) segítségével is megvizsgálják az egy kijelölt felhasználó műveleteit.

### <a name="logged-events-and-log-detail"></a>Naplózott események és napló részletei

Az ISM a naplózási stratégia részét képező Eseménynapló-típusok részletes listáját tartalmazza. A rögzített naplóknak megfelelő részletességgel kell rendelkezniük az elemzés és a vizsgálatok során felmerülő gyakorlati használathoz.

Az Azure-ban összegyűjtött naplók a következő három kategória egyike alá esnek:

* **Vezérlési és felügyeleti naplók**: Ezek a naplók a Azure Resource Manager LÉTREHOZÁSi, frissítési és TÖRLÉSi műveleteiről nyújtanak információt.

* **Adatsíkok naplói**: Ezek az Azure-erőforrások használatának részeként kiváltott eseményeket tartalmaznak. Olyan forrásokat tartalmaz, mint a Windows-eseménynaplók, beleértve a rendszer-, a biztonsági és az alkalmazás-naplókat.

* **Feldolgozott események**: Ezek az események az Azure által az ügyfél nevében automatikusan feldolgozott eseményekkel és riasztásokkal kapcsolatos információkat tartalmaznak. A feldolgozott esemény például egy Azure Security Center riasztás.

Az Azure virtuális gépek figyelését a Windows és a Linux rendszerhez készült virtuálisgép-ügynök üzembe helyezése fokozza. Ez jelentősen növeli az összegyűjtött naplózási adatok szélességét. Az ügynök üzembe helyezése beállítható úgy, hogy automatikusan megtörténjen a Azure Security Center használatával.

A Microsoft részletes információkat nyújt az Azure-erőforrásokra vonatkozó naplókkal és azok [sémájával](https://docs.microsoft.com/azure/security/fundamentals/log-audit)kapcsolatban.

### <a name="log-retention-and-protection"></a>Naplók megőrzése és védelme

 Az eseménynaplókat biztonságosan kell tárolni a szükséges megőrzési időszakra vonatkozóan. Az ISM azt tanácsolja, hogy a naplók legalább hét évig megmaradnak. Az Azure számos eszközt biztosít az összegyűjtött naplók hosszú élettartamának biztosításához. Alapértelmezés szerint az Azure log-események 90 napra vannak tárolva. Azure Monitor által rögzített naplózási adatok áthelyezhetők és tárolhatók egy Azure Storage-fiókban a hosszú távú adatmegőrzéshez szükséges módon. Az Azure Storage-fiókokban tárolt tevékenység-naplók megtekinthetők meghatározott számú napig, vagy határozatlan időre, ha szükséges.

Az Azure log-események tárolására használt Azure Storage-fiókok geo-redundánsak lehetnek, és Azure Backup használatával készíthetők biztonsági másolat. Miután a Azure Backup rögzítette, a naplókat tartalmazó biztonsági másolatok törlését a rendszergazdai jóváhagyást és törlésre kijelölt biztonsági mentéseket a rendszer a helyreállítást engedélyező 14 napig továbbra is megtartja. A Azure Backup lehetővé teszi egy védett példány 9999 példányának használatát, amely több mint 27 év napi biztonsági mentést biztosít.

A szerepköralapú Access Control az Azure-naplózáshoz használt erőforrásokhoz való hozzáférés szabályozására kell használni. A Azure Monitor, az Azure Storage-fiókokat és az Azure biztonsági mentéseket szerepköralapú hozzáférés-vezérléssel kell konfigurálni, hogy a naplókban tárolt adatokat biztonságban lehessen biztosítani.

### <a name="log-auditing"></a>Napló naplózása

A naplók valódi értékét a rendszer az elemzés után felismeri. Az automatizált és a manuális elemzés, valamint az elérhető eszközök megismerése révén segítséget nyújt a szervezeti biztonsági házirend és a számítógépes biztonsági incidensek megsértésének észleléséhez és kezeléséhez. Azure Monitor az összegyűjtött naplók elemzésére szolgáló eszközök széles választékát kínálja. Ennek az elemzésnek az eredménye ezután megosztható a rendszerek között, több formátumban is megjeleníthető vagy terjeszthető.

A Azure Monitor tárolt adatnaplók Log Analytics munkaterületen tárolódnak. Minden elemzés egy lekérdezéssel kezdődik. Azure Monitor lekérdezések a Kusto lekérdezési nyelvén íródnak. A lekérdezések a Azure Monitor összes kimenetének alapját képezik, az Azure-Irányítópultokról a riasztási szabályokra.

![Az Azure log-lekérdezések áttekintése](media/queries-overview.png)

A naplók naplózása a figyelési megoldások használatával javítható. Ezek olyan előre csomagolt megoldások, amelyek gyűjteményi logikát, lekérdezéseket és adatvizualizációs nézeteket tartalmaznak. A Microsoft számos megfigyelési megoldást és további megoldásokat kínál a termék-előállítók [számára](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) az Azure piactéren.

### <a name="vulnerability-assessment-and-management"></a>Sebezhetőségi felmérés és felügyelet

Az ISM megjegyzi, hogy a biztonsági rések felmérése és kezelése elengedhetetlen. Az informatikai környezet folyamatosan fejlődik, és a külső biztonsági fenyegetések végtelenül változnak. A Azure Security Center lehetővé teszi az automatikus sebezhetőségi felméréseket, és útmutatást nyújt a Szervizelési tevékenységek tervezéséhez és végrehajtásához.

A Azure Security Center biztonsági pontszáma felsorolja azokat az ajánlásokat, amelyek alkalmazása esetén a környezet biztonsága javulni fog. A lista a teljes biztonsági pontszámra gyakorolt hatás alapján a legmagasabbtól a legalacsonyabb értékre van rendezve. A lista hatásának megrendelése lehetővé teszi, hogy a legmagasabb prioritású ajánlásokra összpontosítson, amelyek a lehető legnagyobb értéket jelentik a biztonság növelése érdekében.

A Azure Policy a biztonsági rések felmérésében is kulcsszerepet játszik. A Azure Policy tartományban elérhető házirend-típusok az erőforrás-címkék és-értékek érvényesítésével, az erőforrások létrehozásához használható Azure-régiók korlátozásával, valamint az egyes erőforrástípusok teljes létrehozásának blokkolásával. Az Azure-szabályzatok egy csoportja kezdeményezésekbe csoportosítható. A kezdeményezések a kapcsolódó Azure-szabályzatok alkalmazására szolgálnak, amelyeket csoportként alkalmazva egy adott biztonsági vagy megfelelőségi célkitűzés alapján alkotnak.

Azure Policy tartalmaz egy beépített szabályzat-definíciót tartalmazó könyvtárat, amely folyamatosan bővül. A Azure Portal lehetővé teszi saját egyéni Azure Policy-definíciók készítését is. Miután megtalálta a szabályzatot a meglévő könyvtárban, vagy létrehozhat egy újat, hozzárendelheti a szabályzatot az Azure-erőforrásokhoz. Ezeket a hozzárendeléseket [](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) az erőforrás-kezelési hierarchia különböző szintjein lehet hatókörbe állítani. A házirend-hozzárendelés örökölt, ami azt jelenti, hogy a hatókörben lévő összes alárendelt erőforrás ugyanazt a szabályzat-hozzárendelést kapja. Az erőforrások a hatókörön belüli szabályzat-hozzárendelésből is kizárhatók a szükséges módon.

Az összes üzembe helyezett Azure-szabályzat hozzájárul a szervezet biztonságos pontszámához. A kiválóan testre szabott környezetekben létrehozhatók és üzembe helyezhetők egyéni Azure Policy-definíciók, amelyek adott számítási feladatokhoz igazított naplózási információkat biztosítanak.

## <a name="getting-started"></a>Első lépések

A Azure Security Center és az Azure Monitor, az Advisor és a Policy teljes körű használatának elkezdéséhez a Microsoft a következő kezdeti lépéseket javasolja:

* Azure Security Center engedélyezése
* Frissítés a standard szintre
* A Microsoft monitoring Agent Automatikus kiépítés engedélyezése a támogatott Azure-Virtual Machines
* A biztonsági javaslatok és riasztások áttekintése, rangsorolása és enyhítése a Security Center irányítópultján

## <a name="next-steps"></a>További lépések

A szabályzatok és a szabályozásoknak való megfelelés biztosítása érdekében olvassa el a [Azure Policy és az Azure tervrajzait](azure-policy.md) .
