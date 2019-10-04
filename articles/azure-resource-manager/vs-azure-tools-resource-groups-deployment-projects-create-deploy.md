---
title: Hozzon létre, és üzembe helyezés a Visual Studio Azure resource projektek
description: Az erőforrások üzembe helyezése az Azure és a egy Azure erőforráscsoport-projekt létrehozása a Visual Studio használatával.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: 8677d906375853bdde5c192c86dacc7479f2e31e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311326"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással

A Visual Studio alkalmazással olyan projekteket hozhat létre, amelyekkel telepíthető az infrastruktúra és kód az Azure rendszerébe. Telepíthet például a webkiszolgálót, a webhely és a kód a webhelyhez. A Visual Studio számos különböző kezdősablont kínál a gyakori forgatókönyvek telepítéséhez. Ebben a cikkben webalkalmazás üzembe helyezése.  

Ez a cikk bemutatja, hogyan [2019 vagy újabb, az Azure-fejlesztési és ASP.NET számítási feladatok telepítve a Visual Studio](/visualstudio/install/install-visual-studio?view=vs-2019). Ha a Visual Studio 2017-et használ, a felhasználói élmény a nagymértékben megegyezik.

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Ebben a szakaszban egy Azure erőforráscsoport-projektet hoz létre egy **webalkalmazás** sablont.

