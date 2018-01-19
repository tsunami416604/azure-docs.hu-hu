---
title: "Kulcs tároló .NET 2.x API kibocsátási megjegyzései |} Microsoft Docs"
description: ".NET-fejlesztők számára a kód API-t fogja használni az Azure Key Vault"
services: key-vault
author: lleonard-msft
manager: mbaldwin
editor: alleonar
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: alleonar
ms.openlocfilehash: a7735f8c1c4332bf2472bc83c0c37baf49019004
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Az Azure Key Vault .NET 2.0 – kibocsátási megjegyzések és az áttelepítési útmutató
Az alábbi információk segítségével történő az Azure Key Vault kódtár 2.0-s verzióját, a C# és a .NET.  Korábbi verzióira írt kell támogatja a legújabb verzióra frissíti.  Ezek a változások szükségesek, teljes körű támogatásához, mint az új és továbbfejlesztett szolgáltatásaival, **Key Vault tanúsítványok**.

## <a name="key-vault-certificates"></a>Key Vault tanúsítványok

Key Vault tanúsítványok kezelése x509 tanúsítványokat, és támogatja az alábbiak egyike:  

* A Key Vault létrehozási folyamat tanúsítványok létrehozása vagy meglévő tanúsítvány importálása. Ez magában foglalja, mindkettő önaláírt, és a tanúsítvány hitelesítésszolgáltatói (CA) tanúsítvány jön létre.
* Biztonságos helyen tárolja, és kezelheti a x509 tárolási tanúsítvány titkos kulcs adatai használatával beavatkozás nélkül.  
* Adja meg a házirendekben, amelyek közvetlenül a Key Vault kezeléséhez a tanúsítvány élettartama.  
* Adja meg a kapcsolattartási adatok életciklus-események, például lejárati figyelmeztetések és megújítási értesítések.  
* A kiválasztott kiállítók (Key Vault partner X509 tanúsítvány szolgáltatók és tanúsítványszolgáltatók) tanúsítványok automatikus megújítására. * támogatási tanúsítványt a másodlagos (nem partnerként) biztosít, és a hitelesítésszolgáltatók (nem támogatja az automatikus tartománymegújítási).  

## <a name="net-support"></a>.NET-támogatás

* **A .NET 4.0** nem támogatja az Azure Key Vault .NET könyvtár a 2.0-s verziójához
* **.NET-keretrendszer 4.5.2-es** támogatja az Azure Key Vault .NET könyvtár a 2.0-s verziójához
* **.NET-szabvány 1.4** támogatja az Azure Key Vault .NET könyvtár a 2.0-s verziójához

## <a name="namespaces"></a>Névterek

* A névtér **modellek** változtatják **Microsoft.Azure.KeyVault** való **Microsoft.Azure.KeyVault.Models**.
* A **Microsoft.Azure.KeyVault.Internal** névtér megszakad.
* A következő Azure SDK-függőségek névterek rendelkezik 

    - **Hyak.Common** most **Microsoft.Rest**.
    - **Hyak.Common.Internals** most **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Módosításai

* *Titkos kulcs* változott *SecretBundle*
* *Szótár* változott *IDictionary*
* *Lista<T>, string []* változott *IList elemet.<T>*
* *NextList* változott *NextPageLink*

## <a name="return-types"></a>Visszatérési típusokat

* **KeyList** és **SecretList** most adja vissza *IPage<T>*  helyett *ListKeysResponseMessage*
* A létrehozott **BackupKeyAsync** most adja vissza *BackupKeyResult*, amely tartalmazza *érték* (biztonsági másolat blob). Korábban a metódus lett burkolt, és csak a értéket adott vissza.

## <a name="exceptions"></a>Kivételek

* *KeyVaultClientException* változott *KeyVaultErrorException*
* Hiba a szolgáltatás változása *kivétel. Hiba* való *kivétel. Body.Error.Message*.
* További információ eltávolítja a hibaüzenetet **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorok

* Elfogadása helyett egy *HttpClient* konstruktor argumentumként, a konstruktor csak fogad *HttpClientHandler* vagy *DelegatingHandler []*.

## <a name="downloaded-packages"></a>A letöltött csomagok

Amikor egy ügyfél Kulcstárolónak függőséget dolgoz fel, a következő csomagok tölti:

### <a name="previous-package-list"></a>Előző csomaglista

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Aktuális csomaglista

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Osztály változások

* **UnixEpoch** osztály el lett távolítva.
* **Base64UrlConverter** osztály neve módosult az **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Az egyéb módosítások

* A konfiguráció KV művelet újrapróbálkozási házirend átmeneti hibáiról már támogatja az API ezen verziójára.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* A művelet által visszaadott egy *tároló*, a visszatérési típusa történt egy osztály, amely tartalmazza a **tároló** tulajdonság. A visszatérési típus már *tároló*.
* *PermissionsToKeys* és *PermissionsToSecrets* most már *Permissions.Keys* és *Permissions.Secrets*
* Bizonyos visszatérési típusokat módosítások a vezérlő-vezérlősík is vonatkoznak.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* A csomag megszakad legfeljebb **Microsoft.Azure.KeyVault.Extensions** és **Microsoft.Azure.KeyVault.Cryptography** a titkosítási műveletek.

