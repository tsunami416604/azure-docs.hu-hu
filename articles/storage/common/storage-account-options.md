---
title: Az Azure Storage-fiók beállításai | Microsoft Docs
description: Az Azure Storage használatához kapcsolódó beállítások megismerése.
services: storage
author: xyh1
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: get-started-article
ms.date: 06/22/2018
ms.author: hux
ms.openlocfilehash: 3f1dfa09c0f123d20a7be043aa8d0033a5b6bd72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335771"
---
# <a name="azure-storage-account-options"></a>Az Azure Storage-fiók beállításai

## <a name="overview"></a>Áttekintés
Az Azure Storage három eltérő fiókbeállítási lehetőség kínál, amelyekhez különböző díjszabás és támogatott szolgáltatások tartoznak. Egy tárfiók létrehozása előtt vegye figyelembe ezeket a különbségeket, és döntse el, hogy az alkalmazásai számára melyik a legelőnyösebb beállítás. A következő három tárfiók-beállítás közül választhat:

* **Általános célú v2- (GPv2-)** fiók 
* **Általános célú v1- (GPv1-)** fiók
* **Blob Storage**-fiók

A következő szakasz mindegyik fióktípust bővebben ismerteti.

## <a name="storage-account-options"></a>Tárfiók-beállítások

### <a name="general-purpose-v2"></a>Általános célú v2

Az általános célú v2- (GPv2-) fiókok olyan tárfiókok, amelyek támogatják a blobok, a fájlok, az üzenetsorok és a táblák összes legújabb funkcióját. A GPv2-fiókok a GPv1- és Blob Storage-fiókok által támogatott összes API-t és funkciót is támogatják. Emellett az ezekkel a fióktípusokkal megegyező szintű tartósságot, rendelkezésre állást, méretezhetőséget és teljesítményt nyújtó szolgáltatásokat biztosítanak. A GPv2-fiókok díjszabása úgy lett kialakítva, hogy a legalacsonyabb gigabájtonkénti árak és iparági szinten versenyképes tranzakciós költségek legyenek elérhetők.

GPv1- vagy Blob Storage-fiókjáról az Azure Portal, a PowerShell vagy az Azure CLI használatával válthat GPv2-fiókra. 

A GPv2-fiókokhoz tartozó blokkblobok esetében a fiók szintjén a gyakori és a ritka elérésű tárolási szint, az egyes blobok szintjén pedig a gyakori és ritka elérésű, illetve az archív tárolási szint közül választhat az adathozzáférési mintáknak megfelelően. A költségek optimalizálása érdekében a gyakran, nem gyakran és ritkán használt adatokat tárolja a megfelelő gyakori elérésű, ritka elérésű vagy archív tárolási szinten. 

A GPv2-tárfiókokban fiókszinten elérhető a **Hozzáférési szint** attribútum, amellyel megadható, hogy a tárfiók alapértelmezett szintje **gyakori** vagy **ritka** elérésű legyen. Az alapértelmezett tárfiókszint érvényes az összes olyan blobra, amelynél nincs explicit módon meghatározva a szint az egyes blobokra vonatkozóan. Ha változik az adatok használati módja, bármikor válthat a tárolási szintek között. Az **archív szint** csak a blobok szintjén alkalmazható.

> [!NOTE]
> A tárolási szintek módosítása további díjakat vonhat maga után. További részleteket az [árakat és számlázást](#pricing-and-billing) ismertető szakaszban olvashat.
>
> A Microsoft a legtöbb esetben az általános célú v2-tárfiókok használatát javasolja a Blob Storage-fiókokkal szemben.

### <a name="upgrade-a-storage-account-to-gpv2"></a>Tárfiók frissítése GPv2-fiókra

A felhasználók bármikor frissíthetik GPv1- vagy Blob Storage-fiókjukat GPv2-fiókra a PowerShell vagy az Azure CLI használatával. Ez a módosítás nem vonható vissza, és minden egyéb módosítás tiltott.

#### <a name="upgrade-with-powershell"></a>Frissítés a PowerShell-lel

Ha GPv1- vagy Blob Storage-fiókját a PowerShell segítségével kívánja GPv2-fiókra frissíteni, először frissítse a PowerShellt, hogy az az **AzureRm.Storage** modul legfrissebb verzióját használja. A PowerShell telepítésével kapcsolatos információkért lásd [az Azure PowerShell telepítését és konfigurálását](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ismertető cikket. Ezután a fiók frissítéséhez hívja meg a következő parancsot, amelybe helyettesítse be az erőforráscsoportja és a tárfiókja nevét:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

#### <a name="upgrade-with-azure-cli"></a>Frissítés az Azure CLI-vel

Ha GPv1- vagy Blob Storage-fiókját az Azure CLI segítségével kívánja GPv2-fiókra frissíteni, először telepítse az Azure CLI legújabb verzióját. A CLI telepítésével kapcsolatban lásd [az Azure CLI 2.0-s verziójának telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető szakaszt. Ezután a fiók frissítéséhez hívja meg a következő parancsot, amelybe helyettesítse be az erőforráscsoportja és a tárfiókja nevét:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```` 

### <a name="general-purpose-v1"></a>Általános célú v1

Az általános célú v1- (GPv1-) fiókok hozzáférést biztosítanak az összes Azure Storage-szolgáltatáshoz, de előfordulhat, hogy nem állnak rendelkezésre a legújabb funkciók vagy a legalacsonyabb gigabájtonkénti díjszabás. A ritkán használt és az archivált adatok tárolása például a GPv1 esetében nem támogatott. A GPv1-tranzakciók díjszabása alacsonyabb, így magas adatváltozású vagy magas olvasási sebességű számítási feladatok esetében ennek a fióktípusnak a használata előnyösebb lehet.

Az általános célú v1 (GPv1) a legrégebbi tárfióktípus, és az egyetlen olyan altípus, amely a klasszikus üzemi modellben használható. 

### <a name="blob-storage-accounts"></a>Blob Storage-fiókok

A Blob Storage-fiókok ugyanazokat a blokkblob-szolgáltatásokat támogatják, mint a GPv2-fiókok, viszont kizárólag a blokkblobok használatát támogatják. A díjszabás általánosságban az általános célú v2-fiókokéhoz hasonló. Az ügyfeleknek érdemes áttekinteni a Blob Storage- és a GPv2-fiókok közötti árképzési eltéréseket, és megfontolniuk a GPv2-re történő frissítést. Ez a frissítés nem vonható vissza.

> [!NOTE]
> A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem.
>
> A Microsoft a legtöbb esetben az általános célú v2-tárfiókok használatát javasolja a Blob Storage-fiókokkal szemben.

## <a name="recommendations"></a>Javaslatok

A tárfiókokkal kapcsolatosan további információt [az Azure Storage-fiókok ismertetésében](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) talál.

A legújabb blokk- és hozzáfűzőblob-szolgáltatásokat igénylő alkalmazásokhoz javasoljuk a GPv2-tárfiókok használatát, így kiaknázhatóak a szintekre bontott tárolás differenciált árképzési modelljének előnyei. Bizonyos esetekben azonban érdemes a GPv1-et használni, például:

* Ha továbbra is a klasszikus üzemi modellt kell alkalmaznia. A GPv2- és a Blob Storage-fiókok kizárólag az Azure Resource Manager-alapú üzemi modellben érhetők el.
* Ha sok tranzakciót vagy georeplikációs sávszélességet használ (a kapcsolódó költségek egyaránt magasabbak a GPv2- és Blob Storage-fiókok esetében, mint a GPv1-nél), és nem áll rendelkezésre elegendő olyan tárterület, amelyen az alacsonyabb gigabájtonkénti tárolási költség előnyeit kihasználhatná.
* A [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-nél korábbi verzióját vagy egy 4.x-nél korábbi verziójú ügyfélkódtárat használ, és nem tudja frissíteni az alkalmazást.

## <a name="pricing-and-billing"></a>Árak és számlázás
Az összes tárfiók az egyes blobok szintjén alapuló árképzési modellt alkalmaz a blobtároláshoz. Tárfiókok használatakor az alábbi számlázási szempontok érvényesülnek:

* **Tárolási költségek**: Az adattárolás díja a tárolt adatok mennyisége mellett a tárolási szinttől függ. A gigabájtonkénti költség csökken, ha a szint ritkábban használt adatokat tárol.
* **Adathozzáférési költségek**: az adathozzáférési költségek emelkednek, ha a szint ritkábban használt adatokat tárol. A ritka elérésű és az archív tárolási szint esetében gigabájtonkénti adathozzáférési díjat kell fizetni az adatolvasásokért.
* **Tranzakciós költségek**: Minden szint esetében tranzakciónkénti díjat kell fizetni, ez emelkedik, ha a szint ritkábban használt adatokat tárol.
* **Georeplikációs adatátviteli költségek**: Ez csak a georeplikációval konfigurált fiókok esetében érvényes, beleértve a GRS-t és az RA-GRS-t. A georeplikációs adatátvitel gigabájtonkénti díj ellenében érhető el.
* **Kimenő adatátviteli költségek**: A kimenő adatátvitel (azaz az adott Azure-régióból kivitt adatok) esetében gigabájtalapú sávszélesség-használati díjak lépnek fel, csakúgy, mint az általános célú tárfiókok esetében.
* **A tárolási szint módosítása**: a fiók tárolási szintjének a ritka elérésű szintről gyakori elérésűre váltása esetében felmerülő díj megegyezik a tárfiókban lévő összes adat beolvasásának költségével. A fiók tárolási szintjének gyakori elérésűről ritka elérésűre való váltása esetében felmerülő díj viszont az összes adat ritka elérésű szintre írásának költségével egyezik meg (csak a GPv2-fiókok esetében).

> [!NOTE]
> A tárfiókok árképzési modelljével kapcsolatos további információért lásd [az Azure Storage díjszabását](https://azure.microsoft.com/pricing/details/storage/) ismertető lapot. A kimenő adatátviteli díjakkal kapcsolatos további információért lásd az [adatátviteli díjszabást](https://azure.microsoft.com/pricing/details/data-transfers/) ismertető lapot.

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

Ebben a szakaszban a következő forgatókönyveket mutatjuk be az Azure Portal használatával:

* [GPv2-tárfiók létrehozása.](#create-a-gpv2-storage-account-using-the-azure-portal)
* [GPv1- vagy Blob Storage-fiók konvertálása GPv2-tárfiókra.](#convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal)
* [Fiókszint beállítása GPv2- vagy Blob Storage-fiókokban.](#change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal)
* [Blobszint beállítása GPv2- vagy Blob Storage-fiókokban.](#change-the-storage-tier-of-a-blob-using-the-azure-portal)

A következő példákban nem állíthatja a hozzáférési szintet archívra, mivel ez a beállítás a teljes tárfiókra vonatkozik. Az archív réteg csak egy adott blobra vonatkozóan állítható be.

### <a name="create-a-gpv2-storage-account-using-the-azure-portal"></a>GPv2-tárfiók létrehozása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Központ menüben válassza az **Erőforrás létrehozása** > **Adatok + tárolás** > **Tárfiók** elemet.

3. Adja meg a tárfiók nevét.

    A névnek globálisan egyedinek kell lennie. A név része lesz a tárfiókban található objektumok elérése használt URL-címnek.  

4. Válassza a **Resource Manager** lehetőséget üzemi modellként.

    A többszintű tárolás csak a Resource Manager tárfiókjaival használható. A Resource Manager az új erőforrások ajánlott üzembehelyezési modellje. További információért lásd [az Azure Resource Manager áttekintését](../../azure-resource-manager/resource-group-overview.md).  

5. A **Fiók típusa** legördülő listában válassza az **Általános célú v2** elemet.

    Ha a GPv2-t választja, a teljesítményszint beállítása Standard lesz. A többszintű tárolás nem érhető el a Prémium teljesítményszinten.

6. Válassza ki a tárfiók replikálási beállítását: **LRS**, **ZRS**, **GRS** vagy **RA-GRS**. Az alapértelmezett beállítás az **RA-GRS**.

    LRS = helyileg redundáns tárolás, ZRS = zónaredundáns tárolás, GRS = georedundáns tárolás (két régió), RA-GRS = olvasási hozzáférésű georedundáns tárolás (két régió, olvasási hozzáférés a másodikhoz).

    Az Azure Storage replikálási beállításaival kapcsolatos további részletek: [Azure Storage replication](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Az Azure Storage replikációja).

7. Válassza ki az igényeinek megfelelő tárolási szintet: a **Hozzáférési szint** beállításnál válassza a **Ritka** vagy a **Gyakori** lehetőséget. Az alapértelmezett réteg a **gyakran használt adatok**.

8. Válassza ki az előfizetést, amelyikben az új tárfiókot létre szeretné hozni.

9. Adjon meg egy új erőforráscsoportot, vagy válasszon ki egy meglévőt. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).

10. Válassza ki a tárfiók régióját.

11. Kattintson a **Létrehozás** parancsra a tárfiók létrehozásához.

### <a name="convert-a-gpv1-or-blob-storage-account-to-a-gpv2-storage-account-using-the-azure-portal"></a>GPv1- vagy Blob Storage-fiók konvertálása GPv2-tárfiókra az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Tárfiókjának eléréséhez válassza a **Minden erőforrás** lehetőséget, majd válassza ki a tárfiókját.

3. A Beállítások szakaszában kattintson a **Konfiguráció** elemre.

4. A **Fiók típusa** területen kattintson a **Frissítés** elemre.

5. A **Frissítés megerősítése** területen írja be a fiók nevét. 

5. Kattintson a Frissítés gombra a panel alján.

### <a name="change-the-storage-tier-of-a-gpv2-storage-account-using-the-azure-portal"></a>A GPv2-tárfiók tárolási szintjének módosítása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Tárfiókjának eléréséhez válassza a **Minden erőforrás** lehetőséget, majd válassza ki a tárfiókját.

3. A Beállítások panelen kattintson a **Konfiguráció** elemre a fiók konfigurációjának megtekintéséhez és/vagy megváltoztatásához.

4. Válassza ki az igényeinek megfelelő tárolási szintet: a **Hozzáférési szint** beállításnál válassza a **Ritka** vagy a **Gyakori** lehetőséget.

5. Kattintson a panel tetején lévő Mentés elemre.

### <a name="change-the-storage-tier-of-a-blob-using-the-azure-portal"></a>Blob tárolási szintjének módosítása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A tárfiókban található blobja eléréséhez válassza a **Minden erőforrás** lehetőséget, majd válassza ki a tárfiókot, a tárolót és végül a blobot.

3. A Blob tulajdonságai panelen kattintson a **Hozzáférési szint** legördülő menüre, és válassza a **Gyakori**, **Ritka** vagy **Archív** tárolási szintet.

5. Kattintson a panel tetején lévő Mentés elemre.

> [!NOTE]
> A tárolási szintek módosítása további díjakat vonhat maga után. További részleteket az [árakat és számlázást](#pricing-and-billing) ismertető szakaszban olvashat.


## <a name="evaluating-and-migrating-to-gpv2-storage-accounts"></a>A lehetőség mérlegelése és migrálás GPv2-tárfiókokra
Ennek a résznek az a célja, hogy segítséget nyújtson a felhasználóknak a GPv1-tárfiókokról a GPv2-tárfiókok használatára való zökkenőmentes váltásban. Két felhasználói forgatókönyv közül választhat:

* Rendelkezik egy meglévő GPv1-tárfiókkal, és szeretné kiértékelni a GPv2-tárfiók megfelelő tárolási szintjére való áttérést.
* Úgy döntött, hogy GPv2-tárfiókot fog használni, vagy már van egy ilyen fiókja, és szeretné meghatározni, hogy a gyakori vagy a ritka elérésű tárolási szintet célszerű-e használnia.

Mindkét esetben a legfontosabb prioritás a GPv2-tárfiókban tárolt adataihoz kapcsolódó tárolási és hozzáférési költségek kiszámítása, és az eredmények összehasonlítása az aktuális költségekkel.

## <a name="evaluating-gpv2-storage-account-tiers"></a>A GPv2-tárfiókok szintjeinek kiértékelése

A GPv2-tárfiókban lévő adatok tárolási és hozzáférési költségeire vonatkozó becslés elkészítéséhez ki kell értékelnie a jelenlegi használati módot vagy a várható használati módot. Általában a következőket érdemes figyelembe venni:

* Tárhelyhasználat – Mennyi adatot tárol, és ez milyen mértékben változik havi szinten?
* Tároló-hozzáférési minta – Mennyi adatot olvas és ír a fiókba (beleértve az új adatokat is)? Hány tranzakciót használ az adatok eléréséhez, és ezek milyen típusú tranzakciók?

## <a name="monitoring-existing-storage-accounts"></a>A meglévő tárfiókok figyelése

A meglévő tárfiókok monitorozásához és az ezzel kapcsolatos adatgyűjtéshez nyújt segítséget az Azure Storage Analytics, amellyel naplózhatja a tárfiókokat, és megnézheti a fiókokra vonatkozó mérőszámokat. A Storage Analytics olyan mérőszámokat tárol, amelyek a tárolási szolgáltatáshoz érkező kérések összesített tranzakcióstatisztikáját és kapacitási adatait tartalmazzák mind a GPv1 és a GPv2, mind a Blob Storage fióktípus esetében. Ezeket az adatokat a jól ismert táblák tárolják az adott tárfiókban.

További információért lásd [a Storage Analytics mérőszámainak áttekintését](https://msdn.microsoft.com/library/azure/hh343258.aspx) és [a Storage Analytics mérőszámainak táblasémáját](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> A Blob Storage-tárfiókok a tábla szolgáltatásvégpontját csak az adott fiók mérőszámadatainak tárolásához és eléréséhez jelenítik meg. 

A Blob Storage tárolófelhasználásának figyeléséhez engedélyeznie kell a kapacitási mérőszámot.
Ha ez a mérőszám engedélyezve van, a rendszer naponta rögzíti a tárfiók blobszolgáltatásának kapacitásadatait, és létrehoz egy táblabejegyzést az adott tárfiók *$MetricsCapacityBlob* táblájában.

A Blob Storage adathozzáférési mintáinak figyeléséhez engedélyeznie kell az óránkénti tranzakciók mérőszámát az API szintjén. Ha az óránkénti tranzakciók mérőszáma engedélyezve van, a rendszer óránként összesíti az API-tranzakciókat, és táblabejegyzést hoz létre az adott tárfiók *$MetricsHourPrimaryTransactionsBlob* táblájában. A *$MetricsHourSecondaryTransactionsBlob* tábla a másodlagos végpontra rögzíti a tranzakciókat RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Ha egy általános célú tárfiókkal rendelkezik, amelyben lapblobokat és virtuálisgép-lemezeket, illetve üzenetsorokat, fájlokat vagy táblákat tárol a blokkblobok és a hozzáfűző blobok adatai mellett, akkor ez a becslési folyamat nem alkalmazható. A kapacitási adatok nem tesznek különbséget a blokkblobok és más adattípusok között, és nem adnak meg kapacitási adatokat ez utóbbiakhoz. Ilyen adattípusok használatakor a legutóbb számlázott mennyiségi tételek feltárásához alternatív módszert kell alkalmazni.

Azt javasoljuk, hogy az adatfelhasználás és -hozzáférés megfelelő mintájának előállításához olyan megőrzési időszakot válasszon a mérőszámhoz, amely megfelel az Ön használati szokásainak, és extrapolálja az adatokat. Az egyik lehetőség az, hogy hét napig őrzi meg a mérőszámadatokat, és minden héten összegyűjti az adatokat a hónap végén elvégzendő elemzéshez. A másik lehetőség az, hogy az utolsó 30 nap mérőszámadatait őrzi meg, és a 30 napos időszak végén hajtja végre az adatok összegyűjtését és elemzését.

További tudnivalók a mérőszámadatok engedélyezésével, gyűjtésével és megtekintésével kapcsolatban: [Az Azure Storage mérőszámainak engedélyezése és a mérőszámadatok megtekintése](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Az elemzési adatok tárolása, elérése és letöltése ugyanúgy díjhoz kötött, mint a normál felhasználói adatok használata.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Költségbecslés a használati mérőszámok alapján

#### <a name="storage-costs"></a>Tárolási költségek

A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„data”* sorkulcsú utolsó bejegyzése mutatja a felhasználói adatok által igénybe vett tárolókapacitást. A kapacitási mérőszám *$MetricsCapacityBlob* táblájának *„analytics”* sorkulcsú utolsó bejegyzése mutatja az elemzési naplók által igénybe vett tárolókapacitást.

A felhasználói adatok és az elemzési naplók (ha engedélyezve vannak) által igénybe vett teljes kapacitás alapján lehet megbecsülni a tárfiókban lévő adatok tárolási költségét. Ugyanezzel a módszerrel becsülhetők meg a tárolási költségek a GPv1-tárfiókokban.

#### <a name="transaction-costs"></a>Tranzakciós költségek

A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalBillableRequests”* összege mutatja az adott API tranzakcióinak teljes számát. *Például* az adott időszakba eső *„GetBlob”* tranzakciók száma a *„user;GetBlob”* sorkulcsú bejegyzések összes számlázható kérelmének összege alapján számítható ki.

A Blob Storage-tárfiókok tranzakciós költségeinek kiszámításához a tranzakciókat három csoportra kell felosztania, mivel ezekhez különböző árak tartoznak.

* Írási tranzakciók, például *„PutBlob”*, *„PutBlock”*, *„PutBlockList”*, *„AppendBlock”*, *„ListBlobs”*, *„ListContainers”*, *„CreateContainer”*, *„SnapshotBlob”* és *„CopyBlob”*.
* Törlési tranzakciók, például *„DeleteBlob”* és *„DeleteContainer”*.
* Minden egyéb tranzakció.

A GPv1-tárfiókok tranzakciós költségeinek becsléséhez összesítenie kell az összes tranzakciót, függetlenül a művelettől és az API-tól.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Az adatok hozzáférésének és a georeplikációs adatok átvitelének költségei

Bár a tároló elemzése nem jelzi a tárfiókból olvasott és a tárfiókba írt adatok mennyiségét, a tranzakciók mérőszámának táblája alapján lehetőség van megközelítőleges becslésre. A tranzakciók mérőszámának táblájában az adott API-hoz tartozó bejegyzések *„TotalIngress”* összege mutatja az adott API bejövő adatainak teljes mennyiségét bájtban. Hasonlóképpen a *„TotalEgress”* összege a kimenő adatok teljes mennyiségét mutatja bájtban.

A Blob Storage-tárfiókok adathozzáférési költségeinek kiszámításához a tranzakciókat két csoportra kell felosztania:

* A tárfiókból lekért adatok mennyisége a *„TotalEgress”* összegéből becsülhető meg, elsődlegesen a *„GetBlob”* és a *„CopyBlob”* művelet alapján.
* A tárfiókba írt lekért adatok mennyisége a *„TotalIngress”* összegéből becsülhető meg, elsődlegesen a *„PutBlob”*, a *„PutBlock”*, a *„CopyBlob”* és az *„AppendBlock”* művelet alapján.

A Blob Storage-tárfiókok georeplikációs adatátviteli költségei szintén az írt adatok mennyiségének becslése alapján számítható ki GRS- vagy RA-GRS-tárfiókok használata esetében.

> [!NOTE]
> Ha részletesebb példát szeretne látni a gyakori és ritka elérésű tárolási szint használatához kapcsolódó költségek kiszámítására, tekintse át a *„What are Hot and Cool access tiers and how should I determine which one to use?”* (Mi a gyakori és a ritka elérésű tárolási szint, és hogyan határozhatom meg, hogy melyiket kell használnom?) című gyakori kérdéseket. az [Azure Storage díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/storage/).

## <a name="migrating-existing-data"></a>Meglévő adatok áttelepítése

A GPv1-fiókok egyszerűen, leállás és API-módosítás, valamint adatok migrálása nélkül frissíthetők GPv2-re. Ezen oknál fogva erősen ajánlott a GPv1-fiókokat Blob Storage-fiók helyett GPv2-fiókba migrálni.

Ha azonban Blob Storage-fiókba kell migrálnia, és nem áll módjában GPv2-fiókokat használni, az alábbi utasítások szerint járjon el. 

A Blob Storage-fiókok kifejezetten blokkblobok és hozzáfűző blobok tárolására készültek. A meglévő általános célú tárfiókok, amelyek a blobok mellett táblák, üzenetsorok, fájlok és lemezek tárolását is lehetővé teszik, nem konvertálhatóak Blob Storage-fiókká. A tárolási szintek használatához létre kell hoznia egy új Blob Storage-fiókot, és migrálni meglévő adatait az újonnan létrehozott fiókra. 

A meglévő adatok helyszíni tárolóeszközökről, külső felhőtárolási szolgáltatókból vagy meglévő általános célú Azure-tárfiókokból Blob Storage-fiókokba való áttelepítéséhez az alábbi módszereket használhatja:

### <a name="azcopy"></a>AzCopy

Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. Az AzCopy használatával adatait átmásolhatja Blob Storage-fiókjába meglévő általános célú tárfiókjából, vagy feltölthet adatokat helyi tárolóeszközeiről a Blob Storage-fiókba.

További információt [az AzCopy parancssori segédprogrammal történő adatátvitelt](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

### <a name="data-movement-library"></a>Adatátviteli könyvtár

Az Azure Storage .NET-keretrendszerhez készült adatátviteli kódtára az AzCopyt működtető alapvető adatátviteli keretrendszeren alapul. A könyvtár az AzCopyhoz hasonló nagy teljesítményű, megbízható és könnyű adatátviteli műveletekhez készült. Így az AzCopy által kínált szolgáltatások funkcióit teljes mértékben, natív módon kiaknázhatja az alkalmazásában anélkül, hogy ehhez az AzCopy külső példányait kellene futtatnia vagy felügyelnie.

További információt [az Azure Storage a .Net-keretrendszerhez készült adatátviteli kódtárát](https://github.com/Azure/azure-storage-net-data-movement) ismertető szakaszban talál.

### <a name="rest-api-or-client-library"></a>REST API vagy ügyfélkódtár

Az adatok a Blob Storage-fiókra való áttelepítéséhez létrehozhat egy egyéni alkalmazást az Azure ügyfélkódtárai vagy az Azure Storage szolgáltatások REST API felülete segítségével. Az Azure Storage gazdag ügyfélkódtárakat biztosít több nyelvhez és platformhoz is, beleértve a következőket: .NET, Java, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

További információért lásd [az Azure Blob Storage ismertetését](../blobs/storage-dotnet-how-to-use-blobs.md).

> [!IMPORTANT]
> Az ügyféloldali titkosítással titkosított blobok a titkosítással kapcsolatos metaadatokat a blobbal együtt tárolják. Ha ügyféloldali titkosítással titkosított blobot másol, ügyeljen arra, hogy a másolási művelet megőrizze a blob metaadatait, különös tekintettel a titkosítással kapcsolatos metaadatokra. Ha a blobokat a titkosítási metaadatok nélkül másolja, a blob tartalma nem kérhető le újból. További információt a titkosítással kapcsolatos metaadatokról [az Azure Storage ügyféloldali titkosítását](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ismertető szakaszban talál.

## <a name="faq"></a>GYIK

**A meglévő tárfiókok továbbra is elérhetőek?**

Igen, a meglévő tárfiókok (GPv1) továbbra is elérhetőek változatlan árképzéssel és funkcionalitással. A GPv1-fiókokhoz nem érhető el a tárolási szintek kiválasztásának lehetősége, és a jövőben sem rendelkeznek majd ezzel a képességgel.

**Miért és mikor érdemes elkezdenem GPv2-tárfiókot használni?**

A GPv2-tárfiókok egyedi tulajdonsága, hogy a legkisebb GB-onkénti tárolási költséget biztosítják, emellett pedig iparági szinten is versenyképes tranzakciós és adathozzáférési költségeket garantálnak. A továbbiakban a GPv2-tárfiókot ajánljuk a blobok tárolására, mivel a későbbi képességek, például a módosítási értesítések ezen fióktípus alapján lesznek bevezetve. Azonban Ön döntheti el üzleti igényei alapján, mikor kíván frissíteni. Dönthet például úgy is, hogy a frissítést megelőzően optimalizálja a tranzakciós mintákat.

A GPv2-fiókról GPv1-fiókra való visszalépés nem támogatott, ezért a fiókjai GPv2-fiókra frissítése előtt vegye figyelembe a díjszabás esetleges változásait.

**Frissíthetem meglévő tárfiókomat GPv2-tárfiókra?**

Igen. A GPv1- vagy Blob Storage-fiókok egyszerűen frissíthetők GPv2-re a portálon, vagy a PowerShell, illetve a parancssori felület használatával. 

A GPv2-fiókról GPv1-fiókra való visszalépés nem támogatott, ezért a fiókjai GPv2-fiókra frissítése előtt vegye figyelembe a díjszabás esetleges változásait.

**Tárolhatok objektumokat mindkét tárolási szinten egyazon fiók esetében?**

Igen. A **Hozzáférési szint** attribútum a fiók szintjén beállított alapértelmezett szint, amely a fiókban lévő összes olyan objektumra vonatkozik, amelyhez nem állítottak be szintet. A Blobszintű rétegezés szolgáltatással azonban külön megadhatja a hozzáférési szintet az egyes objektumokhoz, függetlenül attól, hogy a fiókon milyen hozzáférési szint van beállítva. Egy adott fiók tartalmazhat blobokat egyszerre akár mindhárom (gyakori, ritka és archív) szinten is.

**Módosíthatom a GPv2-tárfiókom tárolási szintjét?**

Igen, a fiók tárolási szintjét úgy változtathatja meg, hogy a tárfiókban megadja a **Hozzáférési szint** attribútum kívánt értékét. A fiók tárolási szintjének módosítása az adott fiókban lévő összes olyan objektumra vonatkozik, amelyhez nincs külön szint beállítva. A tárolási szint gyakoriról ritka elérésű szintre váltása költséggel jár az írási műveletek (10 000 műveletenként) vonatkozásában (csak GPv2-tárfiókok esetében), a ritkáról gyakori elérésű szintre való váltás pedig az olvasási műveletek (10 000 műveletenként) és az adatlekérés (GB-onként) vonatkozásában jár költséggel a fiókban lévő összes adat beolvasására vonatkozóan.

**Milyen gyakran módosíthatom a GPv2- vagy Blob Storage-fiókom tárolási szintjét?**

Bár nincs korlátozás meghatározva a tárolási szint módosításának gyakoriságára vonatkozóan, vegye figyelembe, hogy a tárolási szint ritka elérésű szintről gyakorira való váltása jelentős költségekkel járhat. A tárolási szintek közötti gyakori váltás nem javasolt.

**A gyakori elérésű tárolási szinten és a ritka elérésű tárolási szinten eltérően viselkednek a blobok?**

A GPv2- és Blob Storage-fiókok gyakori elérésű tárolási szintjén a blobok ugyanolyan késéssel rendelkeznek, mint a GPv1-tárfiókok esetében. A ritka elérésű tárolási szinten a blobok hasonló késéssel rendelkeznek (ezredmásodpercben), mint a gyakori elérésű szint esetében. Az archív tárolási szinten lévő blobok esetében több óra késés tapasztalható.

A ritka elérésű tárolási szinten a blobok rendelkezésre állási szolgáltatási szintje (SLA) kissé alacsonyabb, mint a gyakori elérésű tárolási szinten. További információt a [tárolók rendelkezésreállási szolgáltatási szintjeit](https://azure.microsoft.com/support/legal/sla/storage) ismertető szakaszban talál.

**Tárolhatok lapblobokat és virtuális gépek lemezeit a Blob Storage-fiókban?**

Nem. A Blob Storage-fiókok csak a blokkblobokat és a hozzáfűző blobokat támogatják, a lapblobokat nem. Az Azure virtuális gépek lemezei lapblobokon alapulnak, ezért a Blob Storage-fiókok nem használhatóak virtuális gépek lemezeinek tárolására. Azonban lehetséges a virtuális gépek lemezeinek biztonsági másolatait blokkblobként tárolni a Blob Storage-fiókokban. Ez az egyik ok, ami miatt érdemes megfontolnia a GPv2 használatát a Blob Storage-fiókokkal szemben.

**Szintezhetem a lapblobokat is a GPv2-tárfiókjaimban?**

Nem. A lapblobok megöröklik a fiók tárolási szintjét, azonban ez a díjszabásukra vagy a rendelkezésre állásukra nincs hatással. A lapblobok hozzáférési szintje nem változtatható gyakori, ritka elérésű vagy archív tárolási szintre. A prémium szintű tárfiókban lévő lapblobokon engedélyezett a Blobszint beállítása művelet, azonban ez csak a prémium szintű lapblob megengedett méretét, IOPS-értékét és sávszélességét határozza meg. További információkért lásd: [Blobszint beállítása](https://docs.microsoft.com/en-us/rest/api/storageservices/set-blob-tier).

**Módosítanom kell a meglévő alkalmazásaimat a GPv2-tárfiókok használatához?**

A GPv2-tárfiókok 100%-ban API-konzisztensek a GPv1- és a Blob Storage-fiókokkal. Ha az alkalmazás blokkblobokat vagy hozzáfűző blobokat használ, és a [Storage szolgáltatások REST API felülete](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014. 02. 14-i vagy újabb verzióját használja, az alkalmazásnak működnie kellene. Ha a protokoll egy régebbi verzióját használja, frissítenie kell az alkalmazást, hogy az az új verziót használja, és mindkét típusú tárfiókkal zökkenőmentesen működjön. Általánosságban véve mindig a legújabb verzió használatát javasoljuk, függetlenül a használt tárfiók típusától.

A GPv2-fiókok tranzakcióinak és sávszélességének díjszabása általában magasabb, mint a GPv1-fiókoké. Ezért a frissítés előtt érdemes lehet optimalizálni a tranzakciós mintákat, ha nem szeretné, hogy nőjön a számla végösszege.

**Változik a felhasználói élmény?**

A GPv2-tárfiókok nagyon hasonlóak a GPv1-tárfiókokhoz, valamint támogatják az Azure Storage minden lényeges szolgáltatását, beleértve a nagy tartósságot, valamint a magas szintű rendelkezésre állást, méretezhetőséget, teljesítményt és biztonságot. A Blob Storage-fiókokra jellemző konkrét szolgáltatásoktól és korlátozásoktól, valamint a fent kiemelt tárolási szintektől eltekintve minden más változatlan marad a GPv2- vagy Blob Storage-fiókra történő frissítés során.

## <a name="next-steps"></a>További lépések

### <a name="evaluate-blob-storage-accounts"></a>A Blob Storage-fiókok értékelése

[A Blob Storage-fiókok elérhetőségének ellenőrzése régió alapján](https://azure.microsoft.com/regions/#services)

[Aktuális tárfiókjai használatának értékelése az Azure Storage mérőszámainak engedélyezésével](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[A Blob Storage régió szerinti árképzésének megtekintése](https://azure.microsoft.com/pricing/details/storage/)

[Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-gpv2-storage-accounts"></a>A GPv2-tárfiókok használatának első lépései

[Ismerkedés az Azure Blob Storage szolgáltatással](../blobs/storage-dotnet-how-to-use-blobs.md)

[Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Adatátvitel az AzCopy parancssori segédprogrammal](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[A tárfiókok tallózása és felfedezése](http://storageexplorer.com/)
