---
title: A Microsoft Azure Storage ügyféloldali Java titkosításának |} Microsoft Docs
description: Az Azure Storage ügyféloldali kódtára a Java ügyféloldali titkosítás és az Azure Key Vault integration támogatja az Azure Storage-alkalmazások a maximális biztonság érdekében.
services: storage
documentationcenter: java
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 3df49907-554c-404a-9b0c-b3e3269ad04f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.openlocfilehash: b4f3814ac2dbc8b74cef8f5fcb0540b7509efa0d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>A Microsoft Azure tárolás Java ügyféloldali titkosítás és az Azure Key Vault
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Azure Storage ügyféloldali kódtára a Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) támogatja a titkosított adatok ügyfélalkalmazásokon belüli feltöltése az Azure Storage, és az adatok visszafejtése során az ügyfél letöltése előtt. A kódtár emellett támogatja az integrációs [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiókkulcs-kezelés.

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
Az ügyféloldali kódtár jelenleg csak a teljes blobok titkosítása. Titkosítás támogatott kifejezetten, amikor a felhasználó használja a **feltöltése*** módszerek vagy a **openOutputStream** metódust. Letöltések, mind a teljes és tartomány letöltések támogatott.  

Titkosítás során az ügyféloldali kódtár létre egy véletlenszerű inicializálási vektor (IV) 16 bájt, 32 bájt véletlenszerű tartalom titkosítási kulcs (CEK) együtt, és hajtsa végre a boríték ezen információk alapján blob adatok titkosítását. A burkolt CEK és néhány további titkosítási metaadatok tárolása ezután, a blob-metaadatok a titkosított blob szolgáltatás együtt.

> [!WARNING]
> Ha szerkesztése vagy a saját metaadatok a BLOB feltöltése, győződjön meg arról, hogy megőrzi a metaadatok szeretné. A metaadatok nélkül új metaadatokkal tölt fel, ha a becsomagolt CEK, IV és egyéb metaadatokat elvész, és a blob tartalma soha nem lesznek lekérhető újra.
> 
> 

Egy titkosított blob letöltése magában foglalja a teljes blob használatával a tartalom lekérése a **letöltési * / openInputStream** kényelmi módszerek. A burkolt CEK kicsomagolják, és ha a felhasználók számára a visszafejtett adatokat a IV (tárolt blob metaadatai ebben az esetben) együtt használja.

Egy tetszőleges tartomány letöltése (**downloadRange*** módszerek) a titkosított BLOB magában foglalja a kis mennyiségű sikeresen visszafejtése a kért használható további adatokat kíván gyűjteni a felhasználók által biztosított tartomány beállítása tartomány.  

Az összes blob-típusok (blokkblobokat, lapblobokat és hozzáfűző blobok) titkosított/visszafejthető használatával a rendszer.

### <a name="queues"></a>Üzenetsorok
Üzenetsor-üzeneteket tetszőleges méretű lehet, mert az ügyféloldali kódtár egyéni formátum, amely tartalmazza az üzenet szövege az inicializálási vektor (IV) és a titkosított tartalom titkosítási kulcs (CEK) határozza meg.  

Titkosítás során az ügyféloldali kódtár hoz létre egy véletlenszerű IV 16 bájt 32 bájt véletlenszerű CEK együtt, és ezen információk alapján a várólista üzenetszöveg boríték titkosítást végez. A burkolt CEK és néhány további titkosítási metaadatokból ezután hozzáadódnak a titkosított üzenetsorban lévő üzenetet. A módosított (lásd alább) tárolása pedig a szolgáltatást.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
4. Ezek fenntartott szükség tovább tulajdonságokra a titkosításhoz, mert felhasználók most már rendelkezik 252 helyett csak 250 egyéni tulajdonságokat. Az entitás teljes mérete 1MB-nál kevesebb kell lennie.  
   
   Vegye figyelembe, hogy a tulajdonságok csak string titkosíthatók. Ha más típusú tulajdonságok titkosítani kell, azok karakterláncok kell konvertálni. A titkosított karakterláncok tárolja a szolgáltatás bináris tulajdonságként, és telepítésekké lesznek átalakítva vissza karakterláncok a visszafejtés után.
   
   Táblák, a titkosítási házirenden kívül a felhasználók fiók kell adnia a titkosítani kell. Ezt megteheti megadásával vagy (az POCO entitások, amelyek a TableEntity) [titkosítása] attribútum vagy egy titkosítási feloldó lehetőségek. Egy titkosítási feloldó egy delegált veszi a partíciós kulcs, sorkulcsot és tulajdonság nevét, és logikai érték beolvasása, amely jelzi, hogy a tulajdonság titkosítani kell-e. Titkosítás során az ügyféloldali kódtár döntse el, hogy a tulajdonság titkosítani kell-e az átvitel közbeni írása során fogja használni ezt az információt. A delegált is körül hogyan tulajdonságok vannak titkosítva logika lehetőségét biztosítja. (Például, ha X, majd titkosítása A tulajdonság; ellenkező esetben a Tulajdonságok A és b titkosítása) Vegye figyelembe, hogy nincs szükség arra, hogy ezek az információk olvasása vagy entitás lekérdezése közben.

### <a name="batch-operations"></a>Kötegelt műveletek
A kötegelt műveletek az azonos KEK is használni fogja kötegelt művelet sorait, mivel az ügyféloldali kódtár csak egy beállítások objektum (és ezáltal egy házirend vagy KEK) kötegelt művelet. Azonban az ügyféloldali kódtár belső létrehoz egy új véletlenszerű IV és soronkénti véletlenszerű CEK a kötegben. Felhasználók is beállíthatja a kötegben minden művelethez más tulajdonságokkal titkosítására Ez a viselkedés meghatározása a titkosítási feloldó.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Az entitások titkosítva vannak, mert titkosított szűrő lekérdezéseket nem futtatható.  Kísérli meg, ha eredmények lesz helytelen, mert a szolgáltatás volna az összehasonlítani kívánt titkosított adatok nem titkosított adatokat.
> 
>
Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs feloldó, amely képes feloldani az eredménykészletben a kulcsokat. Ha a lekérdezés eredményében található entitás nem oldható fel egy szolgáltatót, az ügyféloldali kódtár kivételhibát hiba. A lekérdezés, amely végrehajtja a kiszolgáló oldalán leképezések az ügyféloldali kódtár felveszi különleges titkosítási metaadat-tulajdonságainak (_ClientEncryptionMetadata1 és _ClientEncryptionMetadata2) alapértelmezés szerint a kijelölt oszlopokban.

## <a name="azure-key-vault"></a>Azure Key Vault
Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault használatával felhasználók titkosíthatják a kulcsokat és titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat. PFX-fájlok és a jelszavakat) hardveres biztonsági modulokkal (HSM) védett kulcsokkal. További információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md).

