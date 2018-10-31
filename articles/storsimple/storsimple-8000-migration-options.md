---
title: Adatok áttelepítése az a StorSimple 5000-7000-es sorozat lehetőségeinek kiértékeléséhez |} A Microsoft Docs
description: A beállítások át adatokat a StorSimple 5000-7000-es sorozat áttekintést nyújt.
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
ms.date: 10/30/2018
ms.author: alkohli
ms.openlocfilehash: 5166e154e1a67cea777933b6bf8757661a9c3ad5
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242959"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Adatok áttelepítése az a StorSimple 5000-7000-es sorozat beállításai 

> [!IMPORTANT]
> A 2019. július 31-ig. a StorSimple 5000/7000 sorozat megszűnik a támogatás (EOS) állapotát. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozat ügyfelek áttelepítése a a dokumentumban ismertetett alternatív megoldások egyikét.

A StorSimple 5000-7000-es sorozat eléri-e [támogatásuk](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) a július 2019. A futtató ügyfelek StorSimple 5000-7000-es sorozat rendelkezik más Azure-bA frissítését belső hibrid szolgáltatások. Ez a cikk ismerteti a rendelkezésre álló adatok migrálása az Azure hybrid lehetőségeket. 

## <a name="migration-options"></a>Áttelepítési beállítások

A StorSimple 5000-7000-es sorozat használó ügyfelek az alábbi lehetőségei vannak:

