---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 3a17e73c66c2296cc36b24e3b0a8abfcab00e46a
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89419399"
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

    <!--If you decide to use another network interface for compute, make sure that you:
    
    - Delete all the VMs that you have deployed using Azure Resource Manager.
    
    - Delete all virtual network interfaces and the virtual network associated with this network interface. 
    
    - You can now enable another network interface for compute.-->

<!--1. You may also need to configure TLS 1.2 on your client machine if running older versions of AzCopy.--> 

