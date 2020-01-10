---
title: Azure Service Fabric alkalmazás-erőforrás modell
description: Ez a cikk áttekintést nyújt az Azure Service Fabric-alkalmazások kezeléséről Azure Resource Manager
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 44073967730d95e803f57d504aa9d8c529250a8d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751194"
---
# <a name="service-fabric-application-resource-model"></a>Alkalmazás-erőforrás modell Service Fabric

Azt javasoljuk, hogy az Service Fabric alkalmazások Azure Resource Manager használatával legyenek telepítve a Service Fabric-fürtön. Ez a módszer lehetővé teszi a JSON-alkalmazások és-szolgáltatások leírását, és azokat ugyanabban a Resource Manager-sablonban telepíteni, mint a fürtöt. Az alkalmazások PowerShell vagy Azure CLI használatával történő üzembe helyezése és kezelése helyett nem kell megvárnia, hogy a fürt készen álljon. Az alkalmazásregisztráció, -kiépítés és -üzembehelyezés folyamata mind egy lépésben valósulhat meg. Ez a fürtbéli alkalmazás-életciklusok kezelésének ajánlott eljárása. További információt az [ajánlott eljárások: az infrastruktúra mint kód](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)című témakörben talál.

Ha alkalmazható, az alkalmazásokat Resource Manager-erőforrásként kezelheti a fejlesztéshez:

* Naplózási nyomvonal: a Resource Manager naplózza az összes műveletet, és részletesen *naplózza a tevékenységeket* , amelyek segítségével nyomon követheti az ezen alkalmazások és a fürtön történt módosításokat.
* Szerepköralapú hozzáférés-vezérlés: a fürtökhöz való hozzáférés kezelése, valamint a fürtön üzembe helyezett alkalmazások ugyanazon Resource Manager-sablonnal is elvégezhetők.
* A Azure Resource Manager (a Azure Portal használatával) a fürt és a kritikus fontosságú alkalmazások központi telepítésének kezeléséhez egy-egy stop-shop lesz.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Alkalmazási életciklus Service Fabric Azure Resource Manager

Ebből a dokumentumból megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
>
> * Alkalmazás-erőforrások központi telepítése Azure Resource Manager használatával
> * Alkalmazás-erőforrások frissítése Azure Resource Manager használatával
> * Alkalmazás-erőforrások törlése

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Alkalmazás-erőforrások központi telepítése Azure Resource Manager használatával

Ha egy alkalmazást és annak szolgáltatásait az Azure Resource Manager alkalmazás-erőforrás modell használatával szeretné üzembe helyezni, az alkalmazás kódját kell megadnia, fel kell töltenie a csomagot, majd a csomag helyét egy Azure Resource Manager sablonban kell megadnia alkalmazásként. erőforrás. További információkért tekintse meg [az alkalmazás csomagját](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).

