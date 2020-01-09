---
title: Frissítés általános célú v2 Storage-fiókra
titleSuffix: Azure Storage
description: Frissítsen az általános célú v2 Storage-fiókokra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: e7228556659a7a4a9f01ea31f61e75b5e870d50c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457434"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Frissítés általános célú v2 Storage-fiókra

Az általános célú v2 Storage-fiókok támogatják az Azure Storage legújabb funkcióit, és az általános célú v1-és blob Storage-fiókok összes funkcióját beépítik. Az általános célú v2-fiókok használata a legtöbb tárolási helyzetben ajánlott. Az általános célú v2-fiókok az Azure Storage-ban a legalacsonyabb/GB-os kapacitást biztosítják, valamint az iparágban versenyképes tranzakciós árakat. Az általános célú v2-fiókok támogatják az alapértelmezett fiók-hozzáférési szinteket a gyakori vagy ritka elérésű és a blob szintű, a gyakori és a ritka elérésű, illetve az archiválási szintek között.

Egy általános célú v2-es Storage-fiókra való frissítés az általános célú v1-vagy blob Storage-fiókokból egyszerű. A frissítést a Azure Portal, a PowerShell vagy az Azure CLI használatával végezheti el.

> [!IMPORTANT]
> Az általános célú v1-vagy blob Storage-fiókok általános célú v2-re való frissítése végleges, és nem vonható vissza.

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg a tárfiókot.
3. A **Beállítások** szakaszban kattintson a **konfiguráció**elemre.
4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.
5. A **Frissítés megerősítése** területen írja be a fiók nevét.
6. Kattintson a panel alján található **frissítés** gombra.

    ![Fiók frissítése](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha egy általános célú v1-fiókot szeretne egy általános célú v2-fiókra frissíteni a PowerShell használatával, először frissítse a PowerShellt, hogy az az **. Storage** modul legújabb verzióját használja. A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket.

Ezután hívja meg a következő parancsot a fiók frissítéséhez, az erőforráscsoport nevének, a Storage-fiók nevének és a kívánt fiók hozzáférési szintjének a behelyettesítéséhez.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha egy általános célú v1-fiókot az Azure CLI használatával szeretne általános célú v2-fiókra frissíteni, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető szakaszt.

Ezután hívja meg a következő parancsot a fiók frissítéséhez, az erőforráscsoport nevének, a Storage-fiók nevének és a kívánt fiók hozzáférési szintjének a behelyettesítéséhez.

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Hozzáférési szintek megadása a blob-adatértékekhez

Az általános célú v2-fiókok támogatják az összes Azure Storage-szolgáltatást és-adatobjektumot, de a hozzáférési szintek csak a blob Storage-ban található blokk-Blobok esetében érhetők el. Egy általános célú v2-es Storage-fiókra való frissítéskor megadhat egy alapértelmezett fiók-hozzáférési szintet, amely az alapértelmezett szint, a blob-adatok feltöltése, ha nincs megadva az egyéni blob-hozzáférési réteg paraméter.

A blob hozzáférési szintjei lehetővé teszik a leginkább költséghatékony tárterület kiválasztását a várt használati minták alapján. A blokkos Blobok a gyakori, ritka vagy archív szinteken tárolhatók. A hozzáférési szintekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív tárolási szintek](../blobs/storage-blob-storage-tiers.md).

Alapértelmezés szerint a rendszer létrehoz egy új Storage-fiókot a gyors elérési szinten, és egy általános célú v1-es Storage-fiókot is frissít a gyakori vagy a ritkán használt fiók szintjére. Ha nincs megadva fiók-hozzáférési szint a Verziófrissítéskor, a rendszer alapértelmezés szerint a frissítésre frissíti. Ha vizsgálja meg, hogy melyik hozzáférési szintet szeretné használni a frissítéshez, vegye figyelembe az aktuális adatfelhasználási forgatókönyvet. Az általános célú v2-fiókokba való áttelepítéshez két tipikus felhasználói forgatókönyv van:

* Rendelkezik egy meglévő általános célú v1 Storage-fiókkal, és szeretné kiértékelni az általános célú v2 Storage-fiókra való frissítést, a blob-adatelérési réteg megfelelő tárolási hozzáférési szintjével.
* Úgy döntött, hogy egy általános célú v2-es Storage-fiókot használ, vagy már rendelkezik ilyennel, és szeretné kiértékelni, hogy érdemes-e a gyakori vagy ritka elérésű tárolási hozzáférési szintet használni a blob-adatokhoz.

Mindkét esetben az első prioritás az általános célú v2 Storage-fiókban tárolt adatok tárolási, elérési és üzemeltetési költségeinek becslése, valamint az aktuális költségekkel való összehasonlítás.

## <a name="pricing-and-billing"></a>Árak és számlázás

Egy v1-es Storage-fiók egy általános célú v2-fiókra való frissítése ingyenes. A frissítési folyamat során megadhatja a kívánt fiók szintjét. Ha nincs megadva a fiók szintje a Verziófrissítéskor, a frissített fiók alapértelmezett fiókjának szintje `Hot`lesz. Ha azonban a frissítés után módosítja a tárolási hozzáférési szintet, akkor a számla módosítása is előfordulhat, hogy a frissítés során javasolt az új fiók rétegének megadására.

Az összes tárfiók az egyes blobok szintjén alapuló árképzési modellt alkalmaz a blobtároláshoz. Tárfiókok használatakor az alábbi számlázási szempontok érvényesülnek:

* **Tárolási költségek**: a tárolt adatok mennyisége mellett az adattárolás költsége a tárolási hozzáférési szintjétől függően változhat. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.

