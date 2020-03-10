---
title: Oktatóanyag – Az Azure Cost Managementből exportált adatok létrehozása és kezelése
description: Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti az Azure Cost Managementből exportált adatokat külső rendszerekkel való használat céljából.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 04228a5f3f7e0b10e3cd881043af7861bb9fb347
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251143"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Oktatóanyag: Exportált adatok létrehozása és kezelése

Ha elolvasta a Költségelemzés című oktatóanyagot, akkor ismeri a Cost Management-adatok manuális letöltésének módját. Azonban létrehozhat egy ismétlődő feladatot, amely naponta, hetente vagy havonta automatikusan exportálja a Cost Management-adatokat az Azure Storage-ba. Az exportált adatok CSV formátumúak, és a Cost Management által gyűjtött összes adatot tartalmazzák. Ezt követően az Azure Storage-beli exportált adatokat külső rendszerekkel is használhatja, illetve saját egyéni adataival kombinálhatja őket. Az exportált adatokat külső rendszerben, például egy irányítópulton vagy egyéb pénzügyi rendszerben is használhatja.

Tekintse meg a [Storage-exportálások Azure Cost Managementtel történő ütemezését ismertető](https://www.youtube.com/watch?v=rWa_xI1aRzo) videót az Azure-költségadatok Azure Storage-ba való ütemezett exportálásáról.

Az oktatóanyagban szereplő példák bemutatják, hogyan exportálhatja a Cost Management-adatokat, majd hogyan ellenőrizheti, hogy az adatok exportálása sikeres volt-e.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek
Az adatexportálás számos Azure-fióktípushoz elérhető, beleértve a [Nagyvállalati Szerződéssel (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) és a [Microsoft Ügyfélszerződéssel](get-started-partners.md) rendelkező ügyfeleket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](understand-cost-mgt-data.md). Az alábbi Azure-engedélyek és -hatókörök előfizetésenként támogatottak felhasználó vagy csoport általi adatexportáláshoz. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).

- Tulajdonos – Ütemezett exportálási feladatokat hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő – Létrehozhatja, módosíthatja vagy törölheti saját ütemezett exportálási feladatait. Módosíthatja a mások által létrehozott ütemezett exportálási feladatok nevét.
- Olvasó – Ütemezheti azokat az exportálási feladatokat, amelyekhez engedéllyel rendelkezik.

Azure Storage-fiókok esetén:
- A konfigurált tárfiók módosításához írási engedélyekre van szükség függetlenül attól, hogy az exportálásra milyen engedélyek vonatkoznak.
- Az Azure Storage-fiókot blob- vagy fájltároláshoz kell konfigurálni.

Új előfizetés esetén nem használhatja azonnal a Cost Management szolgáltatásait. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="create-a-daily-export"></a>Napi rendszerességű exportálás létrehozása

Adatexportálás létrehozásához, megtekintéséhez vagy ütemezéséhez nyissa meg a kívánt hatókört az Azure Portalon, majd válassza a **Költségelemzés** lehetőséget a menüben. Például lépjen az **Előfizetések** pontra, válasszon ki egy előfizetést a listában, majd válassza a **Költségelemzés** menüpontot. A Költségelemzés lap tetején kattintson az **Exportálás** elemre, majd válasszon ki egy exportálási beállítást. Például válassza az **Exportálás üzemezése** lehetőséget.  

> [!NOTE]
> - Az előfizetéseken túl erőforráscsoportokhoz, fiókokhoz, részlegekhez és regisztrációkhoz is hozhat létre exportálásokat. További információ a hatókörökről: [A hatókörök ismertetése és használata](understand-work-scopes.md).
>- Amikor partnerként jelentkezik be a számlázási fiók hatókörébe vagy egy ügyfél bérlőjébe, adatokat exportálhat abba az Azure Storage-fiókba, amely össze van kapcsolva a partner tárfiókjával. Ehhez azonban aktív előfizetéssel kell rendelkeznie a CSP-bérlő esetében.
>


Kattintson a **Hozzáadás** elemre, adja meg az exportálás kívánt nevét, majd válassza **Az eddigi tárgyhavi költségek napi exportálása** lehetőséget. Kattintson a **Tovább** gombra.

![Példa új exportálásra az exportálás típusával](./media/tutorial-export-acm-data/basics_exports.png)

Adja meg az Azure-tárfiók előfizetését, majd válassza ki a tárfiókját.  Adja meg a tárolót és a könyvtár elérési útját, ahová az exportálást irányítani szeretné. Kattintson a **Tovább** gombra.

![Új exportálási példa a tárfiók adataival](./media/tutorial-export-acm-data/storage_exports.png)

Tekintse át az exportálás adatait, majd kattintson a **Létrehozás** gombra.

