---
title: Key Vault .NET 2.x API kibocsátási megjegyzései |} A Microsoft Docs
description: .NET-fejlesztők számára az Azure Key Vault fogja használni a kódot az API
services: key-vault
author: msmbaldwin
manager: barbkess
editor: bryanla
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 3d2543ea2b2ee6261b04396f92d5f3583a89ffb0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406906"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Az Azure Key Vault .NET 2.0 – kibocsátási megjegyzések és a Migrálási útmutatót
A következő információk segítséget nyújtanak a C# és a .NET 2.0-s verzióját az Azure Key Vault tár-ba való migrálás.  A korábbi verziók írt alkalmazásokat kell legújabb verzióját frissíti.  Ezek a változások szükségesek, teljes körűen támogatja az új és továbbfejlesztett funkciók, mint például **Key Vault tanúsítványokkal**.

## <a name="key-vault-certificates"></a>A Key Vault-tanúsítványok

A Key Vault-tanúsítványok kezelése x509 tanúsítványokat, és támogatja az alábbi viselkedés tapasztalható:  

* A Key Vault létrehozási folyamata keretében tanúsítványok létrehozása vagy meglévő tanúsítvány importálása. Ez magában foglalja, mindkettő önaláírt, és a hitelesítésszolgáltató (CA) tanúsítvány jön létre.
* Biztonságosan tárolhatja és kezelheti a x509 tárolási tanúsítvány titkos kulcs adatai használatával beavatkozás nélkül.  
* Határozza meg, hogy a tanúsítvány életciklusának kezelése a Key Vault közvetlen házirendjét.  
* Kapcsolattartási adatok életciklus-események, például lejárati figyelmeztetések és a megújítására vonatkozó értesítéseket biztosítanak.  
* A kiválasztott kiállítók (Key Vault partner X509 tanúsítványszolgáltatók és hitelesítésszolgáltatók) tanúsítványok automatikus megújítására. * támogatási tanúsítványt a másodlagos (partnerek) biztosít, és a hitelesítésszolgáltatók (nem támogatja az automatikus megújítás).  

## <a name="net-support"></a>.NET-támogatásnak

* **.NET 4.0-s** az Azure Key Vault .NET-kódtár 2.0-s verziója nem támogatott
* **.NET-keretrendszer 4.5.2-es** támogatja az Azure Key Vault .NET-kódtár 2.0-s verziója
* **.NET standard 1.4-es** az Azure Key Vault .NET-kódtár 2.0-s verziója támogatja

## <a name="namespaces"></a>Névterek

* A névtér a **modellek** változtatják **Microsoft.Azure.KeyVault** való **Microsoft.Azure.KeyVault.Models**.
* A **Microsoft.Azure.KeyVault.Internal** névtér már nincs használatban.
* A következő Azure SDK-függőségek névterek rendelkezik 

    - **Hyak.Common** mostantól **Microsoft.Rest**.
    - **Hyak.Common.Internals** mostantól **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Adattípus-módosítások

* *Titkos kulcs* változott *SecretBundle*
* *Szótár* változott *IDictionary*
* *Lista<T>, string []* változott *IList<T>*
* *NextList* változott *NextPageLink*

## <a name="return-types"></a>Návratové typy

* **KeyList** és **SecretList** mostantól ad vissza *IPage<T>*  helyett *ListKeysResponseMessage*
* A generált **BackupKeyAsync** mostantól ad vissza *BackupKeyResult*, tartalmazó *érték* (biztonsági mentési blobtárat). Korábban a metódus lett burkolt be, és csak a értéket adott vissza.

## <a name="exceptions"></a>Kivételek

* *KeyVaultClientException* módosul, amelyikben *KeyVaultErrorException*
* Hiba a szolgáltatás változása: *kivétel. Hiba* való *kivétel. Body.Error.Message*.
* További információ eltávolítva a hibaüzenetben **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorok

* Ahelyett, hogy elfogadja az *HttpClient* konstruktor argumentumként, a konstruktor csak fogad *HttpClientHandler* vagy *DelegatingHandler []*.

## <a name="downloaded-packages"></a>A letöltött csomagok

Amikor egy ügyfél a Key Vault függőség feldolgozza, a következő csomagok tölti:

### <a name="previous-package-list"></a>Előző csomaglista

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Jelenlegi csomag listája

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Osztály módosítások

* **UnixEpoch** osztály el lett távolítva.
* **Base64UrlConverter** osztály neve mostantól **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Egyéb módosítások

* Az API-verzió támogatása KV művelet újrapróbálkozási szabályzat az átmeneti hibák konfigurációját bővült.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* A műveletek, amelyek adott vissza egy *tároló*, a visszatérési típus lett egy osztályt, amely tartalmaz egy **tároló** tulajdonság. A visszatérési típus már *tároló*.
* *PermissionsToKeys* és *PermissionsToSecrets* immár *Permissions.Keys* és *Permissions.Secrets*
* Bizonyos návratové typy módosítások a vezérlősík is vonatkoznak.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* A csomag sérült akár **Microsoft.Azure.KeyVault.Extensions** és **Microsoft.Azure.KeyVault.Cryptography** a titkosítási műveletek.

