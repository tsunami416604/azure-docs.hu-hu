---
title: Jelentkezzen az Analytics-adatok biztonsági |} Microsoft Docs
description: Információ hogyan Naplóelemzési adatvédelmi és titkosítja az adatokat.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magoedte
ms.openlocfilehash: f14b96b88a96f4bef24602bb9338a77352fbf375
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="log-analytics-data-security"></a>Naplófájl Analytics adatok biztonsága
Ez a dokumentum olyan kiegészítésére az adatokat az Azure Naplóelemzés információt [Azure biztonsági és adatkezelési központ](../security/security-microsoft-trust-center.md).  

Ez a cikk azt ismerteti, hogyan adatok gyűjtése, feldolgozása, és Naplóelemzési által védett. Ügynökök segítségével a webszolgáltatáshoz csatlakozni, használja a System Center Operations Manager operatív adatok gyűjtéséért felelős ügyfélfeladatot vagy adatainak lekérése az Azure diagnostics Naplóelemzési általi használatra. 

A Naplóelemzési szolgáltatás felhőalapú adatait biztonságosan kezeli az alábbi módszerekkel:

* Az adatok elkülönítése
* Adatmegőrzés
* Fizikai biztonság
* Incidenskezelés
* Megfelelőség
* biztonsági szabványok tanúsítványok

