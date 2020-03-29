---
title: Ügyféloldali titkosítás Pythonnal
titleSuffix: Azure Storage
description: Az Azure Storage-ügyfélkódtár python támogatja az ügyféloldali titkosítást az Azure Storage-alkalmazások maximális biztonsága érdekében.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 16e66cd762b86b27dc6703542ca7261b2300a33b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895370"
---
# <a name="client-side-encryption-with-python"></a>Ügyféloldali titkosítás Pythonnal

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Áttekintés
Az [Azure Storage-ügyfélkódtár python](https://pypi.python.org/pypi/azure-storage) támogatja az adatok titkosítását az ügyfélalkalmazásokon belül, mielőtt feltöltené az Azure Storage-ba, és visszafejti az adatokat az ügyfélre való letöltés közben.

> [!NOTE]
> Az Azure Storage Python-kódtár előzetes verzióban érhető el.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Titkosítás és visszafejtés a borítéktechnikával
A titkosítás és a visszafejtés folyamatai a borítéktechnikát követik.

### <a name="encryption-via-the-envelope-technique"></a>Titkosítás a borítéktechnikán keresztül
A borítéktechnikán keresztüli titkosítás a következőképpen működik:

1. Az Azure storage-ügyfélkódtár létrehoz egy tartalomtitkosítási kulcsot (CEK), amely egy egyszeri használatú szimmetrikus kulcs.
2. A felhasználói adatok titkosítása ezzel a CEK-vel történik.
3. A CEK ezután a kulcstitkosítási kulcs (KEK) használatával burkolódulatba (titkosít). A KEK-et egy kulcsazonosító azonosítja, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, amely helyileg van kezelve.
   Maga a tárolóügyfél-könyvtár soha nem fér hozzá a KEK-hez. A könyvtár meghívja a KEK által biztosított kulcstördelési algoritmust. A felhasználók egyéni szolgáltatókat használhatnak a kulcstördeléshez/kicsomagoláshoz, ha szükséges.
4. A titkosított adatok at majd feltölti az Azure Storage szolgáltatásba. A burkolt kulcs néhány további titkosítási metaadattal együtt vagy metaadatként (blobon) vagy interpolálva van a titkosított adatokkal (várólistaüzenetek és táblaentitások).

### <a name="decryption-via-the-envelope-technique"></a>Visszafejtés a borítéktechnikával
A borítékon keresztüli visszafejtés a következő módon működik:

1. Az ügyfélkódtár feltételezi, hogy a felhasználó helyileg kezeli a kulcstitkosítási kulcsot (KEK). A felhasználónak nem kell ismernie a titkosításhoz használt kulcsot. Ehelyett egy kulcsfeloldó, amely feloldja a különböző kulcsazonosítók kulcsok, lehet beállítani és használni.
2. Az ügyfélkódtár letölti a titkosított adatokat a szolgáltatásban tárolt titkosítási anyagokkal együtt.
3. A burkolt tartalomtitkosítási kulcs (CEK) ezután kicsomagolja (visszafejti) a kulcstitkosítási kulcs (KEK) használatával. Itt is az ügyféltár nem fér hozzá a KEK.Here again, the client library does not access to KEK. Egyszerűen meghívja az egyéni szolgáltató kicsomagolási algoritmusát.
4. A tartalomtitkosítási kulcs (CEK) ezután a titkosított felhasználói adatok visszafejtésére szolgál.

## <a name="encryption-mechanism"></a>Titkosítási mechanizmus
A tárolóügyfél-tár [az AES-t](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) használja a felhasználói adatok titkosításához. Pontosabban, [Cipher Block Chaining (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mód AES. Minden szolgáltatás működik kissé másképp, így meg beszéljük mindegyiket itt.

### <a name="blobs"></a>Blobok
Az ügyfélkódtár jelenleg csak a teljes blobok titkosítását támogatja. Pontosabban a titkosítás akkor támogatott, ha a felhasználók a **create*** metódusok használata. A letöltések esetében a teljes és a tartományletöltés is támogatott, és a feltöltés és a letöltés párhuzamosítása is elérhető.

A titkosítás során az ügyfélkódtár létrehoz egy véletlenszerű inicializálási vektort (IV) 16 bájtból, valamint egy 32 bájtos véletlenszerű tartalomtitkosítási kulcsot (CEK), és ezzel az információval borítéktitkosítást hajt végre a blobadatokon. A burkolt CEK és néhány további titkosítási metaadatok ezután blob metaadatokként tárolják a szolgáltatás titkosított blobjával együtt.

> [!WARNING]
> Ha saját metaadatait szerkeszti vagy tölti fel a blobhoz, gondoskodnia kell ametaadatok megőrzéséről. Ha új metaadatokat tölt fel a metaadatok nélkül, a burkolt CEK, IV és egyéb metaadatok elvesznek, és a blobtartalom soha többé nem lesz visszakereshető.
> 
> 

Egy titkosított blob letöltése magában foglalja a teljes blob tartalmának lekérésével a **get*** kényelmi módszerek használatával. A burkolt CEK kicsomagolva van, és a IV-el együtt (ebben az esetben blob metaadatként tárolva) együtt a visszafejtett adatok visszaküldése a felhasználóknak.

Egy tetszőleges tartomány letöltése **(get*** metódusok tartományparaméterekkel) a titkosított blobban magában foglalja a felhasználók által biztosított tartomány módosítását annak érdekében, hogy kis mennyiségű további adatot kapjon, amelyek felhasználhatók a kért tartomány sikeres visszafejtéséhez.

A blokkblobok és a lapblobok csak ezzel a sémával titkosíthatók/visszafejthetők. Jelenleg nem támogatja a hozzáfűző blobok titkosítását.

### <a name="queues"></a>Üzenetsorok
Mivel a várólistaüzenetek bármilyen formátumúak lehetnek, az ügyféltár olyan egyéni formátumot határoz meg, amely tartalmazza az inicializálási vektort (IV) és a titkosított tartalomtitkosítási kulcsot (CEK) az üzenet szövegében.

A titkosítás során az ügyfélkönyvtár egy véletlenszerű IV 16 bájtot hoz létre egy 32 bájtból álló véletlenszerű CEK-val együtt, és ezzel az információval borítéktitkosítást hajt végre a várólistaüzenet szövegében. A burkolt CEK és néhány további titkosítási metaadat ezután hozzáadódik a titkosított várólista-üzenethez. Ez a módosított üzenet (lásd alább) a szolgáltatás tárolja.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

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
3. A burkolt CEK és néhány további titkosítási metaadat két további fenntartott tulajdonságként tárolódik. Az első fenntartott\_tulajdonság (ClientEncryptionMetadata1) egy karakterlánc-tulajdonság, amely a IV. A második fenntartott\_tulajdonság (ClientEncryptionMetadata2) egy bináris tulajdonság, amely a titkosított tulajdonságok adatait tárolja. A második tulajdonságban\_(ClientEncryptionMetadatadata2) lévő információ maga is titkosítva van.
4. A titkosításhoz szükséges további fenntartott tulajdonságok miatt a felhasználók mostantól csak 250 egyéni tulajdonsággal rendelkezhetnek 252 helyett. Az entitás teljes méretének 1 MB-nál kisebbnek kell lennie.

   Ne feledje, hogy csak karakterlánc-tulajdonságok titkosíthatók. Ha más típusú tulajdonságokat titkosítani szeretne, azokat karakterláncokká kell konvertálni. A titkosított karakterláncok bináris tulajdonságokként tárolódnak a szolgáltatásban, és visszakerülnek karakterláncokká (nyers karakterláncok, nem EdmType.STRING típusú EntityProperties) a visszafejtés után.

   Táblák esetén a titkosítási házirendmellett a felhasználóknak meg kell adniuk a titkosítandó tulajdonságokat. Ez úgy teheti meg, hogy ezeket a tulajdonságokat a TableEntity objektumokban tárolja az EdmType.STRING típusú típussal, és titkosítja a true értéket, vagy a encryption_resolver_function a tableservice objektumra állítja. A titkosítás-feloldó olyan függvény, amely egy partíciókulcsot, sorkulcsot és tulajdonságnevet vesz fel, és egy logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféltár ezt az információt használja annak eldöntéséhez, hogy egy tulajdonságot titkosítson-e a vezetékbe írás közben. A delegált is lehetővé teszi a logika körül, hogyan tulajdonságok titkosítását. (Ha például X, akkor titkosítsa az A tulajdonságot; egyébként titkosítsa az A és B tulajdonságokat.) Vegye figyelembe, hogy ezeket az adatokat nem szükséges megadni entitások olvasása vagy lekérdezése közben.

### <a name="batch-operations"></a>Kötegelt műveletek
Egy titkosítási házirend a köteg összes sorára vonatkozik. Az ügyfélkönyvtár belsőleg létrehoz egy új véletlenszerű IV és véletlenszerű CEK soronként a kötegben. A felhasználók dönthetnek úgy is, hogy a köteg minden műveletéhez különböző tulajdonságokat titkosítanak, ha ezt a viselkedést a titkosítási feloldóban definiálják.
Ha egy köteg et a tableservice batch() metóduson keresztül hoz létre környezetkezelőként, a tableservice titkosítási házirendje automatikusan alkalmazva lesz a kötegre. Ha egy köteg et kifejezetten a konstruktor hívásával hoz létre, a titkosítási házirendet paraméterként kell átadni, és változatlanul kell hagyni a köteg élettartama alatt.
Vegye figyelembe, hogy az entitások titkosítva vannak, amint a köteg titkosítási házirendjével kerülnek be a kötegbe (az entitások nem titkosítva vannak a köteg véglegesítésekekéna a tableservice titkosítási házirendjével).

### <a name="queries"></a>Lekérdezések
> [!NOTE]
> Mivel az entitások titkosítva vannak, nem futtathat olyan lekérdezéseket, amelyek titkosított tulajdonságra szűrnek.  Ha megpróbálja, az eredmények helytelenek lesznek, mert a szolgáltatás a titkosított adatokat titkosítatlan adatokkal próbálja összehasonlítani.
> 
> 
> Lekérdezési műveletek végrehajtásához meg kell adnia egy kulcsfeloldót, amely képes feloldani az eredményhalmaz összes kulcsát. Ha a lekérdezés eredményében szereplő entitás nem oldható fel szolgáltatóval, az ügyféltár hibát fog eljelenni. A kiszolgálóoldali vetületeket végző lekérdezések esetén az ügyféltár alapértelmezés\_szerint hozzáadja a \_speciális titkosítási metaadat-tulajdonságokat (ClientEncryptionMetadata1 és ClientEncryptionMetadata2) a kijelölt oszlopokhoz.
> 
> [!IMPORTANT]
> Az ügyféloldali titkosítás használatakor vegye figyelembe ezeket a fontos pontokat:
> 
> * Titkosított blobból olvasáskor vagy íráskor használja a teljes blob-feltöltési parancsokat és a tartomány/teljes blob letöltési parancsokat. Ne írjon titkosított blobba olyan protokollműveletekkel, mint a Put Block, put block list, Write Pages vagy Clear Pages; ellenkező esetben megsérülhet a titkosított blob, és olvashatatlan.
> * Táblák esetén hasonló megkötés létezik. Ügyeljen arra, hogy a titkosítási metaadatok frissítése nélkül ne frissítse a titkosított tulajdonságokat.
> * Ha metaadatokat állít be a titkosított blobon, felülírhatja a visszafejtéshez szükséges titkosítással kapcsolatos metaadatokat, mivel a metaadatok beállítása nem additív. Ez a pillanatképekre is igaz; ne adja meg a metaadatokat, miközben egy titkosított blob pillanatképét hozza létre. Ha be kell állítani a metaadatokat, először hívja meg a **get_blob_metadata** metódust az aktuális titkosítási metaadatok lehívásához, és kerülje az egyidejű írásokat a metaadatok beállítása közben.
> * Engedélyezze a **require_encryption** jelzőt a szolgáltatásobjektumon olyan felhasználók számára, akik csak titkosított adatokkal dolgozhatnak. Lásd alább további információkért.

A tárolóügyfél-tár elvárja, hogy a megadott KEK és kulcsfeloldó megvalósítsa a következő felületet. [A](https://azure.microsoft.com/services/key-vault/) Python KEK-kezelés Azure Key Vault-támogatása függőben van, és a rendszer beépül ebbe a tárba, ha elkészült.

## <a name="client-api--interface"></a>Ügyfél API / felület
A tárolási szolgáltatás objektumának (azaz a blockblobservice) létrehozása után a felhasználó értékeket rendelhet a titkosítási házirendet alkotó mezőkhöz: key_encryption_key, key_resolver_function és require_encryption. A felhasználók csak egy KEK, csak egy feloldó, vagy mindkettő. key_encryption_key az alapvető kulcstípus, amelyet egy kulcsazonosító segítségével azonosítanak, és amely biztosítja a körbefuttatás/kicsomagolás logikáját. key_resolver_function kulcs feloldására szolgál a visszafejtési folyamat során. Egy érvényes KEK-et ad vissza, amely kulcsazonosítót ad vissza. Ez lehetővé teszi a felhasználók számára, hogy több helyen kezelt kulcsok közül válasszanak.

A KEK-nek az alábbi módszereket kell végrehajtania az adatok sikeres titkosításához:

* wrap_key(cek): A megadott CEK (bájt) körbefolyatása a felhasználó által választott algoritmus sal. A burkolt kulcsot adja vissza.
* get_key_wrap_algorithm(): A kulcsok tördeléséhez használt algoritmust adja eredményül.
* get_kid(): Ennek a KEK-nek a karakterlánckulcs-azonosítója.
  A KEK-nek a következő módszereket kell végrehajtania az adatok sikeres visszafejtéséhez:
* unwrap_key(cek, algoritmus): A megadott CEK kicsomagolt formáját adja eredményül a karakterlánc által megadott algoritmus sal.
* get_kid(): A KEK karakterlánckulcs-azonosítót ad eredményül.

A kulcsfeloldónak legalább olyan módszert kell megvalósítania, amely kulcsazonosítóval a fenti felületet megvalósító megfelelő KEK-et adja vissza. Csak ezt a módszert kell hozzárendelni a szolgáltatásobjektum key_resolver_function tulajdonságához.

* A titkosításhoz mindig a kulcs használatos, és a kulcs hiánya hibát eredményez.
* Visszafejtéshez:

  * A kulcsfeloldó meghívása, ha meg van adva a kulcs leése. Ha a feloldó meg van adva, de nem rendelkezik a kulcsazonosító leképezésével, a rendszer hibát jelez.
  * Ha a feloldó nincs megadva, de kulcs van megadva, a kulcs akkor használatos, ha annak azonosítója megegyezik a szükséges kulcsazonosítóval. Ha az azonosító nem egyezik, a rendszer hibát jelez.

    A titkosítási minták az azure.storage.samples bemutatja a blobok, várólisták és táblák részletesebb, végpontok közötti forgatókönyvét.
      A KEK és a kulcsfeloldó mintaimplementációi a mintafájlokban KeyWrapper és KeyResolver néven találhatók.

### <a name="requireencryption-mode"></a>RequireEncryption mód
A felhasználók tetszés szerint engedélyezhetik a működési módot, ahol az összes feltöltést és letöltést titkosítani kell. Ebben a módban a titkosítási házirend nélküli adatok feltöltésére vagy a szolgáltatáson nem titkosított adatok letöltésére tett kísérletek sikertelenek lesznek az ügyfélen. A szolgáltatásobjektum **require_encryption** jelzője szabályozza ezt a viselkedést.

### <a name="blob-service-encryption"></a>Blob szolgáltatás titkosítása
Állítsa be a titkosítási házirend-mezőket a blockblobservice objektumon. Minden mást az ügyfélkönyvtár belsőleg kezel.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Várólista-szolgáltatás titkosítása
Állítsa be a titkosítási házirendmezőket a queueservice objektumon. Minden mást az ügyfélkönyvtár belsőleg kezel.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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

### <a name="table-service-encryption"></a>Táblaszolgáltatás titkosítása
A titkosítási házirend létrehozása és kérésre történő beállítása mellett meg kell adnia egy **encryption_resolver_function** a **tableservice**szolgáltatáson, vagy be kell állítania a titkosítási attribútumot az EntityProperty tulajdonságon.

### <a name="using-the-resolver"></a>A feloldó használata

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

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
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Attribútumok használata
Mint már említettük, egy tulajdonság lehet jelölni a titkosítás takarja azt egy EntityProperty objektumot, és a titkosítási mező beállítása.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Titkosítás és teljesítmény
Vegye figyelembe, hogy a tárolási adatok titkosítása további teljesítménytöbbletet eredményez. Létre kell hozni a tartalomkulcsot és a IV-et, magát a tartalmat titkosítani kell, és további metaadatokat kell formázni és feltölteni. Ez a többletterhelés a titkosított adatok mennyiségétől függ. Azt javasoljuk, hogy az ügyfelek mindig teszteljék az alkalmazások teljesítményét a fejlesztés során.

## <a name="next-steps"></a>További lépések
* Az [Azure Storage Ügyfélkönyvtár Java PyPi-csomag letöltése](https://pypi.python.org/pypi/azure-storage)
* Az [Azure Storage-ügyfélkódtár python-forráskód letöltése a GitHubról](https://github.com/Azure/azure-storage-python)
