---
title: StorSimple 8000 sorozat migrálása Azure File Syncre
description: Megtudhatja, hogyan telepíthet át egy StorSimple 8100-es vagy 8600-es készüléket Azure File Syncba.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/16/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: dcb690829aaed1d5a256f53766f05c922f8b2a9a
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331114"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 és 8600 Migrálás Azure File Syncre

Az StorSimple 8000 sorozatot a 8100, a 8600 fizikai, a helyszíni készülékek és a Cloud Service-összetevők jelölik. Az adatok bármelyik készülékről egy Azure File Sync környezetbe telepíthetők át. A Azure File Sync az alapértelmezett és a stratégiai hosszú távú Azure-szolgáltatás, amelyet a StorSimple berendezések áttelepíthetnek.

A StorSimple 8000-es sorozat [életciklusa](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series) december 2022-ig ér véget. Fontos, hogy a lehető leghamarabb megkezdje az áttelepítés megtervezését. Ez a cikk a Azure File Syncba való sikeres áttelepítéshez szükséges háttérbeli ismereteket és áttelepítési lépéseket ismerteti. 

## <a name="phase-1-prepare-for-migration"></a>1. fázis: Felkészülés az áttelepítésre

Ez a szakasz azokat a lépéseket tartalmazza, amelyeket a StorSimple-kötetekről az Azure-fájlmegosztásba való áttelepítés kezdetekor kell végrehajtania.

### <a name="inventory"></a>Leltár
Amikor megkezdi az áttelepítés megtervezését, először azonosítsa az összes áttelepíteni kívánt StorSimple-berendezést és kötetet. Ha ezt elvégezte, eldöntheti, hogy a lehető legjobb áttelepítési útvonalat választotta-e.

