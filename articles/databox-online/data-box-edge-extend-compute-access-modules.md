---
title: Számítási hálózat az Azure Data Box Edge-ben való hozzáférés modulok kezelése |} A Microsoft Docs
description: Ismerteti, hogyan terjeszthető ki a számítási hálózatát, a Data Box Edge eléréséhez a modulok egy külső IP-címen keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917235"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Az Azure Data Box Edge számítási hálózati engedélyezése

Ez a cikk ismerteti, hogy a modulok az Azure Data Box Edge futó férhet hozzá a számítási hálózati engedélyezve van az eszközön.

A hálózat konfigurálására, megteheti az alábbi lépéseket:

- Egy hálózati adaptert a Data Box Edge-eszközön számítási engedélyezése
- A Data Box Edge hozzáférés számítási hálózati egy modul hozzáadása
- Ellenőrizze, hogy a modul hozzáférhet az engedélyezett hálózati adapter

Ebben az oktatóanyagban egy webkiszolgáló alkalmazást modul fogja használni a forgatókönyv bemutatásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, szüksége lesz:

- Egy Data Box peremhálózati eszköz, az eszközön a telepítés befejeződött.
- Ön teljesítette **számítás konfigurálása** megfelelően. lépés: a [oktatóanyag: Adatátalakítás az Azure Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) az eszközön. Az eszköz egy IoT Hub társított erőforrást, egy IoT-eszköz és IoT Edge-eszköz rendelkezhet.

## <a name="enable-network-interface-for-compute"></a>Hálózati adapter számítási engedélyezése

A külső hálózaton keresztül az eszközön futó modulok el kell IP-címet rendel egy hálózati adapter az eszközön. Ezek kezelheti számítási beállításokat a helyi webes felületén.

Az alábbi lépéseket a helyi webes felületén számítási beállítások konfigurálása.

1. A helyi webes felhasználói felületen váltson **Configuration > Számítási beállítások**.  

2. **Engedélyezése** csatlakozhat egy számítási modul, amely az eszköz meg fogja futtatni kívánt hálózati adapter.

    - Ha statikus IP-címeket használ, adja meg a hálózati adapter IP-címet.
    - Ha használja a DHCP, az IP-címek automatikusan kapnak. Ebben a példában a DHCP.

    ![1 compute-beállítások engedélyezése](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Válassza ki **alkalmaz** a alkalmazni a beállításokat. Jegyezze fel a DHCP használata a hálózati adapterhez rendelt IP-cím.

    ![Compute-beállítások engedélyezése](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Webkiszolgáló alkalmazás modul hozzáadása

Az alábbi lépéseket egy webkiszolgáló alkalmazást modul hozzáadása a Data Box Edge-eszközön.

1. Nyissa meg az IoT Hub-erőforrás a Data Box Edge-eszköz társított, majd **IoT Edge-eszköz**.
2. Válassza ki a Data Box Edge-eszköz társított IoT Edge-eszközökön. Az a **eszközadatok**válassza **modulok beállítása**. A **modulok hozzáadása**válassza **+ Hozzáadás** majd **IoT Edge-modul**.
3. Az a **egyéni IoT Edge-modulok** panelen:

    1. Adjon meg egy **neve** a webkiszolgáló alkalmazás modul, amely számára telepíteni kívánja.
    2. Adjon meg egy **rendszerkép URI** a modul lemezkép számára. Lekéri a megadott névvel és címkék egyeztetése modul. Ebben az esetben `nginx:stable` fogja lekérni egy stabil nginx rendszerképet (stable címkével) nyilvános [Docker-tárházat](https://hub.docker.com/_/nginx/).
    3. Az a **tároló létrehozása beállítások**, illessze be az alábbi mintakód:  

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

        Ez a konfiguráció lehetővé teszi, hogy a modul a számítási hálózati IP-cím használatával hozzáférést *http* TCP a 8080-as portra (az alapértelmezett webkiszolgáló port folyamatban van a 80-as).

        ![IoT Edge-modul egyéni panelen adja meg a port adatai](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Kattintson a **Mentés** gombra.

## <a name="verify-module-access"></a>A modul hozzáférés ellenőrzése

1. Ellenőrizze a modul sikeres üzembe helyezése és fut-e. Az a **eszközadatok** lap a **modulok** lapon, a modul futási állapotát kell lennie **futó**.  
2. A server app modul csatlakozni. Nyisson meg egy böngészőablakot, és írja be:

    `http://<compute-network-IP-address>:8080`

    Megtekintheti, hogy fut-e a webkiszolgáló alkalmazást.

    ![Ellenőrizze a kapcsolatot modulhoz megadott porton keresztül](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](data-box-edge-manage-users.md).
