---
title: A Microsoft Azure Storage a .NET ügyféloldali titkosítás |} Microsoft Docs
description: Az Azure Storage ügyféloldali kódtára a .NET ügyféloldali titkosítás és az Azure Key Vault integration támogatja az Azure Storage-alkalmazások a maximális biztonság érdekében.
services: storage
documentationcenter: .net
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: becfccca-510a-479e-a798-2044becd9a64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cshoe
ms.openlocfilehash: 723ef31c0247d2b2b5e546b4e4fb3d91a516773c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>A Microsoft Azure Storage ügyféloldali titkosítás és az Azure Key Vault
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Azure Storage ügyféloldali kódtára a .NET Nuget-csomag](https://www.nuget.org/packages/WindowsAzure.Storage) támogatja a titkosított adatok ügyfélalkalmazásokon belüli feltöltése az Azure Storage, és az adatok visszafejtése során az ügyfél letöltése előtt. A kódtár emellett támogatja az integrációs [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiókkulcs-kezelés.

Egy részletes oktatóanyag, amely végigvezeti a titkosított blobok ügyféloldali titkosítás és az Azure Key Vault használatával, lásd: [titkosítása és visszafejtése az Azure Key Vault használatával a Microsoft Azure Storage blobs](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Ügyféloldali titkosítás a Java, lásd: [Java a Microsoft Azure Storage ügyféloldali titkosítás](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítás és visszafejtés keresztül a boríték módszer
A titkosítási és visszafejtési folyamatok kövesse a boríték módszer.

### <a name="encryption-via-the-envelope-technique"></a>A boríték technika keresztül titkosítás
A boríték technika keresztül titkosítás az alábbi módon működik:

1. Az Azure storage ügyféloldali kódtár állít elő, a tartalom titkosítási kulcs (CEK), amely a szimmetrikus kulcs egy egyszeri használata.
2. Felhasználói adatok titkosítása a CEK használatával.
3. A CEK ezt követően (titkosított) (KEK-) kulcs titkosítási kulcs használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspár, vagy egy szimmetrikus kulcsot és is kezelhetők helyileg vagy Azure kulcs tárolók tárolja.
   
    A storage ügyféloldali kódtár maga soha nem KEK elérésére. A könyvtár a kulcs alkalmazásburkoló algoritmus Key Vault által biztosított hív meg. Felhasználók maguk dönthetik használandó egyéni szolgáltatók kulcs alkalmazásburkoló/kicsomagolásával Ha szükséges.

4. A titkosított adatok majd feltöltése az Azure Storage szolgáltatásba. A becsomagolt kulcs néhány további titkosítási metaadatokból együtt tárolt metaadatok (a blob) vagy köztes értékkel, a titkosított adatok (üzenetsor-üzeneteket és táblaentitásokat).

### <a name="decryption-via-the-envelope-technique"></a>A boríték technika keresztül visszafejtés
A boríték technika keresztül visszafejtési az alábbi módon működik:

1. Az ügyféloldali kódtár feltételezi, hogy a felhasználó kezeli a fő titkosítási kulcs-(KEK) helyileg vagy az Azure Key tárolók. A felhasználónak nem kell tudni, hogy a titkosításhoz használt kulcs. Ehelyett egy kulcs feloldó, amely különböző kulcs azonosítók kulcsok beállítása is, és használja.
2. Az ügyféloldali kódtár letölti a titkosított adatokat a szolgáltatás tárolt titkosítási anyagok együtt.
3. A burkolt tartalom titkosítási kulcs (CEK) nem burkolatlan (visszafejtett) használatával a fő titkosítási kulcsát (KEK). Itt ebben az esetben az ügyféloldali kódtár nem rendelkezik hozzáféréssel a KEK. Egyszerűen meghívja a az egyéni vagy kulcstároló-szolgáltató kibontásához algoritmus.
4. A tartalom titkosítási kulcs (CEK) szolgál majd visszafejteni a titkosított adatokat.

## <a name="encryption-mechanism"></a>Titkosítási módszer
A storage ügyféloldali kódtár által használt [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) ahhoz, hogy a felhasználói adatok titkosítására. Pontosabban [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) AES módban. Minden szolgáltatás működik némileg eltér, azok itt ismertetik.

### <a name="blobs"></a>Blobok
Az ügyféloldali kódtár jelenleg csak a teljes blobok titkosítása. Titkosítás támogatott kifejezetten, amikor a felhasználó használja a **UploadFrom*** módszerek vagy a **OpenWrite** metódust. Letöltések, mind a teljes és tartomány letöltések támogatott.

Titkosítás során az ügyféloldali kódtár létre egy véletlenszerű inicializálási vektor (IV) 16 bájt, 32 bájt véletlenszerű tartalom titkosítási kulcs (CEK) együtt, és hajtsa végre a boríték ezen információk alapján blob adatok titkosítását. A burkolt CEK és néhány további titkosítási metaadatok tárolása ezután, a blob-metaadatok a titkosított blob szolgáltatás együtt.

> [!WARNING]
> Ha szerkesztése vagy a saját metaadatok a BLOB feltöltése, győződjön meg arról, hogy megőrzi a metaadatok szeretné. Ha blobtárolóba tölti fel ezeket a metaadatokat, a becsomagolt CEK, IV és egyéb metaadatokat nélkül új metaadatok elvesznek, és a blob tartalma soha nem lesznek lekérhető újra.
> 
> 

Egy titkosított blob letöltése magában foglalja a teljes blob használatával a tartalom lekérése a **DownloadTo x /**BlobReadStream ** módszerek kényelmét szolgálja. A burkolt CEK kicsomagolják, és ha a felhasználók számára a visszafejtett adatokat a IV (tárolt blob metaadatai ebben az esetben) együtt használja.

Egy tetszőleges tartomány letöltése (**DownloadRange*** módszerek) a titkosított BLOB magában foglalja a kis mennyiségű sikeresen visszafejtése a kért tartomány használható további adatokat kíván gyűjteni a felhasználók által biztosított tartományon hangolását.

Az összes blob-típusok (blokkblobokat, lapblobokat és hozzáfűző blobok) titkosított/visszafejthető használatával a rendszer.

### <a name="queues"></a>Üzenetsorok
Üzenetsor-üzeneteket tetszőleges méretű lehet, mert az ügyféloldali kódtár egyéni formátum, amely tartalmazza az üzenet szövege az inicializálási vektor (IV) és a titkosított tartalom titkosítási kulcs (CEK) határozza meg.

Titkosítás során az ügyféloldali kódtár hoz létre egy véletlenszerű IV 16 bájt 32 bájt véletlenszerű CEK együtt, és ezen információk alapján a várólista üzenetszöveg boríték titkosítást végez. A burkolt CEK és néhány további titkosítási metaadatokból ezután hozzáadódnak a titkosított üzenetsorban lévő üzenetet. A módosított (lásd alább) tárolása pedig a szolgáltatást.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

A becsomagolt kulcs, a visszafejtés során az üzenetsorban lévő üzenetet kivonjuk és kicsomagolják. A IV is kivonjuk az üzenetsorban lévő üzenetet, és együtt a burkolatlan kulcs használatával fejti vissza a várólista állapotüzenet-adatokat. Vegye figyelembe, hogy a titkosítási metaadatokból kis (alatt 500 bájt), ezért azt egy üzenetsor-üzenetet a 64 KB-os korlát felé száma, amíg a hatás kezelhető kell lennie.

### <a name="tables"></a>Táblák
Az ügyféloldali kódtár támogatja a titkosítást az Entitástulajdonságok szúrhatók be, és cserélje le a műveleteket.

> [!NOTE]
> Egyesítési jelenleg nem támogatott. Mivel a Tulajdonságok részhalmazát, előfordulhat, hogy korábban használatával titkosított egy másik kulcsot, egyszerűen csak az új tulajdonságok egyesítése és a metaadatok frissítése adatok elvesztését eredményezi. Az egyesítés vagy megköveteli a már meglévő entitás olvasni a szolgáltatás további szolgáltatás-hívások, vagy tulajdonságonként egy új kulcsot használ, amelyek mindegyikét nem alkalmasak a teljesítményre vonatkozó megfontolásból.
> 
> 

Tábla adattitkosítás a következőképpen működik:  

1. Felhasználók titkosítandó tulajdonságok megadása
2. Az ügyféloldali kódtár hoz létre egy "véletlenszerű inicializálási vektor (IV) együtt véletlenszerű tartalom titkosítási kulcs (CEK) 32 bájt összes entitás 16 bájt, és az egyes tulajdonságok való származtatás egy új IV tulajdonságonként hagy a boríték titkosítást végez. Bináris adatok tárolja a titkosított tulajdonságot.
3. A burkolt CEK és néhány további titkosítási metaadatok tárolása ezután két további fenntartott tulajdonságként. Az első fenntartott (_ClientEncryptionMetadata1) tulajdonság, amely tartalmazza a IV, a verziót és a becsomagolt kulcs karakterlánc típusú tulajdonság. A második fenntartott (_ClientEncryptionMetadata2) tulajdonság olyan bináris tulajdonság, amely tartalmazza a titkosított tulajdonságok. A második tulajdonság (_ClientEncryptionMetadata2) található információk maga is titkosítva.
4. Ezek fenntartott szükség tovább tulajdonságokra a titkosításhoz, mert felhasználók most már rendelkezik 252 helyett csak 250 egyéni tulajdonságokat. Az entitás teljes mérete 1 MB-nál kevesebb kell lennie.

Vegye figyelembe, hogy a tulajdonságok csak string titkosíthatók. Ha más típusú tulajdonságok titkosítani kell, azok karakterláncok kell konvertálni. A titkosított karakterláncok tárolja a szolgáltatás bináris tulajdonságként, és telepítésekké lesznek átalakítva vissza karakterláncok a visszafejtés után.

Táblák, a titkosítási házirenden kívül a felhasználók fiók kell adnia a titkosítani kell. Ezt megteheti megadásával vagy (az POCO entitások, amelyek a TableEntity) [EncryptProperty] attribútum vagy egy titkosítási feloldó lehetőségek. Egy titkosítási feloldó egy delegált veszi a partíciós kulcs, sorkulcsot és tulajdonság nevét, és logikai érték beolvasása, amely jelzi, hogy a tulajdonság titkosítani kell-e. Titkosítás során az ügyféloldali kódtár döntse el, hogy a tulajdonság titkosítani kell-e az átvitel közbeni írása során fogja használni ezt az információt. A delegált is körül hogyan tulajdonságok vannak titkosítva logika lehetőségét biztosítja. (Például, ha X, majd titkosítása A tulajdonság; ellenkező esetben a Tulajdonságok A és b titkosítása) Vegye figyelembe, hogy nincs szükség arra, hogy ezek az információk olvasása vagy entitás lekérdezése közben.

### <a name="batch-operations"></a>Kötegelt műveletek
A kötegelt műveletek az azonos KEK is használni fogja kötegelt művelet sorait, mivel az ügyféloldali kódtár csak egy beállítások objektum (és ezáltal egy házirend vagy KEK) kötegelt művelet. Azonban az ügyféloldali kódtár belső létrehoz egy új véletlenszerű IV és soronkénti véletlenszerű CEK a kötegben. Felhasználók is beállíthatja a kötegben minden művelethez más tulajdonságokkal titkosítására Ez a viselkedés meghatározása a titkosítási feloldó.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Az entitások titkosítva vannak, mert titkosított szűrő lekérdezéseket nem futtatható.  Kísérli meg, ha eredmények lesz helytelen, mert a szolgáltatás volna az összehasonlítani kívánt titkosított adatok nem titkosított adatokat.
> 
> 
Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs feloldó, amely képes feloldani az eredménykészletben a kulcsokat. Ha a lekérdezés eredményében található entitás nem oldható fel egy szolgáltatót, az ügyféloldali kódtár kivételhibát hiba. A lekérdezés kiszolgálóoldali leképezések végző az ügyféloldali kódtár felveszi különleges titkosítási metaadat-tulajdonságainak (_ClientEncryptionMetadata1 és _ClientEncryptionMetadata2) alapértelmezés szerint a kijelölt oszlopokban.

## <a name="azure-key-vault"></a>Azure Key Vault
Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault használatával felhasználók titkosíthatják a kulcsokat és titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat. PFX-fájlok és a jelszavakat) hardveres biztonsági modulokkal (HSM) védett kulcsokkal. További információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md).

A storage ügyféloldali kódtára a Key Vault alap függvénytár használja ahhoz, hogy egy közös keretrendszer kulcsok kezelése az Azure biztosít. A felhasználók is kapnak további előnye, hogy a Key Vault bővítmények szalagtárat használ. A bővítmények kódtár egyszerű és zökkenőmentes Symmetric/RSA helyi és a fő szolgáltatók, valamint összesítő és a gyorsítótár hasznos funkciókat biztosít.

### <a name="interface-and-dependencies"></a>Felület és a függőségek
Nincsenek három Key Vault csomagok:

* Microsoft.Azure.KeyVault.Core IKey és IKeyResolver tartalmazza. Nem rendelkezik függőségekkel rendelkező kis csomag is. A storage ügyféloldali kódtára a .NET határozza meg azt a függőség beállításához.
* Microsoft.Azure.KeyVault a Key Vault REST ügyfél tartalmazza.
* Microsoft.Azure.KeyVault.Extensions bővítmény kódot tartalmaz, amely tartalmazza többek között a titkosítási algoritmusok és egy RSAKey és egy SymmetricKey. A Core és a KeyVault névterek függ, és adható meg az összesített feloldó (amikor a felhasználók szolgáltatókat szeretnének használni több kulcs) és egy kulcs gyorsítótárazása funkciókat biztosítja. Bár a storage ügyféloldali kódtár közvetlenül függ a csomag, ha a felhasználók szeretné használni az Azure Key Vault, a kulcsok tárolására, vagy a helyi felhasználását és a kriptográfiai szolgáltatók felhőalapú a Key Vault bővítmények használatát, hogy meg kell ezt a csomagot.

Key Vault végzi a nagy értékű főkulcsok, és a Key Vault / szabályozó korlátozások figyelembe ennek kialakítása. A Key Vault ügyféloldali titkosítás elvégzésekor modellt hoz használja szimmetrikus master Key Vault a titkos kulcsok tárolása és gyorsítótárazott helyileg. Felhasználók a következőket kell tennie:

1. Kapcsolat nélküli titkos kulcs létrehozása, és töltse fel a Key Vault.
2. Paraméterként a titkos kulcsot base azonosító használatával javítsa ki a titkosításhoz a titkos kulcs jelenlegi verziója, és ezek az információk helyileg gyorsítótárazzák. Használja a CachingKeyResolver gyorsítótárazás; felhasználók-azonosítóknak nem megvalósításához a saját logikát gyorsítótárazását.
3. A gyorsítótárazása használja bemeneti adatokként a titkosítási házirend létrehozása közben.

Key Vault használatának kapcsolatos további információk találhatók a [titkosító mintakódok](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Ajánlott eljárások
Titkosítás támogatása csak a storage ügyféloldali kódtára a .NET érhető el. Windows Phone és Windows-Futtatókörnyezetű jelenleg támogatja a titkosítást.

> [!IMPORTANT]
> Vegye figyelembe a fontos pontok ügyféloldali titkosítás használata esetén:
> 
> * Ha egy titkosított blob írásakor vagy olvasásakor, használja a teljes blob feltöltése és tartomány/egész blob letöltési parancsok. Egy titkosított blobba protokoll műveletek, például a Put blokk, Put tiltólista, írási lapok, törölje a jelet lapok vagy hozzáfűzése blokk; használatával elkerülése érdekében Ellenkező esetben előfordulhat, hogy a titkosított blob sérült, és nem olvasható teszi.
> * A táblákhoz hasonló korlátozás létezik. Ügyeljen rá, nem titkosított tulajdonságainak módosítása a paramétertitkosítási metaadatokat frissítése nélkül.
> * Ha a titkosított blob metaadatai állít be, felülírhatja a szükséges jogokkal, mivel a metaadatok beállítása nem additívak titkosítással kapcsolatos metaadatok. Ez akkor is igaz, a pillanatképek; Ne adjon meg a metaadat egy titkosított blob pillanatképének létrehozása során. Ha metaadatokat be kell állítani, ügyeljen arra, hogy hívja a **FetchAttributes** módszert, először az aktuális titkosítási metaadatot beszerezni, és közben a metaadatok beállítása egyidejű írási műveletek megakadályozásához.
> * Engedélyezze a **RequireEncryption** tulajdonság az alapértelmezett beállításokat olyan felhasználóknál, akik csak együtt kell működnie a titkosított adatokat. További információkért lásd az alábbi.
> 
> 

## <a name="client-api--interface"></a>Ügyfél API-ja / felület
Egy EncryptionPolicy objektum létrehozása során a felhasználók megadhatják csak kulcsot (végrehajtási IKey), csak egy feloldó (végrehajtási IKeyResolver), vagy mindkettőt. IKey, amely azonosítja a kulcsazonosító, és a logikai biztosít, amely alkalmazásburkoló/kicsomagolásával alapvető kulcs típusa. IKeyResolver feloldásához egy kulcsot a visszafejtés során használt. Meghatározza a ResolveKey módszere, amely egy megadott kulcsazonosítójával IKey adja vissza. Ez biztosítja a felhasználók több helyen felügyelt több kulcsok közötti részére.

* A titkosításhoz használja a rendszer mindig, és egy kulcs hiányában hibát eredményez.
* A visszafejtéshez:
  * A kulcs feloldó meghívták, ha meg van adva a kulcs beszerzése. A feloldó van megadva, de nem rendelkezik a kulcsazonosító társítás, ha hiba fordul elő.
  * Ha nincs megadva a feloldó, de egy kulcs van megadva, a kulcs akkor használatos, ha az azonosítója a szükséges kulcs azonosítója megegyezik-e. Az azonosító nem egyezik, ha hiba fordul elő.

Ebben a cikkben szereplő példák bemutatják, egy titkosítási házirend és a titkosított adatok használata, de nem működik az Azure Key Vault mutatnak. A [titkosítási minták](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) a Githubon mutassa be a részletes végpont forgatókönyv BLOB, üzenetsorok és táblák, jelszavat Key Vault-integráció.

### <a name="requireencryption-mode"></a>RequireEncryption mód
Felhasználók engedélyezheti üzemmódot ahol feltöltések és a letöltött fájl titkosítva kell lennie. Ebben a módban a megpróbálja feltölteni az adatokat egy titkosítási házirend nélkül, vagy a szolgáltatás a nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A **RequireEncryption** a kérelem beállítások objektum a tulajdonság szabja meg ezt a viselkedést. Ha az alkalmazás titkosítja az Azure Storage-ban tárolt összes objektumot, majd beállíthatja a **RequireEncryption** az alapértelmezett beállításokat a szolgáltatás ügyfél objektum tulajdonságát. Állítsa például **CloudBlobClient.DefaultRequestOptions.RequireEncryption** való **igaz** kényszeríteni a titkosítást, az összes blob-ügyfél objektum keresztül végrehajtott műveleteket.


### <a name="blob-service-encryption"></a>Blob service encryption
Hozzon létre egy **BlobEncryptionPolicy** objektumot, majd állítsa be a kérelem beállításai (API vagy egy ügyfél szinten használatával **DefaultRequestOptions**). Minden más kezelik az ügyféloldali kódtár által belsőleg.

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

### <a name="queue-service-encryption"></a>Queue service encryption
Hozzon létre egy **QueueEncryptionPolicy** objektumot, majd állítsa be a kérelem beállításai (API vagy egy ügyfél szinten használatával **DefaultRequestOptions**). Minden más kezelik az ügyféloldali kódtár által belsőleg.

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

### <a name="table-service-encryption"></a>TABLE szolgáltatás titkosítási
Egy titkosítási házirend létrehozása és beállítása a lehetőségek a kívül meg kell adnia egy **EncryptionResolver** a **TableRequestOptions**, vagy állítsa a [EncryptProperty] attribútumot az entitás.

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
Említetteknek megfelelően, ha az entitás TableEntity valósítja meg, majd a tulajdonságok is látható el a [EncryptProperty] attribútummal megadása helyett a **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a tároló eredményezi további teljesítményigény titkosítása. A tartalomkulcs és IV kell létrejönnie, a tartalom titkosítva kell lennie és további metaadatok kell kell formázva és fel kell tölteni. Ez a terhelés a titkosított adatok mennyisége függvényében. Azt javasoljuk, hogy az ügyfelek mindig tesztelje az alkalmazások fejlesztése során teljesítmény.

## <a name="next-steps"></a>További lépések
* [Oktatóanyag: Titkosításához és visszafejtéséhez az Azure Key Vault használatával a Microsoft Azure Storage blobs](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Töltse le a [Azure Storage ügyféloldali kódtára a .NET NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.Storage)
* Töltse le az Azure Key Vault NuGet [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [ügyfél](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/), és [bővítmények](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) csomagok  
* Látogasson el a [az Azure Key Vault dokumentáció](../../key-vault/key-vault-whatis.md)
