---
title: Adatok modell kompatibilitási szint az Azure Analysis Services |} Microsoft Docs
description: Táblázatos modell kompatibilitási szintnek ismertetése.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 231a7ea42560aac3fba0ccf27e27123320f219a3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Az Analysis Services rendszerbeli táblázatos modellek kompatibilitási szintjének

*Kompatibilitási szint* az Analysis Services motor a kiadási-specifikus viselkedések hivatkozik. A kompatibilitási szint módosítása általában egybe az SQL Server kiadások. Ezeket a módosításokat is valósíthatók meg az Azure Analysis Services mindkét platformok közötti paritás fenntartása érdekében. Kompatibilitási szint módosítások is érintik a táblázatos modellek szolgáltatásához. Például DirectQuery és az objektum táblázatos metaadatok rendelkezik különböző megvalósítások attól függően, hogy a kompatibilitási szintet. 

Az Azure Analysis Services támogatja a 1400 és 1200-as kompatibilitási szintű táblázatos modellek.

A legfrissebb kompatibilitási szintet a 1 400. Ez a szint az SQL Server 2017 Analysis Services megegyezik. Az 1 400-kompatibilitási szint a főbb funkciók a következők:

*  Új infrastruktúra adatkapcsolat és a táblázatos modellek történő TOM API-k és TMSL parancsfájlkezelés támogatása. Ezen új szolgáltatás lehetővé teszi, hogy a további adatforrások, például az Azure Blob-tároló támogatása.
*  Adatok átalakítása és adatok adategyesítési képességeket adatok beolvasása és M kifejezések használatával.
*  Mértékek egy DAX-kifejezés az adott sorok tulajdonsághoz támogatja. Ez a tulajdonság lehetővé teszi, hogy az ügyfél eszközök, például a Microsoft Excel fúrjon le a program egy összesített jelentés részletes adatait. Például ha a felhasználó egy régiót, és a hónap összes értékesítés, akkor megtekintheti a társított rendelés részleteit. 
*  A tábla és oszlop neve mellett található adatok biztonsági objektumszintű.
*  Továbbfejlesztett támogatása az egyenetlen szélű hierarchiákat.
*  Teljesítmény- és továbbfejlesztések.
  
## <a name="set-compatibility-level"></a>Kompatibilitási szint beállítása 
 Amikor létrehoz egy új táblázatos modell projektet SSDT, megadhatja a kompatibilitási szintje a **táblázatos modellek tervezőjében** párbeszédpanel. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Ha bejelöli a **ne jelenjen meg többé ez az üzenet** lehetőség, minden ezt követő projektek használata a megadott alapértelmezett kompatibilitási szintet. Az SSDT-ben az alapértelmezett kompatibilitási szint **eszközök** > **beállítások**.  
  
 Frissíti a meglévő táblázatos modell projekt SSDT-ben, adja meg a **kompatibilitási szint** tulajdonság a modell **tulajdonságok** ablak. A szem előtt tartani, a kompatibilitási szint frissítése nem vonható vissza.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Az SQL Server Management Studio táblázatos modell adatbázis kompatibilitási szintjének ellenőrzése 
 Az SSMS, kattintson a jobb gombbal az adatbázis neve > **tulajdonságok** > **kompatibilitási szint**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Támogatott kompatibilitási szint szolgáltatáshoz az ssms kiszolgáló  
 Az SSMS, kattintson a jobb gombbal a kiszolgáló neve > **tulajdonságok** > **támogatott kompatibilitási szint**.  
  
 Ez a tulajdonság meghatározza a legmagasabb szintű kompatibilitási egy adatbázist, amely a kiszolgálón (kivéve az előzetes verzió) fog futni. A támogatott kompatibilitási szintje nem módosítható.  

## <a name="next-steps"></a>További lépések
  [A modell létrehozása Azure-portálon](analysis-services-create-model-portal.md)   
  [Analysis Services kezelése](analysis-services-manage.md)  
