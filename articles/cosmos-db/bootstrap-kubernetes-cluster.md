---
title: Az Azure Kubernetes használata az Azure Cosmos DB-vel
description: Megtudhatja, hogyan lehet egy Azure Cosmos DB-t használó Kubernetes-fürt indítása az Azure-ban (előzetes verzió)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 9dbbc914580d8d80a3f9b7d730574e24b44827c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70093727"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Az Azure Kubernetes használata az Azure Cosmos DB-vel (előzetes verzió)

Az Azure Cosmos DB etcd API-ja lehetővé teszi, hogy az Azure Cosmos DB-t használja az Azure Kubernetes háttértárolójaként. Az Azure Cosmos DB megvalósítja az etcd wire protokollt, amely lehetővé teszi a fő csomópont API-kiszolgálói számára az Azure Cosmos DB használatát, ugyanúgy, mint egy helyileg telepített etcd. etcd API az Azure Cosmos DB jelenleg előzetes verzióban. Ha az Azure Cosmos etcd API-t használja a Kubernetes háttértárolójaként, a következő előnyöket kapja: 

* Nem kell manuálisan beállítani és kezelni, stb.
* Magas rendelkezésre állása etcd, garantált Cosmos (99,99% egyetlen régióban, 99,999% több régióban).
* Az etcd rugalmas skálázhatósága.
* Biztonságos, alapértelmezés szerint & vállalati használatra kész.
* Iparágvezető, átfogó SLA-k.

Ha többet szeretne megtudni az etcd API-ról az Azure Cosmos DB-ben, tekintse meg az [áttekintő](etcd-api-introduction.md) cikket. Ez a cikk bemutatja, hogyan használhatja az [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-engine) a kubernetes-fürt az Azure-ban, amely az [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) helyett a helyileg telepített és konfigurált etcd. 

## <a name="prerequisites"></a>Előfeltételek

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)legújabb verzióját. Letöltheti az Azure CLI-t az operációs rendszerére jellemzően, és telepítheti.