- **Azure-lehetőségeket**:

    - **Frissítés a StorSimple 8000 sorozat** – frissítsen a StorSimple 8000-es sorozat, és így továbbra is a StorSimple-platformon.  Ezeket a frissítési lépéseket igényel az ügyfelek számára, hogy az 5000 – 7000 sorozatú eszközöket cserélje le a 8000-es sorozat. Az adatok áttelepítése az 5000 – 7000 sorozatú eszközről az áttelepítési eszköz használatával. Az áttelepítés sikeres végrehajtása után a StorSimple 8000 sorozatú eszközök továbbra is az Azure Blob Storage-szintű adatforgalom. 

    A StorSimple 8000 sorozat használata az adatok migrálása további információkért látogasson el [át adatokat a StorSimple 5000-7000-es sorozat a 8000-es sorozatú eszköz](storsimple-8000-migrate-from-5000-7000.md).

    - **Az Azure File Sync át** – Ez teljesen új áttelepítési beállítás lehetővé teszi az ügyfelek számára, hogy a cég fájlmegosztások tárolása az Azure Files. Ezek a fájlmegosztások az Azure File Sync (AFS) használatával a helyszíni hozzáférés majd központi üzemelnek. AFS is telepíthető a Windows Server-gazdagépen. Az adatok tényleges áttelepítése akkor hajt végre gazdagépként példányát, vagy az áttelepítési eszköz használata.

    Adatok áttelepítése az Azure File Sync további információkért látogasson el [át adatokat a StorSimple 5000-7000-es sorozat az Azure File Sync](https://aka.ms/StorSimpleMigrationAFS).

- **Harmadik féltől származó beállítások**:

    - **Panzura szabadon NAS át** – a StorSimple 5000-7000-es ügyfelek kiválaszthatják a Panzura szabadon NAS tárolni az adatokat az Azure-ban való áttelepítéséhez. Panzura szabadon megoldás egy NAS-megoldás, adatközpontok, iroda, nyilvános és magánfelhőkben is biztosít. A megoldás lehetővé teszi a helyi, hibrid és a felhőbeli adatok munkafolyamatok NFS, SMB és mobilos ügyfeleket. Ez az áttelepítés Panzura által támogatott és Kezdésként igénylésével az áttelepítés támogatása a [Panzura webhely](https://panzura.com/storsimple-migration/).

## <a name="migration---frequently-asked-questions"></a>Migrálás – gyakori kérdések

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>K. Ha nem a StorSimple 5000 és 7000 sorozatú eszköz éri el a szolgáltatás teljes? 

A. A StorSimple 5000-7000-es sorozat elérni [szolgáltatás végén](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) a július 2019. Szolgáltatás végén, az azt jelenti, hogy a Microsoft már nem tudja támogatást nyújt a hardver és szoftver ezen eszköz július 2019 után. Kifejezetten javasoljuk, hogy először, át az adatokat az eszközök most már terv kidolgozásában.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>K. Mi történik, az I az Azure-ban tárolt adatokat?  

A. Továbbra is használhatja az adatokat az Azure-ban, egy újabb szolgáltatás áttelepítése után. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>K. Mi történik az adatok I rendelkezik helyi tárolására az a StorSimple eszköz? 

A. Az adatok a helyi eszközön, az újabb szolgáltatásba az áttelepítési dokumentációban leírt módon lehet másolni.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>K. Mi történik, ha szeretnék, hogy a StorSimple 5000/7000 sorozat készülék? 

A. A szolgáltatások esetleg tovább használhatók, amíg a Microsoft már nem lesz képes biztosítani a hardver- és támogatási. Áttelepítési erősen ajánlott az üzletmenet folytonosságáról.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>K. Milyen lehetőségek állnak rendelkezésre adatok áttelepítése az a StorSimple 5000-7000-es sorozatú eszközöket? 

A. A forgatókönyvtől függően a StorSimple 5000-7000-es sorozat felhasználók rendelkeznek az alábbi áttelepítési lehetőségek. 

 - **Frissítés a 8000-es sorozat**: használja ezt a beállítást, ha folytatja a StorSimple-platformon. 
 - **Az Azure File Sync át**: használja ezt a beállítást, ha azt szeretné, váltson át az Azure natív formátumban. Az Azure File Sync fájlmegosztások központi kezelésére használható. 

Áttelepítési beállítások nem szerepel itt vitatni Support forduljon.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>K. Az áttelepítés egyéb tárolási megoldások támogatott?

A. Igen. Egyéb tárolási megoldások használata az adatok másolatát gazdagépre történő áttelepítése támogatott.

### <a name="q-is-migration-supported-by-microsoft"></a>K. A Microsoft által támogatott áttelepítés? 

A. Az 5000 és 7000 sorozatú-ről egy olyan teljes körűen támogatott művelet. Valójában a Microsoft azt javasolja, lépjen kapcsolatba a támogatási migrálás megkezdése előtt. Migrálás jelenleg egy támogatott művelet. Ha az adatok áttelepíthetők a StorSimple 5000-7000-es sorozatú eszközre, szeretne [hozzon létre egy támogatási jegyet](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>K. Mi az az áttelepítési lehetőségek díjszabási modellje?

A. Az áttelepítés költség lehetőségtől függően változik. Áttelepítési maga ingyenes, ha úgy dönt, hogy frissítse a StorSimple 8000 sorozat pedig a hardvereszköz költsége lesz. 

Ehhez hasonlóan az Azure File Sync használata esetén a szolgáltatás az előfizetési díjak lehetnek érvényben. Minden esetben ügyfelek is megkapják a folyamatban lévő tárolási költségeket fizetni. Tekintse meg a következő becslésre használható: 
- [A StorSimple-díjszabás](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS díjszabása]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>K.  Mennyi ideig tart egy áttelepítés elvégzéséhez?

A. Adatok áttelepítése az idő az adatok és a frissítési lehetőséget kiválasztva függ. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>K. Mi az a StorSimple 8000 sorozat közötti-támogatás dátumát?

A. A StorSimple 8000 sorozat közötti-támogatás dátumát közzé van téve [Itt](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>További lépések
 - [Telepítse át az adatokat a storsimple 5000-7000-es sorozat a 8000-es sorozatú eszköz](storsimple-8000-migrate-from-5000-7000.md).
 - [Az Azure File Sync át adatokat a StorSimple 5000-7000-es sorozat](storsimple-5000-7000-afs-migration.md)
