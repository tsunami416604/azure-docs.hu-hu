---
title: Oktatóanyag a Azure Stack Edge Pro R-eszköz csatlakoztatásához, konfigurálásához, aktiválásához Azure Portalban | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat a Azure Stack Edge Pro R-eszközhöz a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ce97c22cf4bfbe5cca01183574597706a8c239e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468288"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Oktatóanyag: Kapcsolódás Azure Stack Edge Pro R-hez

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Azure Stack Edge Pro R-eszközhöz a helyi webes KEZELŐFELÜLET használatával.

A kapcsolódási folyamat körülbelül 5 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro R telepítésének](azure-stack-edge-pro-r-deploy-install.md)részletesen telepítette.


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Konfigurálja az Ethernet-adaptert a számítógépén az Azure Stack Edge Pro R-eszközhöz való csatlakozáshoz a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címével.

2. A számítógép csatlakoztatása az 1. PORThoz az eszközön. Ha a számítógépet közvetlenül csatlakoztatja az eszközhöz (kapcsoló nélkül), használjon egy fordított kábelt vagy egy USB Ethernet-adaptert. Az alábbi ábra segítségével azonosíthatja az eszközön az 1. PORTOT.

    ![Egy bekábelezett eszköz hátlapja](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Nyissa meg egy böngészőablakot, és lépjen az eszköz helyi webes felhasználói felületére a `https://192.168.100.10` címen.  
    Ez a művelet eltarthat pár percig, miután bekapcsolta az eszközt.

    Megjelenik egy hiba vagy egy figyelmeztetés, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával. 
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. Válassza **a folytatás ehhez a weboldalhoz** lehetőséget.  
    Ezek a lépések eltérők lehetnek, attól függően, hogy milyen böngészőt használ.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Azure Stack Edge-eszköz bejelentkezési lapja](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. A parancssorban módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszónak 8 és 16 karakter közöttinek kell lennie. A következő karakterek közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és speciális karakterek.

Most már **megtekintheti** az eszköz áttekintés lapját. A következő lépés az eszköz hálózati beállításainak konfigurálása.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


A Azure Stack Edge Pro R-eszköz hálózati beállításainak konfigurálásáról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Hálózat konfigurálása](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
