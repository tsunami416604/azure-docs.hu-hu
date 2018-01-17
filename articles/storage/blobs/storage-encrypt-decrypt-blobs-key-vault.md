---
title: "Oktatóanyag: Titkosításához és visszafejtéséhez az Azure Key Vault használatával Azure Storage blobs is |} Microsoft Docs"
description: "Hogyan titkosításához és visszafejtéséhez az Azure Key Vault a Microsoft Azure Storage ügyféloldali titkosítással blob."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: adhurwit
ms.openlocfilehash: 405ccb44c9daf8d555946e6c68ef318ed2b82505
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Oktatóanyag: Titkosításához és visszafejtéséhez az Azure Key Vault használatával a Microsoft Azure Storage blobs
## <a name="introduction"></a>Bevezetés
Ez az oktatóanyag bemutatja, hogyan adhat ellenőrizze az Azure Key Vault storage ügyféloldali titkosítás használata. Végigvezeti titkosítás és visszafejtés egy konzolalkalmazást, ezeknek a technológiáknak a blob.

**Az oktatóanyag áttekintésének várható időtartama:** 20 perc

Áttekintés az Azure Key Vault kapcsolatos információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Áttekintés az Azure Storage ügyféloldali titkosítással kapcsolatos információkért lásd: [ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez szüksége lesz:

* Egy Azure Storage-fiók
* A Visual Studio 2013 vagy újabb verzió
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Ügyféloldali titkosítás áttekintése
Az Azure Storage ügyféloldali titkosítás áttekintéséért lásd: [ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Ügyféloldali titkosítása működése rövid leírása itt található:

1. Az Azure Storage-ügyfél SDK állít elő, a tartalom titkosítási kulcs (CEK), amely a szimmetrikus kulcs egy egyszeri használata.
2. Felhasználói adatok titkosítása a CEK használatával.
3. A CEK ezt követően (titkosított) (KEK-) kulcs titkosítási kulcs használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspár vagy egy szimmetrikus kulcsot kell és is kezelhetők helyileg vagy az Azure Key Vault tárolja. A tárolási ügyfélen soha nem a KEK elérésére. Egyszerűen meghívja a Key Vault által biztosított kulcs alkalmazásburkoló algoritmus. Az ügyfelek használhat egyéni szolgáltatók alkalmazásburkoló/kicsomagolásával Ha szeretné, hogy azok kulcshoz.
4. A titkosított adatok majd feltöltése az Azure Storage szolgáltatásba.

## <a name="set-up-your-azure-key-vault"></a>Az Azure Key Vault beállítása
Ez az oktatóanyag folytatásához kell tennie az alábbi lépésekkel, amelyeket az oktatóanyag vázolt [Ismerkedés az Azure Key Vault](../../key-vault/key-vault-get-started.md):

* Hozzon létre egy kulcstartót.
* A kulcsok vagy titkos hozzáadása a key vault.
* Alkalmazás regisztrálása az Azure Active Directoryban.
* Engedélyezi az alkalmazásnak a kulcsok vagy titkos kulcsok használatára.

Jegyezze fel a ClientID és a ClientSecret, amikor egy alkalmazás regisztrálása az Azure Active Directoryval létrehozott.

A key vault mindkét kulcs létrehozása. Feltételezzük, hogy az oktatóanyag a többi használja a következő szerepel: ContosoKeyVault és TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Hozzon létre egy konzolalkalmazást a csomagok és AppSettings
A Visual Studio új Konzolalkalmazás létrehozása.

A Package Manager konzolon adja hozzá a szükséges nuget-csomagok.

```
Install-Package WindowsAzure.Storage

// This is the latest stable release for ADAL.
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

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

Adja hozzá a következő `using` irányelvek és ügyeljen rá, hogy a System.Configuration mutató hivatkozás hozzáadása a projekthez.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adja hozzá a metódust a Konzolalkalmazás jogkivonat beolvasása
A következő metódust a kulcstároló elérésére hitelesítést igénylő Key Vault-osztályokat használja.

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

## <a name="access-storage-and-key-vault-in-your-program"></a>Tárolás és a Key Vault hozzáférni a program
Adja hozzá a következő kódot a Main függvényre.

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
> Kulcstároló Hálózatiobjektum-modelljei
> 
> Fontos megérteni, hogy vannak-e ténylegesen két Key Vault hálózatiobjektum-modelljei tisztában lenni a: egy REST API-t (névtér: KeyVault) alapján, a másik egy bővítmény ügyféloldali titkosítás.
> 
> A Key Vault ügyfél kommunikál a REST API-t, és megértette a JSON webes kulcsok és titkos dolog, amelyek szerepelnek a Key Vault két típusú.
> 
> A kulcs tároló bővítmények olyan osztályok, úgy tűnik, az Azure Storage ügyféloldali titkosítás kifejezetten létrehozott. Kulcsok (IKey) és egy kulcs feloldó fogalma alapján osztályok illesztőfelület tartalmaznak. Nincsenek két implementációja, amely tudnia kell IKey: RSAKey és SymmetricKey. Most bekövetkezésük egybe a dolgokhoz, amelyek szerepelnek a kulcstároló, de ezen a ponton független osztályok (így a kulcs és a titkos kulcsot tároló-ügyfél által lekért nem valósítja meg a IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>A blob titkosításához, és töltse fel
Adja hozzá a következő kódot blob titkosításához, és töltse fel az Azure-tárfiókot. A **ResolveKeyAsync** használt módszer egy IKey adja vissza.

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
> Ha megnézi a BlobEncryptionPolicy konstruktor, látni fogja, hogy el tudja fogadni a kulcs és/vagy a feloldó. Vegye figyelembe, hogy most egy feloldó nem használata titkosításhoz, mert jelenleg nem támogatja egy alapértelmezett kulcs.
> 
> 

## <a name="decrypt-blob-and-download"></a>Blob visszafejtésére és letöltése
Amikor a feloldó osztályokat célszerű visszafejtési valójában. A titkosításhoz használt kulcs azonosítója társítva a metaadatait, a blob, így nincs lekérni a kulcsot, és ne feledje kulcs és a blob közötti társítás OK. Csak akkor győződjön meg arról, hogy a kulcs a Key Vault marad.   

RSA-kulcs titkos kulcsával marad a Key Vault, így a visszafejtéshez megtörténik, a titkosított kulcs, amely tartalmazza a CEK blob metaadatok alapján kap Key Vault a visszafejtéshez.

Adja hozzá a következő, a blob imént feltöltött visszafejtéséhez.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Más típusú feloldókat könnyebben kulcskezelést, beleértve néhány: AggregateKeyResolver és CachingKeyResolver.
> 
> 

## <a name="use-key-vault-secrets"></a>Key Vault titkos kulcsok használata
A titkos kulcs használata ügyféloldali titkosítás módja a SymmetricKey osztály keresztül mert titkos kulcs lényegében egy szimmetrikus kulcsot. De a fentieknek megfelelően a Key Vault titkos kulcs van leképezve pontosan egy SymmetricKey. Néhány dolgot megértése:

* A kulcs egy SymmetricKey-nak kell lennie egy rögzített hosszúságú: 128, 192, 256-ot, 384 vagy 512 bites.
* A kulcsot egy SymmetricKey a Base64 kódolású legyen.
* A Key Vault titkos kulcsot, amely egy SymmetricKey lesz a Key Vault egy "application/octet-stream" tartalomtípusa rendelkeznie kell.

Íme egy példa a titkos kulcs létrehozásának a kulcstároló, egy SymmetricKey használható PowerShell.
Ne feledje, hogy a nél nagyobb értéket, $key, a csak bemutató célra. A saját kód érdemes ezt a kulcs létrehozásához.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

A Konzolalkalmazás használhatja az adott hívásban, mielőtt beolvasni a titkos kulcsot, mint egy SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
Ennyi az egész. Jó munkát!

## <a name="next-steps"></a>További lépések
Microsoft Azure Storage a C# használatával kapcsolatos további információkért lásd: [Microsoft Azure Storage ügyféloldali kódtára a .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

A Blob REST API kapcsolatos további információkért lásd: [Blob szolgáltatás REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx).

A Microsoft Azure Storage legfrissebb információkért látogasson el a [Microsoft Azure tárolás fejlesztői Blog](http://blogs.msdn.com/b/windowsazurestorage/).
