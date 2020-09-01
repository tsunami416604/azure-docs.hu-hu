---
title: Oktatóanyag a Azure Portal GPU-val való kapcsolódáshoz, konfiguráláshoz, aktiváláshoz Azure Stack Edge-eszközhöz | Microsoft Docs
description: Az Azure Stack Edge üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c0581a79bda4ae4e311390f5b2d929073e0833d9
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182020"
---
# <a name="tutorial-configure-device-settings-for-azure-stack-edge-with-gpu"></a>Oktatóanyag: Azure Stack Edge eszköz beállításainak konfigurálása GPU-val

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja az eszközhöz kapcsolódó beállításokat a Azure Stack Edge-eszközhöz egy beépített GPU-val. Megadhatja az eszköz nevét, a frissítési kiszolgálót és az időkiszolgálót a helyi webes felületen keresztül.

Az eszközbeállítások körülbelül 5-7 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

## <a name="prerequisites"></a>Előfeltételek

Mielőtt a GPU-val konfigurálja az eszközhöz kapcsolódó beállításokat az Azure Stack Edge-eszközön, győződjön meg a következőket:

* Fizikai eszköz esetén:

    - A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-gpu-deploy-install.md)című részletesen telepítette.
    - A hálózati és az engedélyezett és konfigurált számítási hálózatot konfigurálta az eszközön, az [oktatóanyag: a hálózat konfigurálása Azure stack Edge-hez GPU-val című útmutatóban](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)részletesen.


## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszközökhöz kapcsolódó beállítások konfigurálásához kövesse az alábbi lépéseket.
 
1. Az eszközbeállítások csempén az **eszköz** **beállításnál** válassza a **Konfigurálás**lehetőséget.

    ![Helyi webes felhasználói felület "eszköz" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-1.png)

    Az **eszköz** oldalon hajtsa végre a következő lépéseket:

    1. Adja meg az eszköz rövid nevét. A felhasználóbarát névnek 1 – 13 karakterből kell állnia, és tartalmazhat betűt, számot és kötőjelet.

    2. Adja meg az eszköz **DNS-tartományát** . Ez a tartomány az eszköz fájlkiszolgálóként való beállítására szolgál.

    3. A konfigurált eszközbeállítások ellenőrzéséhez és alkalmazásához válassza az **alkalmaz**lehetőséget.

        ![Helyi webes felhasználói felület "eszköz" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

        Ha módosította az eszköz nevét és a DNS-tartományt, az eszközön automatikusan létrehozott önaláírt tanúsítványok nem fognak működni. A tanúsítványok konfigurálásakor a következő lehetőségek közül kell választania: 
        
        - Az eszköz tanúsítványainak előállítása és letöltése. 
        - Hozza saját tanúsítványait az eszközhöz, beleértve az aláírási láncot is.
    

        ![Helyi webes felhasználói felület "eszköz" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-3.png)

    4. Amikor az eszköz neve és a DNS-tartomány módosul, létrejön az SMB-és NFS-végpontok.  

    5. A beállítások alkalmazása után lépjen vissza **a kezdéshez.**

## <a name="configure-update"></a>Frissítés konfigurálása

1. Az **eszközbeállítások** csempén a **frissítés**lapon válassza a **Konfigurálás**lehetőséget. Mostantól konfigurálhatja a helyet, ahonnan letöltheti az eszköz frissítéseit.  

    ![Helyi webes felhasználói felület "frissítési kiszolgáló" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-1.png)

    - A frissítéseket közvetlenül a **Microsoft Update-kiszolgálóról**kérheti le.

        ![Helyi webes felhasználói felület "frissítési kiszolgáló" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-2.png)

        A frissítéseket a **Windows Server Update Services** (WSUS) szolgáltatásból is telepítheti. Adja meg a WSUS-kiszolgáló elérési útját.
        
        ![Helyi webes felhasználói felület "frissítési kiszolgáló" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Ha külön Windows Update kiszolgáló van konfigurálva, és ha a *https* protokollon keresztüli kapcsolódást választja ( *http*helyett), akkor szükség van a frissítési kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítványok aláírására. A tanúsítványok létrehozásáról és feltöltéséről a [tanúsítványok kezelése](azure-stack-edge-j-series-manage-certificates.md)című témakörben olvashat bővebben. 

2. Válassza az **Alkalmaz** lehetőséget.
3. A frissítési kiszolgáló konfigurálása után lépjen vissza **a kezdéshez.**
    

## <a name="configure-time"></a>Konfigurálás időpontja

Az alábbi lépéseket követve konfigurálhatja az eszközön az időbeállításokat. 

1. Az **eszköz beállítása** csempén válassza az **idő**lehetőséget. Kiválaszthatja az eszköz időzónáját, valamint az elsődleges és másodlagos NTP-kiszolgálókat.  

    > [!IMPORTANT]
    > Bár az időbeállítások nem kötelezőek, javasoljuk, hogy egy elsődleges NTP-t és egy másodlagos NTP-kiszolgálót állítson be az eszköz helyi hálózatán. Ha a helyi kiszolgáló nem érhető el, a nyilvános NTP-kiszolgálókat is konfigurálhatja.
    
    Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.

    ![Helyi webes felhasználói felület "Time" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-1.png)

2. Az **idő** lapon tegye a következőket:
    
    1. Az **időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz üzembe helyezésének földrajzi helyének.
        Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az **Elsődleges NTP-kiszolgáló** mezőbe írja be az eszköz elsődleges kiszolgálóját, vagy fogadja el a Time.Windows.com alapértelmezett értékét.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom áthaladjon az adatközpontból az internetre.

    3. Szükség esetén a **másodlagos NTP-kiszolgáló** mezőbe írja be az eszköz másodlagos kiszolgálóját.

    4. A beállított idő beállításainak ellenőrzéséhez és alkalmazásához válassza az **alkalmaz**lehetőséget.

        ![Helyi webes felhasználói felület "Time" lapja](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/time-2.png)

3. A beállítások alkalmazása után lépjen vissza **a kezdéshez.**



## <a name="next-steps"></a>További lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

Az Azure Stack Edge-eszköz tanúsítványának konfigurálásáról a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Tanúsítványok konfigurálása](./azure-stack-edge-gpu-deploy-configure-certificates.md)
