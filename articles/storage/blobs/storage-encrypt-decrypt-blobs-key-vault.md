---
title: 'Oktatóanyag: Az Azure Key Vault használatával Azure Storage-blobok titkosítása és visszafejtése |} A Microsoft Docs'
description: Hogyan titkosítása és visszafejtése az Azure Key Vault a Microsoft Azure Storage ügyféloldali titkosítás egy blobhoz.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 09bb74ead0ff52cc7a70170357ddc54a91bf00d9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239407"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Oktatóanyag: Az Azure Key Vault használatával a Microsoft Azure Storage blobok titkosítása és visszafejtése
## <a name="introduction"></a>Bevezetés
Ez az oktatóanyag bemutatja, hogyan javíthatja a storage ügyféloldali titkosítás az Azure Key Vault használata. Azt ismerteti, hogyan titkosításához és visszafejtéséhez egy konzolalkalmazást, ezeknek a technológiáknak a blobok.

**Becsült időtartama:** 20 perc

Áttekintés az Azure Key Vault kapcsolatos információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Áttekintés az Azure Storage ügyféloldali titkosítással kapcsolatos információkért lásd: [ügyféloldali titkosítás és a Microsoft Azure Storage for Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez szüksége lesz:

* Azure Storage-fiók
* A Visual Studio 2013-as vagy újabb
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Ügyféloldali titkosítás áttekintése
Az Azure Storage ügyféloldali titkosítás áttekintését lásd: [ügyféloldali titkosítás és az Azure Key Vault a Microsoft Azure Storage-hoz](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Itt látható egy rövid leírást ügyféloldali titkosítása működése:

1. Az Azure Storage ügyféloldali SDK-tartalom titkosítási kulcs (CEK), amely egyszer használható egy szimmetrikus kulcs állít elő.
2. Vásárlói adatok titkosítását a rendszer a CEK.
3. A CEK ezt követően (titkosítva) a kulcstitkosítási kulcs-(KEK) használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspárt alkotnak, vagy egy szimmetrikus kulcsot és is kezelhetők helyileg vagy az Azure Key Vaultban tárolt. A Storage-kliens maga soha nem rendelkezik hozzáféréssel a KEK. Csak a Key Vault által biztosított kulcs alkalmazásburkoló algoritmus hív meg. Ügyfelek kiválaszthatják a használandó egyéni szolgáltatók alkalmazásburkoló/kicsomagolási Ha szeretné, hogy azok kulcsot.
4. A titkosított adatok majd fel van töltve az Azure Storage szolgáltatásba.

## <a name="set-up-your-azure-key-vault"></a>Az Azure Key Vault beállítása
Folytassa az oktatóanyagot, kövesse az alábbi lépéseket, amelyek az oktatóanyagban leírt kell [első lépései az Azure Key Vault](../../key-vault/key-vault-get-started.md):

* Kulcstartó létrehozása.
* Egy kulcs vagy titkos kód hozzáadása a key vaultban.
* Alkalmazás regisztrálása az Azure Active Directoryval.
* Engedélyezze az alkalmazás használatához a kulcs vagy titkos kód.

Jegyezze fel a ClientID és az Azure Active Directory-alkalmazás regisztrálásakor létrehozott ClientSecret.

Hozzon létre a mindkét kulcsot a key vaultban. Feltételezzük, hogy az oktatóanyag további részeinek esetében használja a következő nevekkel: ContosoKeyVault és TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Hozzon létre egy konzolalkalmazást a csomagok és AppSettings
A Visual Studióban hozzon létre egy új konzolalkalmazást.

Adja hozzá a Package Manager Console szükséges nuget-csomagok.

```
Install-Package WindowsAzure.Storage
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adja hozzá az App.Config AppSettings.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adja hozzá a következő `using` irányelvek és ellenőrizze, hogy a System.Configuration hivatkozás hozzáadása a projekthez.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;        
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adjon hozzá egy metódus lekéréséhez a Konzolalkalmazás egy tokent
A következő metódust, amely a key vault eléréséhez hitelesítenie kell a Key Vault-osztályokat használja.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        ConfigurationManager.AppSettings["clientId"],
        ConfigurationManager.AppSettings["clientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-storage-and-key-vault-in-your-program"></a>A program a tárolási és Key Vault eléréséhez
Adja hozzá a következő kódot a fő függvényt.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    ConfigurationManager.AppSettings["accountName"],
       ConfigurationManager.AppSettings["accountKey"]);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> A Key Vault objektummodellt
> 
> Fontos megérteni, hogy nincsenek-e valójában két Key Vault objektummodellt érdemes figyelembe vennie: egyet a REST API (KeyVault névtér) alapján, és más ügyféloldali titkosítás-bővítmény.
> 
> A Key Vault-ügyfélnek a REST API-val kommunikál, és tisztában van azzal, JSON Web kulcsok és titkos kulcsok a Key Vaultban lévő dolog kétféle.
> 
> A Key Vault-bővítmények olyan osztályok, úgy tűnik, az Azure Storage ügyféloldali titkosítás speciálisan létrehozott. Kulcsok (Rendszerállapotkulcsot) és a egy kulcs feloldó fogalma alapján osztályok illesztőfelületet tartalmaznak. Két megvalósításai, Rendszerállapotkulcsot, ismernie kell: RSAKey és SymmetricKey. Most már előfordulásuk egybeessen a Key Vaultban lévő dolog, de ezen a ponton azok független osztályok (így a kulcs és a Key Vault-ügyfél által beolvasott titkos kulcs nem implementálja a Rendszerállapotkulcsot).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Blob titkosítása és feltöltése
Adja hozzá a következő kódot a blobok titkosítását, és töltse fel az Azure storage-fiók. A **ResolveKeyAsync** használt módszer változón adja vissza.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
// Remember that we used the names contosokeyvault and testrsakey1.
var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Ha megtekinti a BlobEncryptionPolicy konstruktort, látni fogja, hogy el tudja fogadni a kulcs és/vagy egy feloldó. Vegye figyelembe, hogy most egy feloldó nem használata titkosításhoz, mert jelenleg nem támogatja a alapértelmezett kulcs.
> 
> 

## <a name="decrypt-blob-and-download"></a>Blob visszafejtéséhez, és töltse le
Visszafejtési valójában, amikor a feloldó-osztályokkal jelentéssel bírnak. A titkosításhoz használt kulcs Azonosítóját a blob metaadatait, a társítva, ezért nem lehet lekérni a kulcsot, és ne felejtse el a kulcs és a blob közötti társítás OK. Csak akkor ügyeljen arra, hogy a kulcsot a Key Vaultban továbbra is.   

A titkos kulcsot az RSA-kulcsa marad a Key Vaultban, így a visszafejtéshez előfordul, a titkosított kulcsát, a blob metaadatai, amely tartalmazza a CEK megkap Key Vault visszafejtéshez.

Adja hozzá a következő, az imént feltöltött blobon visszafejtéséhez.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Van néhány feloldók könnyebben kulcsok kezelését, beleértve a más típusú: AggregateKeyResolver és CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>A Key Vault titkos kulcsok használata
A titkos kulcs használata ügyféloldali titkosítás módja a SymmetricKey osztállyal mert titkos kód lényegében egy szimmetrikus kulcsot. De a fentieknek megfelelően a Key Vaultban titkos kulcs nem feleltethető meg pontosan egy SymmetricKey. Néhány dolgot megismerése:

* A kulcsot egy SymmetricKey a rögzített hosszúságú lehet, hogy rendelkezik: 128, 192, 256, 384 vagy 512 bites.
* A kulcsot egy SymmetricKey a Base64-kódolású kell lennie.
* A Key Vault titkos kulcsából, amely egy SymmetricKey lesz rendelkeznie kell egy "application/octet-stream" tartalomtípusa Key vaultban.

Íme egy példa a PowerShell, a titkos kulcs létrehozása a kulcstároló, amelyet egy SymmetricKey használható.
Vegye figyelembe, hogy a jelenleg, $key, érték csak a bemutatási célból. A saját kódját érdemes létrehozni ezt a kulcsot.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

A Konzolalkalmazás használhatja a azonos hívást, mielőtt beolvasni a titkos kód, mint egy SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
Ennyi az egész. Jó munkát!

## <a name="next-steps"></a>További lépések
A C# használatával a Microsoft Azure Storage használatával kapcsolatos további információkért lásd: [a Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://msdn.microsoft.com/library/azure/dn261237.aspx).

A Blob REST API-val kapcsolatos további információkért lásd: [Blob Service REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx).

A Microsoft Azure Storage a legfrissebb információkért nyissa meg a [a Microsoft Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage/).
