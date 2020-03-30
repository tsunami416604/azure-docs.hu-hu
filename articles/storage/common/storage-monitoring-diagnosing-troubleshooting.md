---
title: Az Azure Storage figyelése, diagnosztizálása és hibaelhárítása | Microsoft dokumentumok
description: Az Azure Storage-kezeléssel kapcsolatos problémák azonosításához, diagnosztizálásához és elhárításához olyan funkciókat használhat, mint a tárolási elemzés, az ügyféloldali naplózás és más külső eszközök.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3d5f3ade3ef3b79ddb3996b5bf2d609b11aff8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255963"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Áttekintés
A felhőalapú környezetben üzemeltetett elosztott alkalmazások ban felmerülő problémák diagnosztizálása és elhárítása összetettebb lehet, mint a hagyományos környezetekben. Az alkalmazások paaS- vagy IaaS-infrastruktúrában, a helyszínen, a mobileszközön vagy ezek a környezetek valamilyen kombinációjában telepíthetők. Az alkalmazás hálózati forgalma általában nyilvános és magánhálózatokon is áthaladhat, és az alkalmazás más adattárakmellett több tárolási technológiát is használhat, például Microsoft Azure storage-táblákat, blobokat, várólistákat vagy fájlokat. és dokumentumadatbázisok.

Az ilyen alkalmazások sikeres kezeléséhez proaktív módon figyelje őket, és ismerje meg, hogyan diagnosztizálhatja és háríthatja el azok és függő technológiáik minden aspektusát. Az Azure Storage-szolgáltatások felhasználójaként folyamatosan figyelnie kell az alkalmazás által a viselkedés váratlan változásaira (például a szokásosnál lassabb válaszidőkre) használt tárolási szolgáltatásokat, és a naplózást használva részletesebb adatokat gyűjthet, és elemezheti a problémát Mélység. A figyelési és naplózási diagnosztikai információk segítenek meghatározni az alkalmazás által észlelt probléma kiváltó okát. Ezután elháríthatja a problémát, és meghatározhatja a javításhoz szükséges lépéseket. Az Azure Storage egy alapvető Azure-szolgáltatás, és fontos részét képezi az ügyfelek által az Azure-infrastruktúrára telepített megoldások többségének. Az Azure Storage olyan funkciókat tartalmaz, amelyek egyszerűsítik a figyelést, a diagnosztizálást és a tárolási problémák elhárítását a felhőalapú alkalmazásokban.

> [!NOTE]
> Az Azure Files jelenleg nem támogatja a naplózást.
>

Az Azure Storage-alkalmazások végpontok között történő hibaelhárításának gyakorlati útmutatóját az [Azure Storage-metrikák és naplózás, az AzCopy és az Üzenetelemző használatával történő összes hibaelhárítás](../storage-e2e-troubleshooting.md)című témakörben talál.

* [Bevezetés]
  * [Az útmutató rendszerezése]
* [A tárolási szolgáltatás figyelése]
  * [A szolgáltatás állapotának figyelése]
  * [Ellenőrzési kapacitás]
  * [Elérhetőség figyelése]
  * [A teljesítmény figyelése]
* [Tárolási problémák diagnosztizálása]
  * [A szolgáltatások állapotproblémáival kapcsolatos problémák]
  * [Teljesítményproblémák]
  * [Hibák diagnosztizálása]
  * [Tárolási emulátor problémák]
  * [Tárolónaplózási eszközök]
  * [Hálózati naplózási eszközök használata]
* [Végpontok között kontúrozás]
  * [Naplóadatok korrelációja]
  * [Ügyfélkérelem azonosítója]
  * [Kiszolgálókérés azonosítója]
  * [Időbélyegek]
