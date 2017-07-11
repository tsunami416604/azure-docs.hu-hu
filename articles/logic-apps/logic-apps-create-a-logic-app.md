---
title: "Az első munkafolyamat létrehozása felhőalapú alkalmazások és felhőszolgáltatások között – Azure Logic Apps | Microsoft Docs"
description: "Üzleti folyamatok automatizálása a rendszerintegrációs és vállalati alkalmazásintegrációs (EAI) forgatókönyvekhez Azure Logic Appsban létrehozott és futtatott munkafolyamatok segítségével"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "munkafolyamat, felhőalapú alkalmazások, felhőszolgáltatások, üzleti folyamatok, rendszerintegráció, vállalati alkalmazásintegráció, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: LADocs; jehollan; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 204bf123509729b60b55c306050cef54aa7fecc5
ms.contentlocale: hu-hu
ms.lasthandoff: 05/17/2017

---

<a id="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services" class="xliff"></a>

# Az első logikai alkalmazás munkafolyamatának létrehozása folyamatok automatizálásához a felhőalapú alkalmazások és felhőszolgáltatások között

Könnyebben és gyorsabban automatizálhatja az üzleti folyamatokat kód megírása nélkül is, ha az [Azure Logic Apps](logic-apps-what-are-logic-apps.md) segítségével hoz létre és futtat munkafolyamatokat. Az első példa bemutatja, hogyan hozhat létre alapszintű logikaialkalmazás-munkafolyamatot, amely egy webhely RSS-hírcsatornájában keres új tartalmat. Ha új elemek jelennek meg a webhely hírcsatornájában, a logikai alkalmazás e-mailt küld az Outlook- vagy Gmail-fiókból.

