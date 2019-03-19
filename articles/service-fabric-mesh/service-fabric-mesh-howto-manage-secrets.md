---
title: Az Azure Service Fabric háló titkos Alkalmazáskulcsok kezelése |} A Microsoft Docs
description: Titkos Alkalmazáskulcsok kezelése, így biztonságosan létrehozása és üzembe helyezése Service Fabric-háló.
services: service-fabric-mesh
keywords: titkos kódok
author: aljo-microsoft
ms.author: aljo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: 36d0b49f1b9fb1ca5d13283146d134137a5cb028
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900641"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Service Fabric háló titkos Alkalmazáskulcsok kezelése
Titkos kódok Service Fabric-háló támogatja az Azure-erőforrások. Service Fabric-háló titkos lehet például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek tárolása és biztonságosan továbbítani kell bizalmas információkat. Ez a cikk bemutatja, hogyan használhatja a Service Fabric biztonságos Store szolgáltatás üzembe helyezése és karbantartása a titkos kulcsok.

A háló alkalmazás titkos kulcs áll:
* A **titkok** erőforrás, amely egy tároló, amely tárolja a szöveg titkos kulcsok. Tárolt titkos kulcsok a **titkok** erőforrás tárolása és biztonságosan továbbítani.
* Egy vagy több **titkos kulcsok/értékek** tárolt erőforrások a **titkok** az erőforrás-tárolónak. Minden egyes **titkos kulcsok/értékek** erőforrás van megkülönböztető verziószámmal. Egy verziója nem módosítható egy **titkos kulcsok/értékek** erőforrás, csak fűzze hozzá az új verzióra.

A következő lépésekből áll, titkos kódok kezelése:
1. Deklarálja a háló **titkok** egy Azure Resource modell YAML- vagy JSON-táblázatot inlinedValue kind és SecretsStoreRef contentType definíciók-erőforrást.
2. Deklarálja a háló **titkos kulcsok/értékek** erőforrásokat egy lesz tárolva az Azure Resource modell YAML- vagy JSON-fájlban a **titkok** erőforrás (az 1. lépés).
3. Módosítsa a háló alkalmazást való hivatkozáshoz háló titkos értékkel.
4. Üzembe helyezése vagy működés közbeni frissítése a háló alkalmazás titkos értékkel felhasználásához.
5. Az Azure "az" parancssori felület parancsai biztonságos Store szolgáltatás életciklus-felügyelet.

## <a name="declare-a-mesh-secrets-resource"></a>Deklarálja a háló titkok erőforrás
Titkos kódok háló erőforrás deklarálva van egy Azure Resource modell JSON vagy inlinedValue kind és SecretsStoreRef contentType definíciók YAML-fájlt. A háló titkok erőforrás támogatja-e biztonságos Store szolgáltatás forráskódú titkos kulcsok. 
>
A következő egy példa bemutatja, hogyan egy JSON-fájlt a háló titkok erőforrások deklarálásához:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
  ]
}
```
A következő egy példa bemutatja, hogyan egy YAML-fájlt a háló titkok erőforrások deklarálásához:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarálja a titkos kulcsok/értékek háló-erőforrások
Háló titkos kulcsok/értékek erőforrások függőségi viszonyban az előző lépésben meghatározott erőforrásokat használja a háló titkos kulcsok vannak.

"Resources" szakasz közötti kapcsolat vonatkozó "érték:" és "neve:" mezők: második része a "név:" karakterlánca kettősponttal elválasztva a verziószám használni egy titkos kulcsot, és a neve, mielőtt a vessző után meg kell felelnie a háló titkos érték, amelynek van egy függőség. Például az elem ```name: mysecret:1.0```, a verziószám az 1.0-s és a név ```mysecret``` meg kell egyeznie a korábban meghatározott ```"value": "mysecret"```.

>
A következő egy példa bemutatja, hogyan egy JSON-fájlt a háló titkos kulcsok/értékek erőforrások deklarálásához:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "my-secret-value-v1": {
      "type": "string",
      "metadata": {
        "description": "My Mesh Application Secret Value."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "mysecret:1.0",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        'Microsoft.ServiceFabricMesh/secrets/MySecret.txt'
      ],
      "properties": {
        "value": "[parameters('my-secret-value-v1)]"
      }
    }
  ]
}
```
A következő egy példa bemutatja, hogyan egy YAML-fájlt a háló titkos kulcsok/értékek erőforrások deklarálásához:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Háló alkalmazás hivatkozni a háló titkos kulcs-érték módosítása
Service Fabric-háló alkalmazásokat kell vegye figyelembe a következő két karakterláncot felhasználhatja a biztonságos Store szolgáltatás titkos kulcs-értékeket:
1. Microsoft.ServiceFabricMesh/Secrets.name tartalmazza a fájl nevét, és egyszerű szövegként a titkos értéket tartalmazza.
2. A Windows vagy Linux-alapú környezeti változó "Fabric_SettingPath" tartalmazza a könyvtár elérési útja, ahol Store szolgáltatás titkok biztonságos értékeket tartalmazó fájlok lesznek elérhetők. Ez a "C:\Settings" Windows-ban üzemeltetett és a "/ var /" a háló Linux-ban üzemeltetett alkalmazások beállításait jelölik.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Telepítse, vagy használja a működés közbeni frissítés háló alkalmazás titkos értékkel felhasználásához
Titkos kódok és/vagy a verzióval ellátott titkos kulcsok/értékek létrehozása központi telepítések deklarált erőforrásmodell korlátozva. Az egyetlen módja az erőforrások létrehozásához, egy erőforrás modell JSON- vagy YAM fájl használatával átadásával a **az háló üzembe helyezési** paranccsal a következőképpen:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Biztonságos Store szolgáltatás-életciklus kezelésének Azure parancssori felület parancsai

### <a name="create-a-new-secrets-resource"></a>Új titkos kulcsok létrehozása
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Adja át vagy **-sablonfájl** vagy **sablon-uri** (de nem mindkettőt).

Példa:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- Hozzon létre az háló üzembe helyezés – https://www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Titkos kód megjelenítése
A leírás, a titkos kulcsot (de nem a értéket) adja vissza.
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Titkos kulcs törlése

- Titkos kulcs nem törölhető, amíg a háló alkalmazások hivatkozott.
- Titkos kódok erőforrás törlésével törli az összes titkos kódok és erőforrások verzió.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Előfizetés titkos kulcsainak listázása
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Erőforráscsoport titkos kulcsainak listázása
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Az összes titkos kulcs verzióinak listázása
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Titkos kód verziója érték megjelenítése
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Titkos kód verziója érték törlése
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>További lépések 
Service Fabric-háló kapcsolatos további információkért olvassa el a áttekintése:
- [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md)
