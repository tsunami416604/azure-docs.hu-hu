---
title: "Kulcs tároló .NET 2.x API kibocsátási megjegyzései |} Microsoft Docs"
description: ".NET-fejlesztők számára a kód API-t fogja használni az Azure Key Vault"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: 5b03f5092ee4236ca3e7b12db37dc47bd6d3a309
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Az Azure Key Vault .NET 2.0 – kibocsátási megjegyzések és az áttelepítési útmutató
A következő notes és útmutatókat is Azure Key Vault .NET dolgozó fejlesztők számára / C# könyvtár. Az áttérést az 1.0-s verziója a 2.0-s verzióját, a frissítések száma végzett áttelepítés munkahelyi ahhoz, hogy igénybe vehesse az funkciók javításai, és kiegészítéseit, mint a beállítást, a kódban szükség teszi **Key Vault tanúsítványok** támogatja.

## <a name="key-vault-certificates"></a>Key Vault tanúsítványok

Key Vault tanúsítványok támogatás lehetővé teszi a x509 vezetése a tanúsítványok és az alábbiak egyike:  

* Lehetővé teszi, hogy a tanúsítvány tulajdonosa hozzon létre egy tanúsítványt, vagy a meglévő tanúsítvány importálása a Key Vault létrehozási folyamata keresztül. Mindkét önaláírt és a hitelesítésszolgáltató tanúsítványokat jön létre.
* Lehetővé teszi, hogy a Key Vault tanúsítvány tulajdonosa biztonságos tárolása és kezelése X509 végrehajtásához tanúsítványok és titkos kulcs adatai közötti interakció nélkül.  
* Lehetővé teszi, hogy a tanúsítvány tulajdonosának egy házirendet, amely arra utasítja a Key Vault kezeléséhez az életciklus-tanúsítvány létrehozásához.  
* Lehetővé teszi, hogy a tanúsítvány tulajdonos kapcsolattartási kapcsolatos adatok megadása az értesítéshez életciklus-események lejárati és a tanúsítvány megújítása.  
* Támogatja az automatikus megújítását a kiválasztott kiállítók - Key Vault partner X509 szolgáltatók tanúsítvány / hitelesítésszolgáltatók.
  
  * Megjegyzés: - nem közösen szolgáltatók/hitelesítésszolgáltatók is használhatók, de nem támogatja az automatikus megújítási szolgáltatást.

## <a name="net-support"></a>.NET-támogatás

* **A .NET 4.0** nem támogatja az Azure Key Vault .NET könyvtár a 2.0-s verziójához
* **.NET-keretrendszer 4.5.2-es** támogatja az Azure Key Vault .NET könyvtár a 2.0-s verziójához
* **.NET-szabvány 1.4** támogatja az Azure Key Vault .NET könyvtár a 2.0-s verziójához

## <a name="namespaces"></a>Névterek

* A névtér **modellek** változtatják **Microsoft.Azure.KeyVault** való **Microsoft.Azure.KeyVault.Models**.
* A **Microsoft.Azure.KeyVault.Internal** névtér megszakad.
* Az Azure SDK függőségek névtér nem változtatják **Hyak.Common** és **Hyak.Common.Internals** való **Microsoft.Rest** és **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>Módosításai

* *Titkos kulcs* változott *SecretBundle*
* *Szótár* változott *IDictionary*
* *Lista<T>, string []* változott *IList elemet.<T>*
* *NextList* változott *NextPageLink*

## <a name="return-types"></a>Visszatérési típusokat

* **KeyList** és **SecretList** visszaadható *IPage<T>*  helyett *ListKeysResponseMessage*
* A létrehozott **BackupKeyAsync** visszaadható *BackupKeyResult* tartalmazó *érték* (biztonsági másolat blob). A metódus burkolása előtt, és csak a értéket ad vissza.

## <a name="exceptions"></a>Kivételek

* *KeyVaultClientException* változott *KeyVaultErrorException*
* Hiba a szolgáltatás változtatják *kivétel. Hiba* való *kivétel. Body.Error.Message*.
* További információ eltávolítja a hibaüzenetet **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorok

* Elfogadása helyett egy *HttpClient* konstruktor argumentumként, a konstruktor csak fogad *HttpClientHandler* vagy *DelegatingHandler []*.

## <a name="downloaded-packages"></a>A letöltött csomagok

Ha egy ügyfél a Kulcstárolónak függőséget feldolgozása folyamatban van a következő volt lesznek letöltve

### <a name="previous-package-list"></a>Előző csomaglista

* csomag id="Hyak.Common" verzió = "1.0.2-es" targetFramework = "net45"
* csomag id="Microsoft.Azure.Common" verzió = "2.0.4" targetFramework = "net45"
* csomag id="Microsoft.Azure.Common.Dependencies" verzió = "1.0.0" targetFramework = "net45"
* csomag id="Microsoft.Azure.KeyVault" verzió = "1.0.0" targetFramework = "net45"
* csomag id="Microsoft.Bcl" verzió = "1.1.9" targetFramework = "net45"
* csomag id="Microsoft.Bcl.Async" verzió = "1.0.168" targetFramework = "net45"
* csomag id="Microsoft.Bcl.Build" verzió = "1.0.14" targetFramework = "net45"
* csomag id="Microsoft.Net.Http" verzió = "2.2.22" targetFramework = "net45"

### <a name="current-package-list"></a>Aktuális csomaglista

* csomag id="Microsoft.Azure.KeyVault" verzió = "2.0.0-preview" targetFramework = "net45"
* csomag id="Microsoft.Rest.ClientRuntime" verzió = "2.2.0" targetFramework = "net45"
* csomag id="Microsoft.Rest.ClientRuntime.Azure" verzió = "3.2.0" targetFramework = "net45"

## <a name="class-changes"></a>Osztály változások

* **UnixEpoch** osztály el lett távolítva.
* **Base64UrlConverter** osztály neve módosult az **Base64UrlJsonConverter**

## <a name="other-changes"></a>Az egyéb módosítások

* A konfiguráció KV művelet újrapróbálkozási házirend átmeneti hibáiról már támogatja az API ezen verziójára.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* A művelet által visszaadott egy *tároló*, a visszatérési típusa történt egy osztály, amely egy tároló tulajdonság található. A visszatérési típus már *tároló*.
* *PermissionsToKeys* és *PermissionsToSecrets* most már *Permissions.Keys* és *Permissions.Secrets*
* Néhány, a visszatérési típusok változtatást a vezérlő-vezérlősík is vonatkozik.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* A csomag megszakad legfeljebb **Microsoft.Azure.KeyVault.Extensions** és **Microsoft.Azure.KeyVault.Cryptography** a titkosítási műveletek.

