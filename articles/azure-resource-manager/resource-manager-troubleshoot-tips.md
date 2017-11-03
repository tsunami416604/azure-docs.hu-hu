---
title: "Az Azure-telepítés hibák megismerése |} Microsoft Docs"
description: "További tudnivalók az Azure-telepítés hibák ismerteti."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "központi telepítési hiba, az azure-telepítés, telepítse az azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-deployment-errors"></a>Az Azure-telepítés hibák megismerése
Ez a témakör ismerteti a telepítési hibákat, és hogyan felderíthetők a hibával kapcsolatos további információk. Megoldások gyakori telepítési hibáinak, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Két típusú hibák
A hibák fogadhat két típusa van:

* Érvényesítési hiba
* Telepítési hibák

A következő kép bemutatja a műveletnapló az előfizetéshez. Azt jelenti, hogy a két üzemelő példány. Egy központi telepítésben, a sablon ellenőrzése nem sikerült (**ellenőrzése**), és nem haladt. A telepítés esetében a sablon érvényesítési átadott, de nem sikerült az erőforrások létrehozásakor (**írási központi telepítések**). 

![hibakód megjelenítése](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Érvényesítési hibák merülnek fel a szolgáltatásokat, amelyek a központi telepítés előtt meg lehet határozni. Szintaktikai hibákat a sablont, vagy szeretné telepíteni az erőforrásokat, amelyek túllépné az előfizetés kvóták tartoznak. Telepítési hibák feltételek, a telepítési folyamat során előforduló merülhetnek fel. Tartalmaznak egy párhuzamos telepített erőforrás elérésére tett kísérlet.

Mindkét típusú hibák hibakódot, amelyekkel a telepítés hibáinak. Mindkét típusú hibák jelennek meg a [tevékenységnapló](resource-group-audit.md). Azonban érvényesítési hiba jelenik meg a központi telepítés előzményei mert soha nem indult el a központi telepítés.

## <a name="determine-error-code"></a>Határozza meg a hiba kódja

Megismerheti a hiba, ha megnézi a hibaüzenetet, és a hibakód. A [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md) cikkben megoldások hibakóddal leírni. Ez a témakör bemutatja, hogyan használható az Azure-portálon felderíteni a hiba kódja.

### <a name="validation-errors"></a>Érvényesítési hiba

A portálon keresztül telepítésekor érvényesítési hiba láthatja az értékek elküldése után.

![portál érvényesítési hibaüzenet megjelenítése](./media/resource-manager-troubleshoot-tips/validation-error.png)

Válassza ki a további részleteket az üzenetet. Az alábbi képen látható egy **InvalidTemplateDeployment** hiba, és egy házirend jelző üzenet blokkolva központi telepítés.

![érvényesítési részletek megjelenítése](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Telepítési hibák

A művelet a teljesíti az ellenőrző, de nem sikerül a telepítés során, tekintse meg az értesítéseket a hibát. Válassza ki az értesítést.

![értesítési hiba](./media/resource-manager-troubleshoot-tips/notification.png)

Megjelenik a telepítéssel kapcsolatos további részletekért. A beállítás a hibával kapcsolatban további információt.

![központi telepítése nem sikerült](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Megjelenik a hibaüzenet és hibakódok. Figyelje meg, hogy van két hibakódok. Az első hibakód (**"deploymentfailed"**) egy általános hiba, amely nem adja meg a hiba megoldásához szükséges adatokat. A második hibakód (**StorageAccountNotFound**) kell részleteit. 

![Hiba legutolsó részletes adatai](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>A hibakeresési naplózást engedélyező
Néha kérés és válasz felderítéséhez a hibával kapcsolatos további tájékoztatásra van szüksége. PowerShell vagy Azure CLI segítségével kérheti, hogy további információkat kerül a központi telepítés során.

- PowerShell

   A PowerShellben, állítsa be a **DeploymentDebugLogLevel** az összes paramétert, ResponseContent vagy RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Vizsgálja meg a tartalom a következő parancsmaggal kérelem:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Vagy a tartalom a válasz:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Ezen információk segítségével eldöntheti, hogy a sablonban lévő érték helytelen beállítása.

- Azure CLI

   Vizsgálja meg a telepítési műveleteket az alábbi paranccsal:

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Beágyazott sablon

   Beágyazott sablon hibakeresési adatok naplózására, használja a **debugSetting** elemet.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>Hibaelhárítási-sablon létrehozása
Bizonyos esetekben a hibaelhárítás a sablon legkönnyebben teszteléséhez része. Létrehozhat egy egyszerűsített sablont, amely lehetővé teszi, hogy jobban összpontosíthat az a része, amely úgy véli okozza a hibát. Tegyük fel, hogy hiba azért kapta, való hivatkozáskor erőforrás. Ahelyett, hogy egy teljes sablon foglalkozik, a része, a problémát okozó visszaadó sablont létrehozni. Ez segít meghatározni, hogy átadott a megfelelő paraméterek sablon függvénnyel megfelelően, és az erőforrás lekérése várt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Vagy tegyük fel, amely azt feltételezi, helytelenül állította be a függőségek kapcsolódó központi telepítési hibái áll kapcsolatban. Tesztelje a sablon ossza egyszerűsített sablonok. Először hozzon létre egy sablont, amely csak egyetlen erőforrás (például egy SQL Server) telepít. Ha meggyőződött arról, hogy helyesen definiálva erőforrást is tartalmaz, amelyektől függ (például egy SQL-adatbázis) erőforrás hozzáadása. Ha két erőforrások helyesen definiálva van, adja hozzá a más függő erőforrások (például naplózási házirendek). Minden egyes próbatelepítés Between törölje a csoportot, hogy biztosan megfelelően tesztelése a függőségeket. 

## <a name="check-deployment-sequence"></a>Feladatütemezési központi telepítés ellenőrzése

Sok központi telepítési hiba fordulhat elő, amikor egy váratlan sorozat erőforrások telepítése. Ezeket a hibákat okozhat, ha a függőségek nem megfelelően van beállítva. Hiányzik egy szükséges függőséget, amikor egy erőforrást próbál értéket egy másik erőforrás használja, de a másik még nem létezik. Hibaüzenet arról, hogy egy erőforrás nem található. Hiba az ilyen típusú felmerülhet időnként mivel a központi telepítéskor az egyes erőforrások változhat. Például az erőforrások telepítése az első kísérlet sikeres lesz, mert egy szükséges erőforrás véletlenszerűen idő alatt fejeződik be. Azonban a második kísérlet sikertelen, mert a szükséges erőforrás nem fejeződött be időben. 

De el szeretné kerülni, nem szükséges függőségek beállítása. Ha szükségtelen függőségek, erőforrások, amelyek nincsenek a párhuzamos telepített egymástól függenek, hogy meghosszabbítani az üzemelő példány időtartama. Ezenkívül létrehozhat körkörös függőségek, amelyek a központi telepítés letiltása. A [hivatkozás](resource-group-template-functions-resource.md#reference) funkció egy implicit függőség a hivatkozott erőforrás hoz létre, ha ugyanazt a sablont, hogy az erőforrás lett telepítve. Emiatt előfordulhat, hogy további függőségek, mint a függőségek megadott a **dependsOn** tulajdonság. A [resourceId](resource-group-template-functions-resource.md#resourceid) függvény létrehozása egy implicit függőség, vagy nem ellenőrzi, hogy létezik-e az erőforrást.

Amikor a függőség problémákat tapasztal, kell betekintést erőforrás telepítési sorrendjét. A telepítési műveletek sorrendjét megtekintése:

1. Válassza ki az üzembe helyezési előzményeket az erőforráscsoport számára.

   ![Válassza ki a központi telepítés előzményei](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Jelölje ki a központi telepítés előzményeit, és válassza ki **események**.

   ![Válassza ki a központi telepítési eseményeket](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Vizsgálja meg az egyes erőforrások események sorozatát. Nagy figyelmet fordítani az egyes művelet állapotát. Például a következő kép bemutatja, amelynek a telepítése három storage-fiókok párhuzamosan. Figyelje meg, hogy a három storage-fiókok egy időben vannak-e indítva.

   ![Párhuzamos üzembe helyezés](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   A következő kép bemutatja, három tárfiókok nem telepített párhuzamosan. A második tárfiók attól függ, az első tárfiók, és a harmadik tárfiók attól függ, a második tárfiókot. Ezért az első tárfiók elindult, elfogadott, és befejeződött a következő megkezdése előtt.

   ![szekvenciális központi telepítés](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Még a bonyolultabb esetek is ugyanaz a technika segítségével észleléséhez, amikor a központi telepítés elkezdődött és befejeződött minden erőforráshoz. Nézze át a központi telepítési események meg, ha a feladatütemezési más, mint teheti meg. Ha igen, akkor újra kiértékelje ehhez az erőforráshoz tartozó függőségek.

Erőforrás-kezelő körkörös függőségi viszony azonosítja a sablon érvényesítése során. Egy hibaüzenet arról, hogy kifejezetten körkörös függőség létezik adja vissza. Körkörös függőség megoldása:

1. A sablonban a körkörös függőség azonosított erőforrás található. 
2. Az adott erőforráshoz, vizsgálja meg a **dependsOn** tulajdonság és bármely használatát a **hivatkozás** működnek, attól függ, milyen erőforrásokat. 
3. Vizsgálja meg ezeket az erőforrásokat, hogy mely erőforrások függenek. Kövesse a függőségek meg, amikor egy erőforrást, amely az eredeti erőforrás függ.
5. Az erőforrások a körkörös függőség részt, gondosan vizsgálja meg az összes használatát a **dependsOn** tulajdonság esetén nem szükséges összes függőséget azonosításához. Távolítsa el ezeket a függőségeket. Ha biztos abban, hogy a függőség van szükség, távolítsa el azt. 
6. Telepítse újra a sablont.

Az értékek eltávolítása a **dependsOn** tulajdonság hibákat okozhat, ha a sablon telepítéséhez. Ha hibát észlel, adja hozzá a függőség újra üzembe a sablont. 

Ha ezt a megközelítést nem oldja meg a körkörös függőséget, fontolja meg a központi telepítés logikája a gyermekszintű erőforrása (például a bővítmények vagy konfigurációs beállítások). Gyermek erőforrások telepítése után a körkörös függőség erőforrás konfigurálása. Tegyük fel például, két olyan virtuális gépet telepít, de a tulajdonságokat meg kell adni az egyes, amely a másikra hivatkozik. A következő sorrendben telepíthetők:

1. vm1
2. vm2 virtuális gépnek
3. Vm1 kiterjesztés vm1 és vm2 virtuális gépnek függ. A bővítmény beállítása, amely azt lekérése vm2 virtuális gépnek vm1 értékeket.
4. Bővítmény vm2 virtuális gépnek a vm1 és vm2 virtuális gépnek függ. A bővítmény beállítása értékek, amelyek azt lekérése vm1 vm2 virtuális gépnek.

Ugyanezt a megközelítést működik az App Service-alkalmazásokhoz. Fontolja meg a konfigurációs értékek helyezi át az alkalmazás-erőforrást gyermek erőforrása. Két webes alkalmazásokat telepíthet a következő sorrendben:

1. webapp1
2. webapp2
3. config webapp1 webapp1 és webapp2 függ. Alkalmazásbeállítások webapp2 értékeivel tartalmaz.
4. config webapp2 webapp1 és webapp2 függ. Alkalmazásbeállítások webapp1 értékeivel tartalmaz.


## <a name="next-steps"></a>Következő lépések
* Megoldások gyakori telepítési hibáinak, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Műveletek naplózásával kapcsolatos további tudnivalókért lásd: [naplózási műveletek a Resource Manager](resource-group-audit.md).
* Azokról a műveletekről, a hibák megállapításához központi telepítése során további tudnivalókért lásd: [üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md).
