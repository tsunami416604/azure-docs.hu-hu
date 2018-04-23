---
title: A Device Provisioning Service üzembe helyezése Azure Resource Manager-sablonnal | Microsoft Docs
description: Azure rövid útmutató – Az Azure IoT Hub Device Provisioning Service üzembe helyezése
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 02/26/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 8fc014efab898bf0ab3d8cd5eaa83dce53ee275b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Az IoT Hub Device Provisioning Service üzembe helyezése Azure Resource Manager-sablonnal

Az [Azure Resource Managerrel](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) programozott módon üzembe helyezheti az eszközök regisztrációjához szükséges Azure felhőbeli erőforrásokat. Az alábbi lépések IoT Hub és új IoT Hub Device Provisioning Service létrehozását, valamint a két szolgáltatás Azure Resource Manager-sablonnal végzett összekapcsolását ismertetik. Ez a rövid útmutató az [Azure CLI 2.0-s](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli) verzióját használja az erőforráscsoport létrehozásához és a sablon üzembe helyezéséhez szükséges programozási lépések elvégzéséhez, de használhatja helyette az [Azure Portalt](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-portal), a [PowerShellt](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy), illetve a .NET-et, a Rubyt vagy más programozási nyelveket is. 


## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- A rövid útmutatóhoz az Azure CLI helyi futtatása szükséges. Az Azure CLI 2.0-s vagy újabb verzióját kell telepíteni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Bejelentkezés az Azure-ba és erőforráscsoport létrehozása

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login parancsot][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat][lnk-az-account-command]:
    
    ```azurecli
    az account list 
    ```

    Az alábbi parancs segítségével válassza ki azt az előfizetést, amelyet az IoT Hub létrehozásához szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Amikor Azure felhőbeli erőforrásokat, például IoT Hubokat és regisztrációs szolgáltatásokat hoz létre, azokat egy erőforráscsoportban kell létrehozni. Használhat meglévő erőforráscsoportot, vagy futtathatja a következő [parancsot erőforráscsoport létrehozásához][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
    >
    >

## <a name="create-a-resource-manager-template"></a>Resource Manager-sablon létrehozása

JSON-sablon használatával létrehozhat egy regisztrációs szolgáltatást és egy ahhoz kapcsolt IoT Hubot az erőforráscsoportban. Az Azure Resource Manager-sablonok segítségével módosíthatja a meglévő regisztrációs szolgáltatásokat vagy IoT Hubot.

1. Szövegszerkesztőben hozzon létre egy **template.json** nevű Azure Resource Manager-sablont a következő váztartalommal. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Cserélje le a **parameters** szakaszt a következő tartalomra. A parameters szakasz határozza meg, hogy mely paraméterek adhatók át másik fájlból. Ez a szakasz adja meg a létrehozni kívánt IoT Hub és regisztrációs szolgáltatás nevét. Emellett az IoT Hub és a regisztrációs szolgáltatás helyét is meghatározza. Az értékek azon Azure-régiókra korlátozódnak, amelyek támogatják az IoT Hubok és a regisztrációs szolgáltatások használatát. A Device Provisioning Service által támogatott helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs segítségével kérheti le, vagy az [Azure állapotlapján](https://azure.microsoft.com/status/) kereshet a „Device Provisioning Service” kifejezésre.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Cserélje le a **variables** szakaszt a következő tartalomra. Ez a szakasz az IoT Hub kapcsolati karakterláncának későbbi létrehozásához szükséges értékeket határozza meg, amely a regisztrációs szolgáltatás és az IoT Hub összekapcsolásához szükséges. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. IoT Hub létrehozásához adja hozzá a következő sorokat a **resources** gyűjteményéhez. A JSON adja meg az IoT Hub létrehozásához szükséges minimális tulajdonságokat. A **name** és a **location** tulajdonságot paraméterként átadja a rendszer. Az IoT Hub a sablonokban megadható tulajdonságaival kapcsolatban bővebb információt a [Microsoft.Devices/IotHubs sablonreferenciában](https://docs.microsoft.com/en-us/azure/templates/microsoft.devices/iothubs) talál.

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. A regisztrációs szolgáltatás létrehozásához adja hozzá a következő sorokat a **resources** gyűjteményben az IoT Hub specifikációja után. A regisztrációs szolgáltatás **name** és **location** tulajdonságát paraméterekben adja át a rendszer. Az **iotHubs** gyűjteményben adja meg azokat az IoT Hubokat, amelyeket a regisztrációs szolgáltatáshoz szeretne kapcsolni. Meg kell adnia legalább az összekapcsolni kívánt IoT Hubok **connectionString** és **location** tulajdonságát. Ezenkívül az egyes IoT Hubokon beállíthat olyan tulajdonságokat, mint az **allocationWeight** és az **applyAllocationPolicy**, a regisztrációs szolgáltatáson pedig olyan tulajdonságokat, mint az **allocationPolicy** vagy az **authorizationPolicies**. További tudnivalókért lásd a [Microsoft.Devices/provisioningServices sablonreferenciát](https://docs.microsoft.com/en-us/azure/templates/microsoft.devices/provisioningservices).

   A **dependsOn** tulajdonsággal biztosítható, hogy a Resource Manager a regisztrációs szolgáltatás létrehozása előtt hozza létre az IoT Hubot. A sablonhoz az IoT Hub kapcsolati karakterláncában meg kell adni a regisztrációs szolgáltatással való kapcsolatot, ezért elsőként a hubot és annak kulcsait kell létrehozni. A sablon a kapcsolati karakterlánc létrehozásához olyan függvényeket használ, mint a **concat** és a **listKeys**. További tudnivalókért tekintse meg [az Azure Resource Manager-sablonok függvényeiről](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-functions) szóló cikket.

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Mentse a sablonfájlt. Az elkészült sablonnak a következőhöz kell hasonlítania:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Resource Manager-paraméterfájl létrehozása

Az előző lépésben meghatározott sablon paraméterekkel adja meg az IoT Hub és a regisztrációs szolgáltatás nevét, illetve a létrehozásuk helyét (az Azure-régiót). Ezeket a paramétereket egy külön fájlban adja át. Ez lehetővé teszi, hogy ugyanazt a sablont több telepítéshez is felhasználhassa. A paraméterfájl létrehozásához kövesse az alábbi lépéseket:

1. Szövegszerkesztőben hozzon létre egy **parameters.json** nevű Azure Resource Manager-paraméterfájlt a következő váztartalommal: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Adja hozzá az **iotHubName** értéket a paraméterek szakaszához. Ha módosítja a nevet, ügyeljen rá, hogy az megfeleljen az IoT Hubra vonatkozó elnevezési szabályoknak. A név 3–50 karakter hosszúságú lehet, és csak kis- és nagybetűs alfanumerikus karaktereket, illetve kötőjelet (-) tartalmazhat. 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Adja hozzá a **provisioningServiceName** értéket a paraméterek szakaszához. Ha módosítja a nevet, ügyeljen rá, hogy az megfeleljen az IoT Hub Device Provisioning Service-re vonatkozó elnevezési szabályoknak. A név 3–64 karakter hosszúságú lehet, és csak kis- és nagybetűs alfanumerikus karaktereket, illetve kötőjelet (-) tartalmazhat.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Adja hozzá a **hubLocation** értéket a paraméterek szakaszához. Ez az érték határozza meg az IoT Hub és a regisztrációs szolgáltatás helyét. Az értéknek egyeznie kell a sablonfájl paraméterdefinícióiban szereplő **allowedValues** gyűjteményben megadott helyek egyikével. Ebben a gyűjteményben az értékek azon Azure-helyekre korlátozódnak, amelyek támogatják az IoT Hubok és a regisztrációs szolgáltatások használatát. A Device Provisioning Service által támogatott helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs segítségével kérheti le, vagy az [Azure állapotlapján](https://azure.microsoft.com/status/) kereshet a „Device Provisioning Service” kifejezésre.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Mentse a fájlt. 


> [!IMPORTANT]
> Az IoT Hub és a regisztrációs szolgáltatás is DNS-végpontként nyilvánosan észlelhető lesz, ezért ügyeljen arra, hogy az elnevezésükkor ne adjon meg bizalmas adatokat.
>

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A következő Azure CLI-parancsokkal helyezheti üzembe a sablonokat és ellenőrizheti az üzembe helyezést.

1. A sablon üzembe helyezéséhez futtassa a következő [parancsot az üzembe helyezés elindításához](https://docs.microsoft.com/en-us/cli/azure/group/deployment?view=azure-cli-latest#az_group_deployment_create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   A kimenetben ellenőrizze, hogy a **provisioningState** tulajdonság értéke „Succeeded”-e. 

   ![Regisztrációs kimenet](./media/quick-setup-auto-provision-rm/output.png) 


2. Az üzembe helyezés ellenőrzéséhez futtassa az alábbi [parancsot az erőforrások megjelenítéséhez](https://docs.microsoft.com/en-us/cli/azure/resource?view=azure-cli-latest#az_resource_list), és a kimenetben keresse meg az új regisztrációs szolgáltatást és az IoT Hubot:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, az Azure CLI használatával [törölhet egyedi erőforrásokat][lnk-az-resource-command] (például egy IoT Hubot vagy regisztrációs szolgáltatást), vagy törölhet egy erőforráscsoportot és az ahhoz tartozó összes erőforrást.

A regisztrációs szolgáltatás törléséhez futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
IoT Hub törléséhez futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa a következő parancsot:

```azurecli
az group delete --name {your resource group name}
```

Erőforráscsoportokat és egyedi erőforrásokat az Azure Portal, a PowerShell, a REST API-k, illetve az Azure Resource Managerhez vagy az IoT Hub Device Provisioning Service-hez közzétett támogatott platform SDK-k használatával is törölhet.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban IoT Hubot és eszközkiépítési szolgáltatás példányt helyezett üzembe, és csatolta a két erőforrást. Ha szimulált eszköz kiépítéséhez szeretné használni ezt a beállítást, folytassa a szimulált eszköz létrehozásának rövid útmutatójával.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
