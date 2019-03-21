---
title: Oktatóanyag – Az Azure Cost Managementből exportált adatok létrehozása és kezelése | Microsoft Docs
description: Ez a cikk bemutatja, hogyan, hozhat létre és kezelhet az Azure Cost Management exportált adatok, hogy a külső rendszerek használhatja azt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a0b50b86ca164199ca723354e39e194c6cd7423f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014126"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Oktatóanyag: Hozzon létre, és az exportált adatok kezelése

Ha elolvasta a Költségelemzés című oktatóanyagot, akkor ismeri a Cost Management-adatok manuális letöltésének módját. Az ismétlődő feladatokat, amelyek automatikusan exportálja a Cost Management adataihoz az Azure storage-napi, heti vagy havi időközönként is létrehozhat. Az exportált adatok CSV formátumúak, és a Cost Management által gyűjtött összes adatot tartalmazzák. Ezt követően az Azure Storage-beli exportált adatokat külső rendszerekkel is használhatja, illetve saját egyéni adataival kombinálhatja őket. Az exportált adatokat külső rendszerben, például egy irányítópulton vagy egyéb pénzügyi rendszerben is használhatja.

Az oktatóanyagban szereplő példák bemutatják, hogyan exportálhatja a Cost Management-adatokat, majd hogyan ellenőrizheti, hogy az adatok exportálása sikeres volt-e.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek
Adatok exportálása érhető el az Azure-fiók típusú, beleértve a különböző [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ügyfelek. A támogatott típusok teljes listáját, tekintse meg [megismerheti a Cost Management adataihoz](understand-cost-mgt-data.md). A következő Azure-engedélyeket, vagy a hatókör, az adatok exportálásához előfizetésenként által támogatott felhasználók és csoportok. Hatókörök kapcsolatos további információkért lásd: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

- Tulajdonos – Ütemezett exportálási feladatokat hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő – Létrehozhatja, módosíthatja vagy törölheti saját ütemezett exportálási feladatait. Módosíthatja a mások által létrehozott ütemezett exportálási feladatok nevét.
- Olvasó – Ütemezheti azokat az exportálási feladatokat, amelyekhez engedéllyel rendelkezik.

Azure Storage-fiókok esetén:
- A konfigurált tárfiók módosításához írási engedélyekre van szükség függetlenül attól, hogy az exportálásra milyen engedélyek vonatkoznak.
- Az Azure Storage-fiókot blob- vagy fájltároláshoz kell konfigurálni.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="create-a-daily-export"></a>Napi rendszerességű exportálás létrehozása

Létrehozásához vagy egy adatexportálás megtekintéséhez vagy -exportálás ütemezése, nyissa meg a kívánt hatókörhöz az Azure Portalon, és válassza a **költségelemzés** menüjében. Például keresse meg **előfizetések**, és válasszon ki egy előfizetést a listából, majd válassza ki **költségelemzés** menüjében. A Cost analysis lap tetején kattintson **exportálása** majd válassza az exportálási beállítás. Kattintson például **exportálási ütemezése**. Hatókörök kapcsolatos további információkért lásd: [megismerése és együttműködnek a hatókörök](understand-work-scopes.md).

Kattintson a **Hozzáadás**, adjon meg egy nevet az exportálás, és válassza a **hónap elejétől számított költségek napi exportálási** lehetőséget. Kattintson a **tovább**.

![Exportálás típust megjelenítő új exportálási példa](./media/tutorial-export-acm-data/basics_exports.png)

Adja meg az Azure storage-fiók előfizetést, majd válassza ki a tárfiókját.  Adja meg a storage-tárolót és a könyvtár elérési útja, adja meg az exportfájl ugorhat.  Kattintson a **Tovább**gombra.

![Tárfiók részleteit megjelenítő új exportálási példa](./media/tutorial-export-acm-data/storage_exports.png)

Az Exportálás részleteiért tekintse át, és kattintson a **létrehozás**.

Az új exportálási feladat megjelenik az exportálási feladatok listájában. Új exportálási alapértelmezés szerint engedélyezve vannak. Ha szeretne letiltani vagy törölni egy ütemezett exportálási feladatot, kattintson a lista bármelyik elemére, majd a **Letiltás** vagy a **Törlés** parancsra.

Kezdetben egy-két óra is eltelhet az exportálási feladat lefutásáig. Azonban négy órát is igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

### <a name="export-schedule"></a>Ütemezés exportálása

Ütemezett exportálások az időt és a hét első létrehozásakor, az Exportálás vannak hatással. Amikor létrehoz egy ütemezett exportálás, az exportálás minden ezt követő exportálás előfordulást nap egyszerre fut. Például hozzon létre egy napi exportálás: 1:00 Órakor. A következő exportálási 1:00 Órakor a következő napon lefut. Az aktuális idő hatással van minden kiviteli típus azonos módon – a nap, amikor eredetileg létrehozta az Exportálás egyszerre mindig futnak. Egy másik példa, hétfőn egy heti exportálás du. 4:00: hozzon létre. DU. 4:00-kor fut a következő jelentés a következő hétfőn. *Az exportált adatok futási idő négy órán belül érhető el.*

Minden kiviteli hoz létre egy új fájlba, így a régebbi export nem írja felül.

Exportálási beállítások három típusa van:

**Hónap elejétől számított költségek napi exportálási** – az első exportálás azonnal futtatja. Ezt követő exportálások futtassa a következő nap a kezdeti exportálási egyszerre. A legfrissebb adatokat összesített értéket jelenít meg az előző napi exportálások.

**Az elmúlt 7 napra vonatkozó költségek heti exportálási** – az első exportálás azonnal futtatja. Ezt követő export a nap, hét, és a kezdeti exportálási egyszerre futtatni. A díjakat az elmúlt hét napban.

**Egyéni** – lehetővé teszi, hogy az ütemezés hetente és havonta exportálja hét és a hónap elejétől számított összetevőkkel. *A kezdeti exportálási azonnal fog futni.*

![Új Exportálás – egyéni heti-hét kijelölés alapismeretek lap](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Az adatgyűjtés sikerességének ellenőrzése

A Cost Management-adatainak gyűjtését egyszerűen ellenőrizheti, az exportált CSV-fájlt pedig az Azure Storage Explorerrel tekintheti meg.

Az exportálási listában kattintson a tárfiók nevére. A tárfiók oldalán kattintson a Megnyitás az Explorerben elemre. Ha megjelenik egy megerősítési mező, kattintson az **Igen** gombra a fájl Azure Storage Explorerben való megnyitásához.

![Tárfiók oldalának példaadatok és Megnyitás az Explorerben mutató hivatkozást bemutató](./media/tutorial-export-acm-data/storage-account-page.png)

A Storage Explorerben navigáljon ahhoz a tárolóhoz, amelyet meg szeretne nyitni, majd válassza ki az aktuális hónapnak megfelelő mappát. Ekkor egy CSV-fájlokból álló lista jelenik meg. Válasszon ki egy elemet, majd kattintson a **Megnyitás** gombra.

![A Storage Explorerben megjelenő példaadatok](./media/tutorial-export-acm-data/storage-explorer.png)

A fájl abban a programban vagy alkalmazásban nyílik meg, amelyik a CSV kiterjesztésű fájlok megnyitásához van beállítva. Íme egy példa az Excelben.

![Példa az exportált CSV-adatok az Excel programban látható](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>Exportált adatokhoz való hozzáférés más rendszerekből

A Cost Management-adatok exportálásának egyik célja az adatok külső rendszerekből való elérése. Használhat például valamilyen irányítópultot vagy egyéb pénzügyi rendszert. Az ilyen rendszerek nagyban eltérnek egymástól, ezért ebben az esetben nem lenne praktikus példával szolgálni.  Az adatok alkalmazásokból való elérését [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md) című cikkel is elkezdheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

Folytassa a következő oktatóanyaggal, ha optimalizálni és javítani szeretné a hatékonyságot a tétlen és kihasználatlan erőforrások azonosításával.

> [!div class="nextstepaction"]
> [Optimalizálási javaslatok áttekintése és végrehajtása](tutorial-acm-opt-recommendations.md)
