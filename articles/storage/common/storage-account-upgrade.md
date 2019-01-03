---
title: Frissítés az általános célú v2 tárfiók – Azure Storage |} A Microsoft Docs
description: Általános célú v2-tárfiókok frissítse.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/18/2018
ms.author: tamram
ms.openlocfilehash: 7f97b72dc7b3456488d97009bde590b0e29918e6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631434"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Egy általános célú v2-tárfiók frissítése

Általános célú v2-tárfiókok a legújabb Azure Storage-szolgáltatások támogatásához, és építse be az összes funkcióját, általános célú v1 és Blob storage-fiókok. Általános célú v2 fiókok használata akkor javasolt, a storage legtöbb forgatókönyvhöz. Általános célú v2 fiókok kapacitás árak nyújthat a legalacsonyabb gigabájtonkénti az Azure Storage, valamint iparági versenyképes tranzakciós.

Frissítés az általános célú v1, általános célú v2 tárfiók vagy a Blob storage-fiókok használata egyszerű. Az Azure portal, PowerShell vagy az Azure CLI használatával is frissítheti. 

> [!NOTE]
> A tárolási szintek módosítása további díjakat vonhat maga után. További részleteket az [árakat és számlázást](#pricing-and-billing) ismertető szakaszban olvashat.

## <a name="upgrade-using-the-azure-portal"></a>Frissítse az Azure portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a tárfiókot.
3. Az a **beállítások** területén kattintson **konfigurációs**.
4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.
5. A **Frissítés megerősítése** területen írja be a fiók nevét. 
6. Kattintson a **frissítése** a panel alján.

## <a name="upgrade-with-powershell"></a>Frissítés a PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egy általános célú v2 fiókját a PowerShell segítségével egy általános célú v1 fiók frissítéséhez először frissítse PowerShell legújabb verzióját használja a **Az.Storage** modul. A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket. 

Ezután hívja meg a fiókot, és cserélje le az erőforráscsoport nevét és a storage-fiók frissítése a következő parancsot:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Frissítés az Azure CLI-vel

Egy általános célú v2 fiók az Azure CLI-vel egy általános célú v1 fiók frissíteni, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető szakaszt. 

Ezután hívja meg a fiókot, és cserélje le az erőforráscsoport nevét és a storage-fiók frissítése a következő parancsot:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Blobadatok a hozzáférési szint megadását

Általános célú v2 támogatja az összes Azure storage-szolgáltatások és adatok objektum, de az elérési szint csak a Blob storage-ban a blokkblobokhoz érhető el. Amikor frissít egy általános célú v2-tárfiók, megadhatja a hozzáférési szint a blob adatait. 

Hozzáférés csomagjai lehetővé teszik, hogy válassza ki a leginkább költséghatékony tárolási megoldás a várható használati mintái alapján. A blokkblobok használatát támogatják a gyors Elérésűre, ritka elérésű vagy archív szinten tárolhatók. A hozzáférési rétegek további információkért lásd: [Azure Blob storage: A gyakran és ritkán használt adatok, és az archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

Alapértelmezés szerint egy új storage-fiók jön létre a gyakran használt adatok tároláselérési rétegében és a egy általános célú v1 storage-fiók frissítése a gyakori elérésű hozzáférési szintre. Ha, a vizsgált milyen hozzáférési szint használata az adatok frissítése, fontolja meg a forgatókönyvnek. Egy általános célú v2 fiók való áttelepítés során két jellemző felhasználói forgatókönyv közül választhat:

* Rendelkezik egy meglévő általános célú v1-tárfiókot, és szeretné kiértékelni egy általános célú v2 tárfiók blobadatokat a megfelelő tárolási szintjére való áttérést.
* Egy általános célú v2-tárfiók használata vagy már rendelkezik ilyennel, és szeretné meghatározni, hogy blobadatokat kell használnia a gyakori vagy ritka elérésű tárolási szinten választotta.

Mindkét esetben az első érték tárolása, elérése és a egy általános célú v2-tárfiók-ban tárolt adatokkal kapcsolatos működtetése költségének és összehasonlítása az aktuális költségekkel.


## <a name="pricing-and-billing"></a>Árak és számlázás
Az összes tárfiók az egyes blobok szintjén alapuló árképzési modellt alkalmaz a blobtároláshoz. Tárfiókok használatakor az alábbi számlázási szempontok érvényesülnek:

* **Tárolási költségek**: Tárolt adatok mennyisége mellett az adattárolás díja a tárolási réteg függvényében. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.

* **Adathozzáférési költségek**: Az adathozzáférési költségek emelkednek, ha a szint ritkábban. A ritka elérésű és az archív tárolási szint esetében gigabájtonkénti adathozzáférési díjat kell fizetni az adatolvasásokért.

* **Tranzakciós költségek**: A tranzakciónkénti díjat kell fizetni minden szint esetében, amely növeli, ha a szint ritkábban van.

* **Georeplikációs adatátviteli költségek**: Ezt a díjat csak a georeplikációval konfigurált, beleértve a GRS és RA-GRS fiókok vonatkozik. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.

* **Kimenő adatátviteli költségek**: Kimenő adatforgalom (azaz az Azure-régióba adatok) sávszélesség-használat gigabájtonkénti történik, az általános célú tárfiókok esetében a számlázás számítunk fel.

* **A tárolási réteg módosítása**: A díj megegyezik a tárfiókban lévő összes adat ritka elérésű szintről a fiók tárolási szintjének módosítása tekintetében. A fiók tárolási szintjének gyakori elérésűről ritka elérésűre való váltása esetében felmerülő díj viszont az összes adat ritka elérésű szintre írásának költségével egyezik meg (csak a GPv2-fiókok esetében).

> [!NOTE]
> A tárfiókok árképzési modelljével kapcsolatos további információért lásd [az Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Az aktuális felhasználási mintáin költségeinek

Tárolási költségek becsléséhez és a egy általános célú v2-tárfiók az egy adott szinten lévő Blobadatok eléréséhez értékelnie a jelenlegi használati módot vagy a várható használati módot hozzávetőleges. Általában a következőket érdemes figyelembe venni:

* A Blob storage fogyasztásánál, gigabájtban, többek között:
    - Mennyi adatot tárol a tárfiókjában?
    - Havi lebontásban hogyan változik az adatmennyiség? Az új adatok folyamatosan átveszik a korábbi adatok helyét?
* Az elsődleges elérési mintája a Blob storage-adatokból, többek között:
    - Folyamatban van, hogy mennyi adatot olvas és ír a storage-fiókhoz? 
    - Hány olvasási műveletek és írási műveleteket a rendszer az adatok a storage-fiókban?

Az igényeinek megfelelően a legjobb hozzáférési szint használata mellett dönt, hogy hasznos lehet meghatározni az blob kapacitás, és hogyan adatokat használják. Ezt leginkább teheti megnézzük a figyelési metrikákat a fiókjához.

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

További tudnivalók a mérőszámadatok engedélyezésével, gyűjtésével és megtekintésével kapcsolatban: [Az Azure Storage mérőszámainak engedélyezése és a mérőszámadatok megtekintése](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

#### <a name="capacity-costs"></a>A kapacitás költségek

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

A Blob Storage-tárfiókok adathozzáférési költségeinek kiszámításához a tranzakciókat két csoportra kell felosztania.

* A tárfiókból lekért adatok mennyisége a *„TotalEgress”* összegéből becsülhető meg, elsődlegesen a *„GetBlob”* és a *„CopyBlob”* művelet alapján.

* A tárfiókba írt lekért adatok mennyisége a *„TotalIngress”* összegéből becsülhető meg, elsődlegesen a *„PutBlob”*, a *„PutBlock”*, a *„CopyBlob”* és az *„AppendBlock”* művelet alapján.

A Blob Storage-tárfiókok georeplikációs adatátviteli költségei szintén az írt adatok mennyiségének becslése alapján számítható ki GRS- vagy RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Ha részletesebb példát szeretne látni a gyakori és ritka elérésű tárolási szint használatához kapcsolódó költségek kiszámítására, tekintse át a *„What are Hot and Cool access tiers and how should I determine which one to use?”* (Mi a gyakori és a ritka elérésű tárolási szint, és hogyan határozhatom meg, hogy melyiket kell használnom?) című gyakori kérdéseket. az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>További lépések

- [Tárfiók létrehozása](storage-quickstart-create-account.md)
- [Az Azure storage-fiókok kezelése](storage-account-manage.md)
