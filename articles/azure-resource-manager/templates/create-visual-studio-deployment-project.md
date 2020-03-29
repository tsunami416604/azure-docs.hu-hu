---
title: Visual Studio-erőforráscsoport-projektek létrehozása &
description: A Visual Studio segítségével hozzon létre egy Azure-erőforráscsoport-projektet, és telepítse az erőforrásokat az Azure-ba.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 5127732ac0c33d4b27f70bd616fb23aaec5c871f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152731"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással

A Visual Studio alkalmazással olyan projekteket hozhat létre, amelyekkel telepíthető az infrastruktúra és kód az Azure rendszerébe. Telepítheti például a webhely webtárhelyét, webhelyét és kódját. A Visual Studio számos különböző kezdősablont kínál a gyakori forgatókönyvek telepítéséhez. Ebben a cikkben egy webalkalmazást telepít.

Ez a cikk bemutatja, hogyan használhatja [a Visual Studio 2019-es vagy újabb](/visualstudio/install/install-visual-studio?view=vs-2019)az Azure fejlesztési és ASP.NET számítási feladatok telepítve. Ha a Visual Studio 2017-et használja, a felhasználói élmény nagyjából ugyanaz.

## <a name="create-azure-resource-group-project"></a>Azure erőforráscsoport-projekt létrehozása

Ebben a szakaszban hozzon létre egy Azure Resource Group projekt egy **webalkalmazás-sablon.**

1. A Visual Studióban válassza az**Új**>**projekt** **fájlja**>lehetőséget.
1. Válassza ki az **Azure Resource Group** projektsablont és a **Tovább**lehetőséget.

    ![Projekt létrehozása](./media/create-visual-studio-deployment-project/create-project.png)

1. Adjon nevet a projektnek. A többi alapértelmezett beállítás valószínűleg rendben van, de tekintse át őket, hogy a környezetben működjenek. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    ![Projekt létrehozása](./media/create-visual-studio-deployment-project/name-project.png)

1. Válassza ki az Azure Resource Managerbe telepíteni kívánt sablont. Figyelje meg, hogy a telepíteni kívánt projekt típusától függően számos különböző lehetőség áll rendelkezésre. Ebben a cikkben válassza a **Webapp** sablont, és **ok**.

    ![Sablon kiválasztása](./media/create-visual-studio-deployment-project/select-project.png)

    A kiválasztott sablon csak egy kiindulási pont. A forgatókönyvnek való megfelelés érdekében hozzáadhat és eltávolíthat erőforrásokat.

1. A Visual Studio létrehoz egy erőforráscsoport-telepítési projektet a webalkalmazáshoz. A projekt fájljainak megtekintéséhez tekintse meg a csomópontot a központi telepítési projektben.

    ![Csomópontok megjelenítése](./media/create-visual-studio-deployment-project/show-items.png)

    Mivel a webalkalmazás-sablont választotta, a következő fájlok jelennek meg:

   | Fájlnév | Leírás |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |PowerShell-parancsfájl, amely PowerShell-parancsokat futtat az Azure Resource Manager üzembe helyezéséhez. A Visual Studio ezt a PowerShell-parancsfájlt használja a sablon központi telepítéséhez. |
   | Webhely.json |Az Azure szolgáltatásban telepíteni kívánt infrastruktúrát, valamint a telepítés során megadható paramétereket meghatározó Resource Manager-sablon. A telepített erőforrások közti függőségeket is meghatározza, így a Resource Manager megfelelő sorrendben telepíti azokat. |
   | Webhely.paraméterek.json |Paraméterfájl, amely a sablonhoz szükséges értékeket tartalmazza. Megadhat paraméterértékeket, amelyekkel testre szabhatóak az egyes telepítések. |

    Mindegyik erőforráscsoport-telepítési projekt tartalmazza ezeket az alapvető fájlokat. Más projektek további fájlokat is tartalmazhatnak egyéb funkciók támogatásához.

## <a name="customize-resource-manager-template"></a>Az Erőforrás-kezelő sablon testreszabása

A központi telepítési projekt testreszabásához módosítsa az Erőforrás-kezelő sablont, amely leírja a telepíteni kívánt erőforrásokat. A Resource Manager-sablon elemeivel kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése](template-syntax.md).

1. A sablonon való munkához nyissa meg a **WebSite.json webhelyet.**

