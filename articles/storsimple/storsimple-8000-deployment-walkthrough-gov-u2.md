---
title: "A StorSimple 8000 series eszköz kormányzati portálon üzembe helyezése |} Microsoft Docs"
description: "A StorSimple 8000 series eszköz Update 3 és újabb verziók, és a szolgáltatás fut az Azure Government portálon központi telepítésének ajánlott eljárásai és lépéseket ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: 5a622eb5ae14a6c6b0c2dd4eceb6ffdb9733dcff
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>A kormányzati portálon a helyszíni StorSimple eszköz üzembe helyezése

## <a name="overview"></a>Áttekintés
Üdvözöljük a Microsoft Azure StorSimple eszköztelepítő útmutatójában. Ezek az üzembehelyezési oktatóanyagok a StorSimple 8000 Series Update 3 szoftvert futtató, vagy később az Azure Government portálon alkalmazni. Ez az oktatóanyag-sorozat egy konfigurációs ellenőrzőlistát, a konfigurációs Előfeltételek és a StorSimple eszköz részletes konfigurációs lépések listáját tartalmazza.

Az oktatóanyagban szereplő információk arra a feltételezésre alapulnak, hogy áttekintette a biztonsági óvintézkedéseket, valamint hogy kicsomagolta, az állványba helyezte és bekábelezte a StorSimple eszközt. Ha még nem hajtotta végre ezeket a feladatokat, kezdje a [biztonsági óvintézkedések](storsimple-safety.md) áttekintésével. Kövesse az eszköz kicsomagolására, állványra szerelésére és bekábelezésére vonatkozó eszközspecifikus utasításokat.

