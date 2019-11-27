---
title: Oktatóanyag – exportált adatok létrehozása és kezelése Azure Cost Managementból
description: Ez a cikk bemutatja, hogyan hozhatja létre és kezelheti az exportált Azure Cost Management-adatforrásokat, hogy azok külső rendszerekben is használhatók legyenek.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229829"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Oktatóanyag: Exportált adatok létrehozása és kezelése

Ha elolvasta a Költségelemzés című oktatóanyagot, akkor ismeri a Cost Management-adatok manuális letöltésének módját. Az ismétlődő feladatokat, amelyek automatikusan exportálja a Cost Management adataihoz az Azure storage-napi, heti vagy havi időközönként is létrehozhat. Az exportált adatok CSV formátumúak, és a Cost Management által gyűjtött összes adatot tartalmazzák. Ezt követően az Azure Storage-beli exportált adatokat külső rendszerekkel is használhatja, illetve saját egyéni adataival kombinálhatja őket. Az exportált adatokat külső rendszerben, például egy irányítópulton vagy egyéb pénzügyi rendszerben is használhatja.

Tekintse meg az Azure-beli költségadatok Azure Storage-ba történő ütemezett exportálásának [ütemezését bemutató Azure Cost Management videót tartalmazó tárolóba](https://www.youtube.com/watch?v=rWa_xI1aRzo) való exportálás módját.

Az oktatóanyagban szereplő példák bemutatják, hogyan exportálhatja a Cost Management-adatokat, majd hogyan ellenőrizheti, hogy az adatok exportálása sikeres volt-e.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Napi rendszerességű exportálás létrehozása
> * Az adatgyűjtés sikerességének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek
Az adatexportálás számos különböző Azure-fióktípus esetében elérhető, beleértve a [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ügyfeleket is. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md). A következő Azure-engedélyek vagy-hatókörök a felhasználók és csoportok által az adatexportálásra vonatkozó előfizetésekben támogatottak. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).

- Tulajdonos – Ütemezett exportálási feladatokat hozhat létre, módosíthat vagy törölhet az előfizetésben.
- Közreműködő – Létrehozhatja, módosíthatja vagy törölheti saját ütemezett exportálási feladatait. Módosíthatja a mások által létrehozott ütemezett exportálási feladatok nevét.
- Olvasó – Ütemezheti azokat az exportálási feladatokat, amelyekhez engedéllyel rendelkezik.

Azure Storage-fiókok esetén:
- A konfigurált tárfiók módosításához írási engedélyekre van szükség függetlenül attól, hogy az exportálásra milyen engedélyek vonatkoznak.
- Az Azure Storage-fiókot blob- vagy fájltároláshoz kell konfigurálni.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

## <a name="create-a-daily-export"></a>Napi rendszerességű exportálás létrehozása

Ha egy adatexportálást szeretne létrehozni vagy megtekinteni, vagy exportálni szeretné az exportálást, nyissa meg a kívánt hatókört a Azure Portalban, és válassza a menü **Cost Analysis** elemét. Például navigáljon az **előfizetések**elemre, válasszon ki egy előfizetést a listából, majd válassza a menü **Cost Analysis** elemét. A Cost Analysis lap tetején kattintson az **Exportálás** elemre, majd válassza az Exportálás lehetőséget. Kattintson például az **Exportálás időzítése**lehetőségre.  

> [!NOTE]
> - Az előfizetések mellett az erőforráscsoportok, a fiókok, a részlegek és a beléptetések exportálását is létrehozhatja. További információ a hatókörökről: a [hatókörök megismerése és használata](understand-work-scopes.md).
>- Ha partnerként jelentkezett be a Számlázási fiók hatókörében vagy egy ügyfél bérlője számára, exportálhatja az adatait egy olyan Azure Storage-fiókba, amely a partner Storage-fiókjához van csatolva. Azonban rendelkeznie kell aktív előfizetéssel a CSP-bérlőben.
>


