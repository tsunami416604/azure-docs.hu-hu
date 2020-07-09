---
title: Útmutató az Azure Storage Table designhoz | Microsoft Docs
description: Tervezze meg az Azure Table Service-t az olvasási műveletek hatékony támogatásához.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "61269847"
---
# <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez

Az Azure Storage Table szolgáltatással való használatra szolgáló táblák tervezése nagyon különbözik a kapcsolódó adatbázisok kialakítási szempontjaitól. Ez a cikk útmutatást nyújt a Table service megoldás hatékony és írási eredményének megtervezéséhez.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Tervezze meg Table service-megoldását olvasásra és hatékonyságra

* ***Az olvasási és a nagy teljesítményű alkalmazásokban való lekérdezés tervezése.*** A táblázatok tervezésekor gondolja át a lekérdezéseket (különösen a késési különbségeket), amelyeket a rendszer az entitások frissítésének megkezdése előtt végrehajt. Ez általában egy hatékony és nagy teljesítményű megoldás eredményét eredményezi.  
* ***Adja meg a PartitionKey és a RowKey is a lekérdezésekben.*** A *pontok lekérdezései* , például ezek a leghatékonyabb Table Service-lekérdezések.  
* ***Érdemes lehet az entitások duplikált példányait tárolni.*** A Table Storage olcsó, ezért a hatékonyabb lekérdezések lehetővé tételéhez ugyanazt az entitást többször kell tárolni (különböző kulcsokkal).  
* ***Érdemes lehet az adatai denormalizálása.*** A Table Storage olcsó, ezért érdemes lehet az adatai denormalizálása. Tárolhatja például az összegző entitásokat, hogy az összesített adatlekérdezések csak egyetlen entitáshoz férhessenek hozzá.  
* ***Használjon összetett kulcsos értékeket.*** Csak a **PartitionKey** és a **RowKey**kulcsok vannak. Használjon például összetett kulcsos értékeket az entitásokhoz való helyettesítő hozzáférési útvonalak engedélyezéséhez.  
* ***Lekérdezési leképezés használata.*** Csökkentheti a hálózaton keresztül átvitt adatok mennyiségét olyan lekérdezések használatával, amelyek csak a szükséges mezőket választják ki.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Tervezze meg Table service-megoldását, hogy az írható legyen  

* ***Ne hozzon létre forró partíciót.*** Válassza ki azokat a kulcsokat, amelyek lehetővé teszik a kérések több partíción keresztüli terjesztését egy adott időpontban.  
* ***Kerülje a forgalomban lévő tüskéket.*** A forgalmat egy ésszerű időn belül zökkenőmentesen, és elkerülje a forgalomban fellépő tüskéket.
* ***Ne feltétlenül hozzon létre külön táblát az egyes entitások típusaihoz.*** Ha az entitások típusai között atomi tranzakciókat igényel, akkor a több entitás típusát ugyanabban a partícióban tárolhatja ugyanabban a táblában.
* ***Gondolja át, hogy mekkora átviteli sebességet kell elérnie.*** Tisztában kell lennie a Table service skálázhatósági céljaival, és gondoskodnia kell arról, hogy a kialakítás ne lépje túl őket.  

Az útmutató elolvasása során olyan példákat talál, amelyek az összes alapelvet bemutatják a gyakorlatban. 

## <a name="next-steps"></a>További lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Tábla adatai titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