* [Hibaelhárítási útmutató]
  * [A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]
  * [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
  * [A mérőszámok magas AverageServerLatency értéket mutatnak]
  * [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]
  * [A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]
  * [A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]
  * [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]
  * [Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap]
  * [Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap]
  * [Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap]
  * [A metrikák alacsony Százalékos sikervagy elemzési naplóbejegyzések ügyfélhiba-állapotú tranzakciós állapotú műveleteket tartalmaznak.]
  * [A kapacitásmetrikák a tárolási kapacitás használatának váratlan növekedését mutatják]
  * [Az Ön problémája abból ered, hogy a tároló emulátort fejlesztési vagy tesztelési célokra]
  * [Problémákat tapasztal a .NET-hez tartozó Azure SDK telepítése során]
  * [Más probléma van egy tárolási szolgáltatással]
  * [Virtuális virtuális gépek virtuális gépeken a Virtuális Hálózatba nem (VD- k) hibáinak elhárítása](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Vhd-k hibaelhárítása Linux os virtuális gépeken](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Az Azure Files Windows rendszerrel kapcsolatos problémáinak elhárítása](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Az Azure Files linuxos problémáinak elhárítása](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Függelékek]
  * [1. függelék: A Hegedűs használata http- és HTTPS-forgalom rögzítésére]
  * [2. függelék: Wireshark használata a hálózati forgalom rögzítésére]
  * [3. függelék: A Microsoft Message Analyzer használata a hálózati forgalom rögzítésére]
  * [4. függelék: Az Excel használata a mérőszámok és a naplóadatok megtekintéséhez]
  * [5. függelék: Figyelés az Azure DevOps alkalmazáselemzési adataival]

## <a name="introduction"></a><a name="introduction"></a>Bevezetés
Ez az útmutató bemutatja, hogyan használhatja az olyan funkciókat, mint az Azure Storage Analytics, az ügyféloldali naplózás az Azure Storage-ügyfélkönyvtárban, és más külső eszközök az Azure Storage-ral kapcsolatos problémák azonosítására, diagnosztizálására és elhárítására.

![][1]

Ezt az útmutatót elsősorban az Azure Storage Services szolgáltatást használó online szolgáltatások fejlesztői és az ilyen online szolgáltatások kezeléséért felelős informatikai szakemberek olvassák. Az útmutató céljai a következők:

* Az Azure Storage-fiókok állapotának és teljesítményének megőrzése érdekében.
* Az Ön számára a szükséges folyamatokat és eszközöket, hogy segítsen eldönteni, hogy egy kérdés vagy probléma egy alkalmazás kapcsolódik az Azure Storage.
* Az Azure Storage-szal kapcsolatos problémák megoldásához.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Az útmutató rendszerezése
A "[A tárolási szolgáltatás figyelése]" szakasz ismerteti, hogyan figyelheti az Azure Storage-szolgáltatások állapotát és teljesítményét az Azure Storage Analytics metrikák (tárolási metrikák) használatával.

A "[A tárolási problémák diagnosztizálása]" szakasz leírja, hogyan diagnosztizálhatja a problémákat az Azure Storage Analytics-naplózás (Storage Logging) használatával. Azt is ismerteti, hogyan engedélyezheti az ügyféloldali naplózást az ügyfélkódtárak egyikének, például a Storage Client Library for .NET vagy az Azure SDK java-hoz.

A "[Végpontok utáni nyomkövetés]" szakasz azt ismerteti, hogyan lehet korrelálni a különböző naplófájlokban és metrikákban található információkat.

A "[Hibaelhárítási útmutató]" című szakasz hibaelhárítási útmutatást nyújt a tárolással kapcsolatos gyakori problémákhoz.

A "[függelékek]" információkat tartalmaznak arról, hogy más eszközöket, például a Wireshark-ot és a Netmon-t használják a hálózati csomagadatok elemzéséhez, a Fiddler-t a HTTP/HTTPS üzenetek elemzéséhez, és a Microsoft Message Analyzer-t a naplóadatok korrelációjára.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>A tárolási szolgáltatás figyelése
Ha ismeri a Windows teljesítményfigyelés, azt gondolhatja, hogy a tárolási metrikák, mint egy Azure Storage egyenértékű Windows Performance Monitor számlálók. A Storage Metrics,talál egy átfogó metrikák (számlálók a Windows Teljesítményfigyelő terminológiája), például a szolgáltatás rendelkezésre állása, a kérelmek teljes száma a szolgáltatásra, vagy százalékos sikeres kérelmek szolgáltatásra. Az elérhető metrikák teljes listáját a [Storage Analytics Metrics táblasémája című témakörben láthatja.](https://msdn.microsoft.com/library/azure/hh343264.aspx) Megadhatja, hogy a storage szolgáltatás óránként vagy percenként gyűjtse és összesítse a metrikákat. A metrikák engedélyezéséről és a tárfiókok figyeléséről a [Tárolási metrikák engedélyezése és a metrikák adatainak megtekintése](https://go.microsoft.com/fwlink/?LinkId=510865)című témakörben talál további információt.

Kiválaszthatja, hogy mely óránkénti metrikákat szeretné megjeleníteni az [Azure Portalon,](https://portal.azure.com) és konfigurálhatja azokat a szabályokat, amelyek e-mailben értesítik a rendszergazdákat, ha egy óránkénti metrika túllép egy adott küszöbértéket. További információt a [Riasztási értesítések fogadása című témakörben talál.](/azure/monitoring-and-diagnostics/monitoring-overview-alerts)

Azt javasoljuk, hogy tekintse át [az Azure Monitor tárhely (előzetes](../../azure-monitor/insights/storage-insights-overview.md) verzió). Az Azure Monitor szolgáltatása az Azure Storage-fiókok átfogó figyelését kínálja az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes nézetével. Nem igényel semmit engedélyezni vagy konfigurálni, és ezeket a mutatókat azonnal megtekintheti az előre definiált interaktív diagramokból és más képi megjelenítésekből.

A tárolási szolgáltatás a metrikákat a legjobb erőfeszítéssel gyűjti, de előfordulhat, hogy nem rögzít minden tárolási műveletet.

Az Azure Portalon megtekintheti a metrikák, például a rendelkezésre állás, az összes kérelem és a tárfiók átlagos késési számait. Egy értesítési szabály is be van állítva, hogy figyelmeztesse a rendszergazdát, ha a rendelkezésre állás egy bizonyos szint alá csökken. Ezeknek az adatoknak a megtekintésétől az egyik lehetséges vizsgálati terület az, hogy a table service sikerességi aránya 100% alatt van (további információkért lásd a "[Metrikák alacsony Százalékos sikerértéket mutatnak, vagy az elemzési napló bejegyzések a ClientOtherErrors tranzakciós állapotú műveleteket tartalmaznak").]

Folyamatosan figyelje az Azure-alkalmazásokat annak érdekében, hogy azok kifogástalanállapotúak legyenek, és a várt módon teljesítenek:You should continuously monitor your Azure applications to ensure they are healthy and performing by expected by:

* Az alkalmazás néhány alapmetrika létrehozása, amely lehetővé teszi az aktuális adatok összehasonlítását és az Azure storage és az alkalmazás viselkedésében bekövetkező jelentős változások azonosítását. Az alapmutatók értékei sok esetben alkalmazásspecifikusak lesznek, és az alkalmazás teljesítménytesztelése során létre kell hoznia azokat.
* Percmetrikák rögzítése, és használja őket a váratlan hibák és rendellenességek, például a hibaszám vagy a kérelem aránya aktívfigyelése.
* Óránkénti metrikák rögzítése, és azok használatával az átlagos értékek, például az átlagos hibaszámok és a kérelmek aránya.
* A diagnosztikai eszközök használatával kapcsolatos lehetséges problémák vizsgálata a "[Tárolási problémák diagnosztizálása]" című szakasz későbbi részében tárgyaltak.

Az alábbi képen látható diagramok azt mutatják be, hogy az óránkénti metrikáknál előforduló átlagolás hogyan rejtheti el a tevékenység csúcsait. Az óránkénti metrikák úgy tűnik, hogy a kérelmek állandó ütemét mutatják, míg a perc metrikák mutatják az ingadozásokat, amelyek valóban zajlanak.

![][3]

A szakasz további része azt ismerteti, hogy milyen metrikákat kell figyelnie, és miért.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>A szolgáltatás állapotának figyelése
Az Azure [Portal](https://portal.azure.com) segítségével megtekintheti a Storage szolgáltatás (és más Azure-szolgáltatások) állapotát a világ összes Azure-régiójában. Figyelés lehetővé teszi, hogy azonnal megtekintheti, ha egy probléma kívül a vezérlő hatással van a storage szolgáltatás az alkalmazáshoz használt régióban.

Az [Azure Portal](https://portal.azure.com) is értesítéseket biztosít a különböző Azure-szolgáltatásokat érintő incidensek.
Megjegyzés: Ez az információ korábban elérhető volt az [Azure Service Irányítópultján](https://status.azure.com)a korábbi adatokkal együtt.

Míg az [Azure Portal](https://portal.azure.com) egészségügyi adatokat gyűjt az Azure-adatközpontok (in-belül figyelés), érdemes lehet egy külső megközelítést szintetikus tranzakciók, amelyek rendszeresen hozzáférhetnek az Azure-üzemeltetett webalkalmazás több helyről. A [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) és az Application Insights for Azure DevOps által kínált szolgáltatások példák erre a megközelítésre. Az Azure DevOps alkalmazáselemzési adatairól további információt a "[5. függelék: Figyelés az Azure DevOps alkalmazáselemzésekkel című függelékében talál."](#appendix-5)

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Ellenőrzési kapacitás
A storage metrikái csak a blobszolgáltatás kapacitásmetrikáit tárolja, mert a blobok általában a tárolt adatok legnagyobb részét teszik ki (az írás időpontjában nem lehet a storage metrikák használatával figyelni a táblák és várólisták kapacitását). Ezeket az adatokat a **$MetricsCapacityBlob** táblában találja, ha engedélyezte a Blob szolgáltatás figyelését. A Storage Metrics naponta egyszer rögzíti ezeket az adatokat, és a **RowKey** értékével meghatározhatja, hogy a sor tartalmaz-e felhasználói adatokhoz **(értékadatokhoz)** vagy elemzési adatokhoz (értékelemzés) kapcsolódó **entitást.** Minden tárolt entitás információkat tartalmaz a felhasznált tárterület **(kapacitás** bájtban mérve) és az aktuális számú tárolók (**ContainerCount**) és blobok (**ObjectCount**) használatos a tárfiókban. A **$MetricsCapacityBlob** táblázatban tárolt kapacitásmérőkről a [Storage Analytics Metrics táblaséma](https://msdn.microsoft.com/library/azure/hh343264.aspx)című témakörben talál további információt.

> [!NOTE]
> Ezeket az értékeket egy korai figyelmeztetés, hogy közeledik a kapacitáskorlátokat a tárfiók. Az Azure Portalon riasztási szabályokat adhat hozzá, amelyek értesítik, ha az összesített tárhelyhasználat meghaladja vagy a megadott küszöbértékek alá esik.
>
>

A különböző tárolóobjektumok, például a blobok méretének becsléséhez tekintse meg az [Azure Storage számlázásának – sávszélesség, tranzakciók és kapacitás – című](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)blogbejegyzést.

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Elérhetőség figyelése
Figyeld meg a tárfiókban lévő társzolgáltatások elérhetőségét az óránkénti vagy percmérők táblázatainak **Rendelkezésre állásoszlopában** lévő érték figyelésével – **$MetricsHourPrimaryTransactionsBlob,** **$MetricsHourPrimaryTransactionsTable,** **$MetricsHourPrimaryTransactionsQueue,** **$MetricsMinutePrimaryTransactionsBlob,** **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue** **, és MetricsCapacityBlob**. A **rendelkezésre állás oszlop** tartalmaz egy százalékos értéket, amely jelzi a szolgáltatás rendelkezésre állását vagy a sor által képviselt API-műveletet (a **RowKey** megmutatja, hogy a sor a szolgáltatás egészére vagy egy adott API-műveletre vonatkozó metrikákat tartalmaz-e).

A 100%-nál kisebb értékek azt jelzik, hogy egyes tárolási kérelmek sikertelenek. Láthatja, hogy miért nem sikerült, ha megvizsgálja a metrikák adatainak egyéb oszlopait, amelyek a különböző hibatípusokkal rendelkező kérelmek, például a **ServerTimeoutError**számát jelenítik meg. A **rendelkezésre állás** átmenetileg 100% alá csökken, például átmeneti kiszolgálói időtúllépések miatt, miközben a szolgáltatás a partíciókat jobb terheléselosztási kérelemre helyezi át; az ügyfélalkalmazás újrapróbálkozási logikájának kezelnie kell az ilyen időszakos feltételeket. A [cikk Storage Analytics naplózott műveletek és állapotüzenetek](https://msdn.microsoft.com/library/azure/hh343260.aspx) felsorolja a tranzakciótípusokat, amelyek tárolási metrikák tartalmazza a **rendelkezésre állási** számítás.

Az [Azure Portalon](https://portal.azure.com)riasztási szabályokat adhat hozzá, amelyek értesítik, ha egy szolgáltatás **rendelkezésre állása** a megadott küszöbérték alá esik.

Az útmutató "[Hibaelhárítási útmutató]" című szakasza a rendelkezésre állással kapcsolatos gyakori tárolási szolgáltatási problémákat ismerteti.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>A teljesítmény figyelése
A tárolási szolgáltatások teljesítményének figyeléséhez használhatja a következő metrikák az óránkénti és perc metrikák táblák.

* Az **AverageE2ELatency** és az **AverageServerLatency** oszlopok értékei azt mutatják, hogy a storage-szolgáltatás vagy az API-művelettípus átlagosan milyen időt vesz igénybe a kérelmek feldolgozásához. **Az AverageE2ELatency** a végpontok késésének mérőszáma, amely magában foglalja a kérés olvasásához és a válasz elküldéséhez szükséges időt a kérelem feldolgozásához szükséges idő mellett (ezért magában foglalja a hálózati késést, amint a kérés eléri a tárolási szolgáltatást); **Az AverageServerLatency** csak a feldolgozási idő mértéke, ezért kizárja az ügyféllel való kommunikációhoz kapcsolódó hálózati késést. Lásd a "[Metrikák megjelenítése magas AverageE2ELatency és alacsony AverageServerLatency]" című szakaszban az útmutató későbbi részében, hogy megvitathassa, miért lehet jelentős különbség a két érték között.
* A **TotalIngress** és a **TotalEgress** oszlopok értékei a storage-szolgáltatásba érkező és az azt kifelé érkező adatok teljes mennyiségét jelenítik meg bájtban, vagy egy adott API-művelettípuson keresztül.
* A **TotalRequests** oszlopban szereplő értékek az API-művelet tárolási szolgáltatása által fogadott kérelmek teljes számát mutatják. **TotalRequests** a storage szolgáltatás által fogadott kérelmek teljes száma.

Általában figyelni fogja a váratlan változásokat ezen értékek bármelyikében, mint egy mutató, hogy van egy probléma, amely vizsgálatot igényel.

Az [Azure Portalon](https://portal.azure.com)riasztási szabályokat adhat hozzá, amelyek értesítik, ha a szolgáltatás teljesítménymutatói nak bármelyike a megadott küszöbérték alá esik, vagy meghaladja azt.

Az útmutató "[Hibaelhárítási útmutató]" című szakasza a szolgáltatás teljesítményével kapcsolatos gyakori problémákat ismerteti.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Tárolási problémák diagnosztizálása
Az alkalmazásban számos módon tudhatja meg a problémát vagy problémát, többek között:

* Olyan súlyos hiba, amely az alkalmazás összeomlását vagy leállását okozza.
* A figyelt metrikák alapértékeinek jelentős változása a "[A tárolási szolgáltatás figyelése]" című szakaszban leírtak szerint.
* Az alkalmazás felhasználóinak jelentései arról, hogy bizonyos műveletek nem a várt módon fejeződtek be, vagy hogy néhány szolgáltatás nem működik.
* Az alkalmazáson belül létrehozott hibák, amelyek a naplófájlokban vagy más értesítési módszerrel jelennek meg.

Az Azure storage-szolgáltatásokkal kapcsolatos problémák általában a következő négy nagy kategória egyikébe tartoznak:

* Az alkalmazás teljesítményproblémája van, vagy a felhasználók által jelentett, vagy a teljesítménymutatók változásai.
* Probléma van az Azure Storage-infrastruktúrával egy vagy több régióban.
* Az alkalmazás hibát észlel, vagy a felhasználók által jelentett, vagy a figyelési hibák számának növekedése.
* A fejlesztés és a tesztelés során a helyi tároló emulátort használja; előfordulhat, hogy olyan problémákkal szembesül, amelyek kifejezetten a tároló emulátor használatával kapcsolatosak.

A következő szakaszok ismertetik azokat a lépéseket, amelyeket a négy kategória problémáinak diagnosztizálásához és elhárításához kell végrehajtania. Az útmutató későbbi részében található "[Hibaelhárítási útmutató]" című szakasz részletesebben ismerteti az esetleg előforduló gyakori problémákat.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>A szolgáltatások állapotproblémáival kapcsolatos problémák
A szolgáltatás állapotával kapcsolatos problémák általában kívül esnek a szabályozáson. Az [Azure Portal](https://portal.azure.com) az Azure-szolgáltatásokkal, köztük a tárolási szolgáltatásokkal kapcsolatos problémákkal kapcsolatos információkat tartalmazza. Ha a tárfiók létrehozásakor az olvasási szintű georedundáns tárolást választotta, akkor ha az adatok elérhetetlenné válnak az elsődleges helyen, az alkalmazás ideiglenesen átválthat a másodlagos helyen lévő csak olvasható példányra. A másodlagos olvasáshoz az alkalmazásnak képesnek kell lennie az elsődleges és a másodlagos tárolóhelyek közötti váltásra, és képesnek kell lennie az írásvédett adatokkal csökkentett üzemmódban való munkára. Az Azure Storage-ügyfélkódtárak lehetővé teszik, hogy olyan újrapróbálkozási szabályzatot határozzon meg, amely a másodlagos tárolóból is olvasható, ha az elsődleges tárolóból történő olvasás sikertelen. Az alkalmazás nak is tisztában kell lennie azzal, hogy a másodlagos helyen lévő adatok végül konzisztensek. További információt az Azure [Storage redundanciabeállításai és az olvasási szintű georedundáns tárolás című blogbejegyzésben talál.](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)

### <a name="performance-issues"></a><a name="performance-issues"></a>Teljesítményproblémák
Egy alkalmazás teljesítményének megítélése szubjektív lehet, főképp a felhasználó szemszögéből. Ezért fontos, hogy rendelkezzen olyan alapmértékekkel, amelyek segíthetnek a teljesítménnyel kapcsolatos problémák azonosításában. Számos tényező befolyásolhatja az Azure storage-szolgáltatás teljesítményét az ügyfélalkalmazás szempontjából. Ezek a tényezők működhetnek a tárolási szolgáltatásban, az ügyfélben vagy a hálózati infrastruktúrában; ezért fontos, hogy legyen egy stratégia a teljesítménykérdés eredetének azonosítására.

Miután azonosította a teljesítményprobléma okának valószínű helyét a mérőszámokból, a naplófájlok segítségével részletes információkat találhat a probléma további diagnosztizálására és elhárítására.

Az útmutató későbbi , "[Hibaelhárítási útmutató]" című szakasza további információt nyújt a teljesítménysel kapcsolatos gyakori problémákról.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Hibák diagnosztizálása
Az alkalmazás felhasználói értesíthetik önt az ügyfélalkalmazás által jelentett hibákról. A storage metrics a tárolási szolgáltatásokból , például a **NetworkError,** **a ClientTimeoutError**vagy az **AuthorizationError**szolgáltatásból származó különböző hibatípusok számát is rögzíti. Míg a Storage Metrics csak rekordok száma a különböző hibatípusok, az egyes kérelmek részletesebben is megfigyelheti a kiszolgálóoldali, ügyfél-oldali és hálózati naplók. A storage szolgáltatás által visszaadott HTTP-állapotkód általában jelzi, hogy miért nem sikerült a kérés.

> [!NOTE]
> Ne feledje, hogy bizonyos időszakos hibákra számíthat: például átmeneti hálózati feltételek vagy alkalmazáshibák miatti hibákra.
>
>

Az alábbi erőforrások hasznosak a tárolási szolgáltatásokkal kapcsolatos állapot- és hibakódok megértéséhez:

* [Gyakori REST API-hibakódok](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [A Blob Service hibakódjai](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Várólista-szolgáltatás hibakódjai](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Táblaszolgáltatás hibakódjai](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Fájlszolgáltatás hibakódjai](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Tárolási emulátor problémák
Az Azure SDK tartalmaz egy tárolási emulátorfuttatható egy fejlesztési munkaállomáson. Ez az emulátor az Azure storage-szolgáltatások legtöbb viselkedését szimulálja, és a fejlesztés és a tesztelés során hasznos, lehetővé téve az Azure storage-szolgáltatásokat használó alkalmazások futtatását anélkül, hogy Azure-előfizetésre és Egy Azure-tárfiókra lenne szükség.

Az útmutató "[Hibaelhárítási útmutató]" című szakasza a tárolóemulátor használatával tapasztalt néhány gyakori problémát ismerteti.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Tárolónaplózási eszközök
A Storage Logging kiszolgálóoldali naplózást biztosít az Azure storage-fiókjában. A kiszolgálóoldali naplózás engedélyezéséről és a naplóadatok eléréséről a [Tárolónaplózás engedélyezése és](https://go.microsoft.com/fwlink/?LinkId=510867)a naplóadatok elérése című témakörben talál további információt.

A .NET tárolóügyfél-tár lehetővé teszi az alkalmazás által végrehajtott tárolási műveletekhez kapcsolódó ügyféloldali naplóadatok gyűjtését. További információt a [.NET-es Storage-ügyfélkódtárral történő ügyféloldali naplózást](https://go.microsoft.com/fwlink/?LinkId=510868) ismertető szakaszban találhat.

> [!NOTE]
> Bizonyos körülmények között (például a SAS engedélyezési hibák), a felhasználó jelenthet egy hibát, amely nem talál kérésadatokat a kiszolgálóoldali tárolási naplókban. A Storage Client Library naplózási funkcióival megvizsgálhatja, hogy a probléma oka az ügyfélen van-e, vagy a hálózat figyelőeszközeivel vizsgálhatja a hálózatot.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Hálózati naplózási eszközök használata
Rögzítheti az ügyfél és a kiszolgáló közötti forgalmat, hogy részletes információkat szolgáltasson az ügyfél és a kiszolgáló által kicserélt adatokról és az alapul szolgáló hálózati feltételekről. A hasznos hálózati naplózási eszközök a következők:

* [Fiddler](https://www.telerik.com/fiddler) egy ingyenes webes hibakeresési proxy, amely lehetővé teszi, hogy vizsgálja meg a fejlécek és hasznos adatok http és HTTPS kérés és válasz üzeneteket. További információ: [1. függelék: A Hegedűs használata http- és HTTPS-forgalom rögzítésére](#appendix-1).
* [A Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) és [a Wireshark](https://www.wireshark.org/) ingyenes hálózati protokollelemzők, amelyek lehetővé teszik a hálózati protokollok széles körének részletes csomaginformációinak megtekintését. További információ a Wireshark, lásd: "[Függelék 2: A Wireshark segítségével elfog hálózati forgalom](#appendix-2)".
* A Microsoft Message Analyzer a Microsoft olyan eszköze, amely felváltja a Netmon-t, és a hálózati csomagadatok rögzítése mellett segít a más eszközökből rögzített naplóadatok megtekintésében és elemzésében. További információt a "[3.](#appendix-3)
* Ha azt szeretné, hogy egy alapvető kapcsolódási tesztet annak ellenőrzésére, hogy az ügyfélgép csatlakozhat az Azure storage szolgáltatás a hálózaton keresztül, nem teheti meg ezt az ügyfél szabványos **ping** eszköz használatával. A [ **tcping** eszközzel](https://www.elifulkerson.com/projects/tcping.php) azonban ellenőrizheti a kapcsolatot.

Sok esetben a storage-naplózásból és a tárolóügyfél-tárból származó naplóadatok elegendőek lesznek a probléma diagnosztizálásához, de bizonyos esetekben szükség lehet a hálózati naplózási eszközök által nyújtott részletesebb információkra. A Http- és HTTPS-üzenetek megtekintéséhez például a Fiddler segítségével megtekintheti a tárolószolgáltatásokba és onnan küldött fejléc- és hasznos adatadatokat, ami lehetővé teszi annak vizsgálatát, hogy egy ügyfélalkalmazás hogyan próbálja meg újraa tárolási műveleteket. A protokollelemzők, például a Wireshark csomagszinten működnek, lehetővé téve a TCP-adatok megtekintését, ami lehetővé teszi az elveszett csomagok és kapcsolódási problémák elhárítását. A Message Analyzer http- és TCP-rétegeken is működhet.

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Végpontok között kontúrozás
A különböző naplófájlokat használó végpontok utáni nyomkövetés hasznos módszer a lehetséges problémák kivizsgálására. A metrikák adataiból származó dátum/idő adatok at annak jelzésére használhatja, hogy hol kezdje el keresni a naplófájlokat a probléma megoldásához szükséges részletes információkért.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Naplóadatok korrelációja
Az ügyfélalkalmazásokból, a hálózati nyomkövetésekből és a kiszolgálóoldali tárolónaplózásból származó naplók megtekintésekor fontos, hogy a kérelmek et a különböző naplófájlok között korrelálhassa. A naplófájlok számos különböző mezőt tartalmaznak, amelyek korrelációs azonosítóként hasznosak. Az ügyfélkérelem-azonosító a leghasznosabb mező a különböző naplók bejegyzéseinek korrelációjára. Néha azonban hasznos lehet a kiszolgálókérési azonosító vagy az időbélyegek használata. A következő szakaszok további részleteket tartalmaznak ezekről a lehetőségekről.

### <a name="client-request-id"></a><a name="client-request-id"></a>Ügyfélkérelem azonosítója
A storage-ügyfélkódtár automatikusan létrehoz egy egyedi ügyfélkérelem-azonosítót minden kérelemhez.

* A storage-ügyfélkódtár által létrehozott ügyféloldali naplóban az ügyfélkérelem-azonosító a kérelemhez kapcsolódó naplóbejegyzések **ügyfélkérelem-azonosító** mezőjében jelenik meg.
* A hálózati nyomkövetésben, például a Fiddler által rögzített ben az ügyfélkérelem-azonosító **x-ms-client-request-id** HTTP fejlécértékként látható a kérelemüzenetekben.
* A kiszolgálóoldali tárolónaplózási naplóban az ügyfélkérelem-azonosító megjelenik az Ügyfélkérelem-azonosító oszlopban.

> [!NOTE]
> Lehetőség van arra, hogy több kérelem ugyanazt az ügyfélkérelem-azonosítót ossza meg, mivel az ügyfél hozzárendelheti ezt az értéket (bár a Storage Client Library automatikusan új értéket rendel hozzá). Amikor az ügyfél újrapróbálkozik, minden kísérlet ugyanazt az ügyfélkérelem-azonosítót osztja meg. Az ügyféltől küldött köteg esetén a köteg egyetlen ügyfélkérelem-azonosítóval rendelkezik.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Kiszolgálókérés azonosítója
A tárolószolgáltatás automatikusan létrehozza a kiszolgálókérési azonosítókat.

* A kiszolgálóoldali tárolónaplózási naplóban a kiszolgálókérelem-azonosító megjelenik a **Kérelemazonosító fejlécoszlopa.**
* A hálózati nyomkövetésben, például a Fiddler által rögzített ben a kiszolgálókérésazonosító **x-ms-request-id** HTTP fejlécértékként jelenik meg a válaszüzenetekben.
* A tárolóügyfél-tár által létrehozott ügyféloldali naplóban a kiszolgálókérelem azonosítója megjelenik a naplóbejegyzés **Művelet szöveg oszlopában,** amely a kiszolgálóválasz részleteit mutatja.

> [!NOTE]
> A tárolási szolgáltatás mindig egyedi kiszolgálókérelem-azonosítót rendel minden kapott kéréshez, így az ügyfél és a kötegben szereplő minden művelet egyedi kiszolgálókérelem-azonosítóval rendelkezik.
>
>

Ha a storage-ügyféltár **egy StorageException-et** dob az ügyfélben, a **RequestInformation** tulajdonság egy **ServiceRequestID** tulajdonságot tartalmazó **RequestResult** objektumot tartalmaz. **Egy RequestResult** objektumot is elérhet egy **OperationContext** példányból.

Az alábbi kódminta bemutatja, hogyan állíthat be egyéni **ClientRequestId** értéket egy **OperationContext** objektum nak a storage szolgáltatáshoz csatolásával. Azt is bemutatja, hogyan lehet letölteni a **ServerRequestId** értéket a válaszüzenetből.

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

### <a name="timestamps"></a><a name="timestamps"></a>Időbélyegek
Időbélyegekkel is megkeresheti a kapcsolódó naplóbejegyzéseket, de legyen óvatos az ügyfél és a kiszolgáló közötti esetleges encika közötti eltérésekkel. Keressen plusz vagy mínusz 15 percet az ügyfél időbélyege alapján a megfelelő kiszolgálóoldali bejegyzésekért. Ne feledje, hogy a blob metaadatok a blobok tartalmazó metrikák jelzi a blobban tárolt metrikák időtartománya. Ez az időtartomány akkor hasznos, ha sok metrika blobok ugyanahhoz a perchez vagy órához.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató
Ez a szakasz segítséget nyújt az alkalmazás azure storage-szolgáltatások használata során felmerülő gyakori problémák diagnosztizálásában és hibaelhárításában. Az alábbi lista segítségével megkeresheti az adott problémával kapcsolatos információkat.

**Döntési fa hibaelhárítása**

---
A probléma az egyik tárolási szolgáltatás teljesítményével kapcsolatos?

* [A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]
* [A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]
* [A mérőszámok magas AverageServerLatency értéket mutatnak]
* [Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]

---
A probléma az egyik tárolási szolgáltatás rendelkezésre állásával kapcsolatos?

* [A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]
* [A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]
* [A mérőszámok emelkedő PercentNetworkError értéket mutatnak]

---
 Az ügyfélalkalmazás http 4XX (például 404) választ kap egy tárolási szolgáltatástól?

* [Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap]
* [Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap]
* [Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap]

---
[A metrikák alacsony Százalékos sikervagy elemzési naplóbejegyzések ügyfélhiba-állapotú tranzakciós állapotú műveleteket tartalmaznak.]

---
[A kapacitásmetrikák a tárolási kapacitás használatának váratlan növekedését mutatják]

---
[Olyan virtuális gépek váratlan újraindítását tapasztalja, amelyek nagy számú csatlakoztatott virtuális géppel rendelkeznek]

---
[Az Ön problémája abból ered, hogy a tároló emulátort fejlesztési vagy tesztelési célokra]

---
[Problémákat tapasztal a .NET-hez tartozó Azure SDK telepítése során]

---
[Más probléma van egy tárolási szolgáltatással]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak
Az alábbi ábrán az [Azure Portal](https://portal.azure.com) figyelési eszköz mutat egy példát, ahol a **AverageE2ELatency** jelentősen magasabb, mint az **AverageServerLatency**.

![][4]

A tárolási szolgáltatás csak kiszámítja a metrika **AverageE2ELatency** a sikeres kérelmek, és ellentétben **AverageServerLatency,** tartalmazza az időt az ügyfél az adatok küldéséhez és a storage szolgáltatás nyugtázásának fogadása. Ezért az **AverageE2ELatency** és az **AverageServerLatency** közötti különbség lehet az, hogy az ügyfélalkalmazás lassan válaszol, vagy a hálózati feltételek miatt.

> [!NOTE]
> Az **E2ELatency** és a **ServerLatency** az egyes tárolási műveletekhez a Storage Logging log data-ban is megtekintheti.
>
>

#### <a name="investigating-client-performance-issues"></a>Az ügyfelek teljesítményével kapcsolatos problémák vizsgálata
Az ügyfél lassú válaszadásának lehetséges okai közé tartozik a korlátozott számú elérhető kapcsolat vagy szál, vagy az erőforrások , például a processzor, a memória vagy a hálózati sávszélesség hiánya. A problémát úgy oldhatja meg, hogy az ügyfélkód hatékonyabbmódosítása (például a storage szolgáltatás aszinkron hívásainak használatával), vagy egy nagyobb virtuális gép használatával (több maggal és több memóriával).

A tábla- és várólista-szolgáltatások esetében a Nagle algoritmus az **AverageServerLatency-hez**képest magas **AverageE2ELatency-t** is okozhat: további információkért lásd a [Nagle algoritmusa nem barátságos a kis kérésekkel szemben](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). A Nagle algoritmus tágíthatja a kódot a **ServicePointManager** osztály használatával a **System.Net** névtérben. Ezt az alkalmazás ban lévő tábla- vagy várólista-szolgáltatások hívása előtt kell megtennie, mivel ez nincs hatással a már megnyitott kapcsolatokra. A következő példa a **Application_Start** metódus egy feldolgozói szerepkörben.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Ellenőrizze az ügyféloldali naplókban, hogy hány kérelmet küld el az ügyfélalkalmazás, és ellenőrizze az általános .NET-hez kapcsolódó teljesítménybeli szűk keresztmetszeteket az ügyfélben, például a PROCESSPROCESS-t, a .NET szemétgyűjtést, a hálózati kihasználtságot vagy a memóriát. A .NET ügyfélalkalmazások hibaelhárításának kiindulópontjaként lásd: [Hibakeresés, Nyomkövetés és Profilkészítés](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Hálózati késési problémák vizsgálata
A hálózat által okozott felső szintű késés általában átmeneti körülmények miatt. Az átmeneti és az állandó hálózati problémákat, például az eldobott csomagokat is megvizsgálhatja olyan eszközökkel, mint a Wireshark vagy a Microsoft Message Analyzer.

A Wireshark hálózati problémák elhárításához való használatáról a "[2. függelék: A Drótcápa használata a hálózati forgalom rögzítéséhez]" című témakörben olvashat bővebben.

A Microsoft Message Analyzer hálózati problémák elhárításához való használatáról a "[3. függelék: A Microsoft Message Analyzer használata a hálózati forgalom rögzítéséhez]" című témakörben olvashat bővebben.

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal
Ebben a forgatókönyvben a legvalószínűbb ok a tárolási kérelmek elérése a tárolási szolgáltatás késedelmes. Meg kell vizsgálnia, hogy az ügyféltől érkező kérések miért nem jutnak át a blobszolgáltatásba.

Az ügyfél késleltetésének egyik lehetséges oka az, hogy korlátozott számú elérhető kapcsolat vagy szál áll rendelkezésre.

Azt is ellenőrizze, hogy az ügyfél több újrapróbálkozást hajt-e végre, és vizsgálja meg az okát, ha van. Annak megállapításához, hogy az ügyfél több újrapróbálkozást hajt-e végre, a következőket teheti:

* Vizsgálja meg a Storage Analytics naplók. Ha több újrapróbálkozás történik, több műveletet fog látni ugyanazzal az ügyfélkérelem-azonosítóval, de különböző kiszolgálói kérelemazonosítókkal.
* Vizsgálja meg az ügyfélnaplókat. A részletes naplózás azt jelzi, hogy újrapróbálkozás történt.
* Hibakeresés a kódot, és ellenőrizze a tulajdonságait **operationcontext** objektum a kérelemhez társított. Ha a művelet újrapróbálkozott, a **RequestResults** tulajdonság több egyedi kiszolgálókérelem-azonosítót fog tartalmazni. Az egyes kérelmek kezdési és befejezési időpontjait is ellenőrizheti. További információt a [Kiszolgálókérésazonosító]című szakaszban található kódmintában talál.

Ha nincsenek problémák az ügyfélben, vizsgálja meg a lehetséges hálózati problémákat, például a csomagvesztést. A hálózati problémák kivizsgálásához olyan eszközökkel is használhatja, mint a Wireshark vagy a Microsoft Message Analyzer.

A Wireshark hálózati problémák elhárításához való használatáról a "[2. függelék: A Drótcápa használata a hálózati forgalom rögzítéséhez]" című témakörben olvashat bővebben.

A Microsoft Message Analyzer hálózati problémák elhárításához való használatáról a "[3. függelék: A Microsoft Message Analyzer használata a hálózati forgalom rögzítéséhez]" című témakörben olvashat bővebben.

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>A mérőszámok magas AverageServerLatency értéket mutatnak
Abban az esetben, ha a blob letöltési kérelmek magas **AverageServerLatency** esetén a storage logging naplók használatával ellenőrizheti, hogy vannak-e ismétlődő kérelmek ugyanahhoz a blobhoz (vagy blobkészlethez). A blob feltöltési kérelmek esetében meg kell vizsgálnia, hogy az ügyfél milyen blokkméretet használ (például a 64 K-nál kisebb méretű blokkok többletköltségeket eredményezhetnek, kivéve, ha az olvasások 64 K-nál kevesebb adattömbben vannak), és ha több ügyfél tölt fel blokkokat ugyanabba a blobba Párhuzamos. Azt is ellenőrizze a percenkénti metrikák a kiugrások a kérelmek száma, amelyek eredményeként meghaladja a másodpercenkénti skálázhatósági célok: lásd még a "[Metrikák növekedése percentTimeoutError]növekedését mutatja.

Ha a blob letöltési kérelmekhez magas **AverageServerLatency** lehetőséget látja, ha ugyanazok at blob vagy blobkészlet ismételt kérelmek vannak, akkor érdemes lehet ezeket a blobokat az Azure Cache vagy az Azure Content Delivery Network (CDN) használatával gyorsítótárazza. A feltöltési kérelmek, javíthatja az átviteli teljesítmény segítségével egy nagyobb blokkméretet. A táblák lekérdezései esetében ügyféloldali gyorsítótárazás is implementizálható azon ügyfeleken, amelyek ugyanazokat a lekérdezési műveleteket hajtják végre, és ahol az adatok nem változnak gyakran.

A magas **AverageServerLatency** értékek a rosszul megtervezett táblák vagy lekérdezések tünete is lehet, amelyek beolvasási műveleteket eredményeznek, vagy amelyek követik a hozzáfűző/prepend anti-mintát. További információ: "[Metrikák százalékos throttlingerror növekedését mutatják]".

> [!NOTE]
> Az ellenőrzőlista teljesítményellenőrző listáját itt találja: [Microsoft Azure Storage Teljesítmény és Méretezhetőség ellenőrzőlista](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor
Ha az alkalmazás üzenet várólistához való hozzáadása és a várólistából való olvasás elérhetővé válása között késést tapasztal, akkor a probléma diagnosztizálása érdekében tegye meg a következő lépéseket:

* Ellenőrizze, hogy az alkalmazás sikeresen hozzáadta-e az üzeneteket a várólistához. A sikeres ség előtt ellenőrizze, hogy az alkalmazás nem próbálja-e meg többször újra az **AddMessage** metódust. A storage-ügyfélkönyvtár-naplók a tárolási műveletek ismétlődő újrapróbálkozásait jelenítik meg.
* Ellenőrizze, hogy nincs-e óradöntés a várakozói szerepkör és a várólistához kérő feldolgozói szerepkör között, amely úgy jelenik meg, mintha a feldolgozás késése lenne.
* Ellenőrizze, hogy a várakozói üzeneteket beolvasó feldolgozói szerepkör sikertelen-e. Ha egy várólista-ügyfél meghívja a **GetMessage** metódust, de nem válaszol nyugtázással, az üzenet láthatatlan marad a várólistán, amíg az **invisibilityTimeout** időszak le nem jár. Ezen a ponton az üzenet ismét feldolgozásra elérhetővé válik.
* Ellenőrizze, hogy a várólista hossza növekszik-e az idő múlásával. Ez akkor fordulhat elő, ha nem áll rendelkezésre elegendő dolgozó a többi dolgozó által a várólistára helyezett összes üzenet feldolgozásához. Ellenőrizze a metrikákban is, hogy a törlési kérelmek sikertelenek-e, és hogy a várólistán lévő kontrájuk várható-e az üzeneteken, ami az üzenet ismételt sikertelen törlésére irányuló kísérletekre utalhat.
* Vizsgálja meg a storage naplózási naplókat minden olyan várólista-műveletre, amelynek a vártnál magasabb **E2ELatency** és **ServerLatency** értéke a szokásosnál hosszabb ideig van.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>A mérőszámok emelkedő PercentThrottlingError értéket mutatnak
Szabályozáshibák akkor fordulnak elő, ha túllépi a tárolási szolgáltatás méretezhetőségi céljait. A tárolási szolgáltatás szabályozása annak biztosítására, hogy egyetlen ügyfél vagy bérlő nem használhatja a szolgáltatást mások rovására. További információkért lásd: [Méretezhetőségi és teljesítménycélok a standard tárfiókok](scalability-targets-standard-account.md) a storage-fiókok méretezhetőségi céljainak és a storage-fiókokon belüli partíciók teljesítménycéljainak részleteit.

Ha a **PercentThrottlingError** metrika a szabályozási hibával meghibásodott kérelmek százalékos arányának növekedését mutatja, két forgatókönyv egyikét kell megvizsgálnia:

* [Átmeneti növekedése PercentThrottlingError]
* [A PercentThrottlingError hiba tartós növekedése]

A **PercentThrottlingError** növekedése gyakran a tárolási kérelmek számának növekedésével egy időben történik, vagy amikor először tölti be az alkalmazás tesztelését. Ez az ügyfélben is "503 kiszolgáló foglalt" vagy "500 műveletidőtúltöltés" HTTP-állapotüzenetként jelenhet meg a tárolási műveletekből.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Átmeneti növekedése PercentThrottlingError
Ha a **PercentThrottlingError** értékében olyan kiugrásokat lát, amelyek egybeesnek az alkalmazás magas aktivitási időszakaival, exponenciális (nem lineáris) visszalépési stratégiát valósít meg az ügyfél újrapróbálkozásaihoz. A vissza- és újrapróbálkozások csökkentik a partíció azonnali terhelését, és segítenek az alkalmazásnak a forgalom kiugrásának kiegyenlítésében. Az újrapróbálkozási házirendek storage-ügyfélkódtár használatával történő megvalósításáról a [Microsoft.Azure.Storage.RetryPolicies névtérben](/dotnet/api/microsoft.azure.storage.retrypolicies)talál további információt.

> [!NOTE]
> A **PercentThrottlingError** értékében is láthat olyan kiugrásokat, amelyek nem esnek egybe az alkalmazás magas aktivitási időszakaival: a legvalószínűbb ok itt a tárolószolgáltatás mozgó partíciói a terheléselosztás javítása érdekében.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>A PercentThrottlingError hiba tartós növekedése
Ha a tranzakciós kötetek végleges növekedését követően, vagy amikor a kezdeti terhelési teszteket az alkalmazáson, folyamatosan magas értéket lát a **PercentThrottlingError** számára, akkor ki kell értékelnie, hogy az alkalmazás hogyan használja a tárolási partíciókat, és hogy közeledik-e a tárfiók méretezhetőségi céljaihoz. Ha például egy várólistán szabályozási hibákat lát (ami egyetlen partíciónak számít), akkor érdemes további várólistákat használni a tranzakciók több partícióra való felosztásához. Ha egy táblában szabályozási hibákat lát, meg kell fontolnia egy másik particionálási séma használatát a tranzakciók több partícióra való felosztásához a partíciókulcs-értékek szélesebb körének használatával. A probléma egyik gyakori oka az előleg/hozzáfűzés anti-minta, ahol kiválasztja a dátumot partíciókulcsként, majd egy adott napon lévő összes adat egy partícióra van írva: terhelés alatt, ez írási szűk keresztmetszetet eredményezhet. Fontolja meg egy másik particionálási terv, vagy kiértékeli, hogy a blob storage használata lehet jobb megoldás. Azt is ellenőrizze, hogy a sávszélesség-szabályozás a forgalom kiugrásai miatt történik-e, és vizsgálja meg a kérelmek mintájának simításának módjait.

Ha a tranzakciók at több partícióközött osztja el, továbbra is tisztában kell lennie a tárfiókra beállított méretezhetőségi korlátokkal. Ha például tíz várólistát használt másodpercenként legfeljebb 2000 1 KB-os üzenet feldolgozásával, akkor a tárfiók másodpercenkénti 20 000 üzenetmaximális száma lesz. Ha másodpercenként több mint 20 000 entitást kell feldolgoznia, érdemes több tárfiókot használnia. Azt is szem előtt kell tartania, hogy a kérelmek és entitások mérete hatással van, amikor a tárolási szolgáltatás szabályozza az ügyfelek: ha nagyobb kérelmek és entitások, előfordulhat, hogy korábban szabályozásalatt.

A nem hatékony lekérdezéstervezés azt is okozhatja, hogy eléri a táblapartíciók méretezhetőségi korlátait. Például egy szűrővel rendelkező lekérdezés, amely csak egy partíció entitásainak egy százalékát választja ki, de amely a partíció összes entitását ellenőrzi, minden entitáshoz hozzá kell férnie. Minden entitás olvasott beleszámít a partíción lévő tranzakciók teljes számába; ezért könnyen elérheti a méretezhetőségi célokat.

> [!NOTE]
> A teljesítménytesztelésnek fel kell tárnia az alkalmazás nem hatékony lekérdezési terveket.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>A mérőszámok emelkedő PercentTimeoutError értéket mutatnak
A metrikák a **percentTimeoutError** növekedését mutatják az egyik tárolási szolgáltatásban. Ugyanakkor az ügyfél nagy mennyiségű "500 operation timeout" HTTP-állapotüzenetet kap a tárolási műveletektől.

> [!NOTE]
> Előfordulhat, hogy átmenetileg időtúllépésre vonatkozó hibák jelennek meg, mivel a tárolási szolgáltatás terhelése egy partíció új kiszolgálóra való áthelyezésével kiegyensúlyozza a kérelmeket.
>
>

A **PercentTimeoutError** metrika a következő mérőszámok összesítése: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**és **SASServerTimeoutError**.

A kiszolgáló időkimenő-időpontjait a kiszolgálón található hiba okozza. Az ügyfél időtúllépései azért történnek, mert a kiszolgálón egy művelet túllépte az ügyfél által megadott időtúltöltést; a Storage Client Library-t használó ügyfél például időmegállíthat egy műveletet a **QueueRequestOptions** osztály **ServerTimeout** tulajdonságával.

A kiszolgáló időkitöltése a tárolási szolgáltatással kapcsolatos problémát jelez, amely további vizsgálatot igényel. Metrikák segítségével ellenőrizheti, hogy eléri-e a szolgáltatás méretezhetőségi korlátját, és azonosíthatja a forgalmat, amely a problémát okozhatja. Ha a probléma időszakos, annak oka lehet a terheléselosztási tevékenység a szolgáltatásban. Ha a probléma állandó, és nem az okozza, hogy az alkalmazás eléri a szolgáltatás méretezhetőségi korlátait, támogatási problémát kell felvetnie. Az ügyfél időtúlszámai esetén el kell döntenie, hogy az időtúllépésre be van-e állítva az ügyfél megfelelő értékére, és vagy módosítania kell az ügyfélben beállított időtúltöltési értéket, vagy meg kell vizsgálnia, hogyan javíthatja a műveletek teljesítményét a storage szolgáltatásban, például optimalizálással. a tábla lekérdezéseit vagy az üzenetek méretének csökkentését.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>A mérőszámok emelkedő PercentNetworkError értéket mutatnak
A metrikák a **PercentNetworkError** növekedését mutatják az egyik tárolási szolgáltatásesetében. A **PercentNetworkError** metrika a következő mérőszámok összesítése: **NetworkError**, **AnonymousNetworkError**és **SASNetworkError**. Ezek akkor fordulnak elő, ha a tárolási szolgáltatás hálózati hibát észlel, amikor az ügyfél tárolási kérelmet küld.

A hiba leggyakoribb oka az ügyfél leválasztása, mielőtt az időbeli járat lejár a tárolási szolgáltatásban. Vizsgálja meg a kódot az ügyfél, hogy tudja, miért és mikor az ügyfél bontja a kapcsolatot a tárolási szolgáltatás. A Wireshark, a Microsoft Message Analyzer vagy a Tcping segítségével is megvizsgálhatja az ügyfél hálózati kapcsolódási problémáit. Ezeket az eszközöket a [függelékek ismertetik.]

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap
Ha az ügyfélalkalmazás HTTP 403 (Tiltott) hibákat jelez, annak egyik valószínű oka lehet, hogy az ügyfél egy lejárt közös hozzáférésű jogosultságkódot (SAS-t) használ, amikor tárolási kérelmet küld (egyéb lehetséges okok lehetnek még az óraeltérés, az érvénytelen kulcsok és az üres fejlécek). Ha egy lejárt SAS-kulcs a hiba oka, akkor nem fog bejegyzéseket látni a kiszolgálóoldali Storage naplózási szolgáltatásának naplóadataiban. Az alábbi táblázat a Storage Client Library által létrehozott ügyféloldali naplóból származó mintát mutatja be, amely a felmerülő problémát szemlélteti:

| Forrás | Részletesség | Részletesség | Ügyfélkérelem azonosítója | Művelet szövege |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Információ |3 |85d077ab-... |A művelet indítása helymeghatározással Elsődleges helyenként, PrimaryOnly. |
| Microsoft.Azure.Storage |Információ |3 |85d077ab -... |Szinkronkérés indítása<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Információ |3 |85d077ab -... |Várakozás a válaszra. |
| Microsoft.Azure.Storage |Figyelmeztetés |2 |85d077ab -... |Kivétel történt a válaszra való várakozás közben: A távoli kiszolgáló a következő hibát adta vissza: (403) Tiltott. |
| Microsoft.Azure.Storage |Információ |3 |85d077ab -... |Válasz érkezett. Állapotkód = 403, Kérelemazonosító = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Figyelmeztetés |2 |85d077ab -... |Kivétel történt a művelet során: A távoli kiszolgáló hibát adott vissza: (403) Tiltott.. |
| Microsoft.Azure.Storage |Információ |3 |85d077ab -... |Annak ellenőrzése, hogy a műveletet újra meg kell-e próbálni. Újrapróbálkozások száma = 0, HTTP-állapotkód = 403, Kivétel = A távoli kiszolgáló hibát adott vissza: (403) Tiltott.. |
| Microsoft.Azure.Storage |Információ |3 |85d077ab -... |A következő hely a helymód alapján Elsődleges lesz állítva. |
| Microsoft.Azure.Storage |Hiba |1 |85d077ab -... |Az újrapróbálkozási házirend nem engedélyezte az újrapróbálkozást. Sikertelen a távoli kiszolgáló hibát adott vissza: (403) Tiltott. |

Ebben az esetben meg kell vizsgálnia, hogy miért a SAS-jogkivonat lejár, mielőtt az ügyfél elküldi a jogkivonatot a kiszolgálónak:

* Általában nem kell beállítania a kezdési időt, amikor létrehoz egy SAS-t egy ügyfél számára azonnali használat céljából. Ha kis óraeltérések vannak az SAS-t a jelenlegi idő használatával létrehozó gazdagép és a tárolási szolgáltatás között, akkor előfordulhat, hogy a tárolási szolgáltatás olyan SAS-t fogad, amely még nem érvényes.
* Ne állítson be nagyon rövid lejárati időt az SAS-hoz. Az SAS-t létrehozó gazdagép és a tárolási szolgáltatás közötti kis óraeltérések tehát a vártnál látszólag korábban lejáró SAS-t eredményezhetnek.
* A SAS-kulcsverzió-paraméter (például **sv=2015-04-05)** megegyezik a használt Storage Client Library verziójával? Javasoljuk, hogy mindig a Storage [Client Library](https://www.nuget.org/packages/WindowsAzure.Storage/)legújabb verzióját használja.
* Ha újra létrehozza a tárelérési kulcsot, a meglévő SAS-jogkivonatok érvénytelenné válhatnak. Ez a probléma akkor léphet fel, ha hosszú lejárati idejű SAS-jogkivonatokat hoz létre az ügyfélalkalmazások számára gyorsítótárazás céljából.

Ha a Storage ügyféloldali kódtárának használatával hozza létre az SAS-jogkivonatokat, akkor könnyedén hozhat létre érvényes jogkivonatot. Ha azonban a Storage REST API-t használja, és a SAS-jogkivonatokat kézzel készíti, olvassa el a [Hozzáférés delegálása megosztott hozzáférési aláírással című témakört.](https://msdn.microsoft.com/library/azure/ee395415.aspx)

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap
Ha az ügyfélalkalmazás egy HTTP 404 (Nem található) üzenetet kap a kiszolgálótól, akkor ez azt jelenti, hogy az objektum, amelyet az ügyfél használni próbált (például egy entitás, egy táblázat, egy blob, egy tároló vagy egy üzenetsor) nem létezik a tárolási szolgáltatásban. Ennek számos oka lehet, például:

* [Az ügyfél vagy egy másik folyamat korábban törölte az objektumot]
* [Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája]
* [Az ügyféloldali JavaScript-kód nem rendelkezik engedéllyel az objektumhoz való hozzáféréshez]
* [Hálózati hiba]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Az ügyfél vagy egy másik folyamat korábban törölte az objektumot
Olyan esetekben, amikor az ügyfél egy tárolási szolgáltatásban próbál adatokat olvasni, frissíteni vagy törölni, általában könnyen azonosítható a kiszolgálóoldali naplókban egy korábbi művelet, amely törölte a kérdéses objektumot a tárolószolgáltatásból. A naplóadatok gyakran azt mutatják, hogy egy másik felhasználó vagy folyamat törölte az objektumot. A kiszolgálóoldali tárolónaplózási naplóban a művelettípusú és a kért objektumkulcs oszlopok azt mutatják, hogy egy ügyfél mikor törölt egy objektumot.

Abban az esetben, ha egy ügyfél megpróbál beszúrni egy objektumot, előfordulhat, hogy nem azonnal nyilvánvaló, hogy miért eredményez http 404 (Nem található) választ, mivel az ügyfél új objektumot hoz létre. Ha azonban az ügyfél blobot hoz létre, meg kell találnia a blobtárolót, ha az ügyfél üzenetet hoz létre, képesnek kell lennie egy várólista megkeresésére, és ha az ügyfél hozzáad egy sort, akkor képesnek kell lennie megtalálni a táblát.

Az ügyféloldali napló segítségével a storage-ügyfélkönyvtárból részletesebb en megismerheti, hogy az ügyfél mikor küld konkrét kéréseket a tárolási szolgáltatásnak.

A storage-ügyféltár által létrehozott következő ügyféloldali napló bemutatja a problémát, ha az ügyfél nem találja a létrehozott blob tárolóját. Ez a napló a következő tárolási műveletek részleteit tartalmazza:

| Kérelemazonosító | Művelet |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metódus a blob tároló törléséhez. Vegye figyelembe, hogy **HEAD** ez a művelet tartalmaz egy HEAD-kérelmet a tároló létezésének ellenőrzésére. |
| e2d06d78... |**CreateIfNotExists** metódus a blob tároló létrehozásához. Vegye figyelembe, hogy **HEAD** ez a művelet tartalmaz egy HEAD-kérelmet, amely ellenőrzi a tároló létezését. A **HEAD** egy 404-es üzenetet ad vissza, de folytatja. |
| de8b1c3c-... |**UploadFromStream** metódus a blob létrehozásához. A **PUT** kérés 404-es üzenettel sikertelen |

Bejegyzések naplózása:

| Kérelemazonosító | Művelet szövege |
| --- | --- |
| 07b26a5d-... |Szinkron kérés indítása https://domemaildist.blob.core.windows.net/azuremmblobcontainera alkalmazásba. |
| 07b26a5d-... |StringToSign = FEJ............ x-ms-client-request-id:07b26a5d-.... x-ms-date:Ke, 03 Jún 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Várakozás a válaszra. |
| 07b26a5d-... |Válasz érkezett. Állapotkód = 200, Kérelemazonosító = eeead849-... Content-MD5 = , &quot;Etag = 0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |A válaszfejlécek feldolgozása sikeresen megtörtént, a művelet többi részével folytatva. |
| 07b26a5d-... |A válaszszervezet letöltése. |
| 07b26a5d-... |A művelet sikeresen befejeződött. |
| 07b26a5d-... |Szinkron kérés indítása https://domemaildist.blob.core.windows.net/azuremmblobcontainera alkalmazásba. |
| 07b26a5d-... |StringToSign = DELETE............ x-ms-client-request-id:07b26a5d-.... x-ms-date:Ke, 03 Jún 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Várakozás a válaszra. |
| 07b26a5d-... |Válasz érkezett. Állapotkód = 202, Kérelemazonosító = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |A válaszfejlécek feldolgozása sikeresen megtörtént, a művelet többi részével folytatva. |
| 07b26a5d-... |A válaszszervezet letöltése. |
| 07b26a5d-... |A művelet sikeresen befejeződött. |
| e2d06d78-... |Aszinkron kérés indítása https://domemaildist.blob.core.windows.net/azuremmblobcontainera alkalmazáshoz.</td> |
| e2d06d78-... |StringToSign = FEJ............ x-ms-client-request-id:e2d06d78-.... x-ms-date:Ke, 03 Jún 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Várakozás a válaszra. |
| de8b1c3c-... |Szinkron kérés indítása https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txta alkalmazásba. |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==........ x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-.... x-ms-date:Ke, 03 Jún 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Felkészülés a kérelemadatok írására. |
| e2d06d78-... |Válaszra váró kivétel: A távoli kiszolgáló a következő hibát adta vissza: (404) Nem található.. |
| e2d06d78-... |Válasz érkezett. Állapotkód = 404, Kérelemazonosító = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |A válaszfejlécek feldolgozása sikeresen megtörtént, a művelet többi részével folytatva. |
| e2d06d78-... |A válaszszervezet letöltése. |
| e2d06d78-... |A művelet sikeresen befejeződött. |
| e2d06d78-... |Aszinkron kérés indítása https://domemaildist.blob.core.windows.net/azuremmblobcontainera alkalmazáshoz. |
| e2d06d78-... |StringToSign = PUT... 0.........x-ms-client-request-id:e2d06d78-.... x-ms-date:Ke, 03 Jún 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Várakozás a válaszra. |
| de8b1c3c-... |A kérelem adatainak írása. |
| de8b1c3c-... |Várakozás a válaszra. |
| e2d06d78-... |Válaszra váró kivétel: A távoli kiszolgáló hibát adott vissza: (409) Ütközés.. |
| e2d06d78-... |Válasz érkezett. Állapotkód = 409, Kérelemazonosító = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |A hibaválasz törzsének letöltése. |
| de8b1c3c-... |Válaszra váró kivétel: A távoli kiszolgáló a következő hibát adta vissza: (404) Nem található.. |
| de8b1c3c-... |Válasz érkezett. Állapotkód = 404, Kérelemazonosító = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Kivétel történt a művelet során: A távoli kiszolgáló hibát adott vissza: (404) Nem található.. |
| de8b1c3c-... |Az újrapróbálkozási házirend nem engedélyezte az újrapróbálkozást. A távoli kiszolgáló hibája: (404) Nem található.. |
| e2d06d78-... |Az újrapróbálkozási házirend nem engedélyezte az újrapróbálkozást. A távoli kiszolgáló hibája a következő hibaüzenetet adta: (409) Ütközés.. |

Ebben a példában a napló azt mutatja, hogy az ügyfél interleaving kérelmeket a **CreateIfNotExists** metódus (kérelem azonosító: e2d06d78...) a kérelmeket a **UploadFromStream** metódus (de8b1c3c-...). Ez interleaving történik, mert az ügyfélalkalmazás hivatkozik ezek a módszerek aszinkron módon. Módosítsa az aszinkron kódot az ügyfélben annak érdekében, hogy létrehozza a tárolót, mielőtt bármilyen adatot megpróbálna feltölteni egy blobba a tárolóban. Ideális esetben az összes tárolót előre létre kell hoznia.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája
Ha az ügyfélalkalmazás olyan SAS-kulcsot próbál használni, amely nem tartalmazza a művelethez szükséges engedélyeket, a storage szolgáltatás http 404-es (nem található) üzenetet ad vissza az ügyfélnek. Ugyanakkor a **sasauthorizationerror** nem nulla értéket is látni fogja a metrikákban.

Az alábbi táblázat a Tárolónaplózás naplófájlból származó kiszolgálóoldali naplóüzenet mintaüzenetét mutatja be:

| Név | Érték |
| --- | --- |
| Kérelem kezdési időpontja | 2014-05-30T06:17:48.4473697z |
| Művelet típusa     | GetBlobTulajdonságai            |
| Kérelem állapota     | SASAuthorizationError        |
| HTTP-állapotkód   | 404                          |
| Hitelesítés típusa| Sas                          |
| Szolgáltatás típusa       | Blob                         |
| Kérés URL-címe        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&&;sig=XXXXX api-version=2014-02-14 |
| Kérelem azonosítófejléce  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Ügyfélkérelem azonosítója  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Vizsgálja meg, hogy az ügyfélalkalmazás miért próbál olyan műveletet végrehajtani, amelyhez nem kapott engedélyt.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Az ügyféloldali JavaScript-kód nem rendelkezik engedéllyel az objektumhoz való hozzáféréshez
Ha JavaScript-ügyfelet használ, és a tárolási szolgáltatás HTTP 404-es üzeneteket ad vissza, a böngészőben ellenőrizze a következő JavaScript-hibákat:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Az Internet Explorer F12 fejlesztői eszközeivel nyomon követheti a böngésző és a tárolószolgáltatás között kicserélt üzeneteket az ügyféloldali JavaScript-problémák elhárításakor.
>
>

Ezek a hibák azért fordulnak elő, mert a webböngésző ugyanazt az [eredetházirend-biztonsági](https://www.w3.org/Security/wiki/Same_Origin_Policy) korlátozást alkalmazza, amely megakadályozza, hogy egy weblap a lap tól eltérő tartományban lévő API-t hívjon meg.

A JavaScript-probléma kerülő megoldásához konfigurálhatja a Cross Origin Resource Sharing (CORS) szolgáltatást az ügyfél által elérhető tárolási szolgáltatáshoz. További információ: [Cross-Origin Resource Sharing (CORS) Support for Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx).

A következő kódminta bemutatja, hogyan konfigurálhatja a blobszolgáltatást úgy, hogy a Contoso-tartományban futó JavaScript hozzáférjen egy blobhoz a blobstorage-szolgáltatásban:

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

#### <a name="network-failure"></a><a name="network-failure"></a>Hálózati hiba
Bizonyos körülmények között az elveszett hálózati csomagok azt eredményezhetik, hogy a tárolószolgáltatás HTTP 404-es üzeneteket ad vissza az ügyfélnek. Ha például az ügyfélalkalmazás egy entitást a table service-ből tesz, az ügyfél egy "HTTP 404 (nem található)" állapotüzenetet jelent a table service-ből. Amikor megvizsgálja a tábla a table storage szolgáltatásban, láthatja, hogy a szolgáltatás nem törölte az entitást a kért módon.

A kivétel részletei az ügyfél tartalmazza a kérelem azonosítóját (7e84f12d...) a table service által a kérelemhez rendelt: ezen információk segítségével megkeresheti a kérelem részleteit a kiszolgálóoldali tárolónaplókban a **kérelem-id-fejléc** oszlopban való kereséssel a naplófájlban. A metrikák segítségével azonosíthatja, ha ilyen hibák fordulnak elő, majd keressen a naplófájlokban a metrikák által a hiba rögzítésének időpontja alapján. Ez a naplóbejegyzés azt mutatja, hogy a törlés sikertelen volt egy "HTTP (404) Ügyfél egyéb hiba" állapotüzenettel. Ugyanez a naplóbejegyzés tartalmazza az ügyfél által generált kérelemazonosítót is az **ügyfél-kérelemazonosító** oszlopban (813ea74f...).

A kiszolgálóoldali napló egy másik bejegyzést is tartalmaz ugyanazzal az **ügyfél-kérelemazonosító** értékkel (813ea74f...) ugyanazon entitás és ugyanazon ügyfél sikeres törlési műveletéhez. Ez a sikeres törlési művelet nem sokkal a sikertelen törlési kérelem előtt történt.

Ennek a forgatókönyvnek a legvalószínűbb oka az, hogy az ügyfél törlési kérelmet küldött az entitásnak a table service-nek, amely sikeres volt, de nem kapott nyugtát a kiszolgálótól (valószínűleg átmeneti hálózati probléma miatt). Az ügyfél ezután automatikusan újrapróbálkozott a művelettel (ugyanazzal az **ügyfél-kérelemazonosítóval),** és ez az újrapróbálkozás sikertelen volt, mert az entitást már törölték.

Ha ez a probléma gyakran jelentkezik, vizsgálja meg, hogy az ügyfél miért nem kap nyugtázást a table szolgáltatástól. Ha a probléma időszakos, alá kell adnia a "HTTP (404) Not Found" hibát, és naplóznia kell az ügyfélben, de lehetővé kell tennie az ügyfél folytatását.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap
Az alábbi táblázat két ügyfélművelet kiszolgálóoldali naplójának kivonatát mutatja be: **DeleteIfExists,** majd azonnal **a CreateIfNotExists** követi ugyanazt a blobtároló nevet használva. Minden ügyfélművelet két, a kiszolgálónak küldött kérelmet eredményez, először egy **GetContainerProperties** kérést, amely ellenőrzi, hogy a tároló létezik-e, majd a **DeleteContainer** vagy a **CreateContainer** kérés.

| Időbélyeg | Művelet | Eredmény | Tárolónév | Ügyfélkérelem azonosítója |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties (Tároló tulajdonságai) |200 |mmcont között |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer tároló |202 |mmcont között |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties (Tároló tulajdonságai) |404 |mmcont között |bc881924-... |
| 05:10:14.2147723 |Tároló létrehozása |409 |mmcont között |bc881924-... |

Az ügyfélalkalmazásban lévő kód törli, majd azonnal újralétrehozza a blobtárolót ugyanazzal a névvel: a **CreateIfNotExists** metódus (ügyfélkérelem-azonosító: bc881924-...) végül a HTTP 409 (Ütközés) hibával sikertelen lesz. Ha egy ügyfél blobtárolókat, táblázatokat vagy üzenetsorokat töröl, egy rövid ideig nem lesz elérhető ugyanaz a név.

Ha a törlési/ismételt létrehozási minta gyakran előfordul, az ügyfélalkalmazásnak egyedi tárolóneveket kell használnia, valahányszor új tárolókat hoz létre.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>A metrikák alacsony Százalékos sikervagy elemzési naplóbejegyzések ügyfélhiba-állapotú tranzakciós állapotú műveleteket tartalmaznak.
A **PercentSuccess** mérőszám a HTTP-állapotkódjuk alapján sikeres műveletek százalékos arányát rögzíti. A 2XX állapotkódokkal rendelkező műveletek sikeresnek számítanak, míg a 3XX, 4XX és 5XX tartományokállapotkódjaival rendelkező műveletek sikertelennek számítanak, és csökkentik a **PercentSuccess** metrikus értéket. A kiszolgálóoldali tárolási naplófájlokban ezek a műveletek **ÜgyfélHiba-állapotú**tranzakciós állapottal kerülnek rögzítésre.

Fontos megjegyezni, hogy ezek a műveletek sikeresen befejeződtek, és ezért nem befolyásolják más metrikák, például a rendelkezésre állás. Néhány példa a sikeresen végrehajtott, de sikertelen HTTP-állapotkódokat eredményező műveletekre:

* **ResourceNotFound** (Nem található 404), például egy GET-kérelemből egy blobba, amely nem létezik.
* **ResourceAlreadyExists** (409 ütközés), például egy **CreateIfNotExist** műveletből, ahol az erőforrás már létezik.
* **ConditionNotMet** (Nem módosított 304), például egy feltételes műveletből, például amikor egy ügyfél **eTag** értéket és EGY HTTP **If-None-Match** fejlécet küld egy lemezkép kéréséhez, ha az az utolsó művelet óta frissült.

A storage services által visszaadott gyakori REST API-hibakódok listáját a [Common REST API hibakódok](https://msdn.microsoft.com/library/azure/dd179357.aspx)oldalon találja.

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>A kapacitásmetrikák a tárolási kapacitás használatának váratlan növekedését mutatják
Ha hirtelen, váratlan változásokat lát a tárfiók kapacitáshasználatában, az okokat a rendelkezésre állási metrikák első vizsgálatával vizsgálhatja meg; például a sikertelen törlési kérelmek számának növekedése a blobstorage-tároló mennyiségének növekedéséhez vezethet, mivel az alkalmazásspecifikus tisztítási műveletek várhatóan nem a várt módon működnek (például , mert a hely felszabadítására használt SAS-tokenek lejártak).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Az Ön problémája abból ered, hogy a tároló emulátort fejlesztési vagy tesztelési célokra
Általában a tárolási emulátort használja a fejlesztés során, és tesztelje az Azure storage-fiók követelményének elkerülése érdekében. A tárolóemulátor használata során fellépő gyakori problémák a következők:

* [Az "X" funkció nem működik a tároló emulátorban]
* [Hiba : "Az egyik HTTP-fejléc értéke nem a megfelelő formátumú" a tárolóemulátor használatakor]
* [A tárolóemulátor futtatásához rendszergazdai jogosultságok szükségesek]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Az "X" funkció nem működik a tároló emulátorban
A storage-emulátor nem támogatja az Azure storage-szolgáltatások, például a fájlszolgáltatás összes szolgáltatását. További információ: [Az Azure Storage-emulátor használata fejlesztési és tesztelési célokra](storage-use-emulator.md)című témakörben talál.

Azokhoz a funkciókhoz, amelyeket a storage-emulátor nem támogat, használja az Azure storage szolgáltatás a felhőben.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Hiba : "Az egyik HTTP-fejléc értéke nem a megfelelő formátumú" a tárolóemulátor használatakor
A storage-ügyfélkönyvtárat használó alkalmazást a helyi tárolóemulátorellen teszteli, és a metódushívások, például **a CreateIfNotExists** sikertelenek a következő hibaüzenettel: "Az egyik HTTP-fejléc értéke nem a megfelelő formátumú." Ez azt jelzi, hogy a használt tárolóemulátor verziója nem támogatja a használt tárolóügyfél-kódtár verzióját. A tárolóügyfél-tár hozzáadja az **x-ms-version** fejlécet az összes kéréshez. Ha a tárolóemulátor nem ismeri fel az **x-ms-version** fejlécben szereplő értéket, elutasítja a kérelmet.

A Storage Library Client naplók segítségével megtekintheti az általa küldött **x-ms-verziófejléc** értékét. Az **x-ms-verziófejléc** értékét is láthatja, ha a Fiddler használatával nyomon követi az ügyfélalkalmazásból érkező kérelmeket.

Ez a forgatókönyv általában akkor fordul elő, ha a storage-ügyfélkódtár legújabb verzióját telepíti és használja a tárolóemulátor frissítése nélkül. Telepítse a tárolóemulátor legújabb verzióját, vagy az emulátor helyett felhőalapú tárhelyet használjon a fejlesztéshez és teszteléshez.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>A tárolóemulátor futtatásához rendszergazdai jogosultságok szükségesek
A tárolóemulátor futtatásakor a rendszer rendszergazdai hitelesítő adatokat kér. Ez csak akkor fordul elő, ha először inicializálja a tároló emulátort. Miután inicializálta a tároló emulátort, nincs szüksége rendszergazdai jogosultságokra az ismételt futtatásához.

További információ: [Az Azure Storage-emulátor használata fejlesztési és tesztelési célokra](storage-use-emulator.md)című témakörben talál. A tárolóemulátort inicializálhatja a Visual Studióban is, amely hez rendszergazdai jogosultságok is szükség lesz.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Problémákat tapasztal a .NET-hez tartozó Azure SDK telepítése során
Amikor megpróbálja telepíteni az SDK-t, nem próbálja meg telepíteni a tároló emulátort a helyi számítógépre. A telepítési napló az alábbi üzenetek egyikét tartalmazza:

* CAQuietExec: Hiba: Nem lehet elérni az SQL-példányt
* CAQuietExec: Hiba: Nem lehet létrehozni az adatbázist

Az ok a LocalDB meglévő telepítésével kapcsolatos probléma. Alapértelmezés szerint a storage emulátor a LocalDB segítségével megőrzi az adatokat, amikor szimulálja az Azure storage-szolgáltatások. A LocalDB-példány alaphelyzetbe állításához futjon a következő parancsok egy parancssori ablakban, mielőtt megpróbálna telepíteni az SDK-t.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

A **delete** parancs eltávolítja a régi adatbázisfájlokat a tárolóemulátor korábbi telepítéseiből.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Más probléma van egy tárolási szolgáltatással
Ha az előző hibaelhárítási szakaszok nem tartalmazzák a tárolási szolgáltatással kapcsolatos problémát, a probléma diagnosztizálására és hibaelhárítására a következő megközelítést kell alkalmaznia.

* Ellenőrizze a metrikákat, hogy van-e változás a várt alapvonali viselkedéshez képest. A metrikákból meghatározhatja, hogy a probléma átmeneti vagy állandó, és mely tárolási műveleteket érinti a probléma.
* A metrikák adatai segítségével a kiszolgálóoldali naplóadatokban további információkat kereshet az esetlegesen előforduló hibákról. Ezek az információk segíthetnek a probléma elhárításában és megoldásában.
* Ha a kiszolgálóoldali naplókban szereplő információk nem elegendőek a probléma sikeres elhárításához, a Storage Client Library ügyféloldali naplóisegítségével megvizsgálhatja az ügyfélalkalmazás viselkedését, valamint az olyan eszközöket, mint a Fiddler, a Wireshark és a Microsoft. Üzenetelemző a hálózat kivizsgálásához.

A Fiddler használatával kapcsolatos további információkért lásd: "[1. függelék: A Hegedűs használata http- és HTTPS-forgalom rögzítéséhez."]

A Wireshark használatáról további információt a "[2.]

A Microsoft Message Analyzer használatáról további információt a "[3.]

## <a name="appendices"></a><a name="appendices"></a>Függelékek
A függelékek számos olyan eszközt írnak le, amelyek hasznosak lehetnek az Azure Storage (és más szolgáltatások) problémáinak diagnosztizálása és elhárítása során. Ezek az eszközök nem részei az Azure Storage-nak, és néhány harmadik féltől származó termék. Így az ezekben a függelékekben tárgyalt eszközökre nem vonatkozik a Microsoft Azure-ral vagy az Azure Storage-szal kötött támogatási szerződés, ezért a kiértékelési folyamat részeként meg kell vizsgálnia a licencelési és támogatási lehetőségeket, amelyek a ezeket az eszközöket.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>1. függelék: A Hegedűs használata http- és HTTPS-forgalom rögzítésére
[A Fiddler](https://www.telerik.com/fiddler) hasznos eszköz az ügyfélalkalmazás és a használt Azure storage-szolgáltatás közötti HTTP- és HTTPS-forgalom elemzéséhez.

> [!NOTE]
> A Hegedűs dekódolhatja a HTTPS-forgalmat; figyelmesen olvassa el a Fiddler dokumentációját, hogy megértse, hogyan teszi ezt, és hogy megértse a biztonsági következményeket.
>
>

Ez a függelék rövid áttekintést nyújt arról, hogyan konfigurálhatja a Fiddler-t a Fiddler-t telepített helyi gép és az Azure storage-szolgáltatások közötti forgalom rögzítésére.

Miután elindította a Fiddler-t, megkezdi a HTTP és HTTPS forgalom rögzítését a helyi gépen. Az alábbiakban néhány hasznos parancsot a Fiddler vezérléséhez:

* Állítsa le és kezdje el rögzíteni a forgalmat. A főmenüben válassza a Fájl menü **Fájl** menüjét, majd a **Forgalom rögzítése** parancsra a rögzítés be- és kikapcsolásához.
* A rögzített forgalmi adatok mentése. A főmenüben válassza a **Fájl**menü **Mentés**menüjét, majd a **Minden munkamenetet:** ez lehetővé teszi a forgalom munkamenet-archívumfájlba való mentését. Később újra betöltheti a munkamenet-archívumot elemzésre, vagy elküldheti, ha kérik a Microsoft támogatási szolgálatának.

A Fiddler által rögzített forgalom mennyiségének korlátozásához használhatja a **Szűrők** lapon konfigurált szűrőket. A következő képernyőképen egy szűrő látható, amely csak a **contosoemaildist.table.core.windows.net** tárolási végpontra küldött forgalmat rögzíti:

![][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>2. függelék: Wireshark használata a hálózati forgalom rögzítésére
[A Wireshark](https://www.wireshark.org/) egy hálózati protokollelemző, amely lehetővé teszi a részletes csomaginformációk megtekintését a hálózati protokollok széles köréhez.

Az alábbi eljárás bemutatja, hogyan rögzítheti a részletes csomaginformációkat a helyi gépről érkező forgalomhoz, ahol a Wireshark-ot telepítette az Azure storage-fiók táblaszolgáltatásába.

1. Indítsd el a Wireshark-ot a helyi gépeden.
2. A **Start (Start)** szakaszban válassza ki az internethez kapcsolódó helyi hálózati adaptert vagy illesztéseket.
3. Kattintson **a Rögzítési beállítások gombra.**
4. Szűrő hozzáadása a **Rögzítési szűrő** szövegmezőbe. Például **az állomás contosoemaildist.table.core.windows.net** konfigurálja a Wireshark-ot, hogy csak a table service endpoint-ba vagy a **contosoemaildist** storage-fiókból küldött csomagokat rögzítsen. Tekintse meg a [rögzítési szűrők teljes listáját.](https://wiki.wireshark.org/CaptureFilters)

   ![][6]
5. Kattintson a **Start gombra.** Wireshark most elfog minden csomagot küld, vagy a table service végpont használata során az ügyfélalkalmazás a helyi gépen.
6. Ha végzett, kattintson a **főmenüRögzítés,** majd **Leállítás parancsára.**
7. Ha a rögzített adatokat Wireshark capture fájlba szeretné menteni, kattintson a főmenü **Fájl parancsára,** majd a **Mentés parancsra.**

WireShark kiemeli a hibákat, hogy létezik a **packetlist** ablakban. A Szakértői adatok ablakot is **használhatja** (kattintson az **Elemzés**, majd **a Szakértői adatok parancsra)** a hibák és figyelmeztetések összegzésének megtekintéséhez.

![][7]

A TCP-adatokat úgy is megtekintheti, ahogy az alkalmazásréteg látja, ha a jobb gombbal a TCP-adatokra kattint, és a **TCP-adat követése parancsot választja.** Ez akkor hasznos, ha a memóriaképet rögzítési szűrő nélkül rögzítette. További információt a [TCP-adatfolyamok követése](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html)című témakörben talál.

![][8]

> [!NOTE]
> A Wireshark használatáról további információt a [Wireshark felhasználói útmutatójában talál.](https://www.wireshark.org/docs/wsug_html_chunked)
>
>

### <a name="appendix-3-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>3. függelék: A Microsoft Message Analyzer használata a hálózati forgalom rögzítésére
A Microsoft Message Analyzer segítségével a HTTP- és HTTPS-forgalmat a Fiddlerhez hasonlóan rögzítheti, és a Wireshark-hoz hasonlóan rögzítheti a hálózati forgalmat.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Webes nyomkövetési munkamenet konfigurálása a Microsoft Message Analyzer segítségével
Ha a Microsoft Message Analyzer segítségével http- és HTTPS-forgalomra vonatkozó webes nyomkövetési munkamenetet szeretne beállítani, futtassa a Microsoft Message Analyzer alkalmazást, majd kattintson a **Fájl** menü **Rögzítés/Nyomon követés parancsára.** Az elérhető nyomkövetési forgatókönyvek listájában válassza a **WebProxy**lehetőséget. Ezután a **Trace Scenario Configuration** panelEn a **HostnameFilter** szövegmezőben adja hozzá a tárolási végpontok nevét (ezeket a neveket az [Azure Portalon](https://portal.azure.com)keresheti meg). Ha például az Azure-tárfiók neve **contosodata,** a következőt kell hozzáadnia a **HostnameFilter** szövegdobozhoz:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Az állomásneveket szóközkarakter választja el egymástól.
>
>

Ha készen áll a nyomkövetési adatok gyűjtésének megkezdésére, kattintson a **Start with** gombra.

A Microsoft Message Analyzer **webproxy-nyomkövetéséről** a [Microsoft-PEF-WebProxy Provider című](https://technet.microsoft.com/library/jj674814.aspx)témakörben talál további információt.

A Microsoft Message Analyzer beépített **webproxy-nyomkövetése** a Fiddler- en alapul; képes rögzíteni az ügyféloldali HTTPS-forgalmat, és titkosítatlan HTTPS-üzeneteket megjeleníteni. A **webproxy-nyomkövetés** úgy működik, hogy helyi proxyt konfigurál az összes HTTP- és HTTPS-forgalomhoz, amely hozzáférést biztosít számára a titkosítatlan üzenetekhez.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Hálózati problémák diagnosztizálása a Microsoft Message Analyzer segítségével
A Mellett, hogy a **Web Proxy** Microsoft Message Analyzer webproxy-nyomkövetést használja az ügyfélalkalmazás és a tárolási szolgáltatás közötti HTTP/HTTPs-forgalom részleteinek rögzítésére, a beépített **helyi hivatkozási réteg** nyomkövetésével is rögzítheti a hálózati csomagadatokat. Ez lehetővé teszi, hogy rögzítse az adatokhasonló, amelyek rögzíti a Wireshark, és diagnosztizálni hálózati problémák, mint például az eldobott csomagokat.

A következő képernyőképen látható egy példa **a helyi hivatkozási réteg** nyomon követése néhány **tájékoztató** üzeneteket a **DiagnosisTypes** oszlopban. A **DiagnosisTypes** oszlopban egy ikonra kattintva megjelennek az üzenet részletei. Ebben a példában a kiszolgáló újraküldött üzenet #305, mert nem kapott nyugtát az ügyféltől:

![][9]

Amikor létrehozza a nyomkövetési munkamenetet a Microsoft Message Analyzer alkalmazásban, szűrőket adhat meg a nyomkövetésben lévő zaj mennyiségének csökkentésére. A **Rögzítés/nyomon követés** lapon, ahol definiálja a nyomkövetést, kattintson a **Microsoft-Windows-NDIS-PacketCapture**melletti **Konfigurálás** hivatkozásra. A következő képernyőképen látható egy konfiguráció, amely szűri a TCP-forgalmat három tárolási szolgáltatás IP-címeinél:

![][10]

A Microsoft Message Analyzer Local Link Layer nyomkövetési adatairól a [Microsoft-PEF-NDIS-PacketCapture Provider című témakörben talál](https://technet.microsoft.com/library/jj659264.aspx)további információt.

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>4. függelék: Az Excel használata a mérőszámok és a naplóadatok megtekintéséhez
Számos eszköz lehetővé teszi, hogy a Storage Metrics adatokat az Azure táblatárolóból egy tagolt formátumban töltse le, amely megkönnyíti az adatok betöltését az Excelbe megtekintésre és elemzésre. Az Azure blob storage-ból származó adatok naplózása már elhatárolt formátumban van, amelyet betölthet az Excelbe. Azonban meg kell adnia a megfelelő oszlopfejléceket a [Storage Analytics naplóformátumés](https://msdn.microsoft.com/library/azure/hh343259.aspx) [a Storage Analytics metrics táblázatséma](https://msdn.microsoft.com/library/azure/hh343264.aspx)adatai alapján.

A tárolónaplózási adatok importálása az Excelbe, miután letöltötte őket a blobstorage-ból:

* Kattintson az **Adatok** menü **Szövegből parancsára.**
* Tallózással keresse meg a megtekinteni kívánt naplófájlt, és kattintson **az Importálás gombra.**
* A **Szövegimportálás varázsló** **1.**

A **Szövegimportálás varázsló**első lépésében válassza a **Pontosvessző** lehetőséget az egyetlen határolójelként, és válassza a dupla idézőjelet **szövegminősítőként**. Ezután kattintson a **Befejezés** gombra, és adja meg, hogy hová szeretné helyezni az adatokat a munkafüzetben.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>5. függelék: Figyelés az Azure DevOps alkalmazáselemzési adataival
Az Azure DevOps Alkalmazásinsights-szolgáltatása is használható a teljesítmény és a rendelkezésre állás figyelése részeként. Ez az eszköz:

* Győződjön meg arról, hogy a webszolgáltatás elérhető és reszponzív. Függetlenül attól, hogy az alkalmazás egy webszolgáltatást használó webhely vagy eszközalkalmazás, néhány percenként tesztelheti az URL-címet a világ különböző pontjairól, és tudathatja, ha probléma merül fel.
* Gyorsan diagnosztizálhatja a webszolgáltatás teljesítményével kapcsolatos problémákat vagy kivételeket. Megtudhatja, hogy a PROCESSZOR vagy más erőforrások nyújtása folyamatban van-e, a kivételekből veremnyomkövetéseket kaphat, és egyszerűen kereshet a naplónyomok között. Ha az alkalmazás teljesítménye az elfogadható határértékek alá csökken, a Microsoft e-mailt küldhet Önnek. A .NET és a Java webszolgáltatások figyelhetők.

További információt a [Mi az Application Insights](../../azure-monitor/app/app-insights-overview.md)című lapban talál.

## <a name="next-steps"></a>További lépések

Az Azure Storage-ban található elemzésekkel kapcsolatos további információkért tekintse meg az alábbi erőforrásokat:

* [Tárfiók monitorozása az Azure Portalon](storage-monitor-storage-account.md)
* [Tárolási elemzés](storage-analytics.md)
* [Tárolási elemzési mutatók](storage-analytics-metrics.md)
* [Storage analytics metrikák táblaséma](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Storage Analytics-naplók](storage-analytics-logging.md)
* [Tárolási elemzési napló formátuma](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Bevezetés]: #introduction
[Az útmutató rendszerezése]: #how-this-guide-is-organized

[A tárolási szolgáltatás figyelése]: #monitoring-your-storage-service
[A szolgáltatás állapotának figyelése]: #monitoring-service-health
[Ellenőrzési kapacitás]: #monitoring-capacity
[Elérhetőség figyelése]: #monitoring-availability
[A teljesítmény figyelése]: #monitoring-performance

[Tárolási problémák diagnosztizálása]: #diagnosing-storage-issues
[A szolgáltatások állapotproblémáival kapcsolatos problémák]: #service-health-issues
[Teljesítményproblémák]: #performance-issues
[Hibák diagnosztizálása]: #diagnosing-errors
[Tárolási emulátor problémák]: #storage-emulator-issues
[Tárolónaplózási eszközök]: #storage-logging-tools
[Hálózati naplózási eszközök használata]: #using-network-logging-tools

[Végpontok között kontúrozás]: #end-to-end-tracing
[Naplóadatok korrelációja]: #correlating-log-data
[Ügyfélkérelem azonosítója]: #client-request-id
[Kiszolgálókérés azonosítója]: #server-request-id
[Időbélyegek]: #timestamps

[Hibaelhárítási útmutató]: #troubleshooting-guidance
[A mérőszámok magas AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok alacsony AverageE2ELatency és alacsony AverageServerLatency értéket mutatnak, de az ügyfél nagy mértékű késleltetést tapasztal]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[A mérőszámok magas AverageServerLatency értéket mutatnak]: #metrics-show-high-AverageServerLatency
[Váratlan késést tapasztal az üzenetsorban található üzenetek kézbesítésekor]: #you-are-experiencing-unexpected-delays-in-message-delivery

[A mérőszámok emelkedő PercentThrottlingError értéket mutatnak]: #metrics-show-an-increase-in-PercentThrottlingError
[Átmeneti növekedése PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[A PercentThrottlingError hiba tartós növekedése]: #permanent-increase-in-PercentThrottlingError
[A mérőszámok emelkedő PercentTimeoutError értéket mutatnak]: #metrics-show-an-increase-in-PercentTimeoutError
[A mérőszámok emelkedő PercentNetworkError értéket mutatnak]: #metrics-show-an-increase-in-PercentNetworkError

[Az ügyfél HTTP 403 (Tiltott) hibaüzeneteket kap]: #the-client-is-receiving-403-messages
[Az ügyfél HTTP 404 (Nem található) hibaüzeneteket kap]: #the-client-is-receiving-404-messages
[Az ügyfél vagy egy másik folyamat korábban törölte az objektumot]: #client-previously-deleted-the-object
[Egy közös hozzáférésű jogosultságkód (SAS) engedélyezési problémája]: #SAS-authorization-issue
[Az ügyféloldali JavaScript-kód nem rendelkezik engedéllyel az objektumhoz való hozzáféréshez]: #JavaScript-code-does-not-have-permission
[Hálózati hiba]: #network-failure
[Az ügyfél HTTP 409 (Ütközés) hibaüzeneteket kap]: #the-client-is-receiving-409-messages

[A metrikák alacsony Százalékos sikervagy elemzési naplóbejegyzések ügyfélhiba-állapotú tranzakciós állapotú műveleteket tartalmaznak.]: #metrics-show-low-percent-success
[A kapacitásmetrikák a tárolási kapacitás használatának váratlan növekedését mutatják]: #capacity-metrics-show-an-unexpected-increase
[Az Ön problémája abból ered, hogy a tároló emulátort fejlesztési vagy tesztelési célokra]: #your-issue-arises-from-using-the-storage-emulator
[Az "X" funkció nem működik a tároló emulátorban]: #feature-X-is-not-working
[Hiba : "Az egyik HTTP-fejléc értéke nem a megfelelő formátumú" a tárolóemulátor használatakor]: #error-HTTP-header-not-correct-format
[A tárolóemulátor futtatásához rendszergazdai jogosultságok szükségesek]: #storage-emulator-requires-administrative-privileges
[Problémákat tapasztal a .NET-hez tartozó Azure SDK telepítése során]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Más probléma van egy tárolási szolgáltatással]: #you-have-a-different-issue-with-a-storage-service

[Függelékek]: #appendices
[1. függelék: A Hegedűs használata http- és HTTPS-forgalom rögzítésére]: #appendix-1
[2. függelék: Wireshark használata a hálózati forgalom rögzítésére]: #appendix-2
[3. függelék: A Microsoft Message Analyzer használata a hálózati forgalom rögzítésére]: #appendix-3
[4. függelék: Az Excel használata a mérőszámok és a naplóadatok megtekintéséhez]: #appendix-4
[5. függelék: Figyelés az Azure DevOps alkalmazáselemzési adataival]: #appendix-5

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
