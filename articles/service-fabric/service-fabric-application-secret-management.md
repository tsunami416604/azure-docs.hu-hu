---
title: Azure-Service Fabric alkalmazás-titkok kezelése | Microsoft Docs
description: Megtudhatja, hogyan védheti meg a titkos értékeket egy Service Fabric alkalmazásban (platform – agnosztikus).
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
ms.openlocfilehash: 9854ad7118684e1a5e57b0809d733d812ad64176
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828834"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Titkosított titkok kezelése Service Fabric alkalmazásokban
Ez az útmutató végigvezeti a Service Fabric alkalmazásban található titkok kezelésének lépésein. A titkok lehetnek bármilyen bizalmas információk, például a tárolási kapcsolatok karakterláncai, jelszavai vagy más olyan értékek, amelyeket nem szabad egyszerű szövegben kezelni.

A Service Fabric alkalmazásokban a titkosított titkos kulcsok három lépést foglalnak magukban:
* Titkosítási tanúsítvány beállítása és a titkok titkosítása.
* Titkosított titkos kódok megadását egy alkalmazásban.
* Titkosítatlan titkok visszafejtése a szolgáltatási kódból.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Titkosítási tanúsítvány beállítása és a titkos kulcsok titkosítása
A titkosítási tanúsítvány beállítása és a titkok titkosítása a Windows és a Linux között változhat.
* [Titkosítási tanúsítvány beállítása és a titkok titkosítása Windows-fürtökön.][secret-management-windows-specific-link]
* [Titkosítási tanúsítvány beállítása és a titkok titkosítása Linux-fürtökön.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Titkosított titkok meghatározása egy alkalmazásban
Az előző lépés leírja, hogyan titkosíthatja a titkos kódot egy tanúsítvánnyal, és hogyan hozhat létre Base-64 kódolású karakterláncot az alkalmazásokban való használathoz. Ez a Base-64 kódolású karakterlánc a szolgáltatás Settings. XML fájljában, illetve titkosított [környezeti változóként][environment-variables-link] is megadható titkosított [paraméterként][parameters-link] a szolgáltatás ServiceManifest. XML fájljában.

Adja meg a szolgáltatás Settings. xml konfigurációs fájljának titkosított [paraméterét][parameters-link] az `IsEncrypted` attribútummal, amely `true` értékre van beállítva:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Adja meg a titkosított [környezeti változót][environment-variables-link] a szolgáltatás ServiceManifest. XML fájljában a `Type` attribútummal, amely `Encrypted` értékre van beállítva:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

A titkokat a Service Fabric alkalmazásban is fel lehet venni egy tanúsítvány megadásával az alkalmazás jegyzékfájljában. Adjon hozzá egy **SecretsCertificate** elemet a **ApplicationManifest. xml fájlhoz** , és adja meg a kívánt tanúsítvány ujjlenyomatát.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Alkalmazási titkok behelyezése az alkalmazás példányaiba
Ideális esetben a különböző környezetekben történő üzembe helyezésnek a lehető legautomatizáltnak kell lennie. Ezt úgy teheti meg, hogy a titkos titkosítást egy Build környezetben hajtja végre, és a titkosított titkokat paraméterekként adja meg az alkalmazás példányainak létrehozásakor.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Overridable paraméterek használata a Settings. xml fájlban
A Settings. xml konfigurációs fájl lehetővé teszi az alkalmazások létrehozási idején elérhető Overridable paraméterek használatát. A paraméter értékének megadása helyett használja a `MustOverride` attribútumot:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

A Settings. xml fájlban lévő értékek felülbírálásához deklaráljon egy felülbírálási paramétert a szolgáltatáshoz a ApplicationManifest. xml fájlban:

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

Az érték mostantól az alkalmazás egy példányának létrehozásakor is megadható *Application paraméterként* . Az alkalmazás-példányok létrehozásához a PowerShell használatával vagy a szolgáltatásba C#való beírásával lehet létrehozni az egyszerű integrációt egy összeállítási folyamat során.

A PowerShell használatával a paraméter a `New-ServiceFabricApplication` parancshoz van megadva [kivonatoló táblaként](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

A C#használatakor az alkalmazás paramétereinek megadása egy `ApplicationDescription` `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Titkosított titkok visszafejtése a szolgáltatás kódjából
A [Paraméterek][parameters-link] és [környezeti változók][environment-variables-link] elérésére szolgáló API-k lehetővé teszik a titkosított értékek egyszerű visszafejtését. Mivel a titkosított sztring információt tartalmaz a titkosításhoz használt tanúsítványról, nem kell manuálisan megadnia a tanúsítványt. A tanúsítványt csak arra a csomópontra kell telepíteni, amelyen a szolgáltatás fut.

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
További információ az [alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
