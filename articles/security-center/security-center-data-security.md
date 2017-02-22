---
title: "Az Azure Security Center által nyújtott adatbiztonság | Microsoft Docs"
description: "Ez a dokumentum bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0


---
# <a name="azure-security-center-data-security"></a>Az Azure Security Center által nyújtott adatbiztonság
Az Azure Security Center biztonsággal kapcsolatos adatokat gyűjt és dolgoz fel (például konfigurációs információkat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és még sok mást), hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és elhárításában. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Ez a cikk bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.


## <a name="data-sources"></a>Adatforrások
Az Azure Security Center az alábbi forrásokból kapott adatok elemzésével biztosítja a biztonsági állapot áttekinthetőségét, azonosítja a biztonsági réseket, javításokat javasolj, valamint észleli az aktív fenyegetéseket:

- Azure Services: az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat használja.
- Hálózati forgalom: a hálózati forgalom metaadataiból vett mintát használja a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.
- Partnermegoldások: Az integrált partnermegoldásoktól, például tűzfalaktól és kártevőirtó megoldásoktól érkező biztonsági riasztásokat használja.
- Saját virtuális gépek: Konfigurációs és biztonsági eseményekkel kapcsolatos információkat használ (például Windows-esemény- és vizsgálati naplókat, IIS-naplókat, rendszernapló-üzeneteket és a virtuális gépekről származó összeomlási memóriaképeket).


## <a name="data-protection"></a>Adatvédelem
**Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes.

**Adathozzáférés**: Annak érdekében, hogy biztonsági javaslatokat biztosítsanak és megvizsgálják a lehetséges biztonsági fenyegetéseket, a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által gyűjtött vagy elemzett adatokhoz (például összeomlási memóriaképekhez, folyamat-létrehozási eseményekhez, a virtuálisgép-lemez pillanatképeihez és összetevőihez, amelyek véletlenül tartalmazhatnak virtuális gépekről származó ügyfél- és személyes adatokat). Betartjuk a [Microsoft Online Services és az adatvédelmi nyilatkozat](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

**Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

## <a name="data-location"></a>Az adatok helye
**Tárfiók(ok)**: Minden régióhoz, amelyben virtuális gép fut, meg van adva egy tárfiók. Ez lehetővé teszi, hogy abban a régióban tárolja az adatait, amelyben az a virtuális gép található, amelyről az adatok gyűjtése történik. Tárfiókja tartósan tárolja ezeket az adatokat, beleértve az összeomlási memóriaképeket is. A virtuálisgép-lemez pillanatképeit ugyanaz a tárfiók tárolja, mint a virtuálisgép-lemezt.

**Az Azure Security Center tárhelye**: A biztonsági riasztásokkal (beleértve a partnerriasztásokat), javaslatokkal és biztonsági állapottal kapcsolatos információk tárolása jelenleg az Egyesült Államokban, központilag történik. Ez az információ magában foglalhat az Ön virtuális gépeiről összegyűjtött kapcsolódó konfigurációs adatokat és biztonsági eseményeket szükség szerint annak érdekében, hogy megjeleníthessük Önnek a biztonsági riasztásokat, a javaslatokat vagy a biztonsági állapot adatait.

Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva. Az Azure Security Center az elemzéseket ugyanazon a földrajzi helyen végzi el, ahol a munkaterület található, és az elemzés után törli az ideiglenes másolatokat.

A gépösszetevők tárolása központilag történik, ugyanabban a régióban, ahol a virtuális gép is található.


## <a name="managing-data-collection-from-virtual-machines"></a>Az adatgyűjtés kezelése a virtuális gépeken
Ha az Azure Security Center engedélyezését választja, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az adatgyűjtést az Azure Security Center Biztonsági házirend részén is bekapcsolhatja. Amikor az adatgyűjtés be van kapcsolva, az Azure Security Center létrehozza az Azure-figyelőügynököt minden meglévő támogatott virtuális gépen és minden létrehozott új gépen. Az Azure Security Monitoring bővítmény megkeresi a biztonsághoz kapcsolódó különböző konfigurációkat, és eseményként felveszi azokat a [Windows esemény-nyomkövetés](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) nyomkövetéseibe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. Az Azure-figyelőügynök kiolvassa az eseménynapló-bejegyzéseket és az ETW-nyomkövetéseket, és átmásolja azokat az Ön tárfiókjába elemzés céljából.

A virtuális gépekkel kapcsolatos adatok gyűjtését bármikor letilthatja, és ezzel a művelettel eltávolítja az Azure Security Center által korábban telepített összes figyelőügynököt. A virtuálisgép-lemez pillanatképeinek és összetevőinek gyűjtése akkor is engedélyezve lesz, ha letiltotta az adatgyűjtést.


## <a name="see-also"></a>Lásd még:
Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Az Azure Security Center tervezésével, valamint a bevezetés során fontos elemekkel kapcsolatos útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Feb17_HO1-->


