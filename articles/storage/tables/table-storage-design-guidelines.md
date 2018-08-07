---
title: Útmutató az Azure storage table tervezési |} A Microsoft Docs
description: Tervezze meg az Azure table service hatékonyan olvasási műveletek támogatásához.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: ef6d257aee532d4b6325bd3d2f619fd00824e06f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525418"
---
# <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez

Az Azure storage-table service való használatra táblák tervezése nagyon eltérőek a relációs adatbázis megtervezésével. Ez a cikk ismerteti a Table service megoldás hatékony kell olvasása és írása hatékonyan tervezése irányelveit.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>A Table service megoldás való olvasás hatékony megtervezése

* ***A lekérdezés a olvasási alkalmazások tervezése.*** A táblák tervezésekor gondolja át a lekérdezések (különösen a késés bizalmas azokat), amely végrehajtja a, mielőtt úgy gondolja, hogy hogyan fogja frissíteni az entitások kapcsolatban. Ez általában egy hatékony és nagy teljesítményű megoldást eredményez.  
* ***Adja meg a PartitionKey és rowkey tulajdonságok esetén is a lekérdezést.*** *Lekérdezések pont* például ezek a table service a leghatékonyabb lekérdezések.  
* ***Érdemes tárolni a duplikált entitást.*** A TABLE storage olcsó tehát érdemes a ugyanahhoz az entitáshoz tárolni többször (különböző kulccsal rendelkező) hatékonyabb lekérdezések engedélyezéséhez.  
* ***Érdemes lehet denormalizálni az adatokat.*** A TABLE storage olcsó tehát érdemes lehet denormalizálni az adatokat. Tárolhatja például összefoglaló entitások, hogy az összesített adatok lekérdezések csak egyetlen entitás eléréséhez szükséges.  
* ***Összetett kulcs értéket használja.*** A rendelkezésére csak kulcsokat a **PartitionKey** és **RowKey**. Például összetett kulcs értékeit használatával engedélyezi a másodlagos kulcsalgoritmus elérési útvonalakat entitásokhoz.  
* ***Lekérdezés leképezése használja.*** Csökkentheti a lekérdezések, amelyek csak a szükséges mezők kiválasztása a hálózati átvitel adatok mennyisége.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>A Table service megoldás kell írási hatékony megtervezése  

* ***Ne hozzon létre forró partíciók.*** Válassza ki a kulcsokat, amelyek lehetővé teszik a kérelmek helyezkednek el, bármikor több partíciót.  
* ***Adatforgalmi kiugrások elkerülése érdekében.*** A forgalom Smooth ésszerű időn át, és elkerülheti a csúcsterhelésekkel.
* ***Feltétlenül ne hozzon létre egy különálló táblában az egyes entitás.*** Ha elemi tranzakciókat entitástípusok között, tárolhatók ezek több entitás ugyanazon a partíción ugyanabban a táblában.
* ***Fontolja meg a maximális átviteli sebesség, amelyeket el kell érnie.*** Vegye figyelembe a Table service skálázási célértékei kell, és győződjön meg arról, hogy a tervező nem okoz túllépi őket.  

Az útmutató, mivel látni fogja a példa, amelyek a gyakorlatba összes ezeket az alapelveket. 

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Lekérdezés tervezése](table-storage-design-for-query.md)
- [Táblák adatainak titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás tervezése](table-storage-design-for-modification.md)