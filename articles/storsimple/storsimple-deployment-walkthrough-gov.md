---
title: "Kormánya portálon StorSimple eszköz üzembe helyezése |} Microsoft Docs"
description: "Az 1. frissítés StorSimple eszköz és az Azure Government portálon szolgáltatás központi telepítésének ajánlott eljárásai és lépéseket ismerteti."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 3fe3648d-e0b6-4928-a2cb-8d3ccc50d62a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/17/2016
ms.author: v-sharos
ms.openlocfilehash: f120caf4ea21299e52782db33994b9bd8f63780d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>A kormányzati portálon a helyszíni StorSimple eszköz üzembe helyezése
[!INCLUDE [storsimple-version-selector-deploy-gov](../../includes/storsimple-version-selector-deploy-gov.md)]

## <a name="overview"></a>Áttekintés
Üdvözöljük a Microsoft Azure StorSimple eszköztelepítő útmutatójában. Ezek az üzembehelyezési oktatóanyagok a StorSimple 8000 Series Update 1 szoftverek futtatása az Azure Government portálon vonatkozik. Ez az oktatóanyag-sorozat bemutatja a StorSimple eszköz konfigurálásának módját, továbbá tartalmazza a konfigurációhoz szükséges ellenőrzőlistát, előfeltételeket és részletes lépéseket.

Az oktatóanyagban szereplő információk arra a feltételezésre alapulnak, hogy áttekintette a biztonsági óvintézkedéseket, valamint hogy kicsomagolta, az állványba helyezte és bekábelezte a StorSimple eszközt. Ha még nem hajtotta végre ezeket a feladatokat, kezdje a [biztonsági óvintézkedések](storsimple-safety.md) áttekintésével. A modelltől függően ezután kicsomagolhatja, állványra szerelheti és bekábelezheti az eszközt, a következő helyen található utasításokat követve:

* [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
* [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy mielőtt hozzákezdene, tekintse át a konfigurációs ellenőrzőlistát. Az üzembehelyezési és konfigurációs folyamatok végrehajtása eltarthat egy ideig.

> [!NOTE]
> A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozat eszközeire érvényesek. A 7000-es sorozathoz tartozó eszközökről teljes körű információkat itt talál: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). A 7000-es sorozatra vonatkozó üzembehelyezési információkat a [StorSimple rendszer gyors üzembehelyezési útmutatójában](http://onlinehelp.storsimple.com/111_Appliance/) találja.
> 
> 

## <a name="deployment-steps"></a>A központi telepítés lépései
Ezen szükséges lépések végrehajtásával konfigurálhatja a StorSimple eszközt és csatlakoztathatja a StorSimple Manager szolgáltatáshoz. A szükséges lépéseken kívül opcionális lépések és eljárások végrehajtására is szükség lehet az üzembe helyezés során. A részletes üzembehelyezési utasítások jelzik, amikor ezeket az opcionális lépéseket végre kell hajtania.

| Lépés | Leírás |
| --- | --- |
| **ELŐFELTÉTELEK** |Ezeknek kell teljesülniük az üzembe helyezésre való felkészülés során. |
| Üzembehelyezési konfigurációs ellenőrzőlista. |Ezzel az ellenőrzőlistával információkat gyűjthet és rögzíthet az üzembe helyezés előtt és közben. |
| Üzembehelyezési előfeltételek. |Ezek ellenőrzik, hogy a környezet készen áll-e az üzembe helyezésre. |
|  | |
| **RÉSZLETES ÜZEMBE HELYEZÉS** |Ezek a lépések szükségesek a StorSimple eszköz üzemi környezetben való telepítéséhez. |
| 1. lépés: Új szolgáltatás létrehozása. |A felhőfelügyelet és a felhőalapú tárolás beállítása a StorSimple eszközhöz. Hagyja ki ezt a lépést, ha már rendelkezik meglévő szolgáltatással más StorSimple eszközökhöz. |
| 2. lépés: Szolgáltatásregisztrációs kulcs lekérése. |Ezzel a kulccsal regisztrálhatja és csatlakoztathatja az eszközét a felügyeleti szolgáltatáshoz. |
| 3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel. |Csatlakoztassa az eszközt a hálózathoz, és a beállítás befejezéséhez regisztrálja az Azure-ban a felügyeleti szolgáltatás segítségével. |
| 4. lépés: Minimális eszközbeállítások végrehajtása.</br>Választható lehetőség: A StorSimple eszköz frissítése. |A felügyeleti szolgáltatással végezze el az eszköz beállítását, és engedélyezze rajta a tárolást. |
| 5. lépés: Kötettároló létrehozása. |Hozzon létre egy tárolót a kötetek kiépítéséhez. A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. |
| 6. lépés: Kötet létrehozása. |Tárkötet(ek)et építhet ki a StorSimple eszközön a kiszolgálói számára. |
| 7. lépés: Kötet csatlakoztatása, inicializálása és formázása.</br>Választható lehetőség: Az MPIO konfigurálása. |Csatlakoztassa a kiszolgálókat az eszköz által biztosított iSCSI-tárolóhoz. Választható lehetőségként konfigurálhatja az MPIO-t, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat. |
| 8. lépés: Biztonsági mentés készítése. |A biztonsági mentés házirendjének beállítása az adatok védelme érdekében |
|  | |
| **EGYÉB ELJÁRÁSOK** |Előfordulhat, hogy a megoldás üzembe helyezése során ezekre az eljárásokra kell majd hivatkoznia. |
| Új tárfiók konfigurálása a szolgáltatáshoz. | |
| A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz. | |
| Keressen, és alkalmazza a frissítéseket. | |
| Egy Windows Server-állomás IQN-nevének lekérése | |
| Manuális biztonsági mentés létrehozása. | |
| Az MPIO konfigurálása. | |

## <a name="deployment-configuration-checklist"></a>Üzembehelyezési konfigurációs ellenőrzőlista
Az alábbi üzembehelyezési konfigurációs ellenőrzőlista azokat az információkat sorolja fel, amelyeket a StorSimple eszköz szoftverének konfigurálása előtt és közben össze kell gyűjtenie. Az információk időben történő előkészítésével leegyszerűsíthető a StorSimple eszköz üzembe helyezésének folyamata a környezetben. Továbbá ezzel az ellenőrzőlistával lejegyezheti a konfigurációs részleteket az eszköz üzembe helyezése során.

| Fázis | Paraméter | Részletek | Értékek |
| --- | --- | --- | --- |
| **Az eszköz bekábelezése** |Soros hozzáférés |Az eszköz kezdeti konfigurációja |Igen/nem |
|  | | | |
| **Az eszköz konfigurálása és regisztrálása** |Data 0 hálózati beállítások |Data 0 IP-címe:</br>Alhálózati maszk:</br>Átjáró:</br>Elsődleges DNS-kiszolgáló:</br>Elsődleges NTP-kiszolgáló:</br>IP/FQDN webes proxykiszolgáló (opcionális):</br>Webes proxy portja: | |
| &nbsp; |Az eszköz rendszergazdai jelszava |A jelszónak 8–15 karakter hosszúságúnak kell lennie, és tartalmaznia kell kisbetűt, nagybetűt, számot és speciális karaktert. | |
| &nbsp; |StorSimple Snapshot Manager jelszava |A jelszónak 14 vagy 15 karakter hosszúságúnak kell lennie, és tartalmaznia kell kisbetűt, nagybetűt, számot és speciális karaktert. | |
| &nbsp; |Szolgáltatásregisztrációs kulcs |Ezt a kulcsot az Azure portálon jön létre. | |
| &nbsp; |Szolgáltatásadat-titkosítási kulcs |Ez a kulcs akkor jön létre, amikor a rendszer regisztrálja az eszközt a felügyeleti szolgáltatással a StorSimple-höz készült Windows PowerShell-lel. Másolja ki ezt a kulcsot, és mentse egy biztonságos helyre. | |
|  | | | |
| **Minimális eszközbeállítások végrehajtása** |Rövid név megadása az eszközhöz |Ez egy leíró név az eszköz számára. | |
| &nbsp; |Időzóna |Az eszköz minden ütemezett művelethez ezt az időzónát használja. | |
| &nbsp; |Másodlagos DNS-kiszolgáló |Ez egy szükséges konfiguráció. | |
| &nbsp; |Hálózati adapter: Data 0 vezérlő rögzített IP-címei |Ezeknek az IP-címeknek irányíthatóknak kell lenniük az internet felé.</br>0. vezérlő rögzített IP-címe:</br>1. vezérlő rögzített IP-címe: | |
|  | | | |
| **További hálózatiadapter-beállítások** |Hálózati adapter: Data 1</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót. |Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró: | |
| &nbsp; |Hálózati adapter: Data 2</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót. |Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró: | |
| &nbsp; |Hálózati adapter: Data 3</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót. |Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró: | |
| &nbsp; |Hálózati adapter: Data 4</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót. |Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró: | |
| &nbsp; |Hálózati adapter: Data 5</br>Ha az iSCSI engedélyezve van, ne konfigurálja az átjárót. |Felhasználási cél: Felhő/iSCSI/nincs használatban</br>IP-cím:</br>Alhálózati maszk:</br>Átjáró: | |
|  | | | |
| **Kötettároló létrehozása** |Kötettároló neve: |A tároló neve | |
| &nbsp; |Azure Storage-fiók: |A kötettárolóhoz társítandó tárfióknév és elérési kulcs | |
| &nbsp; |Felhőalapú tárolás titkosítási kulcsa: |Titkosítási kulcs az egyes tárolókban való tároláshoz | |
|  | | | |
| **Kötet létrehozása** |Az egyes kötetek részletei |Kötet neve: | |
| &nbsp; |&nbsp; |Méret: | |
| &nbsp; |&nbsp; |Használat típusa: | |
| &nbsp; |&nbsp; |ACR neve: | |
| &nbsp; |&nbsp; |Alapértelmezett biztonsági mentési házirend: | |
|  | | | |
| **Kötet csatlakoztatása, inicializálása és formázása** |A tárolóhoz csatlakozó egyes gazdakiszolgálók részletei |Windows Server neve: | |
| &nbsp; |&nbsp; |Windows Server IQN: | |
| &nbsp; |&nbsp; |Windows Server kötetneve: | |
| &nbsp; |&nbsp; |NTFS csatlakozási pont/meghajtóbetűjel: | |

## <a name="deployment-prerequisites"></a>Üzembehelyezési előfeltételek
Az alábbi szakaszok ismertetik a StorSimple Manager szolgáltatás és a StorSimple eszköz konfigurációs előfeltételeit.

### <a name="for-the-storsimple-manager-service"></a>A StorSimple Manager szolgáltatás esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetés engedélyezve van a StorSimple Manager szolgáltatáshoz. Az előfizetést a [Nagyvállalati Szerződésen](https://azure.microsoft.com/pricing/enterprise-agreement/) keresztül kell megvásárolni.
* Rendelkezik hozzáféréssel olyan terminálemulációs szoftverekhez, mint a PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Az adatközpontban található eszköz esetén
Az eszköz konfigurálása előtt győződjön meg az alábbiakról:

* Az eszköz teljesen ki van csomagolva, állványra van rögzítve és minden kábel be van kötve a tápellátáshoz, a hálózathoz és a soros hozzáféréshez, a következő helyen leírtak szerint:
  
  * [A StorSimple 8100 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8100-hardware-installation.md)
  * [A StorSimple 8600 kicsomagolása, állványra szerelése és bekábelezése](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén
Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont tűzfalának portjai nyitva vannak az iSCSI és a felhőalapú forgalom számára, [A StorSimple eszköz hálózatkezelési követelményei](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device) című szakaszban leírtaknak megfelelően.

## <a name="step-by-step-deployment"></a>Részletes üzembe helyezés
Az alábbi részletes útmutatás segítségével helyezze üzembe a StorSimple eszközt az adatközpontban.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása
A StorSimple Manager szolgáltatás több StorSimple eszközt is tud kezelni. Az alábbi lépések végrehajtásával hozza létre a StorSimple Manager szolgáltatás egy új példányát.

[!INCLUDE [storsimple-create-new-service-gov](../../includes/storsimple-create-new-service-gov.md)]

> [!IMPORTANT]
> Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást. Ezt a tárfiókot akkor fogja használni a rendszer, amikor egy kötettárolót hoz létre.
> 
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>2. lépés: Szolgáltatásregisztrációs kulcs lekérése
Ha a StorSimple Manager szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. A kulccsal regisztrálhatja és csatlakoztathatja a StorSimple eszköz a szolgáltatáshoz.

Hajtsa végre az alábbi lépéseket a kormányzati portálon.

[!INCLUDE [storsimple-get-service-registration-key-gov](../../includes/storsimple-get-service-registration-key-gov.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>3. lépés: Az eszköz konfigurálása és regisztrálása a StorSimple-höz készült Windows PowerShell-lel
A StorSimple-höz készült Windows PowerShell-lel végezze el a StorSimple eszköz kezdeti beállítását az alábbiakban ismertetett eljárás alapján. A lépés végrehajtásához terminálemulációs szoftverre lesz szüksége. További információ: [A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device-gov](../../includes/storsimple-configure-and-register-device-gov.md)]

## <a name="step-4-complete-minimum-device-setup"></a>4. lépés: Minimális eszközbeállítások végrehajtása.
A StorSimple minimális eszközkonfigurációjához a következőket kell végrehajtania:

* Állítsa be a másodlagos DNS-kiszolgálót.
* Engedélyezze az iSCSI-t legalább egy hálózati adapteren.
* Rendeljen rögzített IP-címeket mindkét vezérlőhöz.

Hajtsa végre a következő lépéseket a kormányzati portálra, ahol a minimális eszközbeállítások végrehajtása.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>5. lépés: Kötettároló létrehozása
A kötettároló tárfiók-, sávszélesség- és titkosítási beállításokat biztosít minden benne tárolt kötet számára. Ahhoz, hogy elkezdhessen köteteket kiépíteni a StorSimple eszközön, létre kell hoznia egy kötettárolót.

Hajtsa végre az alábbi lépéseket a kormányzati portálon kötettároló létrehozásához.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>6. lépés: Kötet létrehozása
A kötettároló létrehozása után tárkötetet építhet ki a StorSimple eszközön a kiszolgálók számára. Hajtsa végre az alábbi lépéseket a kormányzati portálon, a kötet létrehozásához.

> [!IMPORTANT]
> Az Azure StorSimple csak dinamikusan kiosztott köteteket hozhat létre.  Nem hozható létre teljesen vagy részben kiosztott köteteket az Azure StorSimple rendszeren.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>7. lépés: Kötet csatlakoztatása, inicializálása és formázása
Hajtsa végre ezeket a lépéseket a Windows Server-állomáson.

> [!IMPORTANT]
> * Annak érdekében, hogy a StorSimple-megoldás nagy mértékben rendelkezésre álljon, javasolt az MPIO konfigurálását (opcionális) az iSCSI konfigurálása előtt elvégezni a gazdakiszolgálókon. Az MPIO gazdakiszolgálón történő konfigurálásával biztosítható, hogy a kiszolgálók képesek legyenek tűrni a kapcsolati, a hálózati és az adapterhibákat.
> * Az MPIO és az iSCSI Windows Server-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-configure-mpio-windows-server.md) című szakaszban találja. Ezekben a StorSimple-kötetek csatolásához, inicializálásához és formázásához szükséges lépéseket is megtalálja.
> * Az MPIO és az iSCSI Linux-állomásra való telepítési és konfigurálási utasításait [Az MPIO konfigurálása a StorSimple Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.
> 
> 

Ha mégsem szeretné konfigurálni az MPIO-t, az alábbi lépések végrehajtásával csatlakoztassa, inicializálja és formázza a StorSimple-köteteket egy Windows Server-állomáson.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>8. lépés: Biztonsági mentés készítése
Az adott időpontban mentett biztonsági másolatok védelmet biztosítanak a kötetek számára, továbbá javítják a rendelkezésre álló helyreállítási lehetőségeket, miközben a helyreállítási időt csökkentik. A StorSimple eszközén kétféle biztonsági mentést készíthet: helyi pillanatképeket és felhőbeli pillanatképeket. Mind a kétféle biztonsági mentés lehet **Ütemezett** vagy **Manuális**.

Hajtsa végre az alábbi lépéseket a kormányzati portálon ütemezett biztonsági mentés létrehozásához.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Manuális biztonsági mentést bármikor létrehozhat. Az eljárásokat a [Manuális biztonsági mentés létrehozása](#create-a-manual-backup) című szakaszban találja.

## <a name="configure-a-new-storage-account-for-the-service"></a>Új tárfiók konfigurálása a szolgáltatáshoz
Ez egy opcionális lépés, amelyet csak akkor kell végrehajtania, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással. A StorSimple-kötettároló létrehozásához Microsoft Azure Storage-fiók szükséges.

Ha az Azure-tárfiókot egy másik régióban szeretné létrehozni, tekintse meg a részletes utasításokat a [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md) című szakaszban.

Hajtsa végre a következő lépéseket kormányzati portálon a **StorSimple Manager szolgáltatás** lap.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>A PuTTY használata az eszköz soros konzoljához való csatlakozáshoz
A StorSimple-höz készült Windows PowerShellhez való csatlakozáshoz szükség lesz egy terminálemulációs szoftverre, például a PuTTY-ra. A PuTTY az eszköz soros konzolon keresztül történő közvetlen elérésekor vagy egy telnet-munkamenet távoli számítógépről történő megnyitása során vehető igénybe.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Frissítések keresése és telepítése
Az eszköz frissítése több órát is igénybe vehet. Az alábbi lépések végrehajtásával frissítéseket kereshet, és telepítheti azokat az eszközre.

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>Az eszköz frissítése
1. Az eszköz **Gyors üzembe helyezés** oldalán kattintson az **Eszközök** elemre. Válassza ki a fizikai eszközt, és kattintson a **Karbantartás**, majd a **Frissítések keresése** lehetőségre.  
2. Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. Ha vannak elérhető frissítések, a **Frissítések keresése** felirat helyett a **Frissítések telepítése** szöveg látható. Kattintson a **Frissítések telepítése** elemre.
3. A rendszer létrehoz egy frissítési feladatot. A frissítés állapotának nyomon követéséhez lépjen a **Feladatok** elemre.
   
   > [!NOTE]
   > A frissítési feladat elindulásakor az állapot azonnal 50 százalékosként jelenik meg. Az állapot ezután csak a frissítési feladat végeztével vált 100 százalékosra. A frissítési folyamat nem jeleníti meg valós időben a frissítés állapotát.
   > 
   > 
4. Az eszköz sikeres frissítése után engedélyezze a Data 2 és a Data 3 hálózati adaptert, ha ezek le voltak tiltva.

## <a name="get-the-iqn-of-a-windows-server-host"></a>Egy Windows Server-állomás IQN-nevének lekérése
Hajtsa végre a következő lépéseket egy Windows Server® 2012 rendszert futtató Windows-állomás iSCSI teljes nevének (IQN) lekéréséhez.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása
Hajtsa végre az alábbi lépéseket a kormányzati portálon csak egy kötet egy azonnali manuális mentést létrehozni a StorSimple eszköz.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup-gov.md)]

## <a name="configure-mpio"></a>Az MPIO konfigurálása
A többutas I/O (MPIO) egy opcionális szolgáltatás, és alapesetben nincs telepítve a Windows Serveren. Szolgáltatásként lehet telepíteni a Kiszolgálókezelőn keresztül. Az MPIO telepítési utasításait [Az MPIO konfigurálása a StorSimple eszközhöz](storsimple-configure-mpio-windows-server.md) című szakaszban találja.

Az MPIO Linux-állomáshoz csatlakoztatott StorSimple eszközre való telepítési utasításait [Az MPIO konfigurálása Linux-állomáshoz](storsimple-configure-mpio-on-linux.md) című szakaszban találja.

> [!NOTE]
> StorSimple virtuális eszközökön az MPIO használata nem támogatott.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Virtuális eszköz](storsimple-virtual-device-u2.md) konfigurálása.
* A StorSimple eszközt a [StorSimple Manager szolgáltatás](https://msdn.microsoft.com/library/azure/dn772396.aspx) segítségével kezelheti.

