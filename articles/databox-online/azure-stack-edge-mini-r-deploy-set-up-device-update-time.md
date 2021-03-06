---
title: Oktatóanyag a Azure Stack Edge mini R-eszköz eszközének, frissítésének és időpontjának beállításához Azure Portal
description: Az Azure Stack Edge mini R üzembe helyezésére vonatkozó oktatóanyag arra utasítja a számítógépet, hogy beállítsa az eszköz, a frissítés és az idő beállításait a fizikai eszközhöz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: ee3805d128a7b6d122f93e692291db1a387cfcf5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468337"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-mini-r"></a>Oktatóanyag: Azure Stack Edge mini R eszköz beállításainak konfigurálása

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja az eszközhöz kapcsolódó beállításokat a Azure Stack Edge mini R-eszközhöz egy beépített GPU-val. Megadhatja az eszköz nevét, a frissítési kiszolgálót és az időkiszolgálót a helyi webes felületen keresztül.

Az eszközbeállítások körülbelül 5-7 percet is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

## <a name="prerequisites"></a>Előfeltételek

Mielőtt az eszközhöz kapcsolódó beállításokat az Azure Stack Edge mini R-eszközön a GPU-val konfigurálja, ügyeljen rá, hogy:

* Fizikai eszköz esetén:

    - Telepítette a fizikai eszközt a [Azure stack Edge mini R telepítésének ](azure-stack-edge-mini-r-deploy-install.md)részletesen.
    - A hálózati és az engedélyezett és konfigurált számítási hálózatot konfigurálta az eszközön, az [oktatóanyag: a hálózat konfigurálása Azure stack Edge mini R-hez a GPU-val című útmutatóban](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)részletesen.


## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszközhöz kapcsolódó beállítások konfigurálásához kövesse az alábbi lépéseket:

1. Az **eszköz** oldalon hajtsa végre a következő lépéseket:

    1. Adja meg az eszköz rövid nevét. A felhasználóbarát névnek 1 – 13 karakterből kell állnia, és tartalmazhat betűt, számot és kötőjelet.

    2. Adja meg az eszköz **DNS-tartományát** . Ez a tartomány az eszköz fájlkiszolgálóként való beállítására szolgál.

    3. A konfigurált eszközbeállítások ellenőrzéséhez és alkalmazásához válassza az **alkalmaz** lehetőséget.

        ![Helyi webes felhasználói felület "eszköz" 1. oldal](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-1.png)

        Ha módosította az eszköz nevét és a DNS-tartományt, az eszközön létezett önaláírt tanúsítványok nem fognak működni. 

        ![Helyi webes felhasználói felület "eszköz" 2. lapja](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-2.png)

        A tanúsítványok konfigurálásakor a következő lehetőségek közül kell választania: 
        
        - Az eszköz tanúsítványainak előállítása és letöltése. 
        - Hozza saját tanúsítványait az eszközhöz, beleértve az aláírási láncot is.
    

    4. Amikor az eszköz neve és a DNS-tartomány megváltozik, létrejön az SMB-végpont.  

        ![Helyi webes felhasználói felület "eszköz" 3. oldala](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/set-up-device-3.png)

    5. A beállítások alkalmazása után válassza a **következő: kiszolgáló frissítése** lehetőséget.


## <a name="configure-update"></a>Frissítés konfigurálása

1. A **frissítés** lapon megadhatja, hogy honnan szeretné letölteni az eszköz frissítéseinek helyét.  

    - A frissítéseket közvetlenül a **Microsoft Update-kiszolgálóról** kérheti le.

        ![Helyi webes felhasználói felület "frissítési kiszolgáló" lapja](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-1.png)

        A frissítéseket a **Windows Server Update Services** (WSUS) szolgáltatásból is telepítheti. Adja meg a WSUS-kiszolgáló elérési útját.
        
        ![Helyi webes felhasználói felület "frissítési kiszolgáló" 2. lapja](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/update-server-2.png)

        > [!NOTE] 
        > Ha külön Windows Update kiszolgáló van konfigurálva, és ha a *https* protokollon keresztüli kapcsolódást választja ( *http* helyett), akkor szükség van a frissítési kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítványok aláírására. A tanúsítványok létrehozásáról és feltöltéséről a [tanúsítványok kezelése](azure-stack-edge-j-series-manage-certificates.md)című témakörben olvashat bővebben. Ha leválasztott módban szeretne dolgozni, például az Azure Stack Edge-eszközök többplatformos adatközpontba való csatlakoztatására, engedélyezze a WSUS szolgáltatást. Az aktiválás során az eszköz frissítéseket keres, és ha a kiszolgáló nincs beállítva, az aktiválás sikertelen lesz. 

2. Kattintson az **Alkalmaz** gombra.
3. A frissítési kiszolgáló konfigurálása után válassza a **következő: idő** lehetőséget.
    

## <a name="configure-time"></a>Konfigurálás időpontja

Az alábbi lépéseket követve konfigurálhatja az eszközön az időbeállításokat. 

> [!IMPORTANT]
> Bár az időbeállítások nem kötelezőek, javasoljuk, hogy egy elsődleges NTP-t és egy másodlagos NTP-kiszolgálót állítson be az eszköz helyi hálózatán. Ha a helyi kiszolgáló nem érhető el, a nyilvános NTP-kiszolgálókat is konfigurálhatja.

Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.

1. Az **idő** lapon kiválaszthatja az eszköz időzónáját, valamint az elsődleges és másodlagos NTP-kiszolgálókat.  
    
    1. Az **időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz üzembe helyezésének földrajzi helyének.
        Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az **Elsődleges NTP-kiszolgáló** mezőbe írja be az eszköz elsődleges kiszolgálóját, vagy fogadja el a Time.Windows.com alapértelmezett értékét.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom áthaladjon az adatközpontból az internetre.

    3. Szükség esetén a **másodlagos NTP-kiszolgáló** mezőbe írja be az eszköz másodlagos kiszolgálóját.

    4. A beállított idő beállításainak ellenőrzéséhez és alkalmazásához válassza az **alkalmaz** lehetőséget.

        ![Helyi webes felhasználói felület "Time" lapja](./media/azure-stack-edge-mini-r-deploy-set-up-device-update-time/time-settings-1.png)

2. A beállítások alkalmazása után válassza a **Tovább: tanúsítványok** lehetőséget.


## <a name="next-steps"></a>További lépések

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Eszközbeállítások megadása
> * Frissítés konfigurálása 
> * Konfigurálás időpontja

Az Azure Stack Edge mini R-eszköz tanúsítványának konfigurálásáról a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Tanúsítványok konfigurálása](./azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)
