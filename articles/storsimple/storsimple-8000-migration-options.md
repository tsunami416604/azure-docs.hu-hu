---
title: Adatáttelepítési lehetőségek a StorSimple 5000-7000 sorozatú eszközökről
description: Áttekintést nyújt a StorSimple 5000-7000 sorozatból származó adatok áttelepítésének lehetőségeiről.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471822"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Adatmigrálási lehetőségek a StorSimple 5000-7000-es sorozatból 

> [!IMPORTANT]
> 2019. július 9-én a StorSimple 5000/7000 sorozat a támogatási (EOS) státusz megszűnése felé tart. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozatú ügyfelek a dokumentumban ismertetett alternatívák egyikére térjenek át.

A StorSimple 5000-7000 sorozat 2019 júliusában ér véget a [támogatásnak.](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) A StorSimple 5000-7000 sorozatot futtató ügyfelek más Azure-szintű hibrid szolgáltatásokra is frissíthetnek. Ez a cikk ismerteti az Azure hibrid lehetőségek az adatok áttelepítéséhez elérhető. 

## <a name="migration-options"></a>Migrálási lehetőségek

A StorSimple 5000-7000 sorozatot használó ügyfelek Azure-beli vagy külső gyártótól származó lehetőségekkel rendelkeznek.

### <a name="azure-options"></a>Azure-lehetőségek

#### <a name="upgrade-to-storsimple-8000-series"></a>Frissítés a StorSimple 8000 sorozatra

Frissítsen a StorSimple 8000 sorozatra, és így folytassa a StorSimple platformon.  Ez a frissítési útvonal megköveteli az ügyfelektől, hogy az 5000-7000-es sorozatú eszközeiket egy 8000-es sorozatra cseréljék. Az adatok áttelepítése az 5000-7000-es sorozatú eszközről az áttelepítési eszközzel történik. Miután az áttelepítés sikeresen befejeződött, a StorSimple 8000 sorozatú eszközök továbbra is réteg adatokat az Azure Blob Storage. 

Az adatok StorSimple 8000 sorozathasználatával történő áttelepítéséről az [Adatok áttelepítése a StorSimple 5000-7000 sorozatból a 8000-es sorozatú eszközre](storsimple-8000-migrate-from-5000-7000.md)című részében talál további információt.

#### <a name="migrate-to-azure-file-sync"></a>Migrálás az Azure File Syncre

Ez a vadonatúj áttelepítési beállítás lehetővé teszi az ügyfelek számára, hogy a szervezet fájlmegosztásait az Azure Files-ban tárolják. Ezek a fájlmegosztások ezután központosított a helyszíni hozzáférés az Azure File Sync (AFS) használatával. Az AFS telepíthető Windows Server-állomásra. A tényleges adatáttelepítés ezután gazdagépmásolatként vagy az áttelepítési eszköz használatával történik.

Az adatok Azure File Sync szolgáltatásba való áttelepítéséről az [Adatok áttelepítése a StorSimple 5000-7000 sorozatból az Azure File Sync szolgáltatásba](storsimple-5000-7000-afs-migration.md)című részében talál további információt.

### <a name="third-party-options"></a>Külső felek beállításai

#### <a name="migrate-to-panzura-freedom-nas"></a>Vándorolnak Panzura Szabadság NAS

A StorSimple 5000-7000 ügyfelek dönthetnek úgy, hogy áttelepülnek a Panzura Freedom NAS-ra, hogy adataik az Azure-ban maradjanak. A Panzura Freedom megoldás olyan NAS-megoldást kínál, amely adatközpontokra, irodákra, nyilvános és privát felhőkre terjed ki. A megoldás helyi, hibrid és felhőn lévő adatmunkafolyamatokat tesz lehetővé NFS-, SMB- és mobilügyfelek számára. 

