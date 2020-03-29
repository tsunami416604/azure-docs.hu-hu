---
title: Az Azure storage tábla tervezésének irányelvei | Microsoft dokumentumok
description: Tervezze meg az Azure table service-t az olvasási műveletek hatékony támogatásához.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: d056d29469ad9a60fceeee307aca3c0e1319283c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61269847"
---
# <a name="guidelines-for-table-design"></a>Irányelvek táblatervezéshez

Táblák tervezése az Azure storage table szolgáltatás használata nagyon eltér a relációs adatbázis tervezési szempontok. Ez a cikk ismerteti a table service megoldás írási és írási hatékonysági tervezésének irányelveit.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>A Table szolgáltatásmegoldás megtervezése olvasható

* ***Tervezze meg a lekérdezést a nehéz alkalmazásokban.*** A táblák tervezésekor gondolja át a lekérdezéseket (különösen a késés-érzékenyeket), amelyeket végrehajt, mielőtt átgondolná, hogyan fogja frissíteni az entitásokat. Ez általában hatékony és hatékony megoldást eredményez.  
* ***Adja meg a PartitionKey és a RowKey a lekérdezések.*** *Pont lekérdezések,* mint ezek a leghatékonyabb tábla szolgáltatás lekérdezések.  
* ***Fontolja meg az entitások ismétlődő másolatainak tárolását.*** A table storage olcsó, ezért fontolja meg ugyanazon entitás többszöri (különböző kulcsokkal) tárolását a hatékonyabb lekérdezések lehetővé tétele érdekében.  
* ***Fontolja meg az adatok denormalizálását.*** Asztali tárolás olcsó, ezért fontolja meg denormalizing az adatokat. Például tárolja az összesítő entitásokat, hogy az összesített adatok lekérdezéseinek csak egyetlen entitáshoz kell hozzáférnie.  
* ***Összetett kulcsértékek használata.*** Az egyetlen kulcs van **PartitionKey** és **RowKey**. Például használja az összetett kulcs értékeket az entitások másodlagos kulcsos elérési utak engedélyezéséhez.  
* ***Használja a lekérdezés-vetületet.*** A hálózaton keresztül átvihető adatok mennyiségét csökkentheti, ha olyan lekérdezéseket használ, amelyek csak a szükséges mezőket választják ki.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>A Table szolgáltatásmegoldás megtervezése írási hatékonysággal  

* ***Ne hozzon létre forró partíciókat.*** Válassza ki azokat a kulcsokat, amelyek lehetővé teszik a kérelmek több partíció közötti terjesztését bármikor.  
* ***Kerülje a forgalmi csúcsokat.*** Simítsa el a forgalmat ésszerű időn keresztül, és kerülje a forgalom csúcsait.
* ***Nem feltétlenül hozzon létre külön táblát az entitások minden típusához.*** Ha entitástípusok közötti atomi tranzakciókra van szüksége, ezeket a több entitástípust ugyanabban a partícióban, ugyanabban a táblában tárolhatja.
* ***Vegye figyelembe a maximális átviteli teljesítmény, amit el kell érnie.*** Tisztában kell lennie a Table szolgáltatás méretezhetőségi céljaival, és győződjön meg arról, hogy a terv nem fogja túlszárnyalni azokat.  

Az útmutató elolvasása közben olyan példákat fogtok látni, amelyek mindezeket a tantételeket átülik a gyakorlatba. 

## <a name="next-steps"></a>További lépések

- [Táblatervezési minták](table-storage-design-patterns.md)
- [Tervezés lekérdezéshez](table-storage-design-for-query.md)
- [Táblaadatok titkosítása](table-storage-design-encrypt-data.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
