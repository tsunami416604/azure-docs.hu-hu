---
title: Régióban – az Azure Deployment Manager biztonságos üzembe helyezési eljárások
description: Ismerteti, hogyan lehet a szolgáltatás telepítése több választható régióban az Azure Deployment Manager keresztül. Ellenőrizze a telepítés előtt minden régióban elérhetőek stabilitását biztonságos üzembe helyezési eljárásokat jeleníti meg.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: a615ab26e4ea046ced70ce2c154a0c304b741986
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138347"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-private-preview"></a>Biztonságos üzembe helyezési eljárások az Azure Deployment Manager (privát előzetes verzió) engedélyezése

A szolgáltatás számos régióban üzembe helyezheti, és ellenőrizze, hogy az egyes régiókban a várt módon fut, az Azure Deployment Manager segítségével egy szakaszos bevezetést a szolgáltatás koordinálja. Ugyanúgy, mint bármely Azure-telepítéshez, az erőforrásokat a szolgáltatáshoz a határoz meg [Resource Manager-sablonok](resource-group-authoring-templates.md). Miután létrehozta a sablonokat, a Deployment Manager a topológia a szolgáltatáshoz, és hogyan azt kell történni használhatja.

Deployment Manager funkciója a Resource Manager. Üzembe helyezés során a képességekkel bővíti. Használat Deployment Manager, ha rendelkezik olyan összetett szolgáltatás, amelynek számos régióban üzembe helyezni. A szolgáltatás kibocsátásának előkészítésével a régiókban történő üzembe helyezés előtt azonosíthat potenciális problémákat. Ha már nincs szüksége egy szakaszos bevezetést, a felesleges óvintézkedéseket, használja a standard [központi telepítési beállítások](resource-group-template-deploy-portal.md) a Resource Managerhez. Deployment Manager zökkenőmentesen integrálható az összes meglévő külső eszközökkel, amelyek támogatják a Resource Manager-környezetek, például a folyamatos integrációt és folyamatos készregyártás (CI/CD) ajánlatok. 

