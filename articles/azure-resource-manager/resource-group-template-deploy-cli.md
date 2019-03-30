---
title: Erőforrások üzembe helyezése az Azure CLI és a sablon |} A Microsoft Docs
description: Azure Resource Manager és az Azure CLI használatával helyezhet üzembe erőforrásokat az Azure-bA. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 92476f9ac48c168c3bbe85d4da49b6afe034c117
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648656"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével

Ez a cikk ismerteti az Azure CLI használata a Resource Manager-sablonok, az erőforrások üzembe helyezése az Azure. Ha nem ismeri a fogalmait, üzembe helyezése és kezelése az Azure-megoldások, lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Ha nem rendelkezik Azure CLI telepítve van, használhatja a [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Üzembe helyezés hatálya

A központi telepítést az Azure-előfizetés és a egy erőforráscsoportot egy előfizetésen belül célba. A legtöbb esetben egy erőforráscsoportba irányuló üzembe helyezés céljaként meghatározott lesz. Előfizetések üzemelő példányai használatával alkalmazza a házirendeket és a szerepkör-hozzárendelések az előfizetésből. Hozzon létre egy erőforráscsoportot, és üzembe helyezni erőforrásokat, előfizetés központi telepítéseket is használ. Az üzembe helyezés, függően különböző parancsokat használhatja.

Központi telepítése egy **erőforráscsoport**, használjon [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Központi telepítése egy **előfizetés**, használjon [az üzembe helyezés létrehozása](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Ebben a cikkben szereplő példák erőforráscsoportok üzemelő példányainak használja. Előfizetések üzemelő példányai kapcsolatos további információkért lásd: [erőforráscsoport és erőforrások létrehozásához az előfizetés szintjén](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

Erőforrások üzembe helyezésekor az Azure-ba, hogy:

1. Jelentkezzen be az Azure-fiókjába
2. Hozzon létre egy erőforráscsoportot, amely a telepített erőforrások tárolójaként szolgál. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójelet tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet ponttal.
3. Az erőforráscsoport üzembe a sablont, amely meghatározza az erőforrások létrehozása

A sablon paramétereit, amelyek lehetővé teszik az üzembe helyezés testreszabása tartalmazhat. Például megadhat értékeket, amelyek egy adott környezetben (például fejlesztési, tesztelési és éles környezetben). A mintasablon a tárfiók SKU paraméter határozza meg. 

Az alábbi példa létrehoz egy erőforráscsoportot, és üzembe helyezi a sablont a helyi gépen:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

Resource Manager-sablonok tárolása a helyi gépén, helyett érdemesebb lehet külső helyen tárolja őket. Sablonok verziókövetési adattár (például a GitHub) tárolhatja. Vagy tárolhatja őket az Azure storage-fiók, a közös hozzáférésű a szervezetben.

Egy külső sablon üzembe helyezéséhez használja a **sablon-uri** paraméter. A példában az URI használatával helyezhet üzembe a mintául szolgáló sablont a Githubból.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Az előző példában a sablon, amely a legtöbb esetben működik, mivel a sablon ne tartalmazza a bizalmas adatokat egy nyilvánosan elérhető-e URI-t igényel. Adja meg a bizalmas adatok (például egy rendszergazdai jelszót) van szüksége, ha biztonságos paraméterként adja át ezt az értéket. Azonban ha nem szeretné a sablon nyilvánosan hozzáférhető, segítségével megvédheti azokat a személyes tárolót tárolja őket. Egy közös hozzáférésű jogosultságkód (SAS) igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

A Cloud shellben használja az alábbi parancsokat:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Telepítse újra a központi telepítésének hibája esetén

Ez a funkció más néven van *visszaállítási hiba*. Ha egy központi telepítés nem sikerül, automatikusan is újratelepítése egy korábbi, a sikeres telepítés az üzembe helyezési előzményekből. Újbóli üzembe helyezés megadásához használja a `--rollback-on-error` paramétert a üzembe helyezés parancsba. Ez a funkció akkor hasznos, ha rendelkezik egy korábbi hibátlan állapotra van az infrastruktúra telepítése és akarja, hogy vissza kell állítani az. Nincsenek figyelmeztetések és korlátozások:

- Az újbóli üzembe helyezés pontosan, ahogy korábban már volt futtatva ugyanazokkal a paraméterekkel futtatja. A paraméterek nem módosítható.
- A korábbi központi telepítés segítségével futtatja a [teljes mód](./deployment-modes.md#complete-mode). Nem szerepel a korábbi központi telepítés minden erőforrás törlődni, és a minden erőforrás-konfigurációt az előző állapotukba. Győződjön meg arról, hogy megértette a [üzembe helyezési mód](./deployment-modes.md).
- Az újratelepítés csak érinti az erőforrásokat, a módosításokat adatokat nem érinti.
- Ez a funkció csak az erőforráscsoport központi telepítések, nem előfizetési szintű központi telepítések támogatott. Előfizetés-szintű üzembe helyezéssel kapcsolatos további információkért lásd: [erőforráscsoport és erőforrások létrehozásához az előfizetés szintjén](./deploy-to-subscription.md).

Ez a beállítás használatához az üzemelő példányok egyedi névvel kell rendelkezniük az előzményekben található ellenőrizhető, hogy. Ha nem rendelkezik egyedi nevét, az aktuális telepítése sikertelen volt. a korábban sikeresen végrehajtott központi telepítés előzményei esetleg felülírhatja. Használhatja ezt a beállítást csak a legfelső szintű telepítések. Beágyazott sablonból üzemelő példányok nem újbóli üzembe helyezés érhetők el.

Telepítse újra a legutóbbi sikeres üzembe helyezés, adja hozzá a `--rollback-on-error` paramétert, azt a jelzőt.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Az újratelepítés egy adott, használja a `--rollback-on-error` paramétert, és adja meg a központi telepítés nevét.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

A megadott központi telepítés sikeres volt kell rendelkeznie.

## <a name="parameters"></a>Paraméterek

Paraméterértékek átadni, vagy a beágyazott paraméterek, vagy egy paraméterfájl használható. Az előző példák ebben a cikkben a beágyazott paraméterek megjelenítése.

### <a name="inline-parameters"></a>A beágyazott paraméterek

A beágyazott paraméterek adhatók át, adja meg az értékeket `parameters`. Például egy karakterláncot és egy tömb átadása egy sablont egy Bash-felületen, használja:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Fájl tartalmának beolvasása, és adja meg a beágyazott paraméterként tartalom is.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

A paraméter értékének lekérése egy fájl akkor hasznos, ha meg kell adnia a konfigurációs értékeket. Megadhat például [Linux rendszerű virtuális gép értékeit a cloud-init](../virtual-machines/linux/using-cloud-init.md).

ArrayContent.json formátuma:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Alkalmazásparaméter-fájlok

Ahelyett, hogy a paraméterek átadása a parancsfájlban beágyazott értékekként, akkor előfordulhat, hogy egyszerűbb paraméter értékét tartalmazó JSON-fájl használata. A paraméterfájl egy helyi fájlba kell lennie. Külső alkalmazásparaméter-fájlok nem támogatottak az Azure CLI használatával.

A paraméterfájl a következő formátumban kell lennie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Figyelje meg, hogy a paraméterek szakasz tartalmazza-e a paraméter neve, amely megegyezik a paraméter definiálva a sablonban (Tárfióktípus). A paraméterfájl a paraméter értékét tartalmazza. Ezt az értéket automatikusan kerülnek a sablon üzembe helyezése során. Hozzon létre több alkalmazásparaméter-fájlt, és akkor továbbítja a forgatókönyvnek megfelelő paraméterfájlban. 

Másolja ki az előző példában, és mentse a fájlt `storage.parameters.json`.

Adja át a helyi alkalmazásparaméter-fájlt, használja a `@` storage.parameters.json nevű helyi fájl megadásához.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>A paraméter sorrend

A beágyazott paraméterek és a egy helyi paraméterfájl azonos központi telepítési művelet is használhatja. Például néhány értéket adja meg a helyi alkalmazásparaméter-fájlt, és adja hozzá az egyéb értékek beágyazott üzembe helyezés során. Ha a paraméter a helyi alkalmazásparaméter-fájlt és a beágyazott értékeket ad meg, a beágyazott elsőbbséget.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>Egy sablon üzemelő példány tesztelése

A sablonnal és paraméterfájlokkal értékek teszteléséhez ténylegesen az olyan erőforrások telepítés nélkül használhatja a [az csoport központi telepítésének ellenőrzése](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Ha nincsenek hibák észlelhetők, a parancs visszaadja a teszt üzembe helyezéssel kapcsolatos adatokat. Különösen, vegye figyelembe, hogy a **hiba** null értékű.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Ha hibát észlel, a parancs hibaüzenetet ad vissza. Például a tárfiók SKU, helytelen értéket átadja adja vissza a következő hibával:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs visszaadja egy hibaüzenet, nem lehetett elemezni a sablont. Az üzenet azt jelzi, hogy a sor száma és az elemzési hiba pozícióját.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>További lépések

- Ebben a cikkben szereplő példák erőforrások üzembe helyezése az erőforráscsoporthoz, az alapértelmezett előfizetést. Használjon másik előfizetést, lásd: [több Azure-előfizetések kezelése](/cli/azure/manage-azure-subscriptions-azure-cli).
- Adja meg, hogyan legyen kezelve az erőforrásokat, az erőforráscsoportban létezik, de nincsenek definiálva a sablonban, lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).
- A sablonban szereplő paraméterekkel definiálása ismertetése: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
- Gyakori üzembehelyezési hibák elhárítása a tippek: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).
- SAS-token igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-cli-sas-token.md).
- Biztonságosan terjed ki a szolgáltatás több régióban, lásd: [Azure Deployment Manager](deployment-manager-overview.md).