A storage ügyféloldali kódtára a Key Vault alap függvénytár használja ahhoz, hogy egy közös keretrendszer kulcsok kezelése az Azure biztosít. A felhasználók is kapnak további előnye, hogy a Key Vault bővítmények szalagtárat használ. A bővítmények kódtár egyszerű és zökkenőmentes Symmetric/RSA helyi és a fő szolgáltatók, valamint összesítő és a gyorsítótár hasznos funkciókat biztosít.

### <a name="interface-and-dependencies"></a>Felület és a függőségek
Nincsenek három Key Vault csomagok:  

* Azure-keyvault-core IKey és IKeyResolver tartalmazza. Nem rendelkezik függőségekkel rendelkező kis csomag is. A storage ügyféloldali kódtára a Javához készült határozza meg azt a függőség beállításához.
* Azure-keyvault a Key Vault REST ügyfél tartalmazza.  
* Azure-keyvault-extensions bővítmény kódot tartalmaz, amely tartalmazza többek között a titkosítási algoritmusok és egy RSAKey és egy SymmetricKey. A Core és a KeyVault névterek függ, és adható meg az összesített feloldó (amikor a felhasználók szolgáltatókat szeretnének használni több kulcs) és egy kulcs gyorsítótárazása funkciókat biztosítja. Bár a storage ügyféloldali kódtár közvetlenül függ a csomag, ha a felhasználók szeretné használni az Azure Key Vault, a kulcsok tárolására, vagy a helyi felhasználását és a kriptográfiai szolgáltatók felhőalapú a Key Vault bővítmények használatát, hogy meg kell ezt a csomagot.  
  
  Key Vault végzi a nagy értékű főkulcsok, és a Key Vault / szabályozó korlátozások figyelembe ennek kialakítása. A Key Vault ügyféloldali titkosítás elvégzésekor modellt hoz használja szimmetrikus master Key Vault a titkos kulcsok tárolása és gyorsítótárazott helyileg. Felhasználók a következőket kell tennie:  

