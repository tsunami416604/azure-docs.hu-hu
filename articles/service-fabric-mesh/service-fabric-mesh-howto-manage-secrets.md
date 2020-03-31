---
title: Az Azure Service Fabric mesh alkalmazás titkost készletei kezelése
description: Alkalmazástitok kezelése, így biztonságosan hozhat létre és telepíthet egy Service Fabric Mesh alkalmazást.
ms.date: 4/2/2019
ms.topic: conceptual
ms.openlocfilehash: d7946092a0bebe374404870fcd711ad33cc98b11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461932"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Szolgáltatásháló alkalmazásttkókainak kezelése
A Service Fabric Mesh támogatja a titkos kulcsokat Azure-erőforrásokként. A Service Fabric Mesh titkos lehet bármilyen bizalmas szöveges információkat, például a tárolási kapcsolat karakterláncok, jelszavak vagy más értékeket, amelyeket biztonságosan kell tárolni és továbbítani. Ez a cikk bemutatja, hogyan használhatja a Service Fabric biztonságos tároló szolgáltatás a titkos kulcsok üzembe helyezéséhez és karbantartásához.

A Mesh alkalmazás titkos sága a következőkből áll:
* A **Titkos kulcsok** erőforrás, amely egy tároló, amely tárolja a szöveges titkokat. A Titkos **kulcsok** erőforrásban található titkos kulcsok biztonságosan tárolódnak és továbbítódnak.
* Egy vagy több **titkos kulcsok/értékek** erőforrások, amelyek a **Titkos kulcsok** erőforrás tárolóban tárolt. Minden **Titkos kulcsok/értékek** erőforrást verziószám különböztetmeg. **A Titkos kulcsok/értékek** erőforrás verziója nem módosítható, csak új verziót fűzhet hozzá.

A titkos kulcsok kezelése a következő lépésekből áll:
1. Mesh **secrets** erőforrás deklarálása egy Azure Resource Model YAML vagy JSON-fájlban inlinedValue kind és SecretsStoreRef contentType definíciók használatával.
2. A titkos **kulcsok/értékek** deklarálása egy Azure Resource Model YAML- vagy JSON-fájlban, amely a **Titkos kulcsok** erőforrásban lesz tárolva (az 1. lépéstől).
3. Mesh alkalmazás módosítása a háló titkos értékére.
4. Telepítse vagy frissítse a hálóalkalmazást titkos értékek felhasználásához.
5. Használja az Azure "az" CLI parancsokat a Secure Store Service életciklus-kezeléséhez.

## <a name="declare-a-mesh-secrets-resource"></a>Hálótitkos kulcsok erőforrás deklarálása
A mesh secrets erőforrás egy Azure Resource Model JSON vagy YAML-fájlban deklarált inlinedValue kind definíció használatával. A mesh titkos kulcsok erőforrás támogatja a Secure Store szolgáltatás forrásból származó titkos kulcsokat. 
>
Az alábbi példa bemutatja, hogyan deklarálhatok hálótitkos kulcsok erőforrásokat egy JSON-fájlban:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Az alábbi példa bemutatja, hogyan deklarálhatok hálótitkos kulcsok erőforrásokat egy YAML-fájlban:
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

## <a name="declare-mesh-secretsvalues-resources"></a>Háló titkost/értékeinek deklarálása erőforrások
A háló titkok/értékek erőforrások az előző lépésben definiált hálótitkok erőforrásoktól függenek.

Az "erőforrások" szakasz "érték:" és "név:" mezői közötti kapcsolat tekintetében: a kettősponttal körülvett "név:" karakterlánc második része a titkos titokverziószáma, és a kettőspont előtti névnek meg kell egyeznie azzal a titkos hálóértékkel, amelynek a titkos értéke Függőség. Elem esetén például a verziószám ```name: mysecret:1.0```1.0, ```mysecret``` és a névnek meg kell egyeznie a korábban definiált névvel. ```"value": "mysecret"```

>
Az alábbi példa bemutatja, hogyan deklarálhatok hálótitkokat/értékeket jsonfájlban:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Az alábbi példa bemutatja, hogyan deklarálhatok hálótitkokat/értékeket yaml-fájlban:
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

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Hálóalkalmazás módosítása hálótitkos értékekre
A Service Fabric Mesh alkalmazásainak tisztában kell lenniük a következő két karakterlánccal a Biztonságos tár szolgáltatástitkos értékeinek felhasználásához:
1. A Microsoft.ServiceFabricMesh/Secrets.name tartalmazza a fájl nevét, és a Titkos kulcsok értéket egyszerű szövegként tartalmazza.
2. A Windows vagy Linux "Fabric_SettingPath" környezeti változó tartalmazza azt a könyvtárelérési utat, amelyhez a Biztonságos tár szolgáltatástitkos kulcsok értékeit tartalmazó fájlok elérhetők lesznek. Ez a "C:\Settings" a Windows által üzemeltetett és a "/var/settings" a Linux által üzemeltetett Mesh alkalmazások, illetve.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>A Mesh alkalmazás működés közbeni frissítésének telepítése vagy használata titkos értékek felhasználásához
Titkos kulcsok és/vagy verziózott titkos kulcsok/értékek létrehozása az erőforrásmodell deklarált központi telepítéseire korlátozódik. Az erőforrások létrehozásának egyetlen módja egy JSON- vagy YAML-fájl átadása az **az mesh telepítési** parancs használatával az alábbiak szerint:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Azure CLI-parancsok a Biztonságos tár szolgáltatás életciklusának kezeléséhez

### <a name="create-a-new-secrets-resource"></a>Új titkos kulcsok erőforrás létrehozása
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Adja át **a sablonfájlt** vagy **a sablon-uri-t** (de mindkettőt nem).

Példa:
- az háló központi telepítése létrehozása --c:\MyMeshTemplates\SecretTemplate1.txt
- az háló központi telepítése\/létrehozása --https: /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Titkos megjelenítése
A titkos titok leírását adja eredményül (de az értéket nem).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Titkos titok törlése

- Egy titkos kulcsot nem lehet törölni, amíg hálóalkalmazás hivatkozik rá.
- A Titkos kulcsok erőforrás törlése törli az összes titkos kulcsok/erőforrás verziót.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Titkok listázása az előfizetésben
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Titkok listázása az erőforráscsoportban
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Titkos verziók összes verziójának listázása
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Titkos verzióérték megjelenítése
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Titkos verzió értékének törlése
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>További lépések 
Ha többet szeretne megtudni a Service Fabric Mesh-ről, olvassa el az áttekintést:
- [A szolgáltatásháló hálója – áttekintés](service-fabric-mesh-overview.md)
