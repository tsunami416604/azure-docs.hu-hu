---
title: Frissítés általános célú v2-tárfiókra
titleSuffix: Azure Storage
description: Frissítsen általános célú v2-es tárfiókokra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.openlocfilehash: 9afbade408d6f95fcd3a61aa1ba65bc09c7a875b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067217"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Frissítés általános célú v2-tárfiókra

Általános célú v2-es tárfiókok támogatják a legújabb Azure Storage-funkciókat, és belefoglalják az általános célú v1- és Blob storage-fiókok összes funkcióját. Általános célú v2-fiókok ajánlott a legtöbb tárolási forgatókönyvek. Az általános célú v2-es fiókok az Azure Storage esetében a legalacsonyabb gigabájtonkénti kapacitásárakat, valamint az iparágban versenyképes tranzakciós árakat biztosítják. Általános célú v2-fiókok támogatják az alapértelmezett fiók hozzáférési szintek gyakori vagy ritka elérésű és blob szintű rétegezés között gyakori, ritka elérésű vagy archív.

Az általános célú v2-es tárfiókra való frissítés az általános célú v1- vagy Blob-tárfiókokból egyszerű. Az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül frissíthet. Nincs állásidő vagy adatvesztés kockázata az általános célú v2 tárfiókra való frissítéssel. A fiók frissítése egy egyszerű Azure Resource Manager-művelet, amely módosítja a fiók típusát.

