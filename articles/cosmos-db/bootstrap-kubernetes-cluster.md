---
title: Az Azure Kubernetes használata Azure Cosmos DB
description: Megtudhatja, hogyan indít el egy Kubernetes-fürtöt az Azure-ban, amely Azure Cosmos DBt használ (előzetes verzió)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093727"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Az Azure Kubernetes és a Azure Cosmos DB használata (előzetes verzió)

A Azure Cosmos DB etcd API-val a Azure Cosmos DB használhatja a háttérbeli tárolóként az Azure Kubernetes. Azure Cosmos DB megvalósítja a etcd huzal protokollt, amely lehetővé teszi, hogy a főcsomópont API-kiszolgálói a Azure Cosmos DB ugyanúgy használják, mint a helyileg telepített etcd. a Azure Cosmos DB etcd API jelenleg előzetes verzióban érhető el. Ha az Azure Cosmos etcd API-t használja a Kubernetes-tárolóhoz, a következő előnyöket kapja: 

* Nincs szükség a etcd manuális konfigurálására és kezelésére.
* A Cosmos által garantált etcd magas rendelkezésre állása (99,99% egyetlen régióban, 99,999% több régióban).
* A etcd rugalmas skálázhatósága.
* Az alapértelmezett & a vállalati használatra készen áll.
* Piacvezető, átfogó SLA-kat.