* A StorSimple fizikai berendezések (8000 sorozat) ezt az áttelepítési útmutatót használják. 
* A Virtual Appliances, [a StorSimple 1200 sorozat egy másik áttelepítési útmutatót használ](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Áttelepítési ár összegzése
A StorSimple Data Manager-erőforrások Adatátalakítási feladataival a StorSimple-kötetek Azure-fájlmegosztás felé történő áttelepítése díjmentes. Az áttelepítés során és azt követően azonban más költségek is felmerülhetnek:

* **Kimenő hálózati forgalom:** A StorSimple-fájlok egy adott Azure-régióban lévő Storage-fiókban laknak. Ha az Azure-fájlmegosztás áttelepítését egy olyan Storage-fiókba helyezi, amely ugyanabban az Azure-régióban található, a kimenő forgalomért nem kerül sor. A fájlokat áthelyezheti egy másik régióban lévő Storage-fiókba az áttelepítés részeként. Ebben az esetben a kimenő forgalomra vonatkozó költségek Önre lesznek érvényesek.
* **Azure-fájlmegosztás tranzakciói:** Ha a fájlok egy Azure-fájlmegosztás részébe másolódnak (a Migrálás részeként vagy az egyiken kívül), a fájlok és a metaadatok írása folyamatban van. Ajánlott eljárásként indítsa el az Azure-fájlmegosztást a tranzakció optimalizált szintjére az áttelepítés során. Az áttelepítés befejezése után váltson a kívánt szintjére. Az alábbi szakaszok a megfelelő ponton fogják hívni ezt a szakaszt.
* **Azure-fájlmegosztás rétegének módosítása:** Az Azure fájlmegosztás költségei tranzakcióinak szintje változó. A legtöbb esetben költséghatékonyabb lesz az előző pont javaslatainak követésére.
* **Tárolási díj:** Amikor az áttelepítés megkezdi a fájlok másolását egy Azure-fájlmegosztásba, Azure Files a tárolót felhasználja és számlázza. 
* **StorSimple:** Amíg nincs esélye a StorSimple-eszközök és a Storage-fiókok kiépítésére, a tárolás, a biztonsági másolatok és a berendezések StorSimple költségeit továbbra is megtörténik.

### <a name="direct-share-access-vs-azure-file-sync"></a>Közvetlen megosztási hozzáférés és Azure File Sync
Az Azure-fájlmegosztás a Fájlszolgáltatások üzembe helyezéséhez szükséges lehetőségek teljes új világát nyitja meg. Az Azure-fájlmegosztás csak egy SMB-megosztás a felhőben, amely úgy is beállítható, hogy a felhasználók a megszokott Kerberos-hitelesítéssel és a meglévő NTFS-engedélyekkel (fájlok és mappák ACL-ekkel) is hozzáférjenek az SMB protokollhoz. [További információ az Azure-fájlmegosztás identitás-alapú hozzáféréséről](storage-files-active-directory-overview.md).

A közvetlen hozzáférés alternatívája [Azure file Sync](https://aka.ms/AFS). Azure File Sync egy közvetlen analóg, amellyel a StorSimple képes gyorsítótárazni a gyakran használt fájlokat a helyszínen. 

A Azure File Sync egy Microsoft Cloud Service, amely két fő összetevő alapján érhető el:

* A fájlok szinkronizálása és a felhő szintjei.
* A fájlmegosztás natív tárolóként az Azure-ban, amely több protokollon keresztül érhető el, például az SMB és a file REST használatával. 

Az Azure-fájlmegosztás a tárolt fájlokhoz (például attribútumok, engedélyek és időbélyegek) fontos fájl-megbízhatósági szempontokat tart fenn. Az Azure-fájlmegosztás esetében már nincs szükség egy alkalmazásra vagy szolgáltatásra a felhőben tárolt fájlok és mappák értelmezéséhez. Ezeket natív módon érheti el az ismerős protokollokkal és az ügyfelekkel, például a Windows fájlkezelővel. Az Azure file shares lehetővé teszi az általános célú fájlkiszolgáló-adat és az alkalmazásadatok felhőben történő tárolását. Az Azure-fájlmegosztás biztonsági mentése egy beépített funkció, amelyet a Azure Backup tovább javíthat.

Ez a cikk az áttelepítési lépésekre összpontosít. Ha az áttelepítés előtt szeretne többet megtudni a Azure File Syncről, javasoljuk a következő cikkeket:

* [Azure File Sync – áttekintés](https://aka.ms/AFS "Áttekintés")
* [Azure File Sync – üzembe helyezési útmutató](storage-sync-files-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple szolgáltatás adattitkosítási kulcsa
A StorSimple-berendezés első beállításakor a rendszer létrehozta a "*szolgáltatás adattitkosítási kulcsát*", és arra utasította, hogy biztonságosan tárolja a kulcsot.
Ezzel a kulccsal titkosíthatja a társított Azure Storage-fiókban lévő összes olyan adatforrást, amelyet a StorSimple-berendezés a fájlok tárolására használ.

A sikeres áttelepítéshez a "Service-adattitkosítási kulcs" szükséges. Most már jó ideje lekérni ezt a kulcsot a rekordokból a leltárban szereplő összes készülékre.

Ha nem találja a kulcs (oka) t a rekordokban, lekérheti a kulcsot a készülékről. Minden berendezés egyedi titkosítási kulccsal rendelkezik.

* Támogatási kérelem Microsoft Azure a Azure Portalon keresztül. A kérelem tartalmának rendelkeznie kell a StorSimple-eszközök sorozatszámával és a "szolgáltatás adattitkosítási kulcsának lekérésére irányuló kérelemmel. 
* A StorSimple-támogatási szakember felveszi Önnel a kapcsolatot egy, a Screen Sharing-értekezletre vonatkozó kéréssel.
* Győződjön meg arról, hogy az értekezlet megkezdése előtt [egy soros konzolon](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console) vagy egy [távoli PowerShell-munkameneten](../../storsimple/storsimple-8000-windows-powershell-administration.md#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)keresztül csatlakozik a StorSimple berendezéshez. 

> [!CAUTION]
> Ha úgy dönt, hogyan csatlakozhat a StorSimple-berendezéshez, vegye figyelembe a következőket:
> * A HTTPS-munkameneten keresztüli csatlakozás a legbiztonságosabb és ajánlott lehetőség.
> * Az eszköz soros konzoljának közvetlen csatlakoztatása biztonságos, de a soros konzol hálózati kapcsolókon keresztül történő csatlakoztatása nem. 
> * A HTTP-munkamenetek kapcsolata egy olyan lehetőség, amely **nem titkosított** , ezért nem ajánlott, kivéve, ha zárt, megbízható hálózaton belül használatban van.

### <a name="storsimple-volume-backups"></a>StorSimple mennyiségi biztonsági mentések
A StorSimple különbözeti biztonsági másolatokat biztosít a kötet szintjén. Az Azure file shares is rendelkezik ezzel a képességgel, amelyet megosztási pillanatképeknek nevezünk.

Döntse el, hogy a Migrálás részeként van-e lehetőség a biztonsági másolatok áthelyezésére is.

> [!CAUTION]
> Ha a biztonsági mentéseket StorSimple kötetekről kell áttelepítenie, állítsa le.
>
> Jelenleg csak a legutóbbi kötet biztonsági mentését lehet áttelepíteni. A biztonsági másolatok áttelepítésének támogatása 2020 végén érkezik meg.
> Ha most indítja el, a biztonsági mentések később nem "bekapcsolva".
> A következő verzióban a biztonsági mentéseket az Azure-fájlmegosztás legrégebbi és legújabb verziójára kell visszajátszani, és az Azure-fájlmegosztás pillanatképeit a közöttük kell elkészíteni.

Ha csak az élő adatokat szeretné áttelepíteni, és nem rendelkezik a biztonsági mentésre vonatkozó követelményekkel, folytassa az útmutató követésével.
Ha csak a rövid távú biztonsági másolatok megőrzésére vonatkozó követelményt állítja be, egy hónapot vagy kettőt, akkor dönthet úgy, hogy folytatja az áttelepítést, és csak az adott időszak után hozza létre a StorSimple-erőforrásokat. Ez a megközelítés lehetővé teszi, hogy az Azure-fájlmegosztás oldalán annyi biztonsági mentési előzményt hozzon létre, amennyire csak szüksége van. A két rendszer futtatásának időpontjában további költségeket kell figyelembe vennie, ami nem jelent megoldást, ha többre van szüksége a biztonsági másolatok nagyon rövid távú megőrzéséhez.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Meglévő StorSimple-kötetek leképezése az Azure-fájlmegosztás számára
[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Storage-fiókok száma
A Migrálás várhatóan több Storage-fiók központi telepítésére is kihasználható, amelyek mindegyike kisebb számú Azure-fájlmegosztást tart fenn.

Ha a fájlmegosztás nagymértékben aktív (számos felhasználó és/vagy alkalmazás használja), akkor csak két Azure-fájlmegosztás érheti el a Storage-fiókja teljesítményének korlátját. Ezért az ajánlott eljárás az, hogy több Storage-fiókba telepítse át őket, amelyek mindegyike saját egyéni fájlmegosztást használ, és általában legfeljebb kettő vagy három megosztást tárol.

Ajánlott eljárás a Storage-fiókok üzembe helyezése egy fájlmegosztás használatával. Ha archiválási megosztással rendelkezik, több Azure-fájlmegosztás is egyesíthető ugyanabba a Storage-fiókba.

Ezek a szempontok többek között a [Felhőbeli hozzáférés](#direct-share-access-vs-azure-file-sync) (Azure-beli virtuális gépen vagy szolgáltatáson keresztül) a Azure file Syncra vonatkoznak. Ha csak ezen megosztásokon szeretné használni a Azure File Sync, akkor a többit egyetlen Azure Storage-fiókba csoportosíthatja. Érdemes lehet azt is figyelembe venni, hogy az alkalmazást a felhőbe szeretné emelni és átirányítani, amely közvetlenül hozzáfér egy fájlmegosztást. Vagy megkezdheti az Azure-ban olyan szolgáltatás használatát, amely a magasabb IOPS és az átviteli sebességek rendelkezésre állását is élvezi. 

Ha létrehozta a megosztások listáját, az egyes megosztásokat az abban található Storage-fiókhoz kell rendelnie.

> [!IMPORTANT]
> Győződjön meg arról, hogy egy Azure-régiót, majd minden egyes Storage-fiókot és Azure File Sync erőforrás-egyeztetést használ, és megegyeznek a kiválasztott régióval.

### <a name="phase-1-summary"></a>1. fázis – összefoglalás
Az 1. fázis végén:
* Jó áttekintést nyújt a StorSimple eszközeiről és köteteiről.
* Az Adatátalakítási szolgáltatás készen áll a felhőben lévő StorSimple-kötetek elérésére, mert minden StorSimple-eszközhöz lekérte a szolgáltatás adattitkosítási kulcsát.
* Nem csak a köteteket kell áttelepíteni, hanem azt is, hogyan képezhető le a kötetek a megfelelő számú Azure-fájlmegosztás és-Storage-fiók számára.

> [!CAUTION]
> Ha a biztonsági másolatokat át kell telepítenie a StorSimple-kötetekről, **itt állítsa le**.
>
> Ez az áttelepítési módszer az Adatátalakítási szolgáltatás új képességeire támaszkodik, amelyek jelenleg nem tudják áttelepíteni a biztonsági mentéseket. A biztonsági másolatok áttelepítésének támogatása 2020 végén érkezik meg.
> Jelenleg csak az élő adatait lehet áttelepíteni. Ha most indítja el, a biztonsági mentések később nem "bekapcsolva".
> A biztonsági mentéseket a legrégebbi Azure-fájlmegosztás legrégebbitől a legújabbtól az élő adatokig kell visszajátszania, az Azure fájlmegosztás pillanatképei között.

Ha csak az élő adatokat szeretné áttelepíteni, és nem rendelkezik a biztonsági mentésre vonatkozó követelményekkel, folytassa az útmutató követésével.

## <a name="phase-2-deploying-azure-storage-and-migration-resources"></a>2. fázis: az Azure Storage és az áttelepítési erőforrások üzembe helyezése

Ez a szakasz az Azure-ban szükséges különböző erőforrástípusok üzembe helyezésének szempontjait ismerteti. Egyesek megtartják az adataikat az áttelepítés után, néhányat pedig kizárólag az áttelepítéshez szükségesek. Ne indítsa el az erőforrások telepítését, amíg véglegesíti a telepítési tervet. Nehéz, néha lehetetlen, hogy az üzembe helyezésük után megváltoztassa az Azure-erőforrások bizonyos aspektusait.

### <a name="deploy-storage-accounts"></a>Storage-fiókok telepítése
Valószínűleg több Azure Storage-fiókot kell telepítenie. A jelen cikk előző szakaszában a telepítési tervnek megfelelően egy kisebb számú Azure-fájlmegosztást fog tárolni. A [tervezett Storage-fiókok üzembe helyezéséhez](../common/storage-account-create.md#create-a-storage-account)Azure Portal van. Vegye figyelembe az alábbi, az új Storage-fiók alapszintű beállításait:

##### <a name="subscription"></a>Előfizetés
Használhatja ugyanazt az előfizetést, amelyet a StorSimple-telepítéshez vagy egy másikhoz használt. Az egyetlen korlátozás, hogy az előfizetésnek ugyanahhoz az Azure AD-bérlőhöz kell tartoznia, mint a StorSimple-előfizetésnek. Az áttelepítés megkezdése előtt érdemes áthelyezni a StorSimple-előfizetést a megfelelő bérlőre. Csak a teljes előfizetés helyezhető át, az egyes StorSimple-erőforrások nem helyezhetők át másik bérlőre vagy előfizetésre.

##### <a name="resource-group"></a>Erőforráscsoport
Az erőforráscsoportok segítséget nyújtanak az erőforrások és a rendszergazdai kezelési engedélyek szervezésében. Tudjon meg többet [Az Azure-beli erőforráscsoportok](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)használatáról.

##### <a name="storage-account-name"></a>Tárfiók neve
A Storage-fiók neve egy URL-cím részévé válik, és bizonyos jellegű korlátozásokkal rendelkezik. A névadási konvencióban vegye figyelembe, hogy a Storage-fiókok neveinek egyedieknek kell lenniük a világon, csak kisbetűket és számokat engedélyezzen, 3 – 24 karakter hosszúságot kell megadni, és a speciális karakterek, például kötőjelek vagy aláhúzások nem megengedettek. Lásd még: [Azure Storage erőforrás-elnevezési szabályok](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

##### <a name="location"></a>Hely
A Storage-fiók "location" vagy Azure-régiója nagyon fontos. Ha Azure File Sync használ, az összes Storage-fióknak ugyanabban a régióban kell lennie, mint a Storage Sync Service-erőforrásnak. Az Ön által választott Azure-régiónak a helyi kiszolgálók/felhasználók számára kell lezárulnia vagy középre kell esnie. Miután telepítette az erőforrást, nem módosíthatja a régióját.

Választhat egy másik régiót, ahol a StorSimple-adatok (Storage-fiók) jelenleg találhatóak.

> [!IMPORTANT]
> Ha a jelenlegi StorSimple eltérő régiót választ, a kimenő forgalomra vonatkozó költségek az áttelepítés során [lesznek érvényesek](https://azure.microsoft.com/pricing/details/bandwidth) . Az adatközpont elhagyja a StorSimple régiót, és megadja az új Storage-fiók régióját. Ha ugyanabban az Azure-régióban marad, a sávszélességért nem kell fizetnie.

##### <a name="performance"></a>Teljesítmény
Lehetősége van a Premium Storage (SSD) kiválasztására az Azure-fájlmegosztás vagy a standard Storage számára. A standard szintű tárterület több szintet is tartalmaz a [fájlmegosztás számára](storage-how-to-create-file-share.md#changing-the-tier-of-an-azure-file-share). A standard szintű tárolás a StorSimple-ből áttelepítésre használt legtöbb ügyfél számára megfelelő lehetőség.

Még mindig nem biztos?
* Ha [prémium szintű Azure-fájlmegosztás teljesítményére](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)van szüksége, válassza a Premium Storage lehetőséget.
* Válassza a standard Storage lehetőséget az általános célú fájlkiszolgálók számítási feladataihoz, beleértve a gyors adatmennyiséget és az archiválási adatokhoz. Akkor is válassza a standard Storage-t, ha a felhőben lévő megosztáson csak a terhelés Azure File Sync.

##### <a name="account-kind"></a>Fiók altípusa
* A standard szintű tároláshoz válassza a következőt: *StorageV2 (általános célú v2)*
* Prémium szintű fájlmegosztás esetén válassza a következőt: *FileStorage*

##### <a name="replication"></a>Replikáció
Több replikációs beállítás is rendelkezésre áll. További információ a különböző replikációs típusokról.

Csak a következő két lehetőség közül választhat:
* *Helyileg redundáns tárolás (LRS)*
* *Zone redundáns tárolás (ZRS)* – az összes Azure-régióban nem érhető el.

> [!NOTE]
> Csak a LRS és a ZRS redundancia-típusok kompatibilisek a nagy, 100TiB kapacitású Azure-fájlmegosztás.

A globálisan redundáns tárolás (minden változat) jelenleg nem támogatott. A redundancia-típust később is átválthatja, és GRS, amikor a támogatás az Azure-ban érkezik.

##### <a name="enable-100tib-capacity-file-shares"></a>100TiB-kapacitású fájlmegosztás engedélyezése

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Egy rendszerkép, amely a Azure Portal speciális lapját jeleníti meg a Storage-fiók létrehozásához.":::
    :::column-end:::
    :::column:::
        A Azure Portal új Storage-fiók varázslójának *speciális* szakaszában engedélyezheti a *nagyméretű fájlmegosztás* támogatását ebben a Storage-fiókban. Ha ez a beállítás nem érhető el, valószínűleg a helytelen redundancia-típust választotta. Győződjön meg róla, hogy csak a LRS vagy a ZRS lehetőséget választja, hogy elérhetővé váljon ehhez a beállításhoz.
    :::column-end:::
:::row-end:::

A nagyméretű, 100TiB kapacitású fájlmegosztás használatának számos előnye van:
* A teljesítmény nagy mértékben megnő a kisebb, 5TiB kapacitású fájlmegosztás összevetéséhez képest. (például: 10 alkalommal a IOPS)
* A Migrálás jelentősen gyorsabban befejeződik.
* Győződjön meg arról, hogy a fájlmegosztás elegendő kapacitással rendelkezik ahhoz, hogy az összes áttelepíteni kívánt adatmennyiséget tárolni fogja.
* A jövőbeli növekedés hatálya alá esik.

### <a name="azure-file-shares"></a>Azure-fájlmegosztás
A Storage-fiókok létrehozása után navigáljon a Storage-fiók *"fájlmegosztás"* szakaszába, és telepítse a megfelelő számú Azure-fájlmegosztást az 1. fázisból származó áttelepítési terv szerint. Vegye figyelembe az alábbi, az új fájlmegosztás Azure-beli alapszintű beállításait:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Egy rendszerkép, amely a Azure Portal speciális lapját jeleníti meg a Storage-fiók létrehozásához.":::
    :::column-end:::
    :::column:::
        </br>**Név**</br>Kisbetűk, számok és kötőjelek támogatottak.</br></br>**Kvóta**</br>A kvóta itt hasonlítható össze a Windows Serveren futó SMB-kvótával. Az ajánlott eljárás az, ha nem állít be kvótát, mert az áttelepítés és más szolgáltatások sikertelenek lesznek a kvóta elérésekor.</br></br>**Szint**</br>Válassza ki az új fájlmegosztás számára *optimalizált tranzakciót* . Az áttelepítés során számos tranzakció fog történni, és a rendszer költséghatékonyan módosítja a szintet a számítási feladathoz legjobban illő szinten.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager
Az áttelepítési feladatokat tároló Azure-erőforrás *"StorSimple Data Manager"* néven jelenik meg. Kattintson az új erőforrás lehetőségre, és keresse meg, majd kattintson a *Létrehozás*gombra.

Ez egy olyan ideiglenes erőforrás, amelyet a rendszer az áttelepítés befejeződése után kiépít. A StorSimple-fióknak ugyanabban az előfizetésben, erőforráscsoporthoz és régióban kell lennie.

### <a name="azure-file-sync"></a>Azure File Sync
A Azure File Sync (AFS) lehetővé teszi a leggyakrabban használt fájlok helyszíni gyorsítótárazásának hozzáadását. A StorSimple gyorsítótárazási képességeihez hasonlóan az AFS Cloud rétegű szolgáltatás a helyi hozzáférés késését is biztosítja a Windows Server és a többhelyes szinkronizálás elérhető gyorsítótár-kapacitásának jobb szabályozásával. Ha helyszíni gyorsítótárral rendelkezik, akkor a helyi hálózaton készítse elő a Windows Server rendszerű virtuális gépeket (a fizikai kiszolgálók és a feladatátvételi fürtök is támogatottak), és elegendő "közvetlen csatlakoztatott tároló (DAS)" kapacitással. 

> [!IMPORTANT]
> Ne állítson be Azure File Sync még, érdemes Azure File Sync beállítani a megosztás áttelepítésének befejeződése után. Az AFS üzembe helyezése nem kezdődhet el az áttelepítés 4. fázisa előtt.

### <a name="phase-2-summary"></a>2. fázis – összefoglalás
A 2. fázis végén üzembe helyezte a Storage-fiókokat, az összes Azure-fájlmegosztást, valamint StorSimple Data Manager erőforrást is. Az utóbbit a 3. fázisban fogja használni, amikor ténylegesen konfigurálja az áttelepítési feladatokat.

## <a name="phase-3-create-and-run-a-migration-job"></a>3. fázis: áttelepítési feladatok létrehozása és futtatása
Ez a szakasz azt ismerteti, hogyan állítható be áttelepítési feladatok, és hogyan végezhető el egy olyan StorSimple-kötet könyvtárainak leképezve, amelyet a kiválasztott Azure-fájlmegosztás részévé kell másolni. Első lépésként navigáljon a StorSimple Data Manager, keresse meg a **feladattípusokat** a menüben, majd válassza a **+ feladatdefiníció**elemet. A cél tárolási típus az alapértelmezett: *Azure-fájlmegosztás*.

![StorSimple 8000 adatsorozat-áttelepítési feladattípusok.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "A feladatütemezés képernyőképe Azure Portal egy új feladatdefiníció párbeszédpanel megnyitásával, amely a következő feladatot kéri: másolás fájlmegosztás vagy blob-tárolóba.")

> [!IMPORTANT]
> Az áttelepítési feladatok futtatása előtt állítsa le a StorSimple-kötetek automatikusan ütemezett biztonsági mentését.

:::row:::
    :::column:::
        ![StorSimple 8000 sorozat áttelepítési feladata.](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png "Képernyőkép az Adatátalakítási feladatokhoz tartozó új feladatok létrehozási űrlapról.")
    :::column-end:::
    :::column:::
        **Feladatdefiníció neve**</br>A névnek az áthelyezett fájlok készletének kell lennie. Ha az Azure-fájlmegosztás hasonló nevet ad, az jó megoldás. </br></br>**A feladatot futtató hely**</br>A régió kiválasztásakor ugyanazt a régiót kell kiválasztania, mint a StorSimple, vagy ha ez nem érhető el, akkor egy régiót kell megadnia. </br></br><h3>Forrás</h3>**Forrás-előfizetés**</br>Válassza ki azt az előfizetést, amelyben a StorSimple Eszközkezelő erőforrást tárolja. </br></br>**StorSimple erőforrás**</br>Válassza ki a StorSimple Eszközkezelő a készülék regisztrálva van. </br></br>**Szolgáltatásadat-titkosítási kulcs**</br>Tekintse meg a jelen [cikk előző szakaszát](#storsimple-service-data-encryption-key), ha nem találja a kulcsot a rekordokban. </br></br>**Eszköz**</br>Válassza ki azt a StorSimple-eszközt, amely az áttelepíteni kívánt kötetet tárolja. </br></br>**Kötet**</br>Válassza ki a forrás kötetet. Később eldöntheti, hogy szeretné-e áttelepíteni a teljes kötetet vagy alkönyvtárakat a cél Azure-fájlmegosztásba. </br></br><h3>Cél</h3>Válassza ki az előfizetést, a Storage-fiókot és az Azure-fájlmegosztást az áttelepítési feladatok céljaként.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> A rendszer a legutóbbi kötet biztonsági mentését fogja használni az áttelepítés végrehajtásához. Győződjön meg arról, hogy legalább egy kötet biztonsági mentése létezik, vagy a feladat sikertelen lesz. Győződjön meg arról is, hogy a legújabb biztonsági mentés meglehetősen friss, hogy a változás az élő megosztáson a lehető legkisebb legyen. Az imént létrehozott feladatok futtatása **előtt** érdemes lehet egy másik kötet biztonsági mentését manuálisan elindítani és befejezni.

### <a name="directory-mapping"></a>Címtár-hozzárendelés
Ez nem kötelező az áttelepítési feladatokhoz. Ha üresen hagyja, a rendszer a StorSimple-kötet gyökerében lévő **összes** fájlt és mappát a cél Azure-fájlmegosztás gyökerébe helyezi át. A legtöbb esetben a teljes kötet tartalmának egy Azure-fájlmegosztás keretében történő tárolása nem a legjobb megoldás. Az Azure-ban gyakran több fájlmegosztás között is érdemes megosztani a kötet tartalmát. Ha még nem hozott létre csomagot, tekintse meg ezt a szakaszt először: [a StorSimple-kötet leképezése az Azure-fájlmegosztás számára](#map-your-existing-storsimple-volumes-to-azure-file-shares)

Az áttelepítési terv részeként dönthet úgy, hogy a StorSimple-köteten lévő mappákat több Azure-fájlmegosztás között kell megosztani. Ha ez az eset áll fenn, ezt a felosztást elvégezheti:
1. Több feladat definiálása a mappák egy köteten való átirányításához mindegyiknek ugyanaz a StorSimple, hanem egy másik Azure-fájlmegosztás is.
1. Pontosan meghatározza, hogy a StorSimple-kötetből mely mappákat kell áttelepíteni a megadott fájlmegosztást a feladatok létrehozási űrlapjának *címtár-hozzárendelési* szakaszának használatával, és az adott [leképezési szemantika](#semantic-elements)követésével.

> [!IMPORTANT]
> Az űrlap elküldésekor nem lehet érvényesíteni az ezen az űrlapon található elérési utakat és leképezési kifejezéseket. Ha a leképezések helytelenül vannak megadva, előfordulhat, hogy a feladat teljesen meghiúsul, vagy nem kívánatos eredményt hoz létre. Ebben az esetben általában törölni kell az Azure-fájlmegosztást, újra létre kell hozni, majd egy új áttelepítési feladatokban ki kell javítani a leképezési utasításokat a megosztáshoz. Ha egy új feladatot rögzített hozzárendelési utasításokkal futtat, akkor kijavíthatja az elhagyott mappákat, és a meglévő megosztásba helyezheti azokat. Így azonban csak azok a mappák jelennek meg, amelyek elérési útja miatt nem lettek kihagyva.

#### <a name="semantic-elements"></a>Szemantikai elemek
A leképezés balról jobbra: [\Source Path] \> [\Target Path].

|Szemantikai karakter          | Értelmezés  |
|:---------------------------|:---------|
| **\\**                     | legfelső szintű kijelző        |
| **\>**                     | [forrás] és [Target] leképezési operátor        |
|**\|** vagy RETURN (új sor) | két mappa-hozzárendelési utasítás elválasztója </br>Azt is megteheti, hogy kihagyja ezt a karaktert, majd az ENTER billentyű lenyomásával beolvassa a következő leképezési kifejezést a saját sorában.        |

### <a name="examples"></a>Példák
Áthelyezi a "felhasználói adatokat" mappa tartalmát a célként megadott fájlmegosztás gyökerébe:
``` console
\User data > \\
```
A teljes kötet tartalmát egy új elérési útra helyezi a célfájl megosztásán:
``` console
\ \> \Apps\HR tracker
```
Áthelyezi a forrás mappa tartalmát a célfájl egy új elérési útjára:
``` console
\HR resumes-Backup \> \Backups\HR\resumes
```
Több forrás helyszínét rendezi egy új címtár-struktúrába:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Szemantikai szabályok
* A mappa elérési útját mindig a gyökér szintjéhez viszonyítva határozza meg. 
* Minden mappa elérési útjának megkezdése egy gyökérszintű kijelzővel " \" . 
* Ne tartalmazzon meghajtóbetűjeleket. 
* Több elérési út megadásakor a forrás-vagy cél elérési utak nem lehetnek átfedésben:</br>
   Érvénytelen a forrás elérési útjának átfedési példája:</br>
    *\\folder\1 > \\ mappa*</br>
    *\\1. mappa \\ \\ 2 > \\ Mappa2*</br>
   A célként megadott elérési út átfedő példája érvénytelen:</br>
   *\\mappa > \\*</br>
   *\\Mappa2 > \\*</br>
* A nem létező forrás mappák figyelmen kívül lesznek hagyva. 
* A rendszer létrehozza a célhelyen nem létező mappák-struktúrákat. 
* A Windowshoz hasonlóan: a mappanevek nem megkülönböztetik a kis-és nagybetűket.

> [!NOTE]
> a rendszer nem másolja át az átalakítási feladatban a "*\System Volume Information*" mappa és a StorSimple kötet "*$Recycle. bin*" tartalmát.

### <a name="phase-3-summary"></a>3. fázis – összefoglalás
A 3. fázis végén az Adatátalakítási feladatokat a StorSimple-kötetekről az Azure-fájlmegosztást fogja futtatni. Mostantól a megosztáshoz (a megosztás áttelepítési feladatainak elvégzése után) vagy a közvetlen megosztáshoz való hozzáférés az Azure-fájlmegosztás eléréséhez Azure File Sync is koncentrálhat.

## <a name="phase-4-accessing-your-azure-file-shares"></a>4. fázis: az Azure-fájlmegosztás elérése

Az Azure-fájlmegosztás eléréséhez két fő stratégia van:

* **Azure file Sync:** [Azure file Sync üzembe helyezése](#deploy-azure-file-sync) helyszíni Windows Serveren. Az AFS rendelkezik a helyi gyorsítótár összes előnyével, akárcsak a StorSimple.
* **Közvetlen megosztási hozzáférés:** [közvetlen megosztású hozzáférés üzembe helyezése](#deploy-direct-share-access). Akkor használja ezt a stratégiát, ha az adott Azure-fájlmegosztás hozzáférési forgatókönyve nem fogja kihasználni a helyi gyorsítótárazást, vagy ha már nem tud helyszíni Windows Servert üzemeltetni. Itt a felhasználók és alkalmazások az SMB protokollon keresztül továbbra is hozzáférnek az SMB-megosztásokhoz, de ezek a megosztások már nem egy helyszíni kiszolgálón, hanem közvetlenül a felhőben is elérhetők.

A jelen útmutató [1. fázisában](#phase-1-prepare-for-migration) már eldöntötte, hogy melyik lehetőség a legmegfelelőbb Önnek.

A szakasz további része az üzembe helyezési utasításokra összpontosít.

### <a name="deploy-azure-file-sync"></a>Az Azure File Sync üzembe helyezése

Itt az ideje, hogy a Azure File Sync egy részét telepítse:
1. Hozza létre a Azure File Sync felhőalapú erőforrást.
1. Telepítse a Azure File Sync-ügynököt a helyszíni kiszolgálón.
1. A kiszolgáló regisztrálása a Felhőbeli erőforrással

Még ne hozzon létre szinkronizálási csoportokat. Az Azure-fájlmegosztás szinkronizálásának beállítása csak akkor történhet meg, ha az áttelepítési feladatok egy Azure-fájlmegosztás után befejeződtek. Ha elkezdte a File Sync használatát az áttelepítés befejeződése előtt, akkor szükségtelenül megnehezíti az áttelepítés elvégzését, mivel nem lehet könnyen megállapítani, hogy mikor kell elindulnia.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>A Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Ha azt szeretné, hogy az adatáttelepítés során milyen Azure-régióban tárolja az adatait, akkor a Storage Sync szolgáltatást ugyanabban a régióban kell telepíteni, mint az áttelepítéshez célként megadott Storage-fiókokat.

#### <a name="deploy-an-on-premises-windows-server"></a>Helyszíni Windows Server üzembe helyezése

* Hozzon létre egy Windows Server 2019-at a minimális 2012R2 virtuális gép vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürt is támogatott. Ne használja újra a kiszolgálót a 8100-es vagy a 8600-es StorSimple.
* Hozzon létre vagy adjon hozzá közvetlenül csatlakoztatott tárolót (a DAS-t a NAS-hez képest, amely nem támogatott).

Az ajánlott eljárás, hogy az új Windows Server-kiszolgálót egyenlő vagy nagyobb mennyiségű tárhelyet adjon meg, mint amennyi a StorSimple 8100-es vagy 8600-es készüléke helyileg elérhető a gyorsítótárazáshoz. A Windows Servert ugyanúgy kell használnia, ahogy az StorSimple berendezést használta, ha a berendezés mérete azonos, akkor a gyorsítótárazási élménynek hasonlónak kell lennie, ha nem ugyanaz a tárterület.
A (z) a Windows-kiszolgálóról is hozzáadhat vagy eltávolíthat tárolót. Ez lehetővé teszi a helyi kötet méretének és a gyorsítótárazáshoz elérhető helyi tárterület mennyiségének a méretezését.

#### <a name="prepare-the-windows-server-for-file-sync"></a>A Windows Server előkészítése fájl-szinkronizálásra

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server"></a>Azure File Sync konfigurálása a Windows Serveren

A regisztrált helyszíni Windows Servernek késznek kell lennie az internethez való csatlakozásra ehhez a folyamathoz.

> [!IMPORTANT]
> A folytatás előtt el kell végeznie a fájlok és mappák StorSimple áttelepítését az Azure-fájlmegosztást. Győződjön meg arról, hogy a fájlmegosztás nem végez több módosítást.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Ügyeljen arra, hogy bekapcsolja a Felhőbeli rétegek bekapcsolását. A felhő-rétegek a Azure File Sync funkció, amely lehetővé teszi, hogy a helyi kiszolgáló kevesebb tárolókapacitással rendelkezzen, mint amennyit a felhőben tárolnak, de a teljes névtér elérhető. A helyileg érdekes adat a gyors, helyi hozzáférési teljesítmény érdekében helyileg is gyorsítótárazható. Egy másik ok a Felhőbeli rétegek bekapcsolására ebben a lépésben, hogy jelenleg nem szeretnénk szinkronizálni a fájl tartalmát ebben a fázisban, csak a névteret kell áthelyezni.

### <a name="deploy-direct-share-access"></a>Közvetlen megosztás – hozzáférés üzembe helyezése

:::row:::
    :::column:::
        [![Részletes útmutató és bemutató arról, hogyan teheti lehetővé az Azure-fájlmegosztás közvetlen biztonságos kiküldését az információkkal dolgozó szakemberek és alkalmazások számára – kattintson a lejátszáshoz!](./media/storage-files-migration-storsimple-8000/azure-files-direct-access-video-placeholder.png)](https://youtu.be/KG0OX0RgytI)
    :::column-end:::
    :::column:::
        Ez a videó egy útmutató és bemutató, amely bemutatja, hogyan teheti lehetővé az Azure-fájlmegosztás közvetlen biztonságos kihelyezését az információkkal dolgozó szakemberek és alkalmazások számára öt egyszerű lépésben.</br>
        A videó az egyes témakörökre vonatkozó dedikált dokumentációra hivatkozik:
* [Az identitások áttekintése](storage-files-active-directory-overview.md)
* [A tartományhoz való csatlakozás egy Storage-fiókhoz](storage-files-identity-auth-active-directory-enable.md)
* [Az Azure-fájlmegosztás hálózatkezelésének áttekintése](storage-files-networking-overview.md)
* [Nyilvános és magánhálózati végpontok konfigurálása](storage-files-networking-endpoints.md)
* [S2S VPN konfigurálása](storage-files-configure-s2s-vpn.md)
* [Windows P2S VPN konfigurálása](storage-files-configure-p2s-vpn-windows.md)
* [Linux P2S VPN konfigurálása](storage-files-configure-p2s-vpn-linux.md)
* [A DNS-továbbítás konfigurálása](storage-files-networking-dns.md)
* [Az elosztott fájlrendszer konfigurálása – N](https://aka.ms/AzureFiles/Namespaces)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>4. fázis – összefoglalás
Ebben a fázisban létrehozott és futtatott több *Adatátalakítási szolgáltatás* (DTS) feladatot a *StorSimple Data Manager*. Ezek a feladatok áttelepítették fájljait és mappáit az Azure-fájlmegosztást. Emellett üzembe helyezte Azure File Sync vagy előkészítette a hálózati és a Storage-fiókokat a közvetlen megosztás eléréséhez.

## <a name="phase-5-user-cut-over"></a>5. fázis: felhasználói kivágás
Ebben a fázisban a Migrálás felfedéséről van szó:
* Tervezze meg az állásidőt.
* A 3. fázisban lévő Adatátalakítási feladatok futtatása során a StorSimple-oldalon létrehozott felhasználók és alkalmazások bármilyen módosítását elvégezheti. 
* A közvetlen megosztás-hozzáférés használatával a felhasználók a Azure File Sync vagy az Azure-fájlmegosztás segítségével átvehetik az új Windows Servert.

### <a name="plan-your-downtime"></a>A leállás megtervezése
Ez az áttelepítési módszer bizonyos állásidőt igényel a felhasználók és az alkalmazások számára. A cél az állásidő minimálisra állítása, és a következő megfontolások segíthetnek:

* Az Adatátalakítási feladatok futtatása közben tartsa elérhetővé a StorSimple-köteteket.
* Amikor befejezte az áttelepítési feladatok (ek) egy megosztáshoz való futtatását, ideje eltávolítani a felhasználói hozzáférést (legalább írási hozzáféréssel) a StorSimple-kötetekről/megosztásokról. A végső RoboCopy az Azure-fájlmegosztást fogja felvenni, majd a felhasználókra is kihasználhatja őket. A RoboCopy futtatásának helye attól függ, hogy Azure File Sync vagy közvetlen megosztású hozzáférést használ-e. A RoboCopy következő szakasza tartalmazza ezt:.
* Miután elvégezte a RoboCopy-felfogást, készen áll arra, hogy elérhetővé tegye az új helyet a felhasználóknak. Az Azure-fájlmegosztás közvetlenül vagy SMB-megosztást használ egy Windows Serveren, amely az AFS-t használja. Az elosztott fájlrendszerbeli telepítések gyakran segítenek gyorsan és hatékonyan elvégezni a kivágást. A meglévő megosztási címek konzisztensek maradnak, és egy új helyre mutatnak, amely tartalmazza az áttelepített fájlokat és mappákat.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Annak megállapítása, hogy a névtér teljes mértékben szinkronizálva van-e a kiszolgálóval

Ha Azure File Synct használ egy Azure-fájlmegosztás esetében, akkor fontos, hogy a teljes névteret a helyi RoboCopy elindítása előtt is letöltse a kiszolgálóra. A névtér letöltéséhez szükséges idő az Azure-fájlmegosztás elemeinek számától függ. Két módszer van annak meghatározására, hogy a névtér teljesen megérkezett-e a kiszolgálón:

#### <a name="azure-portal"></a>Azure Portal
A Azure Portal használatával megtekintheti, hogy a névtér teljesen megérkezett-e.
* Jelentkezzen be a Azure Portalba, navigáljon a szinkronizálási csoportba, és jelölje be a szinkronizálási csoport és a kiszolgálói végpont szinkronizálási állapotát. 
* Az érdekes irány letöltése: Ha a kiszolgáló végpontja újonnan lett kiépítve, megjelenik a **kezdeti szinkronizálás** , amely jelzi, hogy a névtér még mindig leáll.
Miután a rendszer bármit megváltoztatott a *kezdeti szinkronizálásnál*, a névtér teljes mértékben ki lesz töltve a kiszolgálón, és jó, ha folytatja a helyi Robocopy szolgáltatást.

#### <a name="windows-server-event-viewer"></a>Windows Server Eseménynapló
a szervezeti egység a Windows Server Eseménynapló is használhatja annak megadásához, hogy a névtér teljesen megérkezett-e.

1. Nyissa meg a **Eseménynapló** , és navigáljon az **alkalmazásokhoz és a szolgáltatásokhoz**.
1. Navigáljon és nyissa meg a **Microsoft\FileSync\Agent\Telemetry**.
1. Keresse meg a legutóbbi, **9102**-as eseményt, amely megfelel egy befejezett szinkronizálási munkamenetnek.
1. Válassza a **részletek** elemet, és erősítse meg, hogy egy olyan eseményt keres, ahol a **SyncDirection** érték **le van letöltve**.
1. Arra az időre, amikor a névtér befejezte a letöltést a kiszolgálóra, egyetlen esemény lesz a **forgatókönyv**, a Value **FullGhostedSync** és a **HResult**  =  **0**.
1. Ha kihagyja ezt az eseményt, további **9102 eseményt** is megkereshet a **SyncDirection**  =  **letöltésével** és a **Scenario**  =  **"RegularSync"** forgatókönyvvel. Az események egyikének megkeresése azt is jelzi, hogy a névtér befejezte a letöltést és a szinkronizálást a rendszeres szinkronizálási munkamenetek miatt, akár szinkronizálni is kell, akár nem.

### <a name="a-final-robocopy"></a>A végső RoboCopy
Ezen a ponton a helyszíni Windows Server és a StorSimple 8100-es vagy 8600-es készülék közötti különbségek vannak:

1. Fel kell vennie azokat a változtatásokat, amelyeket a StorSimple az áttelepítés során a felhasználók/alkalmazások hoztak létre.
1. A Azure File Sync használata esetén: a StorSimple készülék egy feltöltött gyorsítótárral és a Windows Serverrel csak olyan névtérrel rendelkezik, amely nem helyileg tárolt fájl. Így a végső RoboCopy segítheti a helyi AFS-gyorsítótár indítását úgy, hogy a helyileg gyorsítótárazott fájlok tartalmának áthúzásával a rendelkezésre álló és az AFS-kiszolgálón is elfér.
1. Előfordulhat, hogy az Adatátalakítási feladatokban egyes fájlok érvénytelen karakterek miatt megmaradtak. Ha igen, másolja őket a Azure File Sync-kompatibilis Windows Serverre. Később beállíthatja, hogy szinkronizálja őket. Ha nem használ Azure File Synct egy adott megosztáshoz, akkor jobb, ha a fájlokat érvénytelen karakterekkel átnevezi a StorSimple-köteten, majd a RoboCopy szolgáltatást közvetlenül az Azure-fájlmegosztás alapján futtatja. 

> [!WARNING]
> A RoboCopyt nem kell elindítania ahhoz, hogy a kiszolgáló az Azure-fájlmegosztás névterét teljes mértékben letöltse.
> Pénztár: "[annak meghatározása, hogy a névtér teljes mértékben le legyen-e töltve a kiszolgálóra](#determine-when-your-namespace-has-fully-synced-to-your-server)"

 Csak azokat a fájlokat szeretné átmásolni, amelyeket az áttelepítési feladat utolsó futtatása után módosítottak, és korábban nem áthelyezett fájlokat. A Migrálás befejezése után megoldhatja a problémákat, hogy miért nem mozdultak el később a kiszolgálón. Lásd: [Azure file Sync hibaelhárítás](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

A RoboCopy több paraméterrel rendelkezik. A következő bemutat egy befejezett parancsot és egy listát a paraméterek kiválasztásának okairól:

```console
Robocopy /MT:16 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Háttér

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy több szálon fusson. Az alapértelmezett érték 8, Max 128.
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
      a fájlmásolás hűsége (az alapértelmezett érték a/COPY: DAT), a másolási jelzők: D = adat, A = attribútumok, T = időbélyeg, S = biztonság = NTFS ACL, O = tulajdonos adatai, U = naplózási információ
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      A fájl összes adatának másolása (egyenértékű a következő/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      a címtárak másolásának hűsége (az alapértelmezett érték a/DCOPY: DA), a másolási jelzők: D = az adat, A = attribútumok, A T = timestamps
   :::column-end:::
:::row-end:::

A RoboCopy parancs forrás-és célhelyének konfigurálásakor ellenőrizze, hogy a forrás és a cél szerkezete megfelel-e a célnak. Ha az áttelepítési feladathoz tartozó címtár-leképezési funkciót használta, a gyökérkönyvtár szerkezete eltérő lehet a StorSimple-kötet struktúrájától. Ebben az esetben előfordulhat, hogy több RoboCopy-feladatra van szüksége, amelyek mindegyike egy alkönyvtárhoz tartozhat. Mivel ez a RoboCopy parancs a/MIR-t használja, nem helyezi át a fájlokat, amelyek azonosak (például a többrétegű fájlok), de ha a forrás-és a cél elérési útja nem megfelelő, a/MIR a Windows Server/Azure-fájlmegosztás címtár-struktúráját is törli, amelyek nem szerepelnek a StorSimple forrásának elérési útján. Ezért pontosan meg kell egyezniük ahhoz, hogy a RoboCopy-feladatnak elérje azt a célja, hogy az áttelepített tartalmakat csak az áttelepítés során végrehajtott legújabb módosításokkal frissítse. 

Tekintse meg a RoboCopy naplófájlt, és ellenőrizze, hogy a fájlok le vannak-e maradva. Ha problémák jelentkeznek, javítsa ki, majd futtassa újra a RoboCopy parancsot. Ne szüntesse meg a StorSimple-erőforrások kiépítését, mielőtt kijavította az Ön számára fontos fájlok vagy mappák fennálló problémáit.

Ha nem használja a Azure File Sync az adott Azure-fájlmegosztás gyorsítótárazásához, hanem inkább a közvetlen megosztás elérését választotta:
1. [Csatlakoztassa az Azure-fájlmegosztást](storage-how-to-use-files-windows.md#mount-the-azure-file-share) hálózati meghajtóként egy helyi Windows-gép számára.
1. Hajtsa végre a RoboCopyt a StorSimple és a csatlakoztatott Azure-fájlmegosztás között. Ha a fájlok nem másolhatók, javítsa ki a nevüket a StorSimple oldalon az érvénytelen karakterek eltávolításához, majd próbálja megismételni a RoboCopy parancsot. A korábban felsorolt RoboCopy-parancs többször is futtatható, ha a StorSimple szükségtelen visszahívást okoz.

### <a name="user-cut-over"></a>Felhasználó kivágása

Ha Azure File Sync használ, valószínűleg létre kell hoznia az SMB-megosztásokat azon a Windows Serveren, amely megfelel a StorSimple-köteteken található megosztásoknak. Ezt a lépést előre betöltheti, és megteheti, hogy nem veszít időt itt, de meg kell győződnie arról, hogy a jelen pont előtt senki nem fér hozzá a Windows Server módosításához.

Ha van DFS-N üzemelő példánya, akkor a DFN-Namespaces az új kiszolgáló mappájának helyeire irányíthatja. Ha nem rendelkezik DFS-N üzemelő példányokkal, és a 8100/8600-es készüléket helyileg, egy Windows Serveren keresztül telepítette, akkor a kiszolgálót kikapcsolhatja a tartományból. Ezután a tartományhoz csatlakozhat az új, Azure File Sync engedélyezve, a Windows Serverhez. A folyamat során a kiszolgálónak ugyanazt a kiszolgálónevet és neveket kell megadnia a régi kiszolgálóként, így a felhasználók, a csoportházirend és a parancsfájlok átláthatósága továbbra is megmarad.

[További információ az elosztott fájlrendszerről – N](https://aka.ms/AzureFiles/Namespaces)

## <a name="deprovision"></a>Megszüntetése
Amikor kiépít egy erőforrást, elveszíti az adott erőforrás konfigurációját, valamint annak adatait. A megszüntetés nem vonható vissza, ezért csak akkor folytassa, ha megerősítette, hogy az áttelepítés befejeződik, és nincsenek függőségek a StorSimple-fájlokon, mappákon vagy köteteken.

Mielőtt elkezdené, érdemes megfigyelni az új Azure File Sync üzembe helyezését éles környezetben, egy kicsit. Ez lehetőséget biztosít az esetlegesen felmerülő problémák kijavítására.
Miután megfigyelte Azure File Sync üzemelő példányát legalább néhány napig, megkezdheti az erőforrások kiépítését ebben a sorrendben:

1. A StorSimple Data Manager erőforrás kiépítése a Azure Portal használatával.
   Az összes DTS-feladatot törölni fogjuk. Nem lehet egyszerűen beolvasni a másolási naplókat. Ha fontosak a rekordok számára, a kivonás előtt kérje le őket.
1. Győződjön meg arról, hogy a StorSimple fizikai berendezések át lettek telepítve, majd törölje a regisztrációját.
   Ha nem teljesen biztos abban, hogy áttelepítette őket, ne folytassa a művelettel. Ha kiépíti ezeket az erőforrásokat, miközben továbbra is szükségesek, nem fogja tudni helyreállítani az adatokat vagy azok konfigurációját.
1. Ha nincs több regisztrált eszköz a StorSimple Eszközkezelő, akkor továbbra is eltávolíthatja a Eszközkezelő erőforrást.
1. Most már ideje törölni a StorSimple Storage-fiókot az Azure-ban. A folytatás előtt állítsa le és erősítse meg az áttelepítés befejeződését, és semmi sem függ az adatoktól.
1. Húzza ki a StorSimple fizikai berendezést az adatközpontból.
1. Ha a StorSimple készülék tulajdonosa, a számítógép újrahasznosítja a számítógépet. 
   Ha az eszköz bérelt, tájékoztassa a Bérbeadót, és szükség szerint adja vissza az eszközt.

A Migrálás befejeződött.

> [!NOTE]
> Továbbra is kérdései vannak, vagy problémák merültek fel?</br>
> Segítség: AzureFilesMigration@microsoft . com


## <a name="next-steps"></a>További lépések

* Ismerkedjen meg [Azure file Syncával: aka.MS/AFS](https://aka.ms/AFS).
* Ismerje meg a [felhőre vonatkozó szintű](storage-sync-cloud-tiering.md) házirendek rugalmasságát.
* Az Azure-fájlmegosztás [Azure Backupának engedélyezése](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) a pillanatképek ütemezett és a biztonsági mentési adatmegőrzési ütemtervek definiálásához.
* Ha a Azure Portal úgy látja, hogy egyes fájlok véglegesen nem szinkronizálhatók, tekintse át a [hibaelhárítási útmutatót](storage-sync-files-troubleshoot.md) a problémák megoldásához szükséges lépésekért.
