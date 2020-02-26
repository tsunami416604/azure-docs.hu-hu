---
title: Azure Service Fabric központi titkok tárolója
description: Ez a cikk azt ismerteti, hogyan használható a központi titkok tárolása az Azure Service Fabricban.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589164"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Központi titkok tárolása az Azure-ban Service Fabric 
Ez a cikk azt ismerteti, hogyan használható a Central Secrets Store (CSS) az Azure Service Fabricban a titkok létrehozásához Service Fabric alkalmazásokban. A CSS egy helyi titkos tároló-gyorsítótár, amely a memóriában titkosított bizalmas adatokat, például jelszavakat, jogkivonatokat és kulcsokat tárol.

## <a name="enable-central-secrets-store"></a>Központi titkok tárolójának engedélyezése
A CSS engedélyezéséhez adja hozzá az alábbi parancsfájlt a fürt konfigurációjához `fabricSettings` alatt. Javasoljuk, hogy a CSS-fürtön kívül más tanúsítványt használjon. Győződjön meg arról, hogy a titkosítási tanúsítvány telepítve van az összes csomóponton, és hogy `NetworkService` rendelkezik olvasási engedéllyel a tanúsítvány titkos kulcsához.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Titkos erőforrás deklarálása
A Azure Resource Manager sablon vagy a REST API használatával létrehozhat egy titkos erőforrást.

### <a name="use-resource-manager"></a>A Resource Manager használata

A következő sablonnal használhatja a Resource Managert a titkos erőforrás létrehozásához. A sablon egy `supersecret` titkos erőforrást hoz létre, de még nincs beállítva érték a titkos erőforráshoz.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>A REST API használata

`supersecret` titkos erőforrásnak a REST API használatával történő létrehozásához tegye a következőt: PUT-kérelem `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Titkos erőforrás létrehozásához szükség van a fürt vagy a rendszergazdai ügyféltanúsítvány tanúsítványára.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>A titkos érték beállítása

### <a name="use-the-resource-manager-template"></a>A Resource Manager-sablon használata

A titkos érték létrehozásához és beállításához használja a következő Resource Manager-sablont. Ez a sablon a `supersecret` Secret-erőforráshoz tartozó titkos értéket állítja be `ver1`verzióként.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>A REST API használata

A következő parancsfájl használatával állítsa be a titkos értéket a REST API használatával.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>A titkos érték vizsgálata
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Az alkalmazásban található titkos kód használata

Az alábbi lépéseket követve használhatja a titkos kulcsot a Service Fabric alkalmazásban.

1. Vegyen fel egy szakaszt a **Settings. XML** fájlban a következő kódrészlettel. Vegye figyelembe, hogy az érték {`secretname:version`} formátumú.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importálja a szakaszt a **ApplicationManifest. xml fájlban**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   A környezeti változó `SecretPath` arra a könyvtárra mutat, ahol az összes titkot tárolja. A `testsecrets` szakaszban felsorolt paraméterek külön fájlban tárolódnak. Az alkalmazás mostantól a következő módon használhatja a titkot:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. A titkok csatlakoztatása egy tárolóhoz. A titkos kulcsoknak a tárolón belüli elérhetővé tételéhez csak a `specify` csatlakoztatási pontra van szükség `<ConfigPackage>`ban.
A következő kódrészlet a módosított **ApplicationManifest. XML**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   A Titkok a tárolón belüli csatlakoztatási pont alatt érhetők el.

1. `Type='SecretsStoreRef`megadásával egy titkos kulcsot is megadhat egy folyamat környezeti változóhoz. Az alábbi kódrészlet egy példa arra, hogyan köthető a `supersecret` verziója `ver1` a **ServiceManifest. XML**környezeti változóhoz `MySuperSecret`.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Következő lépések
További információ az [alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md).
