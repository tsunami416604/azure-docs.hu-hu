---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179162"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Frissítse a SharePoint 2010-et SharePoint 2013-ra, majd telepítse a StorSomple adaptert a SharePointhoz
> [!IMPORTANT]
> Az RBS-en keresztül külső tárolóba korábban áthelyezett fájlok csak a frissítés befejeződése és az RBS-szolgáltatás ismételt engedélyezése után lesznek elérhetők. A felhasználói hatás csökkentése érdekében hajtsa végre a frissítést vagy az újratelepítést a tervezett karbantartási időszakban.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>A SharePoint 2010 frissítése SharePoint 2013-ra, majd az adapter telepítése
1. A SharePoint 2010-farmban vegye figyelembe a kikülső blob-k BLOB-tároló elérési útját és azokat a tartalom-adatbázisokat, amelyekhez engedélyezve van az RBS. 
2. Telepítse és konfigurálja az új SharePoint 2013-farmot. 
3. Adatbázisok, alkalmazások és webhelycsoportok áthelyezése a SharePoint 2010-farmról az új SharePoint 2013-farmra. További információt [a SharePoint 2013 frissítési folyamatának áttekintése című témakörben talál.](https://technet.microsoft.com/library/cc262483.aspx)
4. Telepítse a SharePoint StorSimple adapterét az új farmra. Az [eljárásokhoz nyissa meg a StorSimple adapter telepítése a SharePointhoz](#install-the-storsimple-adapter-for-sharepoint) című témakört.
5. Az 1. Az [ELJÁRÁSOK konfigurálása című útmutató](#configure-rbs) az RBS konfigurálása című. A lépés elvégzése után a korábban kikülsővé vált fájloknak elérhetőnek kell lenniük az új farmról. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>A SharePoint-alapú StorSimple adapter frissítése
> [!IMPORTANT]
> A frissítés tervezett karbantartási időszakban történő ütemezése a következő okok miatt történik:
> 
> * A korábban kihelyezett tartalom csak az adapter újratelepítése után lesz elérhető.
> * A StorSimple Adapter sharepoint-hoz való eltávolítása után, de az új verzió telepítése előtt a webhelyre feltöltött tartalmak a tartalom-adatbázisban tárolódnak. Az új adapter telepítése után át kell helyeznie a tartalmat a StorSimple eszközre. A SharePoint `RBS Migrate()` részét képező Microsoft PowerShell-parancsmag segítségével áttelepítheti a tartalmat. További információt a [Tartalom áttelepítése az RBS-be vagy az RBS-ből című](https://technet.microsoft.com/library/ff628255.aspx)témakörben talál. 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>A SharePoint-alapú StorSimple adapter frissítése
1. Távolítsa el a SharePoint-hoz szükséges StorSimple adapter korábbi verzióját.
   
   > [!NOTE]
   > Ez automatikusan letiltja az RBS-t a tartalom-adatbázisokban. Azonban a meglévő BLOB-k maradnak a StorSimple eszközön. Mivel az RBS le van tiltva, és a BLOB-k nem lettek visszatelepítve a tartalom-adatbázisokba, a BLOB-kra vonatkozó kérelmek sikertelenek lesznek. 
   > 
   > 
2. Telepítse az új StorSimple adaptert a SharePointhoz. Az új adapter automatikusan felismeri azokat a tartalom-adatbázisokat, amelyek korábban engedélyezve voltak vagy le voltak tiltva az RBS számára, és a korábbi beállításokat fogják használni.