1. A Visual Studio szerkesztő eszközöket biztosít a Resource Manager-sablon szerkesztéséhez. A **JSON-vázlat** ablak segítségével könnyen áttekinthetőek a sablonban meghatározott elemek.

   ![JSON-tagolás megjelenítése](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Jelöljön ki egy elemet a tagolásban, ha a sablon adott részéhez szeretne lépni.

   ![Navigálás JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. Hozzáadhat egy új erőforrást a JSON-vázlat ablak tetején található **Erőforrás hozzáadása** gomb kiválasztásával, vagy kattintson a jobb gombbal az **erőforrások** elemre, és válassza az **Új erőforrás hozzáadása** lehetőséget.

   ![Erőforrás hozzáadása](./media/create-visual-studio-deployment-project/add-resource.png)

1. Válassza **a Tárfiók lehetőséget,** és adjon neki nevet. Olyan nevet adjon meg, amely nem hosszabb 11 karakternél, és csak számokat és kisbetűket tartalmaz.

   ![Tároló hozzáadása](./media/create-visual-studio-deployment-project/add-storage.png)

1. Figyelje meg, hogy nem csupán az erőforrás lett hozzáadva, hanem a tárfiók típusának paramétere is, valamint egy változó a tárfiók nevével.

   ![Tagolás megjelenítése](./media/create-visual-studio-deployment-project/show-new-items.png)

1. A tárfiók típusának paramétere előre definiált engedélyezett típusokkal és alapértelmezett típussal. Megtarthatja ezeket az értékeket, vagy módosíthatja őket az adott forgatókönyvnek megfelelően. Ha nem szeretné, hogy bárki **Premium_LRS** tárfiókot helyezzen üzembe a sablonon keresztül, egyszerűen törölje azt az engedélyezett típusok közül.

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

1. A Visual Studio az intellisense-t is biztosítja, hogy segítsen megérteni a sablon szerkesztésekor elérhető tulajdonságokat. Például az App Service-csomag tulajdonságainak szerkesztéséhez lépjen a **HostingPlan** erőforrásra, és adjon meg egy értéket a **properties** elemnél. Figyelje meg, hogy az intellisense megjeleníti az elérhető értékeket, valamint az adott értékek leírását.

   ![Mutasd intellisense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   Az OfWorkers számot 1-re **állíthatja,** és mentheti a fájlt.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Nyissa meg a **WebSite.parameters.json** fájlt. A paraméterek fájl segítségével adja át az értékeket a központi telepítés során, amely testre szabja a központi telepítés alatt. Adjon nevet az üzemeltetési tervnek, és mentse a fájlt.

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

Most már készen áll a projekt üzembe helyezésére egy erőforráscsoportban.

Alapértelmezés szerint a PowerShell-parancsfájl (Deploy-AzureResourceGroup.ps1) a projekt ben használja az AzureRM-modult. Ha továbbra is telepítve van az AzureRM-modul, és továbbra is használni szeretné, használhatja ezt az alapértelmezett parancsfájlt. Ezzel a parancsfájllal a Visual Studio felületen telepítheti a megoldást.

Ha azonban áttért az új [Az-modulra,](/powershell/azure/new-azureps-module-az)új parancsfájlt kell hozzáadnia a projekthez. Az Az modult használó parancsfájl hozzáadásához másolja a [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) parancsfájlt, és adja hozzá a projekthez. Ahhoz, hogy ezt a parancsfájlt központi telepítésre használja, a Visual Studio központi telepítési felületének használata helyett egy PowerShell-konzolról kell futtatnia.

Mindkét megközelítés ebben a cikkben látható. Ez a cikk az alapértelmezett parancsfájlt az AzureRM-modul parancsfájlként, az új parancsfájlt pedig az Az modul parancsfájljaként hivatkozik.

### <a name="az-module-script"></a>Az modul parancsfájlja

Az Az modul parancsfájljához nyisson meg egy PowerShell-konzolt, és futtassa a következőket:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM-modulparancsfájl

Az AzureRM-modul parancsfájljához használja a Visual Studio alkalmazást:

1. A központi telepítési projektcsomópont helyi menüjében válassza az**Új** **telepítése parancsot.** > 

    ![Új központi telepítési menüelem](./media/create-visual-studio-deployment-project/deploy.png)

1. Megjelenik a **Telepítés erőforráscsoportra** párbeszédpanel. Az **Erőforráscsoport** legördülő listából válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat. Válassza az **Üzembe helyezés** lehetőséget.

    ![Telepítés az erőforráscsoportban párbeszédpanel](./media/create-visual-studio-deployment-project/show-deployment.png)

1. A **Kimenet** ablakban követhető az üzembe helyezés állapota. Az üzembe helyezés befejeztével az utolsó üzenet jelzi az üzembe helyezés sikerességét, a következőhöz hasonló módon:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Telepített erőforrások megtekintése

Nézzük meg az eredményeket.

1. Egy böngészőben nyissa meg az [Azure Portalt](https://portal.azure.com/), és jelentkezzen be a fiókjával. Az erőforráscsoport megtekintéséhez válassza az **Erőforráscsoportok** lehetőséget, valamint az erőforráscsoportot, amelyiken a telepítést végezte.

1. Az összes telepített erőforrás megjelenik. Figyeljen arra, hogy a tárfiók neve nem pontosan az, amit az erőforrás hozzáadásakor megadott. A tárfiók nevének egyedinek kell lennie. A sablon automatikusan karakterláncot ad a megadott névhez, hogy egyedi nevet hozzon létre.

    ![Erőforrások megjelenítése](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Kód hozzáadása a projekthez

Ezen a ponton az alkalmazás infrastruktúrája már telepítve van, tényleges kód azonban még nincs telepítve a projekttel.

1. Adjon hozzá egy projektet a Visual Studio megoldásához. Kattintson a jobb gombbal a megoldásra, és válassza az**Új projekt** **hozzáadása** > parancsot.

    ![Projekt hozzáadása](./media/create-visual-studio-deployment-project/add-project.png)

1. Adjon hozzá egy **ASP.NET Core webalkalmazást.**

    ![Webalkalmazás hozzáadása](./media/create-visual-studio-deployment-project/add-app.png)

1. Adjon nevet a webalkalmazásnak, és válassza a **Létrehozás gombot.**

    ![Név webalkalmazás](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Válassza a **Webalkalmazás** és **a Létrehozás lehetőséget.**

    ![Webalkalmazás kiválasztása](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Miután a Visual Studio létrehozta a webalkalmazást, a megoldásban mindkét projekt megjelenik.

    ![Projektek megjelenítése](./media/create-visual-studio-deployment-project/show-projects.png)

1. Most győződjön meg arról, hogy az erőforráscsoport észleli az új projektet. Visszalépés az erőforráscsoport-projekthez (ExampleAppDeploy). Kattintson jobb gombbal a **References** (Hivatkozások) elemre és válassza az **Add Reference** (Hivatkozás hozzáadása) lehetőséget.

    ![Hivatkozás hozzáadása](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Válassza ki a webalkalmazás-projektet, amelyet létrehozott.

   ![Hivatkozás hozzáadása](./media/create-visual-studio-deployment-project/add-reference.png)

   Hivatkozás hozzáadásával csatolja a webalkalmazás-projektet az erőforráscsoport-projekthez, és automatikusan beállít bizonyos tulajdonságokat. Ezek a tulajdonságok a hivatkozáshoz tartozó **Properties** (Tulajdonságok) ablakban láthatók. A **Fájl elérési útjának belefoglalása** azt az útvonalat tartalmazza, ahol a csomag létrejött. Jegyezze meg a mappa (ExampleApp) és a fájl (package.zip) nevét. Ismernie kell ezeket az értékeket, mert az alkalmazás üzembe helyezésekor meg kell adni őket paraméterekként.

   ![Lásd a hivatkozást](./media/create-visual-studio-deployment-project/see-reference.png)

1. Lépjen vissza a sablonhoz (WebSite.json), és adjon hozzá egy erőforrást a sablonhoz.

    ![Erőforrás hozzáadása](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Ez alkalommal válassza a **Web Deploy for Web Apps** (Webalkalmazások webes üzembe helyezése) lehetőséget.

    ![Webes telepítés hozzáadása](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Mentse a sablont.

1. Van néhány új paraméter a sablonban. Ezeket az előző lépésben adták hozzá. Nem kell értékeket megadnia **_artifactsLocation** vagy **_artifactsLocationSasToken** mert ezek az értékek automatikusan generálódnak. A mappát és a fájlnevet azonban a központi telepítési csomagot tartalmazó elérési útra kell állítania. A paraméterek neve **packagefolder** és **PackageFileName néven végződik.** A név első része a hozzáadott webes központi telepítés típusú erőforrás neve. Ebben a cikkben a neve **ExampleAppPackageFolder** és **ExampleAppPackageFileName**.

   Nyissa **meg a Website.parameters.json webhelyet,** és állítsa ezeket a paramétereket a referenciatulajdonságokban látott értékekre. Állítsa **az ExampleAppPackageFolder mappát** a mappa nevére. Állítsa **az ExampleAppPackageFileName-t** a zip fájl nevére.

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

Mivel kódot adott a projekthez, a központi telepítés ezúttal egy kicsit más. A telepítés során olyan helyre helyezi a projekt összetevőit, amelyhez az Erőforrás-kezelő hozzáférhet. Az összetevők egy tárfiókba vannak előkészítve.

### <a name="az-module-script"></a>Az modul parancsfájlja

Van egy kis változás, amit el kell tennie a sablonon, ha az Az modul parancsfájlt használja. Ez a parancsfájl egy perjelet ad az összetevők helyére, de a sablon nem számít arra, hogy a perjel. Nyissa meg a WebSite.json webhelyet, és keresse meg az MSDeploy bővítmény tulajdonságait. Van egy packageUri nevű **tulajdonsága.** Távolítsa el az összetevők helye és a csomagmappa közötti perjelet.

Ennek így kell kinéznie:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Az előző példában található `'/',` megjegyzés: nincs **paraméter('_artifactsLocation')** és **parameters('ExampleAppPackageFolder') között.**

Építse újra a projektet. A projekt létrehozása biztosítja, hogy a telepítéshez szükséges fájlok hozzáadódnak az átmeneti mappához.

Most nyisson meg egy PowerShell-konzolt, és futtassa a következőket:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM-modulparancsfájl

Az AzureRM-modul parancsfájljához használja a Visual Studio alkalmazást:

1. Az újratelepítéshez válassza **a Telepítés**lehetőséget és a korábban üzembe helyezett erőforráscsoportot.

    ![Projekt átcsoportosítása](./media/create-visual-studio-deployment-project/redeploy.png)

1. Válassza ki a **rendszermászoló tárfiókhoz**ezzel az erőforráscsoporttal üzembe helyezett tárfiókot.

   ![Webes telepítés újratelepítése](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Webalkalmazás megtekintése

1. Az üzembe helyezés befejeztével válassza ki a webalkalmazást a portálon. Válassza ki az URL-t a hely böngészéséhez.

   ![Webhely böngészése](./media/create-visual-studio-deployment-project/browse-site.png)

1. Láthatja, hogy az alapértelmezett ASP.NET-alkalmazást sikeresen üzembe helyezte.

   ![Telepített alkalmazás megjelenítése](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Műveletek irányítópultjának hozzáadása

Nem csak a Visual Studio felületén keresztül elérhető erőforrásokat használhatja. Az üzemelő példány testreszabásához adjon egyéni erőforrást a sablonhoz. Az erőforrás hozzáadásának megjelenítéséhez műveleti irányítópultot kell hozzáadnia az üzembe helyezett erőforrás kezelése érdekében.

1. Nyissa meg a WebSite.json fájlt, és adja hozzá a következő `]` JSON-fájlt a tárfiók-erőforrás után, de az erőforrások szakasz bezárása előtt.

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
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Telepítse újra a projektet.

1. Az üzembe helyezés befejezése után tekintse meg az irányítópultot a portálon. Válassza **az Irányítópult** lehetőséget, és válassza ki a telepítettet.

   ![Egyéni irányítópult](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Megjelenik a testreszabott irányítópult.

   ![Egyéni irányítópult](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

RBAC-csoportokkal kezelheti az irányítópult hozzáférését. Az irányítópult kinézetét is testreszabhatja az üzembe helyezése után. Ha azonban újból üzembe helyezi az erőforráscsoportot, az irányítópult visszaáll a sablonban szereplő alapértelmezett állapotba. Az irányítópultok létrehozásával kapcsolatos további információkért lásd az [Azure-irányítópultok szoftveres létrehozásával](../../azure-portal/azure-portal-dashboards-create-programmatically.md) foglalkozó témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **erőforráscsoportok** válassza ki.

1. Válassza ki az erőforráscsoport nevét.

1. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtudhatja, hogyan hozhat létre és helyezhet üzembe sablonokat a Visual Studio használatával. Ha többet szeretne megtudni a sablonfejlesztésről, tekintse meg az új kezdő oktatósorozatunkat:

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)
