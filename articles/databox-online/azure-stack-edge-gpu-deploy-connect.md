---
title: Oktatóanyag a Azure Portal GPU-val való kapcsolódáshoz, konfiguráláshoz, aktiváláshoz Azure Stack Edge-eszközhöz | Microsoft Docs
description: Az Azure Stack Edge GPU üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b500a7d93d19bcc0cd9c23d64ad079f0abfd353
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421356"
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

2. A számítógép csatlakoztatása az 1. PORThoz az eszközön. Ha a számítógépet közvetlenül csatlakoztatja az eszközhöz (kapcsoló nélkül), használjon egy fordított kábelt vagy egy USB Ethernet-adaptert. Az alábbi ábra segítségével azonosíthatja az eszközön az 1. PORTOT.

    ![Egy bekábelezett eszköz hátlapja](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Az eszköz hátlapja némileg eltérő lehet a kapott modelltől függően. További információ: [az eszköz csatlakoztatása](azure-stack-edge-gpu-deploy-install.md#cable-the-device).


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
