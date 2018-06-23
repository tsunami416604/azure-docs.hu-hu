---
title: A Tudásbázis feltárása Service API-séma formátumban |} Microsoft Docs
description: További információk a séma formátuma a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 3009392a5acb12a8f4df3d30a2cbe5e74f2172fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347102"
---
# <a name="schema-format"></a>Séma formátumban
A sémát a JSON-fájl, amely leírja a fájlban található objektumok az adatok segítségével hozza létre az indexet a attribútumstruktúra szerepel.  Minden attribútum a sémában a nevét, a adattípus, a választható műveletek és a választható szinonimák listája.  Az objektum már rendelkezik, 0 vagy több értéket minden attribútum.  Alább egy egyszerűsített példa academic kiadvány tartományból van:

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

Attribútumnevek megkülönböztetik a kis-és nagybetűket azonosítók betűvel kezdődhet és csak betűket (A – Z), számok (0-9) áll, és alulvonást (\_).  A fenntartott "logprob" attribútum segítségével adja meg a relatív természetes napló valószínűség objektumok között.

## <a name="attribute-type"></a>Attribútum típusa
Alább felsoroljuk attribútum támogatott adattípusok:

| Típus | Leírás | Műveletek | Példa |
|------|-------------|------------|---------|
| Sztring | Karakterlánc (1 – 1024 karakter) | egyenlő, starts_with | "hello world" |
| Int32 | 32 bites előjeles egész szám | egyenlő, starts_with, is_between | 2016 |
| Int64 | 64 bites előjeles egész szám | egyenlő, starts_with, is_between | 9876543210 |
| Dupla | Kétszeres pontosságú lebegőpontos értéket | egyenlő, starts_with, is_between | 1.602e-19 |
| Dátum | (1 400-01-01-9999-12-31) dátum | egyenlő, is_between | ' 2016-03-14' |
| GUID | Globálisan egyedi azonosítója | egyenlő | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| Blob | Belső tömörített nem indexelt adatokat | *Egyik sem* | "Minden személy és a bolygónk minden munkahely eléréséhez több ellenőrizniük" |
| Összetett | Összeállítás több alárendelt attribútumok| *N/A* | {"Name": "harry shum", "Kapcsolat": "microsoft"} |

Karakterlánc attribútumok jelölésére karakterlánc-értékek jelenhetnek meg a felhasználó lekérdezése részeként használhatók.  Támogatják-e a pontos egyezést *egyenlő* művelet, valamint a *starts_with* lekérdezés végrehajtási forgatókönyvek, például a megfelelő "micros" a "microsoft" műveletet.  Nem betűérzékeny és intelligens megfelelő helyesen adta-e hibák kezelésének egy későbbi kiadásban lesz támogatott.

Int32 vagy Int64/dupla attribútumok használata numerikus érték ábrázolásához.  A *is_between* művelet lehetővé teszi, hogy a egyenlőtlen támogatási (lt, le, gt, ge) futási időben.  A *starts_with* művelet támogatja a lekérdezés végrehajtási forgatókönyvek, például a megfelelő "20" a "2016" vagy "3". a "3.14".

Dátum attribútumok használatával hatékonyan a dátumértékek kódolása.  A *is_between* művelet lehetővé teszi, hogy a egyenlőtlen támogatási (lt, le, gt, ge) futási időben.
  
GUID attribútumok használatával hatékonyan képviselő GUID értékek alapértelmezett támogatása a *egyenlő* műveletet.

A BLOB attribútumok használatával hatékonyan kódolása potenciálisan nagy adatok blobokat futásidejű keresési a megfelelő objektum, a tartalom a blob értékek alapján bármely indexelési művelet támogatása nélkül.

### <a name="composite-attributes"></a>Összetett attribútumok
Összetett attribútumok csoportosítása attribútumértékek használatosak.  Minden egyes alárendelt attribútum neve kezdődik, az összetett attribútum követ neve ".".  A beágyazott attribútum értékeket tartalmazó JSON-objektum összetett attribútumok értékek vannak megadva.  Összetett attribútumok több objektum érték lehet.  Összetett attribútumokat azonban nincs alárendelt attribútumok, amelyek magukat összetett attribútumok.

Academic kiadvány a fenti példában ez engedélyezi az lekérdezése által írt cikkeket által "harry shum" közben: "microsoft" a szolgáltatás.  Összetett attribútumok nélkül a szolgáltatás csak lekérdezheti a "harry shum" által írt cikkeket, ahol a szerzők egyik, a szerzők egyik "Microsoft".  További információkért lásd: [összetett lekérdezések](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Attribútum-műveletek
Alapértelmezés szerint minden attribútum indexelt az attribútum adattípusa számára elérhető összes műveletek támogatásához.  Ha egy adott művelethez nem szükséges, az indexelt műveletek készletét adható explicit módon meg az index méretének csökkentése érdekében.  A következő kódrészletet a fenti példa séma, a a Author.Id attribútum indexelt csak támogatásához a *egyenlő* műveletet, de nem a további *starts_with* és *is_between*  Int32 attribútumok műveletek.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Ha a nyelvtan belül attribútum hivatkozik a *starts_with* műveletet kell megadni ahhoz, hogy a szolgáltatás használatával hozzon létre befejezésekhez részleges lekérdezések a sémában.  

## <a name="attribute-synonyms"></a>Attribútum szinonimák
Gyakran kívánatos szinonimát egy adott string attribútumérték hivatkozik.  Felhasználók például "MSFT" vagy "MS" a "Microsoft" hivatkozhat.  Ezekben az esetekben attribútumdefiníciót határozható meg a fájl ugyanabban a könyvtárban található séma fájl nevét.  Minden sora a szinonima fájl felel egy szinonima bejegyzés a következő JSON formátummal: `["<canonical>", "<synonym>"]`.  A példa séma "AuthorName.syn" Author.Name attribútumok szinonimát értékeit tartalmazó JSON-fájl.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


A kanonikus érték lehet több szinonimák.  Több kanonikus érték a közös szinonimát megoszthatja.  Ezekben az esetekben a szolgáltatás megoldja a félreérthetőség több értelmezéseket keresztül.  Az alábbiakban van egy példa AuthorName.syn szinonimák fájl megfelelő a fenti séma:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
