---
title: Biztonságos üzembe helyezés régiók között – Azure Deployment Manager
description: Bemutatja, hogyan telepíthet egy szolgáltatást számos régióban az Azure Deployment Manager használatával. Biztonságos üzembe helyezési eljárásokat mutat be a telepítés stabilitásának ellenőrzéséhez, mielőtt az összes régióban bevezetne.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 424cd79a6c63200e1f101cf178b1fd2c9083161e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152527"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Biztonságos üzembe helyezési eljárások engedélyezése az Azure Deployment Manager rel (nyilvános előzetes verzió)

A szolgáltatás üzembe helyezéséhez számos régióban, és győződjön meg arról, hogy az elvárt módon fut az egyes régiókban, az Azure Deployment Manager segítségével koordinálhatja a szolgáltatás szakaszos bevezetését. Csakúgy, mint bármely Azure-telepítés, a szolgáltatás erőforrásait az [Erőforrás-kezelő sablonokban](template-syntax.md)határozza meg. A sablonok létrehozása után a Deployment Manager segítségével írja le a szolgáltatás topológiáját, és hogyan kell azt bevezetni.

A Deployment Manager az Erőforrás-kezelő egyik szolgáltatása. Kibővíti a képességeket az üzembe helyezés során. Használja a Deployment Managert, ha olyan összetett szolgáltatással rendelkezik, amelyet több régióban kell telepíteni. A szolgáltatás kibocsátásának előkészítésével a régiókban történő üzembe helyezés előtt azonosíthat potenciális problémákat. Ha nincs szüksége a szakaszos bevezetés további óvintézkedésekre, használja az Erőforrás-kezelő szabványos [telepítési beállításait.](deploy-portal.md) A Deployment Manager zökkenőmentesen integrálható az erőforrás-kezelő üzembe helyezését támogató összes külső gyártótól származó eszközzel, például a folyamatos integrációval és a folyamatos teljesítéssel (CI/CD).

