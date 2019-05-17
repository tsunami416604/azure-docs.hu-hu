---
title: Azure-beli Kubernetes az Azure Cosmos DB használatával
description: Ismerje meg, hogyan elindíthat egy Kubernetes-fürtöt az Azure-ban, amely az Azure Cosmos DB (előzetes verzió)
author: SnehaGunda
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: sngun
ms.openlocfilehash: 43aa0956ef1f44fa5705800ff2b424608ec75499
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795615"
---
# <a name="how-to-use-azure-kubernetes-with-azure-cosmos-db-preview"></a>Azure-beli Kubernetes használata az Azure Cosmos DB (előzetes verzió)

A etcd API az Azure Cosmos DB lehetővé teszi, hogy az Azure Cosmos DB az Azure-beli Kubernetes háttérrendszer tárolóként. Az Azure Cosmos DB valósítja meg a etcd protokoll, amely lehetővé teszi a fő csomópont API-kiszolgálók által használt Azure Cosmos DB, ugyanúgy, mint a helyileg telepített etcd elérésére. az Azure Cosmos DB API etcd jelenleg előzetes verzióban érhető el. Az Azure Cosmos etcd API-t a Kubernetes esetében a háttértárban használja, a következő előnyöket kap: 

* Nem kell manuálisan konfigurálhatja és kezelheti a etcd.
* Etcd, Cosmos (99,99 %-os egyetlen régióban, több régióban is 99,999 %-os) által garantált magas rendelkezésre állás.
* Etcd rugalmas méretezhetőségét.
* Biztonságos alapértelmezett & vállalati használatra.
* Iparág élvonalába tartozó, átfogó SLA-k.

Az Azure Cosmos DB API etcd kapcsolatos további információkért tekintse meg a [áttekintése](etcd-api-introduction.md) cikk. Ez a cikk bemutatja, hogyan használható [Azure Kubernetes-motor](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md) (aks-motor) elindíthat egy Kubernetes-fürt által használt Azure-ban [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) egy helyben telepített és konfigurált etcd helyett. 

## <a name="prerequisites"></a>Előfeltételek

1. Telepítse a legújabb verzióját, [Azure CLI-vel](/cli/azure/install-azure-cli?view=azure-cli-latest). Töltse le az Azure CLI-vel adott operációs rendszerhez, és telepítse.

