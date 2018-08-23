---
title: Hozhat létre, amely a szűrési karakterláncot a tábla Designer |} A Microsoft Docs
description: A tábla Designer hozhat létre, amely szűrő karakterláncok
services: visual-studio-online
author: ghogen
manager: douge
assetId: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: 3ed3e0829932a6db37b4bd48627b68480f5d7343
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054715"
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>A tábla Designer hozhat létre, amely szűrő karakterláncok
## <a name="overview"></a>Áttekintés
Az Azure-tábla a Visual Studio alkalmazásban megjelenő adatok szűrése **Táblatervező**, hozza létre a szűrési karakterláncot, és írja be a Szűrő mezőbe. A szűrő-karakterlánc szintaxisát határozza meg a WCF-adatszolgáltatások és a egy SQL WHERE záradék hasonló, de a Table service via HTTP-kérést küld. A **Táblatervező** kezeli a megfelelő kódolását, ezért szeretne szűrni egy kívánt tulajdonságot, kell csak megadni a tulajdonság nevét, összehasonlító operátor, feltétel értékének és szükség esetén a logikai operátor a Szűrő mezőbe. A $filter lekérdezési beállítást tartalmazza, mint egy URL-cím lekérdezése a tábla keresztül is hozhat létre, ha nem kell a [Storage szolgáltatások REST API-referencia](http://go.microsoft.com/fwlink/p/?LinkId=400447).

A WCF-adatszolgáltatások alapulnak a [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). A szűrő rendszer lekérdezési beállítás részleteiért (**$filter**), tekintse meg a [OData URI konvenciók specifikáció](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Összehasonlító operátorok
A következő logikai operátor használata támogatott, minden tulajdonság esetében:

| Logikai operátor | Leírás | Példa szűrési karakterlánc. |
| --- | --- | --- |
| EQ |Egyenlő |Város eq "Redmond" |
| gt |Nagyobb, mint |Ár gt 20 |
| a GE |Nagyobb vagy egyenlő |Ár ge 10 |
| lt |Kisebb mint |Ár lt 20 |
| le |Kisebb vagy egyenlő |Ár le 100 |
| Ne |Nem egyenlő |Város ne "London" |
| és |És |Ár le 200-as és az ár gt 3.5 |
| vagy |Vagy |Ár gt 200-as vagy ár le 3.5. |
| nem |nem |nem isAvailable |

Szűrő karakterláncának létrehozásánál a következő szabályok fontosak:

* A logikai operátorok segítségével hasonlíthat össze tulajdonságot egy értéket. Vegye figyelembe, hogy már nem hasonlíthat össze tulajdonságot dinamikus értékkel; a a kifejezés egyik oldalának állandónak kell lennie.
* A szűrési sztring minden része megkülönbözteti a kis- és nagybetűket.
* Az állandó érték adattípusának meg kell egyeznie a tulajdonságéval ahhoz, hogy a szűrő érvényes eredményeket adjon vissza. További információ a támogatott tulajdonságtípusokról: [A Table szolgáltatás adatmodelljének ismertetése](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Szűrési karakterlánc-tulajdonságok
A karakterlánc-tulajdonságok szűrésekor közé a karakterlánc-konstansra szimpla idézőjelek között.

Az alábbi példa a szűri a **PartitionKey** és **rowkey tulajdonságok esetén** tulajdonságok; további nem kulcs tulajdonságok is hozzáadni a szűrési karakterláncot:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Minden egyes szűrőkifejezés is formázandó szöveget zárja dupla zárójelek között, bár ez nem szükséges:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Vegye figyelembe, hogy a Table service nem támogatja a helyettesítő karaktert tartalmazó lekérdezéseket, és ezek nem támogatottak a tábla-tervezőben vagy. Előtag-megfeleltetési a kívánt előtaghosszra összehasonlító operátorok használatával is elvégezheti. Az alábbi példa az "A" betűvel kezdődő LastName tulajdonság entitásokat ad vissza:

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>A numerikus tulajdonságok szűrése
Szűrés egy egész vagy lebegőpontos szám, adja meg az idézőjelek nélkül.

Ebben a példában egy kora tulajdonsággal, amelynek az értéke nagyobb, mint 30 összes entitásokat ad vissza:

    Age gt 30

Ebben a példában egy AmountDue tulajdonsággal, amelynek értéke kisebb vagy egyenlő 100.25 összes entitásokat ad vissza:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>A logikai tulajdonságokat szűrése
Szűrés egy logikai értéket, adja meg a **igaz** vagy **hamis** idézőjelek nélkül.

Az alábbi példa entitásokat ad vissza az összes, az IsActive tulajdonsága **igaz**:

    IsActive eq true

A szűrőkifejezés anélkül, hogy a logikai operátor is írhat. A következő példában a Table service fog is minden olyan entitások visszaadása, IsActive van **igaz**:

    IsActive

Az összes olyan entitások visszaadása, IsActive értéke HAMIS, használhatja a not operátor:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Szűrés a dátum és idő tulajdonságai
Szűrés egy dátum/idő értéket, adja meg a **datetime** kulcsszót, és a dátum/idő állandó szimpla idézőjelek között. A dátum/idő állandó kombinált UTC formátumban kell megadni, leírtak szerint [dátum és idő formázása tulajdonságértékek](http://go.microsoft.com/fwlink/p/?LinkId=400449).

Az alábbi példa entitásokat ad vissza, a CustomerSince tulajdonság értéke 2008. július 10:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
