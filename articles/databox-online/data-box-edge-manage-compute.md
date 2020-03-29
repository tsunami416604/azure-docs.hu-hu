---
title: Azure Data Box Edge számítási menedzsment | Microsoft dokumentumok
description: Bemutatja, hogyan kezelheti az Edge számítási beállításait, például az eseményindítót, a modulokat, a számítási konfiguráció megtekintését, a konfiguráció eltávolítását az Azure Data Box Edge-en az Azure Data Box Edge-en keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: a9daf1d59b03d283be999aaab559c6d60f6405dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65953122"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Számítási feladatok kezelése az Azure Data Box Edge-en

Ez a cikk ismerteti, hogyan kezelheti a számítási azure Data Box Edge. A számítási az Azure Portalon keresztül vagy a helyi webes felhasználói felületen keresztül kezelheti a számítást. Az Azure Portal segítségével kezelheti a modulokat, az eseményindítókat és a számítási konfigurációt, valamint a helyi webes felhasználói felületet a számítási beállítások kezeléséhez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eseményindítók kezelése
> * Számítási konfiguráció kezelése


## <a name="manage-triggers"></a>Eseményindítók kezelése

Az események olyan dolgok, amelyek a felhőkörnyezetben vagy az eszközön történnek, és amelyeken esetleg műveleteket szeretne végrehajtani. Ha például egy fájlt egy megosztásban hoznak létre, az egy esemény. Az eseményindítók növelik az eseményeket. A Data Box Edge esetében az eseményindítók fájleseményekre vagy ütemezésre reagálhatnak.

- **Fájl**: Ezek az eseményindítók olyan fájleseményekre reagálnak, mint egy fájl létrehozása, egy fájl módosítása.
- **Ütemezett**: Ezek az eseményindítók egy olyan ütemezésre reagálnak, amelyet a kezdési dátummal, a kezdési idővel és az ismétlési időközzel határozhat meg.


### <a name="add-a-trigger"></a>Eseményindító hozzáadása

