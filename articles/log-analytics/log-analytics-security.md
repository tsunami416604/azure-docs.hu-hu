---
title: "Jelentkezzen az Analytics-adatok biztonsági |} Microsoft Docs"
description: "Információ hogyan Naplóelemzési adatvédelmi és titkosítja az adatokat."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.openlocfilehash: 91af774560860b35913e57b49fb7a1dd59f5640f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-security"></a>Naplófájl Analytics adatok biztonsága
A Microsoft elkötelezett az Ön adatainak védelmében, és az adatok védelme, miközben nagy szoftverek és -szolgáltatások, amelyek segítséget nyújtanak a szervezet informatikai infrastruktúrájának kezelése. Felismertük, hogy megbízhat az adatokat, hogy mások számára, amikor adott megbízhatósági szigorú biztonsági igényel. A Microsoft szigorú megfelelőségi és biztonsági szabályokat követ, a kódolástól kezdve egészen a szolgáltatások üzemeltetéséig.

Biztonságossá tétele és az adatok védelmének a Microsoft a legnagyobb prioritással. Lépjen velünk kapcsolatba, javaslatok, kérdéseivel kapcsolatban a következő adatokat, a biztonsági házirendek, beleértve a problémák [az Azure támogatási lehetőségek](http://azure.microsoft.com/support/options/).

Ez a cikk azt ismerteti, hogyan adatok gyűjtése, feldolgozása, és az Operations Management Suite (OMS) szolgáltatáshoz által védett. Ügynökök segítségével a webszolgáltatáshoz csatlakozni, használja a System Center Operations Manager operatív adatok gyűjtéséért felelős ügyfélfeladatot vagy adatainak lekérése az Azure diagnostics Naplóelemzési általi használatra. Az összegyűjtött adatok van az interneten keresztül használatával küldött tanúsítvány alapú hitelesítést & SSL 3 található Microsoft Azure Naplóelemzés szolgáltatáshoz. Az ügynök által adatok tömörített elküldés előtt.

A Naplóelemzési szolgáltatás felhőalapú adatait biztonságosan kezeli az alábbi módszerekkel:

* Az adatok elkülönítése
* az adatok megőrzése
* Fizikai biztonság
* Incidenskezelés
* Megfelelőségi
* biztonsági szabványok tanúsítványok

## <a name="data-segregation"></a>Az adatok elkülönítése
Ügyféladatok tartják logikailag külön minden egyes összetevő teljes az OMS szolgáltatáshoz. Az összes adat szervezet szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. Minden egyes ügyfélnek van dedikált Azure blob, amely a hosszú távú adatok

## <a name="data-retention"></a>Adatmegőrzés
Indexelt naplófájl-keresési adatok tárolásának és őrződnek meg az árképzési tervnek megfelelően. További információkért lásd: [napló Analytics Díjszabásáról](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft 30 nap az OMS-munkaterület bezárása után törli az ügyféladatokat. A Microsoft az adatokat tároló Azure Storage-fiókot is törli. Felhasználói adatok eltávolítása után nem fizikai meghajtók megsemmisülnek.

Az alábbi táblázat mutatja a rendelkezésre álló megoldások OMS és példák a típusú adatokat gyűjtenek.

| **Megoldás** | **Adattípusok** |
| --- | --- |
| Konfiguráció felmérése |Konfigurációs adatok, a metaadatok és a rendszerállapot-adatok |
| Kapacitástervezés |Teljesítményadatok és metaadatok |
| Kártevőirtó |Konfigurációs adatok és metaadatok |
| Rendszerfrissítési felmérés |Metaadat-és állapot |
| Naplókezelés |Felhasználó által definiált eseménynaplók, Windows-Eseménynapló és/vagy az IIS-napló |
| Változások követése |A szoftverleltár és a Windows szolgáltatás metaadatai |
| Az SQL és az Active Directory értékelése |WMI-adatok, a beállításjegyzék-adatok, a teljesítményadatokat és az SQL Server dinamikus felügyeleti eredmények megtekintése |

Az alábbi táblázat példákat adattípusok:

| **Adattípus** | **Mezők** |
| --- | --- |
| Riasztás |Riasztás neve riasztás leírása, BaseManagedEntityId, probléma azonosítója, IsMonitorAlert, RuleId, ResolutionState, prioritás, súlyosság, kategória, tulajdonos, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguráció |CustomerID, ügynökazonosító, entityid beállítást, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Esemény |Eseményazonosító, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, számot, kategória, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Megjegyzés:** írásakor egyéni mezők eseményeket a Windows eseménynaplóba, OMS gyűjti azokat. |
| Metaadatok |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP-cím, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, az IP- Cím NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, egyszerű név, OffsetInMinuteFromGreenwichTime |
| Teljesítmény |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Állapot |StateChangeEventId, StateId, NewHealthState, OldHealthState, a környezetben, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, monitorid attribútumként, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fizikai biztonság
A Naplóelemzési az OMS szolgáltatáshoz van működtetett Microsoft személyzete és az összes tevékenység naplózza, és naplózhatók. A szolgáltatás teljesen lefut az Azure-ban, és megfelel az Azure közös mérnöki. 18 lapján megtekintheti az Azure eszközök fizikai biztonsági adatait a [Microsoft Azure biztonsági áttekintése](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fizikai hozzáférési jogosultsága ahhoz, hogy biztonságos területek bárki, aki már nem rendelkezik az OMS-szolgáltatás, így az átvitel és a megszakítási felelősséget módosítja egy munkanapon belül. A jelenleg használt globális fizikai infrastruktúra olvashat [Microsoft Datacenters](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Incidenskezelés
OMS rendelkezik egy incidenskezelési folyamat, amely az összes Microsoft-szolgáltatás igazodik. Összefoglalva, azt:

* Ahol a Microsoft biztonsági felelős része tartozik, és az ügyfél tartozik egy részét megosztott felelősségi modellt használnak
* Az Azure biztonsági incidensek kezeléséhez
  * Indítsa el a vizsgálatot esemény észlelése
  * Mérje fel, a hatás és a súlyosság egy incidens által egy a készenléti incidensválasz-csoport tagja. Bizonyító adatok alapján, az értékelési előfordulhat, hogy vagy a további biztonsági válasz csoporthoz eszkalációs eredménye nem.
  * Végezze el a technikai vagy Törvényszéki nyomozások, elszigetelési, a megoldás és a megoldás stratégiák meghatározása biztonsági válasz szakértők által incidens diagnosztizálásához. Ha a biztonsági csoportja úgy véli, hogy ügyféladatokat előfordulhat, hogy rendelkezik lesz kitéve törvénybe ütköző vagy jogosulatlan személy, az ügyfél incidens értesítési folyamat párhuzamos végrehajtása párhuzamosan kezdődik.  
  * Stabilizálását, és az incidens helyreállíthatók. Az incidensekre adott reakciók csapata a probléma elhárítása érdekében helyreállítási terv létrehozása. Válság elszigetelési lépéseket karanténba helyezés érintett rendszerek például akkor fordulhat elő, azonnal, és diagnosztikai párhuzamosan. Hosszabb távú megoldást is kell tervezett bekövetkező azonnali kockázatának letelte után.  
  * Az incidens lezárása, és végezze el a levágást. Az incidensekre adott reakciók csapat hoz létre egy levágást, amely az incidens vizsgálja felül a házirendek, eljárások és az esemény ismétlődése megelőzése érdekében folyamatok szándékkal részleteit ismerteti.
* A biztonsági események ügyfelek értesítése
  * Határozza meg az érintett felhasználók és így birtokában bárki, aki egy értesítést a lehető legrészletesebb kihatással van
  * Hozzon létre egy értesítés, így rendelkező ügyfelek részletes elegendő információ ahhoz, hogy vizsgálatot végez a végfelhasználók és megfelelnek a vállalt azok rendelkezik a végfelhasználók számára nem jogosulatlanul késlelteti az értesítési folyamat során.
  * Erősítse meg, és az incidens, szükség szerint deklarálható.
  * Értesítse az incidens értesítések ésszerűtlenül haladéktalanul és jogi vagy szerződéses kötelezettségeket rendelkező ügyfelek. A biztonsági események értesítések bármilyen módon Microsoft választ, beleértve az e-mailben egy vagy több ügyfél rendszergazdák érkeznek.
* Végezze el a csapat készültségi és képzése
  * Microsoft csoporthoz biztonsági és tájékoztatási képzési, ami segít azonosítani és biztonsági problémák jelentése befejezéséhez.  
  * A Microsoft Azure szolgáltatásban működik operátorok körülvevő ügyféladatok üzemeltető érzékeny rendszerekre való hozzáadását képzési kötelezettségek rendelkeznek.
  * A Microsoft biztonsági válasz személyzet speciális képzésben a szerepkörükhöz

A felhasználói adatok elvesztése esetén értesítést mindegyik ügyfél egy napon belül. Azonban ügyfél soha nem történt adatvesztés az OMS Szolgáltatáshoz. Ezenkívül azt másolatait a létrehozott adatok, vagy földrajzilag elosztott van.

Hogyan reagál a Microsoft biztonsági eseményekre vonatkozó további információkért lásd: [Microsoft Azure biztonsági válasz a felhőben](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Megfelelőség
Az OMS szoftverek fejlesztési és szolgáltatás csapat információk biztonsági és irányítási program támogatja az üzleti követelmények és részben ismertetett módon törvény és szabályozás megfelelő [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) és [ A Microsoft biztonsági és adatkezelési központ megfelelőségi](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Hogyan OMS biztonsági követelményeket állapítja, biztonsági vezérlők azonosítja, kezeli, és figyeli a kockázatok is létezik ismerteti. Évente, azt felülvizsgálati házirendeket, szabványokat, eljárásokra és útmutatást.

Minden egyes OMS fejlesztési csoport egy tagja megkapja a formális alkalmazás biztonsági képzés. A verziókezelő rendszer belsőleg, szoftverek fejlesztésére használjuk. Minden szoftver projekt védik a rendszerhez.

A Microsoft hogyan felügyeli, és értékeli az összes szolgáltatás a Microsoft biztonsági és megfelelőségi csoport rendelkezik. Biztonsági informatikusok jött létre a team, és nincsenek OMS fejlesztése mérnöki osztályai társítva. A biztonsági tisztviselő saját felügyeleti lánc rendelkezik, és végezze el a termékek és szolgáltatások biztosításához, biztonsági és megfelelőségi független értékeléseket.

A Microsoft board vezetésében kapcsolatos összes információ biztonsági programok Microsoft éves jelentést értesíti.

Az OMS szoftverek fejlesztési és a szolgáltatás csapat egyéb iparági partnerekkel különböző tanúsítványok beszerzése és a Microsoft Legal és megfelelőségi csapat aktívan.

## <a name="certifications-and-attestations"></a>Tanúsítványok és tanúsítványok
OMS Naplóelemzési megfelel az alábbi követelményeknek:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* [Fizetési Card Industry (PCI szabványoknak) adatok biztonsági szabvány (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) a PCI biztonsági szabványok Tanács.
* [Szolgáltatás szervezet vezérlők (SOC) 1 1 és SOC 2 1-es típusú](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) megfelelő
* [HIPAA és HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) vállalatok számára megállapodással rendelkező HIPAA üzleti társítása
* A Windows gyakori mérnöki feltételek
* Microsoft Trustworthy Computing
* Az Azure-szolgáltatások, mint az összetevők OMS használó Azure megfelelőségi követelmények igazodik. A további [Microsoft Megbízhatósági központ megfelelőségi](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> Az egyes tanúsítványok/tanúsítványokat, Log Analyticshez megtalálható-e a korábbi nevét *Operational Insights*.
>
>


## <a name="cloud-computing-security-data-flow"></a>A felhőalapú informatika biztonsági adatfolyama
Az alábbi ábrán egy biztonsági architektúra irányuló információáramlást, a vállalat, és hogyan titkosítása, helyezi át a Naplóelemzés szolgáltatás, a végső soron láthatók, az OMS-portálon. További információ az egyes lépések a diagram követi.

![OMS adatok összegyűjtése és védelme képe](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. A Naplóelemzési és adatokat gyűjthet a Regisztrálás
A szervezet adatokat küldeni a Naplóelemzési Windows-ügynökök, Azure virtuális gépeken, vagy az OMS-ügynököt a Linux operációs rendszert futtató ügynökökön kell konfigurálni. Ha használja az Operations Manager-ügynökök, majd segítségével a konfigurációs varázsló az operatív konzolon konfigurálja őket. Felhasználók (ami lehet, hogy Ön, más felhasználókat vagy egy csoport tagjainak) hozzon létre egy vagy több OMS fiókokat (OMS-munkaterület), és ügynökök regisztrálni a következő fiókok egyikével:

* [Szervezeti Azonosítóval](../active-directory/sign-up-organization.md)
* [Microsoft-fiók – Outlook-Office Live, az MSN](http://www.microsoft.com/account/default.aspx)

Az OMS-munkaterület, ahol adatokat gyűjti, összesítve, elemzése, és jelenik meg. A munkaterület elsősorban partíció adatok segítségével, és egyes munkaterületeken egyedi. Például előfordulhat, hogy szeretné a termelési adatok használatával egy OMS-munkaterület felügyelt és a másik munkaterületen kezelt vizsgálati adatok. Munkaterületek is állítsunk egy rendszergazda felhasználó az adatokat. Minden felhasználói fiók hozzáférhessen a több OMS-munkaterület, és egyes munkaterületeken rendelkezhet több felhasználói fiókhoz társítva. A munkaterületek datacenter régió alapján hoz létre. Egyes munkaterületeken más régióban, amely elsősorban az OMS-szolgáltatás rendelkezésre állása adatközpontok replikálódik.

Az Operations Manager a konfigurációs varázsló befejezése után minden egyes Operations Manager felügyeleti csoport kapcsolatot létesít a Naplóelemzés szolgáltatással. Számítógépek hozzáadása varázsló kiválaszthatja, mely a felügyeleti csoportban lévő számítógépek számára engedélyezett adatokat küldeni a szolgáltatás majd használni. A más ügynök típusú csatlakozik biztonságosan az OMS szolgáltatáshoz.

Csatlakoztatott és a Naplóelemzés szolgáltatás közötti összes kommunikáció titkosítva van.  A TLS (HTTPS) protokollt használják a titkosítás.  A Microsoft SDL-folyamat Naplóelemzési naprakész, és a titkosítási protokollok legfrissebb eredményeit, így követi.

Minden ügynök típusú adatokat gyűjt a Naplóelemzési. A gyűjtött adatok típus használt megoldások típusú függ. Adatok gyűjtése: összefoglalása látható [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). Ezenkívül részletesebb gyűjtemény áll rendelkezésre információ a legtöbb megoldások. A megoldás egy előre meghatározott nézeteket, a naplófájl-keresési lekérdezések, az adatok gyűjtési szabályok és a feldolgozó logika kötegelt. Csak rendszergazdák használhatják a Log Analyticshez importálni egy megoldást. A megoldás az importálása után áthelyezése az Operations Manager felügyeleti kiszolgálón (ha van), majd választott összes ügynököt. Ezt követően az ügynökök összegyűjti az adatokat.

## <a name="2-send-data-from-agents"></a>2. Adatküldés az ügynökök
Minden ügynök típusok regisztrálása egy regisztrációs kulcsot, és az ügynök és a tanúsítvány alapú hitelesítést és az SSL használata a 443-as porton Naplóelemzés szolgáltatás közötti biztonságos kapcsolatot létesít. OMS titkos tároló létrehozása és karbantartása a kulcsokat használ. A titkos kulcsok legyenek-e elforgatva 90 naponta és az Azure-ban tároljuk, és kezeli az Azure üzemeltetése szigorú szabályozási és megfelelőségi vonatkozásairól követése.

Az Operations Manager a munkaterület regisztrálása a Log Analytics szolgáltatásban, és egy biztonságos HTTPS-kapcsolat létrehozása az Operations Manager felügyeleti kiszolgáló között.

Az Azure virtuális gépeken futó Windows-ügynökök, a csak olvasható tároló kulcs használatával kiolvassa az Azure-táblákban diagnosztikai eseményeket.

Minden ügynök nem tud kommunikálni a bármilyen okból szolgáltatással, ha a gyűjtött adatok egy ideiglenes gyorsítótárban helyben tárolódnak, és a felügyeleti kiszolgáló megpróbálná elküldeni az adatokat két órán át nyolc percenként. Az ügynök a gyorsítótárazott adatokat az operációs rendszer tanúsítványtároló védi. Ha a szolgáltatás nem tudja feldolgozni az adatokat két órával később, az ügynökök sorba kerülnek az adatok. Ha a várólista megtelik, OMS elindul, adattípusok, teljesítményadatokat kezdve eldobását. Az ügynök várólistára vonatkozó korlátozást egy beállításkulcs megadásával így módosíthatja, ha szükséges. Összegyűjtött adatok tömörített, és ezután a szolgáltatást, és megkerüljék a helyszíni adatbázisokhoz, így azt nem adja hozzá a terhelés őket. Az összegyűjtött adatokat küldi el, miután a rendszer eltávolítja a gyorsítótárból.

A fent leírtaknak megfelelően a ügynököktől adatküldést SSL-en keresztül a Microsoft Azure adatközpontjaiban. ExpressRoute segítségével szükség esetén további biztonsága érdekében az adatokat. ExpressRoute módja a közvetlenül csatlakozik Azure a meglévő WAN hálózaton, például a többprotokollos átváltását (MPLS) VPN-profilok, a szolgáltató által megadott címkével. További információkért lásd: [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. A Log Analytics szolgáltatás fogadja és dolgozza fel az adatokat
A Log Analytics szolgáltatás biztosítja, hogy a bejövő adatok megbízható forrásból érvényesítésével azonosítsa a tanúsítványok és az Azure hitelesítési adatok integritását. A feldolgozatlan nyers adatok majd tárolja a blob [Microsoft Azure Storage](../storage/common/storage-introduction.md) és nem titkosított. Azonban minden Azure storage-blob tartozik egy kulcsok, egyedi olyan készletét, amely csak az, hogy a felhasználó számára hozzáférhető. A tárolt adatok függ a típusú megoldások, amelyek importálva lettek, és adatok gyűjtéséért felelős ügyfélfeladatot. A Naplóelemzési szolgáltatás ezt követően a nyers adatokat az Azure storage-blob a dolgozza fel.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Log Analytics segítségével fér hozzá az adatokhoz
Regisztrálhat szolgáltatáshoz az OMS-portálon a szervezeti fiók vagy a korábban állítsa be Microsoft-fiók használatával. Az OMS-portálon és az OMS szolgáltatáshoz közötti összes forgalom HTTPS biztonságos csatornán keresztül zajlik. Az OMS-portálon használata esetén egy munkamenet-Azonosítót a felhasználói ügyfélen (webböngésző) jön létre, és a helyi gyorsítótárban tárolt adatok, mindaddig, amíg a munkamenet megszakítása. Ha leállt, a rendszer törli a gyorsítótár. Ügyféloldali cookie-kat, amelyek nem tartalmaznak személyes azonosításra alkalmas adatokat, nem lesznek automatikusan eltávolítva. Munkamenet-cookie-k HTTPOnly megjelölve, és biztosított. Egy előre meghatározott tétlen időszak után az OMS-portálon munkamenet megszakítása.

Az OMS-portált használja, exportálhatja adatokat tartalmazó CSV-fájl, és az adatok keresési API-k eléréséhez. Exportálás CSV korlátozódik exportálási 50 000 sorszám és API-adatok csak olyan keresési / 5000 sort.

## <a name="next-steps"></a>Következő lépések
* [Ismerkedés a Naplóelemzési](log-analytics-get-started.md) tudjon meg többet a Naplóelemzési, és működik, és perc múlva.