Az új exportálási feladat megjelenik az exportálási feladatok listájában. Alapértelmezés szerint az új exportálások engedélyezve vannak. Ha le szeretne tiltani vagy törölni szeretne egy ütemezett exportálást, kattintson a lista bármelyik elemére, majd a **Letiltás** vagy a **Törlés** parancsra.

Kezdetben egy-két óra is eltelhet az exportálási feladat lefutásáig. Azonban négy órát is igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

### <a name="export-schedule"></a>Exportálás üzemezése

Az ütemezett exportálást meghatározza, hogy azt eredetileg a hét mely napján és mely időpontban hozta létre. Amikor ütemezett exportálást hoz létre, minden további exportálási eseményre ugyanazon gyakorisággal fog sor kerülni. Például ha egy aktuális havi exportálás napi gyakoriságúra van beállítva, az exportálási feladat naponta fut le. Hasonlóan a heti exportáláshoz, az exportálási feladat minden héten ugyanazon a napon fut le, az ütemezésnek megfelelően. Az exportált adatok pontos kézbesítési időpontja nem garantált, és az exportált adatok a futtatás időpontjától számított négy órán belül állnak rendelkezésre.
Mindegyik exportálás egy új fájlt hoz létre, azaz a régebben exportált adatok nem lesznek felülírva.

Kétféle exportálási lehetőség áll rendelkezésre:

**Az eddigi tárgyhavi költségek napi exportálása** – A kezdeti exportálás azonnal lefut. A további exportálási feladatok minden rákövetkező nap az eredeti exportálással meg egyező időpontban futnak le. Az aktuális adatokat a rendszer összesíti a korábbi napi exportálások adataival.

**Egyéni** – Heti és havi exportálási feladatok ütemezését teszi lehetővé az addigi tárgyheti vagy tárgyhavi adatokkal. *A kezdeti exportálás azonnal lefut.*

Ha használatalapú, MSDN- vagy Visual Studio-előfizetéssel rendelkezik, a számlázási időszak nem feltétlenül esik egybe a naptári hónapokkal. Az ilyen típusú előfizetésekhez és erőforráscsoportokhoz olyan exportálásokat is létrehozhat, amelyek a számlázási időszakhoz vagy a naptári hónapokhoz igazodnak. A számlázási hónaphoz igazodó exportálás létrehozásához lépjen az **Egyéni** menüpontra, majd válassza a **Számlázási időszak mai napig** lehetőséget.  A naptári hónaphoz igazodó exportálás létrehozásához válassza az **Ebben a hónapban** lehetőséget.
>
>

![Új exportálás – Alapvető beállítások lap, amelyen látható az aktuális heti egyéni kiválasztás](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>Az adatgyűjtés sikerességének ellenőrzése

A Cost Management-adatainak gyűjtését egyszerűen ellenőrizheti, az exportált CSV-fájlt pedig az Azure Storage Explorerrel tekintheti meg.

Az exportálási listában kattintson a tárfiók nevére. A tárfiók lapján válassza a Megnyitás az Explorerben lehetőséget. Ha megjelenik egy megerősítési mező, kattintson az **Igen** gombra, hogy megnyissa a fájlt az Azure Storage Explorerben.

![A tárfiók lapja mintául szolgáló adatokkal és a Megnyitás az Explorerben hivatkozással](./media/tutorial-export-acm-data/storage-account-page.png)

A Storage Explorerben navigáljon ahhoz a tárolóhoz, amelyet meg szeretne nyitni, majd válassza ki az aktuális hónapnak megfelelő mappát. Ekkor egy CSV-fájlokból álló lista jelenik meg. Válasszon ki egy elemet, majd kattintson a **Megnyitás** gombra.

![A Storage Explorerben megjelenő, mintául szolgáló adatok](./media/tutorial-export-acm-data/storage-explorer.png)

A fájl abban a programban vagy alkalmazásban nyílik meg, amelyik a CSV kiterjesztésű fájlok megnyitásához van beállítva. Íme egy példa az Excelben.

![Példa Excelben megjelenített exportált CSV-adatokra](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>Exportált adatokhoz való hozzáférés más rendszerekből

A Cost Management-adatok exportálásának egyik célja az adatok külső rendszerekből való elérése. Használhat például valamilyen irányítópultot vagy egyéb pénzügyi rendszert. Az ilyen rendszerek nagyban eltérnek egymástól, ezért ebben az esetben nem lenne praktikus példával szolgálni.  Az adatok alkalmazásokból való elérését [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md) című cikkel is elkezdheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

Folytassa a következő oktatóanyaggal, ha optimalizálni és javítani szeretné a hatékonyságot a tétlen és kihasználatlan erőforrások azonosításával.

> [!div class="nextstepaction"]
> [Optimalizálási javaslatok áttekintése és végrehajtása](tutorial-acm-opt-recommendations.md)
