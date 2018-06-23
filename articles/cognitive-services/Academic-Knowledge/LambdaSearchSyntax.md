---
title: Academic Knowledge API-ja lambda keresési szintaxisának |} Microsoft Docs
description: További tudnivalók a Lambda keresési szintaxist a Academic Knowledge API kognitív Microsoft-szolgáltatásokban is használhatja.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f486368e1d0258087091acb846a84b125712db40
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346938"
---
# <a name="lambda-search-syntax"></a>Lambda keresési szintaxisának

Minden egyes *lambda* keresési lekérdezés-karakterlánc egy grafikonon mintát írja le. A lekérdezés rendelkeznie kell legalább egy kiindulási csomópont, mely graph-csomópont a átjárás először megadása. Adja meg a kiinduló csomóponthoz, hívja meg a *súly. StartFrom()* metódus, és adjon át egy vagy több csomópont, vagy egy lekérdezés azonosítót objektum, amely megadja, hogy a keresés megkötések. A *StartFrom()* metódus három túlterheléssel rendelkezik. Az összes tenni két argumentumot a második alatt nem kötelező. Az első argumentum lehet egész szám, a hosszú egész egy enumerálható gyűjteményhez vagy egy karakterlánc, amely egy JSON objektumot, és a azonos szemantikájú *json* keresés:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

A az lambda keresési lekérdezés írása nem végigvezetheti egyik csomópontról egy másikra. Az él bízná típusú megadásához használja *FollowEdge()* , és adja át a kívánt biztonsági típusok. *FollowEdge()* egy tetszőleges számú karakterlánc argumentumok:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Ha nem tartjuk a követendő edge(s) típusú, egyszerűen csak hagyja el a *FollowEdge()* két csomópont között: a lekérdezés a lehetséges szélén keresztül részletesen ismerteti a két csomópont között.

Az átviteli műveletek egy csomóponton keresztül végrehajtandó adható meg *VisitNode()*, ez azt jelenti, hogy ezen a csomóponton leállítására, és térjen vissza az aktuális útvonalon miatt, vagy továbbra is megismerheti a diagramon.  A számbavételi típus *művelet* határozza meg a két művelettípust: *Action.Return* és *Action.Continue*. Azt is adja át az ilyen enum érték közvetlenül a *VisitNode()*, vagy bitenkénti való egyesíthetők- és operátor "&". Két művelet kombinálva azt jelenti, hogy mindkét műveleteket hajtja végre. Megjegyzés: ne használjon bitenként- vagy operátor "|}" a műveleteket. Ez ugyanis a lekérdezés nem tér vissza semmit leáll. Kihagyása *VisitNode()* két között *FollowEdge()* hívások, akkor a lekérdezés feltétel nélkül felfedezése, mely a grafikonon egy csomóponton belül.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

A *VisitNode()*, azt is átadhatja típusú lambda kifejezésben *kifejezés\<Func\<INode, műveletet\>\>*, amely fogad egy *INode* , és adja vissza egy átjárás művelet:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* biztosít *írásvédett* adatelérési felületek és a csomópont néhány beépített segédfüggvények találhatók. 

### <a name="basic-data-access-interfaces"></a>Alapszintű data access felületek

##### <a name="long-cellid"></a>hosszú CellID

A csomópont 64 bites azonosítója. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(mezőnév karakterlánc)

Lekérdezi a megadott tulajdonság értéke. *T* a kívánt típus, amely a mező kellene hibaként értelmezi. Automatikus típus adattípusokról létrehozására történik kísérlet, ha a kívánt típusú implicit módon nem alakítható át a mező típusa. Megjegyzés: Ha a tulajdonság értéke többértékű, *GetField\<karakterlánc\>*  , akkor a lista szerializálható ["érték1", "érték2",...] Json karakterláncnak. Ha a tulajdonság nem létezik, akkor lesz kivételt jelez, és megszakítás a jelenlegi diagramhoz feltárása.

##### <a name="bool-containsfieldstring-fieldname"></a>logikai ContainsField (mezőnév karakterlánc)

Jelzi, ha az aktuális csomópont van ilyen nevű mező.

##### <a name="string-getstring-fieldname"></a>karakterlánc (string mezőnév) beolvasása

Működik, mint az *GetField\<karakterlánc\>(mezőnév)*. Azonban nem azt throw kivételek, ha a mező nem található, egy üres string("") Ehelyett adja vissza.

##### <a name="bool-hasstring-fieldname"></a>logikai rendelkezik (mezőnév karakterlánc)

Jelzi, hogy az adott tulajdonság létezik-e az aktuális csomópont. Ugyanaz, mint a *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>logikai rendelkezik (karakterlánc mezőnév, karakterlánc)

Jelzi, hogy a tulajdonság rendelkezik-e a megadott érték. 

##### <a name="int-countstring-fieldname"></a>egész szám (mezőnév karakterlánc)

Lekérni az adott tulajdonság értékét. Ha a tulajdonság nem létezik, a 0 értéket adja vissza.

### <a name="built-in-helper-functions"></a>Beépített segédfüggvények találhatók

##### <a name="action-returnifbool-condition"></a>A művelet return_if (logikai feltétel)

Beolvasása *Action.Return* Ha a feltétel *igaz*. Ha a feltétel *hamis* és a kifejezés nem egy bitenkénti keresztül kapcsolódik más műveletek- és operátor szerinti szűrése, a graph feltárása meg lesz szakítva.

##### <a name="action-continueifbool-condition"></a>A művelet continue_if (logikai feltétel)

Beolvasása *Action.Continue* Ha a feltétel *igaz*. Ha a feltétel *hamis* és a kifejezés nem egy bitenkénti keresztül kapcsolódik más műveletek- és operátor szerinti szűrése, a graph feltárása meg lesz szakítva.

##### <a name="bool-dicedouble-p"></a>logikai darabolható (kettős p)

Nagyobb vagy egyenlő 0,0 és 1,0-nél kisebb véletlenszerű számot állít elő. Ez a függvény visszaadja *igaz* csak ha a szám kisebb vagy egyenlő, mint *p*.

Összehasonlítja *json* keresési, *lambda* keresési több kifejező: C# lambda kifejezések közvetlenül segítségével adja meg a lekérdezési mintáinak. Az alábbiakban a két példa.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
