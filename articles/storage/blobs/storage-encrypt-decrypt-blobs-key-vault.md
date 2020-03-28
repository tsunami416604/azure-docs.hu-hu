---
title: Oktatóanyag – Blobok titkosítása és visszafejtése az Azure Key Vault használatával
titleSuffix: Azure Storage
description: Ismerje meg, hogyan titkosíthatja és fejtse vissza a blobokat ügyféloldali titkosítással az Azure Key Vault használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c83e56a47f4b212a5612cb9e6965ce8e73228dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74892889"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Oktatóanyag – Blobok titkosítása és visszafejtése az Azure Key Vault használatával

Ez az oktatóanyag ismerteti, hogyan használhatja az ügyféloldali tárolási titkosítást az Azure Key Vault használatával. Bemutatja, hogyan titkosíthatja és fejtheti vissza a blobot egy konzolalkalmazásban ezekkel a technológiákkal.

**Az oktatóanyag áttekintésének várható időtartama:** 20 perc

Az Azure Key Vaultról a [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)című témakörben olvashat.

Az Azure Storage ügyféloldali titkosításával kapcsolatos további információkért lásd: [Ügyféloldali titkosítás és az Azure Key Vault for Microsoft Azure Storage.](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure Storage-fiók
* Visual Studio 2013-as vagy újabb verzió
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Ügyféloldali titkosítás áttekintése

Az Azure Storage ügyféloldali titkosításának áttekintését az [Ügyféloldali titkosítás és az Azure Key Vault for Microsoft Azure Storage című](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) témakörben találja.

Itt van egy rövid leírást, hogyan ügyféloldali titkosítás működik:

1. Az Azure Storage-ügyfél SDK létrehoz egy tartalom titkosítási kulcs (CEK), amely egy egyszeri használatszimmetrikus kulcs.
2. Az ügyféladatok titkosítása ezzel a CEK-vel történik.
3. A CEK ezután a kulcstitkosítási kulcs (KEK) használatával burkolódulatba (titkosít). A KEK egy kulcsazonosító val azonosítható, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és helyileg kezelhető vagy az Azure Key Vaultban tárolható. Maga a storage-ügyfél soha nem fér hozzá a KEK-hez. Csak meghívja a key wrap algoritmus, amely a Key Vault által biztosított. Az ügyfelek egyéni szolgáltatókat használhatnak a kulcstördeléshez/kicsomagoláshoz, ha akarják.
4. A titkosított adatok at majd feltölti az Azure Storage szolgáltatásba.

## <a name="set-up-your-azure-key-vault"></a>Az Azure Key Vault beállítása

Az oktatóanyag folytatásához a következő lépéseket kell megtennie, amelyeket az [oktatóanyag rövid útmutatója ismertet: Titkos kulcs beállítása és lekérése az Azure Key Vaultból egy .NET webalkalmazás használatával:](../../key-vault/quick-create-net.md)

* Kulcstartó létrehozása.
* Adjon hozzá egy kulcsot vagy titkos kulcsot a kulcstartóhoz.
* Regisztráljon egy alkalmazást az Azure Active Directoryval.
* Engedélyezze az alkalmazás számára a kulcs vagy titkos kulcs használatát.

Jegyezze fel az ügyfélazonosítót és az ügyféltitkos kulcsot, amely egy alkalmazás Azure Active Directoryval való regisztrálásakor jött létre.

Hozza létre mindkét kulcsot a key vaultban. Feltételezzük, hogy a többi az oktatóanyag, hogy a következő neveket használta: ContosoKeyVault és TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Konzolalkalmazás létrehozása csomagokkal és AppSettings-al

A Visual Studióban hozzon létre egy új konzolalkalmazást.

Adja hozzá a szükséges nuget csomagokat a Package Manager konzolhoz.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adja hozzá az AppSettings alkalmazást az App.Config fájlhoz.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adja hozzá `using` a következő irányelveket, és győződjön meg arról, hogy hivatkozást aSystem.Configuration a projekthez.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Egy token bekérésével egy módszer hozzáadása a konzolalkalmazáshoz

A következő módszert használják a Key Vault osztályok, amelyek hitelesítésére a key vault eléréséhez.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Az Azure Storage és a Key Vault elérése a programban

A Main() metódusban adja hozzá a következő kódot.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey");
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Key Vault objektummodellek
> 
> Fontos megérteni, hogy valójában két Key Vault-objektummodellvan tudatában: az egyik a REST API-n (KeyVault névtér), a másik pedig az ügyféloldali titkosítás kiterjesztése.
> 
> A Key Vault-ügyfél együttműködik a REST API-val, és ismeri a JSON webkulcsokat és a Key Vaultban található két fajta dolog titkos titkait.
> 
> A Key Vault-bővítmények olyan osztályok, amelyek úgy tűnik, kifejezetten az ügyféloldali titkosítás az Azure Storage-ban. A kulcsfeloldó koncepcióján alapuló kulcsok (IKey) és osztályok felületét tartalmazzák. Az IKey két implementációja van, amelyeket tudnia kell: RSAKey és SymmetricKey. Most ezek történetesen egybeesnek a key vaultban található dolgokkal, de ezen a ponton független osztályok (így a Key Vault-ügyfél által lekért kulcs és titkos kulcs nem valósítja meg az IKey-t).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Blob titkosítása és feltöltése

Adja hozzá a következő kódot egy blob titkosításához, és töltse fel az Azure-tárfiókba. A használt **ResolveKeyAsync** metódus ikey-t ad vissza.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Ha megnézi a BlobEncryptionPolicy konstruktor, látni fogja, hogy képes elfogadni egy kulcsot és/vagy egy feloldó. Ne feledje, hogy jelenleg nem használhat feloldót a titkosításhoz, mert jelenleg nem támogatja az alapértelmezett kulcsot.

## <a name="decrypt-blob-and-download"></a>Blob visszafejtése és letöltése

A visszafejtés nek valóban van értelme a Resolver osztályok használatakor. A titkosításhoz használt kulcs azonosítója a blobhoz van társítva a metaadatokban, így nincs ok a kulcs lekérésére és a kulcs és a blob közötti társítás megjegyzésére. Csak meg kell győződnie arról, hogy a kulcs a Key Vaultban marad.   

Az RSA-kulcs titkos kulcsa a Key Vaultban marad, így a visszafejtéshez a CEK-t tartalmazó blob metaadatok titkosított kulcsa a Key Vaultba kerül visszafejtésre.

Adja hozzá az alábbiakat az imént feltöltött blob visszafejtéséhez.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Van néhány más típusú feloldók, hogy a kulcskezelése könnyebb, többek között: AggregateKeyResolver és CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>A Key Vault titkos kulcsainak használata

A titkos kulcs ügyféloldali titkosítással való használatának módja a SymmetricKey osztályon keresztül történik, mivel a titkos kulcs lényegében egy szimmetrikus kulcs. De, mint fentebb említettük, egy titkos kulcs a Key Vault nem pontosan egy SymmetricKey. Van néhány dolog, amit meg kell értened:

* A SymmetricKey kulcsának rögzített hosszúságúnak kell lennie: 128, 192, 256, 384 vagy 512 bit.
* A symmetricKey kulcsának Base64 kódolásúnak kell lennie.
* A Key Vault titkos, hogy a szimmetrikus kulcs ként fog használni kell a "application/oktett-stream" tartalomtípusa a Key Vaultban.

Íme egy példa a PowerShell egy titkos kulcs létrehozása a Key Vaultban, amely szimmetrikus kulcsként használható.
Kérjük, vegye figyelembe, hogy a $key kemény kódolt érték csak demonstrációs célokat szolgál. A saját kódjában érdemes létrehozni ezt a kulcsot.

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

A konzolalkalmazásban használhatja ugyanazt a hívást, mint korábban, hogy beolvassa ezt a titkos kulcsot szimmetrikus kulcsként.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Ennyi az egész. Jó munkát!

## <a name="next-steps"></a>További lépések

A Microsoft Azure Storage C# használatával kapcsolatos további tudnivalókért tekintse meg a Microsoft Azure Storage Client Library for .NET című [témakört.](https://msdn.microsoft.com/library/azure/dn261237.aspx)

A Blob REST API-ról további információt a [Blob Service REST API című témakörben talál.](https://msdn.microsoft.com/library/azure/dd135733.aspx)

A Microsoft Azure Storage szolgáltatással kapcsolatos legfrissebb információkért látogasson el a [Microsoft Azure Storage Team blogjára.](https://blogs.msdn.com/b/windowsazurestorage/)
