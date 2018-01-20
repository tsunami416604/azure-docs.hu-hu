---
title: "Adatforrások csatlakoztatása |} Microsoft Docs"
description: "Útmutató a cikk kiemelés az Azure Data Catalog felderített adatforrásokhoz való csatlakozás."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: b5bed534d9fa1a64b0e90c268407281724185ce8
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-connect-to-data-sources"></a>Kapcsolódás az adatforrásokhoz
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a regisztráció és a rendszer a vállalati adatforrások felderítési funkcionál. Más szóval **Azure Data Catalog** minden útmutatás nyújtása a felhasználók felderítése, megismeréséhez és használatához adatforrások, és segíti a szervezeteket kihasználása érdekében a meglévő adatok körül forog. A legfőbb szempontja, hogy ez a forgatókönyv használ az adatok – Miután a felhasználó felderíti az adatforrást, és együttműködik a célja, a következő lépésre, amelyre az adatokat az adatforráshoz való kapcsolódáshoz.

## <a name="data-source-locations"></a>Adatforrás helye
Az adatforrás regisztrációja során **Azure Data Catalog** megkapja az adatforrás metaadatait. A metaadatok részletezi az adatforrás helyét. A hely részleteit adatforráshoz adatforrásból változó, de mindig a csatlakozáshoz szükséges információt tartalmaz. Például egy SQL Server tábla helyét tartalmazza a kiszolgáló neve, az adatbázis neve, a sémanév és a táblanév, miközben egy SQL Server Reporting Services jelentés helyét tartalmazza, a kiszolgáló nevét és a jelentés elérési útját. Más adatforrásokat kell a struktúra és a forrásrendszer képességek megfelelően helyeket.

## <a name="integrated-client-tools"></a>Integrált ügyféleszközök
A legegyszerűbb módja egy adatforráshoz való kapcsolódáshoz a "Megnyitás..." a menü a **Azure Data Catalog** portálon. Ebben a menüben a kiválasztott adategységet történő kapcsolódáshoz beállítások listáját jeleníti meg.
Ha az alapértelmezett mozaik elrendezés nézetben, az egyes mozaikokon ebben a menüben érhető el.

 ![SQL Server tábla megnyitása az Excel adatok eszköz csempén](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Amikor a listanézet használatával, a keresési sávon a portál ablakának tetején a menü érhető el.

 ![A keresősáv származó a jelentéskezelőben egy SQL Server Reporting Services jelentés megnyitása](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Ügyfelek támogatott alkalmazások
Használata esetén a "Megnyitás..." az Azure Data Catalog-portált, a megfelelő ügyfél-alkalmazás adatforrásához menü telepítenie kell az ügyfélszámítógépen.

| Nyissa meg az alkalmazás | Kiterjesztése / protokoll | Alkalmazás támogatott verziói |
| --- | --- | --- |
| Excel |.odc |Az Excel 2010-es vagy újabb |
| Excel (első 1000) |.odc |Az Excel 2010-es vagy újabb |
| Power Query |.xlsx |Excel 2016 vagy az Excel 2010 vagy az Excel 2013 a Power Query az Excel-bővítmény telepítése |
| A Power BI Desktop |.pbix |A Power BI Desktop július 2016 vagy újabb |
| SQL Server Data Tools |vsweb:// |A Visual Studio 2013 Update 4 vagy újabb rendszert telepített SQL Server tooling |
| Jelentéskezelő |http:// |Lásd: [SQL Server Reporting Services böngészőre vonatkozó követelményei](https://technet.microsoft.com/en-us/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Az adatok, az eszközök
A menüben elérhető beállítások a jelenleg kiválasztott adategységet típusú függ. Természetesen nem minden lehetséges eszközök fog szerepelni a "Megnyitás..." menü, de még könnyen bármely ügyfél eszközt használó adatforráshoz való kapcsolódáshoz. Amikor egy adategységet kiválasztásakor a **Azure Data Catalog** portál, a teljes helyen jelenik meg a Tulajdonságok panelen.

 ![Egy SQL Server tábla-kapcsolódási információt](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

A kapcsolat adatai az adatforrás típusa az adatforrás típusa eltérőek, de a portál adatok erre azért van szükség minden az adatforrás minden ügyféleszközben való kapcsolódáshoz szükséges. Felhasználók használatával észlelt azok az adatforrások kapcsolati adatainak másolhatja **Azure Data Catalog**, amelyek lehetővé teszik az adatok kezeléséhez a tetszőleges eszköz általi.

## <a name="connecting-and-data-source-permissions"></a>Csatlakozás és az adatforrás-engedélyek
Bár a **Azure Data Catalog** adatforrások felderíthető, access lesz az adatok maga marad a adatok forrás tulajdonosa vagy a rendszergazda felügyelete alatt. Az adatforrás felderítéséhez **Azure Data Catalog** nem felhasználói jogosultságok megadása az összes magát az adatforrás elérésére.

Megkönnyítik a felhasználók számára az adatforrás felderítéséhez, de nincs engedélye az adatokhoz való hozzáférésre, felhasználók is adja meg a hozzáférés kérése tulajdonságban amikor ellátása megjegyzésekkel adatforrás. – Beleértve a folyamat vagy az adatok elérése való kapcsolódási pontot hivatkozások – az itt megadott információk mellett a Tartózkodásihely-adatok a portálon jelenik meg.

 ![A kérés hozzáférési utasításokat kapcsolatadatok](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Összegzés
Adatforrás regisztrálása **Azure Data Catalog** lehetővé teszi, hogy az adatok felderíthető szerkezeti és leíró metaadatok másolja az adatforrás a Catalog szolgáltatásba. Miután regisztrált egy adatforrást, és felderített, a felhasználók kapcsolódhatnak az adatforrást a **Azure Data Catalog** portal "Megnyitás..." " menü vagy a kiválasztott data tools használatával.

## <a name="see-also"></a>Lásd még
* [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóprogram lépésről lépésre vonatkozó további információért adatforrásokhoz való csatlakozás.
