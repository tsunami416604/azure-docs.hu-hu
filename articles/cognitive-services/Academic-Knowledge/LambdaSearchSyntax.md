---
title: Lambda keresési szintaxis - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a Lambda keresési szintaxis az Academic Knowledge API is használhatja.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873199"
---
# <a name="lambda-search-syntax"></a>Lambda keresési szintaxis

Minden egyes *lambda* keresési lekérdezés-karakterlánc egy graph mintázatot ismertet. A lekérdezés rendelkeznie kell legalább egy kiindulási csomópont, adja meg, melyik graph csomópontból a bejárási kezdődik. Adjon meg egy kezdő csomópont, hívja meg a *MAG. StartFrom()* metódust, és adja meg az azonosítót, egy vagy több csomópont vagy egy lekérdezés, amely meghatározza, hogy a keresési korlátozó objektum. A *StartFrom()* metódus három túlterheléssel rendelkezik. Ezek mindegyike igénybe két argumentumot a második folyamatban van a nem kötelező. Az első argumentum nagyobb egész szám, egy hosszú egész enumerálható gyűjteménye vagy egy karakterlánc, amely egy JSON objektum, mint az azonos sémantikou *json* keresés:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

A lambda keresési lekérdezés írása való és megtudhatja, hogyan egyik csomópontról a másikra. Az edge megtudhatja, hogyan adhatja meg, használja a *FollowEdge()* és a kívánt edge típusok adja át. *FollowEdge()* vesz igénybe egy tetszőleges számú karakterlánc argumentum:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Ha azt nem érdeklik a követendő edge(s) típusainak, egyszerűen hagyja el *FollowEdge()* két csomópont között: a lekérdezés az összes lehetséges élek végigvezetik a két csomópont között.

A bejárási műveletek egy csomóponton keresztül végrehajtandó adható meg *VisitNode()*, azt jelenti, hogy ezen a csomóponton leállítása és az aktuális elérés úton vissza eredményként, vagy böngészhet a gráf továbbra is.  A számbavételi típussal *művelet* kétféle típusú műveletek meghatározása: *Action.Return* és *Action.Continue*. Az ilyen enum érték közvetlenül átadható *VisitNode()*, vagy kombinálhatja őket a bitenkénti- és az operátorral 'és'. Amikor a rendszer kombinálja a két művelet, az azt jelenti, hogy mindkét műveleteket hajtja végre. Megjegyzés: ne használjon bitenként- vagy operátor "|}" műveletek. Ezt a lekérdezést, hogy bármit visszaszolgáltatása nélkül felmondhatja miatt. A rendszer kihagyja *VisitNode()* két közötti *FollowEdge()* hívások feltétel nélkül megismerése után egy csomópont továbbítótól a gráf lekérdezése okoz.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

A *VisitNode()*, is átadható egy lambda kifejezés típusa *kifejezés\<Func\<INode, a művelet\>\>*, amely fogadja az *INode* és a egy bejárási művelet adja vissza:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* biztosít *csak olvasható* adat-hozzáférési felületek és a egy csomóponton néhány beépített segédfüggvények. 

### <a name="basic-data-access-interfaces"></a>Alapszintű hozzáférés Adatfelületek

##### <a name="long-cellid"></a>long CellID

A csomópont 64 bites azonosítója. 

##### <a name="t-getfieldtstring-fieldname"></a>T egyszerre GetField\<T\>(mezőnév string)

Lekérdezi a megadott tulajdonság értékét. *T* a kívánt típusa, amely a mező kellene értelmezhető. Automatikus típus döntő rendszer kísérli meg, ha a kívánt típust nem lehet implicit módon konvertálni az a mező típusát. Megjegyzés: Ha a tulajdonság értéke többértékű adatelemeket *egyszerre GetField\<karakterlánc\>*  okoz a lista szerializálható Json-karakterláncot ["1. érték", "2. érték",...]. Ha a tulajdonság nem létezik, ez lesz kivételt, és megszakíthatja a jelenlegi graph feltárása.

##### <a name="bool-containsfieldstring-fieldname"></a>logikai ContainsField (mezőnév karakterlánc)

Jelzi, ha az aktuális csomópont van a megadott nevű mező.

##### <a name="string-getstring-fieldname"></a>karakterlánc (karakterlánc mezőnév) beolvasása

Működik, mint az *egyszerre GetField\<karakterlánc\>(mezőnév)*. Azonban nem azt throw kivételeket, ha a mező nem található, egy üres string("") Ehelyett adja vissza.

##### <a name="bool-hasstring-fieldname"></a>logikai rendelkezik (mezőnév karakterlánc)

Jelzi, hogy az adott tulajdonság megtalálható-e az aktuális csomópont. Ugyanaz, mint a *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>logikai rendelkezik (karakterlánc mezőnév, karakterlánc-érték)

Jelzi, ha a tulajdonság megadott értéke. 

##### <a name="int-countstring-fieldname"></a>int száma (mezőnév karakterlánc)

Lekérni az adott tulajdonság értékét. Ha a tulajdonság nem létezik, a 0 értéket adja vissza.

### <a name="built-in-helper-functions"></a>Beépített segédfüggvények

##### <a name="action-returnifbool-condition"></a>A művelet return_if (logikai feltétel)

Értéket ad vissza *Action.Return* Ha a feltétel *igaz*. Ha a feltétel *hamis* , és ez a kifejezés nem csatlakozik tartományhoz más műveletekkel által egy bitenkénti- és operátor szerinti szűrése, a graph-feltárás meg fog szakadni.

##### <a name="action-continueifbool-condition"></a>A művelet continue_if (logikai feltétel)

Értéket ad vissza *Action.Continue* Ha a feltétel *igaz*. Ha a feltétel *hamis* , és ez a kifejezés nem csatlakozik tartományhoz más műveletekkel által egy bitenkénti- és operátor szerinti szűrése, a graph-feltárás meg fog szakadni.

##### <a name="bool-dicedouble-p"></a>logikai dice (dupla p)

Létrehoz egy véletlenszerű számot, amely nagyobb vagy egyenlő a 0,0 és 1,0-nél kevesebb. Ez a függvény visszaad *igaz* csak akkor, ha a szám kisebb vagy egyenlő *p*.

Képest *json* search *lambda* keresési kifejezőbb: C#lambda-kifejezésekkel közvetlenül segítségével adja meg a lekérdezési mintáinak. Az alábbiakban két példa.

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