1. Kapcsolat nélküli titkos kulcs létrehozása, és töltse fel a Key Vault.  
2. Paraméterként a titkos kulcsot base azonosító használatával javítsa ki a titkosításhoz a titkos kulcs jelenlegi verziója, és ezek az információk helyileg gyorsítótárazzák. Használja a CachingKeyResolver gyorsítótárazás; felhasználók-azonosítóknak nem megvalósításához a saját logikát gyorsítótárazását.  
3. A gyorsítótárazása használja bemeneti adatokként a titkosítási házirend létrehozása közben.
   A titkosító mintakódok Key Vault használatának kapcsolatos további információk is található. <fix URL>  

## <a name="best-practices"></a>Ajánlott eljárások
Titkosítás támogatása csak a storage ügyféloldali kódtára a Javához készült érhető el.

> [!IMPORTANT]
> Vegye figyelembe a fontos pontok ügyféloldali titkosítás használata esetén:
> 
> * Ha egy titkosított blob írásakor vagy olvasásakor, használja a teljes blob feltöltése és tartomány/egész blob letöltési parancsok. Egy titkosított blobba protokoll műveletek, például a Put blokk, Put tiltólista, írási lapok, törölje a jelet lapok vagy hozzáfűzése blokk; használatával elkerülése érdekében Ellenkező esetben előfordulhat, hogy a titkosított blob sérült, és nem olvasható teszi.
> * A táblákhoz hasonló korlátozás létezik. Ügyeljen rá, nem titkosított tulajdonságainak módosítása a paramétertitkosítási metaadatokat frissítése nélkül.  
> * Ha a titkosított blob metaadatai állít be, felülírhatja a szükséges jogokkal, mivel a metaadatok beállítása nem additívak titkosítással kapcsolatos metaadatok. Ez akkor is igaz, a pillanatképek; Ne adjon meg a metaadat egy titkosított blob pillanatképének létrehozása során. Ha metaadatokat be kell állítani, ügyeljen arra, hogy hívja a **downloadAttributes** módszert, először az aktuális titkosítási metaadatot beszerezni, és közben a metaadatok beállítása egyidejű írási műveletek megakadályozásához.  
> * Engedélyezze a **requireEncryption** jelzőt az alapértelmezett beállításokat a felhasználók számára, amely csak a titkosított adatok együtt kell működnie. További információkért lásd az alábbi.  
> 
> 

## <a name="client-api--interface"></a>Ügyfél API-ja / felület
Egy EncryptionPolicy objektum létrehozása során a felhasználók megadhatják csak kulcsot (végrehajtási IKey), csak egy feloldó (végrehajtási IKeyResolver), vagy mindkettőt. IKey, amely azonosítja a kulcsazonosító, és a logikai biztosít, amely alkalmazásburkoló/kicsomagolásával alapvető kulcs típusa. IKeyResolver feloldásához egy kulcsot a visszafejtés során használt. Meghatározza a ResolveKey módszere, amely egy megadott kulcsazonosítójával IKey adja vissza. Ez biztosítja a felhasználók több helyen felügyelt több kulcsok közötti részére.

