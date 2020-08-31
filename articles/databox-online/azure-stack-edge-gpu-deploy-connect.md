---
title: Oktatóanyag a Azure Portal GPU-val való kapcsolódáshoz, konfiguráláshoz, aktiváláshoz Azure Stack Edge-eszközhöz | Microsoft Docs
description: Az Azure Stack Edge üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c98d6644dba0b3ca51f07f01ec27fee697369f2b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087851"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Oktatóanyag: Kapcsolódás Azure Stack Edge-hez GPU használatával

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Azure Stack Edge-eszközhöz egy beépített GPU-val a helyi webes felhasználói felület használatával.

A kapcsolódási folyamat körülbelül 5 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge-eszköz GPU-val való konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-gpu-deploy-install.md)című részletesen telepítette.


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Konfigurálja az Ethernet-adaptert a számítógépén az Azure Stack Edge-eszközhöz való csatlakozáshoz a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címével.

2. A számítógép csatlakoztatása az 1. PORThoz az eszközön. Az alábbi ábra segítségével azonosíthatja az eszközön az 1. PORTOT.

    ![Egy bekábelezett eszköz hátlapja](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)


3. Nyisson meg egy böngészőablakot, és nyissa meg az eszköz helyi webes FELÜLETét a következő címen: `https://192.168.100.10` .  
    Ez a művelet néhány percet is igénybe vehet, miután bekapcsolta az eszközt. 

    Hibaüzenet vagy figyelmeztetés jelenik meg, amely jelzi, hogy probléma van a webhely biztonsági tanúsítványával. 
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Válassza **a folytatás ehhez a weboldalhoz**lehetőséget.  
    Ezek a lépések a használt böngészőtől függően eltérőek lehetnek.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Azure Stack Edge-eszköz bejelentkezési lapja](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. A parancssorban módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszónak 8 és 16 karakter közöttinek kell lennie. A következő karakterek közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és speciális karakterek.

Most már **megtekintheti** az eszköz áttekintés lapját. A következő lépés az eszköz hálózati beállításainak konfigurálása.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


Az Azure Stack Edge-eszköz hálózati beállításainak konfigurálásáról a következő témakörben tájékozódhat:

> [!div class="nextstepaction"]
> [Hálózat konfigurálása](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
