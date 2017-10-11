---
title: "Hivatkozás a sablonokat az Azure-telepítés |} Microsoft Docs"
description: "Ismerteti az Azure Resource Manager sablon kapcsolt sablonok segítségével moduláris sablon megoldás létrehozása. Bemutatja, hogyan továbbítsa a paraméterértéket, adja meg a paraméter fájlt, és dinamikusan létrehozott URL-címeket."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Azure-erőforrások telepítésekor kapcsolt sablonok használata
A belül egy Azure Resource Manager sablon hozzákapcsolhatja egy másik sablont, amely lehetővé teszi, hogy a központi telepítés egy felbontani megcélzott, cél-specifikus sablonok. Csakúgy, mint egy alkalmazás több kód osztályokba decomposing, a felbontás ellen tesztelése, újbóli és olvashatóság előnyt kínál.  

Átadhatók fő sablonból paraméterek csatolt sablont, és ezeket a paramétereket közvetlenül hozzárendelhető paraméterek és változók jelennek meg, ha a hívó sablont. A csatolt sablont is eltelhet egy kimeneti változó vissza a Forrássablon a sablonok között kétirányú adatcsere engedélyezése.

## <a name="linking-to-a-template"></a>A sablon csatolása
Létrehozhat egy hivatkozást a fő sablont, amely a csatolt sablon mutat található központi telepítési erőforráshoz hozzáadásával két sablonok között. Beállíthatja a **templateLink** URI-azonosítója a csatolt sablon tulajdonságot. A csatolt sablon közvetlenül a sablonban vagy egy paraméterfájl biztosítható a paraméterértékek. Az alábbi példában a **paraméterek** tulajdonságot közvetlenül adja meg a paraméter értékét.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Más típusú erőforrások, például a csatolt sablont és egyéb erőforrások közti függőségeket is beállíthatja. Ezért ha más erőforrásokhoz szükséges egy kimeneti értéket, a csatolt sablonból, biztosíthatja, a csatolt sablon előtt történik. Vagy a csatolt sablon más erőforrások támaszkodik, ha biztos lehet benne, más erőforrások telepítése előtt a csatolt sablont. Egy érték beolvasható egy csatolt sablon a következő szintaxissal:

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

Az erőforrás-kezelő szolgáltatás eléréséhez a csatolt sablon képesnek kell lennie. Nem adhat meg egy helyi fájl vagy a fájl, amely csak akkor érhető el a csatolt sablon a helyi hálózaton. Csak adja meg, amely tartalmazza az vagy URI érték **http** vagy **https**. Egy elem helyezze el a csatolt sablon egy tárfiókot, és az URI használata, hogy az elem, például az alábbi példában látható módon:

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Bár a csatolt sablon külsőleg elérhetőnek kell lennie, nem kell lennie a nyilvános általánosan elérhető. A sablon a személyes storage-fiók, amely csak a fiók tulajdonosa számára hozzáférhető is hozzáadhat. Ezután hozzon létre egy közös hozzáférésű jogosultságkód (SAS) token hozzáférés engedélyezése a telepítés során. A SAS-token hozzáadása az URI a csatolt sablon. A sablont a storage-fiók beállítása és SAS-token létrehozása lépéseiért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](resource-group-template-deploy.md) vagy [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](resource-group-template-deploy-cli.md). 

A következő példa bemutatja a szülő sablon egy másik sablon mutató. A csatolt sablon paraméterként átadott SAS-jogkivonat segítségével érhető el.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Annak ellenére, hogy a jogkivonat érték az átadott egy biztonságos karakterláncot, URI-azonosítója a csatolt sablon, beleértve a SAS-jogkivonat a telepítési műveleteket rögzíti. Korlátozható a támadóknak, beállíthatja a egy lejárati idejét, a jogkivonat esetében.

Erőforrás-kezelő ennek egy külön központi telepítés minden egyes csatolt sablon kezeli. A központi telepítési előzmények ahhoz az erőforráscsoporthoz tekintse meg a szülő és a beágyazott sablonok külön központi telepítése.

![telepítési előzmények](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>A paraméterfájl csatolása
A következő példában a **parametersLink** tulajdonság egy paraméter fájlra való hivatkozáshoz.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

A csatolt paraméterfájl URI értéke nem lehet egy helyi fájl, és tartalmaznia kell vagy **http** vagy **https**. A paraméterfájl is lehet korlátozni a SAS-jogkivonat-en keresztüli hozzáférés.

## <a name="using-variables-to-link-templates"></a>Változók használata sablonok
Az előző példák azt szemléltették, hogy a sablon hivatkozások kódolt URL-cím értékeket. Ez a módszer egy egyszerű sablon esetében is működik, de nem működik jól, ha nagy számú moduláris sablonok használata. Ehelyett hozzon létre egy statikus változó, amely tárolja a fő sablon alap URL-címet, és majd hozható létre dinamikusan URL-címeket az alap URL-címet a kapcsolt sablonok. Ez a megközelítés előnye, egyszerűen áthelyezheti vagy oszthatja ketté a sablont, mert csak módosítani szeretné a statikus változó a fő sablonban. A fő sablont a megfelelő URI-k teljes lebontott sablon továbbítja.

A következő példa bemutatja, hogyan két URL-címéből kapcsolt sablonok létrehozásához használja az alap URL-cím (**sharedTemplateUrl** és **vmTemplate**). 

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Is [deployment()](resource-group-template-functions-deployment.md#deployment) az alap URL-CÍMÉT az aktuális sablon, és azt használja az URL-cím lekérésére más sablonok ugyanazon a helyen. Ez a módszer akkor hasznos, ha a sablon helye megváltozik (lehet, hogy miatt versioning), vagy el szeretné kerülni a merevlemez kódolási URL-címek a sablon fájlban. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Teljes példa
Az alábbi példa sablonok kapcsolt sablonok egyszerűsített elrendezésének, több cikkben fogalmak szemléltetésére megjelenítése. Azt feltételezi, hogy a sablonok lettek hozzáadva a tárfiók ugyanabban a tárolóban, hozzáférésű ki van kapcsolva. A csatolt sablon értéket átadja vissza a fő-sablon a **kimenete** szakasz.

A **parent.json** fájl áll:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

A **helloworld.json** fájl áll:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

PowerShell, a szolgáltatáshitelesítést egy token ahhoz a tárolóhoz, és telepítse központilag a sablon is van:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Az Azure CLI 2.0 szolgáltatáshitelesítést egy token ahhoz a tárolóhoz, és telepítse központilag a sablonok a következő kóddal:

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="next-steps"></a>Következő lépések
* A telepítési sorrendet, az erőforrások meghatározása, lásd: [függőségek meghatározása az Azure Resource Manager-sablonok](resource-group-define-dependencies.md)
* Adja meg egy erőforrást, de több példányát létrehozni, lásd: [erőforrások több példányát az Azure Resource Manager létrehozása](resource-group-create-multiple.md)

