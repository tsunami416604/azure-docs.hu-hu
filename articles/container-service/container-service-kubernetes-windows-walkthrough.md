---
title: "Azure Kubernetes-fürt Windows rendszeren | Microsoft Docs"
description: "Kubernetes-fürt üzembe helyezése Windows-tárolókhoz az Azure Container Service-ben és az első lépések"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: hu-hu
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Bevezetés a Kubernetes és a Windows-tárolók használatába a Container Service-ben


Ez a cikk bemutatja, hogyan hozhat létre olyan Kubernetes-fürtöt az Azure Container Service-ben, amely Windows-csomópontokat tartalmaz Windows-tárolók futtatásához. Első lépésként használja a Kubernetes-fürt Azure Container Service-ben való létrehozására szolgáló `az acs` Azure CLI 2.0-parancsokat. Ezután a Kubernetes `kubectl` parancssori eszközével elkezdheti használni a Docker-rendszerképekből kiépített Windows-tárolókat. 

> [!NOTE]
> Az Azure Container Service-ben a Windows-tárolók Kubernetes-szel való használatának támogatása előzetes verzióban érhető el. 
>



A következő kép egy Kubernetes-fürt architektúráját mutatja be az Azure Container Service-ben. A fürt egy Linux-főcsomóponttal és két Windows-ügyfélcsomóponttal rendelkezik. 

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* A Linux-főcsomópont szolgálja ki a Kubernetes REST API-t, és SSH-val a 22. porton, a `kubectl` használatával pedig a 443. porton érhető el. 
* A Windows-ügyfélcsomópontok egy Azure rendelkezésre állási csoportba vannak besorolva, és a tárolókat futtatják. A Windows-csomópontok RDP SSH-alagúttal érhetők el a főcsomóponton keresztül. A rendszer automatikusan adja hozzá az Azure Load Balancer-szabályokat a fürthöz a közzétett szolgáltatásoktól függően.



Minden virtuális gép ugyanazon a privát virtuális hálózaton található, és teljes mértékben elérhetők egymás számára. Minden virtuális gép egy kubeletet, Dockert és egy proxyt futtat.

További információt [Az Azure Container Service bemutatása](container-service-intro.md) és [A Kubernetes dokumentációja](https://kubernetes.io/docs/home/) című cikkekben talál.

## <a name="prerequisites"></a>Előfeltételek
Az Azure Container Service-fürtök az Azure CLI 2.0-s verziójával való létrehozásának feltételei:
* szükség van egy Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/));
* telepítenie kell az [Azure CLI 2.0-t](/cli/azure/install-az-cli2), és be kell jelentkeznie

A Kubernetes-fürthöz emellett az alábbiakra is szüksége lesz. Ezeket előzetesen is előkészítheti, vagy az `az acs create` parancs kapcsolóival automatikusan is létrehozhatja őket a fürt üzembe helyezése során. 

* **SSH RSA nyilvános kulcs**: a Secure Shell (SSH) RSA-kulcsok létrehozásával kapcsolatban lásd az [OS X és Linux](../virtual-machines/linux/mac-create-ssh-keys.md) vagy a [Windows](../virtual-machines/linux/ssh-from-windows.md) rendszerhez készült útmutatót. 

* **Szolgáltatásnév ügyfél-azonosítója és kulcsa**: az Azure Active Directory-szolgáltatásnevek létrehozásának lépései és további információk: [Tudnivalók a Kubernetes-fürthöz tartozó szolgáltatásnévről](container-service-kubernetes-service-principal.md).

A jelen cikkben szereplő példaparancs automatikusan létrehozza az SSH-kulcsokat és egy egyszerű szolgáltatást.
  
## <a name="create-your-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Itt Azure CLI 2.0-parancsokat talál a fürt létrehozásához. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot egy olyan helyen, ahol az Azure Container Service [elérhető](https://azure.microsoft.com/regions/services/). Az alábbi parancs létrehoz egy *myKubernetesResourceGroup* nevű erőforráscsoportot a *westus* helyen:

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Kubernetes-fürt létrehozása Windows-ügyfélcsomópontokkal

