---
title: Visual Studio Azure-beli erőforráscsoport-projektek létrehozása & üzembe helyezése
description: A Visual Studióval hozzon létre egy Azure erőforráscsoport-projektet, és telepítse az erőforrásokat az Azure-ba.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: tomfitz
ms.openlocfilehash: ae7ff9370bd289dfdec578e6daeb471bbd53a072
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597660"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással

A Visual Studio alkalmazással olyan projekteket hozhat létre, amelyekkel telepíthető az infrastruktúra és kód az Azure rendszerébe. Telepítheti például a webhelyhez tartozó webállomást, webhelyet és kódot. A Visual Studio számos különböző kezdősablont kínál a gyakori forgatókönyvek telepítéséhez. Ebben a cikkben egy webalkalmazást telepítünk.

Ez a cikk bemutatja, hogyan használható [a Visual Studio 2019-es vagy újabb verziója a telepített Azure-beli fejlesztési és ASP.net számítási feladatokkal](/visualstudio/install/install-visual-studio?view=vs-2019). Ha a Visual Studio 2017-et használja, a felhasználói élmény nagyjából azonos.

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Ebben a szakaszban egy Azure erőforráscsoport-projektet hoz létre egy **webalkalmazás** -sablonnal.

1. A Visual Studióban válassza a **fájl** >**új** >**projekt**lehetőséget.
1. Válassza ki az **Azure erőforráscsoport** -projekt sablonját, és kattintson a **Tovább gombra**.

    ![Projekt létrehozása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Adjon nevet a projektnek. A többi alapértelmezett beállítás valószínűleg megfelelő, de tekintse át őket, hogy működjenek a környezetében. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    ![Projekt létrehozása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Válassza ki az Azure Resource Managerbe telepíteni kívánt sablont. Figyelje meg, hogy a telepíteni kívánt projekt típusától függően számos különböző lehetőség áll rendelkezésre. Ehhez a cikkhez válassza a **webalkalmazás** -sablont, és **az OK gombra**.

    ![Sablon kiválasztása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    A kiválasztott sablon csak egy kiindulási pont. A forgatókönyvnek való megfelelés érdekében hozzáadhat és eltávolíthat erőforrásokat.

1. A Visual Studio létrehoz egy erőforráscsoport-telepítési projektet a webalkalmazáshoz. A projekt fájljainak megtekintéséhez tekintse meg a telepítési projektben található csomópontot.

    ![Csomópontok megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    A webalkalmazás-sablon választása óta a következő fájlok láthatók:

   | Fájlnév | Leírás |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |PowerShell-parancsfájl, amely PowerShell-parancsokat futtat az Azure Resource Manager üzembe helyezéséhez. A Visual Studio ezt a PowerShell-parancsfájlt használja a sablon üzembe helyezéséhez. |
   | Webhely. JSON |Az Azure szolgáltatásban telepíteni kívánt infrastruktúrát, valamint a telepítés során megadható paramétereket meghatározó Resource Manager-sablon. A telepített erőforrások közti függőségeket is meghatározza, így a Resource Manager megfelelő sorrendben telepíti azokat. |
   | Webhely. Parameters. JSON |Paraméterfájl, amely a sablonhoz szükséges értékeket tartalmazza. Megadhat paraméterértékeket, amelyekkel testre szabhatóak az egyes telepítések. |

    Mindegyik erőforráscsoport-telepítési projekt tartalmazza ezeket az alapvető fájlokat. Más projektek további fájlokat is tartalmazhatnak egyéb funkciók támogatásához.

## <a name="customize-resource-manager-template"></a>Resource Manager-sablon testreszabása

A központi telepítési projektet testreszabhatja úgy, hogy módosítja a telepíteni kívánt erőforrásokat leíró Resource Manager-sablont. A Resource Manager-sablon elemeivel kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

1. A sablonon való működéshez nyissa meg a **webhely. JSON**fájlt.

1. A Visual Studio szerkesztő eszközöket biztosít a Resource Manager-sablon szerkesztéséhez. A **JSON-vázlat** ablak segítségével könnyen áttekinthetőek a sablonban meghatározott elemek.

   ![JSON-körvonal megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Válasszon ki egy elemet a vázlatban a sablon adott részének megadásához.

   ![Navigáljon a JSON-hoz](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Hozzáadhat egy új erőforrást a JSON-vázlat ablak tetején található **Erőforrás hozzáadása** gomb kiválasztásával, vagy kattintson a jobb gombbal az **erőforrások** elemre, és válassza az **Új erőforrás hozzáadása** lehetőséget.

   ![Erőforrás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Válassza ki a **Storage-fiókot** , és adjon meg egy nevet. Olyan nevet adjon meg, amely nem hosszabb 11 karakternél, és csak számokat és kisbetűket tartalmaz.

   ![Tárhely hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Figyelje meg, hogy nem csupán az erőforrás lett hozzáadva, hanem a tárfiók típusának paramétere is, valamint egy változó a tárfiók nevével.

   ![Körvonal megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. A Storage-fiók típusának paraméterét a rendszer előre definiálja az engedélyezett típusokkal és az alapértelmezett típussal. Megtarthatja ezeket az értékeket, vagy módosíthatja őket az adott forgatókönyvnek megfelelően. Ha nem szeretné, hogy bárki **Premium_LRS** tárfiókot helyezzen üzembe a sablonon keresztül, egyszerűen törölje azt az engedélyezett típusok közül.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. A Visual Studio olyan IntelliSense-t is biztosít, amely segít megérteni a sablon szerkesztésekor elérhető tulajdonságokat. Például az App Service-csomag tulajdonságainak szerkesztéséhez lépjen a **HostingPlan** erőforrásra, és adjon meg egy értéket a **properties** elemnél. Figyelje meg, hogy az intellisense megjeleníti az elérhető értékeket, valamint az adott értékek leírását.

   ![IntelliSense megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Beállíthatja a **numberOfWorkers** 1 értékre, és mentheti a fájlt.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Nyissa meg a **webhely. Parameters. JSON** fájlt. A parameters (paraméterek) fájl segítségével adja át az értékeket a központi telepítés során, amely testreszabja az üzembe helyezett erőforrást. Adja meg az üzemeltetési csomag nevét, és mentse a fájlt.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Projekt üzembe helyezése az Azure-ban

Most már készen áll a projekt egy erőforráscsoporthoz való üzembe helyezésére.

Alapértelmezés szerint a projektben található PowerShell-szkript (Deploy-AzureResourceGroup. ps1) a AzureRM modult használja. Ha még mindig telepítette a AzureRM modult, és szeretné tovább használni, használhatja ezt az alapértelmezett parancsfájlt. Ezzel a parancsfájllal a Visual Studio felületén üzembe helyezheti a megoldást.

Ha azonban áttelepítette az új az [modult](/powershell/azure/new-azureps-module-az), új parancsfájlt kell hozzáadnia a projekthez. Az az modult használó parancsfájl hozzáadásához másolja a [Deploy-AzTemplate. ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) parancsfájlt, és adja hozzá a projekthez. Ha ezt a parancsfájlt a központi telepítéshez szeretné használni, a Visual Studio telepítési felületének használata helyett egy PowerShell-konzolból kell futtatnia.

Ebben a cikkben mindkét megközelítés látható. Ez a cikk az alapértelmezett parancsfájlra hivatkozik, amely az AzureRM modul parancsfájlja, az új parancsfájl pedig az az Module script.

### <a name="az-module-script"></a>Az Module script

Az az Module script esetében nyisson meg egy PowerShell-konzolt, és futtassa a következő parancsot:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM modul parancsfájlja

A AzureRM modul parancsfájlhoz használja a Visual studiót:

1. Az üzembe helyezési projekt csomópontjának helyi menüjén válassza a **Telepítés** > **Új** lehetőséget.

    ![Új központi telepítési menüelem](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Megjelenik a **Telepítés erőforráscsoportra** párbeszédpanel. Az **Erőforráscsoport** legördülő listából válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat. Válassza az **Üzembe helyezés** lehetőséget.

    ![Üzembe helyezés erőforráscsoport-párbeszédpanelen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. A **Kimenet** ablakban követhető az üzembe helyezés állapota. Az üzembe helyezés befejeztével az utolsó üzenet jelzi az üzembe helyezés sikerességét, a következőhöz hasonló módon:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Üzembe helyezett erőforrások megtekintése

Vizsgáljuk meg az eredményeket.

1. Egy böngészőben nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be a fiókjával. Az erőforráscsoport megtekintéséhez válassza az **Erőforráscsoportok** lehetőséget, valamint az erőforráscsoportot, amelyiken a telepítést végezte.

1. Az összes telepített erőforrás megjelenik. Figyeljen arra, hogy a tárfiók neve nem pontosan az, amit az erőforrás hozzáadásakor megadott. A tárfiók nevének egyedinek kell lennie. A sablon automatikusan egy karakterláncot ad hozzá a megadott névhez, amely egyedi nevet hoz létre.

    ![Erőforrások megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Kód hozzáadása a projekthez

Ezen a ponton az alkalmazás infrastruktúrája már telepítve van, tényleges kód azonban még nincs telepítve a projekttel.

1. Adjon hozzá egy projektet a Visual Studio megoldásához. Kattintson a jobb gombbal a megoldásra, és válassza az **Add** > **New Project** (Hozzáadás – Új projekt) parancsot.

    ![Projekt hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. **ASP.net Core webalkalmazás**hozzáadása.

    ![Webalkalmazás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Adjon nevet a webalkalmazásnak, és válassza a **Létrehozás**lehetőséget.

    ![Webhely neve webalkalmazás](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Válassza a **webalkalmazás** lehetőséget, és **hozzon létre**.

    ![Webalkalmazás kiválasztása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Miután a Visual Studio létrehozta a webalkalmazást, a megoldásban mindkét projekt megjelenik.

    ![Projektek megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Most győződjön meg arról, hogy az erőforráscsoport észleli az új projektet. Térjen vissza az erőforráscsoport-projekthez (ExampleAppDeploy). Kattintson jobb gombbal a **References** (Hivatkozások) elemre és válassza az **Add Reference** (Hivatkozás hozzáadása) lehetőséget.

    ![Hivatkozás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Válassza ki a webalkalmazás-projektet, amelyet létrehozott.

   ![Hivatkozás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Egy hivatkozás hozzáadásával összekapcsolja a webalkalmazás-projektet az erőforráscsoport-projekttel, és automatikusan beállítja néhány tulajdonságot. Ezek a tulajdonságok a hivatkozáshoz tartozó **Properties** (Tulajdonságok) ablakban láthatók. A **Fájl elérési útjának belefoglalása** azt az útvonalat tartalmazza, ahol a csomag létrejött. Jegyezze meg a mappa (ExampleApp) és a fájl (package.zip) nevét. Ismernie kell ezeket az értékeket, mert az alkalmazás üzembe helyezésekor meg kell adni őket paraméterekként.

   ![Lásd: hivatkozás](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Lépjen vissza a sablonhoz (webhely. JSON), és adjon hozzá egy erőforrást a sablonhoz.

    ![Erőforrás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Ez alkalommal válassza a **Web Deploy for Web Apps** (Webalkalmazások webes üzembe helyezése) lehetőséget.

    ![Web Deploy hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Mentse a sablont.

1. A sablonban néhány új paraméter szerepel. Ezek az előző lépésben lettek hozzáadva. A **_artifactsLocation** vagy a **_artifactsLocationSasToken** értékeit nem kell megadnia, mert ezek az értékek automatikusan létrejönnek. A mappát és a fájlnevet azonban a központi telepítési csomagot tartalmazó elérési útra kell beállítania. Ezeknek a paramétereknek a nevei a **PackageFolder** és a **PackageFileName**végződéssel rendelkeznek. A név első része a hozzáadott webes üzembe helyezési erőforrás neve. Ebben a cikkben a **ExampleAppPackageFolder** és a **ExampleAppPackageFileName**nevet kapta.

   Nyissa meg a **webhely. Parameters. JSON** fájlt, és állítsa be ezeket a paramétereket a hivatkozás tulajdonságai között megjelenő értékekre. Állítsa a **ExampleAppPackageFolder** a mappa nevére. Állítsa a **ExampleAppPackageFileName** nevet a zip-fájl nevére.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Kód üzembe helyezése infrastruktúrával

Mivel programkódot adott hozzá a projekthez, az üzemelő példány egy kicsit más. Az üzembe helyezés során a projekthez tartozó összetevők a Resource Manager által elérhető helyre kerülnek. Az összetevők egy Storage-fiókba vannak berendezve.

### <a name="az-module-script"></a>Az Module script

Ha az az Module parancsfájlt használja, egy kis módosítást kell végeznie a sablonban. Ez a szkript egy perjelet szúr be az összetevők helyére, de a sablon nem vár erre a perjelre. Nyissa meg a webhely. JSON fájlt, és keresse meg a MSDeploy-bővítmény tulajdonságait. Rendelkezik egy **packageUri**nevű tulajdonsággal. Távolítsa el az összetevők helye és a csomag mappája közötti perjelet.

Így kell kinéznie:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Figyelje meg, hogy az előző példában nincs `'/',` **paraméterek ("_artifactsLocation")** és **Parameters ("ExampleAppPackageFolder")** között.

Hozza létre újra a projektet. A projekt létrehozása biztosítja, hogy a telepíteni kívánt fájlok hozzá legyenek adva az átmeneti mappához.

Most nyisson meg egy PowerShell-konzolt, és futtassa a következőket:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM modul parancsfájlja

A AzureRM modul parancsfájlhoz használja a Visual studiót:

1. Az újbóli üzembe helyezéshez válassza a **telepítés**lehetőséget, majd a korábban üzembe helyezett erőforráscsoportot.

    ![Projekt újbóli üzembe helyezése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Válassza ki azt a Storage-fiókot, amelyet az adott erőforráscsoporthoz telepített az összetevő- **tárolási fiókhoz**.

   ![A web Deploy újratelepítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Webalkalmazás megtekintése

1. Az üzembe helyezés befejeztével válassza ki a webalkalmazást a portálon. Válassza ki az URL-t a hely böngészéséhez.

   ![Webhely tallózása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Láthatja, hogy az alapértelmezett ASP.NET-alkalmazást sikeresen üzembe helyezte.

   ![Telepített alkalmazás megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Műveleti irányítópult hozzáadása

Nem csak a Visual Studio felületén keresztül elérhető erőforrásokat használhatja. Az üzemelő példány testreszabásához adjon egyéni erőforrást a sablonhoz. Az erőforrás hozzáadásának megjelenítéséhez műveleti irányítópultot kell hozzáadnia az üzembe helyezett erőforrás kezelése érdekében.

1. Nyissa meg a webhely. JSON fájlt, és adja hozzá a következő JSON-t a Storage-fiók erőforrása után, de az erőforrások szakasz záró `]` előtt.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. A projekt újbóli üzembe helyezése.

1. Az üzembe helyezés befejezése után tekintse meg az irányítópultot a portálon. Válassza ki az **irányítópultot** , és válassza ki az üzembe helyezett elemet.

   ![Egyéni irányítópult](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. Megjelenik a testreszabott irányítópult.

   ![Egyéni irányítópult](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

RBAC-csoportokkal kezelheti az irányítópult hozzáférését. Az irányítópult kinézetét is testreszabhatja az üzembe helyezése után. Ha azonban újból üzembe helyezi az erőforráscsoportot, az irányítópult visszaáll a sablonban szereplő alapértelmezett állapotba. Az irányítópultok létrehozásával kapcsolatos további információkért lásd az [Azure-irányítópultok szoftveres létrehozásával](../azure-portal/azure-portal-dashboards-create-programmatically.md) foglalkozó témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal a bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

1. Válassza ki az erőforráscsoport nevét.

1. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre és helyezhet üzembe sablonokat a Visual Studio használatával. A sablonok fejlesztésével kapcsolatos további tudnivalókért tekintse meg az új kezdő oktatóanyag-sorozatot:

> [!div class="nextstepaction"]
> [Kezdő oktatóanyagok](./template-tutorial-create-first-template.md)
