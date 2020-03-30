---
title: Az Azure Table tároló tervezése az adatok módosításához | Microsoft dokumentumok
description: Az Azure Table storage-ban az adatok módosításához táblákat tervezhet.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771546"
---
# <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a cikk a beszúrások, frissítések és törlések optimalizálásával kapcsolatos tervezési szempontokra összpontosít. Bizonyos esetekben ki kell értékelnie a kompromisszumot a formatervezési minták között, amelyek optimalizálják az adatmódosításra optimalizáló tervek lekérdezését, ugyanúgy, mint a relációs adatbázisok tervezésénél (bár a tervezési kompromisszumok kezelésének technikái a relációs adatbázisban eltérő). A szakasz Táblázat tervezési minták ismerteti néhány részletes tervezési minták a table szolgáltatás, és kiemeli néhány ilyen kompromisszumokat. A gyakorlatban azt fogja találni, hogy sok entitások lekérdezésére optimalizált tervek is jól működnek az entitások módosítása.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>A beszúrási, frissítési és törlési műveletek teljesítményének optimalizálása
Entitás frissítéséhez vagy törléséhez képesnek kell lennie arra, hogy a **PartitionKey** és a **RowKey** értékek használatával azonosítsa azt. Ebben a tekintetben a **partitionkey** és **rowkey** az entitások módosítására hasonló feltételeket kell követnie a választott pontlekérdezések, mert szeretné azonosítani az entitásokat a lehető leghatékonyabban. Nem szeretné, hogy egy nem hatékony partíció vagy tábla vizsgálat megkeresni egy entitást annak érdekében, hogy felfedezzék a **PartitionKey** és **RowKey** értékeket kell frissíteni vagy törölni.  

A táblázat tervezési mintái című szakaszban a következő minták a teljesítmény vagy a beszúrási, frissítési és törlési műveletek optimalizálásával foglalkoznak:  

* [Nagy mennyiségű törlési minta](table-storage-design-patterns.md#high-volume-delete-pattern) – Nagy mennyiségű entitás törlésének engedélyezése az összes entitás egyidejű törlésével a saját külön táblájukban; az entitásokat a tábla törlésével törölheti.  
* [Adatsor minta](table-storage-design-patterns.md#data-series-pattern) – A teljes adatsorokat egyetlen entitásban tárolja a kérések számának minimalizálása érdekében.  
* [Széles entitások minta](table-storage-design-patterns.md#wide-entities-pattern) – több fizikai entitás használata 252-nél több tulajdonsággal rendelkező logikai entitások tárolásához.  
* [Nagy entitások minta](table-storage-design-patterns.md#large-entities-pattern) – blob storage használatával nagy tulajdonságértékek tárolására.  

## <a name="ensure-consistency-in-your-stored-entities"></a>A tárolt entitások konzisztenciájának biztosítása
A másik kulcsfontosságú tényező, amely befolyásolja a választott kulcsok optimalizálása adatmódosítások, hogyan biztosíthatja a konzisztenciát atomi tranzakciók használatával. Egt-t csak az ugyanazon partíción tárolt entitásokon használhat.  

A cikkben a [Táblázat tervezési mintái](table-storage-design-patterns.md) a konzisztencia kezelésével foglalkoznak a következő minták:  

* [Partíción belüli másodlagos indexminta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – Tárolja az egyes entitások több példányát különböző **RowKey-értékek** használatával (ugyanabban a partícióban) a gyors és hatékony keresések és a különböző **RowKey-értékek** használatával történő rendezési sorrendek engedélyezéséhez.  
* [Partíciók közötti másodlagos indexminta](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – Tárolja az egyes entitások több példányát különböző RowKey-értékek használatával külön partíciókon vagy külön táblákban, hogy gyors és hatékony keresések és alternatív rendezési sorrendek különböző **RowKey-értékek** használatával.  
* [Végül konzisztens tranzakciók minta](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – Engedélyezése végül konzisztens viselkedés partícióhatárokon vagy a tárolási rendszer határait az Azure-várólisták használatával.
* [Entitások indexelése –](table-storage-design-patterns.md#index-entities-pattern) Az indexentitások karbantartása az entitások listáját visszaadó hatékony keresések engedélyezéséhez.  
* [Denormalizációs minta](table-storage-design-patterns.md#denormalization-pattern) – A kapcsolódó adatok egyesítése egyetlen entitásban, hogy egyetlen pontlekérdezéssel lehívhassa az összes szükséges adatot.  
* [Adatsor minta](table-storage-design-patterns.md#data-series-pattern) – A teljes adatsorokat egyetlen entitásban tárolja a kérések számának minimalizálása érdekében.  

Az entitáscsoport tranzakcióiról az [Entitáscsoport tranzakciói](table-storage-design.md#entity-group-transactions)című szakaszban talál információt.  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg róla, hogy a hatékony módosítások érdekében a tervezés megkönnyíti a hatékony lekérdezéseket
Sok esetben a hatékony lekérdezési terv hatékony módosításokat eredményez, de mindig ki kell értékelnie, hogy ez a helyzet az adott forgatókönyv esetében. A cikkben szereplő [Táblatervezési minták](table-storage-design-patterns.md) néhány példa explicit módon értékeli ki az entitások lekérdezése és módosítása közötti kompromisszumokat, és mindig figyelembe kell vennie az egyes művelettípusok számát.  

A cikkben a következő minták a [Tábla tervezési mintái](table-storage-design-patterns.md) a hatékony lekérdezések tervezése és a hatékony adatmódosítás tervezése közötti kompromisszumokat kezelik:  

* [Összetett kulcsminta](table-storage-design-patterns.md#compound-key-pattern) – Összetett **RowKey** értékek használatával engedélyezheti, hogy az ügyfél egypontos lekérdezéssel keresse meg a kapcsolódó adatokat.  
* [Log tail minta](table-storage-design-patterns.md#log-tail-pattern) – A partícióhoz legutóbb hozzáadott *n* entitások lekérése **egy RowKey** érték használatával, amely fordított dátum- és idősorrendben rendezi.  