Az Azure Deployment Manager előzetes verzióban érhető el. Segítsen nekünk javítani a funkciót [azáltal,](https://aka.ms/admfeedback)hogy visszajelzést .

A Deployment Manager használatához négy fájlt kell létrehoznia:

* Topológia sablon
* Bevezetési sablon
* A topológia paraméterfájlja
* Bevezetési paraméterfájl

A topológiasablont a bevezetési sablon üzembe helyezése előtt telepíti.

További források:

- Az [Azure Deployment Manager REST API-hivatkozása](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Oktatóanyag: Használja az Azure Deployment Managert erőforrás-kezelősablonokkal.](./deployment-manager-tutorial.md)
- [Oktatóanyag: Használja az állapotfelmérést az Azure Deployment Manager ben.](./deployment-manager-tutorial-health-check.md)
- [Egy Azure Deployment Manager-minta](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identitás és hozzáférés

A Deployment Manager segítségével a [felhasználó által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) hajtja végre a központi telepítési műveleteket. Ezt az identitást a központi telepítés megkezdése előtt hozza létre. Hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelybe a szolgáltatást üzembe helyezi, és elegendő engedéllyel kell rendelkeznie a központi telepítés befejezéséhez. A szerepkörökön keresztül biztosított műveletekről az [Azure-erőforrások beépített szerepkörei](../../role-based-access-control/built-in-roles.md)című témakörben talál további információt.

Az identitásnak ugyanazon a helyen kell lennie, mint a bevezetés.

## <a name="topology-template"></a>Topológia sablon

A topológiasablon ismerteti a szolgáltatást és üzembe helyezésének helye szerinti Azure-erőforrásokat. Az alábbi képen egy példaszolgáltatás topológiája látható:

![Hierarchia a szolgáltatástopológiától a szolgáltatásokon át a szervizegységekig](./media/deployment-manager-overview/service-topology.png)

A topológiasablon a következő erőforrásokat tartalmazza:

* Műtermék forrása – ahol az Erőforrás-kezelő sablonjai és paraméterei tárolódnak
* Szolgáltatástopológiája – műtermék-forrásra mutat
  * Szolgáltatások – megadja a helyet és az Azure-előfizetés azonosítóját
    * Szolgáltatási egységek - erőforráscsoportot, telepítési módot és a sablon- és paraméterfájl elérési útját határozza meg

Ahhoz, hogy megértsük, mi történik az egyes szinten, érdemes megnézni, hogy milyen értékeket ad meg.

![Értékek minden szinten](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Sablonok műtermék-forrása

A topológiasablonban hozzon létre egy műtermék-forrást, amely tartalmazza a sablonokat és a paraméterfájlokat. A műtermék-forrás egy módja annak, hogy lekéri a fájlokat a központi telepítéshez. A cikk későbbi részében megjelenik egy másik bináris összetevő-forrás.

A következő példa a műtermék-forrás általános formátumát mutatja be.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

További információ: [artifactSources template reference](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Szolgáltatástopológia

A következő példa a szolgáltatás topológia erőforrás általános formátumát mutatja be. Megadja a sablonokat és a paraméterfájlokat tartalmazó műtermék-forrás erőforrásazonosítóját. A szolgáltatás topológia tartalmazza az összes szolgáltatás erőforrást. Győződjön meg arról, hogy a műtermék-forrás elérhető, a szolgáltatás topológiája függ tőle.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

További információ: [serviceTopologies template reference](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Szolgáltatások

A következő példa a szolgáltatási erőforrás általános formátumát mutatja be. Minden szolgáltatásban megadja a helyet és az Azure-előfizetés-azonosítót, amelyet a szolgáltatás üzembe helyezéséhez használni kell. Ha több régióban szeretne üzembe helyezni, minden régióhoz meg kell adnia egy szolgáltatást. A szolgáltatás a szolgáltatás topológiájától függ.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

További információt a [szolgáltatássablon hivatkozási útmutatójában talál.](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)

### <a name="service-units"></a>Szolgáltatási egység

A következő példa a szervizegységek erőforrás általános formátumát mutatja be. Minden szolgáltatási egységben megadhatja az erőforráscsoportot, a központi telepítéshez használandó [telepítési módot,](deployment-modes.md) valamint a sablon- és paraméterfájl elérési útját. Ha relatív elérési utat ad meg a sablonhoz és a paraméterekhez, a teljes elérési út a műtermékek forrásának gyökérmappájából jön létre. Megadhatja a sablon és a paraméterek abszolút elérési útját, de elveszíti a kiadások egyszerű verzióverzióját. A szervizegység a szolgáltatástól függ.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Minden sablonnak tartalmaznia kell az egy lépésben telepíteni kívánt kapcsolódó erőforrásokat. Egy szolgáltatási egység például rendelkezhet egy sablonnal, amely a szolgáltatás előtéréhez az összes erőforrást üzembe helyezi.

További információt a [ServiceUnits sablon hivatkozási száma című témakörben talál.](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)

## <a name="rollout-template"></a>Bevezetési sablon

A bevezetési sablon ismerteti a szolgáltatás üzembe helyezésekor végrehajtandó lépéseket. Megadhatja a használni kívánt szolgáltatástopológiát, és megadhatja a szolgáltatási egységek központi telepítésének sorrendjét. Tartalmaz egy må±termã©k-forrás-tárolása bináris fájlok at a központi telepítéshez. A bevezetési sablonban a következő hierarchiát kell meghatározni:

* Műtermék forrása
* Lépés
* Kiépítése
  * Lépéscsoportok
    * Telepítési műveletek

Az alábbi képen a bevezetési sablon hierarchiája látható:

![Hierarchia a bevezetéstől a lépésekig](./media/deployment-manager-overview/Rollout.png)

Minden bevezetés hez több lépéscsoport is lehet. Minden lépéscsoport rendelkezik egy központi telepítési művelettel, amely a szolgáltatás topológiájában egy szolgáltatási egységre mutat.

### <a name="artifact-source-for-binaries"></a>Bináris összetevők forrása

A bevezetési sablonban hozzon létre egy må±termã©k-forrása a kiszolgálón üzembe helyező bináris fájlokhoz. Ez a műtermék-forrás hasonló a [sablonok műtermék-forrásához,](#artifact-source-for-templates)azzal a különbséggel, hogy tartalmazza a parancsfájlokat, weblapokat, lefordított kódot vagy a szolgáltatás által szükséges egyéb fájlokat.

### <a name="steps"></a>Lépések

Megadhatja a központi telepítési művelet előtt vagy után végrehajtandó lépést. Jelenleg csak `wait` a lépés és a "healthCheck" lépés áll rendelkezésre.

A várakozási lépés szünetelteti a telepítést a folytatás előtt. Lehetővé teszi annak ellenőrzését, hogy a szolgáltatás a várt módon fut-e a következő szervizegység telepítése előtt. A következő példa a várakozási lépés általános formátumát mutatja be.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Az időtartam tulajdonság az [ISO 8601 szabványt](https://en.wikipedia.org/wiki/ISO_8601#Durations)használja. Az előző példa egy perces várakozást ad meg.

Az állapotfelmérési lépésről további információt az [Állapotintegráció bevezetése](./deployment-manager-health-check.md) az Azure Deployment Manager ben és [oktatóanyagban: Állapotfelmérés használata az Azure Deployment Managerben](./deployment-manager-tutorial-health-check.md)című témakörben talál.

További információt a [következő lépésekben](/azure/templates/Microsoft.DeploymentManager/steps)talál: steps template reference .

### <a name="rollouts"></a>Kibocsátások

Annak érdekében, hogy a műtermék-forrás elérhető legyen, a bevezetés ettől függ. A bevezetés határozza meg a lépések csoportjai minden üzembe helyezett szolgáltatási egység. Megadhatja a telepítés előtt vagy után végrehajtandó műveleteket. Megadhatja például, hogy a központi telepítés a szolgáltatási egység üzembe helyezése után várjon. Megadhatja a lépéscsoportok sorrendjét.

Az identitásobjektum a központi telepítési műveleteket [végrehajtó, felhasználó által hozzárendelt felügyelt identitást](#identity-and-access) határozza meg.

A következő példa a bevezetés általános formátumát mutatja be.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

További információt a [Bevezetési sablon hivatkozási útmutatója című témakörben talál.](/azure/templates/Microsoft.DeploymentManager/rollouts)

## <a name="parameter-file"></a>Paraméterfájl

Két paraméterfájlt hoz létre. Az egyik paraméterfájl a szolgáltatás topológia telepítésekor, a másik pedig a bevezetési telepítéshez használatos. Vannak olyan értékek, amelyeket meg kell győződnie arról, hogy mindkét paraméterfájlban azonosak.

## <a name="containerroot-variable"></a>containerRoot változó

Verziós központi telepítések esetén az összetevők elérési útja minden új verzióval megváltozik. A központi telepítés első futtatásakor `https://<base-uri-blob-container>/binaries/1.0.0.0`az elérési út lehet. A második alkalommal `https://<base-uri-blob-container>/binaries/1.0.0.1`lehet . A Deployment Manager leegyszerűsíti a megfelelő gyökérelérési `$containerRoot` út beszerzése az aktuális központi telepítés a változó használatával. Ez az érték minden verzióval változik, és nem ismert a telepítés előtt.

Használja `$containerRoot` a változót a paraméterfájlban a sablon az Azure-erőforrások üzembe helyezéséhez. Üzembe helyezéskor ez a változó a bevezetéstényleges értékeivel lesz lecserélve.

A bevezetés során például létrehoz egy műtermék-forrást a bináris összetevőkhöz.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Figyelje `artifactRoot` `sasUri` meg a és a tulajdonságokat. A műtárgygyökér értéke a `binaries/1.0.0.0`. A SAS URI a tárolótároló URI-ja egy SAS-jogkivonattal a hozzáféréshez. A Deployment Manager automatikusan `$containerRoot` kiépíti a változó értékét. Egyesíti ezeket az értékeket a formátumban `<container>/<artifactRoot>`.

A sablonnak és a paraméterfájlnak ismernie kell a verziózott bináris fájlok beszerzésének helyes elérési útját. Ha például egy webalkalmazáshoz szeretne fájlokat telepíteni, hozza létre a következő paraméterfájlt a $containerRoot változóval. Az elérési úthoz két`\\`fordított perjelet ( ) kell használnia, mert az első egy escape karakter.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Ezután használja ezt a paramétert a sablonban:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

A verziószámozott központi telepítések kezelése új mappák létrehozásával és a gyökérben való átadással a bevezetés során. Az elérési út az erőforrásokat üzembe helyező sablonba jut.

## <a name="next-steps"></a>További lépések

Ebben a cikkben a Deployment Manager ről szerzett tudomást. Folytassa a következő cikkel, hogy megtudja, hogyan telepítheti a Deployment Manager.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Deployment Manager használata erőforrás-kezelői sablonokkal](./deployment-manager-tutorial.md)
>
> [Rövid útmutató: Próbálja ki az Azure Deployment Managert néhány perc alatt](https://github.com/Azure-Samples/adm-quickstart)