Kattintson a **Hozzáadás**elemre, írja be az Exportálás nevét, majd válassza ki a **havi dátum és idő szerinti költségek napi exportálását** . Kattintson a **Tovább** gombra.

![Exportálás típust megjelenítő új exportálási példa](./media/tutorial-export-acm-data/basics_exports.png)

Adja meg az Azure storage-fiók előfizetést, majd válassza ki a tárfiókját.  Adja meg a storage-tárolót és a könyvtár elérési útja, adja meg az exportfájl ugorhat.  Kattintson a **Tovább** gombra.

![Tárfiók részleteit megjelenítő új exportálási példa](./media/tutorial-export-acm-data/storage_exports.png)

Tekintse át az Exportálás részleteit, és kattintson a **Létrehozás**gombra.

Az új exportálási feladat megjelenik az exportálási feladatok listájában. Új exportálási alapértelmezés szerint engedélyezve vannak. Ha szeretne letiltani vagy törölni egy ütemezett exportálási feladatot, kattintson a lista bármelyik elemére, majd a **Letiltás** vagy a **Törlés** parancsra.

Kezdetben egy-két óra is eltelhet az exportálási feladat lefutásáig. Azonban négy órát is igénybe vehet, mire az adatok megjelennek az exportált fájlokban.

### <a name="export-schedule"></a>Exportálási ütemterv

Az ütemezett exportálásra az Exportálás első létrehozásakor érvényes idő és hét napja vonatkozik. Amikor ütemezett exportálást hoz létre, az Exportálás minden további exportálási esemény esetében ugyanazon a napon fut. Létrehozhat például egy napi exportálást 1:00 ÓRAKOR. A következő exportálás a következő napon 1:00 ÓRAKOR fut. Az aktuális idő az összes többi exportálási típusra hatással van – a rendszer mindig ugyanazon a napon fut le, mint amikor először hozta létre az exportálást. Egy másik példában a heti exportálást a hétfő 4:00 ÓRAKOR hozza létre. A következő jelentés a következő hétfőn 4:00 ÓRAKOR fut. *Az exportált adatmennyiség a futtatási idő négy óráján belül elérhető.*

Minden exportálás új fájlt hoz létre, így a régebbi exportálások nem írhatók felül.

Háromféle exportálási lehetőség létezik:

A **hónapról a napra szóló költségek napi exportálása** – a kezdeti exportálás azonnal megkezdődik. A későbbi exportálások a kezdeti exportálással megegyező időpontban futtatják a következő napot. A legfrissebb adatokat a rendszer összesíti a korábbi napi exportálások alapján.

**Az elmúlt 7 nap költségeinek heti exportálása** – a kezdeti exportálás azonnal elindul. A későbbi exportálások a hét napján és a kezdeti exportálással megegyező időpontban futnak. A költségek az elmúlt hét napra vonatkoznak.

**Custom (egyéni** ) – lehetővé teszi a heti és a havi exportálási ütemtervek, valamint a hónap és a nap közötti beállítások megadását. *A kezdeti exportálás azonnal elindul.*

Ha utólagos elszámolású, MSDN-vagy Visual Studio-előfizetéssel rendelkezik, előfordulhat, hogy a számla számlázási időszaka nem a naptári hónaphoz igazodik. Az ilyen típusú előfizetések és erőforráscsoportok esetében létrehozhat egy olyan exportálást, amely a számlázási időszakhoz vagy naptári hónapokhoz van igazítva. A számlázási hónaphoz igazított exportálás létrehozásához navigáljon az **Egyéni**elemre, majd válassza a **számlázás-időszak – dátum**lehetőséget.  A naptári hónapra igazított exportálás létrehozásához válassza a **hónap – dátum**lehetőséget.
>
>

![Új Exportálás – alapvető alapismeretek lap, amely egy egyéni hetente kiválasztott heti kijelölést mutat be](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

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
