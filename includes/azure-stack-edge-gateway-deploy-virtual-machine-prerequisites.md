---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7b98d3c1febd68a7ee73cf3064f4d8e108ea81fa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083846"
---
Mielőtt üzembe helyezi a virtuális gépeket az Azure Stack Edge-eszközön, konfigurálnia kell az ügyfelet, hogy Azure Resource Manageron keresztül kapcsolódjon az eszközhöz Azure PowerShellon keresztül. A részletes lépésekért lépjen a [kapcsolódás Azure Resource Managerhoz az Azure stack Edge-eszközön](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Győződjön meg arról, hogy a következő lépések használhatók az eszköz ügyfélhez való eléréséhez (ezt a konfigurációt Azure Resource Managerhoz való csatlakozáskor tette meg, és csak azt ellenőrzi, hogy a konfiguráció sikeres volt-e.): 

1. Ellenőrizze, hogy a Azure Resource Manager kommunikáció működik-e. Típus:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>:30005/
    ```

1. A hitelesítéshez hívja meg a helyi eszközök API-jait. Típus: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Adja meg a felhasználónevet és a jelszót a *EdgeARMuser* Azure Resource Manager használatával történő kapcsolódáshoz.

1. Ha a Kubernetes-hez konfigurálta a **számítást** , kihagyhatja ezt a lépést. Győződjön meg arról, hogy engedélyezte a hálózati adaptert a számítási feladatokhoz. A helyi felhasználói felületen lépjen a **számítási** beállítások elemre. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog. A létrehozott virtuális gépek az ehhez a porthoz és a társított hálózathoz csatolt virtuális kapcsolókhoz lesznek csatolva. Ügyeljen arra, hogy olyan hálózatot válasszon, amely megfelel a virtuális géphez használt statikus IP-címnek.  

    ![Számítási beállítások engedélyezése 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    A hálózati adapteren engedélyezze a számítást. Azure Stack Edge létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót. Jelenleg ne adjon meg adott IP-címeket a Kubernetes számára. A számítás engedélyezése több percet is igénybe vehet.

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