1. A Visual Studióban válassza **fájl**, **új**, és **projekt**. Válassza ki a **Azure-erőforráscsoport** projektsablon és **tovább**.

    ![Projekt létrehozása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Nevezze el a projektet. Az alapértelmezett beállítások valószínűleg rendben, de tekintse át, győződjön meg arról, hogy működnek az adott környezetben. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    ![Projekt létrehozása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Válassza ki az Azure Resource Managerbe telepíteni kívánt sablont. Figyelje meg, hogy a telepíteni kívánt projekt típusától függően számos különböző lehetőség áll rendelkezésre. Ehhez a cikkhez válassza ki a **webalkalmazás** sablont és **OK**.

    ![Sablon kiválasztása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    A kiválasztott sablon csak egy kiindulási pont. A forgatókönyvnek való megfelelés érdekében hozzáadhat és eltávolíthat erőforrásokat.

1. A Visual Studio létrehoz egy erőforráscsoport-telepítési projektet a webalkalmazás számára. A projekt fájljainak megtekintéséhez tekintse meg a a telepítési projektben található csomópontot.

    ![zobrazit uzly](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Mivel a webalkalmazás-sablon beállítást választja, az alábbi fájlok láthatóak:

   | Fájlnév | Leírás |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |PowerShell-parancsfájl, amely PowerShell-parancsokat futtat az Azure Resource Manager üzembe helyezéséhez. Visual Studio ezt a PowerShell-parancsfájlt használja a sablon üzembe helyezéséhez. |
   | WebSite.json |Az Azure szolgáltatásban telepíteni kívánt infrastruktúrát, valamint a telepítés során megadható paramétereket meghatározó Resource Manager-sablon. A telepített erőforrások közti függőségeket is meghatározza, így a Resource Manager megfelelő sorrendben telepíti azokat. |
   | WebSite.parameters.json |Paraméterfájl, amely a sablonhoz szükséges értékeket tartalmazza. Megadhat paraméterértékeket, amelyekkel testre szabhatóak az egyes telepítések. |

    Mindegyik erőforráscsoport-telepítési projekt tartalmazza ezeket az alapvető fájlokat. Más projektek további fájlokat is tartalmazhatnak egyéb funkciók támogatásához.

## <a name="customize-resource-manager-template"></a>Resource Manager sablon testreszabása

Testre szabhatja, hogy a telepítési projekteket a telepíteni kívánt erőforrásokat leíró Resource Manager-sablon módosításával. A Resource Manager-sablon elemeivel kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

1. A sablon működéséhez, nyissa meg a **WebSite.json**.

1. A Visual Studio szerkesztő eszközöket biztosít a Resource Manager-sablon szerkesztéséhez. A **JSON-vázlat** ablak segítségével könnyen áttekinthetőek a sablonban meghatározott elemek.

   ![JSON-Vázlat megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Válasszon egy elemet a vázlatban, nyissa meg a sablon adott részére.

   ![Keresse meg a JSON-ban](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. Hozzáadhat egy új erőforrást a JSON-vázlat ablak tetején található **Erőforrás hozzáadása** gomb kiválasztásával, vagy kattintson a jobb gombbal az **erőforrások** elemre, és válassza az **Új erőforrás hozzáadása** lehetőséget.

   ![Erőforrás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Válassza ki **Tárfiók** , és adjon meg egy nevet. Olyan nevet adjon meg, amely nem hosszabb 11 karakternél, és csak számokat és kisbetűket tartalmaz.

   ![Tároló hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. Figyelje meg, hogy nem csupán az erőforrás lett hozzáadva, hanem a tárfiók típusának paramétere is, valamint egy változó a tárfiók nevével.

   ![Vázlat megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. A storage-fiók típusú paraméter előre meghatározott, az engedélyezett típusokkal és a egy alapértelmezett típus. Megtarthatja ezeket az értékeket, vagy módosíthatja őket az adott forgatókönyvnek megfelelően. Ha nem szeretné, hogy bárki **Premium_LRS** tárfiókot helyezzen üzembe a sablonon keresztül, egyszerűen törölje azt az engedélyezett típusok közül.

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

1. A Visual Studio IntelliSense szolgáltatása segítségével megismerheti az elérhető tulajdonságok köre a sablon szerkesztése során. Például az App Service-csomag tulajdonságainak szerkesztéséhez lépjen a **HostingPlan** erőforrásra, és adjon meg egy értéket a **properties** elemnél. Figyelje meg, hogy az intellisense megjeleníti az elérhető értékeket, valamint az adott értékek leírását.

   ![intellisense megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   Beállíthat **numberOfWorkers** 1-re, és mentse a fájlt.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Nyissa meg a **WebSite.parameters.json** fájlt. A paraméterfájl segítségével testre szabhatja az erőforrás üzembe helyezve, üzembe helyezés során értékeket adja át. Adja meg a szolgáltatási csomag nevét, és mentse a fájlt.

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

## <a name="deploy-project-to-azure"></a>Projekt telepítése az Azure-bA

Most már készen áll a projekt telepítése egy erőforráscsoportot.

Alapértelmezés szerint a PowerShell-parancsprogram (üzembe helyezés – AzureResourceGroup.ps1) a projekthez az AzureRM-modul használja. Ha továbbra is az AzureRM-modul telepítve van, és továbbra is használhatja azt szeretné, ezt a parancsfájlt használhatja. Ezzel a parancsprogrammal a megoldás üzembe helyezéséhez használhatja a Visual Studio felületén.

Azonban ha az új áttérését [Az modul](/powershell/azure/new-azureps-module-az), kell új parancsfájl hozzáadása a projekthez. Adja hozzá a Az modult használó parancsfájlok, másolja át a [üzembe helyezés – AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) szkriptet, és adja hozzá a projekthez. Ez a szkript üzembe helyezéshez használandó futtatnia kell egy PowerShell-konzolt, hanem üzembe helyezési felület a Visual Studio használatával.

Ez a cikk mindkét módszerénél láthatók. Ez a cikk az alapértelmezett szkript AzureRM modul szkriptet, és az új szkriptet Az modul hivatkozik.

### <a name="az-module-script"></a>Az modul parancsfájl

A modul Az parancsfájl nyisson meg egy PowerShell-konzolt, és futtassa:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM-modul parancsfájl

Az AzureRM-modul parancsfájl a Visual Studio használata:

1. Az üzembe helyezési projekt csomópontjának helyi menüjén válassza a **Telepítés** > **Új** lehetőséget.

    ![Új üzemelő példány menüelem](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Megjelenik a **Telepítés erőforráscsoportra** párbeszédpanel. Az **Erőforráscsoport** legördülő listából válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat. Válassza az **Üzembe helyezés** lehetőséget.

    ![Üzembe helyezése erőforrás-csoport párbeszédpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. A **Kimenet** ablakban követhető az üzembe helyezés állapota. Az üzembe helyezés befejeztével az utolsó üzenet jelzi az üzembe helyezés sikerességét, a következőhöz hasonló módon:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Üzembe helyezett erőforrások megtekintéséhez

Az eredmények ellenőrizni.

1. Egy böngészőben nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be a fiókjával. Az erőforráscsoport megtekintéséhez válassza az **Erőforráscsoportok** lehetőséget, valamint az erőforráscsoportot, amelyiken a telepítést végezte.

1. Az összes telepített erőforrás megjelenik. Figyeljen arra, hogy a tárfiók neve nem pontosan az, amit az erőforrás hozzáadásakor megadott. A tárfiók nevének egyedinek kell lennie. A sablon automatikusan hozzáad egy karakterlánc, hozzon létre egy egyedi nevet a megadott nevet.

    ![erőforrások megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Kód hozzáadása a projekthez

Ezen a ponton az alkalmazás infrastruktúrája már telepítve van, tényleges kód azonban még nincs telepítve a projekttel.

1. Adjon hozzá egy projektet a Visual Studio megoldásához. Kattintson a jobb gombbal a megoldásra, és válassza az **Add** > **New Project** (Hozzáadás – Új projekt) parancsot.

    ![Adja hozzá a projekthez](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Adjon hozzá egy **ASP.NET Core-webalkalmazás**.

    ![webalkalmazás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Adja meg a webalkalmazás nevét, és válassza ki **létrehozás**.

    ![Name web app](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Válassza ki **webes alkalmazás** és **létrehozása**.

    ![Webalkalmazás kiválasztása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Miután a Visual Studio létrehozta a webalkalmazást, a megoldásban mindkét projekt megjelenik.

    ![projektek megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Most győződjön meg arról, hogy az erőforráscsoport észleli az új projektet. Lépjen vissza az erőforráscsoport-projekt (ExampleAppDeploy). Kattintson jobb gombbal a **References** (Hivatkozások) elemre és válassza az **Add Reference** (Hivatkozás hozzáadása) lehetőséget.

    ![Hivatkozás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Válassza ki a webalkalmazás-projektet, amelyet létrehozott.

   ![Hivatkozás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Hozzáad egy rá mutató hivatkozást, erőforráscsoport-projektben a webalkalmazás-projekthez összekapcsolása, és automatikusan beállítja az egyes tulajdonságait. Ezek a tulajdonságok a hivatkozáshoz tartozó **Properties** (Tulajdonságok) ablakban láthatók. A **Fájl elérési útjának belefoglalása** azt az útvonalat tartalmazza, ahol a csomag létrejött. Jegyezze meg a mappa (ExampleApp) és a fájl (package.zip) nevét. Ismernie kell ezeket az értékeket, mert az alkalmazás üzembe helyezésekor meg kell adni őket paraméterekként.

   ![hivatkozás megtekintése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Lépjen vissza a sablon (WebSite.json), és adjon hozzá egy erőforrást a sablonhoz.

    ![Erőforrás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Ez alkalommal válassza a **Web Deploy for Web Apps** (Webalkalmazások webes üzembe helyezése) lehetőséget. 

    ![Adja hozzá a webalkalmazás üzembe helyezése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   A sablon mentéséhez.

1. A sablon néhány új paraméter találhatók. Addig adták hozzá, az előző lépésben. Adja meg az értékeket nem kell **_artifactsLocation** vagy **_artifactsLocationSasToken** mivel ezeket az értékeket automatikusan jönnek létre. Azonban akkor állítsa be a mappa és fájl nevét a elérési utat, amely a központi telepítési csomag tartalmazza. Ezek a paraméterek nevei végződhet **PackageFolder** és **PackageFileName**. Az a név első része a hozzáadott Web Deploy erőforrás nevét. Ebben a cikkben nevű vagyunk **ExampleAppPackageFolder** és **ExampleAppPackageFileName**. 

   Nyissa meg **Website.parameters.json** , és állítsa be ezeket a paramétereket a hivatkozás tulajdonságai látott értékeket. Állítsa be **ExampleAppPackageFolder** a mappa nevére. Állítsa be **ExampleAppPackageFileName** a zip-fájl nevére.

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

## <a name="deploy-code-with-infrastructure"></a>Az infrastruktúra kód üzembe helyezése

Mivel a projekthez adott kód, az üzembe helyezés, eltérő most. Üzembe helyezés során az összetevők egy helyen, erőforrás-kezelő eléréséhez a projekt előkészítéséhez. Az összetevők elő van készítve a tárfiókhoz.

### <a name="az-module-script"></a>Az modul parancsfájl

Nincs egy kisebb módosítást szeretne elvégezni a sablonhoz, a modul Az parancsfájl használata. A parancsfájl hozzáad egy perjel az összetevők helyre, de a sablon nem várható, hogy az adott perjel. Nyissa meg a WebSite.json, és keresse meg a tulajdonságokat a MSDeploy-bővítmény. Vlastnost s rendelkezik **packageUri**. Távolítsa el a perjellel összetevők helyét és a csomag mappájának között.

Hasonlóan kell kinéznie:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Figyelje meg az előző példában nincs semmilyen `'/',` között **parameters('_artifactsLocation')** és **parameters('ExampleAppPackageFolder')** .

A projekt újraépítéséhez. A projekt létrehozásához biztosítja, hogy az üzembe kell helyeznie fájlokat adnak hozzá az átmeneti mappát.

Most nyisson meg egy PowerShell-konzolt, és futtassa:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM-modul parancsfájl

Az AzureRM-modul parancsfájl a Visual Studio használata:

1. Újbóli üzembe helyezéséhez, válassza ki a **telepítés**, és a korábban telepített erőforráscsoportot.

    ![Telepítse újra a projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Válassza ki a tárfiókot, az adott erőforráscsoporttal üzembe helyezett a **összetevő tárfiókja**.

   ![Telepítse újra a webalkalmazás üzembe helyezése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Webes alkalmazás megtekintése

1. Az üzembe helyezés befejeztével válassza ki a webalkalmazást a portálon. Válassza ki az URL-t a hely böngészéséhez.

   ![Hely tallózása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. Láthatja, hogy az alapértelmezett ASP.NET-alkalmazást sikeresen üzembe helyezte.

   ![telepített alkalmazás megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Műveleti irányítópult hozzáadása

Nem csak a Visual Studio felületén keresztül elérhető erőforrásokat használhatja. Az üzemelő példány testreszabásához adjon egyéni erőforrást a sablonhoz. Az erőforrás hozzáadásának megjelenítéséhez műveleti irányítópultot kell hozzáadnia az üzembe helyezett erőforrás kezelése érdekében.

1. Nyissa meg a WebSite.json fájlt, és a következő JSON hozzáadása után a tárfiók típusú erőforrást, de a Bezárás előtt `]` az erőforrások szakasz.

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

1. Telepítse újra a projekthez.

1. Miután a telepítés véget ért, az irányítópult megtekintése a portálon. Válassza ki **irányítópult** , és válassza ki az üzembe helyezett egy.

   ![Egyéni irányítópult](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. A testre szabott irányítópultot láthatja.

   ![Egyéni irányítópult](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

RBAC-csoportokkal kezelheti az irányítópult hozzáférését. Az irányítópult kinézetét is testreszabhatja az üzembe helyezése után. Ha azonban újból üzembe helyezi az erőforráscsoportot, az irányítópult visszaáll a sablonban szereplő alapértelmezett állapotba. Az irányítópultok létrehozásával kapcsolatos további információkért lásd az [Azure-irányítópultok szoftveres létrehozásával](../azure-portal/azure-portal-dashboards-create-programmatically.md) foglalkozó témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon, válassza ki a **erőforráscsoportok** a bal oldali menüből.

1. Válassza ki az erőforráscsoport nevét.

1. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre és helyezhet üzembe sablonokat a Visual Studio használatával. A következő oktatóanyag azt mutatja be, hogyan keresheti meg a megfelelő információkat a sablonreferenciában titkosított Azure Storage-fiók létrehozásához.

> [!div class="nextstepaction"]
> [Titkosított tárfiók létrehozása](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