Lépjen velünk kapcsolatba, javaslatok, kérdéseivel kapcsolatban a következő adatokat, a biztonsági házirendek, beleértve a problémák [az Azure támogatási lehetőségek](http://azure.microsoft.com/support/options/).

## <a name="data-segregation"></a>Az adatok elkülönítése
Miután az adatok a Log Analytics szolgáltatás van okozhatnak, az adatok másolatok logikailag külön során a szolgáltatás minden egyes összetevő. Összes adat / munkaterület van megjelölve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. Az adatok a kiválasztott régióban tárolófürtben dedikált adatbázisban tárolódik.

## <a name="data-retention"></a>Adatmegőrzés
Indexelt naplófájl-keresési adatok tárolásának és őrződnek meg az árképzési tervnek megfelelően. További információkért lásd: [napló Analytics Díjszabásáról](https://azure.microsoft.com/pricing/details/log-analytics/).

Részeként a [előfizetői szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/), Microsoft megőrzi az adatokat a szerződés száma.  Az adatok törlése után azt is törli az adatokat tároló Azure Storage-fiókot.  Felhasználói adatok eltávolítása után nem fizikai meghajtók megsemmisülnek.  

Az alábbi táblázat mutatja be a rendelkezésre álló megoldások és példák a gyűjtött adatok típusát.

| **Megoldás** | **Adattípusok** |
| --- | --- |
| Kapacitást és teljesítményt |Teljesítményadatok és metaadatok |
| Kártevőfelmérés |Konfigurációs adatok és metaadatok |
| Frissítéskezelés |Metaadat-és állapot |
| Naplókezelés |Felhasználó által definiált eseménynaplók, Windows-Eseménynapló és/vagy az IIS-napló |
| Változások követése |A szoftverleltár, a Windows-szolgáltatás és a Linux-démon metaadatok és a Windows/Linux fájlok metaadatait |
| Az SQL és az Active Directory értékelése |WMI-adatok, a beállításjegyzék-adatok, a teljesítményadatokat és az SQL Server dinamikus felügyeleti eredmények megtekintése |

Az alábbi táblázat példákat adattípusok:

| **Adattípus** | **Mezők** |
| --- | --- |
| Riasztás |Riasztás neve riasztás leírása, BaseManagedEntityId, probléma azonosítója, IsMonitorAlert, RuleId, ResolutionState, prioritás, súlyosság, kategória, tulajdonos, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguráció |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Esemény |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Megjegyzés:** írásakor egyéni mezők eseményeket a Windows eseménynaplóba, OMS gyűjti azokat. |
| Metaadatok |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Teljesítmény |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Állapot |StateChangeEventId, StateId, NewHealthState, OldHealthState, a környezetben, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, monitorid attribútumként, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fizikai biztonság
A Naplóelemzési szolgáltatás Microsoft személyzete kezeli, és az összes tevékenység naplózza, és naplózhatók. A Naplóelemzési Azure szolgáltatásként működik, és megfelel-e az összes Azure megfelelőségi és biztonsági követelményeknek. 18 lapján megtekintheti az Azure eszközök fizikai biztonsági adatait a [Microsoft Azure biztonsági áttekintése](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fizikai hozzáférési jogosultsága ahhoz, hogy biztonságos területek bárki, aki már nem rendelkezik az OMS-szolgáltatás, így az átvitel és a megszakítási felelősséget módosítja egy munkanapon belül. A jelenleg használt globális fizikai infrastruktúra olvashat [Microsoft Datacenters](https://www.microsoft.com/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Incidenskezelés
OMS rendelkezik egy incidenskezelési folyamat, amely az összes Microsoft-szolgáltatás igazodik. Összefoglalva, azt:

* Ahol a Microsoft biztonsági felelős része tartozik, és az ügyfél tartozik egy részét megosztott felelősségi modellt használnak
* Az Azure biztonsági incidensek kezeléséhez:
  * Indítsa el a vizsgálatot esemény észlelése
  * Mérje fel, a hatás és a súlyosság egy incidens által egy a készenléti incidensválasz-csoport tagja. Bizonyító adatok alapján, az értékelési előfordulhat, hogy vagy a további biztonsági válasz csoporthoz eszkalációs eredménye nem.
  * Végezze el a technikai vagy Törvényszéki nyomozások, elszigetelési, a megoldás és a megoldás stratégiák meghatározása biztonsági válasz szakértők által incidens diagnosztizálásához. Ha a biztonsági csoportja úgy véli, hogy ügyféladatokat előfordulhat, hogy rendelkezik lesz kitéve törvénybe ütköző vagy jogosulatlan személy, az ügyfél incidens értesítési folyamat párhuzamos végrehajtása párhuzamosan kezdődik.  
  * Stabilizálását, és az incidens helyreállíthatók. Az incidensekre adott reakciók csapata a probléma elhárítása érdekében helyreállítási terv létrehozása. Válság elszigetelési lépéseket karanténba helyezés érintett rendszerek például akkor fordulhat elő, azonnal, és diagnosztikai párhuzamosan. Hosszabb távú megoldást is kell tervezett bekövetkező azonnali kockázatának letelte után.  
  * Az incidens lezárása, és végezze el a levágást. Az incidensekre adott reakciók csapat hoz létre egy levágást, amely az incidens vizsgálja felül a házirendek, eljárások és az esemény ismétlődése megelőzése érdekében folyamatok szándékkal részleteit ismerteti.
* Értesítse a biztonsági események ügyfelek:
  * Határozza meg az érintett felhasználók és így birtokában bárki, aki egy értesítést a lehető legrészletesebb kihatással van
  * Hozzon létre egy értesítés, így rendelkező ügyfelek részletes elegendő információ ahhoz, hogy vizsgálatot végez a végfelhasználók és megfelelnek a vállalt azok rendelkezik a végfelhasználók számára nem jogosulatlanul késlelteti az értesítési folyamat során.
  * Erősítse meg, és az incidens, szükség szerint deklarálható.
  * Értesítse az incidens értesítések ésszerűtlenül haladéktalanul és jogi vagy szerződéses kötelezettségeket rendelkező ügyfelek. A biztonsági események értesítések bármilyen módon Microsoft választ, beleértve az e-mailben egy vagy több ügyfél rendszergazdák érkeznek.
* Team készültsége és a képzési elvégzésével:
  * Microsoft csoporthoz biztonsági és tájékoztatási képzési, ami segít azonosítani és biztonsági problémák jelentése befejezéséhez.  
  * A Microsoft Azure szolgáltatásban működik operátorok körülvevő ügyféladatok üzemeltető érzékeny rendszerekre való hozzáadását képzési kötelezettségek rendelkeznek.
  * A Microsoft biztonsági válasz személyzet speciális képzésben a szerepkörükhöz

A felhasználói adatok elvesztése esetén értesítést mindegyik ügyfél egy napon belül. Azonban ügyfél soha nem történt adatvesztés a szolgáltatással. 

Hogyan reagál a Microsoft biztonsági eseményekre vonatkozó további információkért lásd: [Microsoft Azure biztonsági válasz a felhőben](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Megfelelőség
A Naplóelemzési szoftverek fejlesztési és szolgáltatás csapat információk biztonsági és irányítási program támogatja az üzleti követelmények és részben ismertetett módon törvény és szabályozás megfelelő [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) és [ A Microsoft biztonsági és adatkezelési központ megfelelőségi](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Hogyan Naplóelemzési biztonsági követelményeket állapítja, biztonsági vezérlők azonosítja, kezeli, és figyeli a kockázatok is létezik ismerteti. Évente, azt felülvizsgálati házirendeket, szabványokat, eljárásokra és útmutatást.

Minden fejlesztési csoport egy tagja megkapja a formális alkalmazás biztonsági képzés. A verziókezelő rendszer belsőleg, szoftverek fejlesztésére használjuk. Minden szoftver projekt védik a rendszerhez.

A Microsoft hogyan felügyeli, és értékeli az összes szolgáltatás a Microsoft biztonsági és megfelelőségi csoport rendelkezik. Biztonsági informatikusok jött létre a team, és nincsenek házon belül fejlesztett alkalmazásokra Naplóelemzési mérnöki osztályai társítva. A biztonsági tisztviselő saját felügyeleti lánc rendelkezik, és végezze el a termékek és szolgáltatások biztosításához, biztonsági és megfelelőségi független értékeléseket.

A Microsoft board vezetésében kapcsolatos összes információ biztonsági programok Microsoft éves jelentést értesíti.

A Naplóelemzési szoftverek fejlesztési és szolgáltatás team egyéb iparági partnerekkel különböző tanúsítványok beszerzése és a Microsoft Legal és megfelelőségi csapat aktívan.

## <a name="certifications-and-attestations"></a>Tanúsítványok és tanúsítványok
Az Azure Naplóelemzés megfelel az alábbi követelményeknek:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Fizetési Card Industry (PCI szabványoknak) adatok biztonsági szabvány (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) a PCI biztonsági szabványok Tanács.
* [Szolgáltatás szervezet vezérlők (SOC) 1 1 és SOC 2 1-es típusú](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) megfelelő
* [HIPAA és HITECH](https://www.microsoft.com/TrustCenter/Compliance/HIPAA) vállalatok számára megállapodással rendelkező HIPAA üzleti társítása
* A Windows gyakori mérnöki feltételek
* Microsoft Trustworthy Computing
* Az Azure-szolgáltatások, mint az összetevők Naplóelemzési használó Azure megfelelőségi követelmények igazodik. A további [Microsoft Megbízhatósági központ megfelelőségi](https://www.microsoft.com/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> Az egyes tanúsítványok/tanúsítványokat, Log Analyticshez megtalálható-e a korábbi nevét *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>A felhőalapú informatika biztonsági adatfolyama
Az alábbi ábrán egy biztonsági architektúra irányuló információáramlást, a vállalat, és hogyan, titkosítása helyezi át a Naplóelemzés szolgáltatás, az Azure portálon vagy a klasszikus OMS-portálon az Ön által végső soron látható. További információ az egyes lépések a diagram követi.

![Log Analytics-adatok gyűjtése és biztonsági képe](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. A Naplóelemzési és adatokat gyűjthet a Regisztrálás
A szervezet adatokat küldeni a Naplóelemzési a Windows vagy Linux-ügynököt futtató Azure virtuális gépeken, vagy a környezet vagy más felhőszolgáltatóként virtuális vagy fizikai számítógépeken kell konfigurálni.  Az Operations Manager használata, ha a felügyeleti csoport konfigurálhat az Operations Manager ügynököt. Felhasználók (ami lehet, hogy Ön, más felhasználókat vagy egy csoport tagjainak) hozzon létre egy vagy több Naplóelemzési munkaterület, és regisztrálja az ügynökök a következő fiókok egyikével:

* [Szervezeti Azonosítóval](../active-directory/sign-up-organization.md)
* [Microsoft-fiók – Outlook-Office Live, az MSN](http://www.microsoft.com/account/default.aspx)

A Naplóelemzési munkaterület, ahol adatokat gyűjti, összesítve, elemzése, és jelenik meg. A munkaterület elsősorban partíció adatok segítségével, és egyes munkaterületeken egyedi. Például előfordulhat, hogy szeretné a termelési adatok használatával egy munkaterület felügyelt és a másik munkaterületen kezelt vizsgálati adatok. Munkaterületek is állítsunk egy rendszergazda felhasználó az adatokat. Minden felhasználói fiók hozzáférhessen a több Naplóelemzési munkaterület, és egyes munkaterületeken rendelkezhet több felhasználói fiókhoz társítva. A munkaterületek datacenter régió alapján hoz létre. Egyes munkaterületeken más régióban, elsősorban a Log Analytics szolgáltatás rendelkezésre állása adatközpontok replikálódik.

Az Operations Manager az Operations Manager felügyeleti csoport kapcsolatot hoz létre a Naplóelemzés szolgáltatással. Ezután konfigurálja, a felügyeleti csoportnak az ügynök által felügyelt rendszerek gyűjtése és küldhetnek adatokat a szolgáltatás számára engedélyezett. Attól függően, hogy a megoldás úgy állította be, ezek a megoldások adatait vannak vagy közvetlenül az Operations Manager felügyeleti kiszolgálóról küldött a Naplóelemzés szolgáltatás, vagy az ügynök által felügyelt rendszer által gyűjtött adatok mennyisége miatt küldi a közvetlenül a szolgáltatás az ügynök. A rendszer nem Operations Manager által figyelt csatlakozik biztonságosan a Naplóelemzés szolgáltatás közvetlenül.

Csatlakoztatott és a Naplóelemzés szolgáltatás közötti összes kommunikáció titkosítva van.  A TLS (HTTPS) protokollt használják a titkosítás.  A Microsoft SDL-folyamat Naplóelemzési naprakész, és a titkosítási protokollok legfrissebb eredményeit, így követi.

Minden ügynök típusú adatokat gyűjt a Naplóelemzési. A gyűjtött adatok típus használt megoldások típusú függ. Adatok gyűjtése: összefoglalása látható [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). Ezenkívül részletesebb gyűjtemény áll rendelkezésre információ a legtöbb megoldások. A megoldás egy előre meghatározott nézeteket, a naplófájl-keresési lekérdezések, az adatok gyűjtési szabályok és a feldolgozó logika kötegelt. Csak rendszergazdák használhatják a Log Analyticshez importálni egy megoldást. A megoldás az importálása után áthelyezése az Operations Manager felügyeleti kiszolgálón (ha van), majd választott összes ügynököt. Ezt követően az ügynökök összegyűjti az adatokat.

## <a name="2-send-data-from-agents"></a>2. Adatküldés az ügynökök
Minden ügynök típusok regisztrálása egy regisztrációs kulcsot, és az ügynök és a tanúsítvány alapú hitelesítést és az SSL használata a 443-as porton Naplóelemzés szolgáltatás közötti biztonságos kapcsolatot létesít. A Naplóelemzési létrehozása és karbantartása kulcsok titkos tárolót használ. A titkos kulcsok legyenek-e elforgatva 90 naponta és az Azure-ban tároljuk, és kezeli az Azure üzemeltetése szigorú szabályozási és megfelelőségi vonatkozásairól követése.

Az Operations Manager a felügyeleti csoport egy Naplóelemzési munkaterület regisztrálva van egy Operations Manager felügyeleti kiszolgáló biztonságos HTTPS kapcsolatot létesít.

Az Azure virtuális gépeken futó Windows vagy Linux ügynökök, a csak olvasható tároló kulcs használatával kiolvassa az Azure-táblákban diagnosztikai eseményeket.  

Az összes ügynök, az Operations Manager felügyeleti csoport, amely integrálva van a Naplóelemzési jelent Ha a felügyeleti kiszolgáló nem tud kommunikálni bármilyen okból az összegyűjtött adatokat a szolgáltatás helyben tárolódnak egy ideiglenes gyorsítótárban a felügyeleti a kiszolgáló.   Próbálja újból elküldeni az adatokat két órán át nyolc percenként.  Nincs hatással a felügyeleti kiszolgáló, és közvetlenül a Naplóelemzési küldött adatok esetén a Windows-ügynök összhangban a viselkedés.  

Az operációs rendszer tanúsítványtároló a Windows vagy a felügyeleti kiszolgáló ügynök gyorsítótárazott adatok védelmét. Ha a szolgáltatás nem tudja feldolgozni az adatokat két órával később, az ügynökök sorba kerülnek az adatok. Ha a várólista megtelik, az ügynök elindul, adattípusok, teljesítményadatokat kezdve eldobását. Az ügynök várólistára vonatkozó korlátozást egy beállításkulcs megadásával így módosíthatja, ha szükséges. Összegyűjtött adatok tömörített és küldött a szolgáltatás, az Operations Manager felügyeleti csoport adatbázisok kihagyásával, így azt nem adja hozzá a terhelés őket. Az összegyűjtött adatokat küldi el, miután a rendszer eltávolítja a gyorsítótárból.

A fent leírtaknak megfelelően a felügyeleti kiszolgáló vagy a közvetlenül csatlakoztatott ügynökök adatküldést SSL-en keresztül a Microsoft Azure adatközpontjaiban. ExpressRoute segítségével szükség esetén további biztonsága érdekében az adatokat. ExpressRoute módja a közvetlenül csatlakozik Azure a meglévő WAN hálózaton, például a többprotokollos átváltását (MPLS) VPN-profilok, a szolgáltató által megadott címkével. További információkért lásd: [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. A Log Analytics szolgáltatás fogadja és dolgozza fel az adatokat
A Log Analytics szolgáltatás biztosítja, hogy a bejövő adatok megbízható forrásból érvényesítésével azonosítsa a tanúsítványok és az Azure hitelesítési adatok integritását. A feldolgozatlan nyers adatokat az Azure Event Hubs a régióban, végül az adatokat fog tárolni a aktívan majd tárolódik. A tárolt adatok függ a típusú megoldások, amelyek importálva lettek, és adatok gyűjtéséért felelős ügyfélfeladatot. Ezután a Naplóelemzési szolgáltatás folyamatok a nyers adatokat, és ingests azt az adatbázisba.

A megőrzési időtartam, az összegyűjtött adatokat az adatbázisban tárolt attól függ, hogy a kijelölt tarifacsomag. Az a *szabad* réteg, a 7 napig érhető összegyűjtött adatokat. Az a *fizetve* réteg, gyűjtött adatokat 31 napig alapértelmezés szerint, de kiterjeszthető 720 nap. Adatok titkosítása az Azure storage adatbizalmasság biztosításához tárolja. Az adatok az elmúlt két hétben is SSD-alapú gyorsítótárában vannak tárolva, és ez a gyorsítótár jelenleg nincs titkosítva.  Támogatja az ilyen titkosítási 2018 újabb felében tervezzük.  

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Log Analytics segítségével fér hozzá az adatokhoz
A Naplóelemzési munkaterület eléréséhez jelentkezzen be arra a szervezeti fiók vagy a Microsoft-fiókkal, amely korábban állítsa be az Azure portálon. A portál és Naplóelemzési közötti összes forgalom HTTPS biztonságos csatornán keresztül zajlik. A portál használata esetén egy munkamenet-Azonosítót a felhasználói ügyfélen (webböngésző) jön létre, és a helyi gyorsítótárban tárolt adatok, mindaddig, amíg a munkamenet megszakítása. Ha leállt, a rendszer törli a gyorsítótár. Ügyféloldali cookie-kat, amelyek nem tartalmaznak személyes azonosításra alkalmas adatokat, nem lesznek automatikusan eltávolítva. Munkamenet-cookie-k HTTPOnly megjelölve, és biztosított. Egy előre meghatározott tétlen időszak után az Azure portál munkamenet megszakítása.

## <a name="next-steps"></a>További lépések
* Tudnivalók az Azure virtuális gépek alábbi Naplóelemzési adatok gyűjtéséről a [Azure virtuális gép gyors üzembe helyezés](log-analytics-quick-collect-azurevm.md).  

*  Ha a adatokat gyűjteni a fizikai vagy virtuális Windows vagy Linux rendszerű számítógépek a környezetben, tekintse meg a [Linux rendszerű számítógépek a gyors üzembe helyezés](log-analytics-quick-collect-linux-computer.md) vagy [gyors üzembe helyezése a Windows rendszerű számítógépeken](log-analytics-quick-collect-windows-computer.md)

