---
title: Azure-támogatási kérelem létrehozása | Microsoft Docs
description: Azok az ügyfelek, akiknek segítségre van szükségük, a Azure Portal használhatják az önkiszolgáló megoldásokat, valamint a támogatási kérések létrehozását és kezelését.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: how-to
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 52205ec4f5aabc2eb858229d26bc4966441820aa
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763890"
---
# <a name="how-to-create-an-azure-support-request"></a>Azure-támogatáskérések létrehozása

## <a name="overview"></a>Áttekintés

Az Azure-ban támogatási kérelmeket, más néven támogatási jegyeket hozhat létre és kezelhet. A [Azure Portalban](https://portal.azure.com)olyan kérelmeket hozhat létre és kezelhet, amelyek a jelen cikkben szerepelnek. A kéréseket programozott módon is létrehozhatja és kezelheti az [Azure támogatási jegy REST API](/rest/api/support)használatával.

> [!NOTE]
> A Azure Portal URL-cím kifejezetten arra az Azure-felhőre vonatkozik, ahol a szervezete telepítve van.
>
>* A kereskedelmi felhasználásra Azure Portal a következő:[https://portal.azure.com](https://portal.azure.com)
>* Németország Azure Portal:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* A Egyesült Államok Government Azure Portal a következő:[https://portal.azure.us](https://portal.azure.us)
>
>

Az ügyfelek visszajelzései alapján frissítettük a támogatási kérések tapasztalatát, hogy három fő célt összpontosítsanak:

* **Egyszerűsítve**: a támogatási kérések beküldésével egyszerűen megtalálhatja és leegyszerűsítheti a támogatást és a hibaelhárítást.
* **Integrált**: létrehozhat egy támogatási kérést, ha egy Azure-erőforrással kapcsolatos problémát hibaelhárítás nélkül kapcsol be a környezettel.
* **Hatékony**: gyűjtsön olyan legfontosabb információkat, amelyekre a támogatási ügynöknek szüksége van ahhoz, hogy hatékonyan megoldja a problémát.

## <a name="getting-started"></a>Első lépések

A Azure Portal Súgó és **támogatás** szolgáltatását is elérheti. A szolgáltatás a Azure Portal menüjéből, a globális fejlécből vagy a szolgáltatások erőforrás-menüjéből érhető el. Ha támogatási kérést szeretne beküldeni, rendelkeznie kell a megfelelő engedélyekkel.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Támogatási kérelem létrehozásához [tulajdonosnak](../../role-based-access-control/built-in-roles.md#owner), [közreműködőnek](../../role-based-access-control/built-in-roles.md#contributor) kell lennie, vagy a [támogatási kérelem közreműködői](../../role-based-access-control/built-in-roles.md#support-request-contributor) szerepköréhez kell rendelni az előfizetés szintjén. Ha előfizetés nélküli támogatási kérést szeretne létrehozni, például Azure Active Directory (HRE) forgatókönyv esetén, [rendszergazdai](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)jogosultsággal kell rendelkeznie.

### <a name="go-to-help--support-from-the-global-header"></a>Ugrás a Súgó + támogatás elemre a globális fejlécből

Támogatási kérelem elindítása bárhonnan a Azure Portalban:

1. Válassza ki a **?** elemet. a globális fejlécben. Ezután válassza a **Súgó + támogatás**lehetőséget.

   ![Súgó és támogatás](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Válassza az **Új támogatási kérelem** lehetőséget. Az utasításokat követve adja meg nekünk a problémával kapcsolatos információkat. Javaslatot teszünk néhány lehetséges megoldásra, a probléma részleteinek begyűjtésére és a támogatási kérelem elküldésére és nyomon követésére.

   ![Új támogatási kérelem](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ugrás a Súgó + támogatás erőforrás menüjéből

Ha támogatási kérést szeretne elindítani az erőforrás kontextusában, jelenleg a következővel dolgozik:

1. Az erőforrás menüben, a **támogatás + hibaelhárítás** szakaszban válassza az **új támogatási kérelem**lehetőséget.

   ![Kontextusban](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Az utasításokat követve adja meg nekünk a problémával kapcsolatos információkat. Amikor elindítja a támogatási kérés folyamatát az erőforrásból, egyes beállítások előre ki vannak választva.

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

Végigvezeti a problémával kapcsolatos információk gyűjtésének lépésein, és segít a megoldásában. Az egyes lépéseket a következő szakaszokban ismertetjük.

### <a name="basics"></a>Alapvető beállítások

A támogatási kérelem folyamatának első lépése a probléma és a támogatási csomag alapvető információit gyűjti.

Az **új támogatási kérelem** **alapjai** lapon a választókkal kezdheti el a problémát. Először meg kell határoznia néhány általános kategóriát a probléma típusához, és kiválaszthatja a kapcsolódó előfizetést. Válassza ki a szolgáltatást, például a **Windows rendszerű virtuális gépet**. Válassza ki az erőforrást, például a virtuális gép nevét. Írja le a problémát a saját szavaival, majd **válassza a probléma típusa lehetőséget** a pontosabb megjelenítéshez.

![Alapvető beállítások panel](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Az Azure korlátlan támogatást biztosít az előfizetés-kezeléshez, amely magában foglalja a számlázást, a kvóták módosítását és a fiókok átvitelét. Technikai támogatásért támogatási csomagra van szükség. [További információ a támogatási csomagokról](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Megoldások

Az alapszintű információk összegyűjtése után a következő megoldásokat mutatjuk be a saját kipróbálására. Bizonyos esetekben előfordulhat, hogy egy gyors diagnosztikát is futtatunk. A megoldásokat az Azure-mérnökök írták, és a leggyakoribb problémák megoldására szolgálnak.

### <a name="details"></a>Részletek

A következő lépés a problémával kapcsolatos további részletek begyűjtése. Ebben a lépésben alapos és részletes információkat nyújt a támogatási kérésnek a megfelelő ügynöknek történő továbbításához.

Ha lehetséges, mondja el nekünk, hogy mikor indította el a problémát, és hogy milyen lépéseket kell újból létrehoznia. Feltölthet egy fájlt, például egy naplófájlt vagy egy kimenetet a diagnosztika szolgáltatásból.

Miután megtörtént a problémával kapcsolatos összes információ, válassza ki, hogyan kérhet támogatást. A **részletek**a **támogatási módszer** szakaszban válassza ki a hatás súlyosságát. Adja meg az előnyben részesített kapcsolattartási módszert, amely az Önnel való kapcsolatfelvételhez és a támogatási nyelvhez nyújt segítséget.

Ezután végezze el a **kapcsolattartási adatok** szakaszt, hogy tudjuk, hogyan kell felvenni Önnel a kapcsolatot.

### <a name="review--create"></a>Felülvizsgálat + létrehozás

Fejezze be az összes szükséges információt az egyes lapokon, majd válassza a **felülvizsgálat + létrehozás**elemet. Tekintse át a támogatáshoz küldendő adatokat. Ha szükséges, lépjen vissza bármelyik lapra, és végezze el a módosítást. Ha meggyőződött arról, hogy a támogatási kérelem elkészült, válassza a **Létrehozás**lehetőséget.

A támogatási ügynök felveszi Önnel a kapcsolatot a megadott módszer használatával. További információ a kezdeti válaszidő használatáról: a [támogatás hatóköre és a válaszadás](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Minden támogatási kérelem

A támogatási kérések részleteit és állapotát a **Súgó + támogatás**  >   **minden támogatási kérelem**támogatásával tekintheti meg.

![Minden támogatási kérelem](./media/how-to-create-azure-support-request/allrequestslower.png)

Ezen a lapon az **előfizetés**, a **Létrehozás** dátuma (UTC) és az **állapot**alapján szűrheti a támogatási kérelmeket. Ezen a lapon a támogatási kérések is rendezhetők és kereshetők.

Válasszon ki egy támogatási kérést a részletek megtekintéséhez, beleértve a súlyosságot és azt a várható időt, amelyet a támogatási ügynök válaszolni fog.

Ha módosítani szeretné a kérelem súlyosságát, válassza az **üzleti hatás**lehetőséget. Válassza ki a hozzárendelni kívánt jogosultságok listáját.

> [!NOTE]
> A maximális súlyossági szint a támogatási csomagtól függ. [További információ a támogatási csomagokról](https://azure.microsoft.com/support/plans).
>
>
Ha többet szeretne megtudni az Azure önsegítő támogatási lehetőségeiről, tekintse meg ezt a videót:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>További lépések

* [Küldje el nekünk visszajelzését és javaslatait](https://feedback.azure.com/forums/266794-support-feedback)
* Részvétel a [Twitteren](https://twitter.com/azuresupport)
* Segítség kérése a társaitól a [Microsoft Q&a kérdéses oldalon](https://docs.microsoft.com/answers/products/azure)
* További információ az [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq)
