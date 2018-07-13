---
title: Log Analytics által nyújtott Adatbiztonság |} A Microsoft Docs
description: További tudnivalók arról, hogy a Log Analytics hogyan védi az adatait és védi az adatokat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: b7fd880683eed9e742007d6e595e1f275467b664
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990115"
---
# <a name="log-analytics-data-security"></a>Log Analytics és adatbiztonság
Az információ kiegészítésére az Azure Log Analytics információkat biztosít a dokumentum célközönsége [Azure adatvédelmi központ](../security/security-microsoft-trust-center.md).  

Ez a cikk ismerteti az adatok gyűjtése, feldolgozása és a Log Analytics által védett. Ügynökök használatával a webszolgáltatáshoz csatlakozni, használja a System Center Operations Manager operatív adatok gyűjtéséhez vagy adatokat lekérni az Azure diagnostics Log Analytics általi használatra. 

A Log Analytics szolgáltatás a felhőbeli adatok biztonságosan kezeli a következő módszerekkel:

* az adatok elkülönítése
* Adatmegőrzés
* Fizikai biztonság
* incidenskezelés
* Megfelelőség
* biztonsági szabványok tanúsítványok

Lépjen kapcsolatba velünk, olyan kérdéseket, a javaslatokat vagy a problémákkal kapcsolatban az alábbi információkat, beleértve a biztonsági házirendeket [Azure-támogatási lehetőségek](http://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>A TLS 1.2-es biztonságos az adatok küldése 

A Log Analytics az átvitt adatok biztonságának biztosítása érdekében, erősen javasoljuk, hogy legalább az ügynök konfigurálása Transport Layer Security (TLS) 1.2-es. Sebezhetők régebbi verziói a TLS/Secure Sockets Layer (SSL) találhatók, és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**, és az iparág gyorsan változó kénytelen volt megszakítani ezt támogatása ezen régebbi protokollok. 

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) be van állítva egy [2018. június 30. a határidő](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) régebbi verziói a TLS/SSL és a frissítés biztonságosabb protokoll letiltásához. Miután Azure támogatása, csökken, ha az ügynökök nem keresztül kommunikálnak, legalább a TLS 1.2-es nem tudná Log Analytics szolgáltatásnak. 

Nem javasoljuk, hogy az ügynök csak a TLS 1.2 használatára, ha feltétlenül szükség szerint ez biztonságosabb tönkretehetik platform szintű biztonsági funkciókat, amelyek lehetővé teszik, hogy automatikusan felismeri és használja ki újabb protokollok elérhetővé váló programkedvezményekről például explicit módon beállítása mint TLS 1.3. 

### <a name="platform-specific-guidance"></a>Platform konkrét útmutatást