Az alábbi lépéseket az Azure Portalon hozzon létre egy eseményindító.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **az Edge számítási > eseményindító.** Válassza a **+ Trigger hozzáadása** lehetőséget a parancssávon.

    ![Eseményindító hozzáadása kiválasztása](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Az **Eseményindító** hozzáadása panelben adjon meg egy egyedi nevet az eseményindítónak.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Válasszon **egy típust** az eseményindítóhoz. Válassza a **Fájl** lehetőséget, ha az eseményindító fájleseményre válaszul van. Válassza **az Ütemezett** lehetőséget, ha azt szeretné, hogy az eseményindító meghatározott időpontban kezdődjön, és meghatározott ismétlési időközzel fusson. A választástól függően a beállítások egy másik készlete jelenik meg.

    - **Fájleseményindító** – Válasszon a legördülő listából egy csatlakoztatott megosztást. Ha egy fájlesemény aktiválódik ebben a megosztásban, az eseményindító egy Azure-függvény t.

        ![SMB-megosztás hozzáadása](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Ütemezett eseményindító** – adja meg a kezdési dátumot/időt, valamint az ismétlési intervallumot órákban, percekben vagy másodpercekben. Írja be a témakör nevét is. A témakör rugalmasságot biztosít az eseményindító eszköz az eszközön telepített modulhoz való átirányításához.

        Példa útvonalkarakterláncra: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![NFS-megosztás hozzáadása](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Az eseményindító létrehozásához válassza a **Hozzáadás** lehetőséget. Az értesítés azt mutatja, hogy az eseményindító létrehozása folyamatban van. Az eseményindító létrehozása után a panel frissül, hogy tükrözze az új eseményindító.
 
    ![Frissített eseményindító-lista](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Eseményindító törlése

Az alábbi lépéseket az Azure Portalon egy eseményindító törléséhez.

1. Az eseményindítók listájából jelölje ki a törölni kívánt eseményindítót.

    ![Eseményindító kiválasztása](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Kattintson a jobb gombbal, majd válassza **a Törlés parancsot.**

    ![Törlés kijelölése](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/data-box-edge-manage-compute/add-trigger-1.png)

Az eseményindítók listája a törlésnek megfelelően frissül.

## <a name="manage-compute-configuration"></a>Számítási konfiguráció kezelése

Az Azure Portalon megtekintheti a számítási konfigurációt, eltávolíthatja a meglévő számítási konfigurációt, vagy frissítheti a számítási konfigurációt az IoT-eszköz és az IoT Edge-eszköz hozzáférési kulcsainak szinkronizálásához a Data Box Edge számára.

### <a name="view-compute-configuration"></a>Számítási konfiguráció megtekintése

Az alábbi lépéseket az Azure Portalon az eszköz számítási konfigurációjának megtekintéséhez.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **az Edge számítási > modulok.** Válassza a **Számítási nézet lehetőséget** a parancssávon.

    ![Számítási nézet kiválasztása](media/data-box-edge-manage-compute/view-compute-1.png)

2. Jegyezze fel az eszközön a számítási konfigurációt. A számítási konfigurálásakor létrehozott egy IoT Hub-erőforrást. Az IoT Hub-erőforrás alatt egy IoT-eszköz és egy IoT Edge-eszköz van konfigurálva. Csak a Linux-modulok támogatottak az IoT Edge-eszközön való futtatáshoz.

    ![Konfiguráció megtekintése](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Számítási konfiguráció eltávolítása

Az alábbi lépéseket az Azure Portalon távolítsa el az eszköz meglévő Edge számítási konfigurációját.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **az Edge számítási > az Első lépések**. Válassza a **Számítási parancs eltávolítása** lehetőséget a parancssávon.

    ![Válassza a Számítás eltávolítása lehetőséget.](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Ha eltávolítja a számítási konfigurációt, újra kell konfigurálnia az eszközt arra az esetre, ha újra használnia kell a számítást. Amikor megerősítést kér, válassza az **Igen**lehetőséget.

    ![Válassza a Számítás eltávolítása lehetőséget.](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Szinkronizálja az IoT-eszközt és az IoT Edge-eszköz hozzáférési kulcsait

Amikor konfigurálja a számítást a Data Box Edge,ioT-eszköz és egy IoT Edge-eszköz jön létre. Ezek az eszközök automatikusan szimmetrikus hozzáférési kulcsokat kapnak. Biztonsági ajánlott eljárásként ezeket a kulcsokat rendszeresen elforgatják az IoT Hub szolgáltatáson keresztül.

Ezek a kulcsok elforgatásához nyissa meg az IoT Hub-szolgáltatást, amely létrehozta, és válassza ki az IoT-eszközt vagy az IoT Edge-eszközt. Minden eszköz rendelkezik egy elsődleges hozzáférési kulccsal és egy másodlagos hozzáférési kulccsal. Rendelje hozzá az elsődleges hozzáférési kulcsot a másodlagos hozzáférési kulcshoz, majd hozza létre újra az elsődleges hozzáférési kulcsot.

Ha az IoT-eszköz és az IoT Edge-eszközkulcsok el lett forgatva, majd frissítenie kell a konfigurációt a Data Box Edge a legújabb hozzáférési kulcsok beszerezéséhez. A szinkronizálás segít az eszköznek az IoT-eszköz és az IoT Edge-eszköz legújabb kulcsainak bekapásában. A Data Box Edge csak az elsődleges hozzáférési kulcsokat használja.

Az alábbi lépéseket az Azure Portalon szinkronizálja az eszköz hozzáférési kulcsait.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **az Edge számítási > az Első lépések**. Válassza a **Konfiguráció frissítése** lehetőséget a parancssávon.

    ![Konfiguráció frissítése kiválasztása](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Válassza **az Igen** lehetőséget, amikor megerősítést kér.

     ![Amikor a rendszer kéri, válassza az Igen lehetőséget.](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti az [Edge számítási hálózatát az Azure Portalon keresztül.](data-box-edge-extend-compute-access-modules.md)