> [!IMPORTANT]
> Egy általános célú v1 vagy Blob tárfiók általános célú v2-re való frissítése állandó, és nem vonható vissza.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg a tárfiókot.
3. A **Beállítások csoportban** kattintson a **Konfiguráció gombra.**
4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.
5. A **Frissítés megerősítése** területen írja be a fiók nevét.
6. Kattintson a **frissítés** gombra a panel alján.

    ![Frissítési fiók fajta](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egy általános célú v1-fiók frissítése egy általános célú v2-fiók powershell használatával, először frissítse a PowerShell az **Az.Storage** modul legújabb verzióját. A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket.

Ezután hívja meg a következő parancsot a fiók frissítéséhez, az erőforráscsoport nevének, a tárfiók nevének és a kívánt fiókhozzáférési szintnek a helyettesítéséhez.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy általános célú v1-fiókot általános célú v2-fiókra szeretne frissíteni az Azure CLI használatával, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető szakaszt.

Ezután hívja meg a következő parancsot a fiók frissítéséhez, az erőforráscsoport nevének, a tárfiók nevének és a kívánt fiókhozzáférési szintnek a helyettesítéséhez.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Hozzáférési szint megadása a blobadatokhoz

Általános célú v2-fiókok támogatják az összes Azure storage-szolgáltatások és adatobjektumok, de a hozzáférési szintek csak a Blob storage blokkblobokra vonatkoznak. Amikor egy általános célú v2-es tárfiókra frissít, megadhatja a gyakori vagy ritka elérésű alapértelmezett fiókhozzáférési szintet, amely azt jelzi, hogy a blobadatok feltöltése úgy történik, mintha az egyes blob hozzáférési szint paraméter nincs megadva.

A Blob hozzáférési szintek lehetővé teszik a leginkább költséghatékony tárolás kiválasztását a várható használati minták alapján. A blokkblobok gyakori, ritka elérésű vagy archív rétegekben tárolhatók. A hozzáférési szintekről további információt az [Azure Blob storage: Hot, Cool és Archive storage tiers című témakörben talál.](../blobs/storage-blob-storage-tiers.md)

Alapértelmezés szerint egy új tárfiók jön létre a gyakori elérésű hozzáférési rétegben, és egy általános célú v1-es tárfiók frissíthető a gyakori elérésű vagy ritka elérésű fiókszintre. Ha a frissítéskor nincs megadva fiókhozzáférési szint, akkor alapértelmezés szerint a rendszer felforrósodik. Ha azt vizsgálja, hogy melyik hozzáférési szintet használja a frissítéshez, vegye figyelembe az aktuális adathasználati forgatókönyvet. Két tipikus felhasználói forgatókönyv létezik egy általános célú v2-fiókba való áttelepítéshez:

* Rendelkezik egy meglévő általános célú v1-es tárfiókkal, és szeretné kiértékelni egy általános célú v2-es tárfiók frissítését, a blobadatok megfelelő tárolási hozzáférési szinttel.
* Úgy döntött, hogy egy általános célú v2-tárfiókot használ, vagy már rendelkezik egy, és szeretné kiértékelni, hogy a gyakori vagy ritka elérésű tárolási hozzáférési szint blob adatok.

Mindkét esetben az elsődleges prioritás az általános célú v2-es tárfiókban tárolt adatok tárolásának, elérésének és működtetésének becsült költsége, és az aktuális költségek összehasonlítása.

## <a name="pricing-and-billing"></a>Árak és számlázás

A v1-es tárfiók frissítése egy általános célú v2-fiókra ingyenes. Megadhatja a kívánt fiókréteget a frissítési folyamat során. Ha a frissítéskor nincs megadva fiókszint, a frissített fiók `Hot`alapértelmezett fiókszintje a . Azonban a storage access tier módosítása a frissítés után a számla módosítása ily módon ajánlott megadni az új fiókréteget a frissítés során.

Az összes tárfiók az egyes blobok szintjén alapuló árképzési modellt alkalmaz a blobtároláshoz. Tárfiókok használatakor az alábbi számlázási szempontok érvényesülnek:

* **Tárolási költségek:** A tárolt adatok mennyisége mellett az adatok tárolásának költsége a tárolási hozzáférési szinttől függően változik. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.

* **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A ritka elérésű és archív tárolási hozzáférési szint adataiért gigabájtonkénti adathozzáférési díjat kell fizetnie az olvasásért.

* **Tranzakciós költségek**: Minden szint esetében tranzakciónkénti díjat kell fizetni, ez emelkedik, ha a szint ritkábban használt adatokat tárol.

* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.

* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.

* **A tárelérési szint módosítása:** A fióktár-hozzáférési szint ritka elérésűről gyakori elérésűre történő módosítása a tárfiókban lévő összes adat olvasásával egyenlő díjat von maga után. Azonban a fiók hozzáférési szint módosítása a gyakori elérésűről a ritka elérésűre, az összes adat nak a ritka elérésű rétegbe (csak GPv2-fiókok) történő írásával egyenlő díjat számít fel.

> [!NOTE]
> A tárfiókok árképzési modelljével kapcsolatos további információért lásd [az Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Az aktuális használati minták költségeinek becslése

A blobadatok tárolásának és elérésének becsült költsége egy adott rétegben egy általános célú v2 tárfiókban, értékelje ki a meglévő használati mintát, vagy közelítse meg a várt használati mintát. Általában a következőket érdemes figyelembe venni:

* A Blob-tárolási felhasználás gigabájtban, többek között:
  * Mennyi adatot tárol a tárfiókjában?
  * Havi lebontásban hogyan változik az adatmennyiség? Az új adatok folyamatosan átveszik a korábbi adatok helyét?

* A Blob-tárolási adatok elsődleges hozzáférési mintája, többek között:
  * Mennyi adatot olvas fel és ír a tárfiókba?
  * Hány olvasási és írási művelet történik a tárfiókban lévő adatokon?

Az igényeinek megfelelő legjobb hozzáférési szint meghatározása, hasznos lehet a blob adatkapacitásának meghatározásához, és az adatok használatba való fel- és használatának módját. Ez a legjobb, ha megnézi a fiók figyelési metrikák.

### <a name="monitoring-existing-storage-accounts"></a>A meglévő tárfiókok figyelése

A meglévő tárfiókok monitorozásához és az ezzel kapcsolatos adatgyűjtéshez nyújt segítséget az Azure Storage Analytics, amellyel naplózhatja a tárfiókokat, és megnézheti a fiókokra vonatkozó mérőszámokat. A Storage Analytics olyan mérőszámokat tárol, amelyek a tárolási szolgáltatáshoz érkező kérések összesített tranzakcióstatisztikáját és kapacitási adatait tartalmazzák mind a GPv1 és a GPv2, mind a Blob Storage fióktípus esetében. Ezeket az adatokat a jól ismert táblák tárolják az adott tárfiókban.

További információért lásd [a Storage Analytics mérőszámainak áttekintését](https://msdn.microsoft.com/library/azure/hh343258.aspx) és [a Storage Analytics mérőszámainak táblasémáját](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> A Blob Storage-tárfiókok a tábla szolgáltatásvégpontját csak az adott fiók mérőszámadatainak tárolásához és eléréséhez jelenítik meg.

A Blob Storage tárolófelhasználásának figyeléséhez engedélyeznie kell a kapacitási mérőszámot.
Ha ez a mérőszám engedélyezve van, a rendszer naponta rögzíti a tárfiók blobszolgáltatásának kapacitásadatait, és létrehoz egy táblabejegyzést az adott tárfiók *$MetricsCapacityBlob* táblájában.

A Blob Storage adathozzáférési mintáinak figyeléséhez engedélyeznie kell az óránkénti tranzakciók mérőszámát az API szintjén. Ha az óránkénti tranzakciók mérőszáma engedélyezve van, a rendszer óránként összesíti az API-tranzakciókat, és táblabejegyzést hoz létre az adott tárfiók *$MetricsHourPrimaryTransactionsBlob* táblájában. A *$MetricsHourSecondaryTransactionsBlob* tábla a másodlagos végpontra rögzíti a tranzakciókat RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Ha rendelkezik egy általános célú tárfiókkal, amelyben lapblobokat és virtuálisgép-lemezeket, illetve üzenetsorokat, fájlokat vagy táblákat tárol a blokkblobok és a hozzáfűző blobok adatai mellett, akkor ez a becslési folyamat nem alkalmazható. A kapacitási adatok nem tesznek különbséget a blokkblobok és más adattípusok között, és nem adnak meg kapacitási adatokat ez utóbbiakhoz. Ilyen adattípusok használatakor a legutóbb számlázott mennyiségi tételek feltárásához alternatív módszert kell alkalmazni.

Azt javasoljuk, hogy az adatfelhasználás és -hozzáférés megfelelő mintájának előállításához olyan megőrzési időszakot válasszon a mérőszámhoz, amely megfelel az Ön használati szokásainak, és extrapolálja az adatokat. Az egyik lehetőség az, hogy hét napig őrzi meg a mérőszámadatokat, és minden héten összegyűjti az adatokat a hónap végén elvégzendő elemzéshez. A másik lehetőség az, hogy az utolsó 30 nap mérőszámadatait őrzi meg, és a 30 napos időszak végén hajtja végre az adatok összegyűjtését és elemzését.

A metrikák adatainak engedélyezésével, gyűjtésével és megtekintésével kapcsolatos részleteka [Storage-elemzési metrikák](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)ban.

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

#### <a name="capacity-costs"></a>Kapacitásköltségek

A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„data”* sorkulcsú utolsó bejegyzése mutatja a felhasználói adatok által igénybe vett tárolókapacitást. A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„analytics”* sorkulcsú utolsó bejegyzése mutatja az elemzési naplók által igénybe vett tárolókapacitást.

A felhasználói adatok és az elemzési naplók (ha engedélyezve vannak) által igénybe vett teljes kapacitás alapján lehet megbecsülni a tárfiókban lévő adatok tárolási költségét. Ugyanezzel a módszerrel becsülhetők meg a tárolási költségek a GPv1-tárfiókokban.

#### <a name="transaction-costs"></a>Tranzakciós költségek

A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalBillableRequests”* összege mutatja az adott API tranzakcióinak teljes számát. *Például* az adott időszakba eső *„GetBlob”* tranzakciók száma a *„user;GetBlob”* sorkulcsú bejegyzések összes számlázható kérelmének összege alapján számítható ki.

A Blob Storage-tárfiókok tranzakciós költségeinek kiszámításához a tranzakciókat három csoportra kell felosztania, mivel ezekhez különböző árak tartoznak.

* Írási tranzakciók, például *„PutBlob”*, *„PutBlock”*, *„PutBlockList”*, *„AppendBlock”*, *„ListBlobs”*, *„ListContainers”*, *„CreateContainer”*, *„SnapshotBlob”* és *„CopyBlob”*.
* Törlési tranzakciók, például *„DeleteBlob”* és *„DeleteContainer”*.
* Minden egyéb tranzakció.

A GPv1-tárfiókok tranzakciós költségeinek becsléséhez összesítenie kell az összes tranzakciót, függetlenül a művelettől és az API-tól.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Az adatok hozzáférésének és a georeplikációs adatok átvitelének költségei

Bár a tároló elemzése nem jelzi a tárfiókból olvasott és a tárfiókba írt adatok mennyiségét, a tranzakciók mérőszámának táblája alapján lehetőség van megközelítőleges becslésre. A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalIngress”* összege mutatja az adott API bejövő adatainak teljes mennyiségét bájtban. Hasonlóképpen a *„TotalEgress”* összege a kimenő adatok teljes mennyiségét mutatja bájtban.

A Blob Storage-tárfiókok adat-hozzáférési költségeinek kiszámításához a tranzakciókat két csoportra kell felosztania.

* A tárfiókból lekért adatok mennyisége a *„TotalEgress”* összegéből becsülhető meg, elsődlegesen a *„GetBlob”* és a *„CopyBlob”* művelet alapján.

* A tárfiókba írt lekért adatok mennyisége a *„TotalIngress”* összegéből becsülhető meg, elsődlegesen a *„PutBlob”*, a *„PutBlock”*, a *„CopyBlob”* és az *„AppendBlock”* művelet alapján.

A Blob Storage-tárfiókok georeplikációs adatátviteli költségei szintén az írt adatok mennyiségének becslése alapján számítható ki GRS- vagy RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> A gyakori vagy ritka elérésű tárolási hozzáférési szint használatának költségeinek kiszámítására vonatkozó részletesebb példát a *"Mik azok a gyakori hozzáférési szintek és hogyan kell meghatározni, hogy melyiket kell használni?"* című gyakori kérdéseket. az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](storage-account-create.md)
