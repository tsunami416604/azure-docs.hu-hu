---
title: Ügyféloldali titkosítás a .NET for Microsoft Azure Storage szolgáltatással | Microsoft dokumentumok
description: Az Azure Storage-ügyfélkódtár a .NET támogatja az ügyféloldali titkosítást és az Azure Key Vaultdal való integrációt az Azure Storage-alkalmazások maximális biztonsága érdekében.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6cf19292c3675382789ca25af7f9b7f69e9066fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255417"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Ügyféloldali titkosítás és Azure Key Vault a Microsoft Azure Storage-hoz
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
Az [Azure Storage-ügyfélkódtár a .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) támogatja az adatok titkosítását az ügyfélalkalmazásokon belül, mielőtt feltöltené az Azure Storage-ba, és visszafejteni az adatokat, miközben letölti az ügyfélre. A könyvtár is támogatja az [azure key vault](https://azure.microsoft.com/services/key-vault/) a tárfiók kulcskezelésével való integrációt.

A blobok ügyféloldali titkosítással és az Azure Key Vaulthasználatával történő titkosításfolyamatát bemutató részletes oktatóanyagról a [Blobok titkosítása és visszafejtése az Azure Key Vault használatával](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)című témakörben található.

A Java ügyféloldali titkosításáról az [Ügyféloldali titkosítás java-val a Microsoft Azure Storage számára .](storage-client-side-encryption-java.md)

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítás és visszafejtés a borítéktechnikával
A titkosítás és a visszafejtés folyamatai a borítéktechnikát követik.

### <a name="encryption-via-the-envelope-technique"></a>Titkosítás a borítéktechnikán keresztül
A borítéktechnikán keresztüli titkosítás a következőképpen működik:

1. Az Azure storage-ügyfélkódtár létrehoz egy tartalomtitkosítási kulcsot (CEK), amely egy egyszeri használatú szimmetrikus kulcs.
2. A felhasználói adatok titkosítása ezzel a CEK-vel történik.
3. A CEK ezután a kulcstitkosítási kulcs (KEK) használatával burkolódulatba (titkosít). A KEK egy kulcsazonosító val azonosítható, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és helyileg kezelhető vagy az Azure Key Vaults tárolja.
   
    Maga a tárolóügyfél-könyvtár soha nem fér hozzá a KEK-hez. A könyvtár meghívja a Key Vault által biztosított kulcsburkoló algoritmust. A felhasználók egyéni szolgáltatókat használhatnak a kulcstördeléshez/kicsomagoláshoz, ha szükséges.

4. A titkosított adatok at majd feltölti az Azure Storage szolgáltatásba. A burkolt kulcs néhány további titkosítási metaadattal együtt vagy metaadatként (blobon) vagy interpolálva van a titkosított adatokkal (várólistaüzenetek és táblaentitások).

### <a name="decryption-via-the-envelope-technique"></a>Visszafejtés a borítéktechnikával
A borítékon keresztüli visszafejtés a következő módon működik:

1. Az ügyfélkódtár feltételezi, hogy a felhasználó kezeli a kulcs titkosítási kulcs (KEK) helyileg vagy az Azure Key Vaults. A felhasználónak nem kell ismernie a titkosításhoz használt kulcsot. Ehelyett beállítható és használható egy kulcsfeloldó, amely különböző kulcsazonosítókat old meg a kulcsokhoz.
2. Az ügyfélkódtár letölti a titkosított adatokat a szolgáltatásban tárolt titkosítási anyagokkal együtt.
3. A burkolt tartalomtitkosítási kulcs (CEK) ezután kicsomagolja (visszafejti) a kulcstitkosítási kulcs (KEK) használatával. Itt is az ügyféltár nem fér hozzá a KEK.Here again, the client library does not access to KEK. Egyszerűen meghívja az egyéni vagy key vault-szolgáltató kicsomagolási algoritmusát.
4. A tartalomtitkosítási kulcs (CEK) ezután a titkosított felhasználói adatok visszafejtésére szolgál.

## <a name="encryption-mechanism"></a>Titkosítási mechanizmus
A tárolóügyfél-tár [az AES-t](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) használja a felhasználói adatok titkosításához. Pontosabban, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mód AES. Minden szolgáltatás működik kissé másképp, így meg beszéljük mindegyiket itt.

### <a name="blobs"></a>Blobok
Az ügyfélkódtár jelenleg csak a teljes blobok titkosítását támogatja. Pontosabban a titkosítás akkor támogatott, ha a felhasználók az **UploadFrom** metódust vagy az **OpenWrite** metódust használják. A letöltések esetében a teljes és a tartományletöltés is támogatott.

A titkosítás során az ügyfélkódtár létrehoz egy véletlenszerű inicializálási vektort (IV) 16 bájtból, valamint egy 32 bájtos véletlenszerű tartalomtitkosítási kulcsot (CEK), és ezzel az információval borítéktitkosítást hajt végre a blobadatokon. A burkolt CEK és néhány további titkosítási metaadatok ezután blob metaadatokként tárolják a szolgáltatás titkosított blobjával együtt.

> [!WARNING]
> Ha saját metaadatait szerkeszti vagy tölti fel a blobhoz, gondoskodnia kell ametaadatok megőrzéséről. Ha új metaadatokat tölt fel a metaadatok nélkül, a burkolt CEK, IV és egyéb metaadatok elvesznek, és a blobtartalom soha többé nem lesz visszakereshető.
> 
> 

A titkosított blob letöltése magában foglalja a teljes blob tartalmának lekérésével a **DownloadTo**/**BlobReadStream** kényelmi módszerek használatával. A burkolt CEK kicsomagolva van, és a IV-el együtt (ebben az esetben blob metaadatként tárolva) együtt a visszafejtett adatok visszaküldése a felhasználóknak.

Tetszőleges tartomány **(DownloadRange** módszerek) letöltése a titkosított blobban magában foglalja a felhasználók által biztosított tartomány módosítását annak érdekében, hogy kis mennyiségű további adatot kapjon, amelyek felhasználhatók a kért tartomány sikeres visszafejtéséhez.

Minden blobtípus (blokkblobok, lapblobok és hozzáfűző blobok) lehet titkosítani/visszafejteni ezzel a sémával.

### <a name="queues"></a>Üzenetsorok
Mivel a várólistaüzenetek bármilyen formátumúak lehetnek, az ügyféltár olyan egyéni formátumot határoz meg, amely tartalmazza az inicializálási vektort (IV) és a titkosított tartalomtitkosítási kulcsot (CEK) az üzenet szövegében.

A titkosítás során az ügyfélkönyvtár egy véletlenszerű IV 16 bájtot hoz létre egy 32 bájtból álló véletlenszerű CEK-val együtt, és ezzel az információval borítéktitkosítást hajt végre a várólistaüzenet szövegében. A burkolt CEK és néhány további titkosítási metaadat ezután hozzáadódik a titkosított várólista-üzenethez. Ez a módosított üzenet (lásd alább) a szolgáltatás tárolja.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

A visszafejtés során a csomagolt kulcs kileszvonva a várólistaüzenetből, és kicsomagolva lesz. A IV is kinyeri a várólista-üzenet, és együtt használják a csomagolatlan kulcsot visszafejteni a várólista-üzenet adatait. Vegye figyelembe, hogy a titkosítási metaadatok kicsik (500 bájt alatt), így bár beleszámít a 64 KB-os korlátba egy üzenetsor-üzenetesetében, a hatásnak kezelhetőnek kell lennie.

### <a name="tables"></a>Táblák
Az ügyfélkódtár támogatja a beszúrási és csereműveletek entitástulajdonságainak titkosítását.

> [!NOTE]
> Az egyesítés jelenleg nem támogatott. Mivel előfordulhat, hogy a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményez. Az egyesítéshez vagy további szolgáltatáshívásokat kell kezdeményezni a már meglévő entitás szolgáltatásból való olvasásához, vagy egy új kulcsot használ tulajdononként, amelyek teljesítménybeli okokból nem alkalmasak.
> 
> 

A táblaadat-titkosítás a következőképpen működik:  

1. A felhasználók határozzák meg a titkosítandó tulajdonságokat.
2. Az ügyfélkönyvtár 16 bájtos véletlenszerű inicializálási vektort (IV) hoz létre, valamint egy 32 bájtos, véletlenszerű tartalomtitkosítási kulcsot (CEK) minden entitáshoz, és borítéktitkosítást hajt végre az egyes tulajdonságokon, amelyeket tulajdonságonként új IV-vel kell titkosítani. A titkosított tulajdonság bináris adatként van tárolva.
3. A burkolt CEK és néhány további titkosítási metaadat két további fenntartott tulajdonságként tárolódik. Az első fenntartott tulajdonság (_ClientEncryptionMetadata1) egy karakterlánc-tulajdonság, amely a IV. A második fenntartott tulajdonság (_ClientEncryptionMetadata2) egy bináris tulajdonság, amely a titkosított tulajdonságokadatait tartalmazza. A második tulajdonságban (_ClientEncryptionMetadata2) lévő információ maga is titkosított.
4. A titkosításhoz szükséges további fenntartott tulajdonságok miatt a felhasználók mostantól csak 250 egyéni tulajdonsággal rendelkezhetnek 252 helyett. Az entitás teljes méretének 1 MB-nál kisebbnek kell lennie.

Ne feledje, hogy csak karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságokat titkosítani szeretne, azokat karakterláncokká kell konvertálni. A titkosított karakterláncok bináris tulajdonságokként tárolódnak a szolgáltatásban, és a visszafejtés után karakterláncokká alakulnak vissza.

Táblák esetén a titkosítási házirendmellett a felhasználóknak meg kell adniuk a titkosítandó tulajdonságokat. Ez történhet egy [EncryptProperty] attribútum megadásával (a TableEntity-ból származó POCO-entitásokhoz), vagy egy titkosítás-feloldó megadásával a kérelembeállításokban. A titkosítási feloldó olyan delegált, amely partíciókulcsot, sorkulcsot és tulajdonságnevet ad vissza, és logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféltár ezt az információt használja annak eldöntéséhez, hogy egy tulajdonságot titkosítson-e a vezetékbe írás közben. A delegált is lehetővé teszi a logika körül, hogyan tulajdonságok titkosítását. (Ha például X, akkor titkosítsa az A tulajdonságot; egyébként titkosítsa az A és B tulajdonságokat.) Vegye figyelembe, hogy ezeket az adatokat nem szükséges megadni entitások olvasása vagy lekérdezése közben.

### <a name="batch-operations"></a>Kötegelt műveletek
Kötegelt műveletekben ugyanazt a KEK-et fogja használni a kötegművelet összes sorában, mert az ügyfélkódtár kötegelt műveletenként csak egy beállításobjektumot (és így egy házirendet/KEK-et) engedélyez. Az ügyfélkönyvtár azonban belsőleg létrehoz egy új véletlenszerű IV-et és véletlenszerű CEK-t soronként a kötegben. A felhasználók dönthetnek úgy is, hogy a köteg minden műveletéhez különböző tulajdonságokat titkosítanak, ha ezt a viselkedést a titkosítási feloldóban definiálják.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Mivel az entitások titkosítva vannak, nem futtathat olyan lekérdezéseket, amelyek titkosított tulajdonságra szűrnek.  Ha megpróbálja, az eredmények helytelenek lesznek, mert a szolgáltatás a titkosított adatokat titkosítatlan adatokkal próbálja összehasonlítani.
> 
> 
> Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcsfeloldót, amely képes feloldani az eredményhalmaz összes kulcsát. Ha a lekérdezés eredményében szereplő entitás nem oldható fel szolgáltatóval, az ügyféltár hibát fog eljelenni. A kiszolgálóoldali vetületeket végző lekérdezések esetén az ügyféltár alapértelmezés szerint hozzáadja a speciális titkosítási metaadat-tulajdonságokat (_ClientEncryptionMetadata1 és _ClientEncryptionMetadata2) a kijelölt oszlopokhoz.

## <a name="azure-key-vault"></a>Azure Key Vault
Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault használatával a felhasználók titkosíthatják a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat, a . PFX fájlok és jelszavak) hardveres biztonsági modulokkal (HSM) védett kulcsokkal. További információ: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md).

A tárolóügyfél-tár a Key Vault magkönyvtárát használja a kulcsok kezeléséhez az Azure-ban egy közös keretrendszer biztosítása érdekében. A felhasználók a Key Vault-bővítmények könyvtárának további előnyeit is élvezhetik. A bővítmények könyvtára hasznos funkciókat biztosít az egyszerű és zökkenőmentes szimmetrikus/RSA helyi és felhőkulcs-szolgáltatók, valamint az összesítés és a gyorsítótárazás területén.

### <a name="interface-and-dependencies"></a>Felület és függőségek
Három Key Vault-csomag létezik:

* A Microsoft.Azure.KeyVault.Core tartalmazza az IKey és az IKeyResolver. Ez egy kis csomag, amely nem függ. A .NET tárolóügyfél-tárfüggőségként határozza meg.
* A Microsoft.Azure.KeyVault tartalmazza a Key Vault REST-ügyfelet.
* A Microsoft.Azure.KeyVault.Extensions olyan bővítménykódot tartalmaz, amely kriptográfiai algoritmusok implementációit, valamint egy RSAKey-t és egy SymmetricKey-t tartalmaz. Ez a Core és a KeyVault névterektől függ, és lehetővé teszi az összesített feloldó (ha a felhasználók több kulcsszolgáltatót szeretnének használni) és egy gyorsítótárazási kulcsfeloldó meghatározásához. Bár a tárolóügyfél-kódtár nem függ közvetlenül ettől a csomagtól, ha a felhasználók az Azure Key Vault használatával szeretnék tárolni a kulcsokat, vagy a Key Vault-bővítményeket a helyi és felhőalapú kriptográfiai szolgáltatók felhasználására használni, szükségük lesz erre a csomagra.

A Key Vault nagy értékű főkulcsokhoz készült, és a key vaultonkénti szabályozási korlátok at ezt szem előtt tartva tervezték. Ügyféloldali titkosítás key vault, az előnyben részesített modell használata szimmetrikus főkulcsok titkos kulcsként a Key Vault és a helyi gyorsítótárazott. A felhasználóknak a következőket kell tenniük:

1. Hozzon létre egy titkos kapcsolat nélküli kapcsolat nélküli, és töltse fel a Key Vault.
2. A titkos kulcs alapazonosítójának paraméterként való használata a titkos kulcs titkosítási verziójának feloldásához és az adatok helyi gyorsítótárazásához. Gyorsítótárazáshoz használja a CachingKeyResolver-t; felhasználóktól nem várható el, hogy saját gyorsítótárazási logikát valósítsanak meg.
3. Használja a gyorsítótárazási feloldót bemenetként a titkosítási házirend létrehozásakor.

A Key Vault használatával kapcsolatos további információk a [titkosítási kódmintákban](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)találhatók.

## <a name="best-practices"></a>Ajánlott eljárások
A titkosítástámogatása csak a .NET tárolóügyfél-tárában érhető el. A Windows Phone és a Windows Runtime jelenleg nem támogatja a titkosítást.

> [!IMPORTANT]
> Az ügyféloldali titkosítás használatakor vegye figyelembe ezeket a fontos pontokat:
> 
> * Titkosított blobból olvasáskor vagy íráskor használja a teljes blob-feltöltési parancsokat és a tartomány/teljes blob letöltési parancsokat. Ne írjon titkosított blobba olyan protokollműveletek kel, mint a Put Block, put block list, Write Pages, Clear Pages vagy Append Block; ellenkező esetben megsérülhet a titkosított blob, és olvashatatlan.
> * Táblák esetén hasonló megkötés létezik. Ügyeljen arra, hogy a titkosítási metaadatok frissítése nélkül ne frissítse a titkosított tulajdonságokat.
> * Ha metaadatokat állít be a titkosított blobon, felülírhatja a visszafejtéshez szükséges titkosítással kapcsolatos metaadatokat, mivel a metaadatok beállítása nem additív. Ez a pillanatképekre is igaz; ne adja meg a metaadatokat, miközben egy titkosított blob pillanatképét hozza létre. Ha be kell állítani a metaadatokat, először hívja meg a **FetchAttributes** metódust az aktuális titkosítási metaadatok lekéréséhez, és kerülje az egyidejű írásokat a metaadatok beállítása közben.
> * Engedélyezze a **RequireEncryption** tulajdonságot az alapértelmezett kérelembeállításokban azon felhasználók számára, akik csak titkosított adatokkal dolgozhatnak. Lásd alább további információkért.
> 
> 

## <a name="client-api--interface"></a>Ügyfél API / felület
EncryptionPolicy objektum létrehozása közben a felhasználók csak egy kulcsot (implementálásI IKey), csak egy feloldót (implementálásI IKeyResolver), vagy mindkettőt biztosíthatnak. Az IKey az alapvető kulcstípus, amelyet egy kulcsazonosító segítségével azonosítanak, és amely biztosítja a körbefuttatás/kicsomagolás logikáját. Az IKeyResolver egy kulcs feloldására szolgál a visszafejtési folyamat során. Ez határozza meg a ResolveKey metódust, amely egy kulcsazonosítót ad vissza egy IKey-t. Ez lehetővé teszi a felhasználók számára, hogy több helyen kezelt kulcsok közül válasszanak.

* A titkosításhoz mindig a kulcs használatos, és a kulcs hiánya hibát eredményez.
* Visszafejtéshez:
  * A kulcsfeloldó meghívása, ha meg van adva a kulcs leése. Ha a feloldó meg van adva, de nem rendelkezik a kulcsazonosító leképezésével, a rendszer hibát jelez.
  * Ha a feloldó nincs megadva, de kulcs van megadva, a kulcs akkor használatos, ha annak azonosítója megegyezik a szükséges kulcsazonosítóval. Ha az azonosító nem egyezik, a rendszer hibát jelez.

A jelen cikkben található kódpéldák bemutatják a titkosítási szabályzat beállítását és a titkosított adatokkal való munkát, de nem bizonyítják az Azure Key Vault használatával való munkát. A [GitHubon](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) lévő titkosítási minták egy részletesebb, végpontok közötti forgatókönyvet mutatnak be a blobok, várólisták és táblák, valamint a Key Vault-integráció számára.

### <a name="requireencryption-mode"></a>RequireEncryption mód
A felhasználók tetszés szerint engedélyezhetik a működési módot, ahol az összes feltöltést és letöltést titkosítani kell. Ebben a módban a titkosítási házirend nélküli adatok feltöltésére vagy a szolgáltatáson nem titkosított adatok letöltésére tett kísérletek sikertelenek lesznek az ügyfélen. A kérelembeállítások objektum **RequireEncryption** tulajdonsága szabályozza ezt a viselkedést. Ha az alkalmazás titkosítja az Azure Storage-ban tárolt összes objektumot, majd beállíthatja a **RequireEncryption** tulajdonságot a szolgáltatásügyfél-objektum alapértelmezett kérési beállításaiban. Például állítsa **CloudBlobClient.DefaultRequestOptions.RequireEncryption** **true** értékre, hogy az ügyfélobjektumon keresztül végrehajtott összes blobművelet titkosítását követelje meg.


### <a name="blob-service-encryption"></a>Blob szolgáltatás titkosítása
Hozzon létre egy **BlobEncryptionPolicy** objektumot, és állítsa be a kérelembeállításokban (API-nként vagy ügyfélszinten a **DefaultRequestOptions**használatával). Minden mást az ügyfélkönyvtár belsőleg kezel.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

 // Set the encryption policy on the request options.
 BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

 // Upload the encrypted contents to the blob.
 blob.UploadFromStream(stream, size, null, options, null);

 // Download and decrypt the encrypted contents from the blob.
 MemoryStream outputStream = new MemoryStream();
 blob.DownloadToStream(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Várólista-szolgáltatás titkosítása
Hozzon létre egy **QueueEncryptionPolicy** objektumot, és állítsa be a kérelembeállításokban (API-nként vagy ügyfélszinten a **DefaultRequestOptions**használatával). Minden mást az ügyfélkönyvtár belsőleg kezel.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

### <a name="table-service-encryption"></a>Táblaszolgáltatás titkosítása
A titkosítási házirend létrehozása és kérésre történő beállítása mellett meg kell adnia egy **EncryptionResolver-t** a **TableRequestOptions**alkalmazásban, vagy be kell állítania az entitáson az [EncryptProperty] attribútumot.

#### <a name="using-the-resolver"></a>A feloldó használata

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Attribútumok használata
Mint már említettük, ha az entitás megvalósítja TableEntity, majd a tulajdonságokat lehet díszített a [EncryptProperty] attribútum helyett adja meg a **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a tárolási adatok titkosítása további teljesítménytöbbletet eredményez. Létre kell hozni a tartalomkulcsot és a IV-et, magát a tartalmat titkosítani kell, és további metaadatokat kell formázni és feltölteni. Ez a többletterhelés a titkosított adatok mennyiségétől függ. Azt javasoljuk, hogy az ügyfelek mindig teszteljék az alkalmazások teljesítményét a fejlesztés során.

## <a name="next-steps"></a>További lépések
* [Oktatóanyag: Blobok titkosítása és visszafejtése a Microsoft Azure Storage-ban az Azure Key Vault használatával](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Az [Azure Storage-ügyfélkönyvtár letöltése a .NET NuGet csomaghoz](https://www.nuget.org/packages/WindowsAzure.Storage)
* Az Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Ügyfél](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)és Bővítmények csomagok [letöltése](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
* Látogasson el az [Azure Key Vault dokumentációjára](../../key-vault/key-vault-overview.md)
