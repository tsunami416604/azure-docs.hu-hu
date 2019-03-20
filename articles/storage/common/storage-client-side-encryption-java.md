---
title: A Microsoft Azure Storage Java-Client-Side titkosítási |} A Microsoft Docs
description: Az Azure Storage ügyféloldali kódtára a Javához készült ügyféloldali titkosítás és az Azure Key Vault-integráció támogatja a maximális biztonság az Azure Storage-alkalmazásokhoz.
services: storage
author: lakasa
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: 0a2088e603828a7850cb250c1874008d63fe9c89
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992454"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>A Microsoft Azure Storage Java-Client-Side Encryption és az Azure Key Vaulttal
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Azure Storage ügyféloldali kódtára a Javához készült](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) támogatja a titkosított belüli előtt feltöltése az Azure Storage, az adatok visszafejtése során az ügyfélre történő letöltéskor. A kódtár emellett támogatja az integrációt [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiókkulcs-kezelés.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítási és visszafejtési a boríték technika keresztül
A folyamatok, titkosítási és visszafejtési kövesse a boríték technika.  

### <a name="encryption-via-the-envelope-technique"></a>Titkosítás a boríték technika keresztül
Titkosítás a boríték technika keresztül működik, a következő módon:  

1. Az Azure storage ügyféloldali kódtára a tartalom titkosítási kulcsot (CEK), amely egyszer használható egy szimmetrikus kulcs állít elő.  
2. Felhasználói adatok titkosítását a rendszer a CEK.   
3. A CEK ezt követően (titkosítva) a kulcstitkosítási kulcs-(KEK) használatával. A KEK kulcsazonosítójával által azonosított és aszimmetrikus kulcspárt alkotnak, vagy egy szimmetrikus kulcsot és is kezelhetők helyileg vagy az Azure Key vault-Kulcstartók tárolva.  
   A storage ügyféloldali kódtára maga soha nem KEK hozzáféréssel rendelkezik. A könyvtár a Key Vault által biztosított kulcs alkalmazásburkoló algoritmus hív meg. Felhasználók választhatnak használandó egyéni szolgáltatók kulcs alkalmazásburkoló/kicsomagolási Ha szükséges.  
4. A titkosított adatok majd fel van töltve az Azure Storage szolgáltatásba. A burkolt kulcs néhány további titkosítási metaadatok együtt tárolt metaadatok (a blob) és vagy interpolált a titkosított adatok (üzenetsorbeli üzenetek és táblaentitások).

### <a name="decryption-via-the-envelope-technique"></a>A boríték technika keresztül visszafejtés
Visszafejtési keresztül a boríték módszer a következő módon működik:  

1. Az ügyféloldali kódtár azt feltételezi, hogy a felhasználó kezeli a kulcstitkosítási kulcs-(KEK) helyileg vagy az Azure Key vault-Kulcstartók. A felhasználónak nem kell tudni, hogy az adott a titkosításhoz használt kulcs. Ehelyett egy fő feloldó, amelyeket különböző kulcsok azonosítók feloldása egy olyan kulcsok beállítása is, és használja.  
2. Az ügyféloldali kódtár a titkosított adatokat a szolgáltatásban tárolt titkosítási anyagok együtt letölti.  
3. A burkolt tartalom titkosítási kulcs (CEK) nem burkolatlan (visszafejtett) használatával a kulcstitkosítási kulcs-(KEK). Itt, az ügyféloldali kódtár nincs KEK való hozzáférést. Egyszerűen meghívja az egyéni vagy kulcstároló-szolgáltató kicsomagolási algoritmus.  
4. A tartalom titkosítási kulcs (CEK) szolgál majd visszafejteni a titkosított adatokat.

## <a name="encryption-mechanism"></a>Titkosítási mechanizmus
A storage ügyféloldali kódtára használ [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) annak érdekében, hogy a felhasználói adatok titkosításához. Pontosabban a [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) AES mód. Minden egyes service működésének némileg eltér, így azok itt ismertetjük.

### <a name="blobs"></a>Blobok
Az ügyféloldali kódtár jelenleg csak a teljes blobok titkosítását. Titkosítás támogatott, ha a felhasználók használhatják a **feltöltése*** metódusokat vagy a **openOutputStream** metódus. A letöltések, egyaránt teljes és a tartomány letöltések támogatottak.  

Titkosítás során az ügyféloldali kódtár hozzon létre egy "véletlenszerű inicializálási vektor (IV) 16 bájtos, és a egy véletlenszerű tartalom titkosítási kulcs (CEK) 32 bájt, és elvégezni a blob típusú adatok ezen információk alapján boríték-titkosítást. A burkolt CEK és néhány további titkosítási metaadatok majd tárolódnak, mivel a szolgáltatás a titkosított BLOB metaadatait a blob.

> [!WARNING]
> Ha szerkesztési vagy feltöltése a blob metaadatait a saját, győződjön meg arról, hogy a rendszer megőrzi ezeket a metaadatokat szeretne. Ha új metaadatokkal a metaadatok nélkül tölt fel, a burkolt CEK, IV és egyéb metaadatok elvesznek, és a blob-tartalmak soha nem lesznek lekérhető újra.
> 
> 

Egy titkosított blob letöltése magában foglalja a használatával a teljes blob tartalmának beolvasása a **letöltése**/**openInputStream** egyszerűsített metódusok. A burkolt CEK kicsomagolják, és együtt a IV (tárolt blob metaadatai, ebben az esetben) segítségével a felhasználók számára a visszafejtett adatokat adja vissza.

Egy tetszőleges címtartományt letöltése (**downloadRange** módszerek) a titkosított BLOB magában foglalja a kisebb mennyiségű használható sikerült visszafejteni a kért további adatok lekérése érdekében a felhasználók által biztosított tartomány beállítása tartomány.  

Minden blob-típusok (blokkblobok, lapblobok és hozzáfűző blobok) is titkosítva/fejthetők vissza a séma használatával.

### <a name="queues"></a>Üzenetsorok
Üzenetsorbeli üzenetek esetén minden olyan a formátuma lehet, mivel az ügyféloldali kódtár, amely tartalmazza az inicializálási vektor (IV) és a titkosított tartalom titkosítási kulcs (CEK) az üzenet szövege az egyéni formátum határozza meg.  

Titkosítás során az ügyféloldali kódtár egy véletlenszerű IV 16 bájtos együtt egy véletlenszerű CEK 32 bájtot állít elő, és az üzenetsor üzenetszöveg ezen információk alapján boríték-titkosítást végzi. A burkolt CEK és néhány további titkosítási metaadatok ezután hozzáadódnak a titkosított üzenetsorban található üzenet. A szolgáltatás tárolja a módosított üzenet (lásd alább).

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

A burkolt kulcsa, visszafejtés közben az üzenetsorban található üzenet kinyert és kicsomagolják. A IV is az üzenetsorban található üzenet kinyert és az üzenetsor üzenetet adatainak visszafejtése a burkolatlan kulcs együtt használni. Vegye figyelembe, hogy a titkosítási metaadatok kis (alatt 500 bájt), így bár azt beleszámítanak üzenetsorbeli üzenet alapján a 64 KB-os korlátot, a hatás kezelhető kell lennie.

### <a name="tables"></a>Táblák
Az ügyféloldali kódtár támogatja a titkosítást az entitás tulajdonságai szúrhatók be, és cserélje le a műveleteket.

> [!NOTE]
> Egyesítési jelenleg nem támogatott. A tulajdonságok egy részének előfordulhat, hogy korábban használatával titkosított egy másik kulcsot, mert csak az új tulajdonságok egyesítése, és a metaadatok frissítése adatok elvesztését eredményezi. Az egyesítés vagy igényel, a már meglévő entitás olvasni a szolgáltatás további szolgáltatás-hívások, illetve tulajdonságonként egy új kulcsot használ, mindkettő nem alkalmasak a teljesítmény javítása érdekében.
> 
> 

Tábla adattitkosítás a következőképpen történik:  

1. Felhasználók titkosítását tulajdonságainak megadása.  
2. Az ügyféloldali kódtár hoz létre egy "véletlenszerű inicializálási vektor (IV) és a egy véletlenszerű tartalom titkosítási kulcs (CEK) a 32 bájtok minden entitás 16 bájtos, és a boríték-titkosítást végzi, az egyes tulajdonságok egy új IV tulajdonságonként származtatás titkosítására. A titkosított tulajdonságot a bináris adatok van tárolva.  
3. A burkolt CEK és néhány további titkosítási metaadatok majd két további fenntartott tulajdonságok formájában tárolja. Az első fenntartott (_ClientEncryptionMetadata1) tulajdonság, amely tartalmazza a IV, verzióját és burkolt kulcsot karakterlánc típusú tulajdonság. A második fenntartott (_ClientEncryptionMetadata2) tulajdonsága a bináris tulajdonságot, amely tartalmazza a titkosított tulajdonságait. A második (_ClientEncryptionMetadata2) tulajdonság található információk maga is titkosítva.  
4. Ezek a további fenntartott tulajdonságok a titkosításhoz szükséges, mert felhasználók most már rendelkezik 252 helyett csak 250 egyéni tulajdonságokat. Az entitás teljes méretének 1MB-nál kisebbnek kell lennie.  
   
   Vegye figyelembe, hogy csak karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságok titkosítását, azok karakterláncokat kell konvertálni. A titkosított karakterláncokat tárolja a szolgáltatás bináris tulajdonságokként, és azok alakítja vissza karakterláncok a visszafejtés után.
   
   Felhasználók táblákat, a titkosítási szabályzat mellett titkosítását a tulajdonságokat kell megadnia. Ezt megteheti megadásával vagy (POCO entitások, amelyek TableEntity származtatva) [titkosítása] attribútum vagy egy titkosítási feloldási lehetőségek. Egy titkosítási feloldó egy meghatalmazott, amelyek egy partíciókulcsot, egy sorkulcsot és egy tulajdonságnév vesz igénybe, és logikai érték beolvasása, amely azt jelzi, hogy tulajdonság titkosítani kell. Titkosítás során az ügyféloldali kódtár használatával ezek az információk döntse el, hogy tulajdonság titkosítani kell az átviteli írása közben. A delegált körül hogyan tulajdonságok vannak titkosítva logikai lehetőségét is biztosít. (Például, ha X, majd titkosítása egy tulajdonság; ellenkező esetben a tulajdonságok a és b titkosítása) Vegye figyelembe, hogy azt nem kell ezt az információt entitások lekérdezése vagy olvasása közben.

### <a name="batch-operations"></a>Kötegelt műveletek
A kötegelt műveletek az azonos KEK használható között, hogy a kötegelt műveletben szereplő összes sor, mivel az ügyféloldali kódtár csak egy objektum (és ezáltal egy házirend vagy KEK) kötegelt művelet. Azonban az ügyféloldali kódtár lesz belső hozzon létre egy új véletlenszerű IV és minden egyes sorára véletlenszerű CEK a batch szolgáltatásban. Felhasználók is beállíthatja ezt a viselkedést definiálása a titkosítási feloldó különböző tulajdonságai a batch-ben minden művelet titkosítására.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Az entitások titkosítva vannak, mivel a titkosított szűrő lekérdezéseket nem futtatható.  Ha meg, eredmények helytelen lesz, mert a szolgáltatás lenne összehasonlítani kívánt titkosított adatok nem titkosított adatok.
> 
> 
> Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs feloldó, amely tudja feloldani az eredményhalmaz összes kulcsot. Ha a lekérdezés eredménye található entitás nem tudja feloldani a szolgáltatóra, az ügyféloldali kódtár kivételt fogja kijelezni hiba. Minden olyan lekérdezéshez, amely végrehajtja a kiszolgáló oldalán leképezések az ügyféloldali kódtár adnak hozzá metaadat-tulajdonságot (_ClientEncryptionMetadata1 és _ClientEncryptionMetadata2) a speciális titkosítás alapértelmezés szerint a kijelölt oszlopokban.

## <a name="azure-key-vault"></a>Azure Key Vault
Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vaulttal, felhasználók titkosítsa a kulcsokat és titkos kulcsokat (például hitelesítési kulcsokat, a tárfiók kulcsait, az adattitkosítási kulcsokat. PFX-fájlok és jelszavak) hardveres biztonsági modulokban (HSM) által védett kulcsok használatával. További információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md).

A storage ügyféloldali kódtára a Key Vault alap függvénytár használ annak érdekében, hogy egy közös keretrendszer Azure-ban kulcsok kezeléséhez. Felhasználók is kaphat további előnye, hogy a Key Vault-bővítmények kódtár használatával. A bővítmények kódtár egyszerű és zökkenőmentes Symmetric/RSA helyi és a fő felhőszolgáltatók, valamint az Összesítés és gyorsítótárazás hasznos funkciókat biztosít.

### <a name="interface-and-dependencies"></a>Felület és a függőségek
Nincsenek három Key Vault-csomagot:  

* az Azure-keyvault-core Rendszerállapotkulcsot és IKeyResolver tartalmazza. Egy kis csomagot függőségek. A storage ügyféloldali kódtára a Javához készült az függőségként határozza meg.
* az Azure-keyvault a Key Vault REST-ügyfél tartalmazza.  
* Azure-keyvault-bővítményeket tartalmazza többek között az titkosítási algoritmusok és a egy RSAKey és a egy SymmetricKey bővítmény-kódot tartalmaz. A Core és a KeyVault névterek attól függ, és a egy összesített feloldó (Ha a felhasználó szeretné több-szolgáltató használata) és a egy fő gyorsítótárazása megadása funkciókat biztosít. A storage ügyféloldali kódtár nem közvetlenül függ a csomag, ha felhasználókat szeretne használni a az Azure Key Vault, a kulcsok tárolására, vagy a Key Vault-bővítmények használatával a felhasználását a helyi és a felhőbeli kriptográfiai szolgáltatókat, bár ez a csomag kell.  
  
  Key Vault célja az értékes főkulcsok, és a egy Key Vault szabályozási korlátok úgy tervezték, ezt szem előtt. Ügyféloldali titkosítás a Key Vault végrehajtásakor a modellt, hogy a Key Vault titkos kódként tárolt és a gyorsítótárba helyezték a szimmetrikus főkulcsok helyileg. Felhasználók a következőket kell tennie:  

1. Kapcsolat nélküli titkos kulcs létrehozása, és töltse fel a Key Vaultban.  
2. Paraméterként a titkos kulcsnak base azonosító használatával javítsa ki a titkosításhoz a titkos kulcs jelenlegi verziója, és helyileg ezt az információt gyorsítótárban. Gyorsítótárazás; CachingKeyResolver használata felhasználók nem várt megvalósítása a saját logikai gyorsítótárazás.  
3. A gyorsítótárazási feloldó használja bemenetként a titkosítási szabályzat létrehozása közben.
   A Key Vault használatára vonatkozó további információ a titkosítási Kódminták található. <fix URL>  

## <a name="best-practices"></a>Ajánlott eljárások
Titkosítás támogatása csak a storage ügyféloldali kódtára a Javához készült érhető el.

> [!IMPORTANT]
> Vegye figyelembe az alábbi fontos szempontokat ügyféloldali titkosítás használata esetén:
> 
> * Ha titkosított blobba írásakor vagy olvasásakor, használja a teljes blob feltöltése és tartomány/egész blob letöltése parancsok. Protokoll műveletek, például a Blokkelraktározási, Put tiltólista, írási oldalak, egyértelmű oldalak vagy hozzáfűző letiltása; használatával titkosított blobba elkerülése érdekében Ellenkező esetben előfordulhat, hogy sérült a titkosított blobot, és győződjön meg arról, hogy nem olvasható.
> * A táblázatok egy hasonló megkötés létezik. Ügyeljen arra, hogy nem frissítése a titkosítási metaadatok nélkül a titkosított tulajdonságainak frissítése.  
> * A titkosított blob metaadatainak állít be, ha felülírhatja a titkosítással kapcsolatos metaadatok visszafejtési, mivel a metaadatok, nem additív szükséges. Ez akkor is igaz, a pillanatképek; Ne adjon meg a metaadat-titkosítású blob pillanatképének létrehozása közben. Hívja meg a metaadatok be lehet állítani, ha lehet a **downloadAttributes** módszert először az aktuális titkosítási metaadatok lekérése, és elkerülheti az egyidejű írások, metaadatok van beállítása közben.  
> * Engedélyezze a **requireEncryption** használható titkosított adatok csak a felhasználók számára, az alapértelmezett kérésbeállításokat jelölőjét. További információkért lásd az alábbi.  
> 
> 

## <a name="client-api--interface"></a>Ügyfél API-JÁNAK / csatoló
Egy EncryptionPolicy objektum létrehozása során a felhasználók megadhatják csak kulcsot (megvalósítása a Rendszerállapotkulcsot), csak egy (végrehajtási IKeyResolver) feloldó, vagy mindkettőt. Rendszerállapotkulcsot az alapszintű kulcs típusa, amely azonosítja a folyamat használatával egy kulcs azonosítója, és amely alkalmazásburkoló és-kicsomagolási biztosít a logikai. IKeyResolver a visszafejtés során egy kulcs feloldására szolgál. Azt határozza meg, amely visszaadja a megadott kulcsazonosítójával változón ResolveKey metódus. Ez lehetővé teszi a felhasználóknak több helyen felügyelt szolgáltatásban több kulcs választhat.

* A titkosításhoz használja a rendszer mindig, és a egy kulcs hiányában egy hibát eredményez.  
* A visszafejtéshez:  
  
  * A kulcs feloldó hív, ha meg van adva a kulcs beszerzése. Ha a feloldó van megadva, de nem rendelkezik a kulcs azonosítóját leképezése, egy hiba lépett fel.  
  * Ha nincs megadva a feloldó, de egy kulcs van megadva, a kulcs szolgál, ha annak azonosítója megegyezik a szükséges kulcs azonosítóját. Az azonosító nem egyezik, ha hiba fordul elő.  
    
    A [titkosítási minták](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>egy részletesebb végpontok közötti forgatókönyv a blobokhoz, üzenetsorokhoz és táblákhoz, valamint bemutatják a Key Vault-integráció.

### <a name="requireencryption-mode"></a>RequireEncryption mód
Felhasználók hol feltöltéseket és a letöltött fájl titkosítva kell lennie üzemmódot engedélyezheti. Ebben a módban a kísérletek töltse fel az adatok egy titkosítási szabályzat nélkül, vagy a szolgáltatás nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A **requireEncryption** jelzőt a kérelem beállítások objektum azt szabályozza, ezt a viselkedést. Ha az alkalmazás fogja titkosítani az Azure Storage szolgáltatásban tárolt összes objektum, majd beállíthatja a **requireEncryption** az alapértelmezett kérelem beállításokat a szolgáltatás ügyfél-objektum tulajdonságának.   

Például **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** kényszeríteni a titkosítást az összes blobműveletei ügyfél objektum használatával történik.

### <a name="blob-service-encryption"></a>BLOB service encryption szolgáltatással
Hozzon létre egy **BlobEncryptionPolicy** objektumra, és állítsa be a lehetőségek (API-t, vagy az ügyfél szinten **DefaultRequestOptions**). Minden más kezelik az ügyféloldali kódtár által belsőleg.

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

### <a name="queue-service-encryption"></a>Queue service encryption
Hozzon létre egy **QueueEncryptionPolicy** objektumra, és állítsa be a lehetőségek (API-t, vagy az ügyfél szinten **DefaultRequestOptions**). Minden más kezelik az ügyféloldali kódtár által belsőleg.

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

### <a name="table-service-encryption"></a>TABLE service encryption szolgáltatással
Mellett egy titkosítási szabályzat létrehozása, és a lehetőségek beállítása, vagy adjon meg egy **EncryptionResolver** a **TableRequestOptions**, vagy az [titkosítása] attribútum beállítása az entitás Getter a Setter se.

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
Ahogy említettük, ha az entitás TableEntity valósítja meg, majd a Tulajdonságok Getter a Setter se is rendelkeznie megadása helyett a [titkosítása] attribútummal az **EncryptionResolver**.

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
Vegye figyelembe, hogy a titkosított további teljesítménybeli terhelést okoz a tárolás eredményezi. A tartalomkulcs és IV kell létrejönnie, maga a tartalom titkosítva kell lennie, és további metaadatokkal kell formázni és feltöltött. Ez a terhelés hozzáadódik a titkosított adatok mennyisége függően változhat. Azt javasoljuk, hogy az ügyfelek mindig vizsgálati alkalmazásaikat a teljesítmény a fejlesztés során.

## <a name="next-steps"></a>További lépések
* Töltse le a [Azure Storage ügyféloldali kódtára a Java-Maven-csomag](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Töltse le a [az Azure Storage ügyféloldali kódtára a Java forráskódját a Githubról](https://github.com/Azure/azure-storage-java)   
* Töltse le az Azure Key Vault Maven Library for Java Maven-csomagokra:
  * [Core](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core) csomag
  * [Ügyfél](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault) csomag
* Látogasson el a [az Azure Key Vault-dokumentáció](../../key-vault/key-vault-whatis.md)