1. Telepítse a [v0.32.3](https://github.com/Azure/aks-engine/releases/tag/v0.32.3) Azure Kubernetes-motor verziója. A telepítési utasításokat a különböző operációs rendszerekhez érhetők el a [Azure Kubernetes-motor](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install-aks-engine) lapot. Egyszerűen lépéssorral **AKS motor telepítése** szakaszában a csatolt dokumentumot. A letöltés után csomagolja ki a zip-fájlt.

   Az Azure Kubernetes-motor (**aks-motor**) hoz létre a Kubernetes-fürtök az Azure-ban Azure Resource Manager-sablonokkal. Aks-motor a bemeneti értéke egy fürt csomagdefiníciós fájl, amely leírja a kívánt fürt, beleértve az orchestrator, a szolgáltatások és az ügynökök. A bemeneti fájlok szerkezete hasonlít a nyilvános API-t az Azure Kubernetes Service-ben.

1. Az Azure Cosmos DB a etcd API jelenleg előzetes verzióban érhető el. Regisztráljon, hogy az előzetes verziót használja: https://aka.ms/cosmosetcdapi-signup. Az űrlap mentése után az előfizetés lesz használata az Azure Cosmos etcd API engedélyezési listán. 

## <a name="deploy-the-cluster-with-azure-cosmos-db"></a>Az Azure Cosmos DB-fürt üzembe helyezése

1. Nyisson meg egy parancssori ablakot, és jelentkezzen be az Azure a következő paranccsal:

   ```azurecli-interactive
   az login 
   ```

1. Ha egynél több előfizetéssel rendelkezik, váltson át az előfizetést, amely már szerepel az engedélyezési listán az Azure Cosmos DB etcd API-t. A következő parancsot a szükséges előfizetésre válthat:

   ```azurecli-interactive
   az account set --subscription "<Name of your subscription>"
   ```
1. Ezután hozzon létre egy új erőforráscsoportot, ahol telepíti az Azure-beli Kubernetes-fürt által igényelt erőforrásokra. Ellenőrizze, hogy a "centralus" régióban hozza létre az erőforráscsoportot. Nem kötelező, azonban kell "centralus" régióban, az erőforráscsoport, az Azure Cosmos etcd API jelenleg érhető el "centralus" régióban kizárólag üzembe helyezéséhez. Ezért érdemes a Kubernetes-fürt kell ugyanarra a szalagra a Cosmos etcd példánnyal rendelkezik:

   ```azurecli-interactive
   az group create --name <Name> --location "centralus"
   ```

1. Ezután egy egyszerű szolgáltatás létrehozása az Azure-beli Kubernetes-fürt számára, hogy képes legyen kommunikálni az erőforrásokat, amelyek ugyanabba az erőforráscsoportba tartozik. Létrehozhat egy egyszerű szolgáltatást ebben a példában létrehozni a CLI lesz, Azure CLI-vel, a PowerShell és az Azure portal használatával.

   ```azurecli-interactive
   az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<Your_Azure_subscription_ID>/resourceGroups/<Your_resource_group_name>"
   ```
   Ez a parancs kimenete például szolgáltatásnévvel, részleteit:
   
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
   
   Jegyezze fel a **appId** és a **jelszó** mezők, ugyanis ezeket a paramétereket a következő lépésben fogja használni. 

1. A parancssorban keresse meg a mappát, ahol az Azure Kubernetes-motor végrehajtható fájl található. Ha például a parancssorban válthat mappába:

   ```cmd
   cd "\aks-engine-v0.32.3-windows-amd64\aks-engine-v0.32.3-windows-amd64"
   ```

1. Nyisson meg egy tetszőleges szövegszerkesztőben, és határozza meg, amely az Azure Kubernetes-fürt az Azure Cosmos DB etcd API üzembe helyezése Resource Manager-sablonnal. A következő JSON-definíciót a szövegszerkesztőben másolja és mentse a fájlt az `apiModel.json`:

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

   A JSON-/ fürt-definíciós fájlt, vegye figyelembe, hogy a kulcs paraméter megadása **"cosmosEtcd": true**. Ez a paraméter "masterProfile" tulajdonságaiban, és azt jelzi, hogy az üzembe helyezés az Azure Cosmos etcd API-val rendszeres etcd helyett. 

1. Helyezze üzembe az Azure Kubernetes-fürtöt, amely az Azure Cosmos DB a következő parancsot:

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

   Az Azure Kubernetes-motor a fürt definíciója, amelyik felvázolja a kívánt alakzat, méretét és az Azure kubernetes konfigurációs használ fel. Nincsenek számos funkciót, amely a fürt definíciója keresztül engedélyezhető. Ebben a példában használja a következő paraméterekkel:

   * **előfizetés-azonosító:** Azure-előfizetés azonosítója, amely rendelkezik az Azure Cosmos DB etcd API engedélyezve van.
   * **client-id:** Az egyszerű szolgáltatás alkalmazásazonosítója. A `appId` kimenetként a 4. lépésben adott vissza.
   * **Client-secret:** A szolgáltatásnév jelszava vagy véletlenszerűen létrehozott jelszót. Ez az érték a 4. lépésben a 'password' paraméter output típusúként adott vissza. 
   * **dnsPrefix:** Egy régióban egyedi DNS-nevet. Ez az érték az állomásnevet (például értékek vannak – myprod1, átmeneti) részét képezik.
   * **Hely:**  Hely, ahol a fürt legyen telepítve, jelenleg csak "centralus" támogatott.

   > [!Note]
   > Az Azure Cosmos etcd API jelenleg csak a "centralus" régióban üzembe helyezéséhez. 
 
   * **api-model:** A sablon fájl teljes elérési útja.
   * **force-overwrite:** Ezzel a beállítással automatikusan felülírja a meglévő fájlokat a kimeneti könyvtárat.
 
   A következő parancsot a központi telepítésre példát látható:

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

A sablon üzembe helyezés több percet vesz igénybe. Miután a telepítés sikeresen befejeződött, a parancsok-parancssorban a következő kimenet jelenik meg:

```cmd
WARN[0006] apimodel: missing masterProfile.dnsPrefix will use "aks-sg-test"
WARN[0006] --resource-group was not specified. Using the DNS prefix from the apimodel as the resource group name: aks-sg-test
INFO[0025] Starting ARM Deployment (aks-sg-test-546247491). This will take some time...
INFO[0587] Finished ARM Deployment (aks-sg-test-546247491). Succeeded
```

Az erőforráscsoport már tartalmaz erőforrást például – virtuális gépet, az Azure Cosmos-fiók (etcd API), virtuális hálózatot, rendelkezésre állási csoport, és egyéb erőforrások a Kubernetes-fürt által igényelt. 

Az Azure Cosmos-fiók neve egyezni fog a megadott DNS-előtag k8s kiegészítve. Az Azure Cosmos-fiók nevű adatbázis automatikusan megkapják **EtcdDB** és a egy tárolót **EtcdData**. A tároló tárolja az összes etcd kapcsolatos adatok. A tároló ki van építve, a kérelemegységek bizonyos számú és is [méretezési (növelheti/csökkentheti) az átviteli sebességet](scaling-throughput.md) a számítási feladatok alapján. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [működnek az Azure Cosmos database, tárolók és elemek](databases-containers-items.md)
* Ismerje meg, hogyan [kiosztott átviteli sebesség a költségek optimalizálása](optimize-cost-throughput.md)

