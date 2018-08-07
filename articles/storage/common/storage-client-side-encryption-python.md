---
title: Ügyféloldali titkosítás a Microsoft Azure Storage-hoz a Pythonnal |} A Microsoft Docs
description: Az Azure Storage ügyféloldali kódtára a Pythonhoz készült ügyféloldali titkosítás támogatja a maximális biztonság az Azure Storage-alkalmazásokhoz.
services: storage
author: lakasa
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.component: common
ms.openlocfilehash: 6a6508393fe935b456cde815d35f2fd4447cd2d4
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528122"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>A Microsoft Azure Storage-hoz a Python ügyféloldali titkosítás
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Azure Storage ügyféloldali kódtára a Pythonhoz készült](https://pypi.python.org/pypi/azure-storage) támogatja a titkosított belüli előtt feltöltése az Azure Storage, az adatok visszafejtése során az ügyfélre történő letöltéskor.

> [!NOTE]
> Az Azure Storage Python-kódtár előzetes verzióban van.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítási és visszafejtési a boríték technika keresztül
A folyamatok, titkosítási és visszafejtési kövesse a boríték technika.

### <a name="encryption-via-the-envelope-technique"></a>Titkosítás a boríték technika keresztül
Titkosítás a boríték technika keresztül működik, a következő módon:

1. Az Azure storage ügyféloldali kódtára a tartalom titkosítási kulcsot (CEK), amely egyszer használható egy szimmetrikus kulcs állít elő.
2. Felhasználói adatok titkosítását a rendszer a CEK.
3. A CEK ezt követően (titkosítva) a kulcstitkosítási kulcs-(KEK) használatával. A KEK-kulcs azonosítója azonosíthatók, és aszimmetrikus kulcspárt alkotnak, vagy egy szimmetrikus kulcsot, amelyek felügyelete helyben is lehet.
   A storage ügyféloldali kódtára maga soha nem KEK hozzáféréssel rendelkezik. A könyvtár az, hogy a KEK által biztosított kulcs alkalmazásburkoló algoritmus hív meg. Felhasználók választhatnak használandó egyéni szolgáltatók kulcs alkalmazásburkoló/kicsomagolási Ha szükséges.
4. A titkosított adatok majd fel van töltve az Azure Storage szolgáltatásba. A burkolt kulcs néhány további titkosítási metaadatok együtt tárolt metaadatok (a blob) és vagy interpolált a titkosított adatok (üzenetsorbeli üzenetek és táblaentitások).

### <a name="decryption-via-the-envelope-technique"></a>A boríték technika keresztül visszafejtés
Visszafejtési keresztül a boríték módszer a következő módon működik:

1. Az ügyféloldali kódtár azt feltételezi, hogy a felhasználó kezeli a kulcstitkosítási kulcs-(KEK) helyileg. A felhasználónak nem kell tudni, hogy az adott a titkosításhoz használt kulcs. Ehelyett kulcs feloldó, és oldja fel a különböző kulcs azonosítók a kulcsokhoz, beállítása is, és használja.
2. Az ügyféloldali kódtár a titkosított adatokat a szolgáltatásban tárolt titkosítási anyagok együtt letölti.
3. A burkolt tartalom titkosítási kulcs (CEK) nem burkolatlan (visszafejtett) használatával a kulcstitkosítási kulcs-(KEK). Itt, az ügyféloldali kódtár nincs KEK való hozzáférést. Egyszerűen meghívja az egyéni szolgáltató kicsomagolási algoritmus.
4. A tartalom titkosítási kulcs (CEK) szolgál majd visszafejteni a titkosított adatokat.

## <a name="encryption-mechanism"></a>Titkosítási mechanizmus
A storage ügyféloldali kódtára használ [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) annak érdekében, hogy a felhasználói adatok titkosításához. Pontosabban a [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) AES mód. Minden egyes service működésének némileg eltér, így azok itt ismertetjük.

### <a name="blobs"></a>Blobok
Az ügyféloldali kódtár jelenleg csak a teljes blobok titkosítását. Titkosítás támogatott, ha a felhasználók használhatják a **létrehozása*** metódusokat. A letöltések, egyaránt teljes és a tartomány letöltések támogatottak, és a fel- és letöltést ezerszer érhető el.

Titkosítás során az ügyféloldali kódtár hozzon létre egy "véletlenszerű inicializálási vektor (IV) 16 bájtos, és a egy véletlenszerű tartalom titkosítási kulcs (CEK) 32 bájt, és elvégezni a blob típusú adatok ezen információk alapján boríték-titkosítást. A burkolt CEK és néhány további titkosítási metaadatok majd tárolódnak, mivel a szolgáltatás a titkosított BLOB metaadatait a blob.

> [!WARNING]
> Ha szerkesztési vagy feltöltése a blob metaadatait a saját, győződjön meg arról, hogy a rendszer megőrzi ezeket a metaadatokat szeretne. Ha új metaadatokkal a metaadatok nélkül tölt fel, a burkolt CEK, IV és egyéb metaadatok elvesznek, és a blob-tartalmak soha nem lesznek lekérhető újra.
> 
> 

Egy titkosított blob letöltése magában foglalja a használatával a teljes blob tartalmának beolvasása a **első*** egyszerűsített metódusok. A burkolt CEK kicsomagolják, és együtt a IV (tárolt blob metaadatai, ebben az esetben) segítségével a felhasználók számára a visszafejtett adatokat adja vissza.

Egy tetszőleges címtartományt letöltése (**első*** átadott módszerek tartomány paraméterekkel rendelkező) a titkosított BLOB magában foglalja a további adatokat, amelyek segítségével sikeresen kisebb mennyiségű lekérése érdekében a felhasználók által biztosított tartomány beállítása a kért tartomány visszafejteni.

Blokkblobok és lapblobok csak lehet titkosított vagy visszafejtett Ez a séma használatával. Nincs rendszer jelenleg nem támogatja a titkosított hozzáfűző blobokat.

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
3. A burkolt CEK és néhány további titkosítási metaadatok majd két további fenntartott tulajdonságok formájában tárolja. Az első fenntartott tulajdonság (\_ClientEncryptionMetadata1) egy karakterlánc-tulajdonság, amely tartalmazza a IV, verzióját és burkolt kulcs. A második fenntartott tulajdonság (\_ClientEncryptionMetadata2) egy bináris tulajdonság, amely tartalmazza a titkosított tulajdonságait. A második tulajdonságban tárolt adatok (\_ClientEncryptionMetadata2) maga is titkosítva.
4. Ezek a további fenntartott tulajdonságok a titkosításhoz szükséges, mert felhasználók most már rendelkezik 252 helyett csak 250 egyéni tulajdonságokat. Az entitás teljes méretének 1MB-nál kisebbnek kell lennie.
   
   Vegye figyelembe, hogy csak karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságok titkosítását, azok karakterláncokat kell konvertálni. A titkosított karakterláncokat tárolja a szolgáltatás bináris tulajdonságokként, és azok alakítja vissza karakterláncok (nyers karakterláncok, nem típusú EdmType.STRING EntityProperties) a visszafejtés után.
   
   Felhasználók táblákat, a titkosítási szabályzat mellett titkosítását a tulajdonságokat kell megadnia. A típus értéke EdmType.STRING TableEntity objektumok vagy tárolja ezeket a tulajdonságokat teheti meg, és a beállítása igaz értékre, vagy a encryption_resolver_function beállítása a tableservice objektum titkosítása. Egy titkosítási feloldó egy függvényt, amely egy partíciókulcsot, egy sorkulcsot és egy tulajdonságnév vesz igénybe, és logikai érték beolvasása, amely azt jelzi, hogy tulajdonság titkosítani kell. Titkosítás során az ügyféloldali kódtár használatával ezek az információk döntse el, hogy tulajdonság titkosítani kell az átviteli írása közben. A delegált körül hogyan tulajdonságok vannak titkosítva logikai lehetőségét is biztosít. (Például, ha X, majd titkosítása egy tulajdonság; ellenkező esetben a tulajdonságok a és b titkosítása) Vegye figyelembe, hogy azt nem kell ezt az információt entitások lekérdezése vagy olvasása közben.

### <a name="batch-operations"></a>Kötegelt műveletek
A kötegben szereplő összes sor egy titkosítási szabályzat vonatkozik. Az ügyféloldali kódtár lesz belső hozzon létre egy új véletlenszerű IV és minden egyes sorára véletlenszerű CEK a batch szolgáltatásban. Felhasználók is beállíthatja ezt a viselkedést definiálása a titkosítási feloldó különböző tulajdonságai a batch-ben minden művelet titkosítására.
Ha a batch környezeti vezető hozza létre a tableservice batch() módszerrel, a tableservice titkosítási szabályzat automatikusan alkalmazza a Batch. A batch explicit módon jön létre a konstruktort meghívásával, ha a titkosítási házirend kell paraméterként és balra kívánja módosítani a köteg teljes élettartama.
Vegye figyelembe, hogy entitásokat, a batch, a batch-titkosítási házirend (entitások nincs titkosítva a batch segítségével a tableservice titkosítási házirend véglegesítése idején) használatával való beszúrása titkosítva legyenek.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Az entitások titkosítva vannak, mivel a titkosított szűrő lekérdezéseket nem futtatható.  Ha meg, eredmények helytelen lesz, mert a szolgáltatás lenne összehasonlítani kívánt titkosított adatok nem titkosított adatok.
> 
>
Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs feloldó, amely tudja feloldani az eredményhalmaz összes kulcsot. Ha a lekérdezés eredménye található entitás nem tudja feloldani a szolgáltatóra, az ügyféloldali kódtár kivételt fogja kijelezni hiba. Minden olyan lekérdezéshez, amely végrehajtja a kiszolgáló oldalán leképezések, az ügyféloldali kódtár felveszi a Speciális titkosítási metaadat-tulajdonságot (\_ClientEncryptionMetadata1 és \_ClientEncryptionMetadata2) a kijelölt oszlopok alapértelmezés szerint.

> [!IMPORTANT]
> Vegye figyelembe az alábbi fontos szempontokat ügyféloldali titkosítás használata esetén:
> 
> * Ha titkosított blobba írásakor vagy olvasásakor, használja a teljes blob feltöltése és tartomány/egész blob letöltése parancsok. Protokoll műveletek, például a Blokkelraktározási, Put tiltólista, írási oldalak vagy egyértelmű oldalak; használatával titkosított blobba elkerülése érdekében Ellenkező esetben előfordulhat, hogy sérült a titkosított blobot, és győződjön meg arról, hogy nem olvasható.
> * A táblázatok egy hasonló megkötés létezik. Ügyeljen arra, hogy nem frissítése a titkosítási metaadatok nélkül a titkosított tulajdonságainak frissítése.
> * A titkosított blob metaadatainak állít be, ha felülírhatja a titkosítással kapcsolatos metaadatok visszafejtési, mivel a metaadatok, nem additív szükséges. Ez akkor is igaz, a pillanatképek; Ne adjon meg a metaadat-titkosítású blob pillanatképének létrehozása közben. Hívja meg a metaadatok be lehet állítani, ha lehet a **get_blob_metadata** módszert először az aktuális titkosítási metaadatok lekérése, és elkerülheti az egyidejű írások, metaadatok van beállítása közben.
> * Engedélyezze a **require_encryption** jelzőt, amely használható a titkosított adatok csak a felhasználók számára szolgáltatás objektumon. További információkért lásd az alábbi.
> 
> 

A storage ügyféloldali kódtára a megadott KEK és a kulcs feloldó, a következő felület megvalósítása vár. [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Python KEK felügyeleti függőben, és ebben a könyvtárban befejezése után integráljuk támogatása.

## <a name="client-api--interface"></a>Ügyfél API-JÁNAK / csatoló
(Azaz blockblobservice) tárolási szolgáltatás objektum létrehozása után a felhasználó lehet értéket rendelni egy titkosítási szabályzat alkotó mezőket: key_encryption_key, key_resolver_function, és require_encryption. Felhasználók megadhatják KEK csak csak egy feloldó, vagy mindkettőt. key_encryption_key alapszintű kulcs típusa, amely azonosítja a folyamat használatával egy kulcs azonosítója, és amely alkalmazásburkoló és-kicsomagolási biztosít a logikai. key_resolver_function a visszafejtés során egy kulcs feloldására szolgál. Egy adott kulcs azonosítója érvényes KEK adja vissza. Ez lehetővé teszi a felhasználóknak több helyen felügyelt szolgáltatásban több kulcs választhat.

A KEK meg kell valósítani az alábbi módszerek sikeresen megtörtént az adatok titkosítása:

* wrap_key(cek): a megadott CEK (bájt), a felhasználó által választott algoritmus segítségével burkolja. Burkolt kulcsát adja vissza.
* get_key_wrap_algorithm(): ad vissza a kulcsokat burkolása használt algoritmust határozza meg.
* get_kid(): a KEK adja vissza a karakterlánc-kulcs azonosítója.
  A KEK meg kell valósítani az alábbi módszerek sikerült visszafejteni az adatokat:
* unwrap_key (cek, algoritmus): a megadott CEK a karakterlánc algoritmus használatával burkolatlan formájában adja vissza.
* get_kid(): egy karakterlánc-kulcs azonosítója a KEK adja vissza.

A kulcs feloldó legalább meg kell valósítania egy adott, a kulcs azonosítója adja vissza a fenti felületet megvalósító megfelelő KEK metódust. Ez a módszer csak hozzá kell rendelni a service objektum key_resolver_function tulajdonsága van.

* A titkosításhoz használja a rendszer mindig, és a egy kulcs hiányában egy hibát eredményez.
* A visszafejtéshez:
  
  * A kulcs feloldó hív, ha meg van adva a kulcs beszerzése. Ha a feloldó van megadva, de nem rendelkezik a kulcs azonosítóját leképezése, egy hiba lépett fel.
  * Ha nincs megadva a feloldó, de egy kulcs van megadva, a kulcs szolgál, ha annak azonosítója megegyezik a szükséges kulcs azonosítóját. Az azonosító nem egyezik, ha hiba fordul elő.
    
    A titkosítási minták azure.storage.samples <fix URL>blobokhoz, üzenetsorokhoz és táblákhoz részletesebb végpontok közötti forgatókönyv bemutatásához.
      Minta megvalósítása a KEK és a kulcs feloldó vannak megadva a mintafájlok KeyWrapper és KeyResolver jelölik.

### <a name="requireencryption-mode"></a>RequireEncryption mód
Felhasználók hol feltöltéseket és a letöltött fájl titkosítva kell lennie üzemmódot engedélyezheti. Ebben a módban a kísérletek töltse fel az adatok egy titkosítási szabályzat nélkül, vagy a szolgáltatás nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A **require_encryption** jelzőt a service objektum a viselkedését szabályozza.

### <a name="blob-service-encryption"></a>BLOB service encryption szolgáltatással
A titkosítási szabályzat mezőket a blockblobservice objektu nastavit. Minden más kezelik az ügyféloldali kódtár által belsőleg.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Queue szolgáltatás titkosítási
A titkosítási szabályzat mezőket a queueservice objektu nastavit. Minden más kezelik az ügyféloldali kódtár által belsőleg.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>TABLE service encryption szolgáltatással
Mellett egy titkosítási szabályzat létrehozása, és a lehetőségek beállítása, vagy adjon meg egy **encryption_resolver_function** a a **tableservice**, vagy a titkosítás attribútum beállítása a EntityProperty.

### <a name="using-the-resolver"></a>A feloldó használata

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1') # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.
def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False

# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Attribútumok használata
Mint már említettük, egy tulajdonság lehet megjelölni titkosítási tárolásukról EntityProperty objektum, és a titkosítás mező.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a titkosított további teljesítménybeli terhelést okoz a tárolás eredményezi. A tartalomkulcs és IV kell létrejönnie, maga a tartalom titkosítva kell lennie, és további metaadatok legyen formázva és feltöltött. Ez a terhelés hozzáadódik a titkosított adatok mennyisége függően változhat. Azt javasoljuk, hogy az ügyfelek mindig vizsgálati alkalmazásaikat a teljesítmény a fejlesztés során.

## <a name="next-steps"></a>További lépések
* Töltse le a [Azure Storage ügyféloldali kódtára a Java PyPi csomag](https://pypi.python.org/pypi/azure-storage)
* Töltse le a [az Azure Storage ügyféloldali kódtára a Pythonhoz forráskódját a Githubról](https://github.com/Azure/azure-storage-python)
