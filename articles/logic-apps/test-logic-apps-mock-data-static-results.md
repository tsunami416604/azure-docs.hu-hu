---
title: Logikai alkalmazások tesztelése próbaadatokkal
description: Statikus eredmények beállítása a logikai alkalmazások modelladatokkal való teszteléséhez az éles környezetek befolyásolása nélkül
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790278"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Logikai alkalmazások tesztelése modelladatokkal statikus eredmények beállításával

A logikai alkalmazások tesztelése során előfordulhat, hogy különböző okok miatt nem áll készen az alkalmazások, szolgáltatások és rendszerek tényleges hívására vagy elérésére. Általában ezekben a helyzetekben előfordulhat, hogy különböző feltételútvonalakat kell futtatnia, hibákat kell kikényszerítenie, konkrét üzenetválasz-törzseket kell megadnia, vagy akár meg kell próbálnia kihagyni néhány lépést. Statikus eredmények beállítása egy művelet a logikai alkalmazásban, a művelet kimeneti adatokat gúnyolódni. Statikus eredmények engedélyezése egy művelet nem futtatja a műveletet, de adja vissza a modell adatok helyett.

Ha például statikus eredményeket állít be az Outlook 365 e-mail küldési műveletéhez, a Logic Apps motor csak a statikus eredményként megadott áladatokat adja vissza, nem pedig az Outlook hívását és az e-mail küldését.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelynek statikus eredményeket szeretne beállítani

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Statikus eredmények beállítása

1. Ha még nem, az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic Apps Designer.

1. Abban a műveletben, amelyen statikus eredményeket szeretne beállítani, kövesse az alábbi lépéseket: 

   1. A művelet jobb felső sarkában válassza a három pont (*...*) gombot, majd a **Statikus eredmény**lehetőséget, például:

      ![Válassza a "Statikus eredmény" > a "Statikus eredmény engedélyezése" lehetőséget.](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Válassza **a Statikus eredmény engedélyezése**lehetőséget. A szükséges (*) tulajdonságokhoz adja meg a művelet válaszához visszaadandó álkimeneti értékeket.

      Például a HTTP-művelet hez szükséges tulajdonságok a következők:

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Állapot** | A művelet visszatérési állapota |
      | **Állapotkód** | A visszaküldandó állapotkód |
      | **Fejlécek** | A visszaadandó fejléctartalom |
      |||

      ![Válassza a "Statikus eredmény engedélyezése" lehetőséget](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Ha a hamis adatokat JavaScript objektumjelölés (JSON) formátumban szeretné megadni, válassza **a Váltás JSON módra lehetőséget** (![Válassza a "Váltás JSON módra"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)lehetőséget).

   1. Választható tulajdonságok esetén nyissa meg a **Választható mezők kiválasztása** listát, és jelölje ki a kigúnyolni kívánt tulajdonságokat.

      ![Választható tulajdonságok kiválasztása](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Ha készen áll a mentésre, válassza **a Kész gombot.**

   A művelet jobb felső sarkában a címsorban megjelenik egy tesztfőzőpohár-ikon (a![statikus eredmények](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)ikonja ), amely azt jelzi, hogy engedélyezte a statikus eredményeket.

   ![Engedélyezett statikus eredményeket megjelenítő ikon](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   A korábbi futtatások, amelyek áladatokat használó, [olvassa el a keresési fut, amely statikus eredményeket használ](#find-runs-mock-data) később ebben a témakörben.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Korábbi kimenetek újrafelhasználása

Ha a logikai alkalmazás rendelkezik egy korábbi futtatás kimenetekkel, amelyeket újra felhasználhat álkimenetként, másolhatja és beillesztheti a kimeneteket az adott futtatásból.

1. Ha még nem, az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic Apps Designer.

1. A logikai alkalmazás főmenüjében válassza **az Áttekintés**lehetőséget.

1. A **Futtatási előzmények** csoportban válassza ki a kívánt logikai alkalmazásfuttatást.

1. A logikai alkalmazás munkafolyamatában keresse meg és bontsa ki a kívánt kimeneteket tartalmazó műveletet.

1. Válassza a **Nyers kimenetek megjelenítése hivatkozást.**

1. Másolja a teljes JavaScript objektumnoton (JSON) objektumot vagy a használni kívánt alszakaszt, például a kimenetek szakaszt, vagy akár csak a fejlécek szakaszt.

1. A Statikus eredmények beállítása című részben kövesse a [művelet](#set-up-static-results) **statikus eredménymezőjének** megnyitásához szükséges lépéseket.

1. A **Statikus eredménymező** megnyitása után válasszon a következő lépés közül:

   * Teljes JSON-objektum beillesztéséhez válassza **a Váltás JSON módra** (Váltás![](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)JSON módba) lehetőséget:

     ![Válassza a "Váltás JSON módba" lehetőséget a teljes objektumhoz](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Ha csak egy JSON-szakaszt szeretne beilleszteni, a szakasz címkéje mellett válassza **a Váltás JSON módra** lehetőséget az adott szakaszhoz, például:

     ![Válassza a "Váltás JSON módra" lehetőséget a kimenetek esetén](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. A JSON szerkesztőbe illessze be a korábban másolt JSON-t.

   ![JSON mód](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Ha elkészült, válassza a **Kész** lehetőséget. Vagy a tervezőhöz való visszatéréshez válassza![a **Szerkesztőváltás mód (Váltás** szerkesztőmód)](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)lehetőséget .

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Statikus eredményeket használó futtatások keresése

A logikai alkalmazás fut történelem azonosítja a futtatások, ahol a műveletek statikus eredményeket használ. A futtatások megkereséséhez kövesse az alábbi lépéseket:

1. A logikai alkalmazás főmenüjében válassza **az Áttekintés**lehetőséget. 

1. A jobb oldali ablaktáblában az **Előzmények futtatása**csoportban keresse meg a **Statikus eredmények oszlopot.** 

   Az eredményeket tartalmazó műveleteket tartalmazó futtatások statikus **eredmények** oszlopa **engedélyezve**van, például:

   ![Előzmények futtatása - statikus eredmények oszlop](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. A statikus eredményeket használó műveletek megtekintéséhez jelölje ki azt a futtatást, amelyen a **Statikus eredmények** oszlop **engedélyezve**van.

   A statikus eredményeket használó műveletek a![tesztfőzőpohár](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)(a statikus eredmények ikonja) ikont mutatnak, például:

   ![Előzmények futtatása – statikus eredményeket használó műveletek](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Statikus eredmények letiltása

A statikus eredmények kikapcsolása nem dobja ki az értékeket a legutóbbi beállításból. Így ha legközelebb bekapcsolja a statikus eredményeket, folytathatja a korábbi értékek használatát.

1. Keresse meg azt a műveletet, amelynek helyében le szeretné tiltani a statikus kimeneteket. A művelet jobb felső sarkában válassza a tesztfőzőpohár![ikonját](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)( a statikus eredmények ikonja ).

   ![Statikus eredmények letiltása](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Válassza a Statikus eredmény >  **letiltása****kész**lehetőséget.

   ![Statikus eredmények letiltása](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referencia

Erről a beállításról az alapul szolgáló munkafolyamat-definíciókban további információt a [Statikus eredmények – Sémahivatkozás a Munkafolyamat-definíció nyelvéhez](../logic-apps/logic-apps-workflow-definition-language.md#static-results) és [a runtimeConfiguration.staticResult - Futásidejű konfigurációs beállítások című](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings) témakörben talál.

## <a name="next-steps"></a>További lépések

* További információ az [Azure Logic-alkalmazásokról](../logic-apps/logic-apps-overview.md)