Ha többet szeretne megtudni a Azure Cosmos DB etcd API-ról, [](etcd-api-introduction.md) tekintse meg az áttekintő cikket. Ez a cikk bemutatja, hogyan használható az [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (ak-Engine) egy olyan Kubernetes-fürt rendszerindítására az Azure-ban, amely helyileg telepített és konfigurált etcd helyett [Azure Cosmos db](https://docs.microsoft.com/azure/cosmos-db/) használ. 

## <a name="prerequisites"></a>Előfeltételek

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)legújabb verzióját. Az Azure CLI-t az operációs rendszeréhez és a telepítéshez is letöltheti.

1. Telepítse az Azure Kubernetes Engine [legújabb verzióját](https://github.com/Azure/aks-engine/releases) . A különböző operációs rendszerekre vonatkozó telepítési utasítások az [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) oldalon érhetők el. Ehhez csupán a csatolt doc-hoz tartozó **Kabai motor telepítése** szakasz lépéseit kell megadnia. A letöltés után bontsa ki a zip-fájlt.

   Az Azure Kubernetes Engine (**AK-Engine**) Azure Resource Manager sablonokat hoz létre az Azure-beli Kubernetes-fürtökhöz. Az Kabai-motor bemenete a fürt definíciós fájlja, amely leírja a kívánt fürtöt, beleértve a Orchestrator, a szolgáltatásokat és az ügynököket. A bemeneti fájlok szerkezete hasonló az Azure Kubernetes Service nyilvános API-hoz.

1. A Azure Cosmos DB etcd API jelenleg előzetes verzióban érhető el. Regisztráljon az előzetes verzió használatára a következő címen: https://aka.ms/cosmosetcdapi-signup. Az űrlap elküldése után az előfizetését az Azure Cosmos etcd API használatára fogja engedélyezni. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>A fürt üzembe helyezése Azure Cosmos DB

1. Nyisson meg egy parancssori ablakot, és jelentkezzen be az Azure-ba a következő paranccsal:

   ```azurecli-interactive
   az login 
   ```

1. Ha egynél több előfizetéssel rendelkezik, váltson arra az előfizetésre, amelyet a Azure Cosmos DB etcd API számára engedélyezett. A szükséges előfizetésre váltás a következő paranccsal végezhető el:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Ezután hozzon létre egy új erőforráscsoportot, amelybe telepíteni fogja az Azure Kubernetes-fürt által igényelt erőforrásokat. Győződjön meg arról, hogy az erőforráscsoportot az "CentralUS" régióban hozza létre. Az erőforráscsoport nem kötelező az "CentralUS" régióban, azonban az Azure Cosmos etcd API jelenleg csak a "CentralUS" régióban helyezhető üzembe. Ezért érdemes a Kubernetes-fürtöt a Cosmos etcd-példánnyal együtt elhelyezni:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Ezután hozzon létre egy egyszerű szolgáltatásnevet az Azure Kubernetes-fürt számára, hogy kommunikálni tudjon az azonos erőforráscsoport részét képező erőforrásokkal. Az Azure CLI, a PowerShell vagy a Azure Portal használatával létrehozhat egy egyszerű szolgáltatást, ebben a példában a CLI-t fogja létrehozni.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Ez a parancs egy egyszerű szolgáltatásnév részleteit jeleníti meg, például:
   
   ```cmd
   Retrying role assignment creation: 1/36
   {
     "appId": "8415a4e9-4f83-46ca-a704-107457b2e3ab",
     "displayName": "azure-cli-2019-04-19-19-01-46",
     "name": "http://azure-cli-2019-04-19-19-01-46",
     "password": "102aecd3-5e37-4f3d-8738-2ac348c2e6a7",
     "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
   }
   ```
   
   Jegyezze fel a **AppID** és a **jelszó** mezőt, mivel ezeket a paramétereket a következő lépésekben fogja használni. 

1. A parancssorban navigáljon ahhoz a mappához, ahol az Azure Kubernetes motor végrehajtható fájlja található. A parancssorban például a következő módon nyithatja meg a mappát:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Nyisson meg egy tetszőleges szövegszerkesztőt, és Definiáljon egy Resource Manager-sablont, amely a Azure Cosmos DB etcd API-val telepíti az Azure Kubernetes-fürtöt. Másolja az alábbi JSON-definíciót a szövegszerkesztőbe, és mentse a `apiModel.json`fájlt a következőként:

   ```json

   {
    "apiVersion": "vlabs",
    "properties": {
        "orchestratorProfile": {
            "orchestratorType": "Kubernetes",
            "kubernetesConfig": {
                "useManagedIdentity": false
            }
        },
        "masterProfile": {
            "count": 1,
            "dnsPrefix": "",
            "vmSize": "Standard_D2_v3",
            "cosmosEtcd": true
        },
        "agentPoolProfiles": [
            {
                "name": "agent",
                "count": 1,
                "vmSize": "Standard_D2_v3",
                "availabilityProfile": "AvailabilitySet"
            }
        ],
        "linuxProfile": {
            "adminUsername": "azureuser",
            "ssh": {
                "publicKeys": [
                    {
                        "keyData": ""
                    }
                ]
            }
        }
    }
   }
   ```

   A JSON-/fürt-definíciós fájlban a **"cosmosEtcd": true (igaz**) paramétert kell megjegyezni. Ez a paraméter a "masterProfile" tulajdonságban található, és azt jelzi, hogy a központi telepítés az Azure Cosmos etcd API-t használja a normál etcd helyett. 

1. Telepítse a Azure Cosmos DBt használó Azure Kubernetes-fürtöt a következő paranccsal:

   ```cmd
   aks-engine deploy \
     --subscription-id <Your_Azure_subscription_ID> \
     --client-id <Service_ principal_appId> \
     --client-secret <Service_ principal_password> \
     --dns-prefix <Region_unique_dns_name > \
     --location centralus \
     --resource-group <Resource_Group_Name> \
     --api-model <Fully_qualified_path_to_the_template_file>  \
     --force-overwrite
   ```

   Az Azure Kubernetes Engine egy olyan fürtcsomópont-definíciót használ, amely az Azure-Kubernetes kívánt formáját, méretét és konfigurációját ismerteti. Több szolgáltatás is engedélyezhető a fürt definícióján keresztül. Ebben a példában a következő paramétereket fogja használni:

   * **előfizetés-azonosító:** Azure Cosmos DB etcd API-val rendelkező Azure-előfizetés azonosítója.
   * **client-id:** Az egyszerű szolgáltatás appId. A `appId` vissza lett visszaadva a 4. lépésben megadott kimenetként.
   * **Ügyfél – titok:** Az egyszerű szolgáltatásnév jelszava vagy véletlenszerűen generált jelszó. Ez az érték a 4. lépésben a "password" paraméterben kimenetként lett visszaadva. 
   * **dnsPrefix:** Régió – egyedi DNS-név. Ez az érték az állomásnév részét képezi (például: myprod1, átmeneti).
   * **helyen**  A hely, ahol a fürtöt telepíteni kell, a jelenleg csak a "CentralUS" támogatott.

   > [!Note]
   > Az Azure Cosmos etcd API jelenleg csak a "CentralUS" régióban helyezhető üzembe. 
 
   * **api-model:** A sablonfájl teljesen minősített elérési útja.
   * **kényszerített felülírás:** Ezzel a beállítással automatikusan felül lehet írni a kimeneti könyvtár meglévő fájljait.
 
   A következő parancs egy példaként szolgáló központi telepítést mutat be:

   ```cmd
   aks-engine deploy \
     --subscription-id 1234fc61-1234-1234-1234-be1234d12c1b \
     --client-id 1234a4e9-4f83-46ca-a704-107457b2e3ab \
     --client-secret 123aecd3-5e37-4f3d-8738-2ac348c2e6a7 \
     --dns-prefix aks-sg-test \
     --location centralus \
     --api-model "C:\Users\demouser\Downloads\apiModel.json"  \
     --force-overwrite
   ```

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A sablon üzembe helyezése több percet is igénybe vehet. Az üzembe helyezés sikeres befejeződése után a következő kimenet jelenik meg a parancsok parancssorában:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Az erőforráscsoport mostantól olyan erőforrásokat tartalmaz, mint a-Virtual Machine, az Azure Cosmos Account (etcd API), a Virtual Network, a rendelkezésre állási csoport és a Kubernetes-fürt által igényelt egyéb erőforrások. 

Az Azure Cosmos-fiók neve megegyezik a megadott DNS-előtaggal, amely a k8s-vel van hozzáfűzve. Az Azure Cosmos-fiókját a rendszer automatikusan kiépíti egy **EtcdDB** nevű adatbázissal és egy **EtcdData**nevű tárolóval. A tároló tárolja a etcd kapcsolatos összes adatát. A tároló bizonyos számú kérési egységgel lett kiépítve, és a [számítási feladatok alapján méretezheti (növelheti/csökkentheti) az átviteli sebességet](scaling-throughput.md) . 

## <a name="next-steps"></a>További lépések

* Ismerje meg [, hogyan dolgozhat az Azure Cosmos Database, tárolók és elemek](databases-containers-items.md) használatával
* Útmutató a kiépített [átviteli sebességek optimalizálásához](optimize-cost-throughput.md)

