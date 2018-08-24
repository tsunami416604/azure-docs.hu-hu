---
title: Csatlakozhat a Dynamics 365 – Azure Logic Apps |} A Microsoft Docs
description: Hozzon létre, és a Dynamics 365 (online) REST API-k és az Azure Logic Apps-rekordok kezelése
author: Mattp123
ms.author: matp
ms.service: logic-apps
services: logic-apps
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: b1ff93f1e03e047ad5ac00259c1aa53afda0c76d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818940"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Az Azure Logic Apps Dynamics 365-rekordok kezelése

Az Azure Logic Apps és a Dynamics 365-összekötő automatizált feladatokat és a Dynamics 365-rekordok alapján munkafolyamatokat hozhat létre. A munkafolyamatok hozhat létre rekordokat, elemek, visszaadott rekordok és egyéb, a Dynamics 365-fiókjában. A logic Apps, amely választ kaphat a Dynamics 365 és más műveletek elérhetővé a kimeneti műveleteket is felvehet. Ha egy elem frissül, a Dynamics 365, például is használja az Office 365 e-mail küldése.

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely létrehoz egy feladatot a Dynamics 365, ha új érdeklődő rekord jön létre a Dynamics 365.
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* A [Dynamics 365-fiók](https://dynamics.microsoft.com)

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné a Dynamics 365-fiókjába. A logikai alkalmazást egy Dynamics 365-trigger indít el, hogy szüksége egy [üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="add-dynamics-365-trigger"></a>Dynamics 365-eseményindító hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Először adja hozzá a Dynamics 365-eseményindító, amely akkor aktiválódik, ha új érdeklődő rekord jelenik meg a Dynamics 365.

1. Az a [az Azure portal](https://portal.azure.com), az üres logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben, ha nem, nyissa meg a már.

1. A Keresés mezőbe írja be szűrőként "Dynamics 365". Ebben a példában a triggerek listájában válassza ki az eseményindító: **egy rekord létrehozásakor**

   ![Trigger kiválasztása](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Ha a Dynamics 365 bejelentkezni kéri, jelentkezzen be most.

1. Adja meg ezeket a trigger részletei:

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------| 
   | **Szervezet neve** | Igen | Figyelő, például "Contoso" a szervezet Dynamics 365-példány neve |
   | **Entitás neve** | Igen | A név az entitás figyelése, például "érdeklődők" | 
   | **Gyakoriság** | Igen | Az eseményindító kapcsolatos időközökkel használata frissítések keresésekor időegység |
   | **Intervallum** | Igen | A másodperc, perc, óra, nap, hét vagy a következő ellenőrzés előtt továbbítani hónap száma |
   ||| 

   ![A trigger részletei](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365-művelet hozzáadása

Most adja hozzá a Dynamics 365-műveletet, amely az új érdeklődő bejegyzését a feladat rekordot hoz létre.

1. Az eseményindító területén válassza a **új lépés**.

1. A Keresés mezőbe írja be szűrőként "Dynamics 365". Válassza ezt a műveletet a műveletek listájának: **új rekord létrehozása**

   ![Művelet kiválasztása](./media/connectors-create-api-crmonline/select-action.png)

1. Adja meg a művelet részletei:

   | Tulajdonság | Szükséges | Leírás | 
   |----------|----------|-------------| 
   | **Szervezet neve** | Igen | A Dynamics 365-példányt, ahol hozhat létre a rekord, amely nem az eseményindító a használt példánnyal rendelkezik, de nem ebben a példában a "Contoso" |
   | **Entitás neve** | Igen | Az entitást, ahol szeretné létrehozni a bejegyzést, például "Tevékenységek" | 
   | | |

   ![Művelet részletei](./media/connectors-create-api-crmonline/action-details.png)

1. Ha a **tulajdonos** mezőben jelenik meg a műveletet, kattintson a **tulajdonos** mezőbe, így a dinamikus tartalmak listája jelenik meg. Ebből a listából válassza ki a feladat-rekord az új érdeklődő bejegyzését társított foglalandó mezők értékét:

   | Mező | Leírás | 
   |-------|-------------| 
   | **Vezetéknév** | A Vezetéknév, az érdeklődő, a bejegyzés az elsődleges kapcsolattartási |
   | **A témakör** | A leíró neve a rekordban levő az érdeklődő | 
   | | | 

   ![A feladat részleteit](./media/connectors-create-api-crmonline/create-record-details.png)

1. A Tervező eszköztárán válassza **mentése** a logikai alkalmazás. 

1. A logikai alkalmazás manuális indításához a Tervező eszköztárán válassza **futtatása**.

   ![Logikai alkalmazás futtatása](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Mostantól új érdeklődő létrehozása a Dynamics 365, a logikai alkalmazás munkafolyamat is indíthat.

## <a name="add-filter-or-query"></a>Szűrő és lekérdezés hozzáadása

Adja meg, hogyan szűrhetők az adatok egy Dynamics 365-műveletet, válassza a **speciális beállítások megjelenítése** , hogy működés közben. Egy szűrő vagy rendelés hozzáadhatja a lekérdezés által.
Használhatja például egy Szűrőlekérdezés a csak az aktív fiókokat, és azokat a rekordokat rendezés neve. Ebben a feladatban kövesse az alábbi lépéseket:

1. A **Szűrőlekérdezés**, adja meg az OData-szűrő lekérdezés: `statuscode eq 1`

2. A **Order By**, amikor a dinamikus tartalmak listája jelenik meg, válassza ki **fióknév**. 

   ![Adja meg a szűrési és egy rendezési](./media/connectors-create-api-crmonline/advanced-options.png)

További információkért tekintse meg a Dynamics 365 Customer Engagement webes API rendszerlekérdezési beállításait: 

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Gyakorlati tanácsok a Speciális beállítások

Egy mező értékét egy műveletet vagy eseményindítót ad meg, ha az érték adattípus meg kell egyeznie a mező típusa manuálisan adja meg az értéket vagy a dinamikus tartalmú listából válassza ki a értéket.

Ez a táblázat néhány mezőt és az ezek értékeinek szükséges adattípusokat mutatja be.

| Mezőtípus | Kötelező adatok típusa | Leírás | 
|------------|--------------------|-------------|
| Szövegmezők | Egysoros szöveg | Ezek a mezők egyetlen sornyi szöveget vagy a dinamikus tartalmat, amely rendelkezik a text típus szükséges. <p><p>*Példa mezők*: **leírás** és **kategória** | 
| Egész számból álló mezők | Egész szám | Egyes mezőkben egész szám vagy az egész szám típusú dinamikus tartalom szükség. <p><p>*Példa mezők*: **készültségi szint** és **időtartama** | 
| Dátummezők | Dátum és idő | Egyes mezőkben hh/nn/éééé formátumban vagy dátum típusú dinamikus tartalom dátum szükség. <p><p>*Példa mezők*: **létrehozott a**, **Kezdődátum**, **tényleges kezdet**, **tényleges Befejezés**, és **fizetési határidő** | 
| Írja be a mezőkben a Rekordazonosító és a keresési | Elsődleges kulcs | Más entitásrekordra hivatkozó egyes mezőkben a Rekordazonosító és a egy keresési típus szükséges. | 
||||

Ezek mezőtípusok kibővítve, az alábbiakban a Dynamics 365 triggereket és műveleteket, egy rekord azonosítója és a keresési típus együttes példa mezőket. Ez a követelmény azt jelenti, hogy a dinamikus listából kiválasztott értékek nem fognak működni. 

| Mező | Leírás | 
|-------|-------------|
| **Tulajdonos** | Kell egy érvényes felhasználói azonosító bármelyik vagy csapat rekord azonosítója. | 
| **Tulajdonos típusa** | Kell lennie, vagy **rendszerfelhasználó** vagy **csapatok**. | 
| **Kapcsolódó elemek** | Kell egy érvényes Rekordazonosítót, például egy fiók azonosítója, vagy lépjen kapcsolatba a rekord azonosítója. | 
| **Kapcsolódó elemek típusa** | Például lehet egy keresési típus **fiókok** vagy **névjegyek**. | 
| **Ügyfél** | Kell egy érvényes Rekordazonosítót, például egy fiók azonosítója, vagy lépjen kapcsolatba a rekord azonosítója. | 
| **Ügyfél típusa** | Például lehet a keresési típus **fiókok** vagy **névjegyek**. | 
|||

Ebben a példában a művelet nevű **hozzon létre egy új rekordot** létrehoz egy új feladat rekordot: 

![A feladat-rekord Rekordazonosítók és keresési típusok létrehozása](./media/connectors-create-api-crmonline/create-record-advanced.png)

Ezzel a művelettel egy adott felhasználói azonosító vagy csapat Rekordazonosítója alapján a Rekordazonosító a rendeli hozzá a feladat rekord a **tulajdonosa** mező, és a keresési írja be a **Tulajdonostípus** mező:

![Tulajdonos és a keresési típusú rekorddal](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Ez a művelet is hozzáadja egy a rekord azonosítója a hozzá társított ügyfélrekordot a **kapcsolatos** mező, majd a keresési típust a **kapcsolódó elemek típusa** mező: 

![Kapcsolódó rekord és a keresési típus](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Keresse meg a rekord azonosítója

Egy rekord Azonosítójának megkereséséhez kövesse az alábbi lépéseket: 

1. Dynamics 365 nyissa meg a rekord, például egy ügyfélrekordot.

2. A Műveletek eszköztáron válassza az alábbi lépéseket:

   * Válasszon **kiugró**. ![levelezőprogramba rekord](./media/connectors-create-api-crmonline/popout-record.png) 
   * Válasszon **E-mail egy HIVATKOZÁST** másolásához a teljes URL-címet az alapértelmezett e-mail-programba.

   A rekord azonosítója megjelenik az URL-cím között a `%7b` és `%7d` kódolási karakterek:

   ![Keresse meg a rekord azonosítója](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Sikertelen futtatásának hibaelhárítása

Keresse meg, és tekintse át a sikertelen lépéseket a logikai alkalmazásban, megtekintheti a logikai alkalmazás futtatási előzmények, állapot, bemenetek, kimenetek és így tovább.

1. Válassza ki az Azure Portalon, a logikai alkalmazás főmenü **áttekintése**. Az a **futtatási előzmények** rész, amely azt mutatja, hogy a logikai alkalmazás futtatási minden állapot, válassza ki a sikertelen futtatást további információt.

   ![A logikai alkalmazás futtatásának állapota](./media/connectors-create-api-crmonline/run-history.png)

1. Bontsa ki egy hibás lépés, hogy meg tudja tekinteni a további részleteket. 

   ![Bontsa ki a sikertelen lépés](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Tekintse át a lépés részletes adatait, például a bemenetek és kimenetek, amelyek segítségével keresse meg a hiba oka.

   ![Sikertelen lépés - a bemenetek és kimenetek](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

A logic apps hibaelhárítással kapcsolatos további információkért lásd: [logikai alkalmazások hibáinak diagnosztizálása](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részleteket, például a triggereket, műveletek és -korlátok, az összekötő Swagger-fájl által leírt: a [összekötő referencialapja](/connectors/crm/). 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