* [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
* [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy mielőtt hozzákezdene, tekintse át a konfigurációs ellenőrzőlistát. Az üzembehelyezési és konfigurációs folyamatok végrehajtása eltarthat egy ideig.

> [!NOTE]
> A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozat eszközeire érvényesek. A 7000-es sorozathoz tartozó eszközökről teljes körű információkat itt talál: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). A 7000-es sorozatra vonatkozó üzembehelyezési információkat a [StorSimple rendszer gyors üzembehelyezési útmutatójában](http://onlinehelp.storsimple.com/111_Appliance/) találja.


## <a name="deployment-steps"></a>A központi telepítés lépései
Ezen szükséges lépések végrehajtásával konfigurálhatja a StorSimple-eszközt és csatlakoztathatja a StorSimple-eszközkezelő szolgáltatáshoz. A szükséges lépéseken kívül opcionális lépések és eljárások, amelyek során a telepítés befejezéséhez szükség lehet. A részletes üzembehelyezési utasítások jelzik, amikor ezeket az opcionális lépéseket végre kell hajtania.

| Lépés | Leírás |
| --- | --- |
| **ELŐFELTÉTELEK** |Ezeknek kell teljesülniük az üzembe helyezésre való felkészülés során. |
| [Üzembe helyezési konfigurációs ellenőrzőlista](#deployment-configuration-checklist) |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| [Üzembe helyezési előfeltételek](#deployment-prerequisites) |Ezek ellenőrzik, hogy a környezet készen áll a központi telepítés. |
|  | |
| **RÉSZLETES ÜZEMBE HELYEZÉS** |Ezek a lépések szükségesek a StorSimple eszköz üzemi környezetben való telepítéséhez. |
| [1. lépés: Új szolgáltatás létrehozása](#step-1-create-a-new-service) |A felhőfelügyelet és a felhőalapú tárolás beállítása a StorSimple-eszközhöz. *Hagyja ki ezt a lépést, ha már rendelkezik meglévő szolgáltatással más StorSimple eszközökhöz*. |
| [S2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) |Ez a kulcs használatával regisztrálhatja és csatlakoztathatja a StorSimple eszközt a felügyeleti szolgáltatással. |
| [3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Csatlakoztassa az eszközt a hálózathoz, és a beállítás befejezéséhez regisztrálja az Azure-ban a felügyeleti szolgáltatás segítségével. |
| [4. lépés: A minimális eszközbeállítások végrehajtása](#step-4-complete-minimum-device-setup) </br>Választható lehetőség: A StorSimple eszköz frissítése. |A felügyeleti szolgáltatással végezze el az eszköz beállítását, és engedélyezze rajta a tárolást. |
| [5. lépés: Kötettároló létrehozásar](#step-5-create-a-volume-container) |Hozzon létre egy tárolót a kötetek kiépítéséhez. A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. |
| [6. lépés: Kötet létrehozása](#step-6-create-a-volume) |Tárkötet(ek)et építhet ki a StorSimple eszközön a kiszolgálói számára. |
| [7. lépés: Kötet csatlakoztatása, inicializálása és formázása](#step-7-mount-initialize-and-format-a-volume) </br>Választható lehetőség: Az MPIO konfigurálása. |Csatlakoztassa a kiszolgálókat az eszköz által biztosított iSCSI-tárolóhoz. Nem kötelező lépésként beállíthatja az MPIO-t, hogy a kiszolgálók képesek legyenek tűrni kapcsolati, a hálózati és az adapterhibákat. |
| [8. lépés: Biztonsági mentés készítése](#step-8-take-a-backup) |A biztonsági mentés házirendjének beállítása az adatok védelme érdekében |
|  | |
| **EGYÉB ELJÁRÁSOK** |Előfordulhat, hogy a megoldás üzembe helyezése során ezekre az eljárásokra kell majd hivatkoznia. |
| [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) | |
| [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console) | |
| [Keresése és frissítések telepítése](#scan-for-and-apply-updates) | |
| [Egy Windows Server-állomás IQN-nevének lekérése](#get-the-iqn-of-a-windows-server-host) | |
| [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista
A StorSimple eszköz telepítése előtt szüksége lesz a gyűjt adatokat a szoftver konfigurálása az eszközön. Az információk időben történő előkészítésével leegyszerűsíthető a StorSimple eszköz üzembe helyezésének folyamata a környezetben. Töltse le és használja ezt az ellenőrzőlistát a konfigurációs részleteket értesít, az eszköz üzembe helyezése.

[StorSimple üzembehelyezési konfigurációs ellenőrzőlista letöltése](http://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek
Az alábbi szakaszok ismertetik a StorSimple-eszközkezelő szolgáltatás és a StorSimple-eszköz konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetés engedélyezve van a StorSimple-eszközkezelő szolgáltatáshoz. Az előfizetést a [Nagyvállalati Szerződésen](https://azure.microsoft.com/pricing/enterprise-agreement/) keresztül kell megvásárolni.
* Rendelkezik hozzáféréssel olyan terminálemulációs szoftverekhez, mint a PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Az adatközpontban található eszköz esetén
Az eszköz konfigurálása előtt győződjön meg az alábbiakról:

* Az eszköz teljesen ki van csomagolva, állványra van rögzítve és minden kábel be van kötve a tápellátáshoz, a hálózathoz és a soros hozzáféréshez, a következő helyen leírtak szerint:
  
  * [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
  * [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont tűzfalának portjai nyitva vannak az iSCSI és a felhőalapú forgalom számára, [A StorSimple eszköz hálózatkezelési követelményei](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device) című szakaszban leírtaknak megfelelően.

## <a name="step-by-step-deployment"></a>Részletes üzembe helyezés
Az alábbi részletes útmutatás segítségével helyezze üzembe a StorSimple eszközt az adatközpontban.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása
A StorSimple-eszközkezelő szolgáltatás több StorSimple-eszközt is tud kezelni. Hajtsa végre a következő lépésekkel hozza létre a StorSimple Device Manager szolgáltatás egy új példányát.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást. Ezt a tárfiókot akkor fogja használni a rendszer, amikor egy kötettárolót hoz létre.
> 
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.


## <a name="step-2-get-the-service-registration-key"></a>2. lépés: Szolgáltatásregisztrációs kulcs lekérése
Ha a StorSimple-eszközkezelő szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. A kulccsal regisztrálhatja és csatlakoztathatja a StorSimple eszköz a szolgáltatáshoz.

Hajtsa végre az alábbi lépéseket a kormányzati portálon.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel
A StorSimple-höz készült Windows PowerShell-lel végezze el a StorSimple eszköz kezdeti beállítását az alábbiakban ismertetett eljárás alapján. A lépés végrehajtásához terminálemulációs szoftverre lesz szüksége. További információ: [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>4. lépés: Minimális eszközbeállítások végrehajtása.
A StorSimple minimális eszközkonfigurációjához a következőket kell végrehajtania:

* Adjon egy valódi (értelmezhető) nevet az eszköznek.
* Adja meg az eszköz időzónáját.
* Rendeljen rögzített IP-címeket mindkét vezérlőhöz.

Hajtsa végre az alábbi lépéseket az Azure Government portálon a minimális eszközbeállítások végrehajtása.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>5. lépés: Kötettároló létrehozása
A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. Ahhoz, hogy elkezdhessen köteteket kiépíteni a StorSimple eszközön, létre kell hoznia egy kötettárolót.

Hajtsa végre az alábbi lépéseket a kormányzati portálon kötettároló létrehozásához.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>6. lépés: Kötet létrehozása
A kötettároló létrehozása után tárkötetet építhet ki a StorSimple eszközön a kiszolgálók számára. Hajtsa végre az alábbi lépéseket a kormányzati portálon, a kötet létrehozásához.

> [!IMPORTANT]
> StorSimple Device Manager csak dinamikusan kiosztott köteteket hozhat létre.  Azonban nem hozhat létre részben kiosztott köteteket.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>7. lépés: Kötet csatlakoztatása, inicializálása és formázása
Hajtsa végre ezeket a lépéseket a Windows Server-állomáson.

> [!IMPORTANT]
> * Annak érdekében, hogy a StorSimple-megoldás nagy mértékben rendelkezésre álljon, javasolt az MPIO konfigurálását (opcionális) az iSCSI konfigurálása előtt elvégezni a gazdakiszolgálókon. Az MPIO gazdakiszolgálón történő konfigurálásával biztosítható, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.
> * Az MPIO és az iSCSI Windows Server-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-configure-mpio-windows-server.md) című szakaszban találja. Ezekben a StorSimple-kötetek csatolásához, inicializálásához és formázásához szükséges lépéseket is megtalálja.
> * Az MPIO és az iSCSI Linux-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.

Ha mégsem szeretné konfigurálni az MPIO-t, az alábbi lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-állomáson.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>8. lépés: Biztonsági mentés készítése
Az adott időpontban mentett biztonsági másolatok védelmet biztosítanak a kötetek számára, továbbá javítják a rendelkezésre álló helyreállítási lehetőségeket, miközben a helyreállítási időt csökkentik. A StorSimple eszközén kétféle biztonsági mentést készíthet: helyi pillanatképeket és felhőbeli pillanatképeket. Mind a kétféle biztonsági mentés lehet **Ütemezett** vagy **Manuális**.

Hajtsa végre az alábbi lépéseket a kormányzati portálon ütemezett biztonsági mentés létrehozásához.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Manuális biztonsági mentést bármikor létrehozhat. Az eljárásokat a [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) című szakaszban találja.

## <a name="configure-a-new-storage-account-for-the-service"></a>Új tárfiók konfigurálása a szolgáltatáshoz
Ez egy opcionális lépés, amelyet csak akkor kell végrehajtania, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással. A StorSimple-kötettároló létrehozásához Microsoft Azure Storage-fiók szükséges.

Ha az Azure-tárfiókot egy másik régióban szeretné létrehozni, tekintse meg a részletes utasításokat a [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md) című szakaszban.

Hajtsa végre a következő lépéseket kormányzati portálon a **StorSimple Device Manager szolgáltatás** lap.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz
A StorSimple-höz készült Windows PowerShellhez való csatlakozáshoz szükség lesz egy terminálemulációs szoftverre, például a PuTTY-ra. A PuTTY az eszköz soros konzolon keresztül történő közvetlen elérésekor vagy egy telnet-munkamenet távoli számítógépről történő megnyitása során vehető igénybe.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Frissítések keresése és telepítése
Az eszköz frissítése több órát is igénybe vehet. A legújabb frissítés telepítésének lépéseiért lásd: [A 4-es frissítés telepítése](storsimple-8000-install-update-4.md).

## <a name="get-the-iqn-of-a-windows-server-host"></a>Egy Windows Server-állomás IQN-nevének lekérése
Hajtsa végre a következő lépéseket egy Windows Server® 2012 rendszert futtató Windows-állomás iSCSI teljes nevének (IQN) lekéréséhez.

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
Hajtsa végre az alábbi lépéseket a kormányzati portálon csak egy kötet egy azonnali manuális mentést létrehozni a StorSimple eszköz.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Következő lépések
* [Virtuális eszköz](storsimple-8000-cloud-appliance-u2.md) konfigurálása.
* A StorSimple-eszközt a [StorSimple-eszközkezelő szolgáltatás](storsimple-8000-manager-service-administration.md) segítségével kezelheti.