* A titkosításhoz használja a rendszer mindig, és egy kulcs hiányában hibát eredményez.  
* A visszafejtéshez:  
  
  * A kulcs feloldó meghívták, ha meg van adva a kulcs beszerzése. A feloldó van megadva, de nem rendelkezik a kulcsazonosító társítás, ha hiba fordul elő.  
  * Ha nincs megadva a feloldó, de egy kulcs van megadva, a kulcs akkor használatos, ha az azonosítója a szükséges kulcs azonosítója megegyezik-e. Az azonosító nem egyezik, ha hiba fordul elő.  
    
    A [titkosítási minták](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>mutassa be a részletes végpont forgatókönyv BLOB, üzenetsorok és táblák, jelszavat Key Vault-integráció.

### <a name="requireencryption-mode"></a>RequireEncryption mód
Felhasználók engedélyezheti üzemmódot ahol feltöltések és a letöltött fájl titkosítva kell lennie. Ebben a módban a megpróbálja feltölteni az adatokat egy titkosítási házirend nélkül, vagy a szolgáltatás a nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A **requireEncryption** a kérelem beállítások objektum jelzőt a viselkedését szabályozza. Ha az alkalmazás titkosítja az Azure Storage-ban tárolt összes objektumot, majd beállíthatja a **requireEncryption** az alapértelmezett beállításokat a szolgáltatás ügyfél objektum tulajdonságát.   

Tegyük fel például, **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** kényszeríteni a titkosítást, az összes blob-ügyfél objektum keresztül végrehajtott műveleteket.

### <a name="blob-service-encryption"></a>BLOB szolgáltatás titkosítási
Hozzon létre egy **BlobEncryptionPolicy** objektumot, majd állítsa be a kérelem beállításai (API vagy egy ügyfél szinten használatával **DefaultRequestOptions**). Minden más kezelik az ügyféloldali kódtár által belsőleg.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```

### <a name="queue-service-encryption"></a>Várólista titkosítását
Hozzon létre egy **QueueEncryptionPolicy** objektumot, majd állítsa be a kérelem beállításai (API vagy egy ügyfél szinten használatával **DefaultRequestOptions**). Minden más kezelik az ügyféloldali kódtár által belsőleg.

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```

### <a name="table-service-encryption"></a>TABLE szolgáltatás titkosítási
Egy titkosítási házirend létrehozása és beállítása a lehetőségek a kívül meg kell adnia egy **EncryptionResolver** a **TableRequestOptions**, vagy állítsa a [titkosítása] attribútumot a entitás elérő és beállító.

### <a name="using-the-resolver"></a>A feloldó használata

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```

### <a name="using-attributes"></a>Attribútumok használata
Említetteknek megfelelően, ha az entitás TableEntity valósítja meg, majd a Tulajdonságok elérő és beállító is látható el a [titkosítása] attribútummal megadása helyett a **EncryptionResolver**.

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a tároló eredményezi további teljesítményigény titkosítása. A tartalomkulcs és IV kell létrejönnie, a tartalom titkosítva kell lennie és további metaadatok kell kell formázva és fel kell tölteni. Ez a terhelés a titkosított adatok mennyisége függvényében. Azt javasoljuk, hogy az ügyfelek mindig tesztelje az alkalmazások fejlesztése során teljesítmény.

## <a name="next-steps"></a>További lépések
* Töltse le a [Azure Storage ügyféloldali kódtára a Java-Maven-csomag](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Töltse le a [Azure Storage ügyféloldali kódtára a Java-forráskód a Githubról](https://github.com/Azure/azure-storage-java)   
* Töltse le az Azure Key Vault Maven kódtár Java-Maven-csomagok:
  * [Alapvető](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) csomag
  * [Ügyfél](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) csomag
* Látogasson el a [az Azure Key Vault dokumentáció](../../key-vault/key-vault-whatis.md)