Logikai alkalmazás létrehozásához és futtatásához a következő elemekre van szükség:

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

  Az Azure-előfizetés a logikaialkalmazás-használat számlázására szolgál. Ismerje meg, hogyan működik a [használatmérés](../logic-apps/logic-apps-pricing.md) és a [díjszabás](https://azure.microsoft.com/pricing/details/logic-apps) az Azure Logic Apps esetében.

A példához a következő elemek is szükségesek:

* Outlook.com-, Office 365 Outlook- vagy Gmail-fiók

    > [!TIP]
    > Ha rendelkezik személyes [Microsoft-fiókkal](https://account.microsoft.com/account), akkor Outlook.com-fiókja van. Egyéb esetben, ha rendelkezik Azure munkahelyi vagy iskolai fiókkal, **Office 365 Outlook**-fiókja van.

* Egy webhely RSS-hírcsatornájára mutató hivatkozás. Ez a példa a [CNN.com webhely legfontosabb híreit tartalmazó RSS-hírcsatornáját használja](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

<a id="add-a-trigger-that-starts-your-workflow" class="xliff"></a>

## Eseményindító hozzáadása a munkafolyamat indításához

Az [*eseményindító*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) egy olyan esemény, amely elindítja a logikai alkalmazás munkafolyamatát, valamint az első elem, amelyre a logikai alkalmazásnak szüksége van.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal")

2. A bal oldali menüből válassza az **Új** > **Vállalati integráció** > **Logikai alkalmazás** elemet az alább látható módon:

     ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > Vagy válassza az **Új** elemet, írja be a keresőmezőbe a `logic app` kifejezést, majd nyomja le az Enter billentyűt. Válassza a **Logikai alkalmazás** > **Létrehozás** elemet.

3. Nevezze el a logikai alkalmazást, majd válassza ki az Azure-előfizetését. Most hozzon létre vagy válasszon ki egy Azure-erőforráscsoportot, amely segít a kapcsolódó Azure-erőforrások rendszerezésében és kezelésében. Végül válassza ki az adatközpont helyét a logikai alkalmazás futtatásához. Ha elkészült, válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** elemet.

     ![Logikai alkalmazás részletei](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > Amikor kiválasztja a **Rögzítés az irányítópulton** elemet, a logikai alkalmazás az üzembe helyezés után megjelenik az Azure irányítópulton, és automatikusan megnyílik. Ha a logikai alkalmazás nem jelenik meg az irányítópulton, a **Minden erőforrás** csempén válassza a **Több megjelenítése** lehetőséget, majd válassza ki a logikai alkalmazást. Vagy a bal oldali menüben válassza a **További szolgáltatások** elemet. A **Vállalati integráció** résznél válassza a **Logikai alkalmazások** elemet, majd válassza ki a logikai alkalmazást.

4. A logikai alkalmazás első megnyitásakor, a Logikaialkalmazás-tervező megjeleníti a kezdéshez használható sablonokat. Jelen esetben válassza az **Üres logikai alkalmazás** elemet, így az alapoktól építheti fel saját logikai alkalmazását.

    Megnyílik a Logikaialkalmazás-tervező, és megjeleníti a logikai alkalmazásban használható elérhető szolgáltatásokat és *eseményindítókat*.

5. A keresőmezőbe írja be a(z) `RSS` kifejezést, majd válassza ki a következő eseményindítót: **RSS – Egy új hírcsatornaelem közzétételekor** 

    ![RSS-eseményindító](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Írja be a webhely követni kívánt RSS-hírcsatornájára mutató hivatkozást. 

     Módosíthatja a **Gyakoriság** és az **Időköz** értékeket is. 
     Ezek a beállítások határozzák meg, hogy a logikai alkalmazás milyen gyakran keres új elemeket és ad vissza minden megtalált elemet az adott időtartományon belül.

     Ebben a példában minden nap ellenőrizni fogja a CNN webhelyén közzétett legfontosabb híreket.

     ![Eseményindító beállítása RSS-hírcsatornával, gyakorisággal és időközzel](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Egyelőre mentse a munkáját. (A tervező parancssávján válassza a **Mentés** elemet.)

   ![A logikai alkalmazás mentése](media/logic-apps-create-a-logic-app/save-logic-app.png)

   Mentéskor a rendszer élesíti a logikai alkalmazást, de az jelenleg csak a megadott RSS-hírcsatornában keres új elemeket. 
   A példa hasznosabbá tételéhez adjunk hozzá egy műveletet, amelyet a logikai alkalmazás az eseményindító aktiválása után hajt végre.

<a id="add-an-action-that-responds-to-your-trigger" class="xliff"></a>

## Az eseményindítóra válaszoló művelet hozzáadása

A [*művelet*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) a logikai alkalmazás munkafolyamata által végrehajtott feladat. Miután hozzáadott egy eseményindítót a logikai alkalmazáshoz, hozzáadhat egy műveletet is, amely további műveleteket végez el az eseményindító által előállított adatokkal. A példában egy olyan műveletet adunk hozzá, amely e-mailt küld, ha új elem jelenik meg a webhely RSS-hírcsatornájában.

1. A tervezőben az eseményindító területén válassza az **Új lépés** > **Művelet hozzáadása** elemet az alább látható módon:

   ![Művelet hozzáadása](media/logic-apps-create-a-logic-app/add-new-action.png)

   A tervező megjeleníti az [elérhető összekötőket](../connectors/apis-list.md), így választhat, hogy milyen művelet legyen végrehajtva az eseményindító aktiválásakor.

2. E-mail fiókja függvényében kövesse az Outlookra vagy a Gmailre vonatkozó lépéseket.

   * E-mail küldéséhez az Outlook-fiókjából írja be a következő kifejezést a keresőmezőbe: `outlook`. A **Szolgáltatások** területen válassza az **Outlook.com** elemet személyes Microsoft-fiók, illetve az **Office 365 Outlook** elemet Azure munkahelyi vagy iskolai fiókok esetében. 
   A **Műveletek** területen válassza az **E-mal küldése** lehetőséget.

       ![Az Outlook „E-mail küldése” műveletének kiválasztása](media/logic-apps-create-a-logic-app/actions.png)

   * E-mail küldéséhez a Gmail-fiókjából írja be a következő kifejezést a keresőmezőbe: `gmail`. 
   A **Műveletek** területen válassza az **E-mal küldése** lehetőséget.

       ![Válassza a „Gmail – E-mail küldése” elemet](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. Ha a rendszer hitelesítő adatok megadását kéri, jelentkezzen be az e-mail-fiókjához tartozó felhasználónévvel és jelszóval. 

4. Adja meg a művelet részleteit (például a cél e-mail-címét), majd válassza ki az e-mailben szerepeltetni kívánt adatok paramétereit, például:

   ![Az e-mailben szerepeltetni kívánt adatok kiválasztása](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Ha az Outlookot választja, a logikai alkalmazás a következőhöz hasonlóan nézhet ki:

    ![Az elkészült logikai alkalmazás](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Mentse a módosításokat. (A tervező parancssávján válassza a **Mentés** elemet.)

6. Mostantól manuálisan futtathatja a logikai alkalmazást tesztelés céljából. A tervező parancssávján válassza a **Futtatás** elemet. Egyéb esetben beállíthat egy ütemezést, amely alapján a logikai alkalmazás ellenőrzi a megadott RSS-hírcsatornát.

   Ha a logikai alkalmazás új elemeket talál, a kiválasztott adatokat tartalmazó e-mailt küld. 
   Ha nem talál új elemeket, a logikai alkalmazás kihagyja az e-mail küldésére vonatkozó műveletet.

7. A logikai alkalmazás futtatási és eseményindítási előzményeinek figyeléséhez és ellenőrzéséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjében.

   ![A logikai alkalmazás futtatási és eseményindítási előzményeinek figyelése és ellenőrzése](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Ha nem jelennek meg a várt adatok, válassza a **Frissítés** elemet a parancssávon.

   A logikai alkalmazás állapotával, illetve a futtatási és eseményindítási előzményeivel kapcsolatos további információkért, valamint a logikai alkalmazás diagnosztizáláshoz tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](logic-apps-diagnosing-failures.md).

      > [!NOTE]
      > A logikai alkalmazás addig fut, amíg ki nem kapcsolja az alkalmazást. Az alkalmazás ideiglenes kikapcsolásához a logikai alkalmazás menüjében válassza az **Áttekintés** elemet. A parancssávon válassza a **Letiltás** elemet.

Gratulálunk, sikeresen beállította és futtatta az első logikai alkalmazását. Azt is megtanulta, milyen egyszerű a folyamatokat automatizáló munkafolyamat létrehozása, valamint a felhőalapú alkalmazások és felhőszolgáltatások integrálása – mindez kód nélkül.

<a id="manage-your-logic-app" class="xliff"></a>

## A logikai alkalmazás kezelése

Az alkalmazás kezeléséhez olyan műveleteket hajthat végre, mint például az állapot ellenőrzése, a szerkesztés, az előzmények megtekintése, a kikapcsolás vagy a logikai alkalmazás törlése.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal")

2. A bal oldali menüben válassza a **További szolgáltatások** elemet. A **Vállalati integráció** résznél válassza a **Logikai alkalmazások** elemet. Válassza ki a logikai alkalmazást. 

   A logikai alkalmazás menüjében a következő logikaialkalmazás-kezelési feladatokat találja:

   |Tevékenység|Lépések| 
   |:---|:---| 
   | Az alkalmazás állapotának, végrehajtási előzményeinek és általános információinak megtekintése| Válassza az **Áttekintés** elemet.| 
   | Az alkalmazás szerkesztése | Válassza a **Logikaialkalmazás-tervező** lehetőséget. | 
   | Az alkalmazáshoz tartozó munkafolyamat JSON-definíciójának megtekintése | Válassza a **Logikai alkalmazás kódnézete** elemet. | 
   | A logikai alkalmazáson végrehajtott műveletek megtekintése | Válassza a **Tevékenységnapló** elemet. | 
   | A logikai alkalmazás korábbi verzióinak megtekintése | Válassza a **Verziók** elemet. | 
   | Az alkalmazás ideiglenes kikapcsolása | Válassza az **Áttekintés** elemet, majd a parancssávon válassza a **Letiltás** lehetőséget. | 
   | Az alkalmazás törlése | Válassza az **Áttekintés** elemet, majd a parancssávon válassza a **Törlés** lehetőséget. Adja meg a logikai alkalmazás nevét, majd válassza a **Törlés** elemet. | 

<a id="get-help" class="xliff"></a>

## Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon az ötletekre az [Azure Logic Apps felhasználói visszajelzések oldalán](http://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

<a id="next-steps" class="xliff"></a>

## Következő lépések

*  [Feltételek hozzáadása és munkafolyamatok futtatása](../logic-apps/logic-apps-use-logic-app-features.md)
*     [A logikai alkalmazások sablonjai](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Logikai alkalmazások létrehozása Azure Resource Manager-sablonokból](../logic-apps/logic-apps-arm-provision.md)

