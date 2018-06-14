---
title: A Microsoft Azure Storage Python ügyféloldali titkosítás |} Microsoft Docs
description: Az Azure Storage ügyféloldali kódtára a Pythonhoz ügyféloldali titkosítás támogatja az Azure Storage-alkalmazások a maximális biztonság érdekében.
services: storage
documentationcenter: python
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: f9bf7981-9948-4f83-8931-b15679a09b8a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/11/2017
ms.author: lakasa
ms.openlocfilehash: c925b41d1654bd5c9b40438c4b6b9f402ec4bac2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742642"
---
# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>A Microsoft Azure Storage Python ügyféloldali titkosítás
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
A [Azure Storage ügyféloldali kódtára a Pythonhoz](https://pypi.python.org/pypi/azure-storage) támogatja a titkosított adatok ügyfélalkalmazásokon belüli feltöltése az Azure Storage, és az adatok visszafejtése során az ügyfél letöltése előtt.

> [!NOTE]
> Az Azure Storage Python kódtár előzetes verzió van.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítás és visszafejtés keresztül a boríték módszer
A titkosítási és visszafejtési folyamatok kövesse a boríték módszer.

### <a name="encryption-via-the-envelope-technique"></a>A boríték technika keresztül titkosítás
A boríték technika keresztül titkosítás az alábbi módon működik:

1. Az Azure storage ügyféloldali kódtár állít elő, a tartalom titkosítási kulcs (CEK), amely a szimmetrikus kulcs egy egyszeri használata.
2. Felhasználói adatok titkosítása a CEK használatával.
3. A CEK ezt követően (titkosított) (KEK-) kulcs titkosítási kulcs használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, amely helyileg kezelt lehet.
   A storage ügyféloldali kódtár maga soha nem KEK elérésére. A könyvtár a kulcs alkalmazásburkoló algoritmus a KEK által biztosított hív meg. Felhasználók maguk dönthetik használandó egyéni szolgáltatók kulcs alkalmazásburkoló/kicsomagolásával Ha szükséges.
4. A titkosított adatok majd feltöltése az Azure Storage szolgáltatásba. A becsomagolt kulcs néhány további titkosítási metaadatokból együtt tárolt metaadatok (a blob) vagy köztes értékkel, a titkosított adatok (üzenetsor-üzeneteket és táblaentitásokat).

### <a name="decryption-via-the-envelope-technique"></a>A boríték technika keresztül visszafejtés
A boríték technika keresztül visszafejtési az alábbi módon működik:

1. Az ügyféloldali kódtár feltételezi, hogy a felhasználó kezeli a fő titkosítási kulcs-(KEK) helyileg. A felhasználónak nem kell tudni, hogy a titkosításhoz használt kulcs. Ehelyett egy kulcs feloldó, amely különböző kulcs azonosítók kulcsok, beállítása is, és használja.
2. Az ügyféloldali kódtár letölti a titkosított adatokat a szolgáltatás tárolt titkosítási anyagok együtt.
3. A burkolt tartalom titkosítási kulcs (CEK) nem burkolatlan (visszafejtett) használatával a fő titkosítási kulcsát (KEK). Itt ebben az esetben az ügyféloldali kódtár nem rendelkezik hozzáféréssel a KEK. Egyszerűen meghívja az egyéni szolgáltató kibontásához algoritmus.
4. A tartalom titkosítási kulcs (CEK) szolgál majd visszafejteni a titkosított adatokat.

## <a name="encryption-mechanism"></a>Titkosítási módszer
A storage ügyféloldali kódtár által használt [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) ahhoz, hogy a felhasználói adatok titkosítására. Pontosabban [Cipher Block Chaining (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) AES módban. Minden szolgáltatás működik némileg eltér, azok itt ismertetik.

### <a name="blobs"></a>Blobok
Az ügyféloldali kódtár jelenleg csak a teljes blobok titkosítása. Titkosítás támogatott kifejezetten, amikor a felhasználó használja a **létrehozása*** módszerek. A letöltések, mindkettő teljes és a tartomány letöltések támogatottak, és párhuzamos folyamatkezelést biztosítja a feltöltés és letöltés érhető el.

Titkosítás során az ügyféloldali kódtár létre egy véletlenszerű inicializálási vektor (IV) 16 bájt, 32 bájt véletlenszerű tartalom titkosítási kulcs (CEK) együtt, és hajtsa végre a boríték ezen információk alapján blob adatok titkosítását. A burkolt CEK és néhány további titkosítási metaadatok tárolása ezután, a blob-metaadatok a titkosított blob szolgáltatás együtt.

> [!WARNING]
> Ha szerkesztése vagy a saját metaadatok a BLOB feltöltése, győződjön meg arról, hogy megőrzi a metaadatok szeretné. A metaadatok nélkül új metaadatokkal tölt fel, ha a becsomagolt CEK, IV és egyéb metaadatokat elvész, és a blob tartalma soha nem lesznek lekérhető újra.
> 
> 

Egy titkosított blob letöltése magában foglalja a teljes blob használatával a tartalom lekérése a **beolvasása*** kényelmi módszerek. A burkolt CEK kicsomagolják, és ha a felhasználók számára a visszafejtett adatokat a IV (tárolt blob metaadatai ebben az esetben) együtt használja.

Egy tetszőleges tartomány letöltése (**beolvasása*** átadott tartomány paraméterekkel módszerek) a titkosított BLOB magában foglalja a kis mennyiségű sikeresen visszafejtése a kért tartomány használható további adatokat kíván gyűjteni a felhasználók által biztosított tartományon hangolását.

Blokk blobokat és lapblobokat csak lehet titkosított/visszafejteni a rendszer. Jelenleg nem támogatja a titkosított hozzáfűző blobokat.

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
3. A burkolt CEK és néhány további titkosítási metaadatok tárolása ezután két további fenntartott tulajdonságként. Az első fenntartott tulajdonság (\_ClientEncryptionMetadata1), amely tartalmazza a IV, a verziót és a becsomagolt kulcs karakterlánc tulajdonság. A második fenntartott tulajdonság (\_ClientEncryptionMetadata2) egy bináris tulajdonság, amely tartalmazza a titkosított tulajdonságok. A második tulajdonságban tárolt adatok (\_ClientEncryptionMetadata2) maga is titkosítva.
4. Ezek fenntartott szükség tovább tulajdonságokra a titkosításhoz, mert felhasználók most már rendelkezik 252 helyett csak 250 egyéni tulajdonságokat. Az entitás teljes mérete 1MB-nál kevesebb kell lennie.
   
   Vegye figyelembe, hogy a tulajdonságok csak string titkosíthatók. Ha más típusú tulajdonságok titkosítani kell, azok karakterláncok kell konvertálni. A titkosított karakterláncok tárolja a szolgáltatás bináris tulajdonságként, és telepítésekké lesznek átalakítva vissza karakterláncok (nyers karakterláncok, nem típusú EdmType.STRING EntityProperties) a visszafejtés után.
   
   Táblák, a titkosítási házirenden kívül a felhasználók fiók kell adnia a titkosítani kell. Ez a végezhető el a típus értéke EdmType.STRING TableEntity objektumok vagy tárolja ezeket a tulajdonságokat, és igaz értékre kell állítani, vagy a encryption_resolver_function beállítása a tableservice objektum titkosítására. Egy titkosítási feloldó, akkor a függvény egy partíciókulcsot, sorkulcsot és tulajdonságnév veszi, és logikai érték beolvasása, amely jelzi, hogy a tulajdonság titkosítani kell-e. Titkosítás során az ügyféloldali kódtár döntse el, hogy a tulajdonság titkosítani kell-e az átvitel közbeni írása során fogja használni ezt az információt. A delegált is körül hogyan tulajdonságok vannak titkosítva logika lehetőségét biztosítja. (Például, ha X, majd titkosítása A tulajdonság; ellenkező esetben a Tulajdonságok A és b titkosítása) Vegye figyelembe, hogy nincs szükség arra, hogy ezek az információk olvasása vagy entitás lekérdezése közben.

### <a name="batch-operations"></a>Kötegelt műveletek
Egy titkosítási házirendek vonatkoznak a kötegben összes sort. Az ügyféloldali kódtár belső létrehoz egy új véletlenszerű IV és a véletlenszerű CEK soronkénti a kötegben. Felhasználók is beállíthatja a kötegben minden művelethez más tulajdonságokkal titkosítására Ez a viselkedés meghatározása a titkosítási feloldó.
Ha egy kötegelt környezetben vezető hozza létre a tableservice batch() metódussal, a tableservice titkosítási házirend automatikusan alkalmazandó a kötegben. Egy kötegelt explicit módon a konstruktor meghívásával jön létre, ha a titkosítási házirend kell paraméterként átadja és a köteg teljes balra változtatás nélkül.
Vegye figyelembe, hogy entitások titkosítva legyenek-e, mint a batch a kötegelt titkosítási házirenddel (entitások nincs titkosítva a tableservice titkosítási házirenddel kötegelt véglegesítése időpontjában) való beszúrása.

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Az entitások titkosítva vannak, mert titkosított szűrő lekérdezéseket nem futtatható.  Kísérli meg, ha eredmények lesz helytelen, mert a szolgáltatás volna az összehasonlítani kívánt titkosított adatok nem titkosított adatokat.
> 
>
Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcs feloldó, amely képes feloldani az eredménykészletben a kulcsokat. Ha a lekérdezés eredményében található entitás nem oldható fel egy szolgáltatót, az ügyféloldali kódtár kivételhibát hiba. A lekérdezés, amely végrehajtja a kiszolgáló oldalán leképezések, az ügyféloldali kódtár felveszi különleges titkosítási metaadat-tulajdonságainak (\_ClientEncryptionMetadata1 és \_ClientEncryptionMetadata2) a kijelölt oszlopok alapértelmezés szerint.

> [!IMPORTANT]
> Vegye figyelembe a fontos pontok ügyféloldali titkosítás használata esetén:
> 
> * Ha egy titkosított blob írásakor vagy olvasásakor, használja a teljes blob feltöltése és tartomány/egész blob letöltési parancsok. Egy titkosított blobba protokoll műveletek, például a Put blokk, Put tiltólista, lapok írási vagy törölje a jelet lapok; használatával elkerülése érdekében Ellenkező esetben előfordulhat, hogy a titkosított blob sérült, és nem olvasható teszi.
> * A táblákhoz hasonló korlátozás létezik. Ügyeljen rá, nem titkosított tulajdonságainak módosítása a paramétertitkosítási metaadatokat frissítése nélkül.
> * Ha a titkosított blob metaadatai állít be, felülírhatja a szükséges jogokkal, mivel a metaadatok beállítása nem additívak titkosítással kapcsolatos metaadatok. Ez akkor is igaz, a pillanatképek; Ne adjon meg a metaadat egy titkosított blob pillanatképének létrehozása során. Ha metaadatokat be kell állítani, ügyeljen arra, hogy hívja a **get_blob_metadata** módszert, először az aktuális titkosítási metaadatot beszerezni, és közben a metaadatok beállítása egyidejű írási műveletek megakadályozásához.
> * Engedélyezze a **require_encryption** jelzőt a service objektum a felhasználók számára, amely csak a titkosított adatok együtt kell működnie. További információkért lásd az alábbi.
> 
> 

A storage ügyféloldali kódtár vár a megadott KEK és a kulcs feloldó valósítja meg a következő illesztőfelületet. [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/) támogatja a Python KEK felügyeleti függőben, és integrálni kell a befejezésekor a tárba.

## <a name="client-api--interface"></a>Ügyfél API-ja / felület
Tárolási szolgáltatás objektum (azaz blockblobservice) létrehozását követően a felhasználó lehet értéket hozzárendelni a mezőket, egy titkosítási házirend alkotó: key_encryption_key, key_resolver_function, és require_encryption. KEK csak a felhasználók megadhatják csak egy feloldó, vagy mindkettőt. key_encryption_key, amely azonosítja a kulcsazonosító, és a logikai biztosít, amely alkalmazásburkoló/kicsomagolásával alapvető kulcs típusa. key_resolver_function feloldásához egy kulcsot a visszafejtés során használt. A megadott kulcs azonosítója érvényes KEK adja vissza. Ez biztosítja a felhasználók több helyen felügyelt több kulcsok közötti részére.

A KEK meg kell valósítania az alábbi módszerek sikeresen titkosítja az adatokat:

* wrap_key(cek): a megadott CEK (bájt), az a felhasználó által választott algoritmust használó burkolja. A becsomagolt kulcs adja vissza.
* get_key_wrap_algorithm(): kulcsok burkolása használt algoritmust adja vissza.
* get_kid(): a karakterlánc-azonosító a KEK adja vissza.
  A KEK meg kell valósítania az alábbi módszerek sikerült visszafejteni az adatokat:
* unwrap_key (cek, algoritmus): a megadott karakterlánc megadott algoritmussal CEK burkolatlan formájában adja vissza.
* get_kid(): egy karakterlánc-azonosítója a KEK adja vissza.

A kulcs feloldó legalább meg kell valósítania egy metódust,, hogy adott kulcsazonosítója, a fenti felületet megvalósító megfelelő KEK adja vissza. Ez a metódus csak a szolgáltatás objektum key_resolver_function tulajdonság társítva van.

* A titkosításhoz használja a rendszer mindig, és egy kulcs hiányában hibát eredményez.
* A visszafejtéshez:
  
  * A kulcs feloldó meghívták, ha meg van adva a kulcs beszerzése. A feloldó van megadva, de nem rendelkezik a kulcsazonosító társítás, ha hiba fordul elő.
  * Ha nincs megadva a feloldó, de egy kulcs van megadva, a kulcs akkor használatos, ha az azonosítója a szükséges kulcs azonosítója megegyezik-e. Az azonosító nem egyezik, ha hiba fordul elő.
    
    A titkosítási minták azure.storage.samples <fix URL>egy BLOB, üzenetsorok és táblák részletesebb végpont forgatókönyv bemutatásához.
      A KEK és a kulcs feloldó minta implementációja szerepelnek a mintafájlok KeyWrapper és KeyResolver kulcsattribútumokkal.

### <a name="requireencryption-mode"></a>RequireEncryption mód
Felhasználók engedélyezheti üzemmódot ahol feltöltések és a letöltött fájl titkosítva kell lennie. Ebben a módban a megpróbálja feltölteni az adatokat egy titkosítási házirend nélkül, vagy a szolgáltatás a nem titkosított adatok letöltése sikertelen lesz az ügyfélen. A **require_encryption** jelzőt a service objektum a viselkedését szabályozza.

### <a name="blob-service-encryption"></a>Blob service encryption
Állítsa be a titkosítás házirendmezők blockblobservice objektumon. Minden más kezelik az ügyféloldali kódtár által belsőleg.

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

### <a name="queue-service-encryption"></a>Queue service encryption
Állítsa be a titkosítás házirendmezők queueservice objektumon. Minden más kezelik az ügyféloldali kódtár által belsőleg.

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

### <a name="table-service-encryption"></a>TABLE szolgáltatás titkosítási
Egy titkosítási házirend létrehozása és beállítása a lehetőségek a kívül meg kell adnia egy **encryption_resolver_function** a a **tableservice**, vagy állítsa az titkosítása attribútumot a EntityProperty.

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
Fent említett tulajdonság jelölhető titkosításhoz EntityProperty objektum tárolja, és a titkosítás mező.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a tároló eredményezi további teljesítményigény titkosítása. A tartalomkulcs és IV kell létrejönnie, a tartalom titkosítva kell lennie és további metaadatokat kell kell formázva és fel kell tölteni. Ez a terhelés a titkosított adatok mennyisége függvényében. Azt javasoljuk, hogy az ügyfelek mindig tesztelje az alkalmazások fejlesztése során teljesítmény.

## <a name="next-steps"></a>További lépések
* Töltse le a [Azure Storage ügyféloldali kódtára a Java PyPi csomag](https://pypi.python.org/pypi/azure-storage)
* Töltse le a [az Azure Storage ügyféloldali kódtára a Pythonhoz forráskód a Githubról](https://github.com/Azure/azure-storage-python)
