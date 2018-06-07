---
title: Az Azure storage vonatkozó irányelvek táblázat kialakítási |} Microsoft Docs
description: Tervezze meg az Azure table szolgáltatás hatékonyan olvasási műveletek támogatásához.
services: storage
documentationcenter: na
author: SnehaGunda
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 5329d33aee1bb1a55e9982b1ba9e3e8329246980
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660898"
---
# <a name="guidelines-for-table-design"></a>Táblatervezés vonatkozó irányelvek

A tábla az Azure storage szolgáltatással való használatra táblák tervezése egy nagyon eltérő tervezéséhez kapcsolódó szempontokat tartalmazza egy relációs adatbázisban. Ez a cikk ismerteti a Table szolgáltatás megoldásainak hatékony lehet olvasni és írni hatékony irányelveket.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>A Table szolgáltatás megoldást a olvasási hatékony

* ***Tervezze meg az olvasási műveleteket alkalmazásokban lekérdezése.*** A táblák tervezésekor gondolja át a lekérdezések (különösen a várakozási bizalmas is), amely, végrehajtja a véleménye hogyan frissíti az entitások előtt. Ez általában annak az eredménye a hatékony és performant megoldás.  
* ***Adja meg a lekérdezések PartitionKey és RowKey is.*** *Mutasson a lekérdezések* például ezek azok a leghatékonyabb tábla szolgáltatás lekérdezéseket.  
* ***Érdemes tárolni a duplikált entitásokat.*** A TABLE storage olcsó, érdemes a ugyanaz az entitás tárolni többször (különböző kulccsal rendelkező) a hatékonyabb lekérdezések engedélyezéséhez.  
* ***Vegye figyelembe az adatok denormalizing.*** A TABLE storage olcsó ezért fontolja meg az adatok denormalizing. Összegző entitások például tárolja, hogy az összesített adatok lekérdezések csak egyetlen entitáshoz eléréséhez szükséges.  
* ***Használja az összetett kulcs értékeket.*** A csak akkor kulcsokban **PartitionKey** és **RowKey**. Például összetett kulcsértékei használatával engedélyezze a másodlagos kulccsal elérési utakat a részére.  
* ***Lekérdezés vetítéshez használni.*** Válassza ki a szükséges mezők lekérdezésekkel a hálózati átvitel adatmennyiség csökkentése érdekében.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>A Table szolgáltatás megoldást a írási hatékony  

* ***Ne hozzon létre a gyakran használt adatok partíciókat.*** Válassza ki a kulcsokat, amelyek lehetővé teszik, hogy a kérelmek elosztva idő minden helyen több partíciót.  
* ***Kerülje a forgalmat a teljesítményt.*** Elfogadható időn keresztül a forgalom sima, és elkerülheti a forgalmat a teljesítményt.
* ***Feltétlenül ne hozzon létre egy külön táblázat az egyes entitás.*** Ha atomi tranzakciók entitástípusok között, több entitás típusaival tárolhat partícióra ugyanabban a táblában.
* ***Fontolja meg a maximális átviteli sebesség kell elérni.*** Kell figyelembe vennie a méretezhetőségi célok a Table szolgáltatás, és győződjön meg arról, hogy a tervező nem okoz Önnek, hogy azokat.  

Ez az útmutató olvasás példák, amelyek a gyakorlatban az összes alapelvek jelenik meg. 

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Tervezési lekérdezése](table-storage-design-for-query.md)
- [Tábla adatok titkosítása](table-storage-design-encrypt-data.md)
- [Adatmódosítás kialakítása](table-storage-design-for-modification.md)