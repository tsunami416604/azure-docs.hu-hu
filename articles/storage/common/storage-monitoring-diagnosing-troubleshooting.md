---
title: Figyelése, diagnosztizálása és hibaelhárítása az Azure Storage |} A Microsoft Docs
description: Funkciók használata, például a storage analytics, az ügyféloldali naplózás és más külső eszközök azonosításához, diagnosztizálása és hibaelhárítása az Azure Storage szolgáltatással kapcsolatos problémák.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.component: common
ms.openlocfilehash: e560eb9e0bbce09c541bfc66ea760ea3e636f841
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528714"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Áttekintés
Diagnosztizálás és a egy felhőkörnyezetben található elosztott alkalmazásban kapcsolatos hibák elhárítása lehet bonyolultabb, mint a hagyományos környezetekben. PaaS vagy IaaS infrastruktúra, a helyszínen, egy mobileszközön, vagy valamilyen kombinációját ezekben a környezetekben is telepíthető alkalmazások. Általában az alkalmazás hálózati forgalom útválasztópárokon és nyilvános hálózat és az alkalmazás használhat több tárolási technológiát, például a Microsoft Azure Storage-táblák, Blobok, üzenetsorok vagy fájlok kívül más adatokat tárolja, például a relációs és dokumentum-adatbázis.

Az ilyen alkalmazások sikeresen felügyelete kell proaktívan figyelheti azokat, és megtudhatja, hogyan diagnosztika és hibaelhárítás őket, és azok függő technológiák minden aspektusát. Az Azure Storage-szolgáltatások felhasználói kell folyamatosan figyelni a tárolási szolgáltatások (például a lassabb, mint a szokásos válaszidő) viselkedését a váratlan módosítások az alkalmazás használ, és naplózás használata a részletesebb adatainak gyűjtéséről és a probléma elemzéséhez mélységét. A diagnosztikai információkat, figyelés és naplózás is kaphat segítséget az alkalmazás észlelt a probléma okának megállapításához. Ezután a probléma elhárításában, és határozza meg a megfelelő lépéseket megoldásáról. Az Azure Storage olyan alapvető Azure-szolgáltatás, és, hogy ügyfeleink az Azure-infrastruktúra-megoldások többsége fontos részét képezi. Az Azure Storage egyszerűsítése érdekében a figyelése, diagnosztizálása és a felhőalapú alkalmazások a storage-problémák elhárítása képességeket tartalmazza.

> [!NOTE]
> Az Azure Files nem támogatja az jelenleg naplózását.
> 

Teljes körű hibaelhárítás az Azure Storage-alkalmazások gyakorlati útmutató, lásd: [– teljes körű hibaelhárítás az Azure Storage-mérőszámok és a naplózás, az AzCopy és a Message Analyzer használatával](../storage-e2e-troubleshooting.md).

* [Bevezetés]
  * [Hogyan vannak rendszerezve Ez az útmutató]
* [A storage szolgáltatás figyelése]
  * [Szolgáltatás állapotának monitorozása]
  * [Monitorozási kapacitás]
  * [Rendelkezésre állás figyelése]
  * [Teljesítmény figyelése]
* [Tárolási problémák diagnosztizálása]
  * [A szolgáltatás állapotbeli problémák]
  * [Teljesítménnyel kapcsolatos problémák]
  * [Hibák diagnosztizálása]
  * [Storage emulator kapcsolatos problémák]
  * [Storage naplózási eszközök]
  * [Hálózati naplózási eszközök használatával]
* [Teljes körű nyomkövetés]
  * [Naplózási adatok korrelálása]
  * [Ügyfélkérés azonosítója]
  * [Kiszolgálói kérelem azonosítója]
  * [Időbélyegek]
