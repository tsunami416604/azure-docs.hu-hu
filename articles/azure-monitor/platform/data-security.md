---
title: Log Analytics-adatok biztonsága | Microsoft dokumentumok
description: Ismerje meg, hogyan védi a Log Analytics az ön személyes adatait és védi az adatokat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 63d8d8d3701a9adca4bd01e6e061877f5d0bd245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333361"
---
# <a name="log-analytics-data-security"></a>A Log Analytics adatainak biztonsága
Ez a dokumentum az Azure Monitor szolgáltatásának megfelelő Log Analytics szolgáltatásra vonatkozó információk szolgáltatására szolgál az [Azure Trust Center](../../security/fundamentals/trust-center.md)ben található információk kiegészítéseként.  

Ez a cikk bemutatja, hogyan gyűjti és dolgozza fel a Log Analytics az adatokat, és hogyan gondoskodik a védelmükről. Az ügynökök segítségével csatlakozhat a webszolgáltatáshoz, a System Center Operations Manager segítségével gyűjthet működési adatokat, vagy adatokat kérhet le az Azure diagnosztikából a Log Analytics általi használatra. 

A Log Analytics szolgáltatás biztonságosan kezeli a felhőalapú adatokat a következő módszerekkel:

* Az adatok elkülönítése
* Adatmegőrzés
* Fizikai biztonság
* Incidenskezelés
* Megfelelőség
* Biztonsági szabványok tanúsításai

