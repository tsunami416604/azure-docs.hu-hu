---
title: A PHP Vendégkönyv alkalmazás üzembe helyezése az arc-kompatibilis Kubernetes Azure Stack Edge GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan helyezhet üzembe egy PHP-beli Vendégkönyv állapot nélküli alkalmazást a Redis használatával az Azure Stack Edge-alapú Kubernetes-fürtön lévő GitOps segítségével.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 83ac012c861a0d066bdc47d8e15cbe7ac398aa23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254219"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-gpu"></a>PHP-beli Vendégkönyv állapot nélküli alkalmazás üzembe helyezése a Redis on arc enabled Kubernetes-fürtön Azure Stack Edge GPU-ban

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy egyszerű, többrétegű webes alkalmazást a Kubernetes és az Azure arc használatával. Ez a példa a következő összetevőkből áll:

- Egypéldányos Redis-főkiszolgáló a Vendégkönyv-bejegyzések tárolásához
- Több replikált Redis-példány az olvasások kiszolgálására
- Több webes frontend-példány

Az üzembe helyezés a Azure Stack Edge-eszközön lévő arc-kompatibilis Kubernetes-fürtön található GitOps használatával végezhető el. 

Ez az eljárás azok számára készült, akik áttekintették a Kubernetes számítási feladatait [Azure stack Edge-eszközön](azure-stack-edge-gpu-kubernetes-workload-management.md) , és ismeri az [Azure arc-kompatibilis Kubernetes (előzetes verzió)](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)fogalmait.


## <a name="prerequisites"></a>Előfeltételek

Az állapot nélküli alkalmazás üzembe helyezése előtt győződjön meg arról, hogy végrehajtotta a következő előfeltételeket az eszközön és az ügyfélen, amelyet az eszköz eléréséhez fog használni:

### <a name="for-device"></a>Az eszköz esetén

