---
title: Adatforrásokhoz való csatlakozás az Azure Data Catalogban
description: Útmutató cikk, amely kiemeli, hogyan lehet csatlakozni az Azure Data Catalog által felderített adatforrásokhoz.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976773"
---
# <a name="how-to-connect-to-data-sources"></a>Kapcsolódás az adatforrásokhoz
## <a name="introduction"></a>Bevezetés
**A Microsoft Azure Data Catalog** egy teljes körűen felügyelt felhőszolgáltatás, amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. Más szóval az **Azure Data Catalog** arról szól, hogy segítsen az embereknek az adatforrások felderítésében, megértésében és használatában, és segítse a szervezeteket abban, hogy nagyobb értéket kapjanak a meglévő adataikból. Ennek a forgatókönyvnek az egyik fő szempontja az adatok használata – amint a felhasználó felderíti az adatforrást, és megérti annak célját, a következő lépés az adatforráshoz való csatlakozás az adatok felhasználásához.

## <a name="data-source-locations"></a>Adatforrás-helyek
Az adatforrás-regisztráció során **az Azure Data Catalog** metaadatokat kap az adatforrásról. Ez a metaadat tartalmazza az adatforrás helyének részleteit. A hely részletei adatforrásról adatforrásra változnak, de mindig a csatlakozáshoz szükséges információkat tartalmazzák. Egy SQL Server tábla helye például tartalmazza a kiszolgáló nevét, az adatbázis nevét, a sémanevét és a táblanevét, míg az SQL Server Reporting Services jelentés helye tartalmazza a kiszolgáló nevét és a jelentés elérési útját. Más adatforrástípusok olyan helyekkel rendelkeznek, amelyek tükrözik a forrásrendszer szerkezetét és képességeit.

## <a name="integrated-client-tools"></a>Integrált ügyféleszközök
Az adatforráshoz való csatlakozás legegyszerűbb módja a "Megnyitás..." az **Azure Data Catalog** portálon. Ez a menü a kijelölt adateszközhöz való csatlakozás lehetőségeinek listáját jeleníti meg.
Az alapértelmezett csempenézet használatakor ez a menü minden csempén elérhető.

 ![SQL Server-tábla megnyitása az Excelben az adategység csempéjéről](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

A listanézet használatakor a menü a portálablak tetején lévő keresősávban érhető el.

 ![SQL Server Reporting Services jelentés megnyitása a Jelentéskezelőben](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Támogatott ügyfélalkalmazások
A "Megnyitás a..." az Azure Data Catalog portálon található adatforrások menüjében a megfelelő ügyfélalkalmazást kell telepíteni az ügyfélszámítógépre.

| Megnyitás az alkalmazásban | Fájlkiterjesztés / protokoll | Támogatott alkalmazásverziók |
| --- | --- | --- |
| Excel |.odc |Excel 2010 vagy újabb verzió |
| Excel (1000 legnépszerűbb) |.odc |Excel 2010 vagy újabb verzió |
| Power Query |.xlsx |Excel 2016, Excel 2010 vagy Excel 2013 az Excel Hez Power Query bővítmény telepítésével |
| Power BI Desktop |.pbix |2016 júliusában vagy később a Power BI Desktop |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 4- es vagy újabb frissítése az SQL Server eszköztelepítésével |
| Jelentéskezelő |http:// |Lásd: [az SQL Server Reporting Services böngészőkövetelményei](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Az ön adatai, eszközei
A menüben elérhető beállítások az aktuálisan kiválasztott adateszköz típusától függenek. Természetesen nem minden lehetséges eszköz kerül be a "Megnyitás..." menüben, de még mindig könnyen csatlakozhat az adatforráshoz bármilyen ügyféleszközzel. Ha egy adategység van kiválasztva az **Azure Data Catalog** portálon, a teljes hely jelenik meg a tulajdonságok ablaktáblán.

 ![SQL Server tábla kapcsolatadatai](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

A kapcsolatadatainak részletei adatforrástípustól adatforrástípusonként eltérőek lesznek, de a portálon található információk mindent megadnak, amire az adatforráshoz való csatlakozáshoz bármely ügyféleszközben szükség lehet. A felhasználók átmásolhatják az **általuk**felderített adatforrások kapcsolatrészleteit az Azure Data Catalog használatával, lehetővé téve számukra, hogy a választott eszközükben működjenek az adatokkal.

## <a name="connecting-and-data-source-permissions"></a>Csatlakozási és adatforrás-engedélyek
Bár **az Azure Data Catalog** felderíthetővé teszi az adatforrásokat, az adatokhoz való hozzáférés továbbra is az adatforrás tulajdonosa vagy rendszergazdája ellenőrzése alatt marad. Az **Azure Data Catalog** adatforrásának felderítése nem ad engedélyt a felhasználónak az adatforrás hoz való hozzáféréshez.

Annak érdekében, hogy azok a felhasználók könnyebben felismerjenek egy adatforrást, akik nem rendelkeznek hozzáféréssel az adataihoz, az adatforrás jegyzetelésekor a hozzáférés kérése tulajdonságban adhatnak meg információkat. Az itt megadott információk – beleértve az adatforrás-hozzáféréshez szükséges folyamatra vagy kapcsolattartó pontra mutató hivatkozásokat – a portálon található adatforrás helyadatai mellett jelennek meg.

 ![Csatlakozási információk a kéréshez való hozzáférésre vonatkozó utasításokkal](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Összefoglalás
Adatforrás regisztrálása az **Azure Data Catalog** teszi, hogy az adatok felderíthető másolásával szerkezeti és leíró metaadatok az adatforrásból a katalógus szolgáltatásba. Az adatforrás regisztrálása és felderítése után a felhasználók csatlakozhatnak az adatforráshoz az **"Open** in..." azure data catalog portálról. menüben, vagy a választott adateszközeikkel.

## <a name="see-also"></a>Lásd még
* [Az Azure Data Catalog oktatóanyagának első lépései](data-catalog-get-started.md) az adatforrásokhoz való csatlakozás részletes részleteivel.
