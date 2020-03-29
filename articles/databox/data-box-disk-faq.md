---
title: Microsoft Azure Data Box Disk GYIK | Microsoft Docs in data
description: Gyakran ismételt kérdéseket és válaszokat tartalmaz az Azure Data Box Disk felhőalapú megoldásához, amely lehetővé teszi nagy mennyiségű adat átvitelét az Azure-ba
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 7ba6ea8606fc354527ff4114bc45a0904941ba93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918937"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: gyakori kérdések

A Microsoft Azure Data Box Disk felhőalapú megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba. A GYIK a Data Box Disk-lemezek Azure Portalon történő használatával kapcsolatban esetlegesen felmerülő kérdésekre ad választ. 

A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Tudnivalók a szolgáltatásról
- Konfigurálás és csatlakoztatás 
- Állapot nyomon követése
- Adatok áttelepítése 
- Adatok ellenőrzése és feltöltése 


## <a name="about-the-service"></a>Tudnivalók a szolgáltatásról

### <a name="q-what-is-azure-data-box-service"></a>K. Mi az az Azure Data Box szolgáltatás? 
A.  Az Azure Data Box szolgáltatást offline adatbetöltéshez hoztuk létre. A szolgáltatás termékek egész tárházát kezeli, amelyek mindegyike különböző kapacitású tárolókra történő adatátvitelhez lett igazítva. 

### <a name="q-what-are-azure-data-box-disks"></a>K. Mik azok az Azure Data Box-lemezek?
A. Az Azure Data Box-lemezek több terabájtnyi adat gyors, költséghatékony és biztonságos adatátvitelét teszik lehetővé az Azure-ba, illetve az Azure-ból. A Microsoft elküld Önnek 1–5 lemezt, legfeljebb 35 TB-os tárkapacitással. Az Azure Portalon a Data Box szolgáltatás segítségével könnyedén konfigurálhatja, csatlakoztathatja és feloldhatja ezeket a lemezeket.  

A lemezek a Microsoft BitLocker meghajtótitkosítással vannak titkosítva, amelynek titkosítási kulcsait az Azure Portalon kezelheti. Ezt követően átmásolhatja az adatokat az ügyfél kiszolgálóiról. Az adatközpontban a Microsoft egy gyors és privát hálózati feltöltési kapcsolaton keresztül a lemezről a felhőbe telepíti az adatait, majd feltölti őket az Azure-ba.

### <a name="q-when-should-i-use-data-box-disks"></a>K. Mikor érdemes Data Box Disk-lemezeket használnom?
A. Ha 40 TB-nyi (vagy kevesebb) adata van, amelyet szeretne átvinni az Azure-ba, akkor hasznos lehet a Data Box Disk-lemezek használata.

