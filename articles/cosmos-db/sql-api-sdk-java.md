---
title: 'Azure Cosmos DB: SQL Java API-t, az SDK és erőforrások |} Microsoft Docs'
description: Tudnivalók az SQL Java API-t és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB SQL Java SDK verziói között végrehajtott módosításokat.
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/16/2018
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 62555d3ba3513ce7183e24506530edcf78368247
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798627"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API-t Azure Cosmos DB Java SDK: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API Java SDK támogatja a szinkron műveleteket. Az aszinkron támogatást, használja a [SQL API aszinkron Java SDK](sql-api-sdk-async-java.md). 

<table>

<tr><td>**SDK letöltése**</td><td>[Maven 3](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[Java API-referenciadokumentáció](/java/api/com.microsoft.azure.documentdb)</td></tr>

<tr><td>**Hozzájárul az SDK**</td><td>[GitHubon](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedés a Java SDK-val](sql-api-java-get-started.md)</td></tr>

<tr><td>**Webes alkalmazás oktatóanyag**</td><td>[Webalkalmazás fejlesztése a Azure Cosmos DB](sql-api-java-application.md)</td></tr>

<tr><td>**Minimális támogatott futásidejű**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Egyedi Index támogatása.
* Támogatása az adatcsatorna-beállítások folytatási jogkivonat mérete korlátozza.
* Egy hiba kijavítva a Json-szerializálás (időbélyeg).
* A hiba kijavítva a Json-szerializálás (enum).
* Frissítve az 2.9.5 com.fasterxml.jackson.core:jackson databind függ.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Továbbfejlesztett kapcsolatkészlet közvetlen mód.
* Az orderby közötti partíciólekérdezés előzetes betöltési fokozása javult.
* Továbbfejlesztett UUID létrehozása.
* Továbbfejlesztett munkamenet-konzisztencia logikát.
* Multipolygon támogatása.
* Partíciós kulcs tartomány statisztika gyűjtemény támogatása.
* Programhiba rögzített több területi támogatására.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* A Json-szerializálást javítja a teljesítményt.
* Az SDK-verzió Azure Cosmos DB emulátorának elérhetősége a legújabb verziója szükséges a letölthető https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* A Microsoft ismerősök tárak belső módosításokat.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Megtörtént egy probléma javítása olvasási egypartíciós kulcstartományokkal.
* Megtörtént egy probléma javítása a ResourceID elemzése, amely hatással van adatbázis rövid névvel.
* Rögzített egy probléma OK partíciós kulcs kódolja.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* A kérelem feldolgozása során partíció elágazást kritikus hibajavítások.
* Az erős és BoundedStaleness konzisztenciaszintek rögzített kapcsolatos problémát.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Egy új konzisztenciaszint támogatása ConsistentPrefix nevezik.
* Rögzített munkamenetmód gyűjtemény olvasási hiba.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Particionált gyűjtemény, mint az engedélyezett támogatása 2500 RU/mp, alacsony, a méretezés lépésekben 100 RU/mp.
* Rögzített, a hiba a natív szerelvény, ami azt eredményezheti NullRef kivétel néhány lekérdezést.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Programhiba rögzített, a lekérdezési motor konfigurációját, amelyek kivételek lekérdezések okozhatnak átjáró módban.
* A munkamenet-tárolóban, amelyek közvetlenül a webhelycsoport létrehozása után a kéréseket "Tulajdonosa erőforrás nem található" kivétel okozhatnak néhány hibák rögzített.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Összesítési lekérdezéseket (COUNT, MIN, MAX, SUM és átlagos) támogatása. Lásd: [összesítési támogatási](sql-api-sql-query.md#Aggregates).
* Adatcsatorna módosítás támogatása.
* Gyűjtemény kvótaadatok keresztül RequestOptions.setPopulateQuotaInfo támogatása.
* Tárolt eljárás parancsfájl naplózást RequestOptions.setScriptLoggingEnabled támogatása.
* Rögzített programhiba, ahol DirectHttps módban lekérdezés lefagyhat, amikor adatátviteli hiba észlelése.
* Programhiba rögzített munkamenet-konzisztencia módban.
* Egy hiba miatt előfordulhat, hogy NullReferenceException a HttpContext Ha nagy a kérések aránya rögzített.
* Javítja a teljesítményt a DirectHttps mód.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* A hozzáadott egyszerű példány-alapú proxyk ügyféltámogatás ConnectionPolicy.setProxy() API-t.
* A hozzáadott DocumentClient.close() API megfelelően leállítási DocumentClient példányra.
* Javítja a lekérdezési teljesítmény közvetlen kapcsolatot módban való származtatás a lekérdezésterv a natív szerelvény helyett az átjárót.
* Állítsa be a FAIL_ON_UNKNOWN_PROPERTIES = false, így a felhasználóknak nem kell a pojo-vá JsonIgnoreProperties ad meg.
* Átkerült naplózási SLF4J használatára.
* Rögzített, néhány más hibák konzisztencia-olvasó.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* A kapcsolat kezelése közvetlen kapcsolatot módban kapcsolat kiszivárgásának elkerülésére programhiba rögzített.
* Rögzített programhiba a legfelső szintű lekérdezésben, ahol NullReferenece kivétel generálhat.
* Javítja a teljesítményt a belső gyorsítótárat hálózati hívás számának csökkentésével.
* A hozzáadott állapotkód, tevékenységazonosító és a kérelem URI-azonosítója a DocumentClientException hibaelhárítást.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Megtörtént egy probléma javítása a kapcsolat kezelése stabilitását.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* BoundedStaleness konzisztenciaszint támogatása.
* Közvetlen kapcsolat CRUD műveleteihez particionált gyűjtemények támogatása.
* SQL adatbázis lekérdezése hiba kijavítva.
* Programhiba rögzített, a munkamenet-gyorsítótár, ahol munkamenet-azonosító is lehet helytelenül van beállítva.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Támogatja a párhuzamos lekérdezések partíció közötti.
* FELSŐ/ORDER BY lekérdezések a particionált gyűjtemények támogatása.
* Az erős konzisztencia támogatása.
* Alapú kérések közvetlen kapcsolat használatakor támogatása.
* Ellenőrizze az összes kérelem újrapróbálkozások között konzisztens marad ActivityId rögzített.
* Rögzített egy azonos nevű gyűjtemény újbóli létrehozása során a munkamenet-gyorsítótár kapcsolatos hiba.
* A hozzáadott sokszög és LineString adattípusok indexelő házirend geokerítések térbeli lekérdezéseket a gyűjtemény meg.
* Java Doc Java 1.8 rögzített problémákat.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Az egypartíciós gyűjtemények gyorsítótárba, és nem kulcs kérelmek partícióazonosító extra fetch PartitionKeyDefinitionMap programhiba rögzített.
* A hiba nem próbálja meg, amikor egy helytelen partíciókulcs-értékkel van megadva a rögzített.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Több területi adatbázis fiókok támogatása.
* A beállítások az újrapróbálkozások maximális és a várakozási idő maximális újrapróbálkozási testreszabásához szabályozottan halmozott kérelmek automatikus újrapróbálkozási támogatása.  Lásd: RetryOptions és ConnectionPolicy.getRetryOptions().
* Elavult IPartitionResolver egyéni particionálási kód alapján. A particionált gyűjtemények használata magasabb tárolási és átviteli sebességet.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* A hozzáadott újrapróbálkozási házirend támogatása sávszélesség-szabályozás.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* A hozzáadott idő live (TTL)-támogatás a dokumentumok.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Megvalósított [particionált gyűjtemények](partition-data.md) és [felhasználói teljesítményszintet](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Programhiba rögzített HashPartitionResolver kivonatértékeket little endian Csomagjától konzisztens kell létrehozni.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Kivonatoló & tartomány hozzáadása partícióazonosító feloldókat, elősegítve ezzel a horizontális alkalmazások között több partíciót.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert megvalósításához. Új upsertXXX módszerek Upsert szolgáltatás támogatása érdekében adott hozzá.
* Azonosító-alapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, belső összes módosítását.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Kiadás verziószáma átveendő Csomagjától összehangolás kihagyva

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Támogatja a földrajzi Index
* Ellenőrzi az összes erőforrás id tulajdonság. Erőforrások azonosító nem tartalmazhat?, /, #, \, karaktereket vagy záró szóközt.
* Új fejléc "index átalakítása folyamatban" hozzáadása ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 indexelési házirendet alkalmazza

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Kiadás és a használatból való kivonást dátuma
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető.

A Cosmos DB kivont SDK használatával fog kell elutasította a szolgáltatás.

> [!WARNING]
> Összes verziója az SQL Java SDK-es vagy korábbi **1.0.0** volt használatból **2016. február 29-én**.
> 
> 

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.16.1](#1.16.1) |2018. május 16. |--- |
| [1.16.0](#1.16.0) |2018. március 15. |--- |
| [1.15.0](#1.15.0) |2017. november 14. |--- |
| [1.14.0](#1.14.0) |2017. Oct 28. |--- |
| [1.13.0](#1.13.0) |2017. augusztus 25. |--- |
| [1.12.0](#1.12.0) |2017. július 11. |--- |
| [1.11.0](#1.11.0) |2017. május 10. |--- |
| [1.10.0](#1.10.0) |2017. március 11. |--- |
| [1.9.6](#1.9.6) |2017. február 21. |--- |
| [1.9.5](#1.9.5) |2017. január 31-ig. |--- |
| [1.9.4](#1.9.4) |2016. november 24. |--- |
| [1.9.3](#1.9.3) |2016. október 30. |--- |
| [1.9.2](#1.9.2) |2016. október 28. |--- |
| [1.9.1](#1.9.1) |2016. október 26. |--- |
| [1.9.0](#1.9.0) |2016. október 03. |--- |
| [1.8.1](#1.8.1) |2016. június 30. |--- |
| [1.8.0](#1.8.0) |2016. június 14. |--- |
| [1.7.1](#1.7.1) |2016. április 30. |--- |
| [1.7.0](#1.7.0) |2016. április 27. |--- |
| [1.6.0](#1.6.0) |2016. március 29. |--- |
| [1.5.1](#1.5.1) |2015. december 31. |--- |
| [1.5.0](#1.5.0) |2015. december 04. |--- |
| [1.4.0](#1.4.0) |2015. október 05. |--- |
| [1.3.0](#1.3.0) |2015. október 05. |--- |
| [1.2.0](#1.2.0) |2015. augusztus 05. |--- |
| [1.1.0](#1.1.0) |2015. július 09. |--- |
| [1.0.1](#1.0.1) |2015. május 12. |--- |
| [1.0.0](#1.0.0) |2015. április 07. |--- |
| 0.9.5-prelease |2015. gyel 09. |2016. február 29. |
| 0.9.4-prelease |2015. február 17. |2016. február 29. |
| 0.9.3-prelease |2015. január 13. |2016. február 29. |
| 0.9.2-prelease |2014. decemberi 19 |2016. február 29. |
| 0.9.1-prelease |2014. decemberi 19 |2016. február 29. |
| 0.9.0-prelease |2014. december 10. |2016. február 29. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

