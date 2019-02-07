---
title: Adatok módosítása az Azure storage-táblák tervezése |} A Microsoft Docs
description: Tervezési adatok módosítása az Azure table storage-táblákat.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: e993d169025f9b76c5e813bae31ca6cb2a39ba71
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809518"
---
# <a name="design-for-data-modification"></a>Tervezés adatmódosításhoz
Ez a cikk a Beszúrás, frissítés, optimalizálásával kapcsolatos kialakítási szempontok koncentrál, és törli. Bizonyos esetekben kell kiértékelni kompromisszumot kötni a tervek, melyek optimalizálják az ellen, melyek optimalizálják az adatok módosítását, ugyanúgy, mint a relációs adatbázisok tervek (bár a technika kezeléséhez a Tervező feláldozását tervek között eltér a relációs adatbázis). A szakasz Table tervezési minták for a Table service néhány részletes tervezési minták ismerteti, és kiemeli a néhányat ezek skálán. A gyakorlatban tapasztalni fogja, hogy sok műveletekhez optimalizálva entitások lekérdezése is esetén működik megfelelően entitások módosítása.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Az insert, update és delete műveletek teljesítményének optimalizálása
Szeretne frissíteni, vagy olyan entitást töröl, meg kell tudni segítségével azonosítható a **PartitionKey** és **RowKey** értékeket. Ebben a tekintetben a választott **PartitionKey** és **RowKey** az entitások módosítása kell követniük hasonló feltételek pont lekérdezések támogatására, mert a szervezetek, mint a azonosítani szeretné a kívánt lehető leghatékonyabb. Nem szeretné megkeresni egy entitás felderítése nem elég hatékony partíció vagy tábla vizsgálat használandó a **PartitionKey** és **RowKey** értékeket kell frissítenie vagy törölnie azt.  

A következő minták szakaszában táblázat kialakítási minták cím optimalizálása, a teljesítmény vagy az insert, frissítési és törlési műveletek:  

* [Nagy mennyiségű törlése minta](table-storage-design-patterns.md#high-volume-delete-pattern) -entitások nagy mennyiségű törlésének engedélyezése egyidejű törlésre az entitásokat saját különálló táblában tárolja, a tábla törlésével törli az entitásokat.  
* [Adatsorozat adatmintát](table-storage-design-patterns.md#data-series-pattern) -Store mindazok az adatok sorozat egyetlen entitás minimalizálása érdekében, hogy kérések száma.  
* [Széles körű entitások minta](table-storage-design-patterns.md#wide-entities-pattern) -használni a több fizikai entitás legfeljebb 252 tulajdonságot tartalmazhat rendelkező logikai entitás.  
* [Nagy entitások minta](table-storage-design-patterns.md#large-entities-pattern) -a blob storage nagy tulajdonságértékek tárolásához.  

## <a name="ensure-consistency-in-your-stored-entities"></a>A tárolt entitások egységességének biztosítása
A többi kulcsfontosságú tényező, amely befolyásolja a kiválasztott adatok módosítások optimalizálásához kulcsok, hogyan lehet biztosítani a konzisztenciát elemi tranzakciókat használatával. Egy EGT segítségével csak ugyanazon a partíción tárolt entitások a művelethez.  

A cikk a következő minták [Table tervezési minták](table-storage-design-patterns.md) konzisztencia kezelése címe:  

* [Intra-partition másodlagos index minta](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store több példányban minden entitás használatával különböző **RowKey** értékeket (az ugyanazon a partíción) a gyors és hatékony kereséseket, valamint alternatív a rendezési sorrend különböző használatával **RowKey** értékeket.  
* [Közötti partíció másodlagos indexe mintája](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store értékeivel különböző rowkey tulajdonságok esetén külön partícióban, vagy külön táblázatban gyorsan minden entitás több példányát, és hatékony keresések és a másodlagos rendezés orders különböző használatával**RowKey** értékeket.  
* [Végül konzisztens tranzakció minta](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – lehetővé teszi a végül konzisztens viselkedés partícióhatárok vagy a tárolási rendszer határokat funkciókat az Azure-üzenetsorok használatával.
* [Index entitások minta](table-storage-design-patterns.md#index-entities-pattern) -karbantartása index entitások engedélyezése a hatékony keresést, hogy az entitások listáját adja vissza.  
* [Denormalizáció minta](table-storage-design-patterns.md#denormalization-pattern) -összevonás kapcsolatos adatok együtt az egyetlen entitás ahhoz, hogy minden hibaérzékeny pont lekérdezés szükséges adatok lekéréséhez.  
* [Adatsorozat adatmintát](table-storage-design-patterns.md#data-series-pattern) -Store mindazok az adatok sorozat egyetlen entitás minimalizálása érdekében, hogy kérések száma.  

Tranzakciók kapcsolatos információkért lásd a szakasz [tranzakciók](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg arról, a rendszer a módosítások hatékony tervezése elősegíti a hatékony lekérdezések
Sok esetben egy hatékony módosításokat, de hatékony lekérdezési eredmények kialakítása kell mindig mérlegelje, hogy ez a helyzet az adott forgatókönyvhöz. A cikk mintáit [Table tervezési minták](table-storage-design-patterns.md) explicit módon a lekérdezésére és módosítására szolgáló entitások közötti skálán értékeli, és Ön mindig figyelembe kell vennie a különböző típusú műveletet számát.  

A cikk a következő minták [Table tervezési minták](table-storage-design-patterns.md) hatékony lekérdezések tervezése és hatékony adatmódosítás kialakítása során közti kompromisszummal cím:  

* [Összetett kulcs minta](table-storage-design-patterns.md#compound-key-pattern) -használat összetett **RowKey** értékeket talált kapcsolódó adatok egyetlen pont lekérdezéssel ügyfél engedélyezése.  
* [Log tail minta](table-storage-design-patterns.md#log-tail-pattern) -lekérni a *n* használatával a partíció legutóbb hozzáadott entitásokat egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  
