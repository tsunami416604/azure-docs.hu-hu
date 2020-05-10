---
title: Kapcsolódás Common Data Servicehoz
description: Common Data Service-rekordok létrehozása és kezelése Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/08/2020
tags: connectors
ms.openlocfilehash: 98da7e959e4b59ad2d0f3f3f79364391b4ceddbd
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997099"
---
# <a name="create-and-manage-records-in-common-data-service-by-using-azure-logic-apps"></a>Rekordok létrehozása és kezelése Common Data Serviceban Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és az [Common Data Service-összekötővel](https://docs.microsoft.com/connectors/commondataservice/)olyan automatizált munkafolyamatokat hozhat létre, amelyek a [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro) -adatbázisban lévő rekordokat kezelik. Ezek a munkafolyamatok létrehozhatnak rekordokat, frissíthetik a rekordokat, és egyéb műveleteket végezhetnek el. A Common Data Service adatbázisból is kérhet információt, és a kimenet elérhetővé teheti a logikai alkalmazásban használható egyéb műveletek számára. Ha például egy rekord frissül a Common Data Service adatbázisban, az Office 365 Outlook Connector használatával küldhet e-mailt.

Ez a cikk bemutatja, hogyan hozhat létre egy olyan logikai alkalmazást, amely egy új érdeklődői rekord létrehozásakor feladatsort hoz létre.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy [Common Data Service-környezet](https://docs.microsoft.com/power-platform/admin/environments-overview), amely a szervezet által az üzleti és a Common Data Service adatbázis tárolására, kezelésére és megosztására szolgáló terület. További információkért tekintse meg a következő forrásokat:<p>

  * [További információ: a Common Data Service első lépései](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power platform – környezetek – áttekintés](https://docs.microsoft.com/power-platform/admin/environments-overview)

* Alapszintű információ a Logic apps és a logikai alkalmazás [létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md) , ahonnan a Common Data Service adatbázisban lévő rekordokhoz szeretne hozzáférni. Ha Common Data Service triggerrel szeretné elindítani a logikai alkalmazást, üres logikai alkalmazásra van szüksége. Ha most ismerkedik a Azure Logic Appsval, tekintse át a gyors útmutató [: az első munkafolyamat létrehozása a Azure Logic Apps használatával](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Common Data Service trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Ebben a példában adja hozzá a Common Data Service eseményindítót, amely új rekord létrehozásakor következik be.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. A keresőmezőbe írja be a `common data service` kifejezést. Ebben a példában az eseményindítók listában válassza ki ezt az eseményindítót: **rekord létrehozásakor**

   !["A rekord létrehozásakor" trigger kiválasztása](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Ha a rendszer kéri, jelentkezzen be a Common Data Serviceba.

1. A triggerben adja meg a környezetet, ahol figyelni szeretné az új "leads" rekordokat, például:

   ![A figyeléshez használt környezet információinak kiváltása](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Környezet** | Igen | A figyelni kívánt környezet, például "Fabrikam Sales Production". További információ: [Power platform – környezetek – áttekintés](https://docs.microsoft.com/power-platform/admin/environments-overview). |
   | **Entitás neve** | Igen | A figyelni kívánt entitás, például "leads" |
   | **Hatókör** | Igen | Az új rekordot létrehozó forrás, például egy felhasználó a céges egységben vagy a szervezet bármely felhasználója. Ez a példa a "Business Unit" szolgáltatást használja. |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service művelet hozzáadása

Most adjon hozzá egy Common Data Service műveletet, amely létrehoz egy új "leads" rekordhoz tartozó feladat rekordot.

1. A **rekord létrehozásakor** trigger alatt válassza az **új lépés**lehetőséget.

1. A keresőmezőbe írja be a `common data service` kifejezést. A műveletek listából válassza ki ezt a műveletet: **új rekord létrehozása**

   ![Válassza az "új rekord létrehozása" műveletet.](./media/connect-common-data-service/select-create-new-record-action.png)

1. A műveletben adja meg azt a környezetet, amelyben létre szeretné hozni az új feladathoz tartozó rekordot. Ha elérhető, más tulajdonságok is megjelennek a művelethez kiválasztott entitás alapján, például:

   ![A rekord létrehozására szolgáló környezetre vonatkozó műveleti információk](./media/connect-common-data-service/create-new-record-action-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Szervezet neve** | Igen | Az a környezet, amelyben létre kívánja hozni a rekordot, és nem feltétlenül ugyanazt a környezetet kell megadnia az triggerben, de ebben a példában a "Fabrikam értékesítési termelés" szerepel. |
   | **Entitás neve** | Igen | Az entitás, amelyben létre szeretné hozni a rekordot, például "feladatok" |
   | **Tárgy** | Igen, az ebben a példában kiválasztott entitás alapján | A feladat céljával kapcsolatos rövid leírás |
   ||||

   1. A **Tárgy** tulajdonságnál adja meg a következő szöveget egy záró szóközzel:

      `Follow up with new lead:`

   1. Tartsa a mutatót a **Tárgy** mezőben, hogy a dinamikus tartalmak listája látható maradjon.
   
   1. A listában a **rekord létrehozásakor** szakaszban válassza ki a feladat rekordba felvenni kívánt trigger kimeneteket, például:

      ![Válassza ki a feladat rekordban használni kívánt trigger-kimeneteket](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Trigger kimenete | Leírás |
      |----------------|-------------|
      | **Keresztnév** | Az első név a rekordban, amelyet elsődleges partnerként kíván használni a feladatban. |
      | **Vezetéknév** | A tevékenység rekordjában az elsődleges partnerként használandó utolsó név a vezető rekordban |
      | **Leírás** | A feladatsorba foglalandó egyéb kimenetek, például e-mail-cím és munkahelyi telefonszám |
      |||

   Ha elkészült, a művelet a következő példához hasonló lehet:

   ![Az "új rekord létrehozása" művelet befejeződött](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

1. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget. A logikai alkalmazás teszteléséhez hozzon létre egy új "leads" rekordot.

## <a name="trigger-only-on-updated-attributes"></a>Csak a frissített attribútumok esetében aktiválódik

A rekordok frissítésekor futó eseményindítók esetén, például **Ha a rekord frissítésekor művelet történik** , használhat szűrési attribútumokat úgy, hogy a logikai alkalmazás csak akkor fusson, amikor a megadott attribútumok frissülnek. Ez a funkció segít megelőzni a szükségtelen logikai alkalmazások futtatását.

1. Az triggerben az **új paraméter hozzáadása** listából válassza az **attribútumok szűrők**elemet.

   !["Attribute Filters" tulajdonság hozzáadása](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Minden **attribútum szűrő elemnél**válassza ki a frissítések figyeléséhez használni kívánt attribútumot, például:

   !["Attribute Filters" tulajdonság hozzáadása](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Rekordok listázása szűrő alapján

A rekordokat visszaadó műveletek, például a **rekordok listázása** művelettel olyan ODATA-lekérdezést használhat, amely a megadott szűrő alapján visszaadja a rekordokat. A művelet például csak az aktív fiókok rekordjait sorolja fel.

1. A műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **szűrő lekérdezése** tulajdonságot.

   !["Lekérdezés szűrése" tulajdonság hozzáadása](./media/connect-common-data-service/list-records-action-filter-query.png)

1. A **szűrő lekérdezési** tulajdonságában, amely most megjelenik a műveletben, írja be ezt a ODATA-szűrő lekérdezést:`statuscode eq 1`

   ![Adja meg a rekordok szűrésére szolgáló ODATA-szűrési lekérdezést](./media/connect-common-data-service/list-records-action-filter-query-value.png)

További információ `$filter` a rendszerlekérdezési lehetőségekről: [Common Data Service-Filter Results](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results).

## <a name="list-records-based-on-an-order"></a>Rekordok listázása rendelés alapján

A rekordokat visszaadó műveletek, például a **rekordok listázása** művelettel olyan ODATA-lekérdezést használhat, amely egy megadott sorrendben adja vissza a rekordokat, amelyek a művelet által visszaadott rekordoktól függően változnak. Megadhatja például, hogy a művelet a fiók neve alapján sorolja fel a rekordokat.

1. A műveletben nyissa meg az **új paraméter hozzáadása** listát, és válassza ki az **Order by** tulajdonságot.

   !["Order by" tulajdonság hozzáadása](./media/connect-common-data-service/list-records-action-order-by.png)

1. A műveletben most megjelenő **Order by** tulajdonsággal írja be ezt a ODATA-szűrési lekérdezést:`name`

   ![Adja meg a ODATA szűrő lekérdezését a rekordok rendezéséhez](./media/connect-common-data-service/list-records-action-order-by-value.png)

További információ `$orderby` a rendszerlekérdezési lehetőségekről: [Common Data Service Order Results](https://docs.microsoft.com/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results).

## <a name="field-data-types"></a>Mező adattípusai

Függetlenül attól, hogy manuálisan adott-e meg egy értéket, vagy kijelöl egy értéket a dinamikus tartalmak listájából egy trigger vagy művelet mezőjében, az érték adattípusának meg kell egyeznie a mező kötelező adattípusával.

Ez a táblázat néhány mezőtípus és azon adattípusok leírását írja le, amelyekre ezek a mezők az értékekhez szükségesek.

| Mező | Adattípus | Leírás |
|-------|-----------|-------------|
| Szövegmező | Egysoros szöveg | Egy olyan szöveges vagy dinamikus tartalmat igényel, amely a szöveg adattípussal rendelkezik, például a következő tulajdonságokkal: <p><p>- **Leírás** <br>- **Kategória** |
| Egész szám mező | Egész szám | Az egész szám adattípusú egész vagy dinamikus tartalmat igényel, például ezek a tulajdonságok: <p><p>- **Készültségi százalék** <br>- **Időtartama** |
| Dátum mező | Dátum és idő | A (z) hh/nn/YYY formátumú vagy a dátum adattípusú dinamikus tartalomra vonatkozó dátumot igényel, például ezek a tulajdonságok: <p><p>- **Létrehozva:** <br>- **Kezdési dátum** <br>- **Tényleges indítás** <br>- **Tényleges Befejezés** <br>- **Esedékesség dátuma** |
| Egy másik entitási rekordra hivatkozó mező | Elsődleges kulcs | A rekord AZONOSÍTÓját (például GUID) és egy keresési típust is igényli, ami azt jelenti, hogy a dinamikus tartalom lista értékei nem működnek, például ezek a tulajdonságok: <p><p>- **Tulajdonos**: érvényes felhasználói azonosítónak vagy Team Record azonosítónak kell lennie. <br>- **Tulajdonos típusa**: olyan keresési típusnak kell lennie `systemusers` , `teams`mint a vagy a. <p><p>- **Kapcsolatban**: érvényes rekordazonosítónek kell lennie, például egy fiók azonosítójának vagy egy kapcsolattartói rekord azonosítójának. <br>- A **típushoz kapcsolódóan a következő**keresési típusnak `accounts` kell `contacts`lennie:, vagy, illetve. <p><p>- **Ügyfél**: érvényes rekordazonosító, például fiókazonosító vagy kapcsolattartói rekord azonosítója. <br>- **Ügyfél típusa**: a keresési típusnak (például `accounts` vagy `contacts`) kell lennie. |
||||

Ez a példa azt szemlélteti, hogy az **új rekord létrehozása** művelet hogyan hoz létre egy új "Tasks" rekordot, amely más entitás-rekordokhoz van társítva, konkrétan egy felhasználói rekorddal és egy fiók rekorddal. A művelet az adott entitás rekordjaihoz tartozó azonosítókat és keresési típusokat adja meg a releváns tulajdonságok várt adattípusának megfelelő értékek használatával.

* A **tulajdonos** tulajdonság alapján, amely megadja a felhasználói azonosítót és a **tulajdonos Type** tulajdonságát, amely megadja a `systemusers` keresési típust, a művelet az új "feladatok" rekordot társítja egy adott felhasználóval.

* A **kapcsolódó** tulajdonság alapján, amely meghatározza a rekord azonosítóját, és a **Regarding Type** `accounts` keresési típust meghatározó Type tulajdonságot, a művelet társítja az új "feladatok" rekordot egy adott fiókkal.

![Azonosítókkal és keresési típusokkal társított "feladatok" rekord létrehozása](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötők kivágási leírásán alapuló technikai információk, például eseményindítók, műveletek, korlátok és egyéb részletek: az [összekötő hivatkozási lapja](https://docs.microsoft.com/connectors/commondataservice/).

## <a name="next-steps"></a>További lépések

* További tudnivalók [a Azure Logic apps-összekötők](../connectors/apis-list.md) használatáról