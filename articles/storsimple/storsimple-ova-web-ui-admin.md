---
title: "A StorSimple virtuális tömb webes felhasználói felület felügyeleti |} Microsoft Docs"
description: "Ismerteti, hogyan hajthat végre alapszintű eszköz felügyeleti feladatokat a StorSimple virtuális tömb webes felhasználói felületen."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 989e7b697f9b527df549fb32be18edd1d3c8d224
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>A webes felhasználói felület segítségével felügyelheti a StorSimple virtuális tömb
![a telepítő folyamatábra](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Áttekintés
Ebben a cikkben az oktatóanyagok vonatkoznak a Microsoft Azure StorSimple virtuális tömb (más néven a helyszíni virtuális eszközét) 2016. március általános elérhetőségével (GA) verzióját futtatja. Ez a cikk ismerteti az egyes a bonyolult munkafolyamatok és a feladat, amely a StorSimple virtuális tömb hajtható végre. Kezelheti a StorSimple virtuális tömb, használja a StorSimple Manager szolgáltatás (néven a portál felhasználói felületének) felhasználói felület és az eszköz helyi webes felhasználói Felületét. Ez a cikk összpontosít, hogy a webes felhasználói felületen végrehajtható műveletek.

Ez a cikk tartalmazza az alábbi oktatóanyagok:

* A szolgáltatásadat-titkosítási kulcs beszerzése
* Webes felhasználói felület telepítő hibák elhárítása
* A napló-csomag létrehozása
* Állítsa le és indítsa újra az eszközt

## <a name="get-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs beszerzése
A szolgáltatásadat-titkosítási kulcs jön létre, amikor az első eszköz regisztrálása a StorSimple Manager szolgáltatásban. Ezt a kulcsot meg kell majd további eszközök regisztrálása a StorSimple Manager szolgáltatás a szolgáltatás regisztrációs kulcsával szükséges.

Ha a szolgáltatásadat-titkosítási kulcs, és ennek lekéréséhez szükséges feljegyezni hajtsa végre a következő lépéseket a helyi webes felhasználói felület az eszköz regisztrálva a szolgáltatásban.

#### <a name="to-get-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs segítségével
1. Csatlakozás a helyi webes felhasználói felületen. Ugrás a **konfigurációs** > **Felhőbeállítások**.
2. Kattintson a lap alján **Get szolgáltatásadat-titkosítási kulcs**. A kulcs jelenik meg. Másolja ki és mentse ezt a kulcsot.
   
    ![szolgáltatásadat-titkosítási kulcs 1 beolvasása](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Webes felhasználói felület telepítő hibák elhárítása
Bizonyos esetekben amikor konfigurálja az eszközt a helyi webes felhasználói felületén keresztül mutatjuk be hibák. A diagnosztikai tesztek diagnosztizálhatja és ilyen hibák elhárítása, futtassa.

#### <a name="to-run-the-diagnostic-tests"></a>A diagnosztikai tesztek futtatásához
1. A helyi webes felhasználói felület váltson **hibaelhárítás** > **diagnosztikai tesztek**.
   
    ![1 diagnosztika futtatása](./media/storsimple-ova-web-ui-admin/image29.png)
2. Kattintson a lap alján **diagnosztikai tesztek futtatása**. A szolgáltatás kezdeményez a hálózati, az eszköz, a webalkalmazás-proxy, minden lehetséges problémák elemzéséhez tesztek során, és a felhő beállításait. Értesítést fog kapni, hogy az eszköz teszteket futtat.
3. Miután a teszt befejeződött, az eredmények jelenik meg. A következő példa bemutatja a diagnosztikai tesztek eredményét. Vegye figyelembe, hogy a webproxy beállításai nem lettek konfigurálva az eszközön, és ezért a webalkalmazás-proxy teszt nem futott. Hálózati beállítások, a DNS-kiszolgáló és az időbeállítást minden más vizsgálatok sikeresek voltak.
   
    ![2 diagnosztika futtatása](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>A napló-csomag létrehozása
Napló csomag magában foglalja a vonatkozó naplókat, hogy bármely eszköz problémák elhárítása a Microsoft Support segít. Ebben a kiadásban a napló csomag hozhatók létre a helyi webes felhasználói felületen keresztül.

#### <a name="to-generate-the-log-package"></a>A napló-csomag létrehozása
1. A helyi webes felhasználói felület váltson **hibaelhárítás** > **rendszernaplókat**.
   
    ![napló csomagot 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Kattintson a lap alján **napló-csomag létrehozása**. A rendszer a naplók csomag jön létre. Ez eltarthat néhány percig.
   
    ![napló csomagot 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Értesítést fog kapni a csomag sikeresen létre, és az oldal frissül a csomag létrehozásának dátumát és időpontját jelzi.
   
    ![napló csomagot 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Kattintson a **napló letöltőcsomag**. A tömörített csomag a rendszeren, tölti le a program.
   
    ![napló csomagot 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Bontsa ki a letöltött napló csomagot, és megtekintheti a rendszernapló fájljaiban.

## <a name="shut-down-and-restart-your-device"></a>Állítsa le és indítsa újra az eszközt
Állítsa le, illetve újra is indíthatja a virtuális eszközt a helyi webes felhasználói felületen. Azt javasoljuk, hogy újraindítás előtt a kötetek vagy megosztások offline állapotba a gazdagép és az eszköz. Ezzel minimálisra csökkentheti adatsérülés lehetőségét. 

#### <a name="to-shut-down-your-virtual-device"></a>A virtuális eszköz leállítása
1. A helyi webes felhasználói felület váltson **karbantartási** > **energiaellátási beállítások**.
2. Kattintson a lap alján **leállítási**.
   
    ![1 eszköz Leállítás](./media/storsimple-ova-web-ui-admin/image36.png)
3. Megjelenik egy figyelmeztetés, amely meghatározza, hogy, hogy a leállás, az eszköz meg fogja szakítani a bármely IO, amelyek folyamatban volt egy állásidővel. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![eszköz leállítás figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Értesítést fog kapni, hogy a leállítási inicializálva lett.
   
    ![eszköz leállítás indítása](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Az eszköz most leáll. Ha el szeretné indítani az eszközt, akkor ehhez a Hyper-V kezelője segítségével.

#### <a name="to-restart-your-virtual-device"></a>A virtuális eszköz újraindítása
1. A helyi webes felhasználói felület váltson **karbantartási** > **energiaellátási beállítások**.
2. Kattintson a lap alján **indítsa újra a**.
   
    ![eszköz újraindítása](./media/storsimple-ova-web-ui-admin/image36.png)
3. Megjelenik egy figyelmeztetés, amely meghatározza, hogy, hogy az eszköz újraindítása meg fogja szakítani a bármely IOs-, amelyek folyamatban volt egy állásidővel. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Indítsa újra a figyelmeztetés](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Értesítést fog kapni az újraindítást kezdeményezett.
   
    ![újraindítást kezdeményezett](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Az újraindítás folyamatban van, amíg a kapcsolat a felhasználói felület megszűnik. Az újraindítás végezhet figyelést, ha a felhasználói felület rendszeresen frissíteni. Másik lehetőségként az eszköz újraindítása állapotát a Hyper-V kezelője segítségével figyelheti.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [a StorSimple Manager szolgáltatás használni az eszköz kezelését](storsimple-virtual-array-manager-service-administration.md).

