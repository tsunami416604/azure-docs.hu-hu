---
title: Oktatóanyag – Az Azure Cost Managementből exportált adatok létrehozása és kezelése | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti az Azure Cost Managementből exportált adatokat külső rendszerekkel való használat céljából.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/05/2018
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 734020f2e70d09b125867faafebf1e7f7332fec4
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995559"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Oktatóanyag: Exportált adatok létrehozása és kezelése

Ha elolvasta a Költségelemzés című oktatóanyagot, akkor ismeri a Cost Management-adatok manuális letöltésének módját. Az ismétlődő feladatokat, amelyek automatikusan exportálja a Cost Management adataihoz az Azure storage-napi, heti vagy havi időközönként is létrehozhat. Az exportált adatok CSV formátumúak, és a Cost Management által gyűjtött összes adatot tartalmazzák. Ezt követően az Azure Storage-beli exportált adatokat külső rendszerekkel is használhatja, illetve saját egyéni adataival kombinálhatja őket. Az exportált adatokat külső rendszerben, például egy irányítópulton vagy egyéb pénzügyi rendszerben is használhatja.

Az oktatóanyagban szereplő példák bemutatják, hogyan exportálhatja a Cost Management-adatokat, majd hogyan ellenőrizheti, hogy az adatok exportálása sikeres volt-e.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az adatexportálás minden [Nagyvállalati Szerződéssel (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) rendelkező ügyfél számára elérhető. Az alábbi Azure-engedélyek előfizetésenként támogatottak felhasználó vagy csoport általi adatexportáláshoz:

- Tulajdonos – Ütemezett exportálási feladatokat hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő – Létrehozhatja, módosíthatja vagy törölheti saját ütemezett exportálási feladatait. Módosíthatja a mások által létrehozott ütemezett exportálási feladatok nevét.
- Olvasó – Ütemezheti azokat az exportálási feladatokat, amelyekhez engedéllyel rendelkezik.

Azure Storage-fiókok esetén:
- A konfigurált tárfiók módosításához írási engedélyekre van szükség függetlenül attól, hogy az exportálásra milyen engedélyek vonatkoznak.
- Az Azure Storage-fiókot blob- vagy fájltároláshoz kell konfigurálni.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="create-a-daily-export"></a>Napi rendszerességű exportálás létrehozása

Cost Management + Számlázás &gt; Cost Management &gt; válasszon ki egy előfizetést vagy egy előfizetésben szereplő erőforráscsoportot &gt; Exportálás &gt; **Hozzáadás**.

Adjon meg egy nevet az exportálás, és válassza a "Hónap elejétől számított költségek naponta exportálása" lehetőséget. Kattintson a **Tovább** gombra.

![Új exportálási: exportálása típusa](./media/tutorial-export-acm-data/basics_exports.png)

Adja meg az Azure storage-fiók előfizetést, majd válassza ki a tárfiókját.  Adja meg a storage-tárolót és a könyvtár elérési útja, adja meg az exportfájl ugorhat.  Kattintson a **Tovább** gombra.

![Új exportálási: storage](./media/tutorial-export-acm-data/storage_exports.png)

Az Exportálás részleteiért tekintse át, és kattintson a **létrehozás**.

Az új exportálási feladat megjelenik az exportálási feladatok listájában. Új exportálási alapértelmezés szerint engedélyezve vannak. Ha szeretne letiltani vagy törölni egy ütemezett exportálási feladatot, kattintson a lista bármelyik elemére, majd a **Letiltás** vagy a **Törlés** parancsra.

Kezdetben egy-két óra is eltelhet az exportálási feladat lefutásáig. Azonban négy órát is igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

## <a name="verify-that-data-is-collected"></a>Az adatgyűjtés sikerességének ellenőrzése

A Cost Management-adatainak gyűjtését egyszerűen ellenőrizheti, az exportált CSV-fájlt pedig az Azure Storage Explorerrel tekintheti meg.

Az exportálási listában kattintson a tárfiók nevére. A tárfiók oldalán kattintson a Megnyitás az Explorerben elemre. Ha megjelenik egy megerősítési mező, kattintson az **Igen** gombra a fájl Azure Storage Explorerben való megnyitásához.

![Tárfiók oldala](./media/tutorial-export-acm-data/storage-account-page.png)

A Storage Explorerben navigáljon ahhoz a tárolóhoz, amelyet meg szeretne nyitni, majd válassza ki az aktuális hónapnak megfelelő mappát. Ekkor egy CSV-fájlokból álló lista jelenik meg. Válasszon ki egy elemet, majd kattintson a **Megnyitás** gombra.

![Storage Explorer](./media/tutorial-export-acm-data/storage-explorer.png)

A fájl abban a programban vagy alkalmazásban nyílik meg, amelyik a CSV kiterjesztésű fájlok megnyitásához van beállítva. Íme egy példa az Excelben.

![Exportálási példaadatok](./media/tutorial-export-acm-data/example-export-data.png)

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
