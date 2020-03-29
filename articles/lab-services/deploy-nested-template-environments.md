---
title: Beágyazott sablonkörnyezetek üzembe helyezése az Azure DevTest Labsben
description: Megtudhatja, hogyan helyezheti üzembe egymásba ágyazott Azure Resource Manager-sablonokat, hogy az Azure DevTest Labs környezeteket biztosítson.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168821"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Beágyazott Azure Resource Manager-sablonok telepítése tesztelési környezetekben
A beágyazott központi telepítés lehetővé teszi, hogy más Azure Resource Manager-sablonokat hajtson végre egy fő Erőforrás-kezelő sablonon belül. Lehetővé teszi, hogy a központi telepítést célzott és célspecifikus sablonokkészleté bomlasztja. Előnyöket biztosít a tesztelés, újrafelhasználás és olvashatóság szempontjából. A cikk [csatolt sablonok használata azure-erőforrások üzembe helyezésekor](../azure-resource-manager/templates/linked-templates.md) nyújt jó áttekintést nyújt a megoldás több kódmintákat. Ez a cikk egy példa, amely az Azure DevTest Labs. 

## <a name="key-parameters"></a>Fő paraméterek
Bár a saját Resource Manager-sablont a semmiből hozhatja létre, azt javasoljuk, hogy használja az [Azure Resource Group projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a Visual Studióban, amely megkönnyíti a sablonok fejlesztését és hibakeresését. Ha egymásba ágyazott központi telepítési erőforrást ad hozzá az azuredeploy.json hoz, a Visual Studio több elemet ad hozzá a sablon rugalmasabbá tétele érdekében. Ezek közé tartozik a másodlagos sablont és paraméterfájlt tartalmazó almappa, a fő sablonfájlon belüli változónevek, valamint az új fájlok tárolási helyének két paramétere. A **_artifactsLocation** és **a _artifactsLocationSasToken** a DevTest Labs által használt legfontosabb paraméterek. 

Ha nem ismeri a DevTest Labs környezetekkel való működését, olvassa [el a Többvirtuális gépkörnyezetek és PaaS-erőforrások létrehozása az Azure Resource Manager-sablonokkal című témakört.](devtest-lab-create-environment-from-arm.md) A sablonok a DevTest Labs laborhoz kapcsolódó tárházban tárolódnak. Amikor új környezetet hoz létre ezekkel a sablonokkal, a fájlok átkerülnek egy Azure Storage-tárolóba a laborban. A beágyazott fájlok azonosításához és másolásához a DevTest Labs azonosítja a _artifactsLocation és _artifactsLocationSasToken paramétereket, és az almappákat a tárolótárolóba másolja. Ezután automatikusan beilleszti a hely- és a megosztott hozzáférésű aláírás (SaS) jogkivonatot a paraméterekbe. 

## <a name="nested-deployment-example"></a>Példa beágyazott központi telepítésre
Íme egy egyszerű példa a beágyazott telepítésre:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

A sablont tartalmazó tárházban található mappa `nestedtemplates` almappája a **NestOne.json** és a **NestOne.parameters.json**fájlokkal rendelkezik. Az **azuredeploy.json,** uri a sablon épül a műtermékek helye, beágyazott sablon mappa, beágyazott sablon fájl neve. Hasonlóképpen a paraméterek URI-ja a műtermékek helye, a beágyazott sablonmappa és a beágyazott sablon paraméterfájlja alapján épül fel. 

Itt van a kép az azonos projekt struktúra Visual Studio: 

![Projektstruktúra a Visual Studióban](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Az elsődleges mappában további mappákat is hozzáadhat, de nem mélyebbmappákat, mint egyetlen szint. 

## <a name="next-steps"></a>További lépések
A környezetekkel kapcsolatos részleteket az alábbi cikkekben találja: 

- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata az Azure DevTest Labsben](devtest-lab-configure-use-public-environments.md)
- [Környezet csatlakoztatása a labor virtuális hálózatához az Azure DevTest Labs ben](connect-environment-lab-virtual-network.md)