* **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A lassú elérésű és az archív tároló hozzáférési rétegében lévő adatok esetében a GB-nál több adatelérési díjat számítunk fel a beolvasáshoz.

* **Tranzakciós költségek**: Minden szint esetében tranzakciónkénti díjat kell fizetni, ez emelkedik, ha a szint ritkábban használt adatokat tárol.

* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.

* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.

* **A tárolási hozzáférési szint módosítása**: a fiók tárolási hozzáférési rétegének a lassúról a gyors elérésű értékre való módosítása a Storage-fiókban meglévő összes információ olvasásával egyenlő. Azonban a fiókhoz való hozzáférési szint gyors és lassú elérésű értékre való módosítása az összes adatoknak a ritka rétegbe való írásával egyenlő (csak GPv2-fiókok esetében).

> [!NOTE]
> A tárfiókok árképzési modelljével kapcsolatos további információért lásd [az Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>A jelenlegi használati minták költségeinek becslése

Egy adott réteg általános célú v2-es Storage-fiókjában lévő blob-adatok tárolási és elérési költségeinek megbecsléséhez értékelje ki a meglévő használati mintát, vagy közelítse meg a várt használati mintát. Általában a következőket érdemes figyelembe venni:

* A blob Storage-felhasználás (GB), beleértve a következőket:
  * Mennyi adatot tárol a tárfiókjában?
  * Havi lebontásban hogyan változik az adatmennyiség? Az új adatok folyamatosan átveszik a korábbi adatok helyét?

* A blob Storage-adatai elsődleges hozzáférési mintája, beleértve a következőket:
  * Mennyibe kerül az adatok olvasása és írása a Storage-fiókba?
  * Hány olvasási művelet és írási művelet történik a Storage-fiókban lévő adatokon?

Annak érdekében, hogy az igényeinek megfelelő hozzáférési szintet döntse el, hasznos lehet a blob-adatkapacitás meghatározása, valamint az adathasználatuk módja. Ez a fiók figyelési metrikáinak megtekintésével végezhető el legjobban.

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

A metrikák adatainak engedélyezésével, gyűjtésével és megtekintésével kapcsolatos részletekért lásd a [Storage Analytics mérőszámait](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

#### <a name="capacity-costs"></a>Kapacitási költségek

A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„data”* sorkulcsú utolsó bejegyzése mutatja a felhasználói adatok által igénybe vett tárolókapacitást. A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„analytics”* sorkulcsú utolsó bejegyzése mutatja az elemzési naplók által igénybe vett tárolókapacitást.

A felhasználói adatok és az elemzési naplók (ha engedélyezve vannak) által igénybe vett teljes kapacitás alapján lehet megbecsülni a tárfiókban lévő adatok tárolási költségét. Ugyanezzel a módszerrel becsülhetők meg a tárolási költségek a GPv1-tárfiókokban.

#### <a name="transaction-costs"></a>Tranzakciós költségek

A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalBillableRequests”* összege mutatja az adott API tranzakcióinak teljes számát. *Például* az adott időszakba eső *„GetBlob”* tranzakciók száma a *„user;GetBlob”* sorkulcsú bejegyzések összes számlázható kérelmének összege alapján számítható ki.

A Blob Storage-tárfiókok tranzakciós költségeinek kiszámításához a tranzakciókat három csoportra kell felosztania, mivel ezekhez különböző árak tartoznak.

* Írási tranzakciók, például *„PutBlob”* , *„PutBlock”* , *„PutBlockList”* , *„AppendBlock”* , *„ListBlobs”* , *„ListContainers”* , *„CreateContainer”* , *„SnapshotBlob”* és *„CopyBlob”* .
* Törlési tranzakciók, például *„DeleteBlob”* és *„DeleteContainer”* .
* Minden egyéb tranzakció.

A GPv1-tárfiókok tranzakciós költségeinek becsléséhez összesítenie kell az összes tranzakciót, függetlenül a művelettől és az API-tól.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Az adatok hozzáférésének és a georeplikációs adatok átvitelének költségei

Bár a tároló elemzése nem jelzi a tárfiókból olvasott és a tárfiókba írt adatok mennyiségét, a tranzakciók mérőszámának táblája alapján lehetőség van megközelítőleges becslésre. A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalIngress”* összege mutatja az adott API bejövő adatainak teljes mennyiségét bájtban. Hasonlóképpen a *„TotalEgress”* összege a kimenő adatok teljes mennyiségét mutatja bájtban.

A Blob Storage-tárfiókok adat-hozzáférési költségeinek kiszámításához a tranzakciókat két csoportra kell felosztania.

* A tárfiókból lekért adatok mennyisége a *„TotalEgress”* összegéből becsülhető meg, elsődlegesen a *„GetBlob”* és a *„CopyBlob”* művelet alapján.

* A tárfiókba írt lekért adatok mennyisége a *„TotalIngress”* összegéből becsülhető meg, elsődlegesen a *„PutBlob”* , a *„PutBlock”* , a *„CopyBlob”* és az *„AppendBlock”* művelet alapján.

A Blob Storage-tárfiókok georeplikációs adatátviteli költségei szintén az írt adatok mennyiségének becslése alapján számítható ki GRS- vagy RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> A gyakori és ritka elérésű tárolási hozzáférési szint használatának költségeinek kiszámításához tekintse meg a *"mi a gyors és a lassú elérési szint, és Hogyan határozható meg, hogy melyiket érdemes használni?"* című részt. az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Következő lépések

* [Tárfiók létrehozása](storage-quickstart-create-account.md)