1. Telepítse az Azure Kubernetes Engine [legújabb verzióját.](https://github.com/Azure/aks-engine/releases) A különböző operációs rendszerek telepítési utasításai az [Azure Kubernetes Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) lapon érhetők el. Csak a csatolt dokumentum **AKS-motor telepítése** szakaszának lépéseire van szüksége. A letöltés után bontsa ki a zip fájlt.

   Az Azure Kubernetes Engine (**aks-engine)** Azure Resource Manager-sablonokat hoz létre az Azure-fürtökhöz az Azure-ban. Az aks-engine bemenete egy fürtdefiníciós fájl, amely leírja a kívánt fürtöt, beleértve az orchestratort, a szolgáltatásokat és az ügynököket. A bemeneti fájlok szerkezete hasonló az Azure Kubernetes-szolgáltatás nyilvános API-jéhez.

1. Az etcd API az Azure Cosmos DB jelenleg előzetes verzióban. Regisztráljon, hogy használhassa https://aka.ms/cosmosetcdapi-signupaz előnézeti verziót a következő helyen: . Miután elküldte az űrlapot, az előfizetés lesz az Azure Cosmos etcd API használatához. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>A fürt üzembe helyezése az Azure Cosmos DB-vel

1. Nyisson meg egy parancssori ablakot, és jelentkezzen be az Azure-ba a következő paranccsal:

   ```azurecli-interactive
   az login 
   ```

1. Ha egynél több előfizetéssel rendelkezik, váltson át az Azure Cosmos DB-n stb. A következő paranccsal válthat a szükséges előfizetésre:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Ezután hozzon létre egy új erőforráscsoportot, ahol üzembe helyezi az Azure Kubernetes-fürt által igényelt erőforrásokat. Győződjön meg arról, hogy hozza létre az erőforráscsoportot a "centralus" régióban. Nem kötelező, hogy az erőforráscsoport a "centralus" régióban legyen, azonban az Azure Cosmos etcd API jelenleg csak a "centralus" régióban telepíthető. Tehát a legjobb, ha a Kubernetes-fürt a Cosmos etcd példájával együtt helyezkedik el:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Ezután hozzon létre egy egyszerű szolgáltatás az Azure Kubernetes-fürthöz, hogy kommunikáljon az ugyanazon erőforráscsoport részét játszó erőforrásokkal. Az Azure CLI, a PowerShell vagy az Azure Portal használatával egyszerű szolgáltatást hozhat létre ebben a példában a CLI-t, hogy létrehozza azt.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Ez a parancs egy egyszerű szolgáltatás adatait adja ki, például:
   
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
   
   Jegyezze fel az **appAzonosítót** és a **jelszómezőket,** mivel ezeket a paramétereket a következő lépésekben fogja használni. 

1. A parancssorból keresse meg azt a mappát, ahol az Azure Kubernetes Engine végrehajtható fájl található. A parancssorban például a következő módon navigálhat a mappába:

   ```cmd
   cd "\aks-engine-v0.36.3-windows-amd64\aks-engine-v0.36.3-windows-amd64"
   ```

1. Nyisson meg egy ön által választott szövegszerkesztőt, és definiáljon egy Resource Manager-sablont, amely az Azure Cosmos DB-api-val telepíti az Azure Kubernetes-fürtöt. Másolja a szövegszerkesztőbe a következő JSON-definíciót, és mentse a fájlt a következőképpen: `apiModel.json`

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

   A JSON/cluster definíciós fájlban a legfontosabb paraméter a **"cosmosEtcd": true**. Ez a paraméter a "masterProfile" tulajdonságok, és azt jelzi, hogy a központi telepítés használata az Azure Cosmos etcd API-t a rendszeres etcd helyett. 

1. Telepítse az Azure Kubernetes-fürtöt, amely az Azure Cosmos DB-t használja a következő paranccsal:

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

   Az Azure Kubernetes Engine egy fürtdefiníciót használ, amely felvázolja az Azure Kubernetes kívánt alakját, méretét és konfigurációját. Számos olyan szolgáltatás létezik, amely a fürtdefiníción keresztül engedélyezhető. Ebben a példában a következő paramétereket fogja használni:

   * **előfizetés-azonosító:** Azure-előfizetés-azonosító, amely az Azure Cosmos DB etcd API engedélyezve van.
   * **ügyfél-azonosító:** Az egyszerű szolgáltatás alkalmazásazonosítója. A `appId` 4.
   * **Ügyféltitok:** Az egyszerű szolgáltatás jelszava vagy egy véletlenszerűen generált jelszó. Ezt az értéket a 4. 
   * **dnsElőtag:** Régió-egyedi DNS-név. Ez az érték az állomásnév részét képezi (a példaértékek- myprod1, staging).
   * **helyszín:**  Az a hely, ahová a fürtöt telepíteni kell, jelenleg csak a "centralus" támogatott.

   > [!Note]
   > Az Azure Cosmos etcd API jelenleg csak a "centralus" régióban telepíthető. 
 
   * **api-modell:** A sablonfájl teljesen minősített elérési útja.
   * **erő-felülírás:** Ezzel a beállítással automatikusan felülírhatja a kimeneti könyvtárban lévő meglévő fájlokat.
 
   A következő parancs egy példa telepítést mutat be:

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

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

A sablon központi telepítése több percet vesz igénybe. A telepítés sikeres befejezése után a parancssorban a következő kimenet jelenik meg:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Az erőforráscsoport most már olyan erőforrásokat tartalmaz, mint például a virtuális gép, az Azure Cosmos-fiók(etcd API), a virtuális hálózat, a rendelkezésre állási készlet és a Kubernetes-fürt által igényelt egyéb erőforrások. 

Az Azure Cosmos-fiók neve megegyezik a megadott DNS-előtag k8s hozzáfűzve. Az Azure Cosmos-fiók automatikusan kilesz építve egy **EtcdDB** nevű adatbázissal és egy **EtcdData**nevű tárolóval. A tartály tárolja az összes etcd kapcsolódó adatokat. A tároló egy bizonyos számú kérelemegységgel van kiépítve, és a számítási feladatok alapján [skálázhatja (növelheti/csökkentheti) az átviteli értéket.](scaling-throughput.md) 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [dolgozhat az Azure Cosmos-adatbázissal, -tárolókkal és -elemekkel](databases-containers-items.md)
* További információ a [kiépített átviteli-átviteli költségek optimalizálásáról](optimize-cost-throughput.md)

