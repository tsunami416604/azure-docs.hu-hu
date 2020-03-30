---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 9bc5be37f3892186233fac197c08066dbfacb43f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77780599"
---
Most már használhatja a Data Explorer eszközt az Azure Portalon egy adatbázis és tároló létrehozásához. 

1. Válassza az **Adatkezelő** > **új tárolóját**. 
    
    A **Tároló hozzáadása** terület a jobb szélen jelenik meg, előfordulhat, hogy jobbra kell görgetnie, hogy láthassa.

    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. A **Tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis azonosítója**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázisneveknek 1 és 255 karakter `/, \\, #, ?`között kell lennie, és nem tartalmazhatnak , illetve záró szóközt. Ellenőrizze a **létesítési adatbázis átviteli beállítás,** lehetővé teszi, hogy megosszák az adatbázis számára kiosztott átviteli átmenő az adatbázis összes tárolója között. Ez az opció is segít a költségmegtakarítást. |
    |**Teljesítmény**|400|Hagyja az átviteli értéket 400 kérelemegység/másodperc (RU/s) értéken. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Írja be *az elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* kapcsolót használja partíciókulcsként.|
    
    Az előző beállításokon kívül szükség esetén **egyedi kulcsokat** is hozzáadhat a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy tároló létrehozása közben létrehoz egy egyedi kulcsházirendet, biztosítja partíciókulcsonként egy vagy több érték egyediségét. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](../articles/cosmos-db/unique-keys.md) kapcsolatos cikkben talál.
    
    Válassza **az OK gombot.** Az Adatkezelő megjeleníti az új adatbázist és tárolót.