Ezt az áttelepítést a Panzura támogatja, és az ügyfelek a [Panzura webhelyről](https://panzura.com/storsimple-migration/)történő áttelepítési támogatás kérésével kezdhetik el a kezdést.

#### <a name="migrate-to-cohesity"></a>Áttérés a Cohesity

A Cohesity lehetővé teszi az adatok áttelepítését a jelenlegi StorSimple 5000–7000-ből az Azure-beli Cohesity-adatplatformra. A Cohesity Data Platform egy szoftveresen definiált webes méretű megoldás, amely egyetlen felhőalapú natív megoldásra egyesíti a fájlokat, biztonsági mentéseket, objektumokat és virtuális gépeket. Az adatplatformra való áttérés után egyetlen üvegtáblán keresztül kezelheti, védheti és helyezheti üzembe az adatokat és alkalmazásokat a felhőből a magba. A Cohesity, kezdje mindössze három csomópont. 

További információ [a Cohesity Data Platformra való áttérésről.](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)

#### <a name="migrate-to-nasuni"></a>Vándorolni Nasuni

A Nasuni megkönnyíti a StorSimple 5000-7000 ügyfelek számára az áttelepítést és az adatok azure-ban való megőrzését.  A Nasuni az Azure-alapú NAS-tárolási megoldás, amely felhőgazdaságosan és méretgazdaságosan biztosít az ügyfeleknek a prem megoldásoktól elvárt teljesítményt és biztonságot.  A nagy teljesítményű fájltárolás mellett a Nasuni és az Azure is kezeli a biztonsági mentést és a VÉSZ-dr-t, miközben lehetővé teszi az adatok megosztását és közös együttműködését világszerte a központi fájltárolás kezelésével. 

A Nasuni rendelkezik azzal a tapasztalattal, hogy megkönnyítse a migrációt – kezdje el még ma:https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Áttelepítés talongyors

A Talon megkönnyíti a StorSimple 5000-7000 ügyfelek számára, hogy továbbra is kihasználják az általuk értékelt előnyöket a StorSimple platformon (kis helyszíni lábnyom korlátlan felhőerőforrásokkal támogatva) még nagyobb funkcióval.  A Talon FAST megoldással az ügyfelek áttelepíthetik és megtarthatják az adataikat az Azure-ban, miközben még kisebb, csak szoftveres helyszíni lábnyommal rendelkeznek, és olyan előnyökkel is rendelkeznek, mint a globális fájlzárolás, a globális névtér és a többwebhelyes együttműködés.  A Talon az Azure egyik vezető azure-beli ökoszisztéma-megoldása, amely globális ügyfelekkel együttműködve a helyszíni fájlkiszolgálói munkaterheléseiket konszolidált, Azure-alapú lábnyomba telepíti át anélkül, hogy veszélyeztetné a felhasználói munkafolyamatot vagy élményt.  

További információ arról, hogyan fejlesztheti https://www.talonstorage.com/alliances/microsoft-storsimpleát felhőalapú vállalattá a rendszert a rendszeren.


## <a name="migration---frequently-asked-questions"></a>Migráció - Gyakori kérdések

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>K. Mikor érik el a StorSimple 5000 és 7000 sorozatú eszközök a szolgáltatás végét? 

A. A StorSimple 5000-7000 sorozat 2019 júliusában éri el [a szolgáltatás végét.](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) A szolgáltatás megszűnése azt jelenti, hogy a Microsoft 2019 júliusa után már nem lesz képes támogatást nyújtani az eszközök hardveréhez és szoftveréhez. Javasoljuk, hogy most kezdje el az adatok áttelepítésének tervét az eszközökről.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>K. Mi történik az Azure-ban tárolt adatokkal?  

A. Az adatok továbbra is használhatók az Azure-ban, miután áttelepítette őket egy újabb szolgáltatásba. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>K. Mi történik a StorSimple-eszközön helyileg tárolt adatokkal? 

A. A helyi eszközön lévő adatok az áttelepítési dokumentumokban leírtak szerint átmásolhatók az újabb szolgáltatásba.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>K. Mi történik, ha meg szeretném tartani a StorSimple 5000/7000 sorozatú készülékemet? 

A. Bár a szolgáltatások továbbra is működhetnek, a Microsoft a továbbiakban nem tud hardver- és szoftvertámogatást nyújtani. Az áttelepítés erősen ajánlott az üzletmenet folytonossága érdekében.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>K. Milyen lehetőségek állnak rendelkezésre az adatok StorSimple 5000-7000 sorozatú eszközökről történő áttelepítésére? 

A. A forgatókönyvtől függően a StorSimple 5000-7000 sorozat felhasználói a következő áttelepítési lehetőségekkel rendelkeznek. 

 - **Frissítsen 8000-es sorozatra:** Használja ezt a beállítást, ha tovább szeretné folytatni a StorSimple platformon. 
 - **Migrálás az Azure File Sync:** Használja ezt a beállítást, ha át szeretne váltani az Azure natív formátumban. Az Azure File Sync segítségével a fájlmegosztások központi felügyeletéhez. 

A Microsoft támogatási szolgálatához fordulhat, hogy megbeszéljék az itt nem felsorolt áttelepítési lehetőségeket.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>K. Támogatott a más tárolási megoldásokra való áttérés?

A. Igen. Az adatok gazdapéldányát használó más tárolási megoldásokra való áttelepítés támogatott.

### <a name="q-is-migration-supported-by-microsoft"></a>K. Támogatja az áttelepítést a Microsoft? 

A. Az 5000 vagy 7000 sorozatból való áttelepítés teljes mértékben támogatott művelet. A Microsoft azt javasolja, hogy az áttelepítés megkezdése előtt a Microsoft elérje a támogatási felet. Az áttelepítés jelenleg egy támogatott művelet. Ha adatokat kíván áttelepíteni a StorSimple 5000-7000 sorozatú eszközről, [nyisson meg egy támogatási jegyet.](storsimple-8000-contact-microsoft-support.md)

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>K. Mi a díjszabási modell mindkét áttelepítési lehetőséghez?

A. Az áttelepítés költsége a választott beállítástól függ. Míg maga az áttelepítés ingyenes, ha úgy dönt, hogy egy StorSimple 8000 sorozatra frissít, a hardvereszköz költsége lesz. 

Hasonlóképpen, az Azure File Sync használatakor a szolgáltatás előfizetési díja i. Minden esetben az ügyfeleknek is meg kell fizetniük a folyamatos tárolási költségeket. A becslést lásd a következőkben: 
- [StorSimple árképzés](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS-árképzés]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>K.  Mennyi ideig tart az áttelepítés befejezése?

A. Az adatok áttelepítésének ideje az adatok mennyiségétől és a kiválasztott frissítési beállítástól függ. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>K. Mi a StorSimple 8000 sorozat támogatási dátuma?

A. A StorSimple 8000 sorozat támogatási dátuma [itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)jelenik meg.


## <a name="next-steps"></a>További lépések
 - [Adatok áttelepítése StorSimple 5000-7000 sorozatból 8000-es sorozatú eszközbe.](storsimple-8000-migrate-from-5000-7000.md)
 - [Adatok áttelepítése StorSimple 5000-7000 sorozatból az Azure File Sync szolgáltatásba](storsimple-5000-7000-afs-migration.md)
