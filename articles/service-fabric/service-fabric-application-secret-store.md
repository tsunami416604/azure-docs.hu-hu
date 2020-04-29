---
title: Azure Service Fabric központi titkok tárolója
description: Ez a cikk azt ismerteti, hogyan használható a központi titkok tárolása az Azure Service Fabricban.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770414"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Központi titkok tárolása az Azure-ban Service Fabric 
Ez a cikk azt ismerteti, hogyan használható a Central Secrets Store (CSS) az Azure Service Fabricban a titkok létrehozásához Service Fabric alkalmazásokban. A CSS egy helyi titkos tároló-gyorsítótár, amely a memóriában titkosított bizalmas adatokat, például jelszavakat, jogkivonatokat és kulcsokat tárol.

## <a name="enable-central-secrets-store"></a>Központi titkok tárolójának engedélyezése
Adja hozzá a következő parancsfájlt a fürt konfigurációjához `fabricSettings` a CSS engedélyezéséhez. Javasoljuk, hogy a CSS-fürtön kívül más tanúsítványt használjon. Győződjön meg arról, hogy a titkosítási tanúsítvány telepítve van az összes `NetworkService` csomóponton, és olvasási engedéllyel rendelkezik a tanúsítvány titkos kulcsához.
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
A REST API használatával létrehozhat egy titkos erőforrást.
  > [!NOTE] 
  > Ha a fürt Windows-hitelesítést használ, a REST-kérelmet a rendszer a nem biztonságos HTTP-csatornán keresztül küldi el. Javasoljuk, hogy használjon egy X509-alapú fürtöt biztonságos végpontokkal.

Ha a REST API `supersecret` használatával szeretne létrehozni egy titkos erőforrást, tegyen fel kérelmet a `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`következőre:. Titkos erőforrás létrehozásához szükség van a fürt vagy a rendszergazdai ügyféltanúsítvány tanúsítványára.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>A titkos érték beállítása

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

   A környezeti változó `SecretPath` arra a könyvtárra mutat, ahol az összes titkot tárolja. A `testsecrets` szakaszban felsorolt minden paraméter külön fájlban tárolódik. Az alkalmazás mostantól a következő módon használhatja a titkot:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. A titkok csatlakoztatása egy tárolóhoz. Az egyetlen, a tárolón `specify` belül elérhető titkokat a-ben lévő `<ConfigPackage>`csatlakoztatási pontra kell módosítani.
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

1. Megadhat egy titkos kulcsot egy folyamat környezeti változóhoz a következő megadásával: `Type='SecretsStoreRef`. Az alábbi kódrészlet egy példa arra, hogyan köthető `supersecret` a verzió `ver1` a környezeti változóhoz `MySuperSecret` a **ServiceManifest. xml fájlban**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>További lépések
További információ az [alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md).
