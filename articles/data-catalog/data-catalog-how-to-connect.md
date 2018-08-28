---
title: Hogyan lehet csatlakozni az Azure Data Catalog az adatforrásokat
description: Útmutató olyan cikk, kiemelve azt, hogyan csatlakozhat az Azure Data Cataloggal felfedezett adatforráshoz.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 4e6b27a5-cf75-4012-b88c-333c1fe638e8
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 759758c9f3d0f1dadf2048e8ef15eeab8a77ef07
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053525"
---
# <a name="how-to-connect-to-data-sources"></a>Kapcsolódás az adatforrásokhoz
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely egy regisztrációs és felderítőrendszert biztosít a vállalati adatforrások. Más szóval **Azure Data Catalog** lényege személyek felderítése megértésében, valamint használhatják az adatforrásokat, és segíti a szervezetek számára, hogy a meglévő adatok kihasználása. Ebben a forgatókönyvben alkalmazástervezésnél kulcsfontosságú használ az adatok – Miután a felhasználó felderíti az adatforráshoz, és tisztában van azzal a céllal, a következő lépés-e tenni az adatokat, az adatforráshoz csatlakozni.

## <a name="data-source-locations"></a>Adatforrás helye
Az adatforrás regisztrációja során **Azure Data Catalog** megkapja az adatforrás metaadatait. A metaadatok részletezi az adatforrás helyét. A hely adatait adatforráshoz adatforrásból változó, de azt mindig tartalmazza a szükséges adatokat. Például a hely az SQL Server-táblát tartalmaz a kiszolgáló nevét, a adatbázis neve, a séma neve és a táblázat neve, bár a hely SQL Server Reporting Services jelentés tartalmazza a kiszolgáló nevét és a jelentés elérési útját. Más adatforrástípusok lesz a helyek, amely a struktúra és a forrásrendszerben képességeit mutatja.

## <a name="integrated-client-tools"></a>Integrált ügyféleszközökkel
A legegyszerűbb csatlakozik egy adatforráshoz, hogy használja a "Megnyitás a..." a menüben a **Azure Data Catalog** portálon. Ebből a menüből a kiválasztott adategységet való kapcsolódás listáját jeleníti meg.
Az alapértelmezett zobrazení tile használatakor az ebben a menüben érhető el az egyes csempéken.

 ![Egy SQL Server-táblát megnyitása az Excelben az adatok eszköz csempéjéről](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Ha a listanézetet használja, a menüben a portál ablakának tetején a keresősávba érhető el.

 ![A keresősávban lévő a jelentéskezelőben egy SQL Server Reporting Services jelentés megnyitása](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Támogatott ügyfélalkalmazások
Ha a "Megnyitás a..." menüjében adatforrások az Azure Data Catalog-portál, a megfelelő ügyfélalkalmazást a az ügyfélszámítógépen telepítve kell lennie.

| Nyissa meg az alkalmazás | Fájlkiterjesztés / protokoll | Támogatott alkalmazások |
| --- | --- | --- |
| Excel |.odc |Az Excel 2010 vagy újabb |
| Excel (első 1000) |.odc |Az Excel 2010 vagy újabb |
| Power Query |.xlsx-fájlba |Az Excel 2016 vagy Excel 2010 vagy a Power Query az Excel-bővítmény az Excel 2013 telepítve van |
| A Power BI Desktopban |.pbix-fájl |A Power BI Desktop júliusi 2016 vagy újabb |
| SQL Server Data Tools |vsweb:// |A Visual Studio 2013 Update 4 vagy újabb verzió telepítve van az SQL Server-eszközök |
| Jelentéskezelő |http:// |Lásd: [SQL Server Reporting Services böngészőkövetelményei](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Az adatok, az eszközök
A menüben elérhető lehetőségek jelenleg kiválasztott adategységet típusától függ. Természetesen nem minden lehetséges eszközök fog szerepelni a "Megnyitás a..." menü,, de továbbra is egyszerűen, az adatforrás minden olyan ügyfél eszközzel csatlakozhat. Amikor egy adategység van kiválasztva, az a **Azure Data Catalog** portál, a teljes helyen jelenik meg a Tulajdonságok panelen.

 ![Csatlakozási információkat egy SQL Server-tábla](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

A kapcsolati információ részletek különbözni fog adatforrás típusa, adatforrás típusa, de a portálon található információk kap minden, amit az adatforrás minden ügyféleszközben való kapcsolódáshoz. Felhasználók átmásolhatnak azok felderített használatával adatforrások kapcsolati adatait **Azure Data Catalog**, így lehetővé válik az adatok használata a kívánt eszközben dolgozhat.

## <a name="connecting-and-data-source-permissions"></a>Csatlakozás és adatok az adatforrás-engedélyek
Bár a **Azure Data Catalog** lehetővé teszi az adatforrások felderíthető, hozzáférés az adatokhoz maga marad az adatforrás tulajdonosa vagy a rendszergazda felügyelete alá tartozik. Az adatforrás felderítéséhez **Azure Data Catalog** nem felhasználói jogosultságok megadása az bármely magát az adatforrás eléréséhez.

Hogy egyszerűbb legyen a felhasználók számára, akik adatforrásra, de nincs engedélye az adatokhoz való hozzáférésre, felhasználókat lehet szolgáltatni a hozzáférés kérése tulajdonságban adatforrások ellátása megjegyzésekkel. – Beleértve a folyamatra vagy az adatforrás-hozzáférés a kapcsolódási pont hivatkozások – az itt megadott információk mellett az adatforrás helye adatait a portálon jelennek meg.

 ![A kérelem hozzáférési utasításait kapcsolatadatok](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Összegzés
Az adatforrás regisztrálása **Azure Data Catalog** lehetővé teszi az adatok felderíthető szerkezeti és leíró metaadatokat másolja az adatforrás a Catalog szolgáltatásba. Miután regisztrált egy adatforrást, és felderítése, a felhasználók kapcsolódhatnak az adatforrást a **Azure Data Catalog** portál "Megnyitás a..." " menü vagy tetszőleges eszközökkel az adatok.

## <a name="see-also"></a>Lásd még
* [Ismerkedés az Azure Data Catalog](data-catalog-get-started.md) oktatóanyag az adatforrásokhoz való csatlakozás részletes adatait.
