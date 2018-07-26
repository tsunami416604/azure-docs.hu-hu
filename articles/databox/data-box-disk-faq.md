---
title: Microsoft Azure Data Box Disk GYIK | Microsoft Docs in data
description: Gyakori kérdések és válaszok az Azure Data Box Disk felhőalapú megoldással kapcsolatban, amellyel nagy mennyiségű adatot vihet át az Azure-ba
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/17/2018
ms.author: alkohli
ms.openlocfilehash: 5288e9900c75eae7601b84f7366edf9ac739d5da
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125803"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Mi az az Azure Data Box Disk-lemez? (Előzetes verzió)

A Microsoft Azure Data Box Disk felhőalapú megoldással gyorsan, költséghatékonyan és megbízhatóan küldhet több terabájtnyi adatot az Azure-ba. A GYIK a Data Box Disk-lemezek Azure Portalon történő használatával kapcsolatban esetlegesen felmerülő kérdésekre ad választ. 

A kérdéseket és a válaszokat az alábbi kategóriák szerint csoportosítottuk:

- Tudnivalók a szolgáltatásról
- Konfigurálás és csatlakoztatás 
- Állapot nyomon követése
- Adatok áttelepítése 
- Adatok ellenőrzése és feltöltése 

> [!IMPORTANT]
> A Data Box Disk szolgáltatás előzetes verzióban érhető el. A szolgáltatás telepítése előtt tekintse meg az [Azure előzetes verzióra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-the-service"></a>Tudnivalók a szolgáltatásról

### <a name="q-what-is-azure-data-box-service"></a>K. Mi az az Azure Data Box szolgáltatás? 
A.  Az Azure Data Box szolgáltatást offline adatbetöltéshez hoztuk létre. A szolgáltatás termékek egész tárházát kezeli, amelyek mindegyike különböző kapacitású tárolókra történő adatátvitelhez lett igazítva. 

### <a name="q-what-are-azure-data-box-disks"></a>K. Mik azok az Azure Data Box-lemezek?
A. Az Azure Data Box-lemezek több terabájtnyi adat gyors, költséghatékony és biztonságos adatátvitelét teszik lehetővé az Azure-ba, illetve az Azure-ból. A Microsoft elküld Önnek 1–5 lemezt, legfeljebb 35 TB-os tárkapacitással. Az Azure Portalon a Data Box szolgáltatás segítségével könnyedén konfigurálhatja, csatlakoztathatja és feloldhatja ezeket a lemezeket.  

A lemezek a Microsoft BitLocker meghajtótitkosítással vannak titkosítva, amelynek titkosítási kulcsait az Azure Portalon kezelheti. Ezt követően átmásolhatja az adatokat az ügyfél kiszolgálóiról. Az adatközpontban a Microsoft egy gyors és privát hálózati feltöltési kapcsolaton keresztül a lemezről a felhőbe telepíti az adatait, majd feltölti őket az Azure-ba.

### <a name="q-when-should-i-use-data-box-disks"></a>K. Mikor érdemes Data Box Disk-lemezeket használnom?
A. Ha 35 TB-nyi (vagy kevesebb) adata van, amelyet szeretne átvinni az Azure-ba, akkor hasznos lehet a Data Box Disk-lemezek használata.

### <a name="q-what-is-the-price-of-data-box-disks"></a>K. Mennyibe kerülnek a Data Box Disk-lemezek?
A. Az előzetes verzió ideje alatt a Data Box Disk-lemezek díjmentesen érhetők el. A szállítás is ingyenes, az Azure-tárhely azonban költségekkel jár.

### <a name="q-how-do-i-get-data-box-disks"></a>K. Hogyan kaphatok Data Box Disk-lemezeket? 
A.  Ahhoz, hogy Azure Data Box Disk-lemezeket kaphasson, előbb regisztráljon a [Data Box Disk előzetes verziójára](http://aka.ms/AzureDataBox). Ezt követően jelentkezzen be az Azure Portalon, majd hozzon létre egy Data Box-lemezekre vonatkozó megrendelést. Adja meg a kapcsolattartási és értesítési adatait. A megrendelés elküldése után a rendelkezésre állás függvényében 10 napon belül szállítjuk Önnek a lemezeket.   

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>K. Egyszerre legfeljebb mennyi adatot vihetek át a Data Box Disks-lemezekkel?
A. 5 darab egyenként 8 TB-os lemez (7 TB-os felhasználható kapacitással) esetén a felhasználható kapacitás legfeljebb 35 TB. Így egyszerre legfeljebb 35 TB-nyi adat vihető át.  Több adat átviteléhez több lemez megrendelése szükséges.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>K. Honnan tudhatom meg, hogy a Data Box Disk-lemezek elérhetők-e a régiómban? 
A.  Az előzetes verzió fázisában a Data Box Disk-lemezek az USA-ban és az Európai Unió országaiban érhetők el.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>K. Mely régiókban tárolhatok adatokat a Data Box Disk-lemezekkel?
A. Az előzetes verzióban Data Box Disk-lemezek tárolása az USA összes régiójában, illetve Nyugat- és Észak-Európában támogatottak. Csak a nyilvános Azure-felhőrégiók támogatottak. Az Azure Government és más független felhők nem támogatottak.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>K. Kihez fordulhatok, ha probléma merülne fel a Data Box Disk-lemezekkel kapcsolatban?
A. Ha bármilyen probléma merülne fel a Data Box Disk-lemezekkel kapcsolatban, kérjük, forduljon a [Data Box Disk támogatásához](mailto:expresspodsupport@microsoft.com).

## <a name="configure-and-connect"></a>Konfigurálás és csatlakoztatás
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>K. Megadhatom a megrendelésben a Data Box Disk-lemezek számát?
A.  Nem. 8 TB-os lemezeket (legfeljebb 5-öt) fog kapni az adatai mérete és a lemezek rendelkezésre állásának függvényében.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>K. Hogyan oldhatom fel a Data Box Disk-lemezeket? 
A.  Nyissa meg az Azure Portalon a Data Box Disk-megrendelését, majd az **Eszköz részletei** lehetőséget. Másol le a jelszót. Töltse le az Azure Portalról a Data Box Disk lemezzárolás-feloldó eszközét, csomagolja ki, majd futtassa a *DataBoxDiskUnlock.exe* fájlt azon a számítógépen, amelyről az adatokat a lemezekre szeretné másolni. Adja meg a jelszót a lemezek feloldásához. Ugyanaz a jelszó oldja fel az összes lemezt.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>K. Használhatok linuxos gazdaszámítógépet a Data Box Disk-lemezek csatlakoztatásához és az adatok másolásához?
A.  Nem. Csak Windows rendszerű számítógépek támogatottak. További információkért tekintse meg a gazdaszámítógépre vonatkozó [Támogatott operációs rendszerek](data-box-disk-system-requirements.md) listáját.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>K. Már feladták a lemezeket, de szeretném lemondani a megrendelést. Miért nincs Mégse gomb?
A.  A megrendelést csak a lemezek megrendelése után, de még a szállítás megkezdése előtt mondhatja le. Ha feladtuk a lemezeket, már nem mondhatja le a megrendelést. Az előzetes verzió időszakában költségmentesen visszaküldheti a lemezeket, azonban ez valószínűleg változni fog, ha a megoldás már általánosan elérhető lesz. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>K. Csatlakoztathatok adatátvitel céljából több Data Box Disk-lemezt is ugyanahhoz a gazdaszámítógéphez?
A. Igen. Ugyanahhoz a gazdaszámítógéphez egyszerre több Data Box Disk-lemez is csatlakoztatható, és párhuzamosan több másolási feladat is futtatható.

## <a name="track-status"></a>Állapot nyomon követése

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>K. Hogyan tudom nyomon követni a lemezeket a megrendelésem feladásától kezdve egészen azok visszaküldéséig? 
A.  A Data Box Disk-lemez megrendelésének állapotát az Azure Portalon követheti nyomon. A megrendelés létrehozásakor a rendszer egy értesítési e-mail-cím megadását is kéri. Ha adott meg ilyen e-mail-címet, akkor a megrendelés állapotváltozásairól e-mailben is értesíteni fogjuk. További információ az [Értesítési e-mailek beállítása](data-box-portal-ui-admin.md#edit-notification-details) témában.

### <a name="q-how-do-i-return-the-disks"></a>K. Hogyan küldhetem vissza a lemezeket? 
A.  A Microsoft a Data Box Disk-lemezek csomagjában egy levélcímkét is elküld Önnek. Ragassza ezt a címkét egy szállítódobozra, majd adja le a lezárt dobozt a szállítmányozójánál. Ha a címke megsérült vagy elveszett, nyissa meg az **Áttekintés > Levélcímke letöltése** menüpontot, és töltsön le egy új levélcímkét.

## <a name="migrate-data"></a>Adatok áttelepítése

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>K. Legfeljebb mekkora méretű adat esetén használhatók a Data Box Disk-lemezek?  
A.  A Data Box Disk megoldással legfeljebb 5, összesen 35 TB-os felhasználható kapacitással rendelkező lemez használható. Maguk a lemezek 8 TB-osak (amelyből 7 használható fel). 

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>K. Mekkora a Data Box Disk-lemezeken támogatott legnagyobb blokkblob és lapblob mérete? 
A.  A legnagyobb méreteket az Azure Storage tárhelykorlátai határozzák meg. A legnagyobb blokkblob mérete körülbelül 4,768 TiB, a legnagyobb lapblob mérete pedig 8 TiB. További információkért lásd: [Azure Storage Scalability and Performance Targets](../storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai). 

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
- Mérje meg az adatok másolására használt számítógép teljesítményét. Töltse le és használja a [Bluestop FIO eszközt](https://bluestop.org/fio/), amellyel megmérheti a kiszolgáló hardverének teljesítményét.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>K. Hogyan lehet felgyorsítani az adatok másolását, ha a forrásadatok között sok kisméretű fájl található (KB-os vagy néhány MB-os méretűek)?
A.  A másolási folyamat felgyorsításához:

- Hozzon létre egy helyi VHDx-et egy gyors tárolón, vagy hozzon létre egy üres VHD-t a HDD-n vagy az SSD-n (lassabb).
- Csatlakoztassa egy virtuális géphez.
- Másolja a fájlokat a virtuális gép lemezére.


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>K. Használhatok több tárfiókot is a Data Box Disk-lemezekkel?
A.  Nem. A Data Box Disk-lemezekkel jelenleg csak egyetlen általános célú vagy hagyományos tárfiók használata támogatott. A gyakori és a ritka elérésű blob is támogatott. Az előzetes verzió ideje alatt csak az USA-ban, Nyugat-Európában, Észak-Európában és nyilvános Azure-felhőben működő tárfiókok támogatottak.

## <a name="verify-and-upload"></a>Ellenőrzés és feltöltés

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>K. A lemezek visszaküldését követően mennyi időn belül férhetek hozzá az adataimhoz az Azure-ban? 
A.  Amint az Adatmásolás megrendelési állapota befejeződöttként jelenik meg, azonnal hozzáférhet az adataihoz.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>K. A feltöltést követően hol lesznek az adataim az Azure-ban?
A.  Ha a lemezen a *BlockBlob* és a *PageBlob* mappákba másolta az adatait, akkor a *BlockBlob* és a *PageBlob* mappákban található almappáknak létrejön az Azure-tárfiókban egy-egy tároló. Ha a fájlokat közvetlenül a *BlockBlob* és a *PageBlob* mappákba másolta, akkor ezek az alapértelmezett *$root* tárolóban lesznek megtalálhatók az Azure-tárfiókban. 

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>K. Most vettem észre, hogy nem követtem az Azure elnevezési követelményeit a tárolóim esetében. Így is fel lehet tölteni az adataimat az Azure-ba?
A. Ha a tárolók nevei nagybetűket tartalmaznak, akkor azokat automatikusan kisbetűsekké alakítja át a rendszer. Ha a nevek valami más szempontból nem megfelelőek (például speciális karaktereket tartalmaznak, más nyelvűek stb.) akkor a feltöltés meghiúsul.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>K. Hogyan tudom ellenőrizni a több Data Box Disk-lemezre feltöltött adataimat?
A.  Az adatok másolásának befejezése után futtassa az `AzureExpressDiskService.cmd` fájlt az *AzureImportExport* mappából, hogy létrehozzon az érvényesítéshez egy ellenőrzőösszeget. Ha több lemeze is van, akkor minden egyes lemezen meg kell nyitnia a parancssori ablakot, és le kell futtatnia ezt a parancsot. Vegye figyelembe, hogy ez a művelet az adatok méretétől függően akár hosszú ideig (órákig) is eltarthat.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>K. Mi történik az adataimmal, miután visszaküldtem a lemezeket?
A.  Ha befejeződött az adatok Azure-ba történő másolása, a rendszer a lemezen található adatokat a NIST SP 800-88 Revision 1 útmutatásainak megfelelően biztonságosan törli.  

### <a name="q-how-is-my-data-protected-during-transit"></a>K. Milyen védelem alatt állnak az adataim a szállítás közben? 
A.  A Data Box Disk-lemezek 128 bites Microsoft BitLocker AES-titkosítással vannak titkosítva. A feloldáshoz és az adatokhoz való hozzáféréshez egyetlen jelszó szükséges.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>K. Le kell futtatnom ismét az ellenőrzőösszeg-érvényesítést, ha további adatokat másolok a Data Box Disk-lemezekre?
A. Igen. Ha úgy dönt, hogy érvényesíti az adatait (ezt mi is javasoljuk), akkor ha további adatokat másol a lemezre, újra le kell futtatnia az érvényesítést.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>K. Az összes lemezt felhasználtam az adataim átviteléhez, és szükségem van további lemezek megrendelésére. Fel lehet adni gyorsan a megrendelést?
A. Lehetősége van klónozni az előző megrendelését. A klónozással létrehozhat egy, az előzővel azonos megrendelést, amelynek szerkesztheti a részleteit, anélkül, hogy újra meg kellene adnia a címet vagy az értesítési adatokat. 



## <a name="next-steps"></a>További lépések

- A [Data Box rendszerkövetelményeinek](data-box-disk-system-requirements.md) áttekintése.
- A [Data Box korlátjainak](data-box-disk-limits.md) értelmezése.
- Az [Azure Data Box Disk](data-box-disk-quickstart-portal.md) gyors üzembe helyezése az Azure Portalon.