### <a name="q-what-is-the-price-of-data-box-disks"></a>K. Mennyibe kerülnek a Data Box Disk-lemezek?
A. A Data Box Disks árával kapcsolatos információkért látogasson el [az Árképzés lapra.](https://azure.microsoft.com/pricing/details/databox/disk/)

### <a name="q-how-do-i-get-data-box-disks"></a>K. Hogyan kaphatok Data Box Disk-lemezeket? 
A.  Az Azure Data Box lemezek beszerezhetéséhez jelentkezzen be az Azure Portalra, és hozzon létre egy Data Box-rendelést a lemezekhez. Adja meg a kapcsolattartási és értesítési adatait. A megrendelés elküldése után a rendelkezésre állás függvényében 10 napon belül szállítjuk Önnek a lemezeket.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>K. Egyszerre legfeljebb mennyi adatot vihetek át a Data Box Disks-lemezekkel?
A. 5 darab egyenként 8 TB-os lemez (7 TB-os felhasználható kapacitással) esetén a felhasználható kapacitás legfeljebb 35 TB. Így egyszerre legfeljebb 35 TB-nyi adat vihető át. Több adat átviteléhez több lemez megrendelése szükséges.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>K. Honnan tudhatom meg, hogy a Data Box Disk-lemezek elérhetők-e a régiómban? 
A.  Ha meg szeretné tekinteni, hogy jelenleg hol érhetők el az adatdoboz-lemezek, olvassa el a [Régió elérhetősége című témakört.](data-box-disk-overview.md#region-availability)  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>K. Mely régiókban tárolhatok adatokat a Data Box Disk-lemezekkel?
A. A Data Box Disk az Egyesült Államok, Kanada, Ausztrália, Nyugat-Európa és Észak-Európa, Korea és Japán összes régiójában támogatott. Csak a nyilvános Azure-felhőrégiók támogatottak. Az Azure Government és más független felhők nem támogatottak.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>K. A Data Box Disk áthalad az országon a szállítás során?
A. A Data Box Disk a rendeltetési hely szerinti országból származik, és nem lépi át a nemzetközi határokat. Az egyetlen kivétel az Európai Unióban (EU) érkező megrendelések, ahol a lemezek bármely UNIÓS országba és onnan szállíthatók.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>K. Kihez fordulhatok, ha probléma merülne fel a Data Box Disk-lemezekkel kapcsolatban?
A. Ha bármilyen probléma merül fel a Data Box Disks szolgáltatással kapcsolatban, [forduljon a Microsoft támogatási szolgálatához.](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support)

## <a name="configure-and-connect"></a>Konfigurálás és csatlakoztatás
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>K. Megadhatom a megrendelésben a Data Box Disk-lemezek számát?
A.  Nem. 8 TB-os lemezeket (legfeljebb 5-öt) fog kapni az adatai mérete és a lemezek rendelkezésre állásának függvényében.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>K. Hogyan oldhatom fel a Data Box Disk-lemezeket? 
A.  Nyissa meg az Azure Portalon a Data Box Disk-megrendelését, majd az **Eszköz részletei** lehetőséget. Másol le a jelszót. Töltse le az Azure Portalról az operációs rendszerének megfelelő Data Box Disk lemezzárolás-feloldó eszközt. Futtassa azon a számítógépen, amelyről az adatokat a lemezekre szeretné másolni. Adja meg a jelszót a lemezek feloldásához. Ugyanaz a jelszó oldja fel az összes lemezt. 

A részletes útmutatót a [Lemez feloldása Windows-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) vagy a [Lemez feloldása Linux-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) című szakaszban talál.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>K. Használhatok linuxos gazdaszámítógépet a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához?
A.  Igen. Linux- és a Windows-ügyfelek is használhatók a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához. További információkért tekintse meg a gazdaszámítógépre vonatkozó [Támogatott operációs rendszerek](data-box-disk-system-requirements.md) listáját.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>K. Már feladták a lemezeket, de szeretném lemondani a megrendelést. Miért nincs Mégse gomb?
A.  A megrendelést csak a lemezek megrendelése után, de még a szállítás megkezdése előtt mondhatja le. Ha feladtuk a lemezeket, már nem mondhatja le a megrendelést. A lemezeket azonban díjmentesen is visszaküldheti. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>K. Csatlakoztathatok adatátvitel céljából több Data Box Disk-lemezt is ugyanahhoz a gazdaszámítógéphez?
A. Igen. Ugyanahhoz a gazdaszámítógéphez egyszerre több Data Box Disk-lemez is csatlakoztatható, és párhuzamosan több másolási feladat is futtatható.

## <a name="track-status"></a>Állapot nyomon követése

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>K. Hogyan tudom nyomon követni a lemezeket a megrendelésem feladásától kezdve egészen azok visszaküldéséig? 
A.  A Data Box Disk-lemez megrendelésének állapotát az Azure Portalon követheti nyomon. A megrendelés létrehozásakor a rendszer egy értesítési e-mail-cím megadását is kéri. Ha adott meg ilyen e-mail-címet, akkor a megrendelés állapotváltozásairól e-mailben is értesíteni fogjuk. További információ az [Értesítési e-mailek beállítása](data-box-portal-ui-admin.md#edit-notification-details) témában.

### <a name="q-how-do-i-return-the-disks"></a>K. Hogyan küldhetem vissza a lemezeket? 
A.  A Microsoft a Data Box Disk-lemezek csomagjában egy levélcímkét is elküld Önnek. Ragassza ezt a címkét egy szállítódobozra, majd adja le a lezárt dobozt a szállítmányozójánál. Ha a címke megsérült vagy elveszett, nyissa meg az **Áttekintés > Levélcímke letöltése** menüpontot, és töltsön le egy új levélcímkét.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>Magam is felvehetem a Data Box Disk rendelésemet? Visszaküldhetem a lemezeket az általam választott szolgáltatón keresztül?
A. Igen. A Microsoft csak az Egyesült Államok kormányának régiójában kínál saját irányítású szállítást is. A Data Box Disk rendelés elhelyezésekénél kiválaszthatja a saját kezelésű szállítási lehetőséget. A Data Box Disk rendelésének felvételéhez tegye a következő lépéseket:
    
1. A megrendelés lerendelése után a rendszer feldolgozza a rendelést, és előkészíti a lemezeket. E-mailben értesítjük, hogy a megrendelés készen áll a felvételre. 
2. Miután a rendelés készen áll a felvételre, nyissa meg a rendelést az Azure Portalon, és keresse meg az **Áttekintés** panelt. 
3. Megjelenik egy értesítés egy kódot az Azure Portalon. Küldje el e-mailben az [Azure Data Box Operations csapatát,](mailto:adbops@microsoft.com) és adja meg nekik a kódot. A csapat megadja a helyszínt, és beütemezi a felvételi dátumot és időt. Az e-mailes értesítés kézhezvételétől számított 5 munkanapon belül fel kell hívnia a csapatot.

Az adatok másolása és érvényesítése befejezése után hajtsa végre a következő lépéseket a lemez visszaadásához:

1. Miután az adatok érvényesítése befejeződött, válassza le a lemezeket. Távolítsa el a csatlakoztatott kábeleket.
2. Csomagolja be az összes lemezt és kábelt buborékfóliába, és helyezze őket a szállítási dobozba. Ha a tartozékok hiányoznak, a szolgáltató költségeket számíthat fel.

    - Használja fel újra az első szállításkor kapott csomagolást. Ajánlott megfelelően rögzített buborékfóliát használni a lemezek becsomagolásához.
    - Gondoskodjon róla, hogy a lemez szorosan illeszkedjen a dobozba.
3. Nyissa meg a **rendelés áttekintése panelt** az Azure Portalon. Meg kell jelennie egy kódot.
4. Használja ezt a kódot, és e-mailben az [Azure Data Box Operations csapat,](mailto:adbops@microsoft.com) és adja meg nekik a kódot. Ők ad információt, hogy hol és mikor kell ledobni a lemezeket.


## <a name="migrate-data"></a>Adatok áttelepítése

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>K. Legfeljebb mekkora méretű adat esetén használhatók a Data Box Disk-lemezek?  
A.  A Data Box Disk megoldással legfeljebb 5, összesen 35 TB-os felhasználható kapacitással rendelkező lemez használható. Maguk a lemezek 8 TB-osak (amelyből 7 használható fel).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>K. Mekkora a Data Box Disk-lemezeken támogatott legnagyobb blokkblob és lapblob mérete? 
A.  A legnagyobb méreteket az Azure Storage tárhelykorlátai határozzák meg. A legnagyobb blokkblob mérete körülbelül 4,768 TiB, a legnagyobb lapblob mérete pedig 8 TiB. További információ: [Méretezhetőségi és teljesítménycélok a Blob storage.](../storage/blobs/scalability-targets.md)

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>K. Milyen adatátviteli sebességgel rendelkeznek a Data Box Disk-lemezek?
A. A tesztjeink során USB 3.0-ás kapcsolat esetén a lemezeknél 430 MB/s-os adatátviteli sebességet is mértünk. A tényleges szám a használt fájl méretének függvényében eltérő lehet. Kisebb fájlok esetén gyengébb teljesítmény figyelhető meg.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>K. Honnan tudhatom, hogy az átvitel során az adataim biztonságban vannak? 
A.  A Data Box Disk-lemezek 128 bites BitLocker AES-titkosítással vannak titkosítva, és a titkosítás jelszava csak az Azure Portalon érhető el. A jelszó lekéréséhez jelentkezzen be az Azure Portalra a fiókja hitelesítő adataival. Ezt a jelszót a Data Box lemezzárolás-feloldó eszköz használata során adhatja meg.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>K. Hogyan tudom az adataimat a Data Box Disk-lemezekre másolni? 
A.  Az adatok egy SMB-másoló eszköz, például a Robocopy vagy a Diskboss használatával másolhatja a lemezekre, de használhatja akár a Windows Fájlkezelő húzás funkcióját is.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>K. Vannak tippek az adatok másolásának felgyorsítására?
A.  A másolási folyamat felgyorsításához:

- Használjon több másolási adatfolyamot. Használja például a Robocopy többszálas lehetőségét. A ténylegesen használt parancsokkal kapcsolatban további információkat a következő oktatóanyagban talál: [Oktatóanyag: Adatok másolása az Azure Data Box Disk-lemezre, és az adatok ellenőrzése](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Használjon több munkamenetet.
- Például a hálózati megosztásból történő másolás helyett (ahol a hálózat sebessége korlátokat szabhat) gondoskodjon róla, hogy az adatok azon a helyi számítógépen legyenek, amelyre a lemezeket csatlakoztatja.
- Gondoskodjon róla, hogy a másolási folyamathoz USB 3.0-ás vagy újabb kapcsolatot használjon. Töltse le és használja az [USBView eszközt](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview), amellyel azonosíthatja a számítógép USB-vezérlőit és a számítógéphez kapcsolódó USB-s eszközöket.
- Mérje meg az adatok másolására használt számítógép teljesítményét. Töltse le és használja a [Bluestop FIO eszközt](https://ci.appveyor.com/project/axboe/fio), amellyel megmérheti a kiszolgáló hardverének teljesítményét. Válassza ki a legújabb x86-os vagy x64-es **buildet,** válassza az Összetevők lapot, és töltse le az MSI-t.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>K. Hogyan lehet felgyorsítani az adatok másolását, ha a forrásadatok között sok kisméretű fájl található (KB-os vagy néhány MB-os méretűek)?
A.  A másolási folyamat felgyorsításához:

- Hozzon létre egy helyi VHDx-et egy gyors tárolón, vagy hozzon létre egy üres VHD-t a HDD-n vagy az SSD-n (lassabb).
- Csatlakoztassa egy virtuális géphez.
- Másolja a fájlokat a virtuális gép lemezére.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>K. Használhatok több tárfiókot is a Data Box Disk-lemezekkel?
A.  Nem. A Data Box Disk-lemezekkel jelenleg csak egyetlen általános célú vagy hagyományos tárfiók használata támogatott. A gyakori és a ritka elérésű blob is támogatott. Jelenleg csak az Usa, Nyugat-Európa és Észak-Európa az Azure nyilvános felhőben a tárfiókok támogatottak.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>K. Milyen eszközkészlet áll rendelkezésre az adataimhoz a Data Box Disks segítségével?
A. A Data Box Disk lemezhez elérhető eszközkészlet három eszközt tartalmaz:
 - **Data Box Disk Unlock eszköz:** Ezzel az eszközzel feloldhatja a Microsofttól szállított titkosított lemezek zárolását. A lemezek zárolásának feloldásakor az eszköz használatával meg kell adnia egy passkey elérhető a Data Box Disk érdekében az Azure Portalon. 
 - **Data Box Disk Validation tool**: Ezzel az eszközzel ellenőrizze a méretét, formátumát és blob nevét az Azure elnevezési konvenciók szerint. Emellett ellenőrzőösszegeket is létrehoz a másolt adatokhoz, amelyeket a rendszer az Azure-ba feltöltött adatok ellenőrzésére használ.
 - **Data Box Disk Split Copy eszköz:** Ezt az eszközt akkor használja, ha több lemezt használ, és nagy adatkészlettel rendelkezik, amelyet fel kell osztani és át kell másolni az összes lemezközött. Ez az eszköz jelenleg a Windows rendszerhez érhető el. Ez az eszköz nem támogatott felügyelt lemezekkel. Ez az eszköz az adatok másolása kor is érvényesíti az adatokat, így az eszköz használatakor kihagyhatja az érvényesítési lépést.

Az eszközkészlet windowsos és linuxos használatra is elérhető. Letöltheti a szerszámkészlet itt:
- [Data Box Disk eszközkészlet letöltése Windowsra](https://aka.ms/databoxdisktoolswin) 
- [Data Box Disk eszközkészlet letöltése Linuxra](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>K. Használhatom a Data Box Disk-et az adatok Azure Files-ba való átviteléhez, majd az adatok azure file sync-be való átviteléhez? 
A. Az Azure-fájlok a Data Box Disk szolgáltatással támogatottak, de nem működnek jól az Azure File Sync szolgáltatással. Metaadatok nem marad meg, ha a fájladatokat az Azure File Sync használatával használja.


## <a name="verify-and-upload"></a>Ellenőrzés és feltöltés

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>K. A lemezek visszaküldését követően mennyi időn belül férhetek hozzá az adataimhoz az Azure-ban? 
A.  Amint az Adatmásolás megrendelési állapota befejeződöttként jelenik meg, azonnal hozzáférhet az adataihoz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>K. A feltöltést követően hol lesznek az adataim az Azure-ban?
A.  Ha a lemezen a *BlockBlob* és a *PageBlob* mappákba másolta az adatait, akkor a *BlockBlob* és a *PageBlob* mappákban található almappáknak létrejön az Azure-tárfiókban egy-egy tároló. Ha a fájlokat közvetlenül a *BlockBlob* és a *PageBlob* mappákba másolta, akkor ezek az alapértelmezett *$root* tárolóban lesznek megtalálhatók az Azure-tárfiókban. Amikor az adatokat az *AzureFile* mappában lévő mappába másolja, fájlmegosztás jön létre.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>K. Most vettem észre, hogy nem követtem az Azure elnevezési követelményeit a tárolóim esetében. Így is fel lehet tölteni az adataimat az Azure-ba?
A. Ha a tárolók nevei nagybetűket tartalmaznak, akkor azokat automatikusan kisbetűsekké alakítja át a rendszer. Ha a nevek valami más szempontból nem megfelelőek (például speciális karaktereket tartalmaznak, más nyelvűek stb.) akkor a feltöltés meghiúsul. További információt az [Azure elnevezési konvencióit](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) ismertető szakaszban talál.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>K. Hogyan tudom ellenőrizni a több Data Box Disk-lemezre feltöltött adataimat?
A.  Az adatok másolásának befejezése után futtassa az `DataBoxDiskValidation.cmd` fájlt a *DataBoxDiskImport* mappából, hogy létrehozzon az érvényesítéshez egy ellenőrzőösszeget. Ha több lemeze is van, akkor minden egyes lemezen meg kell nyitnia a parancssori ablakot, és le kell futtatnia ezt a parancsot. Vegye figyelembe, hogy ez a művelet az adatok méretétől függően akár hosszú ideig (órákig) is eltarthat.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>K. Mi történik az adataimmal, miután visszaküldtem a lemezeket?
A.  Ha befejeződött az adatok Azure-ba történő másolása, a rendszer a lemezen található adatokat a NIST SP 800-88 Revision 1 útmutatásainak megfelelően biztonságosan törli.  

### <a name="q-how-is-my-data-protected-during-transit"></a>K. Milyen védelem alatt állnak az adataim a szállítás közben? 
A.  A Data Box Disk-lemezek 128 bites Microsoft BitLocker AES-titkosítással vannak titkosítva. A feloldáshoz és az adatokhoz való hozzáféréshez egyetlen jelszó szükséges.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>K. Le kell futtatnom ismét az ellenőrzőösszeg-érvényesítést, ha további adatokat másolok a Data Box Disk-lemezekre?
A. Igen. Ha úgy dönt, hogy érvényesíti az adatait (ezt mi is javasoljuk), akkor ha további adatokat másol a lemezre, újra le kell futtatnia az érvényesítést.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>K. Az összes lemezt felhasználtam az adataim átviteléhez, és szükségem van további lemezek megrendelésére. Fel lehet adni gyorsan a megrendelést?
A. Lehetősége van klónozni az előző megrendelését. A klónozással létrehozhat egy, az előzővel azonos megrendelést, amelynek szerkesztheti a részleteit, anélkül, hogy újra meg kellene adnia a címet vagy az értesítési adatokat.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>K. Adatokat másoltam a ManagedDisk mappába Nem látok olyan felügyelt lemezeket, amelyeken a felügyelt lemezekhez megvan adva erőforráscsoport. Feltöltötték az adataimat az Azure-ba, és hogyan találhatom meg őket?
A. Igen. Az adatok feltöltése az Azure-ba, de ha nem lát felügyelt lemezeket a megadott erőforráscsoportok, valószínűleg azért, mert az adatok nem volt érvényes. Ha a lapblobok, a blokkblobok, az Azure Files és a felügyelt lemezek érvénytelenek, ezek a következő mappákba kerülnek:
 - A lapblobok egy blokkblob-tárolóba kerülnének, amely a *databoxdisk-invalid-pb-* kezdetű.
 - Az Azure Files egy blokkblob-tárolóba kerül, amely a *databoxdisk-invalid-af.*
 - A felügyelt lemezek egy blokkblob-tárolóba kerülnének, amely a *databoxdisk-invalid-md-* kezdetű.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Data Box Disk rendszerkövetelményeit.](data-box-disk-system-requirements.md)
- A [Data Box Disk korlátainak](data-box-disk-limits.md) megismerése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
