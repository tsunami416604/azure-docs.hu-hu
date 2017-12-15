---
title: "Azure Cosmos DB: SQL Python API-t, az SDK és erőforrások |} Microsoft Docs"
description: "Tudnivalók az SQL Python API és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB Python SDK verziói között végrehajtott módosításokat."
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 11/14/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 709a17b6d14e896fea7fce21e40a92e516e661c6
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB Python SDK API-SQL: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**SDK letöltése**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[Python API referenciadokumentációt tartalmaz](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**SDK telepítési utasításokat**</td><td>[Python SDK telepítési utasításokat](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Hozzájárul az SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedés a Python SDK-val](sql-api-python-application.md)</td></tr>

<tr><td>**Aktuális támogatott platform**</td><td>[Python 2.7](https://www.python.org/downloads/) és [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések
### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Az SDK-verzió telepítéséhez a legújabb Azure Cosmos DB emulátorának elérhetősége a https://aka.ms/cosmosdb-emulator letölthető.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Hiba a javítás összesített a szótárhoz.
* A tisztítás hibajavítás törtvonalat az erőforrás-hivatkozás.
* A hozzáadott tesztek a Unicode kódolással.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Egy új konzisztenciaszint támogatása ConsistentPrefix nevezik.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Összesítési lekérdezéseket (COUNT, MIN, MAX, SUM és átlagos) támogatása.
* A beállítás a letiltás SSL ellenőrzési Cosmos DB emulatorban futtatásakor hozzá.
* Függő kérések modul kell lennie, pontosan 2.10.0 korlátozásának eltávolítva.
* Minimális átviteli sebességet 2500 RU/mp 10,100 RU/mp a particionált gyűjtemények szintűre csökkent.
* Támogatja a tárolt eljárás végrehajtása során parancsfájl naplózásának engedélyezéséről.
* REST API-verziót a "2017 bumped-01-19' ebben a kiadásban.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* A dokumentációs célú Szerkesztői módosítja.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Python 3.5 támogatása.
* Támogatja a kapcsolatkészletezést kérelmek modul használatával.
* A munkamenet-konzisztencia támogatása.
* FELSŐ/ORDERBY lekérdezések particionált gyűjtemények támogatása.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Szabályozottan halmozott kérelmek hozzáadott újrapróbálkozási házirend támogatása. (A szabályozottan halmozott kérelmek egy kérelem aránya túl nagy kivétel, hibakód 429 kapnak.) Alapértelmezés szerint Azure Cosmos DB újrapróbálja kilenc alkalommal az egyes kérelmek 429. Hibakód: a rendszer észlelt, amikor az retryAfter idő a válaszfejlécet érvényesítenie. A rögzített újrapróbálkozási időköz most már beállítható a RetryOptions tulajdonság részeként a ConnectionPolicy objektum Ha azt szeretné, figyelmen kívül hagyja a próbálkozások közötti kiszolgáló által visszaadott retryAfter idő. Azure Cosmos-adatbázis most megvárja, legfeljebb 30 másodpercig (függetlenül újrapróbálkozások száma) leszabályozta, és visszaadja a választ 429. Hibakód: minden egyes kérelemhez. Most is felülbírálható lesz a RetryOptions tulajdonságban ConnectionPolicy objektumon.
* Cosmos DB most adja vissza x-ms-szabályozási--újrapróbálkozások és x-ms-throttle-retry-wait-time-ms a válaszfejlécek minden kérelemben a késleltetési jelöléséhez újra számát és a próbálkozások közötti várta a kérelem összesített idő szerint.
* Eltávolította a RetryPolicy osztály és a megfelelő tulajdonságával (retry_policy), a document_client osztály kitett, és ehelyett bevezetett teszi ki a ConnectionPolicy osztály, amely felülbírálhatja az alapértelmezett beállítások némelyike használható RetryOptions tulajdonsága RetryOptions osztály.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Több területi adatbázis fiókok támogatása.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Idő a Live(TTL) szolgáltatás dokumentumok támogatása.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Hibajavítások kapcsolódó kiszolgálóoldali particionálás a partíciós kulcs elérési útja különleges karakterek engedélyezéséhez.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználói teljesítményszintet](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Kivonatoló & tartomány hozzáadása partícióazonosító feloldókat, elősegítve ezzel a horizontális alkalmazások között több partíciót.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert megvalósításához. Új UpsertXXX módszerek Upsert szolgáltatás támogatása érdekében adott hozzá.
* Azonosító-alapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, belső összes módosítását.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Támogatja a földrajzi index.
* Ellenőrzi az összes erőforrás id tulajdonság. Erőforrások azonosító nem tartalmazhat?, /, #, \, karaktereket vagy záró szóközt.
* Új fejléc "index átalakítása folyamatban" hozzáadása ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 indexelési házirendet alkalmazza.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Proxy kapcsolatot támogat.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
A Microsoft biztosít értesítési legalább **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető. 

A Cosmos DB kivont SDK használatával a szolgáltatás vannak elutasította.

> [!WARNING]
> Az Azure SQL SDK for Python-es vagy korábbi összes verziója **1.0.0** volt használatból **2016. február 29-én**. 
> 
> 

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2017. november 10. |--- |
| [2.2.1](#2.2.1) |29. szeptember, 2017 |--- |
| [2.2.0](#2.2.0) |2017. május 10. |--- |
| [2.1.0](#2.1.0) |2017. Előfordulhat, hogy 01. |--- |
| [2.0.1](#2.0.1) |2016. október 30. |--- |
| [2.0.0](#2.0.0) |2016. Szeptembertől 29. |--- |
| [1.9.0](#1.9.0) |2016. július 07. |--- |
| [1.8.0](#1.8.0) |2016. június 14. |--- |
| [1.7.0](#1.7.0) |2016. április 26. |--- |
| [1.6.1](#1.6.1) |2016. április 08. |--- |
| [1.6.0](#1.6.0) |2016. március 29. |--- |
| [1.5.0](#1.5.0) |2016. január 03. |--- |
| [1.4.2](#1.4.2) |2015. október 06. |--- |
| [1.4.1](#1.4.1) |2015. október 06. |--- |
| [1.2.0](#1.2.0) |2015. augusztus 06. |--- |
| [1.1.0](#1.1.0) |2015. július 09. |--- |
| [1.0.1](#1.0.1) |2015. május 25. |--- |
| [1.0.0](#1.0.0) |2015. április 07. |--- |
| 0.9.4-prelease |2015. január 14. |2016. február 29. |
| 0.9.3-prelease |2014. decemberi 09 |2016. február 29. |
| 0.9.2-prelease |2014. november 25. |2016. február 29. |
| 0.9.1-prelease |2014. szeptember 23. |2016. február 29. |
| 0.9.0-prelease |2014. augusztus 21. |2016. február 29. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még:
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