* [Hibaelhárítási útmutató]
  * [A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]
  * [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
  * [A mérőszámok magas AverageServerLatency értéket mutatnak]
  * [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]
  * [A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]
  * [A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]
  * [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]
  * [Az ügyfél fogad üzeneteket HTTP 403 (tiltott)]
  * [Az ügyfél fogad üzeneteket HTTP 404 (nem található)]
  * [Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad]
  * [Metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére a tranzakció állapota]
  * [Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat]
  * [A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával]
  * [.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.]
  * [Van egy másik probléma storage szolgáltatással]
  * [A Windows virtuális gépek VHD-k hibaelhárítása](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [A Linux rendszerű virtuális gépek VHD-k hibaelhárítása](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [A Windows Azure Files-problémák elhárítása](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Linux rendszerű Azure Files-problémák elhárítása](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Mellékletek]
  * [1. függelék: A HTTP és HTTPS-forgalom rögzítésére Fiddler segítségével]
  * [2. függelékben: Wireshark használó rögzítheti a hálózati forgalom]
  * [3. függelék: Microsoft Message Analyzert segítségével rögzítheti a hálózati forgalom]
  * [4. függelék: Segítségével Excel metrikák megtekintése és az adatok naplózása]
  * [5. függelék: A Visual Studio Team Services az Application insights szolgáltatással figyelését.]

## <a name="introduction"></a>Bevezetés
Ez az útmutató bemutatja, hogyan használható az Azure Storage Analytics, funkciókat jelentkezik be az Azure Storage ügyféloldali kódtárat, és egyéb harmadik felektől származó eszközök azonosítása, diagnosztizálása és hibaelhárítása az Azure Storage ügyféloldali kapcsolatos problémákat.

![][1]

Ez az útmutató elsősorban az online szolgáltatások, Azure Storage szolgáltatásainak és informatikai szakemberek az ilyen online szolgáltatások kezeléséért használó fejlesztők által olvasható célja. Ez az útmutató a céljai a következők:

* Segítséget állapotát és teljesítményét az Azure Storage-fiókok kezelése.
* Biztosítja a szükséges folyamatokat és eszközöket segít eldönteni, hogy e probléma vagy hiba az alkalmazások az Azure Storage vonatkozik.
* Az Azure Storage kapcsolatos problémák megoldásához hasznos útmutatást nyújtanak.

### <a name="how-this-guide-is-organized"></a>Hogyan vannak rendszerezve Ez az útmutató
A szakasz "[A storage szolgáltatás figyelése]" állapotát és teljesítményét az Azure Storage Analytics Metrics (Storage Metrics) használatával Azure Storage-szolgáltatások figyelését ismerteti.

A szakasz "[Tárolási problémák diagnosztizálása]" ismerteti, hogyan diagnosztizálhatja a problémákat, az Azure Storage Analytics-naplózás használata (Storage Logging). Emellett bemutatja, hogyan lehet egy, az ügyfél-kódtárak a létesítmények használatával például a Storage ügyféloldali kódtára a .NET vagy a Javához készült Azure SDK ügyféloldali naplózás engedélyezése.

A szakasz "[teljes körű nyomkövetés]" ismerteti, hogyan kapcsolhatja össze a különböző naplófájlokat, és a mérőszámadatok eredményobjektumokban tárolt információt.

A szakasz "[hibaelhárítási útmutató]" nyújt hibaelhárítási útmutatót a néhány gyakoribb storage szolgáltatással kapcsolatos kapcsolatban, hogy léphetnek fel.

A "[mellékletek]" információval, például a Wireshark vagy Netmon más eszközök használatával, elemzése hálózati csomagadatok, Fiddler a HTTP/HTTPS-üzenetek, elemzésére és a Microsoft Message Analyzer használatával történik az adatok naplózása.

## <a name="monitoring-your-storage-service"></a>A storage szolgáltatás figyelése
Ha ismeri, a Windows alkalmazásteljesítmény-figyelés, is felfoghatók Storage Metrics, hogy a Windows Teljesítményfigyelő-számlálókból Azure Storage egyenértékű. Storage-mérőszámok egy átfogó szolgáltatás rendelkezésre állásával, szolgáltatáshoz érkező kérések összesített számát, vagy a szolgáltatáshoz tartozó sikeres kérelmek aránya (a Windows Performance Monitor-terminológiában számlálók) metrikákra található. A rendelkezésre álló metrikák teljes listáját lásd: [Storage Analytics mérőszámainak Táblasémáját](http://msdn.microsoft.com/library/azure/hh343264.aspx). Megadhatja, hogy kívánja-e a storage szolgáltatás begyűjtése és összesítése metrikák óránként vagy percenként. Engedélyezze a mérőszámok és figyelheti a tárfiókok kapcsolatos további információkért lásd: [storage mérőszámainak engedélyezése és a mérőszámadatok](http://go.microsoft.com/fwlink/?LinkId=510865).

Kiválaszthatja, hogy melyik meg szeretne jeleníteni a óránkénti mérőszámot az [az Azure portal](https://portal.azure.com) és értesíthetők a rendszergazdák e-mailben, amikor egy óránkénti mérőszám meghalad egy bizonyos küszöbértéket szabályok konfigurálása. További információkért lásd: [riasztási értesítések fogadása](/azure/monitoring-and-diagnostics/monitoring-overview-alerts). 

A storage szolgáltatás használata ajánlott beavatkozást metrikákat gyűjtő, de előfordulhat, hogy nem minden tárolási műveletet rögzíti.

Az Azure Portalon megtekintheti a metrikákat például rendelkezésre állási, a kérelmek teljes száma és a egy tárfiókhoz tartozó átlagos késése számok. Egy értesítési szabályt a riasztást a rendszergazda, ha rendelkezésre állási egy bizonyos szint alá csökken is be lett állítva. Ezek az adatok megtekintésekor, vizsgálat egy lehetséges terület a table service sikerszázalék nem érik el a 100 %-os (további információkért lásd: a szakasz "[Metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére a tranzakció állapota]").

Az Azure-alkalmazásokat, hogy biztosítsa azok kifogástalan állapotú és elvárt teljesítő, folyamatosan figyelje:

* Alkalmazás, amely lehetővé teszi, hogy az egyes alapmértékekkel létrehozó hasonlítsa össze az aktuális adatok, és azokat a jelentős változásokat az Azure storage és az alkalmazás viselkedését. Az alapkonfiguráció mérőszámok értékeit, sok esetben lesz, és létre kell hoznia őket a teljesítménye az alkalmazás tesztelése esetén.
* Perc típusú metrikák rögzítése, és használja őket a váratlan hibák és a rendellenességek például hiba kiugrások aktívan figyelése számolja, vagy kérje meg díjszabás szerint.
* Óránkénti mérőszámot rögzítése, és használja őket az átlagos értékek például figyelésére átlagos hiba száma és kérelemarányok.
* Diagnosztikai eszközök használatával, a szakaszban később leírt módon lehetséges problémák kivizsgálása "[Tárolási problémák diagnosztizálása]."

Az alábbi ábrán a diagramok bemutatják, hogyan átlagolás, amely akkor fordul elő, óránkénti metrikákhoz elrejtheti adatforgalmi csúcsokhoz tevékenységben. Az óránkénti mérőszámot úgy tűnik, hogy egy állandó kérések másodpercenkénti száma, megjelenítése közben a metrikák megjelenítéséhez a ingadozások által megkövetelt, hogy a rendszer valóban zajló perc.

![][3]

Ez a szakasz további része ismerteti, hogy milyen metrikákat, célszerű figyelemmel kísérni, és miért.

### <a name="monitoring-service-health"></a>Szolgáltatás állapotának monitorozása
Használhatja a [az Azure portal](https://portal.azure.com) minden régióban az Azure a világ különböző pontjain található a Storage szolgáltatás (és más Azure-szolgáltatások) állapotának megtekintéséhez. Figyelés lehetővé teszi, hogy azonnal láthatja, ha a probléma a hatáskörén kívül van hatással az alkalmazás használhatja a régióban a Storage szolgáltatás.

A [az Azure portal](https://portal.azure.com) is lehetővé teszi a különböző Azure-szolgáltatások érintő incidensek értesítéseket.
Megjegyzés: Ez az információ korábban elérhető volt, valamint az előzményadatok, az a [Azure-szolgáltatások irányítópultját](http://status.azure.com).

Bár a [az Azure portal](https://portal.azure.com) egészségügyi adatokat gyűjt a (belső kibővített figyelés), az Azure adatközpontokon belül is érdemes lehet létrehozni rendszeresen hozzáférő szintetikus tranzakciók összetevős megközelítés bevezetése az Azure-ban üzemeltetett webes alkalmazás több helyről. A szolgáltatások [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) és Application Insights a Visual Studio Team Services, erre a megközelítésre példa. Visual Studio Team Serviceshez készült Application Insights kapcsolatos további információkért lásd: a függelék: "[5. függelék: Monitorování pomocí Application Insights a Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Monitorozási kapacitás
Storage-mérőszámok csak tárolja a teljesítmény-mérőszámait a blob service, mert blobok általában fiók tárolt adatok legnagyobb részét (írása idején ez még nem használható a Storage Metrics figyelése kapacitását, a táblák és üzenetsorok). Ezek az adatok a annak a **$MetricsCapacityBlob** táblából, ha engedélyezte a Blob szolgáltatás figyelése. Storage-mérőszámok rögzíti az adatok naponta egyszer, és értékét használhatja a **rowkey tulajdonságok esetén** meghatározni, hogy a sor tartalmaz-e olyan entitás, amely kapcsolódik a felhasználói adatok (érték **adatok**) vagy elemzési adatok (érték **analytics**). Minden tárolt entitás tartalmaz információt a felhasznált tárterület mérete (**kapacitás** bájtban mért) és a tárolók száma (**ContainerCount**) és blobokon (**ObjectCount** ) használja a storage-fiókban. További információ a kapacitási mérőszámot tárolja a **$MetricsCapacityBlob** táblában, tekintse meg [Storage Analytics mérőszámainak Táblasémáját](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Célszerű figyelemmel kísérni ezeket az értékeket egy korai figyelmeztető, hogy Ön hamarosan eléri a tárfiók kapacitásának korlátjáig. Az Azure Portalon a riasztási szabályok arra az esetre, ha az összesített tárhely kihasználtsága meghaladja a, vagy Ön által megadott küszöbértékek alá csökken is hozzáadhat.
> 
> 

Méretének meghatározásáról a különböző tárolási objektumokat, mint a blobok, lásd a következő blogbejegyzésben: [ismertetése az Azure Storage Billing – sávszélesség, tranzakciók és a kapacitás](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Rendelkezésre állás figyelése
Célszerű figyelemmel kísérni a tárolási szolgáltatások rendelkezésre állását a storage-fiókban lévő-figyelési szolgáltatás által a **rendelkezésre állási** oszlop az óránkénti vagy percenkénti metrikákat táblázatokban – **$MetricsHourPrimaryTransactionsBlob** , **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob** , **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. A **rendelkezésre állási** oszlop tartalmaz egy százalékos értéket, amely azt jelzi, hogy a szolgáltatás vagy a sor képviseli az API-művelet rendelkezésre állását (a **RowKey** jeleníti meg, ha a sor metrikáit tartalmazza, a szolgáltatás egészére vagy egy adott API-művelet).

Bármilyen érték kisebb, mint 100 %-os azt jelzi, hogy bizonyos tárolási kérelmek sikertelenek. Láthatja, hogy miért sikertelen, az a többi oszlopot a azt mutatják be, például a különböző típusú kérések számát mérőszámadatokat megvizsgálásával **ServerTimeoutError**. Ön is látni fog **rendelkezésre állási** ideiglenesen fall közben a szolgáltatás átmeneti kiszolgáló időtúllépése például 100 % alatti helyezi át a partíciók jobb terheléselosztás kérelem; az újrapróbálkozási logika az ügyfélalkalmazásban található kell olyan időszakos feltételek kezelésére. A cikk [Storage Analytics naplózott műveletek és az állapotüzenetek](http://msdn.microsoft.com/library/azure/hh343260.aspx) Storage Metrics tartalmaz a tranzakció-típusok listája a **rendelkezésre állási** számítási.

Az a [az Azure portal](https://portal.azure.com), adhat hozzá, amelyek figyelmeztetik, ha a riasztási szabályok **rendelkezésre állási** a szolgáltatás Ön által megadott küszöbérték alá esik.

A "[hibaelhárítási útmutató]" című részében olvashat ismertet néhány gyakori storage szolgáltatás problémát rendelkezésre állását.

### <a name="monitoring-performance"></a>Teljesítmény figyelése
A tárolási szolgáltatások teljesítményének figyelése, használhatja a következő metrikák az óránkénti és percenkénti metrikákat táblából.

* Az értékeket a **AverageE2ELatency** és **averageserverlatency értéket mutatnak** oszlopok megjelenítése az átlagos idő a társzolgáltatás vagy API-művelet típus tart a kérelmek feldolgozásához. **AverageE2ELatency** egy mérték, amely tartalmazza a kérés olvasásának, és a kérelem feldolgozásához szükséges idő mellett a válasz elküldéséhez szükséges idő a végpontok közötti késés (ezért tartalmazza a hálózati késést, ha a kérelem eléri a storage szolgáltatás); **Averageserverlatency értéket mutatnak** csak a feldolgozási idő mértékét, és ezért nem tartalmazza a bármely az ügyfél kommunikál a kapcsolódó hálózati késést. Című témakör "[A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]" az útmutató későbbi részében leírását, hogy miért van jelentős különbség a két ezeket az értékeket.
* Az értékeket a **TotalIngress** és **TotalEgress** oszlopok megjelenítése a teljes adatmennyiség bájtban érkező és a tároló nem működik, vagy egy adott API-művelet típus keresztül történik.
* Az értékeket a **TotalRequests** oszlopban jelennek meg, a storage szolgáltatás API-művelet fogadó kérelmek teljes száma. **TotalRequests** , amely a storage szolgáltatás megkapja a kérelmek teljes száma.

Általában akkor lesz figyelheti ezek bármelyike a váratlan módosítások azt jelzi, hogy a hibát, amely a vizsgálat szükséges.

Az a [az Azure portal](https://portal.azure.com), arra az esetre, ha a teljesítmény-mérőszámok, a szolgáltatás valamelyik alá esnek, vagy meghaladja a küszöbértéket, Ön által megadott értesítési szabályokat adhat hozzá.

A "[hibaelhárítási útmutató]" című részében olvashat a teljesítménnyel kapcsolatos gyakori storage szolgáltatás problémákat ismerteti.

## <a name="diagnosing-storage-issues"></a>Tárolási problémák diagnosztizálása
Számos módon, hogy, előfordulhat, hogy értesülnek a hiba vagy probléma az alkalmazásban, többek között:

* Súlyos hiba, amely az alkalmazás összeomlik, vagy nem működik.
* Az alapértékek a metrikák figyelése az előző szakaszban leírtak szerint a jelentős változtatások "[A storage szolgáltatás figyelése]."
* Az alkalmazást, amely egy adott művelet nem fejeződött be a várt módon jelentéseket vagy, hogy néhány funkció nem működik.
* A hibákat az alkalmazáson belül, amely jelennek meg a naplófájlokban vagy más értesítési módszer.

Az Azure storage szolgáltatásokkal kapcsolatos problémák általában egy négy tág kategóriába tartoznak:

* Az alkalmazás egy teljesítményprobléma, vagy a felhasználók által jelentett, vagy a teljesítmény-mérőszámok változásait által feltárt rendelkezik.
* Nincs probléma adódott egy vagy több régióban az Azure Storage-infrastruktúra.
* Az alkalmazás léptek fel hiba, vagy a felhasználók által jelentett, vagy a tár fel a hiba száma mérőszámok nyomon, hogy valamelyik növelését.
* Fejlesztés és tesztelés során előfordulhat, hogy használni a helyi storage emulator; a storage emulator használata kapcsolatos problémák merülhetnek fel.

Az alábbiakban azoknak a lépéseket kell követnie az alábbi négy kategóriákban problémák diagnosztizálása és hibaelhárítása. A szakasz "[hibaelhárítási útmutató]" Ez az útmutató későbbi néhány gyakori probléma felmerülhet a további részleteket biztosít.

### <a name="service-health-issues"></a>A szolgáltatás állapotbeli problémák
Szolgáltatás állapotbeli problémák általában a hatáskörén kívül esnek. A [az Azure portal](https://portal.azure.com) többek között a tárolási szolgáltatások Azure-szolgáltatásokkal folyamatban lévő problémákat ismerteti. Ha úgy döntött az írásvédett Georedundáns tárolás, a storage-fiók létrehozása során, majd ha az adatok nem érhető el az elsődleges helyen, az alkalmazás válthat ideiglenesen az írásvédett másolatot a másodlagos helyen. Olvasni a másodlagos, az alkalmazás kell tudni váltani használatával az elsődleges és másodlagos helyek között, és képesek együttműködni a csökkentett funkcionalitású módban csak olvasható adatok. Az Azure Storage ügyfélkódtáraival lehetővé teszi, hogy definiáljon egy újrapróbálkozási szabályzatot, amely képes olvasni a másodlagos tárhelyen, abban az esetben, ha az elsődleges storage-ból egy olvasási művelet sikertelen lesz. Az alkalmazásnak kell még figyelembe, hogy a másodlagos helyen lévő adatok konzisztens másolataként. További információkért tekintse meg a következő blogbejegyzésben: [Azure Storage Redundanciabeállításainál és olvasási-Access Geo Redundant Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Teljesítménnyel kapcsolatos problémák
Egy alkalmazás teljesítményének megítélése szubjektív lehet, főképp a felhasználó szemszögéből. Ezért fontos, hogy rendelkezzen olyan alapmértékekkel, amelyek segíthetnek a teljesítménnyel kapcsolatos problémák azonosításában. Számos tényező hatással lehet az Azure storage-szolgáltatás, alkalmazás ügyfelek szemszögéből teljesítményét. Ezek a tényezők a storage szolgáltatás, az ügyfél vagy a hálózati infrastruktúra; előfordulhat, hogy működjön. Ezért fontos egy olyan stratégia, a teljesítménnyel kapcsolatos problémák eredetének azonosításához.

Miután azonosította a metrikákat a teljesítményprobléma oka valószínűleg helyét, a naplófájlok ezután használhatja a diagnosztika és hibaelhárítás a probléma további részletes információkat találhat.

A szakasz "[hibaelhárítási útmutató]" Ez az útmutató későbbi gyakori teljesítménnyel kapcsolatos problémákat tapasztal, előfordulhat, hogy további információt tartalmaz.

### <a name="diagnosing-errors"></a>Hibák diagnosztizálása
Az alkalmazást is értesíti az ügyfélalkalmazás által jelentett hibákat. Storage-mérőszámok rögzíti a tárolási szolgáltatások a különböző típusú száma például **NetworkError**, **ClientTimeoutError**, vagy **AuthorizationError**. Storage Metrics csak rekordok számát a különböző alkalmazáshiba-típusok, miközben szerezhet be további részleteket tartalmaz az egyes kérelmek kiszolgálóoldali, az ügyféloldali és hálózati naplók vizsgálatával. Általában a storage szolgáltatás által visszaadott HTTP-állapotkódot ad arra utalhat, hogy miért a kérelem sikertelen volt.

> [!NOTE]
> Ne feledje, hogy kell látnia néhány időszakos hibába ütközik: például átmeneti hálózati körülmények miatti hibát, vagy az alkalmazáshibák.
> 
> 

A következő erőforrások lehetnek hasznosak ismertetése a storage szolgáltatással kapcsolatos állapota és a hiba kódja:

* [Általános REST API-t hibakódok](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [BLOB Service-hibakódok](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Várólista hibakódjai](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Tábla hibakódjai](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Fájl hibakódjai](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Storage emulator kapcsolatos problémák
Az Azure SDK tartalmaz egy fejlesztői munkaállomáson futtathatja storage emulatort. Az emulator szimulálja az Azure storage szolgáltatások viselkedését a legtöbb és hasznos fejlesztési és tesztelési, során való futtatása az alkalmazásokat, amelyek használják az Azure storage szolgáltatás Azure-előfizetés és a egy Azure storage-fiók nélkül.

A "[hibaelhárítási útmutató]" című részében olvashat ismertet néhány gyakori problémát észlelt, a storage emulator használatával.

### <a name="storage-logging-tools"></a>Storage naplózási eszközök
A Storage naplózási kiszolgálóoldali naplózását, az Azure storage-fiókban tárolási kérelmek biztosít. Kiszolgálóoldali naplózás engedélyezése és a napló az adatokkal kapcsolatos további információkért lásd: [tárolási naplózás engedélyezése és Teljesítménynapló-adatok elérése](http://go.microsoft.com/fwlink/?LinkId=510867).

A .NET-keretrendszerhez készült Storage ügyféloldali kódtára lehetővé teszi, hogy vonatkozik, tárolás, az alkalmazások által végrehajtott műveletek ügyféloldali naplózási adatok gyűjtése. További információt a [.NET-es Storage-ügyfélkódtárral történő ügyféloldali naplózást](http://go.microsoft.com/fwlink/?LinkId=510868) ismertető szakaszban találhat.

> [!NOTE]
> Bizonyos esetekben (például a SAS-engedélyezési hibák) egy felhasználói hiba történt, amelynek a kiszolgálóoldali tárolási naplók találhatja meg a kérelem adatok nem feltétlenül jelentik. Vizsgálja meg, hogy a probléma oka az ügyfélen a naplózási képességeket a Storage ügyféloldali kódtár használatával, vagy vizsgálja meg a hálózati Hálózatfigyelő eszközök használatával.
> 
> 

### <a name="using-network-logging-tools"></a>Hálózati naplózási eszközök használatával
Adja meg az adatokat, az ügyfél és kiszolgáló cseréje és a mögöttes hálózati körülmények kapcsolatos részletes információk az ügyfél és kiszolgáló közötti forgalom rögzítése. Hasznos hálózati naplózási eszközök a következők:

* [A fiddler](http://www.telerik.com/fiddler) egy ingyenes webes hibaelhárító proxy, amely lehetővé teszi, hogy a fejlécek és a HTTP és HTTPS kérelmek és válaszok üzenetek hasznos adatok vizsgálatát. További információkért lásd: [1. függelék: HTTP és HTTPS-forgalom rögzítése a Fiddler használatával](#appendix-1).
* [A Microsoft Hálózatfigyelő (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) és [Wireshark](http://www.wireshark.org/) vannak szabad hálózati protokoll elemzők, amelyek lehetővé teszik számos különféle hálózati protokollok csomag részletes információinak megtekintése. További információ a Wireshark: "[2. függelékben: Wireshark használatával rögzíti a hálózati forgalmat](#appendix-2)".
* Microsoft Message Analyzert egy olyan eszköz, amelynek Netmon, és hatályon kívül hálózati csomag adatai mellett, megtekintése és elemzése a log adatokból származó egyéb eszközökkel segít. További információkért lásd: "[3. függelék: a Microsoft Message Analyzer használatával rögzíti a hálózati forgalmat](#appendix-3)".
* Hajtson végre egy hálózati kapcsolat tesztet, ellenőrizze, hogy az ügyfélszámítógép kapcsolódik az Azure storage szolgáltatást a hálózaton keresztül szeretné, ha nem ezt megteheti a standard használatával **ping** eszköz az ügyfélen. Használhatja azonban a [ **tcping** eszköz](http://www.elifulkerson.com/projects/tcping.php) , ellenőrizze a kapcsolatot.

Sok esetben a Storage-naplózás és a Storage ügyféloldali kódtára a naplóadatok elegendő a probléma diagnosztizálása érdekében lesz, de bizonyos esetekben szükség lehet a részletesebb információkat, e hálózat naplózási eszközök által biztosított. Például a HTTP és HTTPS üzenetek megtekintése a Fiddler segítségével lehetővé teszi küldött és a tárolási szolgáltatások, amelyek lehetővé teszik, hogy megvizsgálja, hogyan ügyfélalkalmazás újrapróbálkozik a tárolási műveletek fejlécének és adattartalmának bontása adatok megtekintéséhez. Például a Wireshark protokoll elemzők TCP-adatok, amelyek lehetővé teszik, hogy elveszett csomagok és a kapcsolódási problémák elhárításához mutatják a csomag szintjén működik. Az Üzenetelemző a HTTP- és TCP rétegek is működnek.

## <a name="end-to-end-tracing"></a>Teljes körű nyomkövetés
Teljes körű nyomkövetési naplófájlok különféle a potenciális problémák kivizsgálása hasznos módszer. Használhatja a dátum/idő információkat a metrikák adatait arra utalhat, hogy hol kell elkezdeni keresi a naplófájlokban a részletes információkat, amelyek segítenek a probléma megoldásához.

### <a name="correlating-log-data"></a>Naplózási adatok korrelálása
Az ügyfélalkalmazások napló megtekintésekor hálózati nyomkövetéseket, és rendkívül fontos tudnia kell korrelálni kiszolgálóoldali tárolási naplózáshoz kérelmek között a különböző naplófájlokat. A naplófájlok a különböző mezők, amelyek hasznosak a korrelációs azonosítóként számú tartozik. Ügyfélkérelem-azonosító segítségével összehasonlíthatja a különböző naplók bejegyzéseket a leghasznosabb mező kitöltése. Egyes esetekben azonban hasznos lehet a kiszolgáló kérelem azonosítója vagy az időbélyegek használatával. A következő szakaszok a beállításokkal kapcsolatos további részletekért.

### <a name="client-request-id"></a>Ügyfélkérés azonosítója
A Storage ügyféloldali kódtára automatikusan létrehozza az egyedi ügyfélkérelem-azonosító minden egyes kérés esetében.

* Az ügyféloldali naplóban, amely a Storage ügyféloldali kódtára hoz létre, ügyfélkérelem-azonosító szerepel a **ügyfélkérelem-azonosító** mezőjét, minden naplóbejegyzés kapcsolódó a kérést.
* A hálózati nyomkövetés, például a Fiddler által rögzített, az ügyfélkérelem-azonosító kérelem üzenetek megjelennek a **x-ms-client-request-id** HTTP-fejléc értéke.
* A kiszolgálóoldali Storage Logging naplóban szereplő ügyfélkérelem-azonosító az ügyfél kérelem azonosító oszlop jelenik meg.

> [!NOTE]
> A többszörös kéréseket, megosztására azonos ügyfélkérelem-azonosító, mert az ügyfél ezt az értéket rendelhet hozzá (bár a Storage ügyféloldali kódtára automatikusan hozzárendeli az új érték) lehetőség. Az ügyfél újrapróbálkozik, ha minden kísérlet megoszthatja az ugyanazon kérelem Ügyfélazonosító. Tartozik egy kötegelt, az ügyfél, akkor a batch az egyetlen ügyfél kérelem azonosítóval rendelkezik.
> 
> 

### <a name="server-request-id"></a>Kiszolgálói kérelem azonosítója
A storage szolgáltatás automatikusan létrehozza a kiszolgáló kérelemazonosítókat.

* A kiszolgálóoldali Storage Logging naplóban megjelenik a kérés Azonosítóját a **kérelem Azonosítójának fejlécét** oszlop.
* A hálózati nyomkövetés, például a Fiddler által rögzített, az megjelenik a kérés Azonosítóját parancsválasz-üzeneteket, a **x-ms-request-id** HTTP-fejléc értéke.
* Az ügyféloldali naplóban, amely a Storage ügyféloldali kódtára hoz létre, a kérelem azonosítója megjelenik a **művelet szöveg** a naplóbejegyzést, a kiszolgáló válasza részleteit megjelenítő oszlopot.

> [!NOTE]
> A storage szolgáltatás mindig egy egyedi kiszolgálói kérelem Azonosítót rendel minden kérelmet kap, ezért minden egyes újrapróbálkozási kísérlet az ügyfél, és minden művelet, foglalja bele egy kötegbe rendelkezik egy egyedi kiszolgálói kérelem.
> 
> 

Ha a Storage ügyféloldali kódtára jelez egy **StorageException** az ügyfél a **RequestInformation** tulajdonság tartalmazza a **RequestResult** objektum, amely tartalmaz egy  **ServiceRequestID** tulajdonság. Emellett egy **RequestResult** objektum egy **OperationContext** példány.

Az alábbi példakód bemutatja, hogyan állíthatja be az egyéni **ügyfélkérelem** csatlakoztatásával érték egy **OperationContext** objektumot a kérés a storage szolgáltatásra. Azt is bemutatja, hogyan kérheti le a **ServerRequestId** értéket a válaszüzenetben.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Időbélyegek
Időbélyegeket használatával keresse meg a kapcsolódó bejegyzései, de legyen óvatos, ha bármely időbeállításainak az ügyfél és a fennálló kiszolgáló között. Keressen rá a plusz vagy mínusz az egyező kiszolgálóoldali bejegyzések alapján történő küldés időbélyegzője legyen az ügyfélen 15 perc. Ne feledje, hogy a blob metaadatait a blobot, mérőszámokat tartalmazó azt jelzi, hogy a metrikák a blobban tárolt időintervallumát. Ebben az időtartományban akkor hasznos, ha sok metrikák BLOB esetében azonos óra vagy perc.

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató
Ez a szakasz segítséget a diagnosztikát és az alkalmazás gyakori problémák elhárítása az Azure storage szolgáltatások használatakor előfordulhatnak. Az alábbi lista segítségével keresse meg az adott problémára vonatkozó információkat.

**Hibaelhárítás a döntési fa**

---
A probléma megoldásához kapcsolódik a teljesítmény, a tárolási szolgáltatások egyik?

* [A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]
* [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
* [A mérőszámok magas AverageServerLatency értéket mutatnak]
* [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]

---
A probléma megoldásához egy, a storage-szolgáltatás rendelkezésre állásának kapcsolódnak?

* [A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]
* [A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]
* [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]

---
 Az ügyfélalkalmazás fogadja-e HTTP 4XX (például a 404-es) választ a storage szolgáltatás?

* [Az ügyfél fogad üzeneteket HTTP 403 (tiltott)]
* [Az ügyfél fogad üzeneteket HTTP 404 (nem található)]
* [Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad]

---
[Metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére a tranzakció állapota]

---
[Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat]

---
[Nagy számú virtuális merevlemezekkel rendelkező virtuális gépek váratlan újraindulása tapasztal]

---
[A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával]

---
[.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.]

---
[Van egy másik probléma storage szolgáltatással]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrikák megjelenítése magas AverageE2ELatency és alacsony averageserverlatency értéket mutatnak
Az alábbi ábra a [az Azure portal](https://portal.azure.com) eszköz figyelési azt szemlélteti, ahol a **AverageE2ELatency** jelentősen nagyobb, mint a **averageserverlatency értéket mutatnak**.

![][4]

A storage szolgáltatás csak a mérték kiszámítja **AverageE2ELatency** sikeres kérések, és eltérően **averageserverlatency értéket mutatnak**, tartalmazza az ügyfél az adatok küldéséhez és fogadásához szükséges idő Nyugtázás a storage szolgáltatásból. Ezért közötti különbség **AverageE2ELatency** és **averageserverlatency értéket mutatnak** miatt az ügyfélalkalmazás, hogy lassan válaszol, vagy lehetnek a hálózaton lévő feltételek miatt.

> [!NOTE]
> Megtekintheti továbbá **E2ELatency** és **ServerLatency** egyes tárolási műveletek használatát a Storage naplózási adatok naplózása.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Ügyfél teljesítménnyel kapcsolatos problémák kivizsgálása
Az ügyfél lassan válaszol-e hiba lehetséges okai például a tekintettel a rendelkezésre álló kapcsolatok vagy Szálakba korlátozott számú vagy alatt álló erőforrások, például a Processzor, a memória vagy a hálózati sávszélesség alacsony. Előfordulhat, hogy a probléma megoldásához, hatékonyabb (például használatával asynchronní volání kanálu a tárolási szolgáltatáshoz) az Ügyfélkód módosításával, illetve (a több processzormag és memória) egy nagyobb méretű virtuális gép használatával.

A table és queue szolgáltatások, a Nagle algoritmus is okozhatnak nagy **AverageE2ELatency** képest, **averageserverlatency értéket mutatnak**: további információkért tekintse meg a bejegyzését [Nagle a Algoritmus el nem valódi felé kis méretű kérések](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). A kódban Nagle algoritmus segítségével letilthatja a **ServicePointManager** az osztály a **System.Net** névtér. Ez a tábla-visszavonásokat győződjön meg arról, vagy nyissa meg a queue szolgáltatások az alkalmazásban, mivel ez nincs hatással a már meglévő kapcsolatok előtt kell tennie. Az alábbi példa származik a **Application_Start** metódus egy feldolgozói szerepkörben.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Ellenőrizze az ügyféloldali naplókat láthatja a boxba küldi az ügyfélalkalmazás hány kéréseket, és általános .NET-ellenőrzés az ügyfél, például a Processzor, a .NET szemétgyűjtés, a hálózathasználat vagy a memória és a teljesítmény szűk kapcsolatos. Kiindulási pontként .NET ügyfélalkalmazások hibaelhárításhoz, tekintse meg a [hibakeresés, a nyomkövetés és Profilace](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Hálózati késési problémák kivizsgálása
A hálózat által okozott magas végpontok közötti késés általában átmeneti feltételek miatt. Mindkét átmeneti és állandó hálózati problémákat, például az eldobott csomagok segítségével megvizsgálhatja, eszközök, például a Wireshark vagy a Microsoft Message Analyzer használatával.

Hálózati problémák hibaelhárításához Wireshark használatával kapcsolatos további információkért lásd: "[2. függelékben: Wireshark használó rögzítheti a hálózati forgalom]."

Hálózati problémák elhárítása a Microsoft Message Analyzer használatával kapcsolatos további információkért lásd: "[3. függelék: Microsoft Message Analyzert segítségével rögzítheti a hálózati forgalom]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metrikák megjelenítése alacsony AverageE2ELatency és alacsony averageserverlatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal
Ebben a forgatókönyvben legvalószínűbb oka késés tapasztalható a tárolási kérelmek, a storage szolgáltatás elérése. Miért érdemes az ügyféltől érkező kérelmek nem körében keresztül a blob szolgáltatáshoz kell vizsgálni.

Az ügyfél küld kérelmeket késleltetése egyik lehetséges oka, hogy nincsenek-e korlátozott számú rendelkezésre álló kapcsolatok vagy Szálakba.

Emellett ellenőrizze, hogy az ügyfél több próbálkozás végez, és vizsgálja meg, hogy azért van. Annak megállapításához, hogy az ügyfél több próbálkozás végez, a következőket teheti:

* Vizsgálja meg a Storage Analytics naplók. Több próbálkozás történik, ha látni fogja az azonos ügyfél-Azonosítót, de különböző kiszolgálói kérelem azonosítók több műveletet.
* Vizsgálja meg a naplókba. Részletes naplózás jelzi, hogy történt-e egy újra.
* A kód hibakereséséhez és tulajdonságait, ellenőrizze a **OperationContext** a kéréshez társított objektumot. Ha a rendszer megpróbálja újból végrehajtani a műveletet a rendelkezik, a **RequestResults** tulajdonság több egyedi kiszolgálói kérelem azonosítókat tartalmazza. Az egyes kérések kezdési és befejezési időpontok is ellenőrizheti. További információkért lásd a kódmintát a szakaszban [Kiszolgálói kérelem azonosítója].

Ha nincsenek problémák az ügyfél, kell vizsgálni – például a csomagvesztés esetleges hálózati problémákat. Eszközök, például a Wireshark vagy a Microsoft Message Analyzer használatával hálózati problémák kivizsgálásában.

Hálózati problémák hibaelhárításához Wireshark használatával kapcsolatos további információkért lásd: "[2. függelékben: Wireshark használó rögzítheti a hálózati forgalom]."

Hálózati problémák elhárítása a Microsoft Message Analyzer használatával kapcsolatos további információkért lásd: "[3. függelék: Microsoft Message Analyzert segítségével rögzítheti a hálózati forgalom]."

### <a name="metrics-show-high-AverageServerLatency"></a>A mérőszámok magas averageserverlatency
Nagy esetén **averageserverlatency értéket mutatnak** letöltési blobkérelmek kell használnia a tárolási naplózáshoz naplók megtekintéséhez, hogy vannak-e a ugyanennek a blobnak (vagy a blobok készletét) vonatkozó ismételt kéréseket. A blob feltöltése kéréseket kell vizsgálni – milyen letiltása az ügyfél mérete (például blokkolja kisebb, mint 64 KB méretű eredményezhet terhek, ha az olvasások is szerepelnek kevesebb, mint 64 K adattömbök) használ, és ha több ügyfél para ugyanennek a blobnak a feltöltése blokkok párhuzamos. Azt is ellenőrizze a meghaladó eredményező kérések száma kiugrások percalapú metrikáit a második teljesítménycélokat Kiszolgálónként: is láthatja, "[A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]."

Ha magas **averageserverlatency értéket mutatnak** blob letöltése a kérelmeket, ha ott meg kell ismételni kérelmek ugyanazon blobot vagy blobok készlete, akkor érdemes lehet gyorsítótárazás az ilyen blobok használata az Azure Cache vagy az Azure Content Delivery Hálózat (CDN). A feltöltés kéréseket növelheti az átviteli sebességet a nagyobb blokkméret használatával. A lekérdezéseket egyben lehetővé kell tenniük az ügyféloldali gyorsítótárazást az ügyfeleken, amely ugyanazokat a lekérdezési műveleteket végrehajtani, és ahol az adatok nem változnak gyakran.

Magas **averageserverlatency értéket mutatnak** értékek rosszul tervezett táblát vagy lekérdezést, hogy vizsgálati műveleteket eredményez, vagy kövesse a Hozzáfűzés/illesztenie kizárási minta tünete is lehet. További információkért lásd: "[A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]".

> [!NOTE]
> Egy átfogó ellenőrzőlista teljesítmény ellenőrzőlistát itt találja: [a Microsoft Azure Storage teljesítmény és méretezhetőség – ellenőrzőlista](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Nem várt késedelmeket egy üzenetsorban található üzenetek tapasztal
Ha egy alkalmazás hozzáad egy üzenetet egy üzenetsorba és olvasása az üzenetsorból számára elérhetővé válik a időpontja közötti késleltetést jelzi, akkor az alábbi lépéseket a probléma diagnosztizálása érdekében kell végrehajtani:

* Ellenőrizze, hogy az alkalmazás sikeresen ad az üzeneteket az üzenetsorba. Ellenőrizze, hogy az alkalmazás nem újra megpróbálja a **AddMessage** többször sikeres előtt. A Storage ügyféloldali kódtára naplók bármely ismételt próbálkozás tárolási műveletek jelennek meg.
* Ellenőrizze, nincs torzulása a feldolgozói szerepkör, amely az üzenetet ad hozzá az üzenetsorhoz között nincs óra és a feldolgozói szerepkör, amely kiolvassa az üzenetet az üzenetsorból, amely megkönnyíti jelennek meg, ha nincs késleltetés feldolgozási.
* Ellenőrizze, hogy ha a feldolgozói szerepkör, amely beolvassa az üzeneteket az üzenetsorból nem működik. Ha egy várólista ügyfél meghívja a **GetMessage** módszer, de nem tudja nyugtázással válaszol, az üzenet nem látható, amíg az üzenetsorban maradnak a **invisibilityTimeout** időszakának lejártáig. Ezen a ponton az üzenet feldolgozására újra elérhetővé válik.
* Ellenőrizze, hogy ha az üzenetsor hossza idővel nő. Ez akkor fordulhat elő, ha nem rendelkezik elegendő feldolgozó is rendelkezésre áll, minden más feldolgozók az üzenetsorban helyez üzenetek feldolgozásához. A mérőszámok megtekintéséhez, ha a törlési kérelmek nem működik, és az eltávolítási számának-üzenetekről, amelyeken az üzenet törlése sikertelenül megpróbálják jelezheti a is ellenőrizheti.
* Vizsgálja meg a naplókat tároló naplózása minden olyan üzenetsor-műveletek, amelyek rendelkeznek a vártnál magasabb **E2ELatency** és **ServerLatency** idő a szokásosnál hosszabb ideig értékekhez.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Mérőszámok emelkedő percentthrottlingerror értéket mutatnak
Szabályozási hibák fordulhatnak elő, ha túllépi a skálázhatósági célokat, a storage szolgáltatás. A storage szolgáltatás szabályozások győződjön meg arról, hogy nincs egyetlen ügyfél vagy a bérlői rovására mások a szolgáltatás használhatja. További információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](storage-scalability-targets.md) teljesítménycélokat storage-fiókok és a storage-fiókokban lévő partíciók teljesítménycéljai részleteiért.

Ha a **percentthrottlingerror értéket mutatnak** metrika emelkedő szabályozási hiba miatt sikertelen kérelmek aránya, ki kell vizsgálni helyzetek egyikében:

* [Átmeneti növekedése percentthrottlingerror értéket mutatnak]
* [Véglegesen növeli a hibás percentthrottlingerror értéket mutatnak]

Növekedése **percentthrottlingerror értéket mutatnak** gyakran tárolási kérelmek számának növelését, egy időben történik, vagy ha kezdetben betölteni az alkalmazás tesztelése. Ez előfordulhat, hogy is manifest magát az ügyfél "503-as kiszolgáló foglalt" vagy "500 művelet időtúllépése" HTTP állapotüzenete tárolási műveletek.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Átmeneti növekedése percentthrottlingerror értéket mutatnak
Ha látja az értékét kiugrások **percentthrottlingerror értéket mutatnak** , amely az alkalmazás magas tevékenységű időszakok egybe, az ügyfél egy exponenciális (lineáris) visszatartási stratégia újrapróbálkozások megvalósítása. Visszatartás újrapróbálkozások a partíció azonnali terhelésének csökkentése, és segít az alkalmazás adatforgalmi kiugrások a forgalom. A Storage ügyféloldali kódtár használatával újrapróbálkozási szabályzatok implementálása kapcsolatos további információkért lásd: [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Is megjelenhetnek értékét kiugrások **percentthrottlingerror értéket mutatnak** , amely nem esik egybe az alkalmazás magas tevékenységű időszakok: a legvalószínűbb oka a terheléselosztás javítása érdekében a partíciók áthelyezése a storage-szolgáltatás.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Véglegesen növeli a hibás percentthrottlingerror értéket mutatnak
Ha folyamatosan magas értéket **percentthrottlingerror értéket mutatnak** véglegesen növeli a tranzakció-köteteket, vagy ha a kezdeti betöltés hajt végre a következő teszteli, az alkalmazásban, majd fel kell mérnie hogyan az alkalmazás adattárolási partíciókat, és hogy közeledik a tárfiók skálázási célértékei használ. Például ha szabályozás hibák egy üzenetsorban (amely egy adott partíció számít), majd érdemes további várólisták használata elosztani a tranzakciók több partíción. Ha szabályozás hibák táblán, fontolja meg egy másik particionálási sémát, a tranzakciók helyezkednek el több partíciót széles körének partíciós kulcsérték használatával szeretne. A probléma egy közös ok: a prepend hozzáfűzése kizárási minta a dátumot jelöli ki a partíciós kulcs, majd egy partíció egy adott nap összes adat íródik: terhelés, ennek hatására írási szűk keresztmetszetté. Fontolja meg egy másik particionálási Tervező, vagy mérlegelje, hogy a blob storage-dzsal jobb megoldás lehet. Is ellenőrizheti a eredményeként a csúcsterhelésekkel e szabályozási történik, és vizsgálja meg a minta kérelmek simítás módjait.

Ha a tranzakciók több partíción terjeszt, továbbra is kell figyelembe állítsa be a tárfiók skálázási korlátaival. Például ha tíz üzenetsorok használta, a maximális száma másodpercenként 2000 1 KB méretű üzenetek feldolgozása, fogjuk elérte a tárfiók másodpercenként 20 000 üzenet összesített korlátot. Ha szeretné feldolgozni másodpercenként több mint 20 000 entitások, érdemes több tárfiók használata. Azt is figyelembe kell, hogy a kérelmek és az entitások méretétől akkor van hatása, amikor a storage szolgáltatás szabályozza-e az ügyfelek a kell vennie: Ha nagyobb kérelmek és entitások, akkor előfordulhat, hogy szabályozva hamarabb.

Nem elég hatékony Lekérdezéstervezés is okozhat, hogy nyomja le a táblázat partíciókat skálázási korlátaival. Például egy lekérdezési szűrő, amely egy partíció csak kijelöli az entitásokat egy százalékát, de, amely megvizsgálja egy partíció összes entitását kell minden entitás eléréséhez. Minden entitás, olvassa el az adott partíció; tranzakciók száma is beleszámít. így könnyen elérheti a skálázhatósági célokat.

> [!NOTE]
> A Teljesítménytesztelés bármely nem elég hatékony lekérdezési tervek az alkalmazás kódproblémájáról van.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Mérőszámok emelkedő percenttimeouterror értéket mutatnak
A metrikák megjelenítése növekedése **percenttimeouterror értéket mutatnak** egy, a storage-szolgáltatás. Egy időben az ügyfél kap "500 művelet időtúllépése" HTTP-állapotüzenetek nagy mennyiségű tárolási műveletek.

> [!NOTE]
> Előfordulhat, hogy hibába ütközik, időkorlát ideiglenesen, a storage szolgáltatás jóváírásokat összefoglaló balances okozta terhelés egy partíción egy új kiszolgálóra helyezi át.
> 
> 

A **percenttimeouterror értéket mutatnak** metrika az összesítést a következő metrikák közül: **ClientTimeoutError**, **AnonymousClientTimeoutError**,  **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, és **SASServerTimeoutError**.

A kiszolgáló időkorlátja a kiszolgálón hiba okozza. Az ügyfél-időtúllépések fordulhat elő, mert egy műveletet a kiszolgálón túllépte az időkorlátot, az ügyfél; megadott például a Storage ügyféloldali kódtár használatával egy ügyfél állíthat egy művelet időtúllépés használatával a **ServerTimeout** tulajdonságát a **QueueRequestOptions** osztály.

Kiszolgáló időtúllépése a storage szolgáltatás, amely további vizsgálat szükséges problémára utalhat. Mérőszámok megtekintéséhez, hogy elérte-e a szolgáltatás skálázhatóságának korlátai, és azonosíthatja a probléma miatt előfordulhat, hogy bármely csúcsterhelésekkel használatát. Ha a probléma időszakos, ennek oka a terheléselosztás lehet a szolgáltatások. Ha a probléma állandó, és nem okozza az alkalmazás szerezze meg a szolgáltatás skálázhatóságának korlátai, egy támogatási problémát kell előléptetése. Az ügyfél-időtúllépések döntse el, ha az időkorlát az ügyfél és az időkorlát értékét állítsa be az ügyfél vagy módosítása egy megfelelő értékre van állítva, vagy vizsgálja meg, hogyan javíthatja a storage szolgáltatásban, a műveletek teljesítményének például optimalizálásával a tábla lekérdezések vagy az üzenetek méretének csökkentését.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Mérőszámok emelkedő percentnetworkerror értéket mutatnak
A metrikák megjelenítése növekedése **percentnetworkerror értéket mutatnak** egy, a storage-szolgáltatás. A **percentnetworkerror értéket mutatnak** metrika az összesítést a következő metrikák közül: **NetworkError**, **AnonymousNetworkError**, és **SASNetworkError** . Ezek fordulhat elő, amikor a storage szolgáltatás észleli a hálózati hiba, amikor az ügyfél egy storage-kérelmet.

Ez a hiba leggyakoribb oka egy ügyfél leválasztása a storage szolgáltatásban egy időkorlát lejáratáig. Vizsgálja meg a kódot az ügyfélben tudni, miért és mikor az ügyfél a storage szolgáltatás kapcsolata megszakad. Az ügyfél hálózati kapcsolatban fennálló problémák vizsgálatához Wireshark, a Microsoft Message Analyzert vagy Tcping is használja. Ezek az eszközök ismertetett a [mellékletek].

### <a name="the-client-is-receiving-403-messages"></a>Az ügyfél fogad üzeneteket HTTP 403 (tiltott)
Ha az ügyfélalkalmazás HTTP 403 (tiltott) hibák szűrész, ennek valószínű oka, hogy az ügyfél a következő egy lejárt közös hozzáférésű Jogosultságkód (SAS) egy storage-kérelmet küld a (bár a másik lehetséges okok a következők óra torzulása, érvénytelen kulcsok és üres fejlécek ). Ha egy lejárt SAS-kulcsot az okot, nem láthatja el azokat a bejegyzéseket a kiszolgálóoldali Storage Logging naplóadatokat. Az alábbi táblázat a ügyféloldali naplóból, amely bemutatja, hogy ez a probléma lépett fel a Storage ügyféloldali kódtár által létrehozott minta:

| Forrás | Részletesség | Részletesség | Ügyfélkérelem azonosítója | A művelet szöveg |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab-… |Hely elsődleges hely módban PrimaryOnly kiindulási műveletet. |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab -… |A szinkron kérések kezdési https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp; sr = c&amp;si = mypolicy&amp;sig OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % = 3D&amp;api-version = 2014. 02. 14. |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab -… |Várakozás a válaszra. |
| Microsoft.WindowsAzure.Storage |Figyelmeztetés |2 |85d077ab -… |Kivétel történt a válasz várakozás közben: A távoli kiszolgáló hibát adott vissza: (403) tiltott. |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab -… |A válasz érkezett. Állapotkód = 403-as, kérelem azonosítója = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, tartalom-MD5 =, az ETag =. |
| Microsoft.WindowsAzure.Storage |Figyelmeztetés |2 |85d077ab -… |Kivétel történt a művelet során: A távoli kiszolgáló hibát adott vissza: (403) tiltott... |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab -… |Annak ellenőrzése, ha a műveletet meg kell ismételni. Ismétlések száma = 0, HTTP-állapotkód: 403-as, Exception = = a távoli kiszolgáló hibát adott vissza: (403) tiltott... |
| Microsoft.WindowsAzure.Storage |Információ |3 |85d077ab -… |A következő helyre elsődleges, a hely módja alapján van beállítva. |
| Microsoft.WindowsAzure.Storage |Hiba |1 |85d077ab -… |Újrapróbálkozási szabályzat nem engedélyezte az újrapróbálkozást. Sikertelen a távoli kiszolgálóval hibát adott vissza: (403) tiltott. |

Ebben az esetben kell vizsgálni, miért lejár az SAS-token előtt az ügyfél elküldi a kiszolgálón:

* Általában nem kell beállítania a kezdési időt, amikor létrehoz egy SAS-ügyfélhez azonnali használatát. Ha kis óra között van különbség a gazdagép létrehozása a SAS-t, az aktuális idő és a storage szolgáltatás, akkor lehetséges, hogy a storage szolgáltatás, amely még nem érvényes SAS fogadásához.
* A SAS egy nagyon rövid lejárati idő nincs beállítva. Újra a gazdagép létrehozása a SAS- és a storage szolgáltatás kis óra különbségeit kártevőnek lejár a vártnál korábban SAS vezethet.
* A verzió paramétert az SAS-kulcs does (például **sv = 2015-04-05**) használ, a Storage ügyféloldali kódtára verziójának megfelelő? Azt javasoljuk, hogy mindig a legújabb verzióját használja a [a Storage ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/).
* A tárelérési kulcsok újragenerálása, ha lehetséges, hogy meglévő SAS-tokenekkel érvényteleníti. A probléma akkor fordulhatnak elő, ha létrehoz egy hosszú lejárati idő, az ügyfélalkalmazások a gyorsítótárhoz az SAS-tokeneket.

A Storage ügyféloldali kódtára segítségével SAS-jogkivonatokat hoz létre, majd, hogy könnyedén hozhatók létre érvényes jogkivonatot. Azonban, ha manuálisan hozhat létre, amely a SAS-tokeneket és a Storage REST API használatával, lásd: [hozzáférés delegálása közös hozzáférésű Jogosultságkód](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Az ügyfél fogad üzeneteket HTTP 404 (nem található)
Ha az ügyfélalkalmazás egy HTTP 404 (nem található) üzenetet kap, a kiszolgálóról, akkor ez azt jelenti, hogy az ügyfél próbált használni (például egy entitás, tábla, a blob, tárolót vagy üzenetsor) az objektum nem létezik a storage szolgáltatásban. Létezik néhány lehetséges oka, például:

* [Az ügyfél vagy egy másik folyamat korábban törölték az objektumot]
* [Egy közös hozzáférésű Jogosultságkód (SAS) hitelesítési hiba]
* [Ügyféloldali JavaScript-kód nincs engedélye az objektumhoz való hozzáférést]
* [Hálózati hiba]

#### <a name="client-previously-deleted-the-object"></a>Az ügyfél vagy egy másik folyamat korábban törölték az objektumot
A forgatókönyvek, ahol az ügyfél megpróbálja olvasni, frissíteni vagy törölni az adatokat egy tárolási szolgáltatásba általában könnyedén azonosíthatja a kiszolgálóoldali naplók egy korábbi művelet, amely a szóban forgó objektum törli a storage szolgáltatásból. Gyakran előfordul a naplóadatok mutatja, hogy egy másik felhasználó vagy folyamat az objektum törölve. A kiszolgálóoldali Storage Logging naplóban művelet-típusát és a kért objektum-kulcs oszlop megjelenítése egy objektum törlésekor az ügyfél.

A forgatókönyvben, ahol egy ügyfél megpróbál egy olyan objektum beszúrására nem lehet azonnal nyilvánvaló miért ennek eredményeként HTTP 404 (nem található) választ, hogy az ügyfél egy új objektumot hoz létre. Azonban ha az ügyfél képesnek kell lennie, keresse meg a blob-tárolóba, ha az ügyfél egy üzenetsorban található képesnek kell lennie egy üzenetet hoz létre egy blobot hoz létre, és ha az ügyfél van hozzáad egy sort képesnek kell lennie a tábla található.

Az ügyféloldali napló használhatja a Storage ügyféloldali kódtára a kapjanak egy részletesebb ismertetése amikor az ügyfél a storage szolgáltatás bizonyos kéréseket küld.

A következő ügyféloldali naplót, a Storage ügyféloldali kódtár által generált szemlélteti a problémát, ha az ügyfél most készül a BLOB nem található a tárolóban. Ez a napló tartalmazza a következő tárolási műveletek részleteit:

| Kérelem azonosítója | Művelet |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metódus használatával törölheti a blob-tároló. Vegye figyelembe, hogy ez a művelet magában foglalja egy **fő** a tároló létezik-e keresni a kérelmet. |
| e2d06d78… |**CreateIfNotExists** metódust a blob-tároló létrehozásához. Vegye figyelembe, hogy ez a művelet magában foglalja egy **fő** kérelmet, amely ellenőrzi, hogy a tároló létezik-e. A **fő** 404-es üzenetet adja vissza, de továbbra is. |
| de8b1c3c-... |**UploadFromStream** metódust a blob létrehozásához. A **PUT** kérelme sikertelen a 404-es üzenet |

Naplófájl-bejegyzéseket:

| Kérelem azonosítója | A művelet szöveg |
| --- | --- |
| 07b26a5d-... |A szinkron kérések kezdési https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Várakozás a válaszra. |
| 07b26a5d-... |A válasz érkezett. Állapotkód: 200, kérelem azonosítója = = eeead849... Tartalom-MD5 =, az ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Válaszfejlécek sikerült feldolgozni, folytatja a műveletet a többi. |
| 07b26a5d-... |Választörzs letöltése. |
| 07b26a5d-... |A művelet sikerült. |
| 07b26a5d-... |A szinkron kérések kezdési https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Várakozás a válaszra. |
| 07b26a5d-... |A válasz érkezett. Állapotkód: 202, kérelem azonosítója = = 6ab2a4cf-..., a tartalom-MD5 =, az ETag =. |
| 07b26a5d-... |Válaszfejlécek sikerült feldolgozni, folytatja a műveletet a többi. |
| 07b26a5d-... |Választörzs letöltése. |
| 07b26a5d-... |A művelet sikerült. |
| e2d06d78-... |Az aszinkron kérés kezdési https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Várakozás a válaszra. |
| de8b1c3c-... |A szinkron kérések kezdési https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Felkészülés a kérelem adatokat írni. |
| e2d06d78-... |Kivétel történt a válasz várakozás közben: A távoli kiszolgáló hibát adott vissza: (404) nem található... |
| e2d06d78-... |A válasz érkezett. Állapotkód: 404-es, kérelem azonosítója = = 353ae3bc-..., a tartalom-MD5 =, az ETag =. |
| e2d06d78-... |Válaszfejlécek sikerült feldolgozni, folytatja a műveletet a többi. |
| e2d06d78-... |Választörzs letöltése. |
| e2d06d78-... |A művelet sikerült. |
| e2d06d78-... |Az aszinkron kérés kezdési https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Várakozás a válaszra. |
| de8b1c3c-... |Írás kérelemadatok. |
| de8b1c3c-... |Várakozás a válaszra. |
| e2d06d78-... |Kivétel történt a válasz várakozás közben: A távoli kiszolgáló hibát adott vissza: (409) ütközés... |
| e2d06d78-... |A válasz érkezett. Állapotkód: 409, kérelem azonosítója = = c27da20e-..., a tartalom-MD5 =, az ETag =. |
| e2d06d78-... |Hiba történt a választörzs letöltése. |
| de8b1c3c-... |Kivétel történt a válasz várakozás közben: A távoli kiszolgáló hibát adott vissza: (404) nem található... |
| de8b1c3c-... |A válasz érkezett. Állapotkód: 404-es, kérelem azonosítója = = 0eaeab3e-..., a tartalom-MD5 =, az ETag =. |
| de8b1c3c-... |Kivétel történt a művelet során: A távoli kiszolgáló hibát adott vissza: (404) nem található... |
| de8b1c3c-... |Újrapróbálkozási szabályzat nem engedélyezte az újrapróbálkozást. Sikertelen a távoli kiszolgálóval hibát adott vissza: (404) nem található... |
| e2d06d78-... |Újrapróbálkozási szabályzat nem engedélyezte az újrapróbálkozást. Sikertelen a távoli kiszolgálóval hibát adott vissza: (409) ütközés... |

Ebben a példában a naplóból azonosítható, hogy az ügyfél van kihagyásos érkező kérelmeket a **CreateIfNotExists** metódus (... kérés azonosítója e2d06d78) érkező kérésekkel a **UploadFromStream** metódus (de8b1c3c-...). Ez kihagyásos oka, hogy az ügyfélalkalmazás aszinkron módon meghívása a metódusokat. Módosítsa a aszinkron kódot az ügyfélszámítógép győződjön meg arról, hogy adatokat feltölteni egy blobot a tárolóban, mielőtt létrehozza a tárolót. Ideális esetben minden a tároló előre kell létrehoznia.

#### <a name="SAS-authorization-issue"></a>Egy közös hozzáférésű Jogosultságkód (SAS) hitelesítési probléma
Ha az ügyfélalkalmazást próbál használni egy SAS-kulcs, amely nem tartalmazza a szükséges engedélyekkel a művelet, a storage szolgáltatás egy HTTP 404 (nem található) üzenet visszaadja az ügyfélnek. Egy időben, látni fogja emellett nullától eltérő értéket **SASAuthorizationError** a metrikákat.

Az alábbi táblázatban látható egy minta kiszolgálóoldali naplófájlüzenetre az Storage Logging naplófájlban:

| Name (Név) | Érték |
| --- | --- |
| Kérelem kezdete | 2014-05-30T06:17:48.4473697Z |
| Művelet típusa     | GetBlobProperties            |
| Kérés állapota     | SASAuthorizationError        |
| HTTP-állapotkód   | 404                          |
| Hitelesítés típusa| SAS                          |
| Szolgáltatás típusa       | Blob                         |
| Lekérdezés URL-címe        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Kérelem Azonosítójának fejlécét  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Ügyfélkérelem azonosítója  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Vizsgálja meg, miért érdemes az ügyfélalkalmazást próbál végrehajtania egy műveletet, amelynek, nem rendelkezik engedélyekkel.

#### <a name="JavaScript-code-does-not-have-permission"></a>Ügyféloldali JavaScript-kód nem rendelkezik hozzáféréssel az objektum
Ha egy JavaScript-ügyfelet használ, és a storage szolgáltatás HTTP 404-es üzeneteket adja vissza, akkor ellenőrizze a böngészőben a következő JavaScript-hibák:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Az Internet Explorer az F12 billentyűt a fejlesztői eszközök használatával nyomon követése az ügyféloldali JavaScript-problémák hibaelhárítása során a böngésző és a storage szolgáltatás között cserélt üzeneteket.
> 
> 

Ezek a hibák elő, amikor a böngésző valósítja meg a [azonos szabályzat](http://www.w3.org/Security/wiki/Same_Origin_Policy) biztonsági korlátozás, amely megakadályozza, hogy a webes-API meghívása egy másik tartományban a tartományból az oldal származik.

A JavaScript probléma idegen eredetű erőforrások megosztása (CORS) is konfigurálhat a storage szolgáltatás, az ügyfél fér hozzá. További információkért lásd: [eltérő eredetű erőforrások megosztása (CORS) támogatása az Azure tárolási szolgáltatások](http://msdn.microsoft.com/library/azure/dn535601.aspx).

Az alábbi példakód bemutatja, hogyan konfigurálja, hogy a blobok a blob storage szolgáltatás eléréséhez a Contoso tartományban futó JavaScript a blob szolgáltatást:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Hálózati hiba
Bizonyos körülmények között elveszett hálózati csomagokat a társzolgáltatás, térjen vissza az ügyfél HTTP 404-es üzenetek vezethet. Például ha az ügyfélalkalmazás entitás törlése a table szolgáltatásból, tekintse meg az ügyfél egy tárolási kivétel jelentés throw egy "HTTP 404 (nem található)" állapotüzenet a table szolgáltatásból. Amikor megvizsgálja a tábla a table storage szolgáltatásban, láthatja, hogy a szolgáltatás minden tudta törölni az entitást, kért.

Az ügyfél a kivétel részleteit tartalmazza a kérés azonosítója (7e84f12d...), a table service, a kérelem által hozzárendelt: ezen információk használatával keresse meg a kérés részleteinek megtekintéséhez a kiszolgálóoldali tárolási naplók a Keresés a **kérelemfejlécből-azonosító**  oszlop a naplófájlban. A mérőszámok a hibák fordulnak elő, és kereshet a naplófájlokban, a metrikák rögzített Ez a hiba ideje alapján azonosítására is használhatja. A naplóbejegyzés jeleníti meg, hogy a törlés nem sikerült egy "HTTP (404) ügyfél más hiba" állapotüzenetet. Az azonos naplóbejegyzés is tartalmazza a kérés azonosítója, az ügyfél által generált a **client-request-id** oszlop (813ea74f...).

A kiszolgálóoldali naplót is tartalmaz egy másik bejegyzés ugyanazzal a **client-request-id** (813ea74f...) értéket a sikeres műveletet ugyanahhoz az entitáshoz, és az ugyanazon ügyféltől érkező törlése. Ez a művelet sikeres törlése nagyon kis türelmet kérünk került sor, a sikertelen kérelem törlése előtt.

Ebben a forgatókönyvben az a legvalószínűbb oka az, hogy az ügyfél az entitás törlési kérelmet küldött a table service, amely sikeres volt, de nem kapott nyugtázást (miatt egy átmeneti hálózati hiba következtében) a kiszolgálóról. Az ügyfél ezután automatikusan újra megpróbálja a műveletet (azonos **client-request-id**), és ez nem sikerült, mert az entitás már törlődött.

Ha a probléma gyakran előfordul, meg kell vizsgálni, miért az ügyfél nem tud fogadni a nyugtázás a table szolgáltatásból. Ha a probléma időszakos, meg kell kezelni a "HTTP (404) nem található" hibát és bejelentkezéshez, az ügyfél, de lehetővé teszi az ügyfél továbbra is.

### <a name="the-client-is-receiving-409-messages"></a>Az ügyfél fogad üzeneteket HTTP 409 (Ütközés)
Az alábbi táblázatban láthatók a kiszolgálóoldali napló két Ügyfélműveletek kivonatát: **DeleteIfExists** azonnal által követett **CreateIfNotExists** blob tároló nevének használatával. Minden ügyfél művelet eredményeként két kérelmet küldött a kiszolgálónak, először egy **GetContainerProperties** kérést ellenőrizze, hogy a tároló, kiegészítve a **DeleteContainer** vagy  **CreateContainer** kérelmet.

| Időbélyeg | Művelet | Eredmény | Tárolónév | Ügyfélkérelem azonosítója |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

A kód az ügyfélalkalmazásban töröl, és azonnal újból létrehozza a blob-tároló nevének használatával: a **CreateIfNotExists** metódus (ügyfél kérés azonosítója bc881924-...) idővel sikertelen, és a HTTP 409 (Ütközés). Ha törli az ügyfél a blobtárolók, táblák és üzenetsorok neve előtt egy rövid ideig ismét elérhetővé válik.

Az ügyfélalkalmazás használjon egyedi a tároló nevének minden alkalommal, amikor új tárolók hoz létre, ha a törlés/hozza létre újra a minta akkor közös.

### <a name="metrics-show-low-percent-success"></a>Metrikák megjelenítése alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors állapotú tranzakciós műveletek
A **PercentSuccess** metrika a százalékos aránya, amelyek sikerrel járt-e a HTTP-állapotkód: alapján műveleteket rögzíti. A 2XX állapotkódok műveletek száma sikeres, mivel a műveletek a állapotkódok 3XX, 4XX és 5XX tartományok számítanak, sikertelen és alacsonyabb a **PercentSucess** Átjárómetrika értékeként. A kiszolgálóoldali tárolási naplófájlokban ezeket a műveleteket rögzíti állapotú tranzakciós **ClientOtherErrors**.

Fontos megjegyezni, hogy ezek a műveletek sikeresen befejeződtek, és ezért nincs hatással a többi mérőszámokat, például a rendelkezésre állási. Néhány művelet, amely sikeresen végrehajtható legyen, de a sikertelen HTTP-állapotkódok eredményezhet, amely többek között:

* **ResourceNotFound** (nem található 404), például a GET kérést egy blobba, amely nem létezik.
* **ResouceAlreadyExists** (ütközés 409), például az egy **CreateIfNotExist** művelet, ahol az erőforrás már létezik.
* **ConditionNotMet** (nem módosított 304), például egy feltételes művelet, például amikor egy ügyfél küld a egy **ETag** érték és a egy olyan HTTP **If-None-Match** fejléc, kép kérése csak akkor, ha van frissítve lett az utolsó művelet óta.

Gyakori REST API-val hibakódok, a tárolási szolgáltatások lapon visszaadó listáját találja [gyakori REST API-hibakódok](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Kapacitás mérőszámok emelkedő váratlan tárolási kapacitás kihasználtsága
Ha hirtelen, kapacitáshasználatát a storage-fiókban a váratlan módosítások megvizsgálhatja az okok bejegyzéseinek áttekintésével kezdődik a rendelkezésre állási metrikák; Ha például növekedése kérelmek vezethet, mivel előfordulhat, hogy rendelkezik várt lehet szabadítson fel lemezterületet és alkalmazásspecifikus karbantartási műveleteket lehet, hogy nem az elvárásoknak megfelelően működik (például használja a blob storage mennyiségének olyan növekedése nem sikerült törölni a száma mert lejárt a SAS-tokeneket használt szabadítson fel helyet).

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>A probléma merül fel, a storage emulator használatával a fejlesztési-tesztelési
Általában a storage emulator használata a fejlesztés során, és ellenőrizze, hogy a követelmény az Azure storage-fiókhoz tartozó elkerülése érdekében. A gyakori kérdések, amely akkor fordulhat elő, a storage emulator használata esetén a következők:

* [A storage emulator az "X" funkció nem működik]
* [Hiba történt "az egyik a HTTP-fejlécek értéke nem a megfelelő formátumban" a storage emulator használata esetén]
* [A storage emulator futtatása rendszergazdai jogosultságra van szükség]

#### <a name="feature-X-is-not-working"></a>A storage emulator az "X" funkció nem működik
A storage emulator nem támogatja az összes, az Azure storage-szolgáltatások, például a szolgáltatás funkcióit. További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral).

Ezeket a szolgáltatásokat, a storage emulator nem támogatja használja az Azure storage szolgáltatás a felhőben.

#### <a name="error-HTTP-header-not-correct-format"></a>Hiba történt "az egyik a HTTP-fejlécek értéke nem a megfelelő formátumban" a storage emulator használata esetén
Teszteli az alkalmazás, mint például a Storage ügyféloldali kódtára a helyi storage emulator és metódus hívások elleni használó **CreateIfNotExists** sikertelen, és a "egy a HTTP-fejléceket az érték nem szerepel a megfelelő hibaüzenet formátum." Ez azt jelzi, hogy a storage emulator használata verziója nem támogatja a verzióját használja, a storage ügyféloldali kódtára. A Storage ügyféloldali kódtár hozzáadása a fejléc **x-ms-version** teszi összes kérésre. Ha a storage emulator nem ismeri fel az értéket a **x-ms-version** fejléc elutasítja a kérelmet.

A Storage Szalagtári ügyfél naplók segítségével tekintse meg az értékét a **x-ms-version fejlécnek** küldi azt. Emellett megtekintheti az értékét a **x-ms-version fejlécnek** Fiddler használatakor, az ügyfélalkalmazás a kérelmek nyomon követéséhez.

Ebben a forgatókönyvben általában akkor fordul elő, ha telepít, és a Storage ügyféloldali kódtár legújabb verzióját használja a storage emulator frissítése nélkül. Kell vagy a storage emulator legújabb verziójának telepítéséhez, vagy használja a felhőalapú tárolás helyett az emulátorban a fejlesztéshez és teszteléshez.

#### <a name="storage-emulator-requires-administrative-privileges"></a>A storage emulator futtatása rendszergazdai jogosultságra van szükség
Kéri a rendszergazdai hitelesítő adatok a storage emulator futtatásakor. Ez csak akkor történik, először a storage emulator vannak inicializálása közben. Miután a storage emulator van inicializálva, nem kell rendszergazdai jogosultságokkal futtassa.

További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral). A Visual Studióban, amely rendszergazdai jogosultságokkal is szüksége lesz a storage emulator inicializálják is lehet.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>.NET-hez készült Azure SDK telepítése problémák merültek fel
A helyi gépen a storage emulator telepíteni próbálja meg az SDK telepítése, meghiúsul. A telepítési naplót a következő üzenetek egyikét tartalmazza:

* CAQuietExec: Hiba: nem érhető el az SQL-példány
* CAQuietExec: Hiba: nem hozható létre adatbázis

Problémáját LocalDB meglévő telepítés oka. Alapértelmezés szerint a storage Emulator LocalDB megőrizheti az adatokat, ha azt az Azure storage szolgáltatások szimulálja. A következő parancsok futtatásával egy parancssori ablakban az SDK telepítésének megkísérlése előtt alaphelyzetbe állíthatja a LocalDB-példányt.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

A **törlése** parancs eltávolítja a storage emulator az előző telepítések bármely régi adatbázisfájlokat.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>A storage szolgáltatás különböző problémája van
Ha az előző hibaelhárítási szakaszokat tartalmazza a tapasztalt problémát, a storage szolgáltatással, diagnosztizálása és a hiba elhárításához a következő módon el kell fogadnia.

* Ellenőrizze a metrikák, ha a várt viszonyítási működése a változás. A metrikák a meg tudja határozni, hogy a probléma átmeneti vagy állandó, és mely tárolási műveletek a probléma van hatással is lehet.
* A kiszolgálóoldali adatainak előforduló hibákat kapcsolatos részletesebb információkért keressen segítséget a metrikák információt is használhatja. Ezek az információk segíthetik elhárítása és a probléma megoldásához.
* Ha az adatokat a kiszolgálóoldali naplók nem elegendő a probléma elhárításához sikeres, a Storage ügyféloldali kódtára ügyféloldali naplók használatával vizsgálja meg a viselkedést az ügyfélalkalmazás és az eszközök, például a Fiddlert, a Wireshark és a Microsoft Az Üzenetelemző a hálózat vizsgálatára.

A Fiddler használatával kapcsolatos további információkért lásd: "[1. függelék: A HTTP és HTTPS-forgalom rögzítésére Fiddler segítségével]."

Wireshark használatával kapcsolatos további információkért lásd: "[2. függelékben: Wireshark használó rögzítheti a hálózati forgalom]."

A Microsoft Message Analyzer használatával kapcsolatos további információkért lásd: "[3. függelék: Microsoft Message Analyzert segítségével rögzítheti a hálózati forgalom]."

## <a name="appendices"></a>Mellékletek
A mellékletek számos olyan eszközt, amely akkor is hasznosnak diagnosztizálása és elhárítása az Azure Storage (és más szolgáltatások) írja le. Ezek az eszközök nem részei az Azure Storage, és a harmadik féltől származó termékekre. Mint ilyen, az eszközök ezen mellékletek tárgyalt minden olyan támogatási szerződés, előfordulhat, hogy a Microsoft Azure vagy az Azure Storage nem tartoznak, és ezért a kiértékelés folyamat részeként meg kell vizsgálni a licencelési és a támogatási lehetőségekről a a Ezek az eszközök szolgáltatók.

### <a name="appendix-1"></a>1. függelék: HTTP és HTTPS-forgalom rögzítése a Fiddler segítségével
[A fiddler](http://www.telerik.com/fiddler) hasznos eszköz a elemzése a HTTP és HTTPS-forgalom az ügyfélalkalmazást és a használ az Azure storage szolgáltatás között.

> [!NOTE]
> Fiddler-HTTPS-forgalmat; is dekódolást. olvassa el a Fiddlert dokumentáció alaposan megismerheti, hogyan teszi ezt, és biztonsági megszegéseinek.
> 
> 

Ebben a függelékben egy rövid útmutató bemutatja, hogyan konfigurálhatja a helyi számítógépen, amelyen telepítette a Fiddler és az Azure storage szolgáltatások közötti forgalom rögzítése a Fiddler.

A Fiddler rendelkezik elindítása, után megkezdődik a helyi gépen a HTTP és HTTPS-forgalom rögzítése. Az alábbiakban néhány hasznos parancsot a Fiddler szabályozni:

* Állítsa le és indítsa el a forgalom rögzítéséhez. Lépjen a főmenü **fájl** majd **forgalom rögzítése** Váltás a be- és kikapcsolhatja a rögzítés.
* Mentse a rögzített forgalmi adatait. Lépjen a főmenü **fájl**, kattintson a **mentése**, és kattintson a **minden munkamenet**: Ez lehetővé teszi, hogy a forgalom munkamenet archív fájlba mentése. Töltse be újra a munkamenet archív későbbi elemzés céljából, vagy küldje el, ha a rendszer kéri a Microsoft támogatási szolgálatához.

Korlátozza a forgalmat, amely a Fiddler rögzíti, konfigurált szűrőket is használhatja a **szűrők** fülre. Az alábbi képernyőfelvételen egy szűrőt, amely csak a küldött forgalmat rögzíti a **contosoemaildist.table.core.windows.net** storage-végpont:

![][5]

### <a name="appendix-2"></a>2. függelék: A hálózati forgalom rögzítésére Wireshark használatával
[Wireshark](http://www.wireshark.org/) van hálózati protokollelemző, amely lehetővé teszi, hogy számos különféle hálózati protokollok csomag részletes információinak megtekintése.

Az alábbi eljárás bemutatja, hogyan részletes csomagok információit a helyi számítógépről érkező forgalom rögzítésére amelyen Wireshark, a table service az Azure storage-fiókban.

1. Indítsa el a Wireshark a helyi gépen.
2. Az a **Start** területen válassza ki a helyi hálózati adapter vagy a felületek, amely csatlakozik az internethez.
3. Kattintson a **beállítások rögzítése**.
4. Adjon hozzá egy szűrőt a **szűrő rögzítése** szövegmezőbe. Például **contosoemaildist.table.core.windows.net gazdagép** Wireshark csak a table service-végpont által küldött és csomagok rögzítéséhez konfigurálja a **contosoemaildist** storage-fiókot. Tekintse meg a [rögzítése szűrők teljes listáját](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Kattintson a **Start**. Wireshark most rögzíti az összes csomagokat küldjön vagy fogadjon a table service-végpont használata során az ügyfélalkalmazást a helyi gépen.
6. Miután végzett, a fő menüben kattintson a **rögzítése** , majd **leállítása**.
7. A rögzített adatok mentéséhez Wireshark rögzítése egy fájlban, a fő menüjében kattintson **fájl** , majd **mentése**.

WireShark ki vannak emelve esetleges hibákat, amelyek szerepelnek a **packetlist** ablak. Is használhatja a **szakértő Info** ablak (kattintson **elemzés**, majd **szakértő Info**), hibák és figyelmeztetések összegzésének megtekintése.

![][7]

Azt is beállíthatja, az alkalmazási rétegre látja, kattintson a jobb gombbal a TCP adatokon, és válassza a TCP adatok megtekintéséhez **hajtsa végre a TCP-Stream**. Ez akkor hasznos, ha a memóriakép anélkül, hogy a rögzítési szűrő rögzített. További információkért lásd: [következő TCP-adatfolyamok](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Wireshark használatával kapcsolatos további információkért lásd: a [Wireshark felhasználói útmutató](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>3. függelék: A Microsoft Message Analyzer használatával rögzíti a hálózati forgalmat
A Microsoft Message Analyzer használatával a HTTP és HTTPS-forgalom rögzítése a Fiddler hasonló módon, és rögzítse a hálózati forgalmat a Wireshark hasonló módon.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>A Microsoft Message Analyzer használatával a webes nyomkövetési munkamenet konfigurálása
Microsoft Message Analyzert, futtassa a Microsoft Message Analyzert alkalmazást használó HTTP és HTTPS-forgalom, majd a webes nyomkövetési munkamenet konfigurálása a **fájl** menüben kattintson a **rögzítési/nyomkövetési**. Rendelkezésre álló nyomkövetési forgatókönyvek listájából válassza ki **webalkalmazás-Proxy**. Ezt a a **nyomkövetési forgatókönyv-konfiguráció** panel a **HostnameFilter** szövegmező, vegye fel a neveket a tárolási végpontok (ezekkel a nevekkel kereshet a [az Azure portal](https://portal.azure.com)). Például, ha az Azure storage-fiók neve **contosodata**, hozzá kell adnia a következőt a **HostnameFilter** szövegbeviteli mező:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Szóköz karakter választja el gazdanévvel.
> 
> 

Ha felkészült a nyomkövetési adatok gyűjtésének megkezdéséhez kattintson a **Start With** gombra.

További információ a Microsoft Message Analyzert **webalkalmazás-Proxy** nyomon követése, lásd: [Microsoft-PEF-WebProxy szolgáltató](http://technet.microsoft.com/library/jj674814.aspx).

A beépített **webalkalmazás-Proxy** Fiddler alapján a Microsoft Message Analyzert nyomkövetési; ügyféloldali HTTPS-forgalom rögzítése és nem titkosított HTTPS üzenetek megjelenítéséhez. A **webalkalmazás-Proxy** nyomkövetési működik az összes HTTP és HTTPS-forgalom, amely hozzáférést biztosít, nem titkosított üzeneteket helyi proxy konfigurálásával.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>A Microsoft Message Analyzer használatával hálózati problémák diagnosztizálása
A Microsoft Message Analyzert használata mellett **webalkalmazás-Proxy** nyomkövetés rögzítése a HTTP/HTTPs-forgalmat, az ügyfélalkalmazás és a storage szolgáltatás közötti részleteit, használhatja a beépített **helyi kapcsolat réteg**  nyomkövetés rögzítése a hálózati csomagok információit. Ez lehetővé teszi, hogy például a hálózati adatok, amelyet a Wireshark rögzítése, és diagnosztizálhatja a hasonló problémák az eldobott csomagok.

Az alábbi képernyőfelvételen szereplő példán látható **helyi kapcsolat réteg** egyes nyomkövetési **tájékoztató** állapotüzenetek a **DiagnosisTypes** oszlop. A megfelelő ikonra kattintva a **DiagnosisTypes** oszlop az üzenet részleteit jeleníti meg. Ebben a példában a kiszolgáló üzenet #305 újraküldött, mert az ügyfél nem kapott nyugtázást:

![][9]

A nyomkövetési munkamenet létrehozásakor a Microsoft Message Analyzert Rövidítse le a lényeget a nyomkövetés a szűrőket is megadhat. Az a **rögzítése / nyomkövetési** oldal, ahol megadhatja a nyomkövetést, kattintson a a **konfigurálása** mellett kapcsolni **Microsoft-Windows-kisméretű NDIS-PacketCapture**. Az alábbi képernyőfelvételen egy konfigurációt, amely három tárolási szolgáltatások IP-címét a TCP-forgalom szűrése:

![][10]

A Microsoft Message Analyzer helyi kapcsolat réteg nyomkövetési kapcsolatos további információkért lásd: [Microsoft-PEF-kisméretű NDIS-PacketCapture szolgáltató](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>4. függelék: Az Excel használatával megtekintheti a metrikákat, és naplózási adatok
Számos eszköz engedélyezése, hogy töltse le a Storage Metrics data tagolt formátumú, amely megkönnyíti az adatok betöltése az Excel megtekintése és elemzése az Azure table storage-ból. Storage naplózási adatok Azure blob storage-ból már betöltheti az Excelbe tagolt formátumú. Azonban szüksége lesz adja hozzá a megfelelő oszlopfejlécek található információk alapján [Storage Analytics naplóformátum](http://msdn.microsoft.com/library/azure/hh343259.aspx) és [Storage Analytics mérőszámainak Táblasémáját](http://msdn.microsoft.com/library/azure/hh343264.aspx).

A blob storage-ból való letöltése után a Storage naplózási adatok importálása Excelbe:

* Az a **adatok** menüben kattintson a **származó szöveges**.
* Keresse meg a naplófájlt megtekintheti, és kattintson a kívánt **importálás**.
* Az 1. lépésében a **varázslóban**válassza **tagolt**.

Az 1. lépésében a **szöveg importálása varázsló**válassza **pontosvesszővel** csak elválasztóként, idézőjel, majd a **Szöveghatároló**. Kattintson a **Befejezés** és elhelyezése az adatok a munkafüzet kiválasztása.

### <a name="appendix-5"></a>5. függelék: Monitorování pomocí Application Insights a Visual Studio Team Serviceshez
Az Application Insights szolgáltatást a Visual Studio Team Services a teljesítmény és rendelkezésre állásának figyelésére szolgáló részeként is használnia. Ez az eszköz a következőket teheti:

* Ellenőrizze, hogy a web service elérhető és válaszkész. Legyen az alkalmazás egy webhely vagy egy eszköz alkalmazást, amely egy webszolgáltatás, néhány perces időközönként URL ellenőrzése a világ különböző pontjain található helyekről, és értesíti, ha probléma van.
* Gyorsan diagnosztizálhatja a teljesítménnyel kapcsolatos problémák vagy a kivételek, a web service-ben. Tudja meg, ha a Processzorhasználat vagy egyéb erőforrásokat is nyújtja, hívásláncokat kivételek, és könnyedén kereshet a nyomkövetési naplók. Ha az alkalmazás teljesítmény elfogadható keretek alá csökken, a Microsoft küldheti el Önnek egy e-mailt. .NET-hez és a Java webes szolgáltatásokat is figyelhet.

További információt talál [Mi az Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Bevezetés]: #introduction
[Hogyan vannak rendszerezve Ez az útmutató]: #how-this-guide-is-organized

[A storage szolgáltatás figyelése]: #monitoring-your-storage-service
[Szolgáltatás állapotának monitorozása]: #monitoring-service-health
[Monitorozási kapacitás]: #monitoring-capacity
[Rendelkezésre állás figyelése]: #monitoring-availability
[Teljesítmény figyelése]: #monitoring-performance

[Tárolási problémák diagnosztizálása]: #diagnosing-storage-issues
[A szolgáltatás állapotbeli problémák]: #service-health-issues
[Teljesítménnyel kapcsolatos problémák]: #performance-issues
[Hibák diagnosztizálása]: #diagnosing-errors
[Storage emulator kapcsolatos problémák]: #storage-emulator-issues
[Storage naplózási eszközök]: #storage-logging-tools
[Hálózati naplózási eszközök használatával]: #using-network-logging-tools

[Teljes körű nyomkövetés]: #end-to-end-tracing
[Naplózási adatok korrelálása]: #correlating-log-data
[Ügyfélkérés azonosítója]: #client-request-id
[Kiszolgálói kérelem azonosítója]: #server-request-id
[Időbélyegek]: #timestamps

[Hibaelhárítási útmutató]: #troubleshooting-guidance
[A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok magas AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageServerLatency
[Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]: #you-are-experiencing-unexpected-delays-in-message-delivery

[A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]: #metrics-show-an-increase-in-PercentThrottlingError
[Átmeneti növekedése percentthrottlingerror értéket mutatnak]: #transient-increase-in-PercentThrottlingError
[Véglegesen növeli a hibás percentthrottlingerror értéket mutatnak]: #permanent-increase-in-PercentThrottlingError
[A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]: #metrics-show-an-increase-in-PercentTimeoutError
[A mérőszámok emelkedő PercentNetworkError értéket mutatnak]: #metrics-show-an-increase-in-PercentNetworkError

[Az ügyfél fogad üzeneteket HTTP 403 (tiltott)]: #the-client-is-receiving-403-messages
[Az ügyfél fogad üzeneteket HTTP 404 (nem található)]: #the-client-is-receiving-404-messages
[Az ügyfél vagy egy másik folyamat korábban törölték az objektumot]: #client-previously-deleted-the-object
[Egy közös hozzáférésű Jogosultságkód (SAS) hitelesítési hiba]: #SAS-authorization-issue
[Ügyféloldali JavaScript-kód nincs engedélye az objektumhoz való hozzáférést]: #JavaScript-code-does-not-have-permission
[Hálózati hiba]: #network-failure
[Az ügyfél HTTP 409 (Ütközés) üzeneteket fogad]: #the-client-is-receiving-409-messages

[Metrika alacsony PercentSuccess vagy analytics naplóbejegyzések rendelkezik ClientOtherErrors működésére a tranzakció állapota]: #metrics-show-low-percent-success
[Teljesítmény-mérőszámait váratlan növekedését megjelenítése a tárolási kapacitás-használat]: #capacity-metrics-show-an-unexpected-increase
[A probléma merül fel a fejlesztési vagy tesztelési a storage emulator használatával]: #your-issue-arises-from-using-the-storage-emulator
[A storage emulator az "X" funkció nem működik]: #feature-X-is-not-working
[Hiba történt "az egyik a HTTP-fejlécek értéke nem a megfelelő formátumban" a storage emulator használata esetén]: #error-HTTP-header-not-correct-format
[A storage emulator futtatása rendszergazdai jogosultságra van szükség]: #storage-emulator-requires-administrative-privileges
[.NET-keretrendszerhez készült Azure SDK telepítése problémák áll kapcsolatban.]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Van egy másik probléma storage szolgáltatással]: #you-have-a-different-issue-with-a-storage-service

[Mellékletek]: #appendices
[1. függelék: A HTTP és HTTPS-forgalom rögzítésére Fiddler segítségével]: #appendix-1
[2. függelékben: Wireshark használó rögzítheti a hálózati forgalom]: #appendix-2
[3. függelék: Microsoft Message Analyzert segítségével rögzítheti a hálózati forgalom]: #appendix-3
[4. függelék: Segítségével Excel metrikák megtekintése és az adatok naplózása]: #appendix-4
[5. függelék: A Visual Studio Team Services az Application insights szolgáltatással figyelését.]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
