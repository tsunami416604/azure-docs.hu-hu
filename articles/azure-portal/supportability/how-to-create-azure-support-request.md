---
title: Azure-támogatási kérelem létrehozása | Microsoft dokumentumok
description: A segítségre szoruló ügyfelek az Azure Portalon önkiszolgáló megoldásokat kereshetnek, valamint támogatási kérelmeket hozhatnak létre és kezelhetnek.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248488"
---
# <a name="how-to-create-an-azure-support-request"></a>Azure-támogatáskérések létrehozása

## <a name="overview"></a>Áttekintés

Az Azure lehetővé teszi támogatási kérelmek, más néven támogatási jegyek létrehozását és kezelését. Az [Azure Portalon](https://portal.azure.com)hozhat létre és kezelhet kéréseket, amelyek ebben a cikkben szerepelnek. A kérelmeket programozott módon is létrehozhatja és kezelheti az [Azure support ticket REST API használatával.](/rest/api/support)

> [!NOTE]
> Az Azure Portal URL-címe az Azure-felhőre vonatkozik, ahol a szervezet telepítve van.
>
>* Az Azure Portal kereskedelmi használatra:[https://portal.azure.com](https://portal.azure.com)
>* A németországi Azure portal a következő:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Az Egyesült Államok kormányának Azure portalja:[https://portal.azure.us](https://portal.azure.us)
>
>

Az ügyfelek visszajelzései alapján frissítettük a támogatási kérelem élményét, hogy három fő célra összpontosítsunk:

* **Egyszerűsített**: A támogatás és a hibaelhárítás egyszerűvé teszi a támogatási kérelmek megkeresését és egyszerűsítését.
* **Integrált**: Könnyedén megnyithat egy támogatási kérelmet, ha egy Azure-erőforrással kapcsolatos problémát hárít el, környezetváltás nélkül.
* **Hatékony:** Gyűjtse össze a támogatási ügynökének a probléma hatékony megoldásához szükséges legfontosabb információkat.

## <a name="getting-started"></a>Első lépések

A Súgó **+ támogatás** az Azure Portalon. Elérhető az Azure Portal menüből, a globális fejlécből vagy egy szolgáltatás erőforrásmenüjéből. A támogatási kérelem benyújtásához megfelelő engedélyekkel kell rendelkeznie.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Támogatási kérelem létrehozásához rendszergazdai munkatársnak kell lennie, vagy az előfizetés szintjén hozzá kell rendelnie a [támogatási kérelem közreműködői](../../role-based-access-control/built-in-roles.md#support-request-contributor) szerepkörhöz.

### <a name="go-to-help--support-from-the-global-header"></a>Ugrás a Súgó + támogatás a globális fejlécből

Támogatási kérelem indítása bárhonnan az Azure Portalon:

1. Válassza ki a **?** elemet. a globális fejlécben. Ezután válassza **a Súgó + támogatás**lehetőséget.

   ![Súgó és támogatás](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Válassza **az Új támogatási kérelem lehetőséget.** Kövesse az utasításokat, hogy tájékoztatást nyújtson a problémáról. Néhány lehetséges megoldást javasolunk, összegyűjtjük a probléma részleteit, és segítünk a támogatási kérelem elküldésében és nyomon követésében.

   ![Új támogatási kérelem](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ugrás a Súgó + támogatás menüből

Ha támogatási kérelmet szeretne elindítani az erőforrás környezetében, jelenleg a következőkkel dolgozik:

1. Az erőforrás menü **Támogatás + Hibaelhárítás** csoportjában válassza az **Új támogatási kérelem**lehetőséget.

   ![Kontextusban](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Kövesse az utasításokat, hogy tájékoztatást nyújtson a problémáról. Amikor elindítja a támogatási kérelem folyamatát az erőforrásból, néhány beállítás előre ki van választva.

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

Végigvezetjük néhány lépésen, hogy információkat gyűjtsünk a problémájáról, és segítsünk megoldani. Minden lépést a következő szakaszok ismertetünk.

### <a name="basics"></a>Alapvető beállítások

A támogatási kérelem folyamatának első lépése alapvető információkat gyűjt a problémáról és a támogatási csomagról.

Az Új támogatási **kérelem** **Alapjai** lapján a választók segítségével kezdje el elmondani nekünk a problémát. Először azonosítson néhány általános kategóriát a probléma típusához, és válassza ki a kapcsolódó előfizetést. Válassza ki a szolgáltatást, például a **Windows rendszert futtató Virtuális gép.** Válassza ki az erőforrást, például a virtuális gép nevét. Írja le a problémát a saját szavaival, majd válassza ki a **probléma típusát,** hogy pontosabban megkapd.

![Alapvető beállítások panel](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Az Azure korlátlan támogatást nyújt az előfizetések kezeléséhez, amely magában foglalja a számlázást, a kvótakorrekciókat és a fiókátviteleket. A technikai támogatáshoz támogatási csomagra van szükség. [További információ a támogatási csomagokról.](https://azure.microsoft.com/support/plans)
>
>

### <a name="solutions"></a>Megoldások

Az alapvető információk összegyűjtése után a következő megoldásokat mutatjuk be, amelyeket saját maga próbálhat ki. Bizonyos esetekben még egy gyors diagnosztikát is futtathatunk. A megoldásokat az Azure mérnökei írják, és a leggyakoribb problémákat oldják meg.

### <a name="details"></a>Részletek

Ezután további részleteket gyűjtünk a problémáról. Ha alapos és részletes információkat nyújt ebben a lépésben, akkor a támogatási kérelmét a megfelelő ügynökhöz irányíthatja.

Ha lehetséges, mondja el, mikor kezdődött a probléma, és minden lépést, hogy reprodukálni. Feltölthet egy fájlt, például egy naplófájlt vagy a diagnosztikából származó kimenetet.

Miután minden információt a probléma, válassza ki, hogyan kap támogatást. A Részletek **támogatási módszer** **szakaszában**válassza ki az ütközés súlyosságát. Adja meg a kívánt kapcsolatfelvételi módot, jó alkalom arra, hogy kapcsolatba lépjen Önnel és a támogatási nyelvével.

Ezután töltse ki a **Kapcsolattartási adatok szakaszt,** hogy tudjuk, hogyan léphetünk kapcsolatba Önnel.

### <a name="review--create"></a>Véleményezés + létrehozás

Töltse ki az összes szükséges információt az egyes lapokon, majd válassza **a Véleményezés + create**lehetőséget. Ellenőrizze a támogatási szolgálatnak elküldött adatokat. Lépjen vissza bármelyik lapra, és szükség esetén változtasson. Ha meggyőződött arról, hogy a támogatási kérelem befejeződött, válassza a **Létrehozás gombot.**

A támogatási ügynök a megadott módszerrel veszi fel Önnel a kapcsolatot. A kezdeti válaszidőről a [Támogatási hatókör és válaszidő](https://azure.microsoft.com/support/plans/response/)című témakörben talál további információt.

## <a name="all-support-requests"></a>Minden támogatási kérelem

A támogatási kérelmek részleteit és állapotát a **Súgó + támogatás** >  **minden támogatási kérelemhez**talál.

![Minden támogatási kérelem](./media/how-to-create-azure-support-request/allrequestslower.png)

Ezen a lapon a támogatási kérelmeket **előfizetés**, **létrehozási** dátum (UTC) és **Állapot**szerint szűrheti. Ezen kívül ezen az oldalon rendezheti és megkeresheti a támogatási kérelmeket.

Válasszon ki egy támogatási kérelmet a részletek megtekintéséhez, beleértve a súlyossági időt és a támogatási ügynök válaszadásához szükséges várható időt.

Ha módosítani szeretné a kérelem súlyosságát, válassza az **Üzleti hatás**lehetőséget. Válasszon a hozzárendelni kívánt végkielégítések listájából.

> [!NOTE]
> A maximális súlyossági szint a támogatási csomagtól függ. [További információ a támogatási csomagokról.](https://azure.microsoft.com/support/plans)
>
>
Ha többet szeretne megtudni az Azure önsegítő támogatási lehetőségeiről, tekintse meg ezt a videót:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>További lépések

* [Küldje el nekünk visszajelzését és javaslatait](https://feedback.azure.com/forums/266794-support-feedback)
* Lépjen kapcsolatba velünk a [Twitteren](https://twitter.com/azuresupport)
* Kérjen segítséget társaitól az [MSDN fórumokon](https://social.msdn.microsoft.com/Forums/azure)
* További információ az [Azure-támogatásról – gyakori kérdések](https://azure.microsoft.com/support/faq)
