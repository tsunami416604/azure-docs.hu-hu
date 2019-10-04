---
title: Kapcsolódás a Dynamics 365hoz – Azure Logic Apps
description: Rekordok létrehozása és kezelése a Dynamics 365 (online) REST API-kkal és Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
manager: carmonm
ms.reviewer: estfan, LADocs
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: ce83e6b1847a8f08467cb7877e517bdaace27953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051009"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Dynamics 365-rekordok kezelése a Azure Logic Apps

Az Azure Logic Appsszel és a Dynamics 365-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre a Dynamics 365-ben található rekordok alapján. A munkafolyamatok rekordokat hozhatnak létre, elemeket frissíthetnek, rekordokat adhatnak vissza, és további feladatokat is elvégezhetnek Dynamics 365-fiókjában. Olyan műveleteket is felvehet a logikai alkalmazásokban, amelyek válaszokat kapnak a Dynamics 365-ből, és más műveletek számára elérhetővé teszik a kimenetet. Ha például egy elem frissül a Dynamics 365-ben, akkor e-mailt küldhet az Office 365 használatával.

Ez a cikk bemutatja, hogyan hozhat létre olyan logikai alkalmazást, amely létrehoz egy feladatot a Dynamics 365-ben, amikor új érdeklődői rekord jön létre a Dynamics 365-ben.
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a következőt: [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Dynamics 365-fiók](https://dynamics.microsoft.com)

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, amelyen el szeretné érni a Dynamics 365-fiókját. A logikai alkalmazás Dynamics 365-triggerrel való elindításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

## <a name="add-dynamics-365-trigger"></a>Dynamics 365-trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Először vegyen fel egy Dynamics 365-eseményindítót, amely akkor következik be, amikor új érdeklődői rekord jelenik meg a Dynamics 365-ben.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. A keresőmezőbe írja be a "Dynamics 365" kifejezést a szűrőként. Ebben a példában az eseményindítók listáról válassza ki ezt az eseményindítót: **Rekord létrehozásakor**

   ![Trigger kiválasztása](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Ha a rendszer felszólítja, hogy jelentkezzen be a Dynamics 365-be, jelentkezzen be most.

1. Adja meg az alábbi trigger részleteit:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Szervezet neve** | Igen | A szervezet Dynamics 365-példányának neve, amely figyeli, például "contoso" |
   | **Entitás neve** | Igen | A figyelni kívánt entitás neve, például "leads" | 
   | **Gyakoriság** | Igen | A triggerhez kapcsolódó frissítések keresésekor használt időegység |
   | **Intervallum** | Igen | Ennyi másodperc, perc, óra, nap, hét vagy hónap elteltével adja meg a következő ellenőrzését |
   ||| 

   ![Trigger részletei](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365 művelet hozzáadása

Most adja hozzá a Dynamics 365 műveletet, amely létrehoz egy feladatot a rekordhoz az új érdeklődő rekordhoz.

1. Az trigger alatt válassza az **új lépés**lehetőséget.

1. A keresőmezőbe írja be a "Dynamics 365" kifejezést a szűrőként. A műveletek listából válassza ki ezt a műveletet: **Új rekord létrehozása**

   ![Művelet kiválasztása](./media/connectors-create-api-crmonline/select-action.png)

1. Adja meg a következő művelet részleteit:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Szervezet neve** | Igen | A Dynamics 365-példány, amelyben létre kívánja hozni a rekordot, és nem feltétlenül azonos példánynak kell lennie az triggerben, de ebben a példában a "contoso". |
   | **Entitás neve** | Igen | Az entitás, amelyben létre szeretné hozni a rekordot, például "feladatok" |
   | | |

   ![Művelet részletei](./media/connectors-create-api-crmonline/action-details.png)

1. Ha a **Tárgy** mező megjelenik a műveletben, kattintson a **Tárgy** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. Ebből a listából válassza ki az új érdeklődői rekordhoz tartozó feladat rekordban szerepeltetni kívánt mezőértékeket:

   | Mező | Leírás |
   |-------|-------------|
   | **Vezetéknév** | Az érdeklődő utolsó neve a rekordban elsődleges partnerként |
   | **A témakör** | A rekordban lévő érdeklődő leíró neve |
   | | |

   ![Feladat rekordjának részletei](./media/connectors-create-api-crmonline/create-record-details.png)

1. A tervező eszköztárán válassza a **Mentés** lehetőséget a logikai alkalmazáshoz. 

1. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget.

   ![Logikai alkalmazás futtatása](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Most hozzon létre egy érdeklődői rekordot a Dynamics 365-ben, így aktiválhatja a logikai alkalmazás munkafolyamatát.

## <a name="add-filter-or-query"></a>Szűrő vagy lekérdezés hozzáadása

Ha meg szeretné határozni, hogyan kell szűrni a Dynamics 365 műveleteit, válassza a művelet **Speciális beállítások megjelenítése** elemét. Ezután hozzáadhat egy szűrőt vagy rendezést lekérdezéssel.
Egy szűrő lekérdezéssel például csak az aktív fiókokat lehet beolvasni, és megrendelni a rekordokat a fiók neve alapján. Ehhez a feladathoz kövesse az alábbi lépéseket:

1. A **szűrő lekérdezése**szakaszban adja meg ezt a OData-szűrési lekérdezést:`statuscode eq 1`

2. A **sorrend szerint beállításnál**a dinamikus tartalmak listájának megjelenésekor válassza a **fióknév**lehetőséget. 

   ![Szűrő és sorrend meghatározása](./media/connectors-create-api-crmonline/advanced-options.png)

További információ: ezek a Dynamics 365 Customer engagement web API rendszerlekérdezési lehetőségek:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Ajánlott eljárások a speciális beállításokhoz

Ha egy művelet vagy trigger mezőjében megad egy értéket, az érték adattípusának meg kell egyeznie a mező típusával, hogy manuálisan adja meg az értéket, vagy a dinamikus tartalom listából válassza ki az értéket.

Ez a táblázat néhány mezőtípus és az értékek kötelező adattípusait ismerteti.

| Mezőtípus | Szükséges adattípus | Leírás | 
|------------|--------------------|-------------|
| Szövegmezők | Egysoros szöveg | Ezeknek a mezőknek egyetlen, szöveg típusú szöveggel vagy dinamikus tartalommal kell rendelkezniük. <p><p>*Példa mezői*: **Leírás** és **Kategória** | 
| Egész szám mezők | Egész szám | Egyes mezők egész szám típusú vagy dinamikus tartalmat igényelnek. <p><p>*Példa mezői*: **Készültségi százalék** és **időtartam** | 
| Dátum mezők | Dátum és idő | Egyes mezőkben egy HH/NN/ÉÉÉÉ formátumú dátum vagy egy dátum típusú dinamikus tartalom szükséges. <p><p>*Példa mezői*: **Létrehozva**, **kezdési dátum**, **Tényleges kezdés**, **Tényleges befejezés**és határidő | 
| A rekordazonosító és a keresési típust igénylő mezők | Elsődleges kulcs | Néhány olyan mező, amely egy másik entitási rekordra hivatkozik, a rekord AZONOSÍTÓját és a keresési típust is igényli. | 
||||

A következő típusú mezők kibontása például a Dynamics 365-eseményindítók és a rekord-azonosítót és a keresési típust igénylő műveletek. Ez a követelmény azt jelenti, hogy a dinamikus listából kiválasztott értékek nem működnek.

| Mező | Leírás |
|-------|-------------|
| **Tulajdonos** | Érvényes felhasználói AZONOSÍTÓnak vagy Team Record AZONOSÍTÓnak kell lennie. |
| **Tulajdonos típusa** | A következők `systemusers` egyikének `teams`kell lennie: vagy. |
| **Vonatkozó** | Érvényes rekordazonosítónek kell lennie, például egy fiókazonosító vagy egy kapcsolattartói rekord azonosítója. |
| **Kapcsolódó típus** | Keresési típusnak kell lennie, például `accounts` vagy `contacts`. |
| **Ügyfél** | Érvényes rekordazonosítónek kell lennie, például egy fiókazonosító vagy egy kapcsolattartói rekord azonosítója. |
| **Ügyfél típusa** | A keresési típusnak kell lennie, `accounts` például `contacts`: vagy. |
|||

Ebben a példában az **új rekord létrehozása** nevű művelet létrehoz egy új feladatot:

![Feladat rekord létrehozása rekordazonosító és keresési típusokkal](./media/connectors-create-api-crmonline/create-record-advanced.png)

Ez a művelet hozzárendeli a feladat rekordját egy adott felhasználói AZONOSÍTÓhoz vagy a csoport rekord-AZONOSÍTÓJÁHOZ a **tulajdonos** mezőben lévő rekordazonosító és a **tulajdonos típusa** mezőben található keresési típus alapján:

![Tulajdonosi rekord azonosítója és keresési típusa](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Ez a művelet hozzáad egy olyan fiókot is, amely társítva van a **vonatkozó** mezőben és a keresési típusban a kapcsolódó **típus** mezőben hozzáadott rekordazonosító-rekorddal.

![A rekordazonosító és a keresési típus kapcsán](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Rekord AZONOSÍTÓjának keresése

A rekord AZONOSÍTÓjának megkereséséhez kövesse az alábbi lépéseket:

1. A Dynamics 365-ben nyisson meg egy rekordot, például egy fiók rekordját.

2. A műveletek eszköztáron válassza a következő lépések egyikét:

   * Válasszaki a kiugró elemet. ![popout-rekord](./media/connectors-create-api-crmonline/popout-record.png) 
   * Válassza a **hivatkozás küldése e-mailben** lehetőséget, hogy a teljes URL-címet másolja az alapértelmezett e-mail-programba.

   A rekordazonosító megjelenik az URL-cím és `%7b` `%7d` a kódolási karakterek között:

   ![Rekord AZONOSÍTÓjának keresése](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Sikertelen futtatások hibáinak megoldása

A logikai alkalmazás sikertelen lépéseinek megkereséséhez és áttekintéséhez tekintse meg a logikai alkalmazás futtatási előzményeit, állapotát, bemeneteit, kimeneteit stb.

1. A Azure Portal a logikai alkalmazás főmenüjében válassza az **Áttekintés**lehetőséget. A futtatási **Előzmények** szakaszban, amely megjeleníti a logikai alkalmazás futtatási állapotát, további információért válasszon egy sikertelen futtatást.

   ![Logikai alkalmazás futtatási állapota](./media/connectors-create-api-crmonline/run-history.png)

1. A sikertelen lépés kibontásával további részleteket tekinthet meg.

   ![Sikertelen művelet kibontása](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Tekintse át a lépés részleteit, például a bemeneteket és kimeneteket, amelyek segítségével megtalálhatja a hiba okát.

   ![Sikertelen lépés – bemenetek és kimenetek](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

A Logic apps hibaelhárításával kapcsolatos további információkért lásd: a [Logic app-hibák diagnosztizálása](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