Az Azure Deployment Manager privát előzetes verzióban érhető el. Az Azure Deployment Manager használatára, végezze el a [bejelentkezési űrlap kitöltése](https://aka.ms/admsignup). Súgó be javítják a szolgáltatást azáltal, hogy [visszajelzés](https://aka.ms/admfeedback).

Deployment Manager használatához szüksége négy fájlok létrehozásához:

* Topológia sablon
* Bevezetés a sablon
* Topológia alkalmazásparaméter-fájlt
* A paraméterfájl bevezetéséhez

A bevezetési sablon üzembe helyezése előtt telepíti a topológia sablont.

Az Azure Deployment Manager REST API-referenciában találhat [Itt](https://docs.microsoft.com/rest/api/deploymentmanager/).

## <a name="supported-locations"></a>A támogatott helyek

Az előzetes verzióra a Deployment Manager-erőforrásokat az USA középső RÉGIÓJA és USA keleti RÉGIÓJA 2 támogatottak. A topológia és a bevezetés sablonok, például a szolgáltatás egységek, összetevő forrásai és az ebben a cikkben leírt kibocsátások az erőforrások meghatározása során meg kell adnia a helyét a díjmentességük egyikét. Azonban úgy, hogy a szolgáltatás, például a virtual machines, a storage-fiókok és a web apps szolgáltatásban, hozzon létre az erőforrások által támogatott összes azok [nem szabványos helyekre](https://azure.microsoft.com/global-infrastructure/services/?products=all).  

## <a name="identity-and-access"></a>Identitás- és hozzáférés-kezelés

A Deployment Manager egy [felhasználó által hozzárendelt felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) az üzembe helyezési műveleteket hajtja végre. Ez az identitás a telepítés megkezdése előtt hozzon létre. Ez az előfizetés, a szolgáltatás telepíti, és a telepítés befejezéséhez szükséges engedélyeket hozzáféréssel kell rendelkeznie. A szerepkörök megadás műveleteivel kapcsolatos információkért lásd: [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md).

Az identitás a támogatott helyek a Deployment Manager kell lennie, és a bevezetés ugyanazon a helyen kell lennie.

## <a name="topology-template"></a>Topológia sablon

A topológia sablont ismerteti az Azure-erőforrások, amelyek a szolgáltatás és az üzembe helyezés helyszíne alkotják. Az alábbi képen egy példa szolgáltatás topológiát mutatja be:

![Hierarchia szolgáltatás topológia szolgáltatás egységek szolgáltatásokhoz](./media/deployment-manager-overview/service-topology.png)

A topológia sablon a következő forrásokat tartalmazza:

* Összetevő-forrás – a Resource Manager-sablonok és a paraméterek tárolására
* Szolgáltatás-topológia – mutat artifact-source
  * Szolgáltatások – Itt adhatja meg a hely és az Azure-előfizetés azonosítója
    * Egységek szolgáltatás – Itt adhatja meg az erőforráscsoport üzembe helyezési mód és sablonnal és paraméterfájlokkal fájl elérési útja

Szeretné megtudni, mi történik, ha az egyes fenyegetési, hasznos lehet adnia értékek megtekintéséhez.

![Az egyes szintek](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>A sablonok összetevő-forrás

A topológia sablont hoz létre egy összetevő-forrás, amely a sablonokat és paraméterfájlt fájlokat tárol. A összetevő lehetőséget kérje le a fájlokat a központi telepítés forrása. Láthatja, hogy egy másik összetevő bináris fájljait, a cikk későbbi részében forrása.

Az alábbi példa bemutatja az összetevő-forrás általános formátumát.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
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

További információkért lásd: [artifactSources sablonreferenciája](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Szolgáltatástopológia

Az alábbi példa bemutatja a szolgáltatás topológia erőforrás általános formátumát. Azt adja meg az összetevő forrás, amely rendelkezik a sablonokat és paraméterfájlt az erőforrás-azonosítója. A szolgáltatás a topológia összes szolgáltatás-erőforrást tartalmaz. Ahhoz, hogy az összetevő-forrás érhető el, a szolgáltatás topológia függ tőle.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

További információkért lásd: [serviceTopologies sablonreferenciája](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Szolgáltatások

Az alábbi példa bemutatja a szolgáltatások erőforrás általános formátumát. Az egyes Service-ben adja meg a hely és az Azure előfizetés-azonosító használata a szolgáltatás telepítéséhez. Üzembe helyezés több régióban, a szolgáltatás minden olyan régió esetében határozza meg. A szolgáltatás a szolgáltatás topológia függ.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
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

További információkért lásd: [sablonreferenciája services](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Szolgáltatási egység

Az alábbi példa bemutatja a szolgáltatás egységek erőforrás általános formátumát. Minden szolgáltatás egységben, adja meg az erőforráscsoportot, a [üzembe helyezési mód](deployment-modes.md) használata a központi telepítés és a sablonnal és paraméterfájlokkal fájl elérési útját. A sablon és paraméterek egy relatív elérési utat ad meg, ha a teljes elérési útja abból a gyökérmappából, az összetevők forrás jön létre. A sablon és paraméterek abszolút elérési utat is megadhat, de a verziók nem lesz lehetősége könnyen verzióra. A szolgáltatás egység szolgáltatástól függ.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
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

Mindegyik sablon tartalmaznia kell a kapcsolódó erőforrásokat, amely egyetlen lépésben telepíteni kívánja. Például egy service egység lehet egy sablont, amely üzembe helyezi a a szolgáltatás előtérbeli erőforrásait.

További információkért lásd: [serviceUnits sablonreferenciája](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Bevezetés a sablon

A bevezetési sablon a szolgáltatás telepítéséhez elvégzendő lépéseket ismerteti. A szolgáltatás topológia használhatja, és üzembe helyezéséhez szolgáltatás egységek sorrendben adhatja meg. Ez magában foglalja egy összetevő forrás a központi telepítés bináris fájlok tárolására. A bevezetési sablonban adja meg a következő hierarchia:

* Összetevő-forrás
* Lépés
* Bevezetés
  * Lépés csoportok
    * Üzembehelyezési műveletek

Az alábbi képen látható a hierarchiában a bevezetési sablon:

![Bevezetés a hierarchia lépéseivel](./media/deployment-manager-overview/Rollout.png)

Minden egyes bevezetési számos lépés csoportok rendelkezhet. Minden lépés csoport rendelkezik egy központi telepítési művelettel, amely egy szolgáltatás egységet a szolgáltatás topológiát mutat.

### <a name="artifact-source-for-binaries"></a>Összetevő bináris fájljait forrása

A bevezetési sablont hoz létre a bináris fájlok, üzembe kell helyeznie a szolgáltatás-összetevő forrását. Az összetevő-forrás hasonlít a [összetevő forrás sablonok](#artifact-source-for-templates), azzal a különbséggel, hogy a parancsfájlok, weboldalak, lefordított kódot vagy a szolgáltatás számára szükséges egyéb fájlokat tartalmazza.

### <a name="steps"></a>Lépések

Meghatározhat egy lépés előtt vagy után a központi telepítési művelet végrehajtásához. Jelenleg csak a `wait` lépés érhető el. A várakozási lépés felfüggeszti a telepítés folytatása előtt. Lehetővé teszi, hogy ellenőrizze, hogy a szolgáltatás a következő szolgáltatás egység üzembe helyezése előtt várt módon fut-e. Az alábbi példa bemutatja egy várakozási lépés általános formátumát.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
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

Az időtartam tulajdonságot használja [ISO 8601 szabványnak](https://en.wikipedia.org/wiki/ISO_8601#Durations). Az előző példában egy egy perces várakozás megadja.

További információkért lásd: [sablonreferenciája lépések](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Kibocsátások

Ahhoz, hogy az összetevő-forrás érhető el, a bevezetés függ tőle. A bevezetési csoportok lépéseket minden egyes üzembe helyezett szolgáltatás egység határozza meg. Megadhatja a telepítés előtt vagy után végrehajtandó műveleteket. Megadhatja például, hogy az üzembe helyezés várjon a szolgáltatás egység üzembe helyezése után. Megadhatja, hogy a lépés csoportok sorrendjét.

Az identitás objektum adja meg a [felhasználó által hozzárendelt felügyelt identitás](#identity-and-access) , amely a központi telepítési műveleteket hajtja végre.

Az alábbi példa bemutatja a bevezetés általános formátumát.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
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

További információkért lásd: [kibocsátások sablonreferenciája](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Alkalmazásparaméter-fájlt

A paraméter két fájlt hoz létre. Egy alkalmazásparaméter-fájlt a szolgáltatás topológia telepítésekor használatos, és a másik a bevezetési központi telepítéshez használt. Nincsenek az kell, hogy egyes értékek azonosak mindkét paraméter fájlban.  

## <a name="containerroot-variable"></a>containerRoot változó

A rendszerverzióval ellátott telepítések esetén a összetevők módosításokat az új verziók elérési útját. Lehet, hogy az első futtatásakor egy központi telepítést az elérési út `https://<base-uri-blob-container>/binaries/1.0.0.0`. Érdemes lehet másodszor `https://<base-uri-blob-container>/binaries/1.0.0.1`. Deployment Manager egyszerűbbé teszi a megfelelő Útvonalgyökér első a jelenlegi üzemelő példány használatával az `$containerRoot` változó. Ez az érték minden egyes verziójában módosítja, és nem ismeri az üzembe helyezés előtt.

Használja a `$containerRoot` változó a sablon üzembe helyezése az Azure-erőforrások alkalmazásparaméter-fájlt. Központi telepítéskor ezzel a változóval tényleges a bevezetés értékeivel váltja fel. 

Kibocsátás közben, hozzon létre például egy összetevő forrás a bináris összetevők.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
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

Figyelje meg a `artifactRoot` és `sasUri` tulajdonságait. Az összetevő legfelső szintű értékének például be lehet állítani `binaries/1.0.0.0`. A SAS URI-ja az URI-t a storage-tároló hozzáférés egy SAS-jogkivonat használatával. Deployment Manager automatikusan hoz létre az értékét a `$containerRoot` változó. Amely ötvözi az ezeket az értékeket a következő formátumban `<container>/<artifactRoot>`.

A sablonnal és paraméterfájlokkal fájl helyes elérési útját a rendszerverzióval ellátott bináris fájlok első ismernie kell. Fájlok egy webalkalmazás üzembe helyezéséhez hozzon létre például a következő paraméterfájl $containerRoot változóval. Két fordított perjelet kell használni (`\\`) elérési úthoz, mert az első az escape-karakter.

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

Ezután használja a paraméter a sablonban:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
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

Új mappák és a legfelső szintű a kibocsátás közben való átadásához rendszerverzióval ellátott központi telepítések, kezelhető. Az elérési utat, amely üzembe helyezi az erőforrásokat a sablonhoz halad keresztül.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, Deployment Manager. Folytassa a következő cikkben megtudhatja, hogyan helyezhet üzembe a Deployment Manager.

> [!div class="nextstepaction"]
> [Oktatóanyag: Használja az Azure Deployment Manager Resource Manager-sablonokkal](./deployment-manager-tutorial.md)