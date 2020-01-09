---
title: A Service Fabric titkoskulcs-tárolója
description: Ez a cikk a Service Fabric Secrets Store használatát ismerteti.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457504"
---
#  <a name="service-fabric-secrets-store"></a>A Service Fabric titkoskulcs-tárolója
Ez a cikk azt ismerteti, hogyan hozhat létre és használhat titkokat Service Fabric alkalmazásokban a Service Fabric Secrets Store (CSS) használatával. A CSS egy helyi titkos tároló-gyorsítótár, amely bizalmas adatok, például jelszó, tokenek és a memóriában titkosított kulcsok megőrzésére szolgál.

## <a name="enabling-secrets-store"></a>Titkok tárolójának engedélyezése
 A CSS engedélyezéséhez adja hozzá az alábbi beállításokat a fürtkonfiguráció `fabricSettings` alatt. Ajánlott olyan tanúsítványt használni, amely eltér a CSS-hez tartozó fürt-tanúsítványtól. Győződjön meg arról, hogy a titkosítási tanúsítvány telepítve van az összes csomóponton, és `NetworkService` rendelkezik olvasási engedéllyel a tanúsítvány titkos kulcsához.
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
## <a name="declare-secret-resource"></a>Titkos erőforrás deklarálása
Létrehozhat egy titkos erőforrást a Resource Manager-sablon vagy a REST API használatával.

* Resource Manager-sablon használata
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
A fenti sablon `supersecret` titkos erőforrást hoz létre, de még nincs beállítva érték a titkos erőforráshoz.

* A REST API használata

A titkos erőforrás létrehozásához `supersecret` PUT-kérést `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. Titkos kód létrehozásához szükség van a fürt vagy a rendszergazdai ügyféltanúsítvány tanúsítványára.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Titkos érték beállítása
* Resource Manager-sablon használata

Az alábbi Resource Manager-sablon létrehozza és beállítja a Secret `supersecret` verziószámát `ver1`.
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
* A REST API használata

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>A titkos kód használata az alkalmazásban

1.  Vegyen fel egy szakaszt a Settings. xml fájlban az alábbi tartalommal. Vegye figyelembe, hogy az érték {`secretname:version`} formátumú.

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Most importálja a szakaszt a ApplicationManifest. xml fájlban.
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

A "SecretPath" környezeti változó arra a könyvtárra mutat, ahol az összes titkot tárolja. A `testsecrets` szakaszban felsorolt paraméterek külön fájlban lesznek tárolva. Az alkalmazás mostantól az alább látható titkos kulcsot is használhatja
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Titkok csatlakoztatása egy tárolóhoz

Csak a szükséges titkokat kell megadnia a tárolón belül a csatlakoztatási pont megadásához `<ConfigPackage>`ban.
Itt látható a módosított ApplicationManifest. xml fájl.  

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
A Titkok a tárolón belüli csatlakoztatási pont alatt lesznek elérhetők.

4. Kötési titok egy környezeti változóhoz 

A "SecretsStoreRef" típus megadásával köthető a titkos kulcshoz egy folyamat környezeti változóhoz. Íme egy példa arra, hogyan köthető `supersecret` verzió `ver1` a környezeti változóhoz `MySuperSecret` a ServiceManifest. xml fájlban.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Következő lépések
További információ az [alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md)