|Platformon és nyelven | Támogatás | További információ |
| --- | --- | --- |
|Linux | Linux-disztribúciók általában támaszkodhat [OpenSSL](https://www.openssl.org) a TLS 1.2 támogatása.  | Ellenőrizze a [OpenSSL változásnaplójában](https://www.openssl.org/news/changelog.html) annak ellenőrzéséhez, hogy az OpenSSL-verziót támogatja.|
| Windows 8.0-s és 10 | Támogatott, és alapértelmezés szerint engedélyezve van. | Ellenőrizze, hogy továbbra is használja a [alapértelmezett beállítások](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings).  |
| A Windows Server 2012-2016-ban | Támogatott, és alapértelmezés szerint engedélyezve van. | Ellenőrizze, hogy továbbra is használja a [alapértelmezett beállításai](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és a Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) lap engedélyezése részleteiért.  |
| Windows Server 2008 SP2 | A TLS 1.2 támogatásához szükséges frissítést. | Lásd: [frissítést a TLS 1.2 támogatása](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) a Windows Server 2008 SP2. |

## <a name="data-segregation"></a>az adatok elkülönítése
Miután az adatokat a Log Analytics szolgáltatás által betöltött, az adatok van logikailag elkülönítve vannak tárolva a szolgáltatás egyes összetevőiben. Az összes adat munkaterület szerint van megcímkézve. Ez a címkézés megmarad az adatok teljes életciklusa alatt, és a szolgáltatás minden rétegében érvényes. Az adatok a kiválasztott régióban a storage fürtben egy dedikált adatbázisban tárolódik.

## <a name="data-retention"></a>Adatmegőrzés
Log search indexelt adatokat tárolja és őrzi meg a díjszabási tervnek megfelelően. További információkért lásd: [Log Analytics Díjszabásáról](https://azure.microsoft.com/pricing/details/log-analytics/).

Részeként a [előfizetői szerződés](https://azure.microsoft.com/support/legal/subscription-agreement/), Microsoft megőrzi az adatokat, a szerződés feltételeinek megfelelően.  Az adatok törlése után is töröljük az Azure Storage-fiókot, ahol az adatok találhatók.  Vásárlói adatokat a rendszer eltávolítja, ha nincsenek fizikai meghajtók megsemmisül.  

Az alábbi táblázat soroljuk fel az elérhető megoldások, és példákat tartalmaz a gyűjtött adatok típusától.

| **Megoldás** | **Adattípusok** |
| --- | --- |
| Kapacitás és teljesítmény |Teljesítményadatok és a metaadatok |
| Frissítéskezelés |Metaadatait és adatok |
| Naplókezelés |Felhasználó által definiált az eseménynaplókat, Windows-eseménynaplók, illetve IIS-naplók |
| Változások követése |A szoftverleltár, a Windows-szolgáltatás és a Linux-démon metaadatok és a Windows/Linux-fájl metaadatait |
| Az SQL és az Active Directory Assessment |WMI-adatok, a beállításjegyzék-adatok, a Teljesítményadatok és az SQL Server dinamikus felügyeleti eredmények megtekintése |

Az alábbi táblázat az adattípusok példái láthatók:

| **Adattípus** | **Mezők** |
| --- | --- |
| Riasztás |Riasztás neve, riasztás leírása, BaseManagedEntityId, probléma azonosítója, IsMonitorAlert, RuleId, ResolutionState, prioritás, súlyosság, kategória, tulajdonosa, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Konfiguráció |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Esemény |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Megjegyzés:** egyéni mezőt tartalmazó eseményeket a Windows eseménynaplóba ír, amikor a Log Analytics gyűjti azokat. |
| Metaadatok |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Teljesítmény |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Állapot |StateChangeEventId, StateId, NewHealthState, OldHealthState, környezet, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, monitorid attribútumként, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fizikai biztonság
A Log Analytics szolgáltatás kezeli a Microsoft ezért felelős munkatársai, és az összes tevékenység naplózása, és ellenőrizhető. A log Analytics egy Azure-szolgáltatásként működik, és megfelel-e az összes Azure-megfelelőségi és biztonsági követelményeknek. A 18 lapján tekintheti meg a fizikai biztonság az Azure-objektumok részleteit a [a Microsoft Azure biztonsági szolgáltatásainak áttekintése](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fizikai hozzáférési jogosultsága ahhoz, hogy biztonságos területek bárki, aki már nem rendelkezik a Log Analytics szolgáltatással, beleértve az átvitel és a megszűnés felelősséget módosítja egy munkanapon belül. Itt olvashat a globális fizikai infrastruktúra használjuk, [Microsoft Datacenters](https://azure.microsoft.com/en-us/global-infrastructure/).

## <a name="incident-management"></a>incidenskezelés
A log Analytics egy incidenskezelési folyamatának, amelyek az összes Microsoft-szolgáltatásokkal rendelkezik. Összefoglalva, hogy:

* A közös felelősség modell, ahol biztonsági feladata egy adott napszakban tartozik, és a egy részét az ügyfél tartozik
* Az Azure biztonsági incidensek kezelése:
  * Egy incidens észlelésekor vizsgálat indítása
  * Felmérheti a hatás és a egy incidens egy a készenléti incidensmegoldási csapat egyik tagja által súlyosságának. Bizonyítékok alapján, az értékelés előfordulhat, hogy, vagy előfordulhat, hogy a biztonsági csoportnak további fordulnia eredményez.
  * Az incidens biztonsági válasz szakértőktől a technikai vagy Törvényszéki vizsgálat végez, tartalmazottsági, kockázatcsökkentési és megoldási stratégiák meghatározása diagnosztizálásához. Ha a biztonsági csapat úgy véli, hogy ügyféladatokat előfordulhat, hogy rendelkezik válnak elérhetővé tett legyen egy jogosulatlan vagy illetéktelen, az ügyfél incidens értesítési folyamat párhuzamos végrehajtás megkezdi a párhuzamosan.  
  * Remegő, és az incidens helyreállítása. Az incidensmegoldási csapat létrehoz egy helyreállítási tervbe, a probléma megoldásához. Válság tartalmazottsági lépést karanténba érintett rendszerek például akkor fordulhat elő, azonnal, és a diagnosztikát párhuzamosan. Hosszabb távon megoldások előfordulhat, hogy lehet tervezett, amely az azonnali kockázati letelte után kerül sor.  
  * Az incidens lezárása és a egy menthet végez. Az incidensmegoldási csapat létrehoz egy menthet, amelyik felvázolja az incidenst, az a szándéka, hogy módosítsa a házirendek, eljárások és folyamatok, hogy az esemény egy ismétlődési részleteit.
* Biztonsági incidensek ügyfelek értesítése:
  * Az érintett ügyfelek, és adja meg, bárki, aki egy értesítést a lehető legrészletesebb kihatással van a hatókör határozza meg
  * Hozzon létre egy értesítés rendelkező ügyfelek részletes elegendő információt, hogy a vizsgálat végrehajtása a végén, és bármely, a végfelhasználók számára nem jogosulatlanul késlelteti az értesítési folyamat során végzett követelmények biztosításához.
  * Erősítse meg, és az incidens, szükség szerint deklarálható.
  * Értesítse a felhasználókat arról, egy incidens értesítéssel feldühítheti késedelem nélkül, és minden olyan jogi vagy szerződéses kötelezettségvállalás megfelelően. Értesítések a biztonsági események egy vagy több olyan ügyfél rendszergazdái Microsoft választja, beleértve az e-mailen keresztül, bármilyen módon érkeznek.
* Csapat készültségi és képzési folytatása:
  * A Microsoft munkatársainak biztonsági és tájékoztatás képzési, segít azonosítani és biztonsági problémák jelentése végrehajtásához szükségesek.  
  * A Microsoft Azure-szolgáltatás használata kezelőnél van továbbá képzési kötelezettségek körülvevő a vásárlói adatokat tároló-és nagybetűket rendszerekhez való hozzáférést.
  * Microsoft security response munkatársai speciális képzésben a szerepkörükhöz

Ha az ügyféladatok elvesztését következik be, minden egyes ügyfél egy napon belül értesítjük. Azonban ügyfél soha nem történt adatvesztés a szolgáltatással. 

A Microsoft a biztonsági incidensekre reakciója kapcsolatos további információkért lásd: [Microsoft Azure Security Response a felhőben](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Megfelelőség
A Log Analytics szoftverek fejlesztési és a szolgáltatás csapata információk biztonsági és cégirányítási program támogatja az üzleti követelmények találhatók, és törvényeknek és szabályozásoknak ismertetett módon [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) és [ A Microsoft adatvédelmi központ – megfelelőség](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hogyan Log Analytics biztonsági követelményeket, azonosítja a biztonsági vezérlőket, felügyeli és figyeli a kockázatok is ott ismerteti. Évente, hogy tekintse át házirendek, szabványok, eljárásokra és irányelveket.

Minden fejlesztési csapat egyik tagja megkapja a hivatalos alkalmazás biztonsági képzés. A verziókezelő rendszer belsőleg, a szoftverfejlesztés használjuk. Minden szoftver projekt a verziókövetési rendszerét védi.

A Microsoft rendelkezik egy biztonsági és megfelelőségi csapat felügyeli, és minden szolgáltatás a Microsoft értékeli. A csapat alkotó Information security officer, és azokat, amelyek nem tartoznak a mérnöki részlegek számára, akik a Log Analytics. A biztonsági tisztviselők saját felügyeleti lánc és a termékek és szolgáltatások biztonsági és megfelelőségi független értékelések végez.

A Microsoft igazgatótanácsi egy éves jelentéssel kapcsolatos összes információ biztonsági programok a Microsoft értesítést kap.

A Log Analytics szoftverek fejlesztési és a szolgáltatás csapata aktívan dolgozik a Microsoft Legal és megfelelőségi csapatok és más iparági partnerekkel, különböző tanúsítványok beszerzéséhez.

## <a name="certifications-and-attestations"></a>Minősítéseket és tanúsítványokat
Az Azure Log Analytics megfelel a következő követelményeknek:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Fizetési kártya iparág (PCI szabványoknak) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) a PCI biztonsági szabványoknak Tanács.
* [Service Organization Controls (SOC) 1 1 és SOC 2 Type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) megfelelő
* [A HIPAA és HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) a vállalatok számára, hogy a HIPAA-társítása szerződés
* Windows közös mérnöki feltételek
* Microsoft Trustworthy Computing
* Azure-szolgáltatásként az összetevőket, amelyek a Log Analytics használja az Azure megfelelőségi követelmények felelnie. Tudjon meg többet a [Microsoft Megbízhatósági központ – megfelelőség](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> Az egyes tanúsítványok és igazolások, a Log Analytics, a korábbi néven szerepel *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>A felhő-számítástechnika biztonsági adatfolyama
A céges az alábbi ábra bemutatja az adatok áramlása látható, a felhőbeli biztonsági architektúra, és hogyan, biztonságos, mivel a Log Analytics szolgáltatást, Ön általi végső soron az Azure Portalon látható helyezi át. További információ az egyes lépések a diagram követi.

![A Log Analytics-adatok gyűjtése és biztonsági képe](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. A Log Analytics és adatokat gyűjthet regisztráljon
A szervezet a Log Analytics szolgáltatásnak egy Azure-beli virtuális gépeken, vagy a környezet vagy egyéb felhőszolgáltató virtuális vagy fizikai számítógépeken futó Windows vagy Linux ügynököt kell konfigurálni.  Ha az Operations Manager használja, a felügyeleti csoportból konfigurálja az Operations Manager-ügynök. Felhasználók (amely az Ön, más felhasználókkal vagy csoport, előfordulhat) egy vagy több Log Analytics-munkaterületek létrehozása és regisztrálása az ügynökök a következő fiókok egyikét használva:

* [Szervezeti azonosító](../active-directory/fundamentals/sign-up-organization.md)
* [A Microsoft-fiók – az Outlook, Office Live, az MSN](https://account.microsoft.com/account)

A Log Analytics-munkaterületen, az adatokat, gyűjtött, összesítése, elemzése, és jelenik meg. A munkaterület elsősorban egy azt jelenti, hogy az adatok particionálása, és az egyes munkaterületeken egyedi. Például érdemes az üzemi adatok egy munkaterülethez felügyelt és a egy másik munkaterülettel felügyelt adatok rendelkezik. Munkaterületek egy rendszergazda felhasználó elérés is segítenek az adatok. Az egyes munkaterületeken lehet több felhasználói fiók tartozik, és minden felhasználói fiók több Log Analytics-munkaterületek hozzáférhet. Munkaterületek adatközpont-régió alapján hoz létre. Más adatközpontokban a régióban, elsősorban a Log Analytics szolgáltatás rendelkezésre állása az egyes munkaterületeken replikálása.

Az Operations Manager felügyeleti csoport Operations Manager, hozza létre a kapcsolatot a Log Analytics szolgáltatással. Ezután konfigurálnia melyik ügynök által felügyelt rendszerekhez a felügyeleti csoport és engedélyezett a gyűjthet adatokat küldeni a szolgáltatás. Engedélyezte a megoldástól függően vannak ezek a megoldások adatait vagy a Log Analytics szolgáltatással vagy az ügynök által felügyelt rendszer által gyűjtött adatok mennyisége miatt küldő közvetlenül egy Operations Manager felügyeleti kiszolgálóról közvetlenül a érkeznek a szolgáltatás az ügynök. Nem figyelt meg az Operations Manager rendszerhez csatlakozik biztonságosan a Log Analytics szolgáltatással közvetlenül.

Csatlakoztatott és a Log Analytics szolgáltatás közötti minden kommunikáció titkosított. A TLS (HTTPS) protokollt használják a titkosítást.  A Microsoft SDL-folyamat után következik, hogy a Log Analytics e naprakészen tartása a legújabb fejlesztések a titkosítási protokollok.

Minden ügynök típusú Log Analytics gyűjti az adatokat. A gyűjtött adatok típus által használt megoldások függ. Láthatja, hogy az adatgyűjtést, összegzését [adja hozzá a Log Analytics solutions kövesse a megoldástárban](log-analytics-add-solutions.md). Ezenkívül részletesebb gyűjteményadatokat a legtöbb megoldás érhető el. A megoldás egy előre meghatározott nézeteket, a naplóbeli keresési lekérdezések, az adatok gyűjtési szabályok és a feldolgozási logika kötegelt. Csak rendszergazdák használhatják a Log Analytics megoldás importálása. A megoldás az importálása után Áthelyezés az Operations Manager felügyeleti kiszolgálókon (ha van), majd a kiválasztott összes ügynököt. Ezt követően az ügynök összegyűjti az adatokat.

## <a name="2-send-data-from-agents"></a>2. Adatok küldése az ügynökök
Minden ügynök esetében egy regisztrációs kulccsal regisztrálja, és a egy biztonságos kapcsolatot hoznak létre az ügynök és a Log Analytics szolgáltatás tanúsítványalapú hitelesítést és az SSL használata a 443-as porton. A log Analytics titkoskód-tárolót létrehozni, és tarthatja karban a kulcsokat használ. Titkos kulcsok vannak elforgatott a 90 naponta, és az Azure-ban vannak tárolva, és kezeli az Azure operations akik szigorú szabályozási és megfelelőségi gyakorlat szerint.

Az Operations Managerrel a felügyeleti csoport regisztrálva a Log Analytics-munkaterületet hoz létre egy biztonságos HTTPS-kapcsolat az Operations Manager felügyeleti kiszolgálóval.

Az Azure virtual machines szolgáltatásban futó Windows vagy Linux ügynökök csak olvasható tárkulcs segítségével olvassa el az Azure-táblákban diagnostické události.  

Bármely, az Operations Manager felügyeleti csoport, amely integrálva van a Log Analytics felé jelentő ügynök, az nem lehet kommunikálni a felügyeleti kiszolgáló esetén a szolgáltatás bármilyen okból az összegyűjtött adatok helyben tárolódnak egy átmeneti gyorsítótárban a felügyeleti a kiszolgáló.   Próbálja meg újra elküldeni az adatokat két órán át nyolc percenként.  Megkerüli a felügyeleti kiszolgáló, és közvetlenül a Log Analytics érkezik adatok esetén a viselkedés a Windows-ügynök összhangban.  

Felügyeleti kiszolgáló ügynök gyorsítótárazott adatok és a Windows hitelesítőadat-tár az operációs rendszer védi. Ha a szolgáltatás nem tudja feldolgozni az adatokat két órával később, az ügynökök várólistára kerülnek az adatok. Ha a várólista megtelik, az ügynök elindul, adattípusok, kezdve a teljesítményadatok elvetését. Az ügynök várólista-határérték egy beállításkulcs megadásával módosíthatja azokat, így szükség esetén. Összegyűjtött adatok tömörítve és a szolgáltatásnak az Operations Manager felügyeleti csoport adatbázisok kihagyásával, így azt nem adható hozzá bármilyen terhelést őket. Miután a rendszer elküldi a gyűjtött adatokat, a rendszer eltávolítja a gyorsítótárból.

A fentiekben ismertetettek szerint a felügyeleti kiszolgálóról vagy közvetlenül csatlakoztatott ügynökök adatküldést SSL-kapcsolaton keresztül a Microsoft Azure-adatközpontok. Az ExpressRoute segítségével szükség esetén adja meg a további biztonsági adatok. Az ExpressRoute egy módja közvetlenül kapcsolódhat az Azure meglévő WAN hálózatról, például a többprotokollos címke váltása (mpls virtuális Magánhálózat) VPN, egy hálózati szolgáltató által biztosított. További információkért lásd: [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. A Log Analytics szolgáltatás fogadja és dolgozza fel az adatokat
A Log Analytics szolgáltatás biztosítja, hogy a bejövő adatok megbízható forrásból érvényesítésével megjeleníthető tanúsítványok és az adatok integritásának megőrzése Azure-hitelesítéssel. A feldolgozatlan nyers adatok majd tárolódik a régióban, az adatok inaktív végül lesznek tárolva az Azure-Eseményközpontba. A tárolt adatok típusát a megoldásokat, amelyek lettek importálva, és adatokat gyűjthet típusú függ. Ezután a Log Analytics szolgáltatás folyamatok a nyers adatokat, és feltölti azt az adatbázisba.

Az összegyűjtött adatokat az adatbázisban tárolt a megőrzési időszak a kiválasztott díjszabással függ. Az a *ingyenes* szint, a 7 napig érhető összegyűjtött adatokat. Az a *fizetős* szinten gyűjtött adatok alapértelmezés szerint 31 napig érhető el, de 720 nap is kiterjeszthető. Adatok tárolása az Azure-tárolót, annak biztosítása érdekében az adatok titkosítását inaktív. Az adatok az elmúlt két hétben is SSD-alapú gyorsítótárában vannak tárolva, és a gyorsítótár jelenleg nincs titkosítva.  Titkosítás támogatása 2018 újabb felében tervezzük.  

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Az adatok eléréséhez a Log Analytics használatával
A Log Analytics-munkaterület elérését, jelentkezzen be az Azure Portalon a szervezeti fiók vagy a korábban használt Microsoft-fiók használatával. A portál és a Log Analytics szolgáltatás közötti összes forgalom egy biztonságos csatornán keresztül zajlik. A portál használata esetén a munkamenet-azonosító akkor jön létre, a felhasználó ügyfélen (webböngésző), és a helyi gyorsítótárban tárolt adatokat, mindaddig, amíg a munkamenet meg lett szakítva. Ha leállt, a gyorsítótár törlődik. Ügyféloldali cookie-kat, amelyek nem tartalmaznak személyes azonosításra alkalmas adatokat, nem lesznek automatikusan eltávolítva. Munkamenet-cookie HTTPOnly vannak megjelölve, és biztosított. Egy előre meghatározott tétlen időszak után az Azure portal munkamenet meg lett szakítva.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan gyűjthet adatokat a Log Analytics az Azure virtuális gépek következő a [Azure virtuális gépek rövid útmutatóját](log-analytics-quick-collect-azurevm.md).  

*  Ha a környezete fizikai vagy virtuális Windows vagy Linux rendszerű számítógépek adatainak összegyűjtése, tekintse meg a [gyors útmutató: Linux rendszerű számítógépek](log-analytics-quick-collect-linux-computer.md) vagy [a rövid útmutató a Windows-számítógépek](log-analytics-quick-collect-windows-computer.md)

