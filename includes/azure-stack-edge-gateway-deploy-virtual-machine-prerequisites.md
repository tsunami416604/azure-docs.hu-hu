---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763428"
---
Mielőtt üzembe helyezi a virtuális gépeket az Azure Stack Edge-eszközön, konfigurálnia kell az ügyfelet, hogy Azure Resource Manageron keresztül kapcsolódjon az eszközhöz Azure PowerShellon keresztül. A részletes lépésekért lépjen a [kapcsolódás Azure Resource Managerhoz az Azure stack Edge-eszközön](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Győződjön meg arról, hogy a következő lépések használhatók az eszköz ügyfélhez való eléréséhez (ezt a konfigurációt Azure Resource Managerhoz való csatlakozáskor tette meg, és csak azt ellenőrzi, hogy a konfiguráció sikeres volt-e.): 

1. Ellenőrizze, hogy a Azure Resource Manager kommunikáció működik-e. Típus:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. A hitelesítéshez hívja meg a helyi eszközök API-jait. Típus: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Adja meg a felhasználónevet és a jelszót a *EdgeARMuser* Azure Resource Manager használatával történő kapcsolódáshoz.

1. Ha a Kubernetes-hez konfigurálta a **számítást** , kihagyhatja ezt a lépést. Győződjön meg arról, hogy engedélyezte a hálózati adaptert a számítási feladatokhoz. A helyi felhasználói felületen lépjen a **számítási** beállítások elemre. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog. A létrehozott virtuális gépek az ehhez a porthoz és a társított hálózathoz csatolt virtuális kapcsolókhoz lesznek csatolva. Ügyeljen arra, hogy olyan hálózatot válasszon, amely megfelel a virtuális gép által használt IP-címnek.  

    ![Számítási beállítások engedélyezése 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    A hálózati adapteren engedélyezze a számítást. Azure Stack Edge létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót. Jelenleg ne adjon meg adott IP-címeket a Kubernetes számára. A számítás engedélyezése több percet is igénybe vehet.

    > [!NOTE]
    > Ha GPU virtuális gépeket hoz létre, válassza ki az internethez csatlakozó hálózati adaptert. Ez lehetővé teszi a GPU-bővítmény telepítését az eszközön.


1. Virtuálisgép-szerepkör engedélyezése a Azure Portal. Ez a lépés létrehoz egy egyedi előfizetést az eszközhöz, amely a virtuális gépek helyi API-k használatával történő létrehozására szolgál. 

    1. A virtuálisgép-szerepkör engedélyezéséhez lépjen a Azure Portal Azure Stack Edge-erőforrásra az Azure Stack Edge-eszközhöz. Ugrás az **Edge számítási > Virtual Machines**.

        ![VM-rendszerkép hozzáadása 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Válassza a **Virtual Machines** lehetőséget az **Áttekintés** oldalra való ugráshoz. A virtuális gépek Felhőbeli felügyeletének **engedélyezése** .
        ![VM-rendszerkép hozzáadása 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)