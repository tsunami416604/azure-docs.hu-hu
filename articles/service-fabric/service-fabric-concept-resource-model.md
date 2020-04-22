---
title: Azure Service Fabric alkalmazáserőforrás-modell
description: Ez a cikk áttekintést nyújt az Azure Resource Fabric-alkalmazások azure Resource Manager használatával történő kezeléséről.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 7a9f59e3e44d3302ac19c7a9e7e77beb51947ce4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682643"
---
# <a name="service-fabric-application-resource-model"></a>Service Fabric alkalmazáserőforrás-modell

Több lehetősége van az Azure Service Fabric-alkalmazások üzembe helyezésére a Service Fabric-fürtön. Az Azure Resource Manager használatát javasoljuk. Ha erőforrás-kezelőt használ, leírhatja az alkalmazásokat és szolgáltatásokat a JSON-ban, majd telepítheti őket ugyanabban az Erőforrás-kezelő sablonban, mint a fürtben. A PowerShell vagy az Azure CLI használatával ellentétben az alkalmazások üzembe helyezése és kezelése esetén az Erőforrás-kezelő használata esetén nem kell megvárnia, hogy a fürt készen álljon; az alkalmazások regisztrációja, kiépítése és üzembe helyezése egy lépésben történhet. Az Erőforrás-kezelő használata a legjobb módja az alkalmazás életciklusának kezelésére a fürtben. További információ: [Gyakorlati tanácsok: Infrastruktúra kódként](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources).

Az alkalmazások erőforrásként történő kezelése az Erőforrás-kezelőben segíthet az alábbi területeken:

* Naplózási nyomvonal: Az Erőforrás-kezelő minden műveletet naplóz, és részletes tevékenységnaplót vezet. A tevékenységnapló segítségével nyomon követheti az alkalmazásokon és a fürtön végrehajtott módosításokat.
* Szerepköralapú hozzáférés-vezérlés: Ugyanazzal az Erőforrás-kezelő sablonnal kezelheti a fürtön telepített fürtökhöz és alkalmazásokhoz való hozzáférést.
* Felügyeleti hatékonyság: Az Erőforrás-kezelő használatával egyetlen helyet (az Azure Portalon) kezelheti a fürt és a kritikus alkalmazások központi telepítések.

Ebben a dokumentumban megtudhatja, hogyan:

> [!div class="checklist"]
>
> * Alkalmazás-erőforrások üzembe helyezése az Erőforrás-kezelő használatával.
> * Az alkalmazás-erőforrások frissítése az Erőforrás-kezelő használatával.
> * Alkalmazás-erőforrások törlése.

## <a name="deploy-application-resources"></a>Alkalmazás-erőforrások telepítése

Az alkalmazás és szolgáltatásainak az Erőforrás-kezelő alkalmazás erőforrás-modelljének használatával történő üzembe helyezéséhez a következő magas szintű lépéseket kell tennie:
1. Csomagolja be az alkalmazás kódját.
1. Töltse fel a csomagot.
1. Hivatkozzon a csomag helyére egy Erőforrás-kezelő sablonban alkalmazás-erőforrásként. 

További információért tekintse meg az Alkalmazás csomagja című [nézetet.](service-fabric-package-apps.md#create-an-sfpkg)

Ezután hozzon létre egy Erőforrás-kezelő sablont, frissítse a paraméterfájlt az alkalmazás részleteivel, és telepítse a sablont a Service Fabric-fürtön. [Vizsgálja meg a mintákat.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Ha egy alkalmazást erőforrás-kezelő sablonból szeretne telepíteni, tárfiókkal kell rendelkeznie. A tárfiók az alkalmazáslemezkép színpadra. 

Felhasználhatja újra a meglévő tárfiókot, vagy létrehozhat egy új tárfiókot az alkalmazások előkészítéséhez. Ha meglévő tárfiókot használ, kihagyhatja ezt a lépést. 

![Tárfiók létrehozása][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>A tárfiók konfigurálása

A tárfiók létrehozása után hozzon létre egy blob tárolót, ahol az alkalmazások at lehet előkészíteni. Az Azure Portalon nyissa meg az Azure Storage-fiókot, ahol az alkalmazásokat szeretné tárolni. Válassza **a Blobok** > **tároló hozzáadása lehetőséget.** 

A fürt erőforrásai a nyilvános hozzáférési szint **privátra**állításával biztosíthatók. A hozzáférést többféleképpen is megadhatod:

* Engedélyezze a hozzáférést a blobokhoz és várólistákhoz az [Azure Active Directory](../storage/common/storage-auth-aad-app.md)használatával.
* Hozzáférést biztosít az Azure blob- és várólista-adatokhoz az RBAC használatával [az Azure Portalon.](../storage/common/storage-auth-aad-rbac-portal.md)
* Hozzáférés delegálása [megosztott hozzáférésű aláírással](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

A következő képernyőképen található példa névtelen olvasási hozzáférést használ a blobokhoz.

![Blob létrehozása][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Az alkalmazás színpadra lépésa a tárfiókban

Egy alkalmazás üzembe helyezése előtt az alkalmazást a blob storage-ban kell elhelyeznie. Ebben az oktatóanyagban manuálisan hozzuk létre az alkalmazáscsomagot. Ne feledje, hogy ez a lépés automatizálható. További információt az [Alkalmazás csomagolása](service-fabric-package-apps.md#create-an-sfpkg)című témakörben talál. 

Ebben az oktatóanyagban a [Szavazás mintaalkalmazást](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)használjuk.

1. A Visual Studio programban kattintson a jobb gombbal a **Szavazás projektre,** majd válassza **a Csomag parancsot.**

   ![Csomag alkalmazás][PackageApplication]  
1. Nyissa meg a *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug* könyvtárat. Zip a tartalmát egy fájl nevű *Voting.zip*. Az *ApplicationManifest.xml* fájlnak a zip-fájl gyökérében kell lennie.

   ![Zip alkalmazás][ZipApplication]  
1. Nevezze át a fájlt a kiterjesztés .zip-ről *.sfpkg-ra*történő módosításához.

1. Az Azure Portalon a tárfiók **alkalmazástárolójában** válassza a **Feltöltés**lehetőséget, majd töltse fel **a Voting.sfpkg fájlt.** 

   ![Alkalmazáscsomag feltöltése][UploadAppPkg]

Most az alkalmazás most van előkészítve, és létrehozhatja az erőforrás-kezelő sablont az alkalmazás üzembe helyezéséhez.

### <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

A mintaalkalmazás [az Azure Resource Manager-sablonokat](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) tartalmazza, amelyek segítségével telepítheti az alkalmazást. A sablonfájl *nevei: UserApp.json* és *UserApp.Parameters.json*.

> [!NOTE]
> A *UserApp.Parameters.json* fájlt frissíteni kell a fürt nevével.
>
>

| Paraméter              | Leírás                                 | Példa                                                      | Megjegyzések                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Annak a fürtnek a neve, amelybe telepíti a központi | sf-cluster123                                                |                                                              |
| Alkalmazás            | Az alkalmazás neve                 | Szavazás                                                       |
| applicationTypeName    | Az alkalmazás típusneve           | Szavazástípusa                                                   | Meg kell egyeznie applicationmanifest.xml                 |
| applicationTypeVersion | Az alkalmazástípus verziója         | 1.0.0                                                        | Meg kell egyeznie applicationmanifest.xml                 |
| szolgáltatásneve            | A szolgáltatás neve         | Szavazás ~ SzavazásWeb                                             | Az ApplicationName~ServiceType formátumban kell lennie.            |
| serviceTypeName        | A szolgáltatás típusneve                | VotingWeb                                                    | Meg kell egyeznie a ServiceManifest.xml fájlnak                 |
| appPackageUrl          | Az alkalmazás blobtárolási URL-címe     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Az alkalmazáscsomag URL-címe a blobstorage-ban (az URL-cím beállításának eljárása a cikk későbbi részében ismertetjük) |

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

Futtassa a **New-AzResourceGroupDeployment** parancsmast az alkalmazás fürterőforrás-csoportba való központi telepítéséhez:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>A Service Fabric-alkalmazás frissítése az Erőforrás-kezelő használatával

Előfordulhat, hogy egy olyan alkalmazást frissít, amely már telepítve van egy Service Fabric-fürtben a következő okok valamelyike miatt:

* Egy új szolgáltatás kerül az alkalmazásba. Szolgáltatásdefiníciót kell hozzáadni *a service-manifest.xml* és *az application-manifest.xml* fájlokhoz, amikor egy szolgáltatást hozzáadnak az alkalmazáshoz. Az alkalmazás új verziójának tükrözéséhez az alkalmazás típusának 1.0.0-ról 1.0.1-re kell módosítania a [UserApp.Parameters.json alkalmazásban:](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)

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

* Egy meglévő szolgáltatás új verziója kerül az alkalmazásba. Ilyenek például az alkalmazáskód módosításai és az alkalmazástípus verziójának és nevének frissítései. A frissítéshez frissítse a UserApp.Parameters.json t a következőkkel:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Alkalmazás-erőforrások törlése

Az erőforrás-kezelőben az alkalmazáserőforrás-modell használatával telepített alkalmazás törlése:

1. A [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) parancsmag segítségével leszeretné késelni az alkalmazás erőforrás-azonosítóját:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Az Alkalmazás-erőforrások törléséhez használja az [Eltávolítás-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) parancsmast:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>További lépések

Az alkalmazáserőforrás-modellről szóló információk:

* [Alkalmazás modellezése a Service Fabricben](service-fabric-application-model.md)
* [A Service Fabric alkalmazás- és szolgáltatásjegyzékei](service-fabric-application-and-service-manifests.md)
* [Gyakorlati tanácsok: Infrastruktúra kódként](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Alkalmazások és szolgáltatások kezelése Azure-erőforrásként](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
