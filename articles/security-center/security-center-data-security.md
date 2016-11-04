---
title: Az Azure Security Center által nyújtott adatbiztonság | Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2016
ms.author: yurid

---
# Az Azure Security Center által nyújtott adatbiztonság
Annak érdekében, hogy segítsen az ügyfeleknek a fenyegetések megelőzésében, észlelésében és az azokra való reagálásban, az Azure Security Center összegyűjti és feldolgozza az Azure-erőforrásainak adatait, beleértve a konfigurációs adatokat, a metaadatokat, az eseménynaplókat, az összeomlási memóriaképeket stb. Fontos kötelességünknek tekintjük ezen adatok védelmét és biztonságát. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig. 

Ez a cikk bemutatja, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik a védelmükről.

## Adatforrások
Az Azure Security Center a következő forrásokból származó adatokat elemzi:

* Azure Services: beolvassa az adott erőforrás-szolgáltatóval kommunikálva üzembe helyezett Azure-szolgáltatások konfigurációjával kapcsolatos információkat.
* Hálózati forgalom: beolvassa a hálózati forgalom metaadataiból vett mintát a Microsoft infrastruktúrájából, például a forrás és a cél IP-címét és portját, a csomagméretet és a hálózati protokollt.
* Partneri megoldások: összegyűjti az integrált partneri megoldások, például a tűzfalak és a kártevőirtó megoldások biztonsági riasztásait. Ezeket az adatokat az Azure Security Center tárolója tartalmazza, amely jelenleg az Amerikai Egyesült Államokban található.
* Az Ön virtuális gépei: az Azure Security Center konfigurációs adatokat és információkat tud gyűjteni a biztonsági eseményekről, például a Windows eseménynaplóit és naplóit, az IIS-naplókat, a syslog-üzeneteket és az összeomlási memóriaképeket az adatgyűjtési ügynököt használó virtuális gépeiről . További részleteket „Az adatgyűjtés kezelése” című alábbi szakaszban talál.  

Ezenkívül az Azure Security Center tárolója (amely jelenleg az Amerikai Egyesült Államokban található) tartalmazza a biztonsági riasztásokkal kapcsolatos információkat, a javaslatokat és a biztonsági állapot adatait. Ez az információ magában foglalhat az Ön virtuális gépeiről összegyűjtött kapcsolódó konfigurációs adatokat és biztonsági eseményeket szükség szerint annak érdekében, hogy megjeleníthessük Önnek a biztonsági riasztásokat, a javaslatokat vagy a biztonsági állapot adatait.

## Adatvédelem
**Az adatok elkülönítése**: az adatok logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. Ezenkívül az Ön tárfiókjai tárolják a virtuális gépeiről összegyűjtött adatokat.

**Az adatok elérése**: a biztonsági javaslatok és a lehetséges biztonsági fenyegetések kivizsgálása végett a Microsoft munkatársai hozzáférhetnek az Azure-szolgáltatások által összegyűjtött vagy elemzett információhoz, beleértve a összeomlási memóriaképek fájljait. Előfordulhat, hogy az összeomlási memóriaképek és a folyamat-létrehozási események véletlenül a virtuális gépeiről származó ügyféladatokat vagy személyes adatokat is tartalmaznak. Betartjuk a [Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) és az [adatvédelmi nyilatkozat](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) feltételeit, amelyek szerint a Microsoft nem használja fel az ügyféladatokat, és nem veszi igénybe azokat hirdetési vagy hasonló kereskedelmi célokra. Az ügyféladatokat szükség esetén csak arra használjuk, hogy biztosítsuk Önnek az Azure-szolgáltatásokat, beleértve a szolgáltatások nyújtásának megfelelő célokat is. Minden, az ügyféladatokhoz fűződő jog az Ön tulajdonában marad.

**Az adatok felhasználása**: a Microsoft a különböző bérlőknél észlelt mintákat és fenyegetésre vonatkozó intelligenciát használ a megelőzési és észlelési funkcióihoz, és ezt az [adatvédelmi nyilatkozatában](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx) ismertetett adatvédelmi kötelezettségeinek megfelelően teszi.

**Az adatok helye**: tárfiók van megadva minden régióhoz, amelyben virtuális gépek futnak. Ez lehetővé teszi, hogy abban a régióban tárolja az adatait, amelyben az a virtuális gép található, amelyről az adatok gyűjtése történik. Tárfiókja tartósan tárolja ezeket az adatokat, beleértve az összeomlási memóriaképeket is. Ezenkívül a szolgáltatás adatokat tárol a biztonsági riasztásokról, például az integrált partneri megoldások riasztásairól, a javaslatokról és a biztonság állapotáról az Azure Security Center tárolójában, amely jelenleg az Amerikai Egyesült Államokban található.

## Az adatgyűjtés kezelése a virtuális gépeken
Ha az Azure Security Center engedélyezését választja, az adatgyűjtés bekapcsolódik minden előfizetésénél. Az adatgyűjtést kikapcsolhatja az Azure Security Center irányítópultjának „Biztonsági szabályzat” szakaszában. Amikor az adatgyűjtés be van kapcsolva, az Azure Security Center létrehozza az Azure-figyelőügynököt minden meglévő támogatott virtuális gépen és minden létrehozott új gépen. Az Azure Security Monitoring bővítmény megkeresi a biztonsághoz kapcsolódó különböző konfigurációkat, és eseményként felveszi azokat a [Windows esemény-nyomkövetés](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) nyomkövetéseibe. Az operációs rendszer emellett az eseménynaplóba írandó eseményeket hoz létre a gép futtatása során. A gyűjtött adatok például a következők: az operációs rendszer típusa és verziója, az operációs rendszer naplói (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek, a bejelentkezett felhasználó és a bérlő azonosítója. Az Azure-figyelőügynök kiolvassa az eseménynapló-bejegyzéseket és az ETW-nyomkövetéseket, és átmásolja azokat az Ön tárfiókjába elemzés céljából. 

Külön tárfiók tartozik minden olyan régióhoz, amelyben virtuális gépeket működtet, és ezek a tárfiókok tárolják az adott régió virtuális gépeiről összegyűjtött adatokat. Így egy földrajzi területen belül tárolhatja az adatokat, ami segít az adatok védelmében, és az egy helyre tartozó adatok közös joghatóság alá helyezésében. Az egyes régiók tárfiókjait az Azure Security Center irányítópultjának „Biztonsági szabályzat” szakaszában konfigurálhatja.

Az Azure-figyelőügynök az összeomlási memóriaképek fájljait is átmásolja a tárfiókba.  Az Azure Security Center ideiglenes másolatokat gyűjt az összeomlási memóriaképek fájljairól, és elemzi ezeket a biztonsági réseket kihasználó támadások és a sikeres feltörések nyomai után kutatva.  Az Azure Security Center abban a földrajzi régióban hajtja végre az elemzést, amelyben a tárfiók található, és az elemzés befejezése után törli az ideiglenes példányokat.

A virtuális gépekkel kapcsolatos adatok gyűjtését bármikor letilthatja, és ezzel a művelettel eltávolítja az Azure Security Center által korábban telepített összes figyelőügynököt.

## Következő lépések
Ebből a dokumentumból megtudta, hogyan kezeli az Azure Security Center az adatokat, és hogyan gondoskodik azok védelméről. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Az Azure Security Center tervezésével, valamint a bevezetés során fontos elemekkel kapcsolatos útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--HONumber=Sep16_HO4-->


