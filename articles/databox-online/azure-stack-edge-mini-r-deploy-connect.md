---
title: Oktatóanyag Azure Stack Edge mini R-hez való kapcsolódáshoz Azure Portal
description: Megtudhatja, hogyan csatlakozhat a Azure Stack Edge mini R-eszközhöz a helyi webes felhasználói felület használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468528"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Oktatóanyag: Kapcsolódás Azure Stack Edge mini R-hez

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a Azure Stack Edge mini R-eszközhöz a helyi webes KEZELŐFELÜLET használatával.

A kapcsolódási folyamat körülbelül 5 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz



## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-mini-r-deploy-install.md)című részletesen telepítette.


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Konfigurálja az Ethernet-adaptert a számítógépén az Azure Stack Edge Pro-eszközhöz a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címével való csatlakozáshoz.

2. A számítógép csatlakoztatása az 1. PORThoz az eszközön. Ha a számítógépet közvetlenül csatlakoztatja az eszközhöz (kapcsoló nélkül), használjon egy fordított kábelt vagy egy USB Ethernet-adaptert. Az alábbi ábra segítségével azonosíthatja az eszközön az 1. PORTOT.

    ![Wi-Fi kábelezése](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Kapcsolódás fizikai eszközhöz


Az eszköz hálózati beállításainak konfigurálásáról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Hálózat konfigurálása](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