Lépjen kapcsolatba velünk az alábbi információkkal kapcsolatos kérdéseivel, javaslataival vagy problémáival kapcsolatban, beleértve az Azure támogatási lehetőségeinek biztonsági [szabályzatait](https://azure.microsoft.com/support/options/)is.

## <a name="sending-data-securely-using-tls-12"></a>Adatok biztonságos küldése a TLS 1.2 használatával 

A Log Analytics szolgáltatásba történő átvitel során az adatok biztonságának biztosítása érdekében javasoljuk, hogy konfigurálja az ügynököt legalább transport layer security (TLS) 1.2 használatára. A TLS/Secure Sockets Layer (SSL) régebbi verziói sebezhetőnek bizonyultak, és bár jelenleg is dolgoznak a visszamenőleges kompatibilitás érdekében, **nem ajánlottak,** és az iparág gyorsan elmozdul, hogy felhagyjon a régebbi protokollok támogatásával. 

A [PCI Biztonsági Szabványügyi Tanács](https://www.pcisecuritystandards.org/) [2018.](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) Miután az Azure eldobja az örökölt támogatást, ha az ügynökök nem tudnak kommunikálni legalább TLS 1.2-n keresztül, nem tud adatokat küldeni a Log Analytics szolgáltatásba. 

Nem javasoljuk kifejezetten, hogy az ügynököt csak a TLS 1.2 használatára állítja be, kivéve, ha feltétlenül szükséges, mivel megtörheti a platformszintű biztonsági funkciókat, amelyek lehetővé teszik az újabb, biztonságosabb protokollok automatikus észlelését és kihasználását, amint elérhetővé válnak, mint például mint A TLS 1.3. 

### <a name="platform-specific-guidance"></a>Platformspecifikus iránymutatás

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux disztribúciók általában az [OpenSSL-re](https://www.openssl.org) támaszkodnak a TLS 1.2 támogatáshoz.  | Ellenőrizze az [OpenSSL Changelog-ot,](https://www.openssl.org/news/changelog.html) hogy az OpenSSL verziója támogatott-e.|
| Windows 8.0 - 10 | Alapértelmezés szerint támogatott és engedélyezett. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)használja.  |
| Windows Server 2012 – 2016 | Alapértelmezés szerint támogatott és engedélyezett. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat használja-e](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Az engedélyezés engedélyezéséről a [Transport Layer Security (TLS) beállításjegyzék-beállításlapján](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) olvashat.  |

## <a name="data-segregation"></a>Az adatok elkülönítése
Miután a Log Analytics szolgáltatás beírta az adatokat, az adatok logikailag elkülönítve maradnak a szolgáltatás minden egyes összetevőjében. Minden adat munkaterületenként van címkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. Az adatok egy dedikált adatbázisban tárolódnak a kiválasztott régióban lévő tárolófürtben.

## <a name="data-retention"></a>Adatmegőrzés
Az indexelt naplókeresési adatok tárolása és megőrzése az árképzési tervnek megfelelően történik. További információt a [Log Analytics-díjszabás című](https://azure.microsoft.com/pricing/details/log-analytics/)témakörben talál.

Az [előfizetési szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/)részeként a Microsoft a szerződés feltételei szerint megőrzi az Ön adatait.  Az ügyféladatok eltávolításakor a fizikai meghajtók nem semmisülnek meg.  

Az alábbi táblázat néhány rendelkezésre álló megoldást sorol fel, és példákat mutat be az általuk gyűjtött adatok típusára.

| **Megoldás** | **Adattípusok** |
| --- | --- |
| Kapacitás és teljesítmény |Teljesítményadatok és metaadatok |
| Frissítéskezelés |Metaadatok és állapotadatok |
| Naplókezelés |Felhasználó által definiált eseménynaplók, Windows eseménynaplók és/vagy IIS-naplók |
| Változások követése |Szoftverleltár, Windows-szolgáltatás és Linux démonmetaadatok, valamint Windows/Linux fájl metaadatok |
| SQL és Active Directory-felmérés |WMI-adatok, beállításjegyzék-adatok, teljesítményadatok és SQL Server dinamikus felügyeleti nézet eredményei |

Az alábbi táblázat példákat mutat be adattípusokra:

| **Adattípus** | **Mezők** |
| --- | --- |
| Riasztás |Riasztás neve, Riasztás leírása, BaseManagedEntityId, Problémaazonosító, IsMonitorAlert, RuleId, ResolutionState, Prioritás, Súlyosság, Kategória, Tulajdonos, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguráció |Ügyfélazonosító, Ügynök, Entitásazonosító, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Esemény |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Megjegyzés:** Amikor egyéni mezőket beírásra ír eseményeket a Windows eseménynaplójába, a Log Analytics összegyűjti azokat. |
| Metaadatok |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Cím, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Teljesítmény |Objektumnév, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Állapot |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fizikai biztonság
A Log Analytics szolgáltatást a Microsoft munkatársai kezelik, és minden tevékenység et naplózza, és naplózható. A Log Analytics Azure-szolgáltatásként működik, és megfelel az Azure megfelelőségi és biztonsági követelményeinek. Az Azure-eszközök fizikai biztonságával kapcsolatos részleteket a [Microsoft Azure biztonsági áttekintésének](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)18. A biztonságos területekre vonatkozó fizikai hozzáférési jogok egy munkanapon belül megváltoznak mindenki számára, aki már nem felelős a Log Analytics szolgáltatásért, beleértve az átvitelt és a megszüntetést is. A [Microsoft Datacenters](https://azure.microsoft.com/global-infrastructure/)globális fizikai infrastruktúrájáról olvashat.

## <a name="incident-management"></a>Incidenskezelés
A Log Analytics olyan incidenskezelési folyamattal rendelkezik, amelyhez minden Microsoft-szolgáltatás megfelel. Összefoglalva, mi:

* Megosztott felelősségi modell használata, ahol a biztonsági felelősség egy része a Microsofthoz tartozik, egy része pedig az ügyfélhez tartozik
* Az Azure biztonsági incidenseikezelése:
  * Vizsgálat indítása egy esemény észlelése után
  * Az esemény hatásának és súlyosságának felmérése az ügyeletes incidensreagálási csapat tagja által. A bizonyítékok alapján az értékelés a biztonsági reagáló csapat további eszkalációját eredményezheti, vagy nem.
  * Diagnosztizálja az incidenst a biztonsági reagálási szakértők által a műszaki vagy törvényszéki vizsgálat lefolytatásához, az elszigetelési, csökkentési és kerülő stratégiák azonosításához. Ha a biztonsági csoport úgy véli, hogy az ügyféladatok jogosulatlan vagy jogosulatlan személynek kerülhettek ki, az Ügyfélincidens-értesítési folyamat párhuzamos végrehajtása párhuzamosan kezdődik.  
  * Stabilizálni és felépülni az esetből. Az incidens-elhárítási csapat helyreállítási tervet hoz létre a probléma enyhítésére. A válságelszigetelési lépések, mint például az érintett rendszerek karanténba kerülése, azonnal és a diagnózissal párhuzamosan fordulhatnak elő. Hosszabb távú mérséklések tervezhetők, amelyek a közvetlen kockázat megszűnését követően következnek be.  
  * Zárják le az incidenst, és végezzenek boncolást. Az incidens-elhárítási csapat létrehoz egy post mortem, amely felvázolja az esemény részleteit, azzal a szándékkal, hogy vizsgálja felül a szabályzatok, eljárások és folyamatok, hogy megakadályozzák az esemény megismétlődését.
* Az ügyfelek értesítése a biztonsági eseményekről:
  * Határozza meg az érintett ügyfelek körét, és adjon értesítést a lehető legrészletesebb értesítésről, aki érintett.
  * Hozzon létre egy értesítést, amely biztosítja az ügyfelek számára a részletes elegendő információt, hogy azok vizsgálatot végezhetnek a végén, és eleget tegyenek a végfelhasználóknak tett kötelezettségvállalásaiknak, miközben nem késleltetik indokolatlanul az értesítési folyamatot.
  * Erősítse meg és jelentse be az incidenst, ha szükséges.
  * Az ügyfeleket indokolatlan késedelem nélkül és a jogi vagy szerződéses kötelezettségvállalásnak megfelelően értesítse az incidensekről szóló értesítéssel. A biztonsági incidensekről szóló értesítéseket az ügyfél egy vagy több rendszergazdája, a Microsoft által kiválasztott bármilyen módon, többek között e-mailben is kézbesítjük.
* Magatartás csapat készenlét és a képzés:
  * A Microsoft munkatársainak biztonsági és tudatosságnövelő képzést kell végezniük, amely segít nekik a feltételezett biztonsági problémák azonosításában és jelentésben.  
  * A Microsoft Azure szolgáltatáson dolgozó operátorok további képzési kötelezettségekkel rendelkeznek az ügyféladatokat tároló érzékeny rendszerekhez való hozzáférésük kel kapcsolatban.
  * A Microsoft biztonsági válaszának munkatársai speciális képzésben részesülnek a szerepkörükhöz

Ha az ügyféladatok elvesztése bekövetkezik, egy napon belül értesítjük az ügyfeleket. Azonban az ügyfél adatvesztés soha nem történt meg a szolgáltatás. 

Ha többet szeretne tudni arról, hogy a Microsoft hogyan reagál a biztonsági incidensekre, olvassa el [a Microsoft Azure biztonsági válasza a felhőben című témakört.](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf)

## <a name="compliance"></a>Megfelelőség
A Log Analytics szoftverfejlesztő és -szolgáltató csapat információbiztonsági és -irányítási programja támogatja üzleti követelményeit, és betartja a [Microsoft Azure Adatvédelmi központban](https://azure.microsoft.com/support/trust-center/) és a Microsoft [Adatvédelmi központ megfelelőségi](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx)szabályzataiban leírt törvényeket és előírásokat. Hogyan log analytics biztonsági követelményeket, azonosítja a biztonsági ellenőrzések, kezeli, és figyeli a kockázatokat is ismertetik. Évente felülvizsgáljuk a rendőrségeket, szabványokat, eljárásokat és irányelveket.

Minden fejlesztőcsapat-tag hivatalos alkalmazásbiztonsági képzést kap. Belsőleg verziókezelő rendszert használunk a szoftverfejlesztéshez. Minden szoftverprojektet a verzióvezérlő rendszer véd.

A Microsoft rendelkezik egy biztonsági és megfelelőségi csapattal, amely felügyeli és értékeli a Microsoft összes szolgáltatását. Információbiztonsági tisztviselők alkotják a csapatot, és nem kapcsolódnak a Log Analytics-et kifejlesztésével rendelkező mérnöki csapatokhoz. A biztonsági tisztviselők saját irányítási lánccal rendelkeznek, és a biztonság és a megfelelőség biztosítása érdekében független termékeket és szolgáltatásokat végeznek.

A Microsoft igazgatótanácsát éves jelentés értesíti a Microsoft összes információbiztonsági programjáról.

A Log Analytics szoftverfejlesztő és -szolgáltatási csapata aktívan együttműködik a Microsoft jogi és megfelelőségi csapataival és más iparági partnerekkel a különböző tanúsítványok megszerzésében.

## <a name="certifications-and-attestations"></a>Tanúsítványok és tanúsítványok
Az Azure Log Analytics az alábbi követelményeknek felel meg:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* A PCI Security Standards Council által a PAYMENT [Card Industry (PCI-kompatibilis) adatbiztonsági szabvány (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) alapján.
* [Service Organization Controls (SOC) 1 1- es és SOC 2 1-es típus-kompatibilis](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA és HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) a HIPAA üzlettársi megállapodással kötött vállalatok számára
* A Windows általános tervezési feltételei
* Microsoft megbízható számítástechnika
* Azure-szolgáltatásként a Log Analytics által használt összetevők megfelelnek az Azure megfelelőségi követelményeinek. További információ: [Microsoft Adatvédelmi központ megfelelősége](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> Egyes tanúsítványokban/tanúsítványokban a Log Analytics az Operational Insights korábbi neve alatt jelenik *meg.*
>
>

## <a name="cloud-computing-security-data-flow"></a>Felhőalapú számítástechnikabiztonsági adatfolyam
Az alábbi ábrán egy felhőalapú biztonsági architektúra látható, amely a vállalattól származó információk áramlását, valamint a log analytics szolgáltatásba való áthelyezés módját mutatja be, amelyet végső soron Ön az Azure Portalon lát. Az egyes lépésekkel kapcsolatos további információk a diagramot követik.

![A Log Analytics adatgyűjtésés és -biztonság képe](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Regisztráljon a Log Analytics szolgáltatásra és gyűjtsön adatokat
A szervezet adatokat küldeni a Log Analytics, konfigurálja a Windows vagy Linux ügynök fut az Azure virtuális gépeken, vagy a virtuális vagy fizikai számítógépek a környezetben vagy más felhőszolgáltató.  Ha az Operations Managert használja, a felügyeleti csoportból konfigurálja az Operations Manager-ügynököt. A felhasználók (amelyek lehetnek Ön, más egyéni felhasználók vagy személyek egy csoportja) egy vagy több Log Analytics-munkaterületet hoznak létre, és az alábbi fiókok egyikével regisztrálják az ügyintézőket:

* [Szervezeti azonosító](../../active-directory/fundamentals/sign-up-organization.md)
* [Microsoft-fiók – Outlook, Office Live, MSN](https://account.microsoft.com/account)

A Log Analytics-munkaterület az adatok gyűjtése, összesítése, elemzése és bemutatása. A munkaterület elsősorban az adatok particionálására szolgál, és minden munkaterület egyedi. Előfordulhat például, hogy az éles adatokat egy munkaterülettel, a tesztadatokat pedig egy másik munkaterülettel szeretné kezelni. A munkaterületek azt is segítik a rendszergazdának, hogy szabályozzák a felhasználók hozzáférését az adatokhoz. Minden munkaterülethez több felhasználói fiók is tartozhat, és minden felhasználói fiók több Log Analytics-munkaterülethez is hozzáférhet. Az adatközpont-régió alapján hoz létre munkaterületeket.

Az Operations Manager esetében az Operations Manager felügyeleti csoport kapcsolatot létesít a Log Analytics szolgáltatással. Ezután konfigurálja, hogy a felügyeleti csoport mely ügynök által felügyelt rendszerei gyűjthetnek és küldhetnek adatokat a szolgáltatásnak. Az engedélyezett megoldástól függően az ezekből a megoldásokból származó adatokat vagy közvetlenül az Operations Manager felügyeleti kiszolgálóról küldi el a Log Analytics szolgáltatásnak, vagy az ügynök által felügyelt rendszer által gyűjtött adatok mennyisége miatt közvetlenül a a közvetítőt a szolgálathoz. Az Operations Manager által nem figyelt rendszerek esetén mindegyik közvetlenül csatlakozik a Log Analytics szolgáltatáshoz.

A csatlakoztatott rendszerek és a Log Analytics szolgáltatás közötti minden kommunikáció titkosítva van. A TLS (HTTPS) protokoll titkosításra szolgál.  A Microsoft SDL-folyamat ot követi annak biztosítása érdekében, hogy a Log Analytics naprakész legyen a kriptográfiai protokollok legújabb fejlődésével.

Minden ügynöktípus adatokat gyűjt a Log Analytics számára. Az összegyűjtött adatok típusa a használt megoldások típusától függ. Az adatgyűjtés összegzését a [Megoldások tárból származó Naplóelemzési megoldások hozzáadása című témakörben találja.](../../azure-monitor/insights/solutions.md) Ezenkívül a legtöbb megoldáshoz részletesebb adatgyűjtési információ is rendelkezésre áll. A megoldás egy előre definiált nézetek, naplókeresési lekérdezések, adatgyűjtési szabályok és feldolgozási logika kötege. Csak a rendszergazdák használhatják a Log Analytics egy megoldás importálásához. A megoldás importálása után a rendszer áthelyezi az Operations Manager felügyeleti kiszolgálóira (ha van ilyen), majd a kiválasztott ügynökökre. Ezután az ügynökök gyűjtik az adatokat.

## <a name="2-send-data-from-agents"></a>2. Adatok küldése ügynököktől
Minden ügynöktípust regisztrál egy regisztrációs kulccsal, és biztonságos kapcsolat jön létre az ügynök és a Log Analytics szolgáltatás között a tanúsítványalapú hitelesítés és a 443-as porttal rendelkező TLS használatával. A Log Analytics egy titkos tárolót használ a kulcsok létrehozásához és karbantartásához. A személyes kulcsok 90 naponta elforgathatók, és az Azure-ban tárolódnak, és az Okat, amelyek szigorú szabályozási és megfelelőségi gyakorlatot követnek, az Azure-műveletek kezelik őket.

Az Operations Manager segítségével a Log Analytics-munkaterülettel regisztrált felügyeleti csoport biztonságos HTTPS-kapcsolatot hoz létre az Operations Manager felügyeleti kiszolgálóval.

Az Azure virtuális gépeken futó Windows- vagy Linux-ügynökök esetében egy csak olvasható tárolási kulcs szolgál az Azure-táblák diagnosztikai eseményeinek olvasásához.  

Ha a naplóelemzéssel integrált Operations Manager felügyeleti csoportnak jelentést tesz bármely ügynök, ha a felügyeleti kiszolgáló bármilyen okból nem tud kommunikálni a szolgáltatással, az összegyűjtött adatokat a rendszer helyileg tárolja a felügyeleti felügyelet ideiglenes gyorsítótárában. Szerver.   Megpróbálják újraküldeni az adatokat nyolc percenként két órán keresztül.  A felügyeleti kiszolgálót megkerülő és közvetlenül a Log Analytics szolgáltatásba küldött adatok esetében a viselkedés összhangban van a Windows-ügynökkel.  

A Windows vagy a felügyeleti kiszolgáló ügynök gyorsítótárazott adatait az operációs rendszer hitelesítő adattárolója védi. Ha a szolgáltatás két óra elteltével nem tudja feldolgozni az adatokat, az ügynökök várólistára helyezik az adatokat. Ha a várólista megtelik, az ügynök elkezdi eldobni az adattípusokat, kezdve a teljesítményadatokkal. Az ügynökvára-várólista-korlát egy beállításkulcs, így szükség esetén módosíthatja azt. Az összegyűjtött adatok tömörítése és a szolgáltatásnak való elküldése megkerülve az Operations Manager felügyeleti csoport adatbázisait, így nem ad hozzá juk terhelést. Az összegyűjtött adatok elküldése után a rendszer eltávolítja azokat a gyorsítótárból.

A fent leírtak szerint a felügyeleti kiszolgálóról vagy a közvetlenül csatlakoztatott ügynökökről származó adatokat a Rendszer A TLS-en keresztül küldi el a Microsoft Azure adatközpontjainak. Szükség esetén az ExpressRoute segítségével további biztonságot nyújthat az adatoknak. ExpressRoute egy módja annak, hogy közvetlenül csatlakozik az Azure-hoz a meglévő WAN-hálózat, például egy többprotokollos címkeváltás (MPLS) VPN, egy hálózati szolgáltató által biztosított. További információ: [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. A Log Analytics szolgáltatás fogadja és feldolgozza az adatokat
A Log Analytics szolgáltatás biztosítja, hogy a bejövő adatok megbízható forrásból származnak a tanúsítványok és az adatok integritásának ellenőrzése az Azure-hitelesítéssel. A feldolgozatlan nyers adatokat ezután tárolja egy Azure Event Hub a régióban az adatok végül tárolja az in-t. A tárolt adatok típusa az importált és az adatgyűjtéshez használt megoldások típusától függ. Ezután a Log Analytics szolgáltatás feldolgozza a nyers adatokat, és betöltődik az adatbázisba.

Az adatbázisban tárolt összegyűjtött adatok megőrzési ideje a kiválasztott díjcsomagtól függ. Az *ingyenes* szint hez az összegyűjtött adatok hét napig állnak rendelkezésre. A *Fizetett* szint esetében az összegyűjtött adatok alapértelmezés szerint 31 napig állnak rendelkezésre, de 730 napra meghosszabbíthatók. Az adatok tárolása in-t tárol az Azure storage-ban, az adatok titkosságának biztosítása érdekében, és az adatok replikálása a helyi redundáns tárolás (LRS) használatával történik. Az elmúlt két hétben az adatok is ssd-alapú gyorsítótárban tárolódnak, és ez a gyorsítótár titkosítva van.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Az adatok eléréséhez használja a Log Analytics szolgáltatást
A Log Analytics-munkaterület eléréséhez jelentkezzen be az Azure Portalon a korábban beállított szervezeti fiók kal vagy Microsoft-fiókkal. A portál és a Log Analytics szolgáltatás közötti összes forgalmat egy biztonságos HTTPS-csatornán keresztül küldi el a rendszer. A portál használatakor a rendszer munkamenet-azonosítót hoz létre a felhasználói ügyfélen (webböngészőben), és az adatokat a helyi gyorsítótárban tárolja, amíg a munkamenet le nem áll. Ha megszakad, a gyorsítótár törlődik. Az ügyféloldali cookie-k, amelyek nem tartalmaznak személyazonosításra alkalmas adatokat, nem törlődnek automatikusan. A munkamenet-cookie-k HTTPOnly jelöléssel vannak ellátva, és biztonságosak. Egy előre meghatározott tétlen időszak után az Azure Portal munkamenet eközben megszakad.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan gyűjthet adatokat az Azure-beli virtuális gépek loganalytics-szolgáltatásával az [Azure virtuális gépek rövid útmutatóját követve.](../../azure-monitor/learn/quick-collect-azurevm.md)  

*  Ha a környezetében lévő fizikai vagy virtuális Windows vagy Linux rendszerű számítógépekről szeretne adatokat gyűjteni, olvassa el a [Gyorsútmutató Linux rendszerű számítógépekhez](../../azure-monitor/learn/quick-collect-linux-computer.md) vagy [a Windows rendszerű számítógépekhez készült gyorsindítást.](../../azure-monitor/learn/quick-collect-windows-computer.md)

