---
title: Az Azure Service Fabric titkos alkalmazáskulcsok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan védheti meg a Service Fabric-alkalmazás (platform-agnosztikus) titkos értékkel.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d151dbf20e68a2152e9d886a74e51786bb8fbfa6
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661003"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>A Service Fabric-alkalmazásokat a titkosított titkos kulcsok kezelése
Ez az útmutató végigvezeti a lépéseken, a Service Fabric-alkalmazás titkos kulcsok kezeléséhez. Titkos kódok lehet a bizalmas adatokat, például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem szövegként kezelje.

A titkosított titkos kulcsok használata a Service Fabric-alkalmazás három lépésből áll:
* Hozzon létre egy titkosítási tanúsítványt, és titkosítani a titkos kulcsok.
* Adja meg a titkosított titkos kulcsok egy alkalmazásban.
* Szolgáltatás-code-ból a titkosított titkos kulcsok visszafejtéséhez.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Állítsa be a titkosítási tanúsítványt, és titkosítani a titkos kulcsok
Egy titkosítási tanúsítványt beállítását, és titkosítani a titkos kulcsok segítségével Windows és Linux rendszerű közé esik.
* [Állítsa be a titkosítási tanúsítványt, és a kulcsok a Windows-fürtök titkosítására.][secret-management-windows-specific-link]
* [Hozzon létre egy titkosítási tanúsítványt, és titkosítani a titkos kulcsokat a Linux-fürtökön.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Adja meg a titkosított titkos kulcsok egy alkalmazásban
Az előző lépés ismerteti, hogyan lehet egy tanúsítványt a titkos kulcs titkosításához, és előállít egy base-64 kódolású sztring az alkalmazásban használható. A base-64 kódolású sztring adható meg egy titkosított [paraméter] [ parameters-link] Settings.xml egy szolgáltatás vagy egy titkosított [környezeti változó] [ environment-variables-link] a ServiceManifest.xml a szolgáltatás.

Adjon meg egy titkosított [paraméter] [ parameters-link] a szolgáltatás Settings.xml konfigurációs fájlban és a `IsEncrypted` attribútum beállítása `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Adjon meg egy titkosított [környezeti változó] [ environment-variables-link] a ServiceManifest.xml fájlt a szolgáltatás a `Type` attribútum beállítása `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Az alkalmazáspéldányok a titkos alkalmazáskulcsok behelyezése
Ideális esetben különböző környezetekben való üzembe helyezés kell lenniük, automatizált, amennyire csak lehetséges. A titkos végrehajtása egy-összeállító környezetet, és a titkosított titkos kulcsok megadásával meg paraméterként, az alkalmazáspéldányok létrehozásakor kell elvégezni.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Felülbírálható paramétereket használja a Settings.xml
A Settings.xml konfigurációs fájl lehetővé teszi, hogy felülbírálható paramétereket, amely a létrehozás időpontjában adható meg. Használja a `MustOverride` attribútum, amely egy értéket egy paraméter helyett:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

A felülbírálási értékeket Settings.xml, deklaráljon egy felülbírálási paramétert, a szolgáltatás az ApplicationManifest.xml:

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

Most az értéket adhat meg egy *parametr aplikace* létrehozásakor az alkalmazás egy példányát. Az alkalmazás-példányok létrehozásának parancsfájlalapú lehet PowerShell-lel, vagy nyelven írt C#, az megkönnyíti az integrációt a buildelési folyamat során.

PowerShell használatával, a paraméter van megadva a `New-ServiceFabricApplication` parancsot, mint egy [kivonattábla](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Használatával C#, alkalmazás-paraméterek vannak megadva egy `ApplicationDescription` , egy `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>A webszolgáltatás kódjához titkosított titkos kulcsok visszafejtéséhez
Az API-k eléréséhez [paraméterek] [ parameters-link] és [környezeti változók] [ environment-variables-link] lehetővé teszik a titkosított értékek egyszerű visszafejtése. Mivel a titkosított karakterlánc a titkosításhoz használt tanúsítvány kapcsolatos információkat tartalmaz, nem kell manuálisan adja meg a tanúsítványt. A tanúsítvány csak a csomóponton, amelyen a szolgáltatás fut, telepíteni kell.

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
Tudjon meg többet [alkalmazás és szolgáltatás biztonsága](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
