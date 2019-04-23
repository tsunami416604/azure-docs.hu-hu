---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804164"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>A SharePoint 2010 frissítése a SharePoint 2013-hoz, és telepítse a StorSomple Adapter a Sharepointhoz
> [!IMPORTANT]
> Azokat a fájlokat, korábban helyezett át a külső tárhelyen RBS nem lesz elérhető, amíg a frissítés elkészült, és a RBS szolgáltatás újra engedélyezve van-e. Korlátozza a felhasználókra gyakorolt hatás, hajtsa végre minden frissítés vagy újratelepítésre tervezett karbantartási időszak alatt.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>A SharePoint 2010 frissítése a SharePoint 2013-hoz és az adapter telepítése
1. A SharePoint 2010-farm jegyezze fel a BLOB-tároló elérési útja a externalized Blobok és a tartalom-adatbázisok, amelyek RBS engedélyezve van. 
2. Telepítse és konfigurálja az új SharePoint 2013-farmokhoz. 
3. Helyezze át adatbázisokat, alkalmazások és webhelycsoportok a SharePoint 2010 farmról. az új SharePoint 2013-farmokhoz. Útmutatásért lépjen [a verziófrissítést a SharePoint 2013 áttekintése](https://technet.microsoft.com/library/cc262483.aspx).
4. A StorSimple Adapter telepítse a SharePoint használata az új farmhoz. Lépjen a [telepítse a StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint) eljárásait.
5. Az 1. lépésben feljegyzett információk segítségével RBS engedélyezése ugyanazokat a tartalom-adatbázisokhoz, és adja meg a BLOB tároló elérési útját a SharePoint 2010 telepített példányán használták. Lépjen a [konfigurálása RBS](#configure-rbs) eljárásait. Miután végrehajtotta ezt a lépést, korábban externalized fájlokat az új farm elérhetőnek kell lenniük. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>A StorSimple Adapter for SharePoint frissítése
> [!IMPORTANT]
> Olyan időszakra ütemezze a frissítést, tervezett karbantartási időszak alatt a következő okok miatt fordulhat elő:
> 
> * Korábban externalized tartalom nem lesz elérhető, amíg az adapter újratelepítése után.
> * Minden előző verzióját a StorSimple Adapter for SharePoint eltávolítása után, de az új verzió telepítése előtt a webhely feltöltött tartalom a tartalom-adatbázist fog kell tárolni. Helyezze át a tartalmat a StorSimple-eszköz, az új adapter telepítése után kell. Használhatja a Microsoft `RBS Migrate()` a tartalmainak migrálása a SharePoint mellékelt PowerShell-parancsmagot. További információkért lásd: [tartalmainak Migrálása, vagy onnan máshová RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Az a StorSimple Adapter for SharePoint frissítése
1. Távolítsa el a korábbi verzióját a StorSimple Adapter for SharePoint.
   
   > [!NOTE]
   > Ezzel automatikusan letiltja a RBS, a tartalom-adatbázisokban található. Meglévő Blobok azonban a StorSimple eszközön maradnak. RBS le van tiltva, és térjen vissza a tartalom-adatbázisokban nem lett áttelepítve a blobokat, mert ezek a blobok kérések meghiúsulnak. 
   > 
   > 
2. Az új StorSimple-Adapter telepítése a Sharepointhoz. Az új adapter automatikusan felismeri a tartalom-adatbázisok korábban engedélyezve van vagy le van tiltva a RBS, és a korábbi beállításokat fogja használni.

