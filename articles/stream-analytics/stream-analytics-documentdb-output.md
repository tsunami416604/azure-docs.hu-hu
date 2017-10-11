---
title: "A Stream Analytics JSON-kimenetét |} Microsoft Docs"
description: "Ismerje meg, hogyan Stream Analytics egy célcsoport kijelölésével az Azure Cosmos DB JSON kimeneti adatok archiválása és alacsony késésű lekérdezései strukturálatlan JSON-adatokat."
keywords: JSON kimeneti
documentationcenter: 
services: stream-analytics,documentdb
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: cc80b0080c806541362a1ef2d71b95862bd51ca2
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="target-azure-cosmos-db-for-json-output-from-stream-analytics"></a>Cél Azure Cosmos DB JSON-kimenetét a Stream Analytics
A Stream Analytics célba [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) JSON-kimenetét, engedélyezi az archiválási és alacsony késésű kérelmek strukturálatlan JSON-adatokat. Ez a dokumentum ismertet néhány gyakorlati tanácsok a konfiguráció alkalmazásához.

Azok számára, akiknek nem ismeri az Cosmos DB, vessen egy pillantást [Azure Cosmos DB képzési terv](https://azure.microsoft.com/documentation/learning-paths/documentdb/) a kezdéshez. 

Megjegyzés: A Mongo DB API alapú Cosmos DB gyűjtemények jelenleg nem támogatott. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Egy kimeneti célként Cosmos DB alapjai
Az Azure Cosmos DB kimeneti a Stream Analytics lehetővé teszi, hogy írása a streamfeldolgozási eredmények JSON kimenetként a Cosmos DB következő gyűjtemény(ek) készleteit szinkronizálja azokat. A Stream Analytics nem gyűjtemények létrehozása az adatbázis ehelyett nincs szükség kell létrehoznia őket előzetes megfizetése esetén. Ez az, hogy a számlázási költségek Cosmos DB gyűjtemények nem érzékelhető, és úgy, hogy a teljesítmény, a konzisztencia és a kapacitás közvetlenül a gyűjtemények észlelheti a [Cosmos DB API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx). A folyamatos átviteli feladat gyűjtemények logikailag külön egy Cosmos adatfolyam-feladatot DB adatbázishoz használatát javasoljuk.

A Cosmos DB adatgyűjtési beállítások némelyike részleteket lejjebb olvashatja.

## <a name="tune-consistency-availability-and-latency"></a>Konzisztencia, a rendelkezésre állás és a késleltetés hangolása
Saját alkalmazás igényeinek Cosmos DB lehetővé teszi, hogy jól hangolja az adatbázis és a gyűjtemények és konzisztencia, a rendelkezésre állás és a késleltetés közötti kompromisszumot. Attól függően, hogy milyen szintű olvasási konzisztenciát elleni forgatókönyv igényeinek és olvasási késés, kiválaszthatja a konzisztenciaszint adatbázis fiókja. Alapértelmezés szerint is Cosmos DB engedélyezi szinkron indexelő minden CRUD-műveletnek a gyűjteményhez. Ez a beállítás egy másik hasznos az írási/olvasási teljesítményt Cosmos DB szabályozására. A témakörrel kapcsolatos további információkért tekintse át a [módosítsa az adatbázis és a lekérdezés konzisztenciaszintek](../documentdb/documentdb-consistency-levels.md) cikk.

## <a name="upserts-from-stream-analytics"></a>A Stream Analytics Upserts
Cosmos DB Stream Analytics integrációja lehetővé teszi beszúrásához vagy frissítéséhez rögzíti a megadott azonosító oszlop alapján Cosmos DB gyűjteményben. Ez más néven a egy *Upsert*.

A Stream Analytics használja az optimista Upsert módszert használja, ha frissítések csak végzett amikor insert Dokumentumazonosítója ütközés miatt sikertelen. A frissítés végzi el a Stream Analytics, a javítás, lehetővé teszi, hogy a dokumentumhoz, azaz hozzáadása új tulajdonságok vagy cseréje a meglévő tulajdonságot Növekményesen történik részleges frissítések. Vegye figyelembe, hogy megváltoztatja a tömb tulajdonságok a JSON-dokumentum eredményben a tömbben teljes első felülírja, azaz a tömb értékei nem egyesített.

## <a name="data-partitioning-in-cosmos-db"></a>A Cosmos DB adatparticionálás
A cosmos DB [particionált gyűjtemények](../cosmos-db/partition-data.md) az ajánlott módszer az adatok felosztásának vannak. 

Egyetlen Cosmos DB gyűjtemények Stream Analytics továbbra is lehetővé teszi az adatok a lekérdezési mintáknak és a teljesítménye az alkalmazás igényeinek alapuló particionálásához. Minden gyűjtemény legfeljebb 10GB adatot (maximum) tartalmazhat, és jelenleg nincs mód vertikális felskálázás (vagy túlcsordulás) egy gyűjtemény. Kiterjesztése, a Stream Analytics lehetővé teszi több gyűjtemény egy adott előtaggal rendelkező írni (használati részleteit lásd alább). A Stream Analytics használ a konzisztens [kivonatoló partíció feloldó](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) stratégia alapján a felhasználó a megadott kimeneti rekordjai particionálásához PartitionKey oszlop. A folyamatos átviteli feladat kezdési időpontban a megadott előtaggal rendelkező gyűjtemények száma használatos a kimeneti partíciók száma, amelyre párhuzamosan ír a feladat (Cosmos DB gyűjtemények = kimeneti partíciók). A lusta indexelési ezzel együtt egyetlen gyűjtemény csak szúrja be, kapcsolatos 0,4 MB/s teljesítménye várhatók. Több gyűjteményt használ lehetővé teszi, nagyobb átviteli sebesség és a nagyobb kapacitás elérése érdekében.

Ha azt tervezi, a partíciók száma növelésére a jövőben, szükség lehet a feladat leállítása, a meglévő gyűjtemények új gyűjteményekbe adatait újraparticionálása, és indítsa újra a Stream Analytics-feladat. További részleteket a PartitionResolver használatával, és újra particionálás mintakód, valamint követő post fognak szerepelni. A cikk [particionálás és Cosmos DB skálázás](../documentdb/documentdb-partition-data.md) is részletesen itt.

## <a name="cosmos-db-settings-for-json-output"></a>JSON kimeneti cosmos DB beállításai
Az alább látható információt adatkérést Cosmos-adatbázis létrehozása a Stream Analytics kimenetként állít elő. Ez a szakasz a Tulajdonságok definíció magyarázattal szolgál.

Particionált gyűjtemény | Több "Egypartíciós" gyűjtemény
---|---
![a documentdb stream analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![a documentdb stream analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> A **több "Egypartíciós" gyűjtemény** forgatókönyv partíciós kulcs használatát igényli, és a támogatott konfiguráció. 

* **A kimeneti Alias** – tekintse meg a kimenetet a ASA lekérdezés alias egy  
* **A fiók neve** – a név vagy a végpont URI-azonosítója a Cosmos DB fiók.  
* **Kulcs fiók** – Cosmos DB fiók a megosztott elérési kulcsot.  
* **Adatbázis** – Cosmos-adatbázis az adatbázis nevét.  
* **Gyűjteménynévmintája** – a gyűjtemény nevét vagy a használandó gyűjtemények gyűjteménynévmintája. A gyűjteménynév-formátum az opcionális {partition} token használatával, ahol a partíciók 0-tól kezdődnek lehet létrehozni. Minta érvényes bemenetei a következők:  
  1\) MyCollection – egy gyűjteményt a következő "MyCollection" néven már léteznie kell.  
  2\) MyCollection {partition} – ilyen gyűjteményeknek létezniük kell – "MyCollection0", "MyCollection1", "MyCollection2" és így tovább.  
* **Kulcs partícióazonosító** – nem kötelező. Ez csak akkor van szükség, ha a gyűjteménynévmintája {particionáló} jogkivonatot használ. A kimeneti eseményekben a kimenet gyűjtemények közötti particionálására szolgáló kulcs megadásához használt mező neve. Egyetlen gyűjtemény kimeneti bármilyen tetszőleges kimeneti oszlop lehet például PartitionId használt.  
* **Dokumentálja azonosító** – nem kötelező. A kimeneti eseményekben a mely Beszúrás vagy frissítés műveletek alapuló elsődleges kulcs megadásához használt mező neve.  
