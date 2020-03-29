---
title: Számítási hálózat kezelése az Azure Data Box Edge-en a modulok eléréséhez| Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként bővítheti ki a számítási hálózatot a Data Box Edge-en a modulok külső IP-címen keresztüli elérésére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65917235"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Számítási hálózat engedélyezése az Azure Data Box Edge-en

Ez a cikk ismerteti, hogy az Azure Data Box Edge-en futó modulok hogyan férhetnek hozzá az eszközön engedélyezett számítási hálózathoz.

A hálózat konfigurálásához tegye a következő lépéseket:

- Hálózati felület engedélyezése a Data Box Edge eszközön a számításhoz
- Modul hozzáadása a Data Box Edge számítási hálózatának eléréséhez
- Annak ellenőrzése, hogy a modul hozzáférhet-e az engedélyezett hálózati adapterhez

Ebben az oktatóanyagban egy webkiszolgáló-alkalmazásmodult fog használni a forgatókönyv bemutatásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz:

- A Data Box Edge eszköz eszköz beállítása befejeződött.
- Elvégezte a számítási lépés **konfigurálása** az [oktatóanyag: Adatok átalakítása](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) az Azure Data Box Edge az eszközön. Az eszköznek rendelkeznie kell egy társított IoT Hub-erőforrással, egy IoT-eszközzel és egy IoT Edge-eszközzel.

## <a name="enable-network-interface-for-compute"></a>Hálózati adapter engedélyezése a számításhoz

Ha külső hálózaton keresztül szeretné elérni az eszközön futó modulokat, hozzá kell rendelnie egy IP-címet az eszköz hálózati adapteréhez. Ezeket a számítási beállításokat a helyi webes felhasználói felületen kezelheti.

A számítási beállítások konfigurálásához tegye a következő lépéseket a helyi webes felhasználói felületen.

1. A helyi webes felhasználói felületen nyissa meg **a Konfigurációs > számítási beállítások című részt.**  

2. **Engedélyezze** a használni kívánt hálózati adaptert az eszközön futtatandó számítási modulhoz való csatlakozáshoz.

    - Statikus IP-címek használata esetén adja meg a hálózati adapter IP-címét.
    - DHCP használata esetén a rendszer automatikusan hozzárendeli az IP-címeket. Ez a példa DHCP-t használ.

    ![Számítási beállítások engedélyezése 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. A beállítások alkalmazásához válassza az **Alkalmaz** lehetőséget. A DHCP használata esetén jegyezze fel a hálózati adapterhez rendelt IP-címet.

    ![Számítási beállítások engedélyezése](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Webkiszolgáló-alkalmazásmodul hozzáadása

A következő lépésekkel vegyen fel egy webkiszolgáló-alkalmazásmodult a Data Box Edge eszközre.

1. Nyissa meg a Data Box Edge-eszközhöz társított IoT Hub-erőforrást, és válassza az **IoT Edge-eszközt.**
2. Válassza ki a Data Box Edge-eszközhöz társított IoT Edge-eszközt. Az **Eszköz részletei**ben válassza a **Modulok beállítása**lehetőséget. A **Modulok hozzáadása területen**válassza a + **Add** and or **IoT Edge Module (IoT edge module**) lehetőséget.
3. Az **IoT Edge egyéni modulok** panelen:

    1. Adja meg a webkiszolgálóalkalmazás-modulnak a telepíteni kívánt **nevét.**
    2. Adjon meg egy **lemezképURI-t** a modulképhez. A megadott névnek és címkéknek megfelelő modult a rendszer lekéri. Ebben az `nginx:stable` esetben egy stabil nginx-lemezképet (stabilként címkézett) fog lehúzni a nyilvános [Docker-tárházból.](https://hub.docker.com/_/nginx/)
    3. A **Tároló létrehozási beállításai párbeszédpanelen**illessze be a következő mintakódot:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Ez a konfiguráció lehetővé teszi a modul elérését a számítási hálózati IP *http-n* keresztül a 8080-as TCP-porton (az alapértelmezett webkiszolgáló-port 80).

        ![Portadatok megadása az IoT Edge egyéni modulpaneljén](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Kattintson a **Mentés** gombra.

## <a name="verify-module-access"></a>Modulhozzáférés ellenőrzése

1. Ellenőrizze, hogy a modul telepítése sikeresen megtörtént-e, és fut-e. Az **Eszköz részletei** lap **Modulok** lapján a modul futásidejű állapotának **futnia**kell.  
2. Csatlakozás a webkiszolgáló alkalmazásmoduljához. Nyisson meg egy böngészőablakot, és írja be a következőt:

    `http://<compute-network-IP-address>:8080`

    Látnia kell, hogy a webkiszolgáló alkalmazás fut.

    ![A modullal való kapcsolat ellenőrzése a megadott porton keresztül](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](data-box-edge-manage-users.md).
