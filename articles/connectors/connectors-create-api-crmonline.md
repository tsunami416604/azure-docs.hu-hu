---
title: Csatlakozás a Dynamics 365-höz
description: Rekordok létrehozása és kezelése dynamics 365 (online) REST API-kkal és Azure Logic Apps alkalmazásokkal
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789886"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Dynamics 365-rekordok kezelése az Azure Logic Appsszel

Az Azure Logic Appsszel és a Dynamics 365-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre a Dynamics 365-ben található rekordok alapján. A munkafolyamatok rekordokat hozhatnak létre, elemeket frissíthetnek, rekordokat adhatnak vissza, és további feladatokat is elvégezhetnek Dynamics 365-fiókjában. A logikai alkalmazásokban olyan műveleteket is beilleszthet, amelyek válaszokat kapnak a Dynamics 365-től, és a kimenetet más műveletek számára is elérhetővé teszik. Ha például egy elemet frissít a Dynamics 365 rendszerben, e-mailt küldhet az Office 365 használatával.

Ez a cikk bemutatja, hogyan hozhat létre egy logikai alkalmazást, amely feladatot hoz létre a Dynamics 365-ben, amikor új érdeklődőbejegyzés jön létre a Dynamics 365 rendszerben.
Ha most kezdi meg a logikai alkalmazásokat, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Dynamics 365-fiók](https://dynamics.microsoft.com)

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez hozzá szeretne férni a Dynamics 365-fiókhoz. A logikai alkalmazás Dynamics 365-eseményindítóval való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

## <a name="add-dynamics-365-trigger"></a>Dynamics 365 eseményindító hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Először adjon hozzá egy Dynamics 365 eseményindítót, amely akkor aktiválódik, amikor új érdeklődőrekord jelenik meg a Dynamics 365-ben.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic App Designerben, ha még nem nyitott.

1. A keresőmezőbe írja be szűrőként a "Dynamics 365" kifejezést. Ebben a példában az eseményindítók listában válassza ezt az eseményindítót: **Rekord létrehozásakor**

   ![Eseményindító kiválasztása](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Ha a rendszer kéri, hogy jelentkezzen be a Dynamics 365-be, jelentkezzen be most.

1. Adja meg a következő eseményindító-részleteket:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Szervezet neve** | Igen | A szervezet Dynamics 365 példányának neve, amelypéldául a "Contoso" |
   | **Entitás neve** | Igen | A figyelni endő entitás neve, például "Érdeklődők" | 
   | **Frekvencia** | Igen | Az eseményindítóval kapcsolatos frissítések keresésekor az időközökkel használható időegység |
   | **Intervallum** | Igen | A következő ellenőrzés előtt eltelt másodpercek, percek, órák, napok, hetek vagy hónapok száma |
   ||| 

   ![Eseményindító részletei](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Dynamics 365 művelet hozzáadása

Most adja hozzá a Dynamics 365 műveletet, amely tevékenységrekordot hoz létre az új érdeklődőbejegyzéshez.

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.**

1. A keresőmezőbe írja be szűrőként a "Dynamics 365" kifejezést. A műveletlistából válassza a műveletet: **Új rekord létrehozása**

   ![Művelet kijelölése](./media/connectors-create-api-crmonline/select-action.png)

1. Adja meg a következő művelet részleteit:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Szervezet neve** | Igen | A Dynamics 365 példány, ahol létre szeretné hozni a rekordot, amelynem kell, hogy ugyanaz legyen az eseményindítóban, de ebben a példában "Contoso" |
   | **Entitás neve** | Igen | Az az entitás, ahol létre szeretné hozni a bejegyzést, például "Feladatok" |
   | | |

   ![Művelet részletei](./media/connectors-create-api-crmonline/action-details.png)

1. Amikor a **Tárgy** mező megjelenik a műveletben, kattintson a **Tárgy** mezőre, hogy a dinamikus tartalomlista megjelenjen. Ebből a listából válassza ki az új érdeklődőbejegyzéshez társított tevékenységrekordba felvenni kívánt mezőértékeket:

   | Mező | Leírás |
   |-------|-------------|
   | **Vezetéknév** | Az érdeklődő vezetékneve elsődleges kapcsolattartóként a bejegyzésben |
   | **Téma** | A bejegyzésben lévő érdeklődő leíró neve |
   | | |

   ![Feladatrekord részletei](./media/connectors-create-api-crmonline/create-record-details.png)

1. A tervező eszköztárán válassza a **Mentés** a logikai alkalmazáshoz lehetőséget. 

1. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás lehetőséget.**

   ![Logikai alkalmazás futtatása](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Most hozzon létre egy érdeklődőrekordot a Dynamics 365-ben, hogy elindíthassa a logikai alkalmazás munkafolyamatát.

## <a name="add-filter-or-query"></a>Szűrő vagy lekérdezés hozzáadása

Ha meg szeretné adni, hogyan szűrheti az adatokat egy Dynamics 365 műveletben, válassza a **Speciális beállítások megjelenítése** a műveletben lehetőséget. Ezután hozzáadhat egy szűrőt vagy a sorrendet lekérdezés szerint.
Szűrőlekérdezéssel például csak az aktív fiókokat kaphatja meg, és ezeket a rekordokat fióknév szerint rendezheti. Ehhez a feladathoz hajtsa végre az alábbi lépéseket:

1. A **Lekérdezés szűrése**csoportban adja meg ezt az OData-szűrőlekérdezést:`statuscode eq 1`

2. A **Rendelés i csoportban,** amikor megjelenik a dinamikus tartalomlista, válassza a **Fióknév**lehetőséget. 

   ![Szűrő és sorrend megadása](./media/connectors-create-api-crmonline/advanced-options.png)

További információt a Dynamics 365 Customer Engagement Web API rendszerlekérdezési beállításai ban talál:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Gyakorlati tanácsok a speciális beállításokhoz

Amikor egy műveletben vagy eseményindítóban megad egy mezőt, az érték adattípusának meg kell egyeznie a mezőtípussal, függetlenül attól, hogy manuálisan adja meg az értéket, vagy kiválasztja az értéket a dinamikus tartalomlistából.

Ez a táblázat néhány mezőtípust és az értékeikhez szükséges adattípusokat ismerteti.

| Mező típusa | Szükséges adattípus | Leírás | 
|------------|--------------------|-------------|
| Szövegmezők | Egysoros szöveg | Ezekhez a mezőkhöz egyetlen sornyi szöveg vagy dinamikus tartalom szükséges, amely rendelkezik a szövegtípussal. <p><p>*Példamezők*: **Leírás** és **Kategória** | 
| Egész számból álló mezők | Egész szám | Egyes mezők egész vagy dinamikus tartalmat igényelnek, amely egész típusú. <p><p>*Példamezők*: **Készültségi százalék** és **Időtartam** | 
| Dátummezők | Dátum és idő | Egyes mezőkben mm/dd/yyyy formátumú dátumot vagy dinamikus tartalmat kell dátumtípussal rendelkező dátum. <p><p>*Példamezők*: **Létrehozás dátuma**, **Kezdő dátuma**, **Tényleges kezdés**, **Tényleges befejezés**és **Határidő** | 
| Rekordazonosítót és keresel típust is igénylő mezők | Elsődleges kulcs | Egyes, egy másik entitásbejegyzésre hivatkozó mezőkrekord-azonosítót és keresőket is igényelnek. | 
||||

Ezekre a mezőtípusokra bontva az alábbi példamezők találhatók a Dynamics 365 eseményindítóiban és műveleteiben, amelyek hez rekordazonosító és keresnek is szükség. Ez a követelmény azt jelenti, hogy a dinamikus listából kiválasztott értékek nem fognak működni.

| Mező | Leírás |
|-------|-------------|
| **Tulajdonos** | Érvényes felhasználói azonosítónak vagy csapatrekord-azonosítónak kell lennie. |
| **Tulajdonos típusa** | Vagy vagy `systemusers` `teams`. |
| **Kapcsolódó elemek** | Érvényes rekordazonosítónak kell lennie, például partnerazonosítónak vagy kapcsolattartó-rekordazonosítónak. |
| **Típusra vonatkozóan** | A következő típusú nak kell `accounts` `contacts`lennie: vagy a . |
| **Customer** (Ügyfél) | Érvényes rekordazonosítónak kell lennie, például partnerazonosítónak vagy kapcsolattartó-rekordazonosítónak. |
| **Vevő típusa** | A következő típusúnak kell `accounts` lennie: vagy `contacts`a . |
|||

Ebben a példában az **Új rekord létrehozása** nevű művelet új tevékenységrekordot hoz létre:

![Feladatrekord létrehozása rekordazonosítókkal és keresőket keresve](./media/connectors-create-api-crmonline/create-record-advanced.png)

Ez a művelet egy adott felhasználói azonosítóhoz vagy csoportbejegyzés-azonosítóhoz rendeli hozzá a feladatrekordot a **Tulajdonos** mezőben szereplő rekordazonosító és a **Tulajdonos típusa** mezőben szereplő keresgőtípus alapján:

![Tulajdonosi rekord azonosítója és keres:](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Ez a művelet hozzáad egy partnerrekordot is, amely a **Kapcsolat** mezőben hozzáadott rekordazonosítóhoz és a **Típus hoz** mezőben szereplő keresési típushoz kapcsolódik:

![A rekordazonosítóval és a keresménytípussal kapcsolatban](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Rekordazonosító keresése

Rekordazonosító megkereséséhez kövesse az alábbi lépéseket:

1. A Dynamics 365 rendszerben nyisson meg egy bejegyzést, például egy partnerbejegyzést.

2. A műveletek eszköztáron válasszon az alábbi lépések közül:

   * Válassza **a Pop Out**lehetőséget. ![előugró rekord](./media/connectors-create-api-crmonline/popout-record.png) 
   * Válassza **az E-MAIL A LINK** lehetőséget, hogy a teljes URL-t átmásolhassa az alapértelmezett levelezőprogramba.

   A rekordazonosító megjelenik az `%7b` URL-címben a és `%7d` a kódolási karakterek között:

   ![Rekordazonosító keresése](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Sikertelen futtatások hibaelhárítása

A logikai alkalmazás sikertelen lépéseinek megkereséséhez és áttekintéséhez megtekintheti a logikai alkalmazás futtatási előzményeit, állapotát, bemeneteit, kimeneteit és így tovább.

1. Az Azure Portalon a logikai alkalmazás főmenüjében válassza **az Áttekintés lehetőséget.** A **Futtatási előzmények** szakaszban, amely a logikai alkalmazás összes futtatási állapotát jeleníti meg, további információkért válasszon egy sikertelen futtatást.

   ![A logikai alkalmazás futási állapota](./media/connectors-create-api-crmonline/run-history.png)

1. Bontsa ki a sikertelen lépést, hogy további részleteket is megtekinthet.

   ![Sikertelen lépés kibontása](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Tekintse át a lépés részleteit, például a bemenetek és kimenetek, amely segíthet megtalálni a hiba oka.

   ![Sikertelen lépés - bemenetek és kimenetek](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

A logikai alkalmazások hibaelhárításáról a [Logikai alkalmazások hibáinak diagnosztizálása](../logic-apps/logic-apps-diagnosing-failures.md)című témakörben talál további információt.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő OpenAPI (korábbi nevén Swagger) fájljában leírtak szerint technikai részleteket, például eseményindítókat, műveleteket és korlátokat az [összekötő referenciaoldalán talál.](/connectors/dynamicscrmonline/)

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)
