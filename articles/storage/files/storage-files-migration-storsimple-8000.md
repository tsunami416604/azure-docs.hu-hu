---
title: StorSimple 8000 sorozat migrálása Azure File Syncre
description: Megtudhatja, hogyan telepíthet át egy StorSimple 8100-es vagy 8600-es készüléket Azure File Syncba.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 046cca4e683a8f14893bf48ac8601b138a7c28a7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630277"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 és 8600 Migrálás Azure File Syncre

Az StorSimple 8000 sorozatot a 8100, a 8600 fizikai, a helyszíni készülékek és a Cloud Service-összetevők jelölik. Ezeknek a készülékeknek az adatait áttelepítheti egy Azure File Sync környezetbe. A Azure File Sync az alapértelmezett és a stratégiai hosszú távú Azure-szolgáltatás, amelyet a StorSimple berendezések áttelepíthetnek.

A StorSimple 8000 sorozat a 2022 decemberében ér [véget](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) . Fontos, hogy a lehető leghamarabb megkezdje az áttelepítés megtervezését. Ez a cikk a Azure File Syncba való sikeres áttelepítéshez szükséges háttérbeli ismereteket és áttelepítési lépéseket ismerteti.

## <a name="phase-1-prepare-for-migration"></a>1. fázis: Felkészülés az áttelepítésre

Ez a szakasz azokat a lépéseket tartalmazza, amelyeket a StorSimple-kötetekről az Azure-fájlmegosztásba való áttelepítés kezdetekor kell végrehajtania.

### <a name="inventory"></a>Leltár

Amikor megkezdi az áttelepítés megtervezését, először azonosítsa az összes áttelepíteni kívánt StorSimple-berendezést és kötetet. Ezt követően eldöntheti, hogy a legjobb áttelepítési útvonalat választotta-e.

