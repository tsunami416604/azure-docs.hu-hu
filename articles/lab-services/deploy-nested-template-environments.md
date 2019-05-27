---
title: Üzembe helyezése Resource Manager sablon beágyazott környezetekben, az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure DevTest Labs környezetek biztosításához beágyazott Azure Resource Manager-sablonok.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835285"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Tesztelési környezetben beágyazott Azure Resource Manager-sablonok üzembe helyezése
Egy beágyazott üzemelő példány segítségével hajtsa végre a fő Resource Manager-sablonnal belül más az Azure Resource Manager-sablonok. Lehetővé teszi, hogy az üzemelő példány egy csoportba a megcélzott és a cél-specifikus sablonok bontható fel. Tesztelés, újrafelhasználás és olvashatóság tekintetében előnyökkel szolgál. A cikk [kapcsolt sablonok használata az Azure-erőforrások üzembe helyezésekor](../azure-resource-manager/resource-group-linked-templates.md) Ez a megoldás szakasz hasznos áttekintést biztosít számos mintakódot. Ez a cikk egy példa, amely kifejezetten az Azure DevTest Labs szolgáltatásban. 

## <a name="key-parameters"></a>Fő paraméterekkel
Teljesen új saját Resource Manager-sablont létrehozni, azt javasoljuk, hogy használja a [Azure erőforráscsoport-projekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) a Visual Studióban, amely megkönnyíti a fejlesztésről és hibakeresésről sablonokat. Egy beágyazott üzemelő példány erőforráshoz hozzá azuredeploy.json, a Visual Studio hozzáadja, a sablon rugalmasabb több elemet. Ezek az elemek közé tartozik a almappát a másodlagos sablon és paraméterek fájl, a változók nevében a fő sablonfájl belül és két paramétert az új fájlok tárolási helyét. A **_artifactsLocation** és **_artifactsLocationSasToken** kulcs paraméterei, amely a DevTest Labs használja. 

Ha nem ismeri a DevTest Labs-környezetek működését, olvassa el [több virtuális gépes környezet és PaaS-erőforrások létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md). A sablonok a tárház a DevTest Labs szolgáltatásban létrehozott tesztkörnyezet csatolva vannak tárolva. Ha ezeket a sablonokat hoz létre egy új környezetet, a fájlok át egy Azure Storage-tárolóba, a tesztkörnyezetben. Tudják azonosítani, és másolja a beágyazott fájlokat, a DevTest Labs a _artifactsLocation és _artifactsLocationSasToken paraméterek azonosítja, és az almappák, akár a storage-tárolóba másol. Ezután azt automatikusan átmásolja a hely és a közös hozzáférésű Jogosultságkód (SaS-) token paraméterek. 

## <a name="nested-deployment-example"></a>Beágyazott központi telepítés példája
Íme egy beágyazott üzemelő példány egyszerű példát:

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

A mappa a tárházban, a sablont tartalmazó könyvtárban van egy almappát `nestedtemplates` azokkal a fájlokkal **NestOne.json** és **NestOne.parameters.json**. Az a **azuredeploy.json**, URI esetében a sablon az összetevők helyét, és a beágyazott Szolgáltatássablon mappáját, és beépített beágyazva sablon fájl neve. Ehhez hasonlóan URI-t a paraméterek a beágyazott sablon összetevők helyének, beágyazott Szolgáltatássablon mappáját, és alkalmazásparaméter-fájlt használatával lett összeállítva. 

A Visual Studióban az azonos projektstruktúra képe a következő: 

![A Visual Studióban projektstruktúra](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

További mappákat is hozzáadhat, az elsődleges mappában, de nem minden mélyebben egyszintű. 

## <a name="next-steps"></a>További lépések
Részletes információ a környezetekről a következő cikkekben talál: 

- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Konfigurálhatja és használhatja a nyilvános környezetben az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-use-public-environments.md)
- [Környezet csatlakoztatása a labor virtuális hálózathoz az Azure DevTest Labs szolgáltatásban](connect-environment-lab-virtual-network.md)