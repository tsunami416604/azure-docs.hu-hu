---
title: Az Azure Service Fabric központi titoktartási tárolója
description: Ez a cikk ismerteti, hogyan használhatja a Központi titok tároló az Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589164"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Központi titkok tárolója az Azure Service Fabricben 
Ez a cikk ismerteti, hogyan használhatja a Központi titkos kulcsok tároló (CSS) az Azure Service Fabric-ben titkos kulcsok létrehozása a Service Fabric-alkalmazásokban. A CSS egy helyi titkos tároló gyorsítótár, amely bizalmas adatokat, például jelszót, jogkivonatokat és kulcsokat titkosítva tárol a memóriában.

## <a name="enable-central-secrets-store"></a>Központi titkos kulcsok tárának engedélyezése
A CSS `fabricSettings` engedélyezéséhez adja hozzá a következő parancsfájlt a fürtkonfigurációhoz. Azt javasoljuk, hogy a CSS fürttanúsítványáttól eltérő tanúsítványt használjon. Győződjön meg arról, hogy a titkosítási tanúsítvány minden csomópontra telepítve van, és amely `NetworkService` olvasási engedéllyel rendelkezik a tanúsítvány személyes kulcsához.
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
Létrehozhat egy titkos erőforrást az Azure Resource Manager sablon vagy a REST API használatával.

### <a name="use-resource-manager"></a>Erőforrás-kezelő használata

A titkos erőforrás létrehozásához használja az alábbi sablont az Erőforrás-kezelő használatával. A sablon `supersecret` létrehoz egy titkos erőforrást, de még nincs beállítva érték a titkos erőforráshoz.


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

Titkos erőforrás `supersecret` létrehozásához a REST API használatával, `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`hogy egy PUT kérelmet. Titkos erőforrás létrehozásához fürt- vagy rendszergazdai ügyféltanúsítványra van szükség.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>A titkos érték beállítása

### <a name="use-the-resource-manager-template"></a>Az Erőforráskezelő sablon használata

A titkos érték létrehozásához és beállításához használja az alábbi Erőforrás-kezelő sablont. Ez a sablon a `supersecret` titkos erőforrás `ver1`titkos értékét verzióként állítja be.
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

A következő parancsfájl használatával a REST API-t a titkos érték beállításához.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>A titkos érték vizsgálata
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Használja a titkos kulcsot az alkalmazásban

Kövesse az alábbi lépéseket a titkos kulcsot a Service Fabric-alkalmazásban.

1. Adjon hozzá egy szakaszt a **settings.xml** fájlhoz a következő kódrészlettel. Itt vegye figyelembe, hogy az`secretname:version`érték {}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importálja a szakaszt az **ApplicationManifest.xml**fájlban.
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

   A környezeti `SecretPath` változó arra a könyvtárra mutat, ahol az összes titkos titok tárolódik. A `testsecrets` szakaszban felsorolt paraméterek külön fájlban tárolódnak. Az alkalmazás most már használhatja a titkot az alábbiak szerint:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. A titkokat egy tárolóba szerelje fel. Az egyetlen változás szükséges, hogy a titkos `specify` kulcsok elérhető `<ConfigPackage>`a tárolóban, hogy egy csatlakoztatási pont.
A következő kódrészlet a módosított **ApplicationManifest.xml**.  

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
   Titkok állnak rendelkezésre a tárolón belüli csatlakoztatási pont alatt.

1. A program titkos kulcsot köthet egy `Type='SecretsStoreRef`folyamatkörnyezeti változóhoz. A következő kódrészlet egy példa arra, `supersecret` `ver1` hogyan lehet `MySuperSecret` a verziót a **ServiceManifest.xml**fájl környezeti változójához kötni.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>További lépések
További információ [az alkalmazás- és szolgáltatásbiztonságról.](service-fabric-application-and-service-security.md)