Ezután hozzon létre egy Azure Resource Manager sablont, frissítse a paramétereket tartalmazó fájlt az alkalmazás részleteivel, és telepítse azt a Service Fabric-fürtön. Tekintse meg a mintákat [itt](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Storage-fiók létrehozása

Az alkalmazások Resource Manager-sablonból való üzembe helyezéséhez Storage-fiók szükséges az alkalmazás rendszerképének előkészítéséhez. Újra felhasználhat egy meglévő Storage-fiókot, vagy létrehozhat egy új Storage-fiókot az alkalmazások előkészítéséhez. Ha meglévő Storage-fiókot szeretne használni, akkor kihagyhatja ezt a lépést. 

![Create a storage account][CreateStorageAccount]

### <a name="configure-storage-account"></a>Storage-fiók konfigurálása

A Storage-fiók létrehozása után létre kell hoznia egy BLOB-tárolót, amelyben az alkalmazások elhelyezhetők. A Azure Portal Navigáljon arra a Storage-fiókra, amelyet az alkalmazásai tárolására szeretne használni. Válassza a **Blobok** panelt, majd kattintson a **tároló hozzáadása** gombra. A fürtben lévő erőforrások biztonságossá tétele a nyilvános hozzáférési szint magánjellegűre állításával lehetséges. A hozzáférés több módon is megadható:

* [Blobokhoz és várólistákhoz való hozzáférés engedélyezése Azure Active Directory](../storage/common/storage-auth-aad-app.md)
* [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure Portalon](../storage/common/storage-auth-aad-rbac-portal.md)
* [Hozzáférés delegálása közös hozzáférésű aláírással (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature
)

 Ebben a példában a Blobok névtelen olvasási hozzáférését fogjuk használni.

![BLOB létrehozása][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Alkalmazás előkészítése egy Storage-fiókban

Az alkalmazás üzembe helyezése előtt a blob Storage-ban kell lennie. Ebben az oktatóanyagban manuálisan hozunk létre egy alkalmazáscsomag-csomagot, azonban ez a lépés automatizálható.  További információkért tekintse meg [az alkalmazás csomagját](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). A következő lépésekben a rendszer a [szavazási minta alkalmazást](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) fogja használni.

1. A Visual Studióban kattintson a jobb gombbal a szavazási projektre, és válassza a csomag lehetőséget.
Alkalmazáscsomag ![][PackageApplication]  
2. Nyissa meg az imént létrehozott **.\service-Fabric-DotNet-quickstart\Voting\pkg\Debug** könyvtárat, és zip-fájlt egy **szavazás. zip** nevű fájlba, hogy a ApplicationManifest. xml fájl a zip-fájl gyökerében legyen.  
![zip-alkalmazás][ZipApplication]  
3. Nevezze át a fájlt. zip kiterjesztését a **. sfpkg**névre.
4. A Azure Portal a Storage-fiók **alkalmazások** tárolójában kattintson a **feltöltés** és a **szavazás. sfpkg**feltöltése elemre.  
Alkalmazáscsomag feltöltése ![][UploadAppPkg]

Az alkalmazás most már előkészítés alatt áll. Most már készen áll a Azure Resource Manager sablon létrehozására az alkalmazás üzembe helyezéséhez.

### <a name="create-the-azure-resource-manager-template"></a>A Azure Resource Manager sablon létrehozása

A minta alkalmazás az alkalmazás telepítéséhez használható [Azure Resource Manager sablonokat](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) tartalmaz. A sablonfájlok neve **UserApp. JSON** és **UserApp. Parameters. JSON**.

> [!NOTE]
> A **UserApp. Parameters. JSON** fájlt frissíteni kell a fürt nevével.
>
>

| Paraméter              | Leírás                                 | Példa                                                      | Megjegyzések                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Annak a fürtnek a neve, amelyre telepítve van | SF – cluster123                                                |                                                              |
| alkalmazás            | Az alkalmazás neve                 | Voting                                                       |
| applicationTypeName    | Az alkalmazás típusának neve           | VotingType                                                   | Meg kell egyeznie a ApplicationManifest. xml fájl tartalmával                 |
| ApplicationTypeVersion | Az alkalmazás típusának verziója         | 1.0.0                                                        | Meg kell egyeznie a ApplicationManifest. xml fájl tartalmával                 |
| serviceName            | A szolgáltatáshoz tartozó szolgáltatás neve         | Szavazás ~ VotingWeb                                             | A formátumának ApplicationName ~ ServiceType kell lennie            |
| ServiceTypeName        | A szolgáltatás típusának neve                | VotingWeb                                                    | Meg kell egyeznie a ServiceManifest. xml fájl tartalmával                 |
| appPackageUrl          | Az alkalmazás blob Storage URL-címe     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Az alkalmazáscsomag blob Storage-beli URL-címe (az alábbi beállításhoz szükséges eljárást lásd alább) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Az alkalmazás központi telepítéséhez futtassa a New-AzResourceGroupDeployment alkalmazást a fürtöt tartalmazó erőforráscsoporthoz való üzembe helyezéshez.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Service Fabric alkalmazás frissítése Azure Resource Manager használatával

A Service Fabric-fürtön már üzembe helyezett alkalmazások a következő okokból lesznek frissítve:

1. Új szolgáltatás kerül be az alkalmazásba. A Service-manifest. XML és a Application-manifest. xml fájlhoz hozzá kell adni egy szolgáltatási definíciót. Ezután az alkalmazás új verziójának megjelenítéséhez frissítenie kell az alkalmazás típusának verzióját a 1.0.0-ról a 1.0.1 [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)fájlra.

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

2. Egy meglévő szolgáltatás új verziója lesz hozzáadva az alkalmazáshoz. Ez magában foglalja az alkalmazás kódjának változásait és az alkalmazás típusú verzió és név frissítését.

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Alkalmazás-erőforrások törlése

Az alkalmazás-erőforrás-modell használatával központilag telepített alkalmazások Azure Resource Manager törölhetők a fürtből az alábbi lépések segítségével

1) Erőforrás-azonosító lekérése az alkalmazáshoz a [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)használatával:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

2) Az alkalmazás erőforrásainak törlése a [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)használatával:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Következő lépések

Információk beolvasása az alkalmazás-erőforrás modelljéről:

* [Alkalmazás modellezése Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric alkalmazás-és szolgáltatás-jegyzékfájlok](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Lásd még:

* [Ajánlott eljárások](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Alkalmazások és szolgáltatások kezelése Azure-erőforrásként](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
