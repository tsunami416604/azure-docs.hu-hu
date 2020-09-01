---
title: Az Azure Stack Edge-eszköz monitorozása a Kubernetes-irányítópulton keresztül | Microsoft Docs
description: Ismerteti, hogyan érheti el és használhatja a Kubernetes irányítópultot az Azure Stack Edge-eszköz figyeléséhez.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 9224888a38c86e35df9ad516c761fd7012824c15
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084045"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>A Kubernetes-irányítópult használata az Azure Stack Edge GPU-eszköz figyeléséhez

Ez a cikk azt ismerteti, hogyan érheti el és használhatja a Kubernetes irányítópultot a Azure Stack Edge GPU-eszköz monitorozásához. Az eszköz figyeléséhez használhatja a Azure Portal diagramokat, megtekintheti a Kubernetes irányítópultját, vagy futtathat `kubectl` parancsokat az eszköz PowerShell-felületén keresztül. 

Ez a cikk csak a Kubernetes-irányítópulton végrehajtható figyelési feladatokra koncentrál.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * A Kubernetes-irányítópult elérése az eszközön
> * Az eszközön üzembe helyezett modulok megtekintése
> * Az eszközön üzembe helyezett alkalmazások IP-címének lekérése
> * Az eszközön üzembe helyezett modulok tároló-naplófájljainak megtekintése


## <a name="about-kubernetes-dashboard"></a>Tudnivalók a Kubernetes-irányítópultról

A Kubernetes irányítópult egy webalapú felhasználói felület, amellyel elháríthatja a tároló alkalmazások hibáit. A Kubernetes irányítópult a Kubernetes parancssorának felhasználói felületén alapuló alternatívája `kubectl` . 

Az Azure Stack Edge-eszközön a Kubernetes-irányítópultot csak olvasható módban használhatja, így áttekintést kaphat az Azure Stack Edge-eszközön futó alkalmazásokról, megtekintheti a Kubernetes-fürt erőforrásainak állapotát, és megtekintheti az eszközön bekövetkezett hibákat.

## <a name="access-dashboard"></a>Hozzáférés az irányítópulthoz

A Kubernetes irányítópult írásvédett, és az 31000-es porton futtatja a Kubernetes fő csomópontját. Az irányítópult eléréséhez kövesse az alábbi lépéseket: 

1. Az eszköz helyi felhasználói felületén nyissa meg az **eszközt** , majd válassza az **eszköz végpontok**lehetőséget. A Kubernetes irányítópult URL-címére kattintva nyissa meg az irányítópultot egy böngészőben.

    ![Kubernetes irányítópult URL-címe az eszköz lapján helyi felhasználói felületen](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. A **Kubernetes-irányítópult bejelentkezési** oldalán válassza a **jogkivonat**elemet. 
1. Adjon meg egy jogkivonatot. 
    1. A jogkivonat lekéréséhez [kapcsolódjon az eszköz PowerShell-felületén keresztül](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Futtassa a parancsot:  `Get-HcsKubernetesDashboardToken`
    
    1. Másolja a megjelenő jogkivonat-karakterláncot a parancssorba. Itt látható egy mintakimenet:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Válassza a **Bejelentkezés** lehetőséget.

    ![Bejelentkezés a Kubernetes-irányítópultra](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. Most már csak olvasható módban tekintheti meg az Azure Stack Edge-eszköz Kubernetes irányítópultját.

    ![Kubernetes-irányítópult főoldala](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Modul állapotának megtekintése

A számítási modulok olyan tárolók, amelyeken a rendszer üzleti logikát alkalmaz. Az irányítópulton ellenőrizheti, hogy a számítási modul üzembe helyezése sikeresen megtörtént-e az Azure Stack Edge-eszközön.

A modul állapotának megtekintéséhez kövesse az alábbi lépéseket az irányítópulton:

1. Az irányítópult bal oldali ablaktáblájában lépjen a **névtér**elemre. Szűrje azt a névteret, amelyben IoT Edge modulok jelennek meg, ebben az esetben a **iotedge**.
1. A bal oldali ablaktáblán lépjen a **Munkaterhelések > központi telepítések**elemre.
1. A jobb oldali panelen megjelenik az eszközre telepített összes modul. Ebben az esetben egy GettingStartedWithGPU-modul lett telepítve az Azure Stack Edge-ben. Láthatja, hogy a modul üzembe lett helyezve.

    ![Modul központi telepítésének megtekintése](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Szolgáltatások vagy modulok IP-címének lekérése

Az irányítópult használatával lekérheti azon szolgáltatások vagy modulok IP-címeit, amelyeket ki szeretne állítani a Kubernetes-fürtön kívül. 

A külső szolgáltatások IP-tartományát az eszköz helyi webes FELÜLETén keresztül rendelheti hozzá a **számítási hálózati beállítások** lapon. IoT Edge modulok üzembe helyezésekor előfordulhat, hogy egy adott modulhoz vagy szolgáltatáshoz rendelt IP-címet szeretné beszerezni. 

Az IP-cím lekéréséhez kövesse az alábbi lépéseket az irányítópulton:

1. Az irányítópult bal oldali ablaktáblájában lépjen a **névtér**elemre. Szűrje azt a névteret, amelyben a külső szolgáltatás telepítve van, ebben az esetben a **iotedge**.
1. A bal oldali ablaktáblán lépjen a **felderítés és terheléselosztás > szolgáltatások**elemre.
1. A jobb oldali ablaktáblában az összes olyan szolgáltatást látni fogja, amely a `iotedge` Azure stack peremhálózati eszközén található névtérben fut.

    ![Külső szolgáltatások IP-címének beolvasása](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Vannak olyan példányok, ahol meg kell tekintenie a tároló naplóit. Az irányítópult használatával lekérheti a Kubernetes-fürtön üzembe helyezett adott tárolóhoz tartozó naplókat.

A tároló naplófájljainak megtekintéséhez kövesse az alábbi lépéseket az irányítópulton:

1. Az irányítópult bal oldali ablaktáblájában lépjen a **névtér**elemre. Szűrje a névteret, ahol a IoT Edge modulokat üzembe helyezi, ebben az esetben a **iotedge**.
1. A bal oldali ablaktáblán lépjen a **Munkaterhelések > hüvelyek**elemre.
1. A jobb oldali ablaktáblán látni fogja az eszközön futó összes hüvelyt. Azonosítsa a modult futtató Pod-t, amelyre vonatkozóan meg szeretné tekinteni a naplókat. Válassza ki az azonosított Pod függőleges három pontját, és a helyi menüben válassza a **naplók**lehetőséget.

    ![Tárolói naplók megtekintése 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. A naplók az irányítópultba beépített naplók megjelenítőben jelennek meg. Letöltheti a naplókat is.

    ![Tárolói naplók megtekintése 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="next-steps"></a>Következő lépések

További információ a Kubernetes kapcsolatos hibák elhárításáról <!--insert link-->.
