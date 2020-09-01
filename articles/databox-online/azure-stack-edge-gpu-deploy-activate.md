---
title: Oktatóanyag Azure Stack Edge-eszköz aktiválásához Azure Portal GPU-val | Microsoft Docs
description: Az Azure Stack Edge üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: be3e7c7f9a5a8c48d8644eefef42b7020e2b37f4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087890"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Oktatóanyag: Azure Stack Edge aktiválása GPU-val

Ez az oktatóanyag azt ismerteti, hogyan aktiválhatja Azure Stack Edge-eszközét egy beépített GPU-val a helyi webes felhasználói felület használatával.

Az aktiválási folyamat körülbelül 5 percet vesz igénybe.

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge-eszköz GPU-val való konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Fizikai eszköz esetén: 
    
    - A fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-gpu-deploy-install.md)című részletesen telepítette.
    - A hálózati és a számítási hálózati beállításokat a [hálózat konfigurálása, a számítási hálózat és a webproxy](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) beállítás részletesen konfigurálta.
    - Feltöltötte saját vagy az eszköz tanúsítványait az eszközön, ha módosította az eszköz nevét vagy a DNS-tartományt az **eszköz** lapon. Ha még nem tette meg ezt a lépést, hibaüzenet jelenik meg az eszköz aktiválása közben, és az aktiválás le lesz tiltva. További információkért keresse fel a [tanúsítványok konfigurálása](azure-stack-edge-gpu-deploy-configure-certificates.md)című témakört.
    
* Az Azure Stack Edge-eszköz kezeléséhez létrehozott Azure Stack Edge-szolgáltatásból aktiválhatja az aktiválási kulcsot. További információ: [felkészülés a Azure stack Edge üzembe helyezésére](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Az eszköz aktiválása

1. Az eszköz helyi webes FELÜLETén válassza az **első lépések** lapot.
2. Az **aktiválási** csempén válassza az **aktiválás**lehetőséget. 

    ![Helyi webes felhasználói felület "felhő részletei" oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. **Az aktiválás ablaktáblán** adja meg azt az **aktiválási kulcsot** , amelyet az [Azure stack Edge aktiválási kulcsának beszerzése](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key)című részében kapott.

4. Válassza az **Alkalmaz** lehetőséget.

    ![Helyi webes felhasználói felület "felhő részletei" oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Először az eszköz aktiválva van. Ekkor a rendszer kéri a kulcsfájl letöltését.
    
    ![Helyi webes felhasználói felület "felhő részletei" oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Válassza a **kulcsfájl letöltése** lehetőséget, és mentse a *keys.js* fájlt az eszközön kívüli biztonságos helyen. **Ez a kulcsfájl tartalmazza az operációs rendszer lemezének és adatlemezének helyreállítási kulcsait az eszközön**. Itt látható a fájl *keys.js* tartalma:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    A következő táblázat ismerteti a különböző kulcsokat:
    
    |Mező  |Leírás  |
    |---------|---------|
    |`Id`    | Ez az eszköz azonosítója.        |
    |`DataVolumeBitLockerExternalKeys`|Ezek az adatlemezek BitLocker-kulcsai, és a helyi adatok helyreállítására szolgálnak az eszközön.|
    |`SystemVolumeBitLockerRecoveryKey`| Ez a rendszerkötet BitLocker-kulcsa. Ez a kulcs segít az eszköz rendszerkonfigurációjának és rendszerállapotának helyreállításában. |
    |`ServiceEncryptionKey`| Ez a kulcs védi az Azure-szolgáltatáson keresztül áramló adatforgalmat. Ez a kulcs biztosítja, hogy az Azure-szolgáltatás sérülése ne veszélyeztesse a tárolt adatokat. |

6. Ugrás az **Áttekintés** oldalra. Az eszköz állapotának **aktiválva**kell lennie.

    ![Helyi webes felhasználói felület "felhő részletei" oldal](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
Az eszköz aktiválása befejeződött. Mostantól megosztásokat is hozzáadhat az eszközhöz.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * A fizikai eszköz aktiválása

Ha szeretné megismerni, hogyan vihetők át adatok az Azure Stack Edge-eszközzel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Adatok átvitele Azure Stack Edge-vel](./azure-stack-edge-j-series-deploy-add-shares.md)
