---
title: A StorSimple Virtual Array webes felhasználói felület felügyeleti |} A Microsoft Docs
description: Ismerteti, hogyan hajthat végre egyszerű eszköz lesz a felügyeleti feladatokat a StorSimple Virtual Array webes felületen keresztül.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630469"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>A webes felhasználói felület használata a StorSimple Virtual Array felügyeletéhez
![a telepítő folyamat](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Áttekintés
Ebben a cikkben szereplő oktatóanyagok vonatkozik a Microsoft Azure StorSimple Virtual Array (más néven a StorSimple helyszíni virtuális eszköz) 2016. március általánosan (elérhetővé tétel GA) verzióját futtatja. Ez a cikk ismerteti az egyes a bonyolult munkafolyamatok és a feladat, amely a StorSimple Virtual Array hajtható végre. Kezelheti a StorSimple Virtual Array használatával a StorSimple Manager szolgáltatás (néven a portál felhasználói Felületét) felhasználói felület és az eszköz helyi webes felületén. Ez a cikk foglalkozik, hogy a webes felhasználói felülete segítségével végrehajtható feladatokat.

A cikk az alábbi oktatóanyagokat tartalmazza:

* A szolgáltatásadat-titkosítási kulcs beszerzése
* Webes felhasználói felület beállítási hibák elhárítása
* -Log-csomag létrehozása
* Az eszköz leállítása és újraindítása

## <a name="get-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs beszerzése
A szolgáltatásadat-titkosítási kulcs jön létre, amikor az első eszköz regisztrálja a StorSimple Manager szolgáltatásban. Ez a kulcs van, majd a szolgáltatás regisztrációs kulcsával együtt szükséges további eszközöket regisztrálni a StorSimple Manager szolgáltatásban.

Ha rendelkezik ügyfél elveszíti a szolgáltatásadat-titkosítási kulcsot, és meg is lekérheti azt kell, végezze el az alábbi lépéseket a helyi webes felhasználói felületen, az eszköz regisztrálva a szolgáltatásban.

#### <a name="to-get-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs beolvasásához
1. Csatlakozzon a helyi webes felületén. Lépjen a **konfigurációs** > **Felhőbeállítások**.
2. Kattintson a lap alján **Get szolgáltatásadat-titkosítási kulcs**. Egy kulcsot fog megjelenni. Másolja és mentse ezt a kulcsot.
   
    ![szolgáltatásadat-titkosítási kulcs 1 beolvasása](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Webes felhasználói felület beállítási hibák elhárítása
Bizonyos esetekben az eszköz helyi webes felhasználói felületen keresztül konfigurálásakor előfordulhat, hogy hibákba ütközik. Felderítésére, és az ilyen hibák elhárítása a diagnosztikai tesztek is futtathatja.

#### <a name="to-run-the-diagnostic-tests"></a>A diagnosztikai tesztek futtatása
1. A helyi webes felhasználói felületen váltson **hibaelhárítás** > **diagnosztikai tesztek**.
   
    ![1 diagnosztika futtatása](./media/storsimple-ova-web-ui-admin/image29.png)
2. Kattintson a lap alján **diagnosztikai tesztek futtatása**. A szolgáltatás kezdeményez a hálózat, az eszköz, a webalkalmazás-proxy lehetséges problémák diagnosztizálásához tesztek idő vagy a felhő beállításait. Értesíteni fogjuk, hogy az eszköz teszteket futtat.
3. Miután elvégezte a vizsgálatokat, az eredmények jelennek meg. Az alábbi példa bemutatja a diagnosztikai tesztek eredményét. Vegye figyelembe, hogy a webproxy beállításai nem lettek konfigurálva ezen az eszközön, és ezért a webalkalmazás-proxy teszt nem futott. A hálózati beállításokat, a DNS-kiszolgáló és az időbeállítások összes egyéb teszt sikeres volt.
   
    ![2 diagnosztika futtatása](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>-Log-csomag létrehozása
A napló csomag áll a megfelelő naplók, amelyek segítségére lehetnek Support bármely eszköz kapcsolatos hibák elhárítása. Ebben a kiadásban a napló csomag létrehozható a helyi webes felhasználói felületen.

#### <a name="to-generate-the-log-package"></a>A napló-csomag létrehozása
1. A helyi webes felhasználói felületen váltson **hibaelhárítás** > **rendszernaplók**.
   
    ![1\. napló csomagjának létrehozása](./media/storsimple-ova-web-ui-admin/image31.png)
2. Kattintson a lap alján **log-csomag létrehozása**. A rendszer naplóit tartalmazó csomagot hoz létre. Ez eltarthat néhány percig.
   
    ![napló 2. csomag létrehozása](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Ön értesítést kap, ha a csomag sikeresen létrejött, és az oldal frissül, és a csomag létrehozásának időpontját jelzi.
   
    ![3\. napló csomagjának létrehozása](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kattintson a **log letöltőcsomag**. Egy tömörített csomagot a rendszer letölti a rendszeren.
   
    ![4\. napló csomagjának létrehozása](./media/storsimple-ova-web-ui-admin/image34.png)
4. Tömörítse ki a letöltött log csomagot, és megtekintheti a rendszernapló fájljaiban.

## <a name="shut-down-and-restart-your-device"></a>Állítsa le és indítsa újra az eszközt
Állítsa le, vagy indítsa újra a virtuális eszköz helyi webes felhasználói felület használatával. Azt javasoljuk, hogy az újraindítás előtt igénybe a kötetek vagy megosztások kapcsolat nélküli módban a gazdagép és az eszköz. Ezzel a minimálisra csökkentheti adatsérülés lehetőségét. 

#### <a name="to-shut-down-your-virtual-device"></a>A virtuális eszköz leállítása
1. A helyi webes felhasználói felületen váltson **karbantartási** > **energiaellátási beállítások**.
2. Kattintson a lap alján **leállítási**.
   
    ![1\. eszköz leállítása](./media/storsimple-ova-web-ui-admin/image36.png)
3. Figyelmezteti, hogy az eszköz egy leállítási szakítja meg egy leállást eredményez a folyamatban lévő i figyelmeztetés jelenik meg. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![eszköz leállítás figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Értesíteni fogjuk, hogy a leállás kezdeményeztek.
   
    ![eszköz leállítás használatába](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Az eszköz most már le fog állni. Ha el szeretné indítani az eszközt, szüksége lesz a Hyper-V kezelője segítségével valósítható meg.

#### <a name="to-restart-your-virtual-device"></a>A virtuális eszköz újraindítása
1. A helyi webes felhasználói felületen váltson **karbantartási** > **energiaellátási beállítások**.
2. Kattintson a lap alján **indítsa újra a**.
   
    ![eszköz újraindítása](./media/storsimple-ova-web-ui-admin/image36.png)
3. Egy figyelmeztetés fog megjelenni arról, hogy az eszköz újraindítása szakítja meg minden, az IOs, folyamatban volt egy leállást eredményez. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![újraindítási figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Értesíteni fogjuk, hogy az újraindítás kezdeményezéséről.
   
    ![Újraindítás elindítva](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Az újraindítás folyamatban van, amíg a kapcsolat a felhasználói felületre elvesznek. A felhasználói felület rendszeres időközönként frissítésével figyelemmel kísérheti az újraindítást. Azt is megteheti az eszköz újraindítási állapotát a Hyper-V Manager segítségével figyelheti.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [felügyelje az eszközt a StorSimple Manager szolgáltatás használatával](storsimple-virtual-array-manager-service-administration.md).

