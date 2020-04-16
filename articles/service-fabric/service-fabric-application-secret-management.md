---
title: Az Azure Service Fabric-alkalmazások titkos készleteikezelése
description: Ismerje meg, hogyan biztonságos titkos értékeket a Service Fabric-alkalmazások (platform-független).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4d2138935122b9e08b21963519fce3f72466ab1f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414515"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Titkosított titkos kulcsok kezelése a Service Fabric-alkalmazásokban
Ez az útmutató bemutatja a service fabric-alkalmazások titkos titkainak kezelésének lépéseit. A titkos kulcsok lehetnek bizalmas információk, például tárolási kapcsolati karakterláncok, jelszavak vagy más értékek, amelyeket nem szabad egyszerű szövegként kezelni.

A titkosított titkos kulcsok használata a Service Fabric-alkalmazásokban három lépésből áll:
* Hozzon létre egy titkosítási tanúsítványt, és titkosítsa a titkos kulcsokat.
* Adja meg a titkosított titkos kulcsokat egy alkalmazásban.
* A titkosított titkok visszafejtése a szolgáltatáskódból.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Titkosítási tanúsítvány beállítása és titkos kulcsok titkosítása
A titkosítási tanúsítvány beállítása és titkosítása titkos kulcsok titkosítása windows és Linux között változik.
* [Állítson be egy titkosítási tanúsítványt, és titkos kulcsokat titkosítson Windows-fürtökön.][secret-management-windows-specific-link]
* [Hozzon létre egy titkosítási tanúsítványt, és titkos kulcsoktitkosítása Linux-fürtökön.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Titkosított titkos kulcsok megadása egy alkalmazásban
Az előző lépés azt ismerteti, hogyan titkos kulcsot titkosíthat egy tanúsítvánnyal, és hogyan hozhat létre egy 64-es alapú kódolású karakterláncot egy alkalmazásban való használatra. Ez a base-64 kódolású karakterlánc megadható titkosított [paraméterként][parameters-link] a szolgáltatás Settings.xml fájljában, vagy titkosított [környezeti változóként][environment-variables-link] a serviceManifest.xml fájlban.

Adjon meg egy titkosított [paramétert][parameters-link] a szolgáltatás Settings.xml konfigurációs fájljában, amelynek `IsEncrypted` attribútuma a következő: `true`

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Adjon meg egy titkosított [környezeti változót][environment-variables-link] a szolgáltatás `Type` ServiceManifest.xml `Encrypted`fájljában, amelynek attribútuma a következő:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

A titkos kulcsokat is szerepelnie kell a Service Fabric-alkalmazásban egy tanúsítvány megadásával az alkalmazás jegyzékfájljában. Adjon hozzá egy **SecretsCertificate** elemet az **ApplicationManifest.xml fájlhoz,** és adja meg a kívánt tanúsítvány ujjlenyomatát.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Egy alkalmazást aktiválva, amely meghatározza a SecretsCertificate, Service Fabric megtalálja a megfelelő tanúsítványt, és megadja az identitás az alkalmazás fut a tanúsítvány személyes kulcsának teljes engedélyével. A Service Fabric is figyeli a tanúsítvány t, és ennek megfelelően újra alkalmazza az engedélyeket. A common name deklarált tanúsítványok változásainak észleléséhez a Service Fabric egy időszakos feladatot futtat, amely megkeresi az összes egyező tanúsítványt, és összehasonlítja az ujjlenyomatok gyorsítótárazott listájával. Új ujjlenyomat észlelésekor ez azt jelenti, hogy a tulajdonos tanúsítványa meglett újulva. A feladat percenként egyszer fut a fürt minden csomópontján.
>
> Bár a SecretsCertificate lehetővé teszi a tárgyalapú deklarációkat, vegye figyelembe, hogy a titkosított beállítások ahhoz a kulcspárhoz vannak kötve, amelyet az ügyfélen lévő beállítás titkosításához használtak. Meg kell győződnie arról, hogy az eredeti titkosítási tanúsítvány (vagy azzal egyenértékű) megegyezik a tárgyalapú deklarációval, és hogy telepítve van-e a fürt minden olyan csomópontján, amely képes az alkalmazást üzemeltetni. Minden, a tulajdonosi deklarációnak megfelelő és az eredeti titkosítási tanúsítvánnyal azonos kulcspárból készült tanúsítványok egyenértékűnek minősülnek.
>

### <a name="inject-application-secrets-into-application-instances"></a>Alkalmazástitkok befecskendezése alkalmazáspéldányokba
Ideális esetben a különböző környezetekben történő telepítésnek a lehető legautomatizáltabbnak kell lennie. Ez úgy valósítható meg, hogy titkos titkosítást hajt végre egy buildkörnyezetben, és a titkosított titkos kulcsokat paraméterekként biztosítja az alkalmazáspéldányok létrehozásakor.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Túlságosan kiadható paraméterek használata a Settings.xml fájlban
A Settings.xml konfigurációs fájl lehetővé teszi az alkalmazás létrehozásakor megadható túlszámíthat paramétereket. Használja `MustOverride` az attribútumot ahelyett, hogy értéket adn meg egy paraméterhez:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

A Settings.xml fájl értékeinek felülbírálásához deklarálja a szolgáltatás felülbírálási paraméterét az ApplicationManifest.xml fájlban:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Most az érték megadható *alkalmazásparaméterként* az alkalmazás egy példányának létrehozásakor. Egy alkalmazáspéldány létrehozása parancsfájllal rendelkezik a PowerShell használatával, vagy C#-ban írható a buildelőfolyamatokba való egyszerű integráció érdekében.

A PowerShell használatával a paraméter `New-ServiceFabricApplication` [kivonattáblaként](https://technet.microsoft.com/library/ee692803.aspx)kerül a parancsba:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

A C# használatával az alkalmazás paraméterei `NameValueCollection`a `ApplicationDescription` következőkben vannak megadva:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Titkosított titkos kulcsok visszafejtése a szolgáltatáskódból
A [paraméterek][parameters-link] és [a környezeti változók][environment-variables-link] eléréséhez szükséges API-k lehetővé teszik a titkosított értékek egyszerű visszafejtését. Mivel a titkosított karakterlánc a titkosításhoz használt tanúsítványadatait tartalmazza, nem kell manuálisan megadnia a tanúsítványt. A tanúsítványt csak telepíteni kell arra a csomópontra, amelyen a szolgáltatás fut.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>További lépések
* Szolgáltatásfabric [titkos tároló](service-fabric-application-secret-store.md) 
* További információ [az alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md