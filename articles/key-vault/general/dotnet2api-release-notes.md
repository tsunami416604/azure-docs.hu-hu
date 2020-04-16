---
title: Key Vault .NET 2.x API Release Notes| Microsoft dokumentumok
description: A .NET-fejlesztők ezt az API-t használják az Azure Key Vault kódolásához
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 436b9c1569d7c33f79a126cd4d0513bac9385d8a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431916"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 – Kibocsátási megjegyzések és áttelepítési útmutató
A következő információk segít migrálni a 2.0-s verziója az Azure Key Vault-kódtár C# és .NET.  A korábbi verziókhoz írt alkalmazásokat frissíteni kell a legújabb verzió támogatásához.  Ezekre a módosításokra az új és továbbfejlesztett szolgáltatások, például a **Key Vault-tanúsítványok**teljes körű támogatásához van szükség.

## <a name="key-vault-certificates"></a>Key Vault-tanúsítványok

A Key Vault-tanúsítványok x509-es tanúsítványokat kezelnek, és a következő viselkedéseket támogatják:  

* Hozzon létre tanúsítványokat a Key Vault létrehozási folyamatán keresztül, vagy importálja a meglévő tanúsítványokat. Ez magában foglalja az önaláírt és a hitelesítésszolgáltató által létrehozott tanúsítványokat is.
* Biztonságosan tárolhatja és kezelheti az x509-es tanúsítványtárolást a személyes kulcsanyagokkal való interakció nélkül.  
* Olyan házirendek definiálása, amelyek a Kulcstárolót a tanúsítvány életciklusának kezeléséhez irányítják.  
* Adja meg az életciklus-események kapcsolattartási adatait, például a lejárati figyelmeztetéseket és a megújítási értesítéseket.  
* A tanúsítványok automatikus megújítása a kijelölt kibocsátókkal (Key Vault-partner X509-es tanúsítványszolgáltatók és hitelesítésszolgáltatók).* A másodlagos (nem partner) támogatási tanúsítvány biztosítja és a hitelesítésszolgáltatók (nem támogatja az automatikus megújítást).  

## <a name="net-support"></a>.NET támogatás

* **A .NET 4.0-s** verzióját az Azure Key Vault .NET függvénytár 2.0-s verziója nem támogatja.
* **A .NET Framework 4.5.2-es** verzióját az Azure Key Vault .NET függvénytár 2.0-s verziója támogatja.
* **A .NET Standard 1.4-et** az Azure Key Vault .NET függvénytár 2.0-s verziója támogatja

## <a name="namespaces"></a>Névterek

* A **modellek** névtere a **Microsoft.Azure.KeyVault** szolgáltatásról **Microsoft.Azure.KeyVault.Models**verzióra változik.
* A **Microsoft.Azure.KeyVault.Internal** névtér eldobásra kerül.
* A következő Azure SDK-függőségek névterei 

    - **Hyak.Common** most **Microsoft.Rest**.
    - **Hyak.Common.Internals** most **Microsoft.Rest.Szerializálás**.

## <a name="type-changes"></a>Beírt módosítások

* *Secret* változott *SecretBundle*
* *Szótár* *iDictionary-re* változott
* *A\<T> lista, karakterlánc []* *iList\<T>*
* *NextList* módosítása *NextPageLink*

## <a name="return-types"></a>Visszatérési típusok

* **A KeyList** és **a SecretList** mostantól az *IPage\<T>* ad vissza a *ListKeysResponseMessage* helyett
* A létrehozott **BackupKeyAsync** most visszaadja *a BackupKeyResult*értéket , amely *értéket* (biztonsági mentési blobot) tartalmaz. Korábban a metódus be volt csomagolva, és csak az értéket adta vissza.

## <a name="exceptions"></a>Kivételek

* *A KeyVaultClientException* *módosítása KeyVaultErrorException-re*
* A szolgáltatáshiba *megváltozott a kivételtől. Hiba* a *kivételhez. Body.Error.Message .*
* További adatok eltávolítása a **[JsonExtensionData]** hibaüzenetből.

## <a name="constructors"></a>Konstruktorok

* Ahelyett, hogy egy *HttpClient-t* elfogadna konstruktor argumentumként, a konstruktor csak a *HttpClientHandler* vagy *a DelegatingHandler[] programot*fogadja el.

## <a name="downloaded-packages"></a>Letöltött csomagok

Amikor egy ügyfél feldolgoz egy Key Vault-függőséget, a rendszer a következő csomagokat tölti le:

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

## <a name="class-changes"></a>Osztályváltozások

* **UnixEpoch** osztály el lett távolítva.
* **Base64UrlConverter** osztály átnevezése **Base64UrlJsonConverter**.

## <a name="other-changes"></a>További változások

* Az API ezen verziójához hozzá lett adva az átmeneti hibák KV-művelet újrapróbálkozási házirendjének konfigurálása.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* A *tárolót*visszaadó műveletek esetében a visszatérési típus egy **Vault** tulajdonságot tartalmazó osztály volt. A visszatéréstípusa most *Vault*.
* *PermissionsToKeys* és *PermissionsToSecrets* most *Permissions.Keys* és *Permissions.Secrets*
* Bizonyos visszatérési típusok módosításai a vezérlősíkra is vonatkoznak.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* A csomag a **Microsoft.Azure.KeyVault.Extensions** és a **Microsoft.Azure.KeyVault.Cryptography** néven van felosztva a kriptográfiai műveletekhez.

