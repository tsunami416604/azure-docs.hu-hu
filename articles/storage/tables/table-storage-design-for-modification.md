---
title: Az Azure storage-táblákat adatok módosítása a(z) kialakítása |} Microsoft Docs
description: Tervezze meg a táblázatok az adatok módosítása az Azure table storage-ban.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 6c008175f01521ce4f96d13e58244dc72d9f6990
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660910"
---
# <a name="design-for-data-modification"></a>Adatmódosítás kialakítása
Ez a cikk a beszúrások, a frissítések optimalizálása kialakítási szempontjai összpontosít, és törli. Néhány esetben szüksége lesz a kompromisszum terveket, terveket, hasonlóan a relációs adatbázisok terveket (bár a Tervező kompromisszumot kezelésére szolgáló módszerek optimalizálás adatok módosítása ellen lekérdezése optimalizálása közötti kiértékelése különböző egy relációs adatbázisban). A szakasz [táblázat kialakítási minta](#table-design-patterns) néhány részletes kialakítási minta a Table szolgáltatás ismerteti, és kiemeli a néhányat ezek kompromisszumot. A gyakorlatban találja, hogy az entitás lekérdezése optimalizált sok tervek is alkalmas entitások módosítása.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Insert, update és delete műveletek teljesítményének optimalizálásához
Frissíteni vagy törölni egy entitást, meg kell tudni használatával azonosíthassák a **PartitionKey** és **RowKey** értékeket. Ebben a tekintetben a választott **PartitionKey** és **RowKey** az entitások módosítása nyújtanak hasonló feltételek pont lekérdezések támogatja, mert a szervezetek, mint a azonosítani szeretné az Ön választása lehető leghatékonyabb módon. Nem használni kívánt nem elég hatékony partíció vagy tábla vizsgálat entitás található felderítéséhez a **PartitionKey** és **RowKey** értékek frissíteni vagy törölni kell.  

A szakasz a következő minták [táblázat kialakítási minta](#table-design-patterns) cím optimalizálása a teljesítmény vagy a beszúrási, frissítési és törlési műveletek:  

* [Nagy mennyiségű törlése mintát](table-storage-design-patterns.md#high-volume-delete-pattern) -a nagyszámú entitások törlésének engedélyezése a entitásokhoz egyidejű törlésre tárolása saját különálló táblában; a tábla törlésével törli az entitásokat.  
* [Adatsorozat adatmintát](table-storage-design-patterns.md#data-series-pattern) -tároló teljes adatsorok egyetlen entitás minimalizálása érdekében elvégezte kérelmek számát jelenti.  
* [Széles entitások mintát](table-storage-design-patterns.md#wide-entities-pattern) -használjon több fizikai entitás legfeljebb 252 tulajdonságot rendelkező logikai entitás tárolására.  
* [Nagy entitások mintát](table-storage-design-patterns.md#large-entities-pattern) -blob-tároló használjon nagy tulajdonságértékek tárolásához.  

## <a name="ensure-consistency-in-your-stored-entities"></a>A tárolt entitásokat a következetesség
A más kulcsfontosságú tényező, amely befolyásolja a választott módosítása optimalizálási kulcsok, hogyan biztosíthatja a atomi tranzakciók használatával. Csak használhat egy EGT az entitásokat tartalmazó partícióra tárolt való működésre.  

A cikk a következő minták [táblázat kialakítási minta](table-storage-design-patterns.md) cím konzisztencia kezelése:  

* [Intra-partíció másodlagos index mintát](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -tárolja a több másolatot minden entitás használatával különböző **RowKey** engedélyezése gyors és hatékony keresések és másodlagos rendezési sorrend különböző használatával (a partícióra) értékek **RowKey** értékeket.  
* [Másodlagos helyek közötti partíció index mintát](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - értékekkel különböző RowKey külön partíciók vagy külön táblázatban gyors engedélyezése minden entitás több példányát tárolja, és a különböző rendelésekhatékonykereséstésamásodlagosrendezésisorrend**RowKey** értékeket.  
* [Idővel konzisztenssé tranzakciók mintát](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -engedélyezni a idővel konzisztenssé partícióhatárok vagy tárolási rendszer határok Azure üzenetsorok használatával.
* [Index entitások mintát](table-storage-design-patterns.md#index-entities-pattern) -index entitások entitások listájának visszaadó hatékony keresések engedélyezése karbantartása.  
* [Denormalization mintát](table-storage-design-patterns.md#denormalization-pattern) -egyesítése kapcsolódó adatok együtt az egyetlen entitás ahhoz, hogy beolvasni az összes adatot a hibaérzékeny pontok lekérdezéssel van szüksége.  
* [Adatsorozat adatmintát](table-storage-design-patterns.md#data-series-pattern) -tároló teljes adatsorok egyetlen entitás minimalizálása érdekében elvégezte kérelmek számát jelenti.  

Entitás csoport tranzakciókkal kapcsolatos információkért lásd: a szakasz [entitás csoport tranzakciók](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Győződjön meg arról, a módosítások hatékony kialakítása elősegíti a hatékony lekérdezések
Sok esetben egy tervezési módosítások hatékony, de hatékony lekérdező eredményez kell mindig mérlegelje, hogy ez a helyzet az adott forgatókönyv szerint. A cikk a minták némelyike [táblázat kialakítási minta](table-storage-design-patterns.md) explicit módon értékeli az kérdez le, és módosítja az entitások közötti kompromisszumot, és hogy mindig figyelembe kell vennie a művelet típusonkénti darabszámot.  

A cikk a következő minták [táblázat kialakítási minta](table-storage-design-patterns.md) hatékony lekérdezések tervezése és kialakítása hatékony adatok módosítása a közötti kompromisszumot cím:  

* [Összetett kulcs mintát](table-storage-design-patterns.md#compound-key-pattern) – használható összetett **RowKey** értékek kapcsolódó adatok egyetlen pont lekérdezéssel talált ügyfél engedélyezése.  
* [Napló végéről mintát](table-storage-design-patterns.md#log-tail-pattern) -beolvasni a *n* partíció legutóbb hozzáadott entitások egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  
