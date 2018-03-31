---
title: A tábla Designer szűrőkarakterláncokban létrehozása |} Microsoft Docs
description: A tábla Designer szűrőkarakterláncokban létrehozása
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: 722052e351062efba85eb143b2ea7bd0136002a0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>A tábla Designer Szűrőkarakterláncokban létrehozása
## <a name="overview"></a>Áttekintés
Az Azure tábla, a Visual Studio alkalmazásban megjelenő adatok szűrése **tábla Designer**, egy szűrési karakterláncot létrehozni, és adja meg a Szűrő mezőbe. A szűrő karakterlánc-formátum: határozzák meg a WCF Data Services és egy SQL WHERE záradék hasonló, de a Table szolgáltatás via HTTP-kérelem érkezik. A **tábla Designer** alkalmazás a megfelelő, ezért szeretne szűrni kívánt tulajdonság értéke, kell csak megadni a tulajdonságnév, összehasonlító operátor, hibafeltételének értékét, és szükség esetén logikai operátor a Szűrő mezőbe. A $filter lekérdezési lehetőség tartalmazza, mint ha volt hozhat létre a tábla keresztül lekérdezni egy URL-címet nem kell a [Storage szolgáltatások REST API-referencia](http://go.microsoft.com/fwlink/p/?LinkId=400447).

A WCF Data Services alapuló a [protokollhoz adatok](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). További részletek a rendszer a szűrő lekérdezési lehetőség (**$filter**), lásd: a [URI egyezmények OData specifikáció](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Összehasonlító operátorok
A következő logikai operátorok az összes tulajdonság esetében támogatottak:

| Logikai operátor | Leírás | Példa szűrési karakterláncot |
| --- | --- | --- |
| eq |Egyenlő |Város eq "Redmond" |
| gt |Nagyobb, mint |Árlista gt 20 |
| GE |Nagyobb vagy egyenlő |Árlista ge 10 |
| lt |Kisebb, mint |Árlista lt 20 |
| le |Kisebb vagy egyenlő |Árlista le 100 |
| Ne |Nem egyenlő |Város ne "London" |
| és |És |Árlista le 200 és ár gt 3.5 |
| vagy |Vagy |Árlista le 3.5-ös vagy ár gt 200 |
| nem |nem |nem isAvailable |

Amikor hozhat létre egy szűrési karakterláncot, a következő szabályok fontosak:

* A logikai operátorok segítségével összehasonlíthatja a tulajdonság értéke. Ne feledje, hogy nem lehetséges az összehasonlítandó dinamikus értékre; tulajdonság a kifejezés egy oldalán állandónak kell lennie.
* A szűrési karakterlánc minden része megkülönbözteti a kis- és nagybetűket.
* Az állandó érték adattípusának meg kell egyeznie a tulajdonságéval ahhoz, hogy a szűrő érvényes eredményeket adjon vissza. További információ a támogatott tulajdonságtípusokról: [A Table szolgáltatás adatmodelljének ismertetése](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Szűrés karakterlánc tulajdonságai
Szűrésekor meg kapcsolatikarakterlánc-tulajdonságokat, tegye a karakterlánc-konstansra szimpla idézőjelek között szerepel.

A következő példa a szűrők a **PartitionKey** és **RowKey** ; további nem kulcs tulajdonságai Tulajdonságok kettőhöz lehet hozzáadni a szűrési karakterláncot:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Tegye minden szűrőkifejezés kerek zárójeleket tartalmazhatnak, de nincs rá szükség:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Vegye figyelembe, hogy a Table szolgáltatás nem támogatja a helyettesítő karakteres lekérdezések, és nem támogatja őket a táblázat-tervezőben vagy. Azonban a kívánt előtagot összehasonlító operátorok használatával megfelelő előtag végezheti el. Az alábbi példában az "A" betű egy LastName tulajdonság kezdetű entitásokat ad vissza:

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>A numerikus tulajdonságok szűrése
Ha szeretne szűrni az egész szám vagy egy lebegőpontos szám, adja meg az idézőjelek nélkül.

Ez a példa egy kora tulajdonsággal, amelynek értéke nagyobb, mint 30 összes entitásokat ad vissza:

    Age gt 30

Ez a példa egy AmountDue tulajdonsággal, amelynek értéke kisebb vagy egyenlő, mint 100.25 összes entitásokat ad vissza:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>A logikai tulajdonságok szűrése
Adja meg a szűréshez logikai érték, **igaz** vagy **hamis** idézőjelek nélkül.

Az alábbi példa entitásokat ad vissza az összes ahol az IsActive tulajdonsága **igaz**:

    IsActive eq true

A szűrőkifejezés nélkül a logikai operátor is írhat. A következő példában a Table szolgáltatás is visszaadható entitásokhoz IsActive esetén **igaz**:

    IsActive

Ad vissza az összes entitásokat, ahol IsActive értéke HAMIS, használja a not operátor:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Dátum és idő tulajdonságai szűrése
Egy DateTime értéket szűréshez, adja meg a **datetime** kulcsszót, és a dátum/idő állandó szimpla idézőjelek között szerepel. A dátum/idő állandó kombinált UTC formátumban kell megadni, a [formázás DateTime tulajdonságértékek](http://go.microsoft.com/fwlink/p/?LinkId=400449).

Az alábbi példa entitásokat ad vissza ahol a CustomerSince tulajdonság értéke 2008 július 10:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