Hozzon létre egy Kubernetes-fürtöt az erőforráscsoportban az `az acs create` parancs, valamint az `--orchestrator-type=kubernetes` és a `--windows` ügynökkapcsolók használatával. A parancs szintaxisát lásd az `az acs create` [Súgójában](/cli/azure/acs#create).

Az alábbi parancs egy *myKubernetesClusterName* nevű Container Service-fürtöt hoz létre *myPrefix* DNS-előtaggal a felügyeleti csomópont számára, illetve létrehozza a megadott hitelesítő adatokat a Windows-csomópontok eléréséhez. A parancs jelen verziója létrehozza az SSH RSA-kulcsokat és egy szolgáltatásnevet a Kubernetes-fürthöz.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Pár perc múlva befejeződik a parancs végrehajtása, és rendelkeznie kell egy működő Kubernetes-fürttel.

> [!IMPORTANT]
> Ha a fiók nem rendelkezik jogosultsággal az Azure AD-szolgáltatásnév létrehozásához, a parancs a következőhöz hasonló hibaüzenetet hoz létre: `Insufficient privileges to complete the operation.`. További információ: [Tudnivalók a Kubernetes-fürthöz tartozó szolgáltatásnévről](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Csatlakozás a fürthöz a kubectl használatával

Ha a csatlakozni kíván a Kubernetes-fürthöz az ügyfélszámítógépről, ehhez a Kubernetes [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) nevű parancssori ügyfelét használhatja. 

Ha a `kubectl` még nincs telepítve helyben, az `az acs kubernetes install-cli` paranccsal telepítheti. (Vagy le is töltheti a [Kubernetes webhelyéről](https://kubernetes.io/docs/tasks/kubectl/install/).)

**Linux vagy macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> Ez a parancs alapértelmezés szerint Linux vagy macOS rendszeren az `/usr/local/bin/kubectl`, illetve Windows rendszeren pedig a `C:\Program Files (x86)\kubectl.exe` helyre telepíti a `kubectl` bináris fájlt. Másik telepítési útvonal megadásához használja az `--install-location` paramétert.
>
> A `kubectl` telepítése után győződjön meg arról, hogy a könyvtára elérhető-e a rendszerbeli elérési úton, vagy adja hozzá az elérési úthoz. 


Ezután futtassa az alábbi parancsot a fő Kubernetes-fürtkonfiguráció helyi `~/.kube/config` fájlba való letöltéséhez:

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

Most már készen áll arra, hogy hozzáférjen a fürthöz a gépéről. Próbálja meg futtatni a következőt:

```bash
kubectl get nodes
```

Ellenőrizze, hogy látja-e a fürtben található gépek listáját.

![Kubernetes-fürtben futó csomópontok](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Az első Kubernetes-szolgáltatás létrehozása

A fürt létrehozása és a `kubectl` használatával való kapcsolódás után megpróbálhat elindítani egy Windows-appot egy Docker-tárolóból, és közzétenni azt az interneten. Ez az alapszintű példa JSON-fájlt használ a Microsoft Internet Information Server- (IIS-) tároló megadásához, majd a `kubctl apply` használatával létrehozza a tárolót. 

1. Hozzon létre egy `iis.json` nevű helyi fájlt, és másolja a következőket. Ez a fájl arra utasítja a Kubernetest, hogy futtassa az IIS-t Windows Server 2016 Server Core rendszeren, a [Docker Hubból](https://hub.docker.com/r/microsoft/iis/) származó, nyilvános rendszerkép használatával. A tároló a 80-as portot használja, de kezdetben csak a fürthálózaton belül érhető el.

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. A alkalmazás elindításához írja be a következőt:  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. A tároló üzembe helyezésének nyomon követéséhez írja be a következőket:  
  ```bash
  kubectl get pods
  ```
  A tároló üzembe helyezése közben az állapot: `ContainerCreating`. 

  ![ContainerCreating állapotú IIS-tároló](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  Az IIS-rendszerkép mérete miatt több percig is eltarthat, amíg a tároló eléri a `Running` állapotot.

  ![Futó állapotú IIS-tároló](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. A tároló elérhetővé tételéhez írja be a következő parancsot:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Ezzel a paranccsal a Kubernetes létrehoz egy nyilvános IP-címmel rendelkező Azure Load Balancer-szabályt. A rendszer néhány percen belül propagálja a módosítást a terheléselosztóra. Részletes információk: [Tárolók terheléselosztása Kubernetes-fürtön az Azure Container Service-ben](container-service-kubernetes-load-balancing.md).

5. Futtassa az alábbi parancsot a szolgáltatás állapotának megtekintéséhez.

  ```bash
  kubectl get svc
  ```

  Az IP-cím először a következőképpen jelenik meg: `pending`

  ![Függőben lévő külső IP-cím](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  Pár percen belül megtörténik az IP-cím beállítása:
  
  ![IIS külső IP-címe](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. Ha már elérhető a külső IP-cím, megnyithatja a böngészőben:

  ![Az IIS keresését ábrázoló kép](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Az IIS-pod törléséhez írja be a következőt:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Következő lépések

* A Kubernetes felhasználói felület használatához futtassa a következő parancsot: `kubectl proxy`. Ezután nyissa meg a http://localhost:8001/ui címet.

* Az egyéni IIS-webhely létrehozásának és Windows-tárolón történő futtatásának lépéseit lásd a [Docker Hub](https://hub.docker.com/r/microsoft/iis/) útmutatójában.

* A Windows-csomópontok főkiszolgáló felé tartó RDP SSH-alagúton keresztül történő elérése a PuTTy használatával: [Az ACS-motor dokumentációja](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