1. A hitelesítő adatok egy 1 csomópontos Azure Stack peremhálózati eszközhöz vannak bejelentkezett.
    1. Az eszköz aktiválva van. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).
    1. Az eszközön a Azure Portal-n keresztül konfigurált számítási szerepkör és egy Kubernetes-fürt van konfigurálva. Lásd: [számítás konfigurálása](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Engedélyezte az Azure arc használatát a meglévő Kubernetes-fürtön az eszközön, és a Azure Portal megfelelő Azure arc-erőforrással rendelkezik. A részletes lépésekért lásd: az [Azure arc engedélyezése Azure stack peremhálózati eszközön](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Az eszközt elérő ügyfél

1. Van egy Windows-ügyfélrendszer, amely az Azure Stack Edge-eszköz elérésére szolgál majd.
  
    - Az ügyfél Windows PowerShell 5,0-es vagy újabb verzióját futtatja. A Windows PowerShell legújabb verziójának letöltéséhez nyissa meg a következőt: [install Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7).
    
    - Bármely más ügyfél [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) is rendelkezhet. Ez a cikk a Windows-ügyfelek használatakor követendő eljárást ismerteti. 
    
1. Végrehajtotta a következő témakörben leírt eljárást: [Azure stack Edge-eszközön található Kubernetes-fürt elérése](azure-stack-edge-gpu-create-kubernetes-cluster.md). A következőket teheti:
    
    - `kubectl`Az ügyfélre telepítve  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Győződjön meg arról, hogy az `kubectl` ügyfél verziószáma nem több, mint egy olyan verzió, amely az Azure stack Edge-eszközön futó Kubernetes-főverzión fut. 
      - Ezzel a paranccsal `kubectl version` ellenőrizhető az ügyfélen futó kubectl verziója. Jegyezze fel a teljes verziót.
      - Az Azure Stack Edge-eszköz helyi felhasználói felületén lépjen az **Áttekintés** elemre, és jegyezze fel a Kubernetes szoftver számát. 
      - Ellenőrizze, hogy ez a két verzió kompatibilis-e a támogatott Kubernetes-verzióban megadott leképezéssel <!--insert link-->.

1. Rendelkezik egy [GitOps-konfigurációval, amely Azure arc-telepítés futtatására használható](https://github.com/kagoyal/dbehaikudemo). A következő fájlokat fogja használni a `yaml` Azure stack peremhálózati eszközön való üzembe helyezéshez.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Az üzembe helyezés konfigurálása

A következő lépésekkel konfigurálhatja az Azure arc-erőforrást a GitOps-konfiguráció üzembe helyezéséhez a Azure Portal használatával: 

1. A Azure Portal nyissa meg az Azure arc-erőforrást, amelyet akkor hozott létre, amikor engedélyezte az Azure arc használatát a Kubernetes-fürtön az eszközön. 

    ![Ugrás az Azure arc-erőforrásra](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Lépjen a **konfigurációk** elemre, és válassza a **+ konfiguráció hozzáadása**elemet.

    ![Ugrás a konfigurációkhoz](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. A **konfiguráció hozzáadása**lapon adja meg a mezők megfelelő értékeit, majd válassza az **alkalmaz**lehetőséget.

    |Paraméter  |Leírás |
    |---------|---------|
    |Konfiguráció neve     | A konfigurációs erőforrás neve.        |
    |Operátor példányának neve     |Az operátor példányának neve egy adott konfiguráció azonosításához. A név a legfeljebb 253 karakterből álló karakterlánc, amely csak kisbetűket, alfanumerikus karaktereket, kötőjelet és pontot tartalmazhat.         |
    |Operátor névtere     | Állítsa a **demotestguestbook** értékre, mivel ez megegyezik az üzemelő példányban megadott névtérrel `yaml` . <br> A mező határozza meg azt a névteret, amelyben a kezelő telepítve van. A név a legfeljebb 253 karakterből álló karakterlánc, amely csak kisbetűket, alfanumerikus karaktereket, kötőjelet és pontot tartalmazhat.         |
    |Tárház URL-címe     |<br>A git-tárház elérési útja `http://github.com/username/repo` vagy `git://github.com/username/repo` formátuma, ahol a GitOps-konfiguráció található.         |
    |Operátor hatóköre     | Válassza a **névtér**lehetőséget. <br>Ez határozza meg azt a hatókört, amelyen a kezelő telepítve van. Válassza ezt a névteret. Az operátor a telepítési YAML-fájlokban megadott névtérbe lesz telepítve.       |
    |Operátor típusa     | Alapértelmezés szerint hagyja. <br>Ezzel a beállítással adható meg, hogy az operátor típusa alapértelmezés szerint a Flux értékre van-e állítva.        |
    |Operátori paraméterek     | Hagyja üresen. <br>Ez a mező a Flux-kezelőnek átadandó paramétereket tartalmaz.        |
    |Helm     | A beállítás **Letiltva**értékre állítható. <br>Akkor engedélyezze ezt a beállítást, ha diagram alapú központi telepítéseket hajt végre.        |


    ![Konfiguráció hozzáadása](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. A konfiguráció központi telepítése elindul, és a **kezelő állapota** **függőben**jelenik meg. 

    ![Ugrás a konfigurációkhoz](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. Az üzembe helyezés pár percet vesz igénybe. Az üzembe helyezés befejeztével a **kezelő állapota** **telepítettként**jelenik meg.

    ![Ugrás a konfigurációkhoz](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)


## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A GitOps-konfiguráción keresztüli központi telepítés létrehoz egy névteret a git-tárházban `demotestguestbook` található központi telepítési `yaml` fájlokban megadott módon.

1. A GitOps-konfiguráció alkalmazása után [kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Futtassa a következő parancsot az üzemelő `demotestguestbook` példányhoz tartozó névtérben futó hüvelyek listázásához.

    `kubectl get pods -n <your-namespace>`
  
    Íme egy minta kimenet.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. Ebben a példában a frontend szolgáltatást a következő típusként telepítettük: terheléselosztó. A Vendégkönyv megtekintéséhez meg kell keresnie ennek a szolgáltatásnak az IP-címét. Futtassa az alábbi parancsot:

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. A előtér-szolgáltatás `type:LoadBalancer` külső IP-címmel rendelkezik. Ez az IP-cím a külső szolgáltatásokhoz megadott IP-címtartomány alapján történik a számítási hálózati beállítások az eszközön való konfigurálásakor. A következő IP-cím használatával megtekintheti a vendégkönyvet az URL-címen: `https://<external-IP-address>` .

    ![Vendégkönyv megtekintése](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Központi telepítés törlése

A központi telepítés törléséhez törölheti a konfigurációt a Azure Portalból. Ez törli a létrehozott objektumokat, beleértve az üzembe helyezéseket és a szolgáltatásokat is.

1. A Azure Portalban nyissa meg az Azure arc-erőforrás > konfigurációit. 
1. Keresse meg a törölni kívánt konfigurációt. Válassza a... a helyi menü meghívásához és a **Törlés**lehetőség kiválasztásához.
    ![Konfiguráció törlése](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

A konfiguráció törléséhez több percet is igénybe vehet.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [figyelheti a Kubernetes irányítópultot a Azure stack Edge-eszközön üzemelő példányok figyelésére](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)
