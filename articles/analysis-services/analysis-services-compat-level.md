---
title: Adatok modell kompatibilitási szint az Azure Analysis Services |} Microsoft Docs
description: Táblázatos modell kompatibilitási szintnek ismertetése.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d68d544a66448fbbf193ff53fa43e179b1edb706
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602067"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Az Analysis Services rendszerbeli táblázatos modellek kompatibilitási szintjének

*Kompatibilitási szint* az Analysis Services motor a kiadási-specifikus viselkedések hivatkozik. A kompatibilitási szint módosítása általában egybe az SQL Server kiadások. Ezeket a módosításokat is valósíthatók meg az Azure Analysis Services mindkét platformok közötti paritás fenntartása érdekében. Kompatibilitási szintje változik is hatása a szolgáltatásainak a táblázatos modellek. Például DirectQuery és az objektum táblázatos metaadatok rendelkezik különböző megvalósítások attól függően, hogy a kompatibilitási szintet. A táblázatos modell projektet a Visual Studio (SSDT) kompatibilitási szintje van megadva. Táblázatos modellek létrehozása és importálása a Power BI Desktop vannak, csak az 1 400 kompatibilitási szinten.

Az Azure Analysis Services támogatja a 1400 és 1200-as kompatibilitási szintű táblázatos modellek. 

A legfrissebb kompatibilitási szintet a 1 400. Ez a szint az SQL Server 2017 Analysis Services megegyezik. Az 1 400-kompatibilitási szint a főbb funkciók a következők:

*  Az új szolgáltatásai adatkapcsolattal és importálás TOM API-k és TMSL parancsfájlkezelés támogatása. 
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