* A StorSimple fizikai berendezések (8000 sorozat) ezt az áttelepítési útmutatót használják.
* A Virtual Appliances, [a StorSimple 1200 sorozat egy másik áttelepítési útmutatót használ](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Áttelepítési ár összegzése

A StorSimple-kötetek Azure-fájlmegosztás felé történő áttelepítése a StorSimple Data Manager-erőforrás Adatátalakítási szolgáltatásának feladatain keresztül díjmentes. A Migrálás során és azt követően további költségek merülhetnek fel:

* **Kimenő hálózati forgalom:** A StorSimple-fájlok egy adott Azure-régióban lévő Storage-fiókban laknak. Ha kiépíti az áttelepített Azure-fájlmegosztást egy olyan Storage-fiókba, amely ugyanabban az Azure-régióban található, a kimenő forgalomért nem kell fizetnie. A fájlokat áthelyezheti egy másik régióban lévő Storage-fiókba az áttelepítés részeként. Ebben az esetben a kimenő forgalomra vonatkozó költségek Önre lesznek érvényesek.
* **Azure-fájlmegosztás tranzakciói:** Ha a fájlok egy Azure-fájlmegosztás részébe másolódnak (az áttelepítés részeként vagy az egyiken kívül), a rendszer a tranzakciós költségeket a fájlok és a metaadatok írásakor alkalmazza. Ajánlott eljárásként indítsa el az Azure-fájlmegosztást a tranzakció optimalizált szintjére az áttelepítés során. Az áttelepítés befejezése után váltson a kívánt szintjére. A következő fázisok ezt a pontot fogják hívni a megfelelő pontra.
* **Azure-fájlmegosztás szintjeinek módosítása:** Az Azure fájlmegosztás költségei tranzakcióinak szintje változó. A legtöbb esetben költséghatékonyabb lesz az előző pont javaslatainak követésére.
* **Tárolási díj:** Amikor az áttelepítés megkezdi a fájlok másolását egy Azure-fájlmegosztásba, Azure Files a tárolót felhasználja és számlázza.
* **StorSimple:** Amíg nincs esélye a StorSimple-eszközök és a Storage-fiókok kiépítésére, a tárolás, a biztonsági másolatok és a berendezések StorSimple költségeit továbbra is megtörténik.

### <a name="direct-share-access-vs-azure-file-sync"></a>Közvetlen megosztás – hozzáférés vagy Azure File Sync

Az Azure-fájlmegosztás a Fájlszolgáltatások üzembe helyezéséhez szükséges lehetőségek teljes új világát nyitja meg. Az Azure-fájlmegosztás csak egy SMB-megosztás a felhőben, amellyel beállíthatja, hogy a felhasználók közvetlenül az SMB protokollon keresztül férhessenek hozzá az ismerős Kerberos-hitelesítéssel és a meglévő NTFS-engedélyekkel (fájlok és mappák ACL-ekkel), amelyek natív módon működnek. További információ az [Azure-fájlmegosztás identitás-alapú eléréséről](storage-files-active-directory-overview.md).

A közvetlen hozzáférés alternatívája [Azure file Sync](./storage-sync-files-planning.md). Azure File Sync egy közvetlen analóg, amellyel a StorSimple képes gyorsítótárazni a gyakran használt fájlokat a helyszínen.

A Azure File Sync egy Microsoft Cloud Service, amely két fő összetevő alapján érhető el:

* A fájlok szinkronizálása és a felhő szintjei.
* A fájlmegosztás natív tárolóként az Azure-ban, amely több protokollon, például az SMB-és a file REST-en keresztül is elérhető.

Az Azure-fájlmegosztás a tárolt fájlokhoz (például attribútumok, engedélyek és időbélyegek) fontos fájl-megbízhatósági szempontokat tart fenn. Az Azure-fájlmegosztás esetében már nincs szükség egy alkalmazásra vagy szolgáltatásra a felhőben tárolt fájlok és mappák értelmezéséhez. Ezeket natív módon érheti el az ismerős protokollokkal és az ügyfelekkel, például a Windows fájlkezelővel. Az Azure file shares lehetővé teszi az általános célú fájlkiszolgáló-adat és az alkalmazásadatok felhőben történő tárolását. Az Azure-fájlmegosztás biztonsági mentése egy beépített funkció, amelyet a Azure Backup tovább javíthat.

Ez a cikk az áttelepítési lépésekre összpontosít. Ha többet szeretne megtudni Azure File Sync az áttelepítés előtt, tekintse meg a következő cikkeket:

* [Azure File Sync áttekintése](./storage-sync-files-planning.md "Áttekintés")
* [Azure File Sync telepítési útmutató](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple szolgáltatás adattitkosítási kulcsa

Amikor először állítja be a StorSimple-berendezést, létrehozta a szolgáltatás adattitkosítási kulcsát, és arra utasította, hogy biztonságosan tárolja a kulcsot. Ezzel a kulccsal titkosíthatja a társított Azure Storage-fiókban lévő összes olyan adatforrást, amelyben a StorSimple-berendezés tárolja a fájlokat.

A sikeres áttelepítéshez a szolgáltatás adattitkosítási kulcsa szükséges. Most már jó ideje lekérni ezt a kulcsot a rekordokból a leltárban szereplő összes készülékre.

Ha nem találja a kulcsokat a rekordokban, lekérheti a kulcsot a készülékről. Minden berendezés egyedi titkosítási kulccsal rendelkezik. A kulcs lekérése:

* Támogatási kérelem Microsoft Azure a Azure Portalon keresztül. A kérelem tartalmának tartalmaznia kell a StorSimple-eszköz sorozatszámait és a "szolgáltatás adattitkosítási kulcsának lekérésére irányuló kérelmet".
* A StorSimple-támogatási szakember felveszi Önnel a kapcsolatot egy, a Screen Sharing-értekezletre vonatkozó kéréssel.
* Győződjön meg arról, hogy az értekezlet megkezdése előtt [egy soros konzolon](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) vagy egy [távoli PowerShell-munkameneten](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)keresztül csatlakozik a StorSimple berendezéshez.

> [!CAUTION]
> Ha úgy dönt, hogyan csatlakozhat a StorSimple-berendezéshez, vegye figyelembe a következőket:
>
> * A HTTPS-munkameneten keresztüli csatlakozás a legbiztonságosabb és ajánlott lehetőség.
> * Az eszköz soros konzoljának közvetlen csatlakoztatása biztonságos, de a soros konzol hálózati kapcsolókon keresztül történő csatlakoztatása nem.
> * A HTTP-munkamenetek kapcsolatai egy lehetőség, de nincsenek *titkosítva*. Nem ajánlott, kivéve, ha egy zárt, megbízható hálózaton belül vannak használatban.

### <a name="storsimple-volume-backups"></a>StorSimple mennyiségi biztonsági mentések

A StorSimple különbözeti biztonsági másolatokat biztosít a kötet szintjén. Az Azure file shares is rendelkezik ezzel a képességgel, amelyet megosztási pillanatképeknek nevezünk.

Döntse el, hogy a Migrálás részeként van-e lehetőség a biztonsági másolatok áthelyezésére is.

> [!CAUTION]
> Ha a biztonsági mentéseket StorSimple kötetekről kell áttelepítenie, állítsa le.
>
> Jelenleg csak a legutóbbi kötet biztonsági mentését lehet áttelepíteni. A biztonsági másolatok áttelepítésének támogatása 2020 végén érkezik meg. Ha most indítja el, a biztonsági mentések később nem "bekapcsolható". A következő verzióban a biztonsági mentéseket az Azure-fájlmegosztás legrégebbi és legújabb verziójára kell visszajátszani, és az Azure-fájlmegosztás pillanatképeit a közöttük kell elkészíteni.

Ha csak az élő adatokat szeretné áttelepíteni, és nem rendelkezik a biztonsági mentésre vonatkozó követelményekkel, folytassa az útmutató követésével. Ha rövid távú biztonsági mentési adatmegőrzési követelményt használ, mondjuk egy hónap vagy kettő, dönthet úgy, hogy folytatja az áttelepítést, és kiépíti a StorSimple-erőforrásokat az adott időszak után. Ez a módszer lehetővé teszi, hogy az Azure-fájlmegosztás oldalán annyi biztonsági mentési előzményt hozzon létre, amennyire csak szüksége van. Ha mindkét rendszert futtatja, akkor további díjakra is szükség van, ami ezt a módszert nem veszi figyelembe, ha többre van szüksége a biztonsági másolatok rövid távú megőrzéséhez.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Meglévő StorSimple-kötetek leképezése az Azure-fájlmegosztás számára

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Storage-fiókok száma

Az áttelepítés valószínűleg kihasználja a több Storage-fiók üzembe helyezését, amelyek mindegyike kisebb számú Azure-fájlmegosztást tart fenn.

Ha a fájlmegosztás nagymértékben aktív (számos felhasználó vagy alkalmazás használja), akkor két Azure-fájlmegosztás elérheti a Storage-fiókja teljesítménybeli korlátját. Ezért az ajánlott eljárás az, hogy több Storage-fiókba telepítse át őket, amelyek mindegyike saját egyéni fájlmegosztást használ, és általában legfeljebb kettő vagy három megosztást tárol.

Ajánlott eljárás a Storage-fiókok üzembe helyezése egy fájlmegosztás használatával. Ha archiválási megosztással rendelkezik, több Azure-fájlmegosztás is egyesíthető ugyanabba a Storage-fiókba.

Ezek a szempontok többek között a [Felhőbeli hozzáférés](#direct-share-access-vs-azure-file-sync) (Azure-beli virtuális gépen vagy szolgáltatáson keresztül) a Azure file Syncra vonatkoznak. Ha azt tervezi, hogy csak ezen megosztásokon Azure File Sync használni, a többit egyetlen Azure Storage-fiókba csoportosítva is működik. Érdemes lehet azt is figyelembe venni, hogy az alkalmazást a felhőbe kívánja emelni és átirányítani, amely közvetlenül hozzáfér egy fájlmegosztást. Vagy megkezdheti az Azure-ban olyan szolgáltatás használatát, amely a magasabb IOPS és az átviteli sebességek rendelkezésre állását is élvezi.

Ha létrehozta a megosztások listáját, minden egyes megosztást hozzárendel a Storage-fiókhoz, ahol a tároló található.

> [!IMPORTANT]
> Döntse el egy Azure-régiót, és győződjön meg arról, hogy minden egyes Storage-fiók és Azure File Sync erőforrás megfelel a kiválasztott régiónak.

### <a name="phase-1-summary"></a>1. fázis – összefoglalás

Az 1. fázis végén:

* Jó áttekintést nyújt a StorSimple eszközeiről és köteteiről.
* Az Adatátalakítási szolgáltatás készen áll a Felhőbeli StorSimple-kötetek elérésére, mert minden egyes StorSimple-eszközhöz beolvasta a szolgáltatás adattitkosítási kulcsát.
* Rendelkezik egy olyan csomaggal, amelybe át kell telepíteni a köteteket, és azt is, hogyan képezhetők le a kötetek a megfelelő számú Azure-fájlmegosztás és-Storage-fiók számára.

> [!CAUTION]
> Ha a biztonsági másolatokat át kell telepítenie a StorSimple-kötetekről, **itt állítsa le**.
>
> Ez az áttelepítési módszer az Adatátalakítási szolgáltatás új képességeire támaszkodik, amelyek jelenleg nem tudják áttelepíteni a biztonsági mentéseket. A biztonsági másolatok áttelepítésének támogatása 2020 végén érkezik meg. Jelenleg csak az élő adatait lehet áttelepíteni. Ha most indítja el, a biztonsági mentések később nem "bekapcsolható". A biztonsági mentéseket a legrégebbi Azure-fájlmegosztás legrégebbitől a legújabbtól az élő adatokig kell visszajátszania, az Azure fájlmegosztás pillanatképei között.

Ha csak az élő adatokat szeretné áttelepíteni, és nem rendelkezik a biztonsági mentésre vonatkozó követelményekkel, folytassa az útmutató követésével.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>2. fázis: az Azure Storage és az áttelepítési erőforrások üzembe helyezése

Ez a szakasz az Azure-ban szükséges különböző erőforrástípusok üzembe helyezésének szempontjait ismerteti. Egyesek megtartják az adataikat az áttelepítés után, néhányat pedig kizárólag az áttelepítéshez szükséges. Ne kezdje el az erőforrások telepítését, amíg véglegesíti a telepítési tervet. Az üzembe helyezésük után az Azure-erőforrások bizonyos aspektusait nehéz, esetenként lehetetlen megváltoztatnia.

### <a name="deploy-storage-accounts"></a>Storage-fiókok telepítése

Valószínűleg több Azure Storage-fiókot kell telepítenie. A jelen cikk előző szakaszában a telepítési tervnek megfelelően egy kisebb számú Azure-fájlmegosztást fog tárolni. A [tervezett Storage-fiókok telepítéséhez](../common/storage-account-create.md#create-a-storage-account)lépjen a Azure Portal. Vegye figyelembe az új Storage-fiókok következő alapvető beállításait.

#### <a name="subscription"></a>Előfizetés

Használhatja ugyanazt az előfizetést, amelyet a StorSimple-telepítéshez vagy egy másikhoz használt. Az egyetlen korlátozás, hogy az előfizetésnek ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia, mint a StorSimple-előfizetésnek. Az áttelepítés megkezdése előtt érdemes áthelyezni a StorSimple-előfizetést a megfelelő bérlőre. A teljes előfizetést csak át lehet helyezni. Az egyes StorSimple-erőforrások nem helyezhetők át másik bérlőbe vagy előfizetésbe.

#### <a name="resource-group"></a>Erőforráscsoport

Az erőforráscsoportok segítséget nyújtanak az erőforrások és a rendszergazdai kezelési engedélyek szervezésében. Tudjon meg többet [Az Azure-beli erőforráscsoportok](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)használatáról.

#### <a name="storage-account-name"></a>Tárfiók neve

A Storage-fiók neve egy URL-cím részévé válik, és bizonyos jellegű korlátozásokkal rendelkezik. A névadási konvencióban vegye figyelembe, hogy a Storage-fiókok neveinek egyedieknek kell lenniük a világon, csak kisbetűket és számokat kell megadni, 3 – 24 karakter hosszúságú, és nem szabad speciális karaktereket (például kötőjeleket vagy aláhúzásokat) engedélyezni. További információ: [Azure Storage erőforrás-elnevezési szabályok](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Hely

A Storage-fiók helye vagy az Azure-régió nagyon fontos. Ha Azure File Sync használ, az összes Storage-fióknak ugyanabban a régióban kell lennie, mint a Storage Sync Service-erőforrásnak. Az Ön által választott Azure-régiónak a helyi kiszolgálók és felhasználók bezárására vagy központira kell esnie. Az erőforrás üzembe helyezését követően a régiója nem módosítható.

Választhat egy másik régiót, ahol a StorSimple-adatok (Storage-fiók) jelenleg találhatóak.

> [!IMPORTANT]
> Ha egy másik régiót választ a jelenlegi StorSimple-fiókjának a helyén, a kimenő forgalomra vonatkozó költségek az áttelepítés során [lesznek érvényesek](https://azure.microsoft.com/pricing/details/bandwidth) . Az adatközpont elhagyja a StorSimple régiót, és megadja az új Storage-fiók régióját. Ha ugyanabban az Azure-régióban marad, a sávszélességért nem kell fizetnie.

#### <a name="performance"></a>Teljesítmény

Lehetősége van a Premium Storage (SSD) kiválasztására az Azure-fájlmegosztás vagy a standard Storage számára. A standard szintű tárterület több szintet is tartalmaz a [fájlmegosztás számára](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). A standard szintű tárolás a StorSimple-ből áttelepítésre használt legtöbb ügyfél számára megfelelő lehetőség.

Még mindig nem biztos?

* Ha [prémium szintű Azure-fájlmegosztás teljesítményére](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)van szüksége, válassza a Premium Storage lehetőséget.
* Válassza a standard Storage lehetőséget az általános célú fájlkiszolgáló számítási feladataihoz, amely magában foglalja a gyors és az archivált adatok mennyiségét. Akkor is válassza a standard Storage-t, ha a felhőben lévő megosztáson csak a terhelés Azure File Sync.

#### <a name="account-kind"></a>Fiók altípusa

* A standard szintű tároláshoz válassza a *StorageV2 (általános célú v2)* lehetőséget.
* A prémium fájlmegosztás esetében válassza a *FileStorage* lehetőséget.

#### <a name="replication"></a>Replikáció

Több replikációs beállítás is rendelkezésre áll. További információ a különböző replikációs típusokról.

Csak a következő két lehetőség közül választhat:

* *Helyileg redundáns tárolás (LRS)*.
* A *zóna redundáns tárolási (ZRS)* , amely nem érhető el az összes Azure-régióban.

> [!NOTE]
> Csak a LRS és a ZRS redundancia-típusok kompatibilisek a nagyméretű 100-TiB-Capacity Azure-fájlmegosztás-megosztásokkal.

A földrajzi redundáns tárolás (GRS) az összes változatban jelenleg nem támogatott. Később átválthatja a redundanciát, és átválthat a GRS-ra, ha a támogatás az Azure-ban érkezik.

#### <a name="enable-100-tib-capacity-file-shares"></a>100 – TiB kapacitású fájlmegosztás engedélyezése

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Egy rendszerkép, amely a Azure Portal speciális lapját jeleníti meg a Storage-fiók létrehozásához.":::
    :::column-end:::
    :::column:::
        A Azure Portal új Storage-fiók varázslójának **speciális** szakaszában engedélyezheti a **nagyméretű fájlmegosztás** támogatását ebben a Storage-fiókban. Ha ez a beállítás nem érhető el, valószínűleg a helytelen redundancia-típust választotta. Győződjön meg róla, hogy csak a LRS vagy a ZRS lehetőséget választja, hogy elérhetővé váljon ehhez a beállításhoz.
    :::column-end:::
:::row-end:::

A nagyméretű, 100-TiB-Capacity fájlmegosztás számos előnnyel jár:

* A teljesítmény jelentősen megnő a kisebb 5 TiB-kapacitású fájlmegosztás (például 10-szer a IOPS) képest.
* A Migrálás jelentősen gyorsabban befejeződik.
* Győződjön meg arról, hogy a fájlmegosztás elegendő kapacitással rendelkezik ahhoz, hogy az összes áttelepíteni kívánt adatmennyiséget tárolni fogja.
* A jövőbeli növekedés hatálya alá esik.

### <a name="azure-file-shares"></a>Azure-fájlmegosztás

A Storage-fiókok létrehozása után nyissa meg a Storage-fiók **fájlmegosztás** szakaszát, és telepítse a megfelelő számú Azure-fájlmegosztást az 1. fázisból származó áttelepítési terv szerint. Vegye figyelembe a következő alapvető beállításokat az Azure új fájlmegosztás számára.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Egy Azure Portal képernyőkép, amely az új fájlmegosztás felhasználói felületét jeleníti meg.":::
    :::column-end:::
    :::column:::
        </br>**Név**</br>A kisbetűs karakterek, számok és kötőjelek támogatottak.</br></br>**Kvóta**</br>A kvóta itt hasonlítható össze egy Windows Server-példányon található SMB-beli merevlemez-kvótával. Az ajánlott eljárás az, hogy ne állítson be kvótát, mert az áttelepítés és más szolgáltatások sikertelenek lesznek a kvóta elérésekor.</br></br>**Szolgáltatási szintek**</br>Válassza ki az új fájlmegosztás számára **optimalizált tranzakciót** . Az áttelepítés során számos tranzakció fog történni. Hatékonyabban módosíthatja a szintet a számítási feladatok legmegfelelőbb szintjére.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Az áttelepítési feladatokat tároló Azure-Erőforrás neve **StorSimple Data Manager**. Válassza ki az **új erőforrást** , és keressen rá. Ezután válassza a **Létrehozás** elemet.

Ez az ideiglenes erőforrás a koordináláshoz használatos. Az áttelepítés befejezése után kiépíti azt. A StorSimple-fióknak ugyanabban az előfizetésben, erőforráscsoporthoz és régióban kell lennie.

### <a name="azure-file-sync"></a>Azure File Sync

A Azure File Sync a leggyakrabban használt fájlok helyszíni gyorsítótárazását is felveheti. A StorSimple gyorsítótárazási képességeihez hasonlóan a Azure File Sync Cloud rétegű szolgáltatás a helyi hozzáférés késését is biztosítja a Windows Server-példány és a többhelyes szinkronizálás elérhető gyorsítótár-kapacitásának jobb szabályozása érdekében. Ha a helyszíni gyorsítótár a cél, akkor a helyi hálózaton készítsen elő egy Windows Server rendszerű virtuális gépet (a fizikai kiszolgálókat és a feladatátvételi fürtöket is támogatja) megfelelő, közvetlenül csatlakoztatott tárolókapacitással.

> [!IMPORTANT]
> Még ne állítson be Azure File Sync. A Azure File Sync beállítása a megosztás áttelepítésének befejezése után ajánlott. Azure File Sync üzembe helyezése nem kezdődhet az áttelepítés 4. fázisa előtt.

### <a name="phase-2-summary"></a>2. fázis – összefoglalás

A 2. fázis végén üzembe helyezte a Storage-fiókokat és az összes Azure-fájlmegosztást. Emellett StorSimple Data Manager erőforrással is rendelkezik. Az utóbbit a 3. fázisban fogja használni az áttelepítési feladatok konfigurálásakor.

## <a name="phase-3-create-and-run-a-migration-job"></a>3. fázis: áttelepítési feladatok létrehozása és futtatása

Ez a szakasz azt ismerteti, hogyan állítható be áttelepítési feladatok, és hogyan végezhető el egy olyan StorSimple-kötet könyvtárainak leképezve, amelyet a kiválasztott Azure-fájlmegosztás részévé kell másolni. Első lépésként nyissa meg a StorSimple Data Manager, keresse meg a menüben a **Feladatdefiníciók** elemet, majd válassza a **+ feladatdefiníció** lehetőséget. A cél tárolási típus az alapértelmezett **Azure-fájlmegosztás**.

![StorSimple 8000 adatsorozat-áttelepítési feladattípusok.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "A feladatütemezés képernyőképe Azure Portal a megnyíló új feladatdefiníció párbeszédpanelt, amely a következő feladatot kéri: másolás fájlmegosztás vagy blob-tárolóba.")

> [!IMPORTANT]
> Az áttelepítési feladatok futtatása előtt állítsa le a StorSimple-kötetek automatikusan ütemezett biztonsági mentését.

:::row:::
    :::column:::
        ![StorSimple 8000 sorozat áttelepítési feladata.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Egy Adatátalakítási szolgáltatási feladatokhoz tartozó új feladatsor űrlap képernyőképe.")
    :::column-end:::
    :::column:::
        **Feladatdefiníció neve**</br>Ennek a névnek szerepelnie kell az áthelyezett fájlok készletén. Ha az Azure-fájlmegosztás hasonló nevet ad, az jó megoldás. </br></br>**A feladatot futtató hely**</br>Régió kiválasztásakor ugyanazt a régiót kell kiválasztania, mint a StorSimple Storage-fiókja, vagy ha ez nem érhető el, akkor egy régiót kell megadnia. </br></br><h3>Forrás</h3>**Forrás-előfizetés**</br>Válassza ki azt az előfizetést, amelyben a StorSimple Eszközkezelő erőforrást tárolja. </br></br>**StorSimple erőforrás**</br>Válassza ki a StorSimple Eszközkezelő a készülék regisztrálva van. </br></br>**Szolgáltatás adattitkosítási kulcsa**</br>Ha nem találja a kulcsot a rekordokban, tekintse meg a jelen [cikk előző szakaszát](#storsimple-service-data-encryption-key) . </br></br>**Eszköz**</br>Válassza ki azt a StorSimple-eszközt, amely az áttelepíteni kívánt kötetet tárolja. </br></br>**Kötet**</br>Válassza ki a forrás kötetet. Később eldöntheti, hogy szeretné-e áttelepíteni a teljes kötetet vagy alkönyvtárakat a cél Azure-fájlmegosztásba. </br></br><h3>Cél</h3>Válassza ki az előfizetést, a Storage-fiókot és az Azure-fájlmegosztást az áttelepítési feladatok céljaként.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> A rendszer a legutóbbi kötet biztonsági mentését fogja használni az áttelepítés végrehajtásához. Győződjön meg arról, hogy legalább egy kötet biztonsági mentése létezik, vagy a feladat sikertelen lesz. Győződjön meg arról is, hogy a legújabb biztonsági mentés meglehetősen friss, hogy a változás a lehető legkisebbre legyen az élő megosztáson. Az imént létrehozott feladatok futtatása *előtt* érdemes lehet egy másik kötet biztonsági mentését manuálisan elindítani és befejezni.

### <a name="directory-mapping"></a>Címtár-hozzárendelés

A címtár-hozzárendelés nem kötelező az áttelepítési feladatokhoz. Ha üresen hagyja a szakaszt, a rendszer a StorSimple-kötet gyökerében lévő *összes* fájlt és mappát a cél Azure-fájlmegosztás gyökerébe helyezi át. A legtöbb esetben a teljes kötet tartalmának egy Azure-fájlmegosztás keretében történő tárolása nem a legjobb megoldás. Az Azure-ban gyakran több fájlmegosztás között is érdemes megosztani a kötet tartalmát. Ha még nem hozott létre csomagot, először tekintse [meg a StorSimple-kötet leképező Azure-fájlmegosztás számára](#map-your-existing-storsimple-volumes-to-azure-file-shares) című témakört.

Az áttelepítési terv részeként dönthet úgy, hogy a StorSimple-köteten lévő mappákat több Azure-fájlmegosztás között kell megosztani. Ha ez a helyzet, ezt a felosztást a következőket teheti:

1. Több feladat definiálásával telepítheti át a mappákat egy köteten. Mindegyiknek ugyanaz a StorSimple, hanem egy másik Azure-fájlmegosztás is.
1. Pontosan meghatározza, hogy a StorSimple-kötetből mely mappákat kell áttelepíteni a megadott fájlmegosztást a feladatok létrehozási űrlapjának **címtár-hozzárendelési** szakaszának használatával, és az adott [leképezési szemantika](#semantic-elements)követésével.

> [!IMPORTANT]
> Az űrlap elküldésekor nem lehet érvényesíteni az elérési utakat és a leképezési kifejezéseket. Ha a leképezések helytelenül vannak megadva, előfordulhat, hogy a feladat teljesen meghiúsul, vagy nem kívánatos eredményt hoz létre. Ebben az esetben általában törölni kell az Azure-fájlmegosztást, újra létre kell hozni, majd ki kell javítani a leképezési utasításokat egy új áttelepítési feladatokban a megosztáshoz. Az új feladatok rögzített hozzárendelési utasításokkal való futtatása javíthatja az elhagyott mappákat, és a meglévő megosztásba helyezheti azokat. Így azonban csak azok a mappák jelennek meg, amelyek elérési útja miatt nem lettek kihagyva.

#### <a name="semantic-elements"></a>Szemantikai elemek

A leképezés balról jobbra: [\Source Path] \> [\Target Path].

|Szemantikai karakter          | Értelmezés  |
|:---------------------------|:---------|
| **\\**                     | Legfelső szintű kijelző.       |
| **\>**                     | [Forrás] és [cél-hozzárendelés] operátor.     |
|**\|** vagy RETURN (új sor) | Két mappa-hozzárendelési utasítás elválasztója </br>Azt is megteheti, hogy kihagyja ezt a karaktert, és az **ENTER** billentyűt lenyomva lekéri a következő leképezési kifejezést a saját sorában.        |

### <a name="examples"></a>Példák
Áthelyezi a mappa *felhasználói adatokat* a cél fájlmegosztás gyökerébe:
``` console
\User data > \
```
A teljes kötet tartalmát egy új elérési útra helyezi a célfájl megosztásán:
``` console
\ > \Apps\HR tracker
```
Áthelyezi a forrás mappa tartalmát a célfájl egy új elérési útjára:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Több forrás helyszínét rendezi egy új címtár-struktúrába:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Szemantikai szabályok

* A mappa elérési útját mindig a gyökér szintjéhez viszonyítva határozza meg.
* Minden mappa elérési útjának megkezdése egy gyökérszintű kijelzővel ( \\ "").
* Ne szerepeljenek a meghajtóbetűjelek.
* Több elérési út megadásakor a forrás-vagy cél elérési utak nem lehetnek átfedésben:</br>
   Érvénytelen a forrás elérési útjának átfedési példája:</br>
    *\\folder\1 > \\ mappa*</br>
    *\\1. mappa \\ \\ 2 > \\ Mappa2*</br>
   A célként megadott elérési út átfedő példája érvénytelen:</br>
   *\\mappa > \\*</br>
   *\\Mappa2 > \\*</br>
* A nem létező forrásoldali mappák figyelmen kívül lesznek hagyva.
* A célhelyen nem létező mappastruktúrát lesz létrehozva.
* A Windowshoz hasonlóan a mappák neve nem különbözteti meg a kis-és nagybetűket.

> [!NOTE]
> A rendszer nem másolja át az átalakítási feladatban a *\System kötet információs* mappájának tartalmát és a StorSimple-köteten lévő *$Recycle. bin* fájlt.

### <a name="phase-3-summary"></a>3. fázis – összefoglalás

A 3. fázis végén futtathatja az Adatátalakítási szolgáltatás feladatait a StorSimple-kötetekről az Azure-fájlmegosztásba. Mostantól a megosztáshoz (a megosztás áttelepítési feladatainak elvégzése után) Azure File Sync beállítására, illetve az információkkal dolgozó szakemberek és alkalmazások számára az Azure-fájlmegosztás elérésére is koncentrálhat.

## <a name="phase-4-access-your-azure-file-shares"></a>4. fázis: az Azure-fájlmegosztás elérése

Az Azure-fájlmegosztás eléréséhez két fő stratégia van:

* **Azure file Sync** : [Azure file Sync üzembe helyezése](#deploy-azure-file-sync) helyszíni Windows Server-példányon. Azure File Sync rendelkezik a helyi gyorsítótár összes előnyével, akárcsak a StorSimple.
* **Közvetlen megosztás – hozzáférés** : [közvetlen megosztású hozzáférés üzembe helyezése](#deploy-direct-share-access). Akkor használja ezt a stratégiát, ha az adott Azure-fájlmegosztás hozzáférési forgatókönyve nem részesül a helyi gyorsítótárazásban, vagy ha már nem tud helyszíni Windows Server-példányt üzemeltetni. Itt a felhasználók és az alkalmazások továbbra is hozzáférhetnek az SMB protokollon keresztüli SMB-megosztásokhoz. Ezek a megosztások már nem egy helyszíni kiszolgálón, hanem közvetlenül a felhőben is elérhetők.

Meg kell határoznia, hogy melyik lehetőség a legmegfelelőbb az útmutató [1. fázisában](#phase-1-prepare-for-migration) .

A szakasz további része az üzembe helyezési utasításokra összpontosít.

### <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése

Itt az ideje, hogy a Azure File Sync egy részét telepítse.

1. Hozza létre a Azure File Sync felhőalapú erőforrást.
1. Telepítse a Azure File Sync ügynököt a helyszíni kiszolgálóra.
1. Regisztrálja a kiszolgálót a Felhőbeli erőforrással.

Még ne hozzon létre szinkronizálási csoportokat. Az Azure-fájlmegosztás szinkronizálásának beállítása csak akkor történhet meg, ha az áttelepítési feladatok egy Azure-fájlmegosztás esetében befejeződik. Ha az áttelepítés befejeződése előtt kezdte el Azure File Sync használni az áttelepítést, akkor szükségtelenül megnehezíti az áttelepítés elvégzését, mivel nem tudta könnyen megállapítani, hogy mikor kezdeményezték a kivágást.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>A Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Ha módosítani szeretné az Azure-régió adatait az áttelepítés befejezése után, telepítse a Storage Sync szolgáltatást ugyanabban a régióban, mint az áttelepítéshez célként megadott Storage-fiókokat.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Helyszíni Windows Server-példány üzembe helyezése

* Hozza létre a Windows Server 2019 (minimális 2012R2) virtuális gépet vagy fizikai kiszolgálót. A Windows Server feladatátvevő fürtök is támogatottak. Ne használja újra a kiszolgálót a 8100-es vagy a 8600-es StorSimple.
* Közvetlenül csatlakoztatott tároló kiépítése vagy hozzáadása. A hálózatra csatlakoztatott tároló nem támogatott.

Javasoljuk, hogy az új Windows Server-példány számára a StorSimple 8100-as vagy a 8600-es készüléken elérhetőnél nagyobb mennyiségű tárterületet adjon meg a gyorsítótárazáshoz. A Windows Server-példányt ugyanúgy használhatja, mint a StorSimple berendezést. Ha ugyanazt a tárterületet használja, mint a berendezés, a gyorsítótárazási élménynek hasonlónak kell lennie, ha nem ugyanaz. A (z) a Windows Server-példányból is hozzáadhat vagy eltávolíthat tárolót. Ez a funkció lehetővé teszi a helyi kötet méretének és a gyorsítótárazáshoz elérhető helyi tárterület mennyiségének a méretezését.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>A Windows Server-példány előkészítése a fájl-szinkronizáláshoz

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Azure File Sync konfigurálása a Windows Server-példányon

A regisztrált helyszíni Windows Server-példánynak készen kell állnia, és az internethez kell csatlakoznia ehhez a folyamathoz.

> [!IMPORTANT]
> A folytatás előtt el kell végeznie a fájlok és mappák StorSimple áttelepítését az Azure-fájlmegosztást. Győződjön meg arról, hogy a fájlmegosztás nem végez több módosítást.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Ügyeljen arra, hogy bekapcsolja a Felhőbeli rétegek bekapcsolását. A felhő-rétegek a Azure File Sync funkció, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezzen, mint amennyit a felhőben tárolnak, de a teljes névtér elérhető. A helyileg érdekes adat a gyors, helyi hozzáférési teljesítmény érdekében helyileg is gyorsítótárazható. Egy másik ok a Felhőbeli rétegek bekapcsolására ebben a lépésben, hogy jelenleg nem szeretnénk szinkronizálni a fájl tartalmát. Most csak a névteret kell áthelyezni.

### <a name="deploy-direct-share-access"></a>Közvetlen megosztás – hozzáférés üzembe helyezése

:::row:::
    :::column:::
        [![Részletes útmutató és bemutató az Azure-fájlmegosztás közvetlenül az információkkal dolgozó szakemberek és alkalmazások számára történő biztonságos elérhetővé tétele érdekében – kattintson a lejátszáshoz!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Ebből a videóból megtudhatja, hogyan teheti lehetővé az Azure-fájlmegosztás közvetlen biztonságos kihelyezését az információkkal dolgozó szakemberek és alkalmazások számára öt egyszerű lépésben.</br>
        A videó az egyes témakörökre vonatkozó dedikált dokumentációra hivatkozik:

* [Az identitások áttekintése](storage-files-active-directory-overview.md)
* [A tartományhoz való csatlakozás egy Storage-fiókhoz](storage-files-identity-auth-active-directory-enable.md)
* [Az Azure-fájlmegosztás hálózatkezelésének áttekintése](storage-files-networking-overview.md)
* [Nyilvános és magánhálózati végpontok konfigurálása](storage-files-networking-endpoints.md)
* [S2S VPN konfigurálása](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN konfigurálása](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN konfigurálása](storage-files-configure-p2s-vpn-linux.md)
* [A DNS-továbbítás konfigurálása](storage-files-networking-dns.md)
* [Az elosztott fájlrendszer konfigurálása – N](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>4. fázis – összefoglalás

Ebben a fázisban több Adatátalakítási szolgáltatási feladatot hozott létre és futtatott a StorSimple Data Managerban. Ezek a feladatok áttelepítették fájljait és mappáit az Azure-fájlmegosztást. Emellett üzembe helyezte Azure File Sync vagy felkészítette a hálózati és a Storage-fiókokat a közvetlen megosztás eléréséhez.

## <a name="phase-5-user-cut-over"></a>5. fázis: felhasználói kivágás

Ebben a fázisban a Migrálás felfedéséről van szó:

* Tervezze meg az állásidőt.
* A 3. fázisban lévő Adatátalakítási feladatok futtatása során a StorSimple-oldalon létrehozott felhasználók és alkalmazások módosításai is felfoghatók.
* A felhasználók az új Windows Server-példányra Azure File Sync vagy az Azure-fájlmegosztás közvetlen megosztáson keresztüli eléréssel való használatát elmulasztják.

### <a name="plan-your-downtime"></a>A leállás megtervezése

Ez az áttelepítési módszer bizonyos állásidőt igényel a felhasználók és az alkalmazások számára. A cél az állásidő minimálisra tartása. A következő szempontok segíthetnek:

* Az Adatátalakítási feladatok futtatása közben tartsa elérhetővé a StorSimple-köteteket.
* Amikor befejezte az adatáttelepítési feladatok egy megosztáshoz való futtatását, ideje eltávolítani a felhasználói hozzáférést (legalább írási hozzáféréssel) a StorSimple-kötetekből vagy-megosztásokból. A végső RoboCopy az Azure-fájlmegosztást fogja felfogni. Ezt követően elvégezheti a felhasználók kivágását. A RoboCopy futtatásának helye attól függ, hogy Azure File Sync vagy közvetlen megosztású hozzáférést használ-e. A RoboCopy a következő szakasza kiterjed a tárgyra.
* Miután végrehajtotta a RoboCopy szolgáltatást, készen áll arra, hogy az Azure-fájlmegosztás közvetlenül vagy egy SMB-megosztáson keresztül tegye elérhetővé a felhasználókat az Azure File Sync-t futtató Windows Server-példányon. Az elosztott fájlrendszerbeli telepítések gyakran segítenek gyorsan és hatékonyan elvégezni a kivágást. A meglévő megosztási címek konzisztensek maradnak, és az áttelepített fájlokat és mappákat tartalmazó új helyre mutatnak.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Annak megállapítása, hogy a névtér teljes mértékben szinkronizálva van-e a kiszolgálóval

Ha Azure File Synct használ egy Azure-fájlmegosztás esetében, akkor fontos, hogy a teljes névteret a helyi RoboCopy elindítása *előtt* is letöltse a kiszolgálóra. A névtér letöltéséhez szükséges idő az Azure-fájlmegosztás elemeinek számától függ. Két módszer van annak meghatározására, hogy a névtér teljesen megérkezett-e a kiszolgálón.

#### <a name="azure-portal"></a>Azure Portal

A Azure Portal használatával megtekintheti, hogy a névtér teljesen megérkezett-e.

* Jelentkezzen be a Azure Portalba, és lépjen a szinkronizálási csoportba. A szinkronizálási csoport és a kiszolgálói végpont szinkronizálási állapotának bejelölése.
* Az érdekes irány letöltése. Ha a kiszolgálói végpont újonnan lett kiépítve, akkor megjelenik a **kezdeti szinkronizálás** , ami azt jelzi, hogy a névtér még mindig leáll.
Ezt követően a névtér teljes mértékben **Initial sync** ki lesz töltve a kiszolgálón. Most már folytathatja a helyi RoboCopyt is.

#### <a name="windows-server-event-viewer"></a>Windows Server Eseménynapló

A Windows Server-példányon lévő Eseménynapló is megadhatja, ha a névtér teljesen megérkezett.

1. Nyissa meg a **Eseménynapló** , és lépjen az **alkalmazások és szolgáltatások** elemre.
1. Nyissa meg a **Microsoft\FileSync\Agent\Telemetry**.
1. Keresse meg a legutóbbi, **9102** -as eseményt, amely megfelel egy befejezett szinkronizálási munkamenetnek.
1. Válassza a **részletek** lehetőséget, és győződjön meg arról, hogy egy olyan eseményt keres, ahol a **SyncDirection** érték **le van letöltve**.
1. Arra az időre, amikor a névtér befejezte a letöltést a-kiszolgálóra, egyetlen esemény lesz a **forgatókönyv** , a **FullGhostedSync** érték és a **HResult**  =  **0**.
1. Ha kihagyja ezt az eseményt, további **9102 eseményt** is megkereshet a **SyncDirection**  =  **letöltésével** és a **Scenario**  =  **"RegularSync"** forgatókönyvvel. Az események egyikének megkeresése azt is jelzi, hogy a névtér befejezte a letöltést és a szinkronizálást a rendszeres szinkronizálási munkamenetek miatt, függetlenül attól, hogy van-e valami szinkronizálás, vagy sem.

### <a name="a-final-robocopy"></a>A végső RoboCopy

Ezen a ponton a helyszíni Windows Server-példány és a StorSimple 8100-es vagy a 8600-es készülék közötti különbségek vannak.

1. Fel kell vennie azokat a módosításokat, amelyeket a StorSimple a felhasználók vagy alkalmazások a Migrálás közben készítettek.
1. A Azure File Sync használata esetén: a StorSimple-berendezés feltölthető gyorsítótárral vagy a Windows Server-példánnyal, és csak egy olyan névtérrel rendelkezik, amely nem tartalmaz helyileg tárolt tartalmat. Az utolsó RoboCopy segítheti a helyi Azure File Sync gyorsítótárának indítását úgy, hogy a helyileg gyorsítótárazott fájlok tartalmát a rendelkezésre álló értékre húzza, és elfér a Azure File Sync-kiszolgálón.
1. Előfordulhat, hogy egyes fájlokat az Adatátalakítási feladatoknak az érvénytelen karakterek miatt megmaradtak. Ha igen, másolja őket a Azure File Sync-kompatibilis Windows Server-példányra. Később beállíthatja, hogy szinkronizálja őket. Ha nem használ Azure File Sync egy adott megosztáshoz, akkor jobb, ha a fájlok átnevezése érvénytelen karaktereket tartalmaz a StorSimple köteten. Ezután futtassa közvetlenül a RoboCopy parancsot az Azure-fájlmegosztás ellen.

> [!WARNING]
> A RoboCopyt *nem kell* elindítania ahhoz, hogy a kiszolgáló egy teljesen letöltött Azure-fájlmegosztás névterét használja. További információ: [annak meghatározása, hogy a névtér teljes mértékben le legyen-e töltve a kiszolgálóra](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Csak azokat a fájlokat szeretné átmásolni, amelyeket az áttelepítési feladat utolsó futtatása után módosítottak, és azokat a fájlokat, amelyek korábban nem lettek áthelyezve. A Migrálás befejezése után a probléma megoldásához, hogy miért nem mozdultak el később a kiszolgálón. További információ: [Azure file Sync hibaelhárítás](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

A RoboCopy több paraméterrel rendelkezik. Az alábbi példa egy befejezett parancsot és a paraméterek kiválasztásának okait mutatja be.

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Háttér

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy több szálon fusson. Az alapértelmezett érték 8, a maximális érték 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Az állapotot a NAPLÓFÁJLba UNICODE-ként adja vissza (felülírja a meglévő naplót).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      A konzol ablakának kimenete. Egy naplófájlban a kimenettel együtt használatos.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      A RoboCopy szolgáltatást ugyanazon a módban futtatja, amikor a biztonságimásolat-készítő alkalmazás használni fogja. Lehetővé teszi, hogy a RoboCopy olyan fájlokat helyezzen át, amelyekhez az aktuális felhasználónak nincs engedélye.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy csak a forrás (StorSimple-készülék) és a cél (Windows Server Directory) közötti különbözeteket vegye figyelembe.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      A fájl másolatának hűsége (alapértelmezett értéke/COPY: DAT), másolási jelzők: D = adat, A = attribútumok, T = timestamps, S = Security = NTFS ACL, O = tulajdonosi információ, U = naplózási információ.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      MÁSOLJA a fájl összes adatát (ezzel egyenértékű a következő/COPY: DATSOU).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      A címtárak másolásának hűsége (az alapértelmezett érték a/DCOPY: DA), a másolási jelzők: D = az adat, A = attribútumok, A T = timestamps.
   :::column-end:::
:::row-end:::

A RoboCopy parancs forrás-és célhelyének konfigurálásakor ellenőrizze, hogy a forrás és a cél szerkezete megfelel-e a célnak. Ha az áttelepítési feladathoz tartozó címtár-leképezési funkciót használta, a gyökérkönyvtár szerkezete eltérő lehet a StorSimple-kötet struktúrájától. Ha ez a helyzet, előfordulhat, hogy több RoboCopy-feladatra is szüksége van, egyet az egyes alkönyvtárakhoz. Ha nem biztos abban, hogy a parancs a várt módon fog-e elindulni, használhatja a */l* paramétert, amely a parancsot szimulálja anélkül, hogy a módosítások végrehajtása ténylegesen megtörténik.

Ez a RoboCopy parancs/MIR használ, ezért nem helyezi át azokat a fájlokat, amelyek azonosak (például rétegű fájlok). Ha azonban a forrás és a cél elérési útja nem megfelelő, a/MIR a Windows Server-példány vagy az Azure-fájlmegosztás StorSimple is törli, amely nem szerepel a forrás elérési útján. Pontosan meg kell egyezniük ahhoz, hogy a RoboCopy-feladatok elérjék az áttelepített tartalom frissítésének célját, és az áttelepítés során végrehajtott legújabb módosításokat.

Tekintse meg a RoboCopy naplófájlt, és ellenőrizze, hogy a fájlok le vannak-e maradva. Ha problémák jelentkeznek, javítsa ki, majd futtassa újra a RoboCopy parancsot. Ne szüntesse meg a StorSimple-erőforrások kiépítését, mielőtt kijavította az Ön számára fontos fájlok vagy mappák fennálló problémáit.

Ha nem használja a Azure File Sync az adott Azure-fájlmegosztás gyorsítótárba helyezését, hanem a közvetlen megosztás elérését választotta:

1. [Csatlakoztassa az Azure-fájlmegosztást](storage-how-to-use-files-windows.md#mount-the-azure-file-share) hálózati meghajtóként egy helyi Windows-gép számára.
1. Hajtsa végre a RoboCopyt a StorSimple és a csatlakoztatott Azure-fájlmegosztás között. Ha a fájlok nem kerülnek másolásra, javítsa a nevüket a StorSimple oldalon az érvénytelen karakterek eltávolításához. Ezután próbálja megismételni a RoboCopy programot. A korábban felsorolt RoboCopy-parancs többször is futtatható, anélkül, hogy szükségtelenül StorSimple a felidézést.

### <a name="user-cut-over"></a>Felhasználó kivágása

Ha Azure File Sync használ, valószínűleg létre kell hoznia az SMB-megosztásokat azon a Azure File Sync-kompatibilis Windows Server-példányon, amely megfelel a StorSimple-köteteken lévő megosztásoknak. Ezt a lépést előre betöltheti, és megteheti, hogy itt nem veszít időt. Azonban meg kell győződnie arról, hogy a pont előtt senki nem fér hozzá a Windows Server-példány változásaihoz.

Ha van DFS-N üzemelő példánya, akkor a DFN-Namespaces az új kiszolgáló mappájának helyeire irányíthatja. Ha nincs DFS-N üzemelő példánya, és a 8100-es vagy 8600-es készüléket helyileg, egy Windows Server-példánnyal elvégezte, akkor a kiszolgálót kikapcsolhatja a tartományból. Ezután a tartományhoz csatlakoztassa az új Azure File Sync-kompatibilis Windows Server-példányt. A folyamat során a kiszolgálónak ugyanazt a kiszolgálónevet és neveket kell megadnia, mint a régi kiszolgálót, így a felhasználók, a csoportházirend és a parancsfájlok átláthatóbb maradnak.

További információ az [elosztott fájlrendszerről – N](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="deprovision"></a>Megszüntetése

Amikor kiépít egy erőforrást, elveszíti az adott erőforrás és a hozzá tartozó adat konfigurációját. A megszüntetés nem vonható vissza. Ne folytassa, amíg meg nem erősítette a következőket:

* A Migrálás befejeződött.
* Nincsenek függőségek a StorSimple-fájlokon, mappákon vagy mennyiségi biztonsági másolatokon, amelyeket fel szeretne venni.

Mielőtt elkezdené, érdemes megfigyelni az új Azure File Sync üzembe helyezését egy ideig. Ez az idő lehetőséget biztosít az esetlegesen felmerülő problémák kijavítására. Miután megfigyelte Azure File Sync üzemelő példányát legalább néhány napig, megkezdheti az erőforrások kiépítését ebben a sorrendben:

1. A StorSimple Data Manager erőforrás kiépítése a Azure Portal használatával. Az összes DTS-feladatot törölni fogjuk. Nem lehet egyszerűen beolvasni a másolási naplókat. Ha fontosak a rekordok számára, a kivonás előtt kérje le őket.
1. Győződjön meg arról, hogy a StorSimple fizikai berendezések át lettek telepítve, majd törölje a regisztrációját. Ha nem teljesen biztos abban, hogy áttelepítette őket, ne folytassa. Ha kiépíti ezeket az erőforrásokat, miközben továbbra is szükségesek, nem fogja tudni helyreállítani az adatokat vagy azok konfigurációját.<br>Ha szeretné, először kiépítheti a StorSimple mennyiségi erőforrását, ami törli a berendezésen lévő adatmennyiséget. Ez több napot is igénybe vehet, és a készüléken lévő adatfeldolgozás **nem** nulla. Ha ez fontos az Ön számára, akkor az erőforrás-kiépítés és a szabályzatok alapján a lemez lenullázása külön kezelhető.
1. Ha nincs több regisztrált eszköz a StorSimple Eszközkezelő, akkor továbbra is eltávolíthatja a Eszközkezelő erőforrást.
1. Most már ideje törölni a StorSimple Storage-fiókot az Azure-ban. A folytatás előtt állítsa le és erősítse meg a Migrálás befejeződését, és hogy semmi sem függ az adatoktól.
1. Húzza ki a StorSimple fizikai berendezést az adatközpontból.
1. Ha a StorSimple készülék tulajdonosa, a számítógép újrahasznosítja a számítógépet. Ha az eszköz bérelt, tájékoztassa a Bérbeadót, és szükség szerint adja vissza az eszközt.

A Migrálás befejeződött.

> [!NOTE]
> Továbbra is kérdése van vagy probléma merült fel?</br>
> Itt segítünk AzureFilesMigration@microsoft.com .

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg [Azure file Syncával: aka.MS/AFS](./storage-sync-files-planning.md).
* Ismerje meg a [felhőre vonatkozó szintű](storage-sync-cloud-tiering.md) házirendek rugalmasságát.
* Az Azure-fájlmegosztás [Azure Backupának engedélyezése](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) a pillanatképek ütemezett és a biztonsági mentési adatmegőrzési ütemtervek definiálásához.
* Ha a Azure Portal úgy látja, hogy egyes fájlok véglegesen nem szinkronizálhatók, tekintse át a [hibaelhárítási útmutatót](storage-sync-files-troubleshoot.md) a problémák megoldásához szükséges lépésekért.