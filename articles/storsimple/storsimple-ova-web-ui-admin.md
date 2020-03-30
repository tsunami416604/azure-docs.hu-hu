---
title: StorSimple Virtual Array webes felhasználói felület felügyelete | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként hajthatja végre az alapvető eszközfelügyeleti feladatokat a StorSimple virtuális tömb webes felhasználói felületén keresztül.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254728"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb felügyelete a webes felhasználói felület használatával
![beállítási folyamat](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Áttekintés
Az ebben a cikkben található oktatóanyagok a 2016 márciusában futó Microsoft Azure StorSimple virtuális tömbre (más néven a Helyszíni StorSimple virtuális eszközre) vonatkoznak. Ez a cikk a StorSimple virtuális tömbön elvégezhető összetett munkafolyamatokat és felügyeleti feladatokat ismerteti. A StorSimple virtuális tömb a StorSimple Manager szolgáltatás felhasználói felületével (portál felhasználói felülete) és az eszköz helyi webes felhasználói felületével kezelhető. Ez a cikk a webes felhasználói felület használatával végrehajtható feladatokra összpontosít.

A cikk az alábbi oktatóanyagokat tartalmazza:

* A szolgáltatásadat-titkosítási kulcs beszereznie
* Webes felhasználói felület beállítási hibáinak elhárítása
* Naplócsomag létrehozása
* Az eszköz leállítása és újraindítása

## <a name="get-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs beszereznie
A szolgáltatás adattitkosítási kulcs jön létre, amikor regisztrálja az első eszköz a StorSimple Manager szolgáltatás. Ez a kulcs ezután a szolgáltatás regisztrációs kulcsregisztrálásához további eszközök regisztrálásához a StorSimple Manager szolgáltatás.

Ha a szolgáltatás adattitkosítási kulcsát rossz helyre helyezte, és le kell kérnie, hajtsa végre a következő lépéseket a szolgáltatáshoz regisztrált eszköz helyi webes felhasználói felületén.

#### <a name="to-get-the-service-data-encryption-key"></a>A szolgáltatás adattitkosítási kulcsának bekéselése
1. Csatlakozzon a helyi webes felhasználói felülethez. Nyissa meg a Configuration Cloud Settings **(Konfigurációs** > **felhő beállításai) lapot.**
2. A lap alján kattintson a **Szolgáltatásadat-titkosítási kulcs betöltése**elemre. Megjelenik egy kulcs. Másolja és mentse ezt a kulcsot.
   
    ![szolgáltatásadat-titkosítási kulcs beírása 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Webes felhasználói felület beállítási hibáinak elhárítása
Bizonyos esetekben, amikor az eszközt a helyi webes felhasználói felületen keresztül konfigurálja, előfordulhat, hogy hibákba ütközik. Az ilyen hibák diagnosztizálásához és elhárításához futtathatja a diagnosztikai teszteket.

#### <a name="to-run-the-diagnostic-tests"></a>A diagnosztikai tesztek futtatása
1. A helyi webes felhasználói felületen nyissa meg a**Hibaelhárítási diagnosztikai tesztek című** **témakört.** > 
   
    ![diagnosztika futtatása 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. A lap alján kattintson a **Diagnosztikai tesztek futtatása gombra.** Ez teszteket indít el a hálózattal, az eszközzel, a webproxyval, az idővel vagy a felhőbeállításokkal kapcsolatos esetleges problémák diagnosztizálására. Értesítést kap arról, hogy az eszköz teszteket futtat.
3. A tesztek befejezése után megjelennek az eredmények. A következő példa a diagnosztikai tesztek eredményeit mutatja be. Vegye figyelembe, hogy a webproxy-beállítások nincsenek konfigurálva ezen az eszközön, ezért a webproxy-teszt nem futott. A hálózati beállítások, a DNS-kiszolgáló és az időbeállítások összes többi tesztje sikeres volt.
   
    ![futtatás diagnosztika 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Naplócsomag létrehozása
A naplócsomag tartalmazza az összes vonatkozó naplót, amely segítséget nyújt a Microsoft támogatási szolgálatának az eszközekkel kapcsolatos problémák elhárításában. Ebben a kiadásban egy naplócsomag a helyi webes felhasználói felületen keresztül generálható.

#### <a name="to-generate-the-log-package"></a>A naplócsomag létrehozása
1. A helyi webes felhasználói felületen nyissa meg **a Hibaelhárítási** > **rendszernaplók című témakört.**
   
    ![naplócsomag létrehozása 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. A lap alján kattintson a **Naplócsomag létrehozása gombra.** Létrejön a rendszernaplók csomagja. Ez eltart pár percig.
   
    ![2. naplócsomag létrehozása](./media/storsimple-ova-web-ui-admin/image32.png)
   
    A csomag sikeres létrehozása után értesítést kap, és a lap frissül, hogy jelezze a csomag létrehozásának időpontját és dátumát.
   
    ![3-as naplócsomag létrehozása](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kattintson **a Naplócsomag letöltése gombra.** A rendszer egy tömörített csomagot tölt le.
   
    ![4 naplócsomag létrehozása](./media/storsimple-ova-web-ui-admin/image34.png)
4. Kicsomagolhatja a letöltött naplócsomagot, és megtekintheti a rendszer naplófájljait.

## <a name="shut-down-and-restart-your-device"></a>Az eszköz leállítása és újraindítása
A virtuális eszközt leállíthatja vagy újraindíthatja a helyi webes felhasználói felület használatával. Azt javasoljuk, hogy az újraindítás előtt vegye offline állapotba a köteteket vagy megosztásokat a gazdagépen, majd az eszközön. Ez minimálisra csökkenti az adatsérülés lehetőségét. 

#### <a name="to-shut-down-your-virtual-device"></a>A virtuális eszköz leállítása
1. A helyi webes felhasználói felületen nyissa meg a **Karbantartási energiagazdálkodási** > **beállítások lehetőséget.**
2. A lap alján kattintson a **Leállítás**gombra.
   
    ![eszköz leállítása 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Megjelenik egy figyelmeztetés, amely szerint az eszköz leállítása megszakítja a folyamatban lévő i/o-t, ami leállást eredményez. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![eszköz leállítása figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Értesítést kap arról, hogy a leállítás megkezdődött.
   
    ![eszköz leállítása megkezdődött](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Az eszköz most leáll. Ha el szeretné indítani az eszközt, ezt a Hyper-V Manageren keresztül kell megtennie.

#### <a name="to-restart-your-virtual-device"></a>A virtuális eszköz újraindítása
1. A helyi webes felhasználói felületen nyissa meg a **Karbantartási energiagazdálkodási** > **beállítások lehetőséget.**
2. A lap alján kattintson az **Újraindítás gombra.**
   
    ![eszköz újraindítása](./media/storsimple-ova-web-ui-admin/image36.png)
3. Megjelenik egy figyelmeztetés, amely szerint az eszköz újraindítása megszakítja a folyamatban lévő IOs-t, ami leállást eredményez. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![újraindítás – figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Értesítést kap arról, hogy az újraindítás megkezdődött.
   
    ![újraindítás megindítva](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Amíg az újraindítás folyamatban van, megszakad a kapcsolat a felhasználói felülettel. Az újraindításfigyeléshez rendszeresen frissítheti a felhasználói felületet. Azt is megteheti, hogy figyeli az eszköz újraindítási állapotát a Hyper-V Manager segítségével.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [kezelheti eszközét a StorSimple Manager szolgáltatással.](storsimple-virtual-array-manager-service-administration.md)

