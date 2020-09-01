---
title: Microsoft Azure Stack Edge-eszköz csatlakoztatása és kezelése a Windows PowerShell felületén keresztül | Microsoft Docs
description: Ismerteti, hogyan csatlakozhat a szolgáltatáshoz, majd hogyan felügyelheti Azure Stack Edge-t a Windows PowerShell felületén keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084513"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Azure Stack Edge GPU-eszköz kezelése a Windows PowerShell használatával

Azure Stack Edge-megoldás lehetővé teszi az adatfeldolgozást és a hálózaton keresztüli küldését az Azure-ba. Ez a cikk az Azure Stack Edge-eszköz konfigurációs és felügyeleti feladatait ismerteti. Az eszköz kezeléséhez használhatja a Azure Portal, a helyi webes felhasználói felületet vagy a Windows PowerShell felületét is.

Ebből a cikkből megtudhatja, hogyan kapcsolódhat az eszköz PowerShell-felületéhez és az ezzel a felülettel elvégezhető feladatokhoz. 


## <a name="connect-to-the-powershell-interface"></a>Kapcsolódás a PowerShell felületéhez

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Támogatási csomag létrehozása

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Tanúsítvány feltöltése

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge tanúsítványokat is feltölthet, hogy biztonságos kapcsolatot lehessen létesíteni a IoT Edge-eszköz és a hozzájuk kapcsolódó alsóbb rétegbeli eszközök között. Három IoT Edge tanúsítvány (*. PEM* formátum) szükséges a telepítéséhez:

- Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy a tulajdonos HITELESÍTÉSSZOLGÁLTATÓja
- Eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa
- Eszköz kulcsának tanúsítványa

A következő példa a parancsmag használatát mutatja be IoT Edge tanúsítványok telepítéséhez:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
A parancsmag futtatásakor a rendszer kérni fogja a hálózati megosztás jelszavának megadását.

