---
title: Séma-formátum – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a séma formátuma a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549681"
---
# <a name="schema-format"></a>Sémaformátum

A séma van megadva az objektumok az adatfájlban, az index létrehozásához használt attribútum szerkezetét leíró JSON-fájlban.  Minden attribútum a sémában a név, adattípus, nem kötelező műveletek és választható szinonimák lista.  Előfordulhat, hogy az objektum 0 vagy több értéket az összes attribútumot.  Alább egy egyszerűsített példa academic kiadvány tartományból van:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Attribútum megkülönböztetik a kis-és nagybetűket azonosítók, amelyeket a betűvel kezdődhet, és tartalmazhatnak csak betűket (A – Z), számokat (0 – 9) és aláhúzásjelet (\_).  A fenntartott "logprob" attribútum segítségével adja meg a relatív természetes logaritmusát valószínűségek objektumok között.

## <a name="attribute-type"></a>Attribútum típusa

Alább felsoroljuk a attribútum támogatott adattípusok:

| Typo | Leírás | Műveletek | Példa |
|------|-------------|------------|---------|
| `String` | Karakterlánc (1 és 1024 karakter) | egyenlő, starts_with | "hello world" |
| `Int32` | 32 bites, előjeles egész | egyenlő, starts_with, is_between | 2016 |
| `Int64` | 64 bites, előjeles egész | egyenlő, starts_with, is_between | 9876543210 |
| `Double` | Kétszeres pontosságú lebegőpontos értéket | egyenlő, starts_with, is_between | 1.602e-19 |
| `Date` | (1400-01-01-9999-12-31) dátum | egyenlő, is_between | '2016-03-14' |
| `Guid` | Globálisan egyedi azonosító | egyenlő | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| `Blob` | Belsőleg tömörített nem indexelt adatok | *Nincsenek* | "Minden ember és minden szervezet tett kötelezettségvállalásai biztosítson hatékony eszközöket" |
| `Composite` | Összeállítás több alárendelt attribútumok| *N/A* | {"Name": "harry shum", "Kapcsolat": "microsoft"} |

Karakterlánc-attribútumok segítségével a felhasználó lekérdezése során megjelenő karakterlánc-értékek jelölik.  Támogatják-e a pontos egyezést *egyenlő* művelet, valamint a *starts_with* lekérdezés befejezési forgatókönyvek, például a megfelelő "micros" a "microsoft" művelet.  Helyesírás-ellenőrzés hibáinak kezelése a megfelelő kis-és nagybetűket, és intelligens egy későbbi kiadásban lesz támogatott.

Int32/Int64/dupla attribútumok segítségével numerikus értékeket jelölik.  A *is_between* művelet lehetővé teszi, hogy a egyenlótlenség támogatási (lt, le, BT, a ge) futási időben.  A *starts_with* műveletet támogatja a lekérdezési befejezési forgatókönyvek, például a megfelelő "20" a "2016-tal" vagy "3". a "3,14".

Hatékony kódolási dátumértékeket dátumattribútumok szolgálnak.  A *is_between* művelet lehetővé teszi, hogy a egyenlótlenség támogatási (lt, le, BT, a ge) futási időben.
  
GUID-attribútumok segítségével hatékonyan a alapértelmezés szerint támogatja a GUID-értékek jelölik a *egyenlő* műveletet.

BLOB attribútumok segítségével hatékonyan kódolása potenciálisan nagy mennyiségű adat-blobok futásidejű keresési, a megfelelő objektumot, minden a tartalom a blob értékek alapján indexelési művelet támogatása nélkül.

### <a name="composite-attributes"></a>Összetett attribútumok

Összetett attribútumok jelölésére attribútumértékek csoportja használhatók.  Minden alárendelt attribútum neve követ, az összetett attribútum neve kezdődik ".".  Összetett attribútumainak értékeit egy beágyazott attribútum értékeket tartalmazó JSON-objektumként vannak megadva.  Előfordulhat, hogy az összetett attribútumok több objektum érték.  Összetett attribútumokat azonban nem lehet alárendelt az attribútumokat, amelyek összetett attribútumok maguk is.

Oktatási kiadvány a fenti példában az lehetővé teszi a szolgáltatás lekérdezni a tanulmányok szerint "harry shum" a "Microsoft" közben.  Összetett attribútumok nélkül a szolgáltatás csak lekérdezheti a tanulmányok, ahol a szerző egyik "harry shum" és a szerzők egyik, a "Microsoft".  További információkért lásd: [összetett lekérdezések](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Attribútum-műveletek

Alapértelmezés szerint minden attribútum indexelt az attribútum adattípusa számára elérhető összes műveletek támogatásához.  Ha egy adott művelethez nem szükséges, az indexelt műveletkészletet explicit módon megadható az index méretének csökkentése érdekében.  Az alábbi kódrészletben a fenti példa séma, az Author.Id attribútum indexelt támogatása csak a *egyenlő* műveletet, de nem a további *starts_with* és *is_between*  Int32 típusú attribútumok műveleteket.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Amikor egy attribútum hivatkozik a nyelvtani belül a *starts_with* műveletet meg kell határozni a sémában ahhoz, hogy a szolgáltatás kiegészítése generálhatnak részleges lekérdezéseket.  

## <a name="attribute-synonyms"></a>Attribútum-szinonimák

Gyakran kívánatos tekintse meg a szinonima által egy adott karakterlánc-attribútum értéke.  Felhasználók például "Microsoft", "MSFT" vagy "MS"-hivatkozhatnak.  Ezekben az esetekben az attribútum definíciója is adja meg a fájl ugyanabban a címtárban található egy séma-fájl neve.  A szinonima fájl minden sorához jelöli egy szinonima bejegyzés a következő JSON formátumban: `["<canonical>", "<synonym>"]`.  A példa séma "AuthorName.syn" egy JSON-fájlt, amely a Author.Name attribútum szinonimát értékeket tartalmaz.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Előfordulhat, hogy a canonical érték több szinonimák.  Több forgalmára kanonikus értékeket egy közös szinonimát megoszthatja.  Ezekben az esetekben a szolgáltatás feloldja a kétértelműséget több értelmezések keresztül.  Az alábbiakban van egy példa AuthorName.syn szinonimák fájl megfelelő a fenti séma:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