A tanúsítványokkal kapcsolatos további információkért nyissa meg a [Azure IoT Edge tanúsítványokat](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) , vagy [telepítse a tanúsítványokat egy átjáróra](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Eszköz adatainak megtekintése
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>GPU-illesztőprogram adatainak megtekintése

Ha a számítási szerepkör konfigurálva van az eszközön, a GPU-illesztőprogram adatait a PowerShell felületén keresztül is lekérheti. 

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A használatával `Get-HcsGpuNvidiaSmi` lekérheti az eszköz GPU-illesztőprogramjának adatait.

    A következő példa a parancsmag használatát mutatja be:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Jegyezze fel az illesztőprogram adatait a parancsmag mintájának kimenetéről.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Többfolyamatos szolgáltatás (MPS) engedélyezése

A többfolyamatos szolgáltatás (mp) az NVIDIA GPU-ban olyan mechanizmust biztosít, amelyben több feladat is megoszthatja a GPU-kat, ahol az egyes feladatok a GPU erőforrásainak bizonyos hányadát foglalják magukban. Az alábbi lépéseket követve engedélyezheti az MPS szolgáltatást a Azure Stack Edge-eszközön:

1. Mielőtt elkezdené, győződjön meg arról, hogy: 

    1. Konfigurálta és [aktiválta Azure stack Edge-eszközét](azure-stack-edge-gpu-deploy-activate.md) egy Azure stack Edge/Data Box Gateway erőforrással az Azure-ban.
    1. [Ezen az eszközön konfigurálta a számítást a Azure Portal](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
1. Az alábbi paranccsal engedélyezheti az MPS használatát az eszközön.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Eszköz alaphelyzetbe állítása

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Számítási naplók beolvasása

Ha a számítási szerepkör konfigurálva van az eszközön, a számítási naplókat a PowerShell felületén keresztül is lekérheti.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. Az `Get-AzureDataBoxEdgeComputeRoleLogs` eszközhöz tartozó számítási naplók beszerzéséhez használja a következőt:.

    A következő példa a parancsmag használatát mutatja be:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Itt látható a parancsmaghoz használt paraméterek leírása:
    - `Path`: Adja meg annak a megosztásnak a hálózati elérési útját, ahol létre szeretné hozni a számítási napló csomagot.
    - `Credential`: Adja meg a hálózati megosztáshoz tartozó felhasználónevet. A parancsmag futtatásakor meg kell adnia a megosztási jelszót.
    - `FullLogCollection`: Ez a paraméter biztosítja, hogy a naplófájl tartalmazza az összes számítási naplót. Alapértelmezés szerint a naplófájl csak a naplók egy részhalmazát tartalmazza.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Kubernetes kapcsolatos hibák elhárítása IoT Edge

A Kubernetes-fürt létrehozásakor a `aseuser` rendszer a rendszernévtérhez társított alapértelmezett felhasználót `iotedge` is létrehoz. A IoT Edgeával kapcsolatos problémák hibakereséséhez használhatja ezt a felhasználói és rendszernévtért is.  

### <a name="create-config-file-for-system-namespace"></a>Konfigurációs fájl létrehozása a rendszer névteréhez

A hibakereséshez először hozza létre a `config` névtérhez tartozó fájlt a következővel: `iotedge` `aseuser` .

Futtassa a `Get-HcsKubernetesUserConfig -AseUser` parancsot, és mentse a kimenetet `config` fájlként (nincs fájlkiterjesztés). Mentse a fájlt a `.kube` felhasználói profiljának mappájába a helyi gépen.

Az alábbi példában a parancs kimenete látható `Get-HcsKubernetesUserConfig` .

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

Egy olyan Azure Stack Edge-eszközön, amelyen a számítási szerepkör konfigurálva van, az összes `kubectl` parancs elérhető a modulok figyeléséhez vagy megoldásához. Az elérhető parancsok listájának megjelenítéséhez futtassa `kubectl --help` a parancsot a parancsablakban.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

A parancsok átfogó listáját itt tekintheti `kubectl` meg: [ `kubectl` súgókártya](https://kubernetes.io/docs/reference/kubectl/cheatsheet/). 

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>A Kubernetes-fürtön kívül elérhető szolgáltatás vagy modul beszerzése

A Kubernetes-en kívül elérhető terheléselosztási szolgáltatás vagy modulok IP-címének lekéréséhez futtassa a következő parancsot:

`kubectl get svc -n iotedge`

A következő példa a Kubernetes-fürtön kívül elérhető összes szolgáltatás vagy modul kimenetét jeleníti meg. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
A külső IP-oszlop IP-címe a szolgáltatás vagy a modul külső végpontjának felel meg. [A külső IP-címet a Kubernetes irányítópulton](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)is lekérheti.


### <a name="to-check-if-module-deployed-successfully"></a>Annak ellenőrzését, hogy a modul telepítése sikerült-e

A számítási modulok olyan tárolók, amelyeken a rendszer üzleti logikát alkalmaz. A Kubernetes Pod több tárolót is tartalmazhat. Annak vizsgálatához, hogy a számítási modul telepítése sikeresen megtörtént-e, futtassa a parancsot, és ellenőrizze, hogy fut-e `get pods` a tároló (a számítási modulnak megfelelő).

Egy adott névtérben futó összes hüvely listájának lekéréséhez futtassa a következő parancsot:

`get pods -n <namespace>`

A következő példa a névtérben futó összes hüvely kimenetét jeleníti meg `iotedge` .

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

Az állapot **állapota** azt jelzi, hogy a névtérben lévő összes hüvely fut, a **kész** érték pedig a pod-ban üzembe helyezett tárolók számát jelzi. Az előző mintában az összes hüvely fut, és az egyes hüvelyekben üzembe helyezett modulok futnak. 

Ahhoz, hogy egy adott Pod részletes kimenetet kapjon egy adott névtérhez, futtathatja a következő parancsot:

`kubectl describe pod <pod name> -n <namespace>` 

A minta kimenete itt látható.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>A tároló naplófájljainak beolvasása

A modul naplóinak lekéréséhez futtassa a következő parancsot:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Mivel a `all-containers` jelölő az összes tároló naplóit kiírja, jó módszer a legutóbbi hibák megtekintésére, ha a kapcsolót használja `--tail 10` .

A következő egy minta kimenet. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Kilépés a távoli munkamenetből

A távoli PowerShell-munkamenetből való kilépéshez zárja be a PowerShell ablakát.

## <a name="next-steps"></a>Következő lépések

- [Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md) üzembe helyezése Azure Portalban.
