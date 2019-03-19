---
title: Azure-elszámolással és a cost management költségvetési eset |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure automation leállíthatja a virtuális gépeket külön költségvetési küszöbértékek alapján.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: erikre
ms.openlocfilehash: c92789c12f4454f5d76590e5323b78223b49c97f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113061"
---
# <a name="manage-costs-with-azure-budgets"></a>Költségek kezelése az Azure Budgetsszel

Költségkontroll egy kritikus összetevő, hogy a felhőben a befektetésének értékét. Költség látható-e, jelentéseket és vezénylési költségalapú amelyeknél kritikus fontosságú üzleti folyamatos műveleteivel számos forgatókönyv közül választhat. [Az Azure Cost Management API-k](https://docs.microsoft.com/rest/api/consumption/) API-kat támogatja az egyes forgatókönyvek esetében is biztosítanak. Az API-k meg a használati adatokat, így megtekintheti a részletes példány költségeket.

Költségvetés gyakran használják a költségvezérlés részeként. Költségvetés hatóköre beállítható az Azure-ban. Például szűkítheti meg a költségvetési nézet előfizetés, erőforráscsoport vagy -erőforrások gyűjteményét alapján. Használja a költségvetés API-t arra az esetre, e-mailen keresztül, a költségvetés küszöbérték elérésekor, mellett használhatja [Azure Monitor Műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) aktiválhat egy előkészített műveletkészletet egy költségvetés esemény miatt.

Az ügyfél nem kritikus fontosságú számítási feladatot futtat egy általános költségvetések forgatókönyv akkor fordulhat elő, szeretne költségvetési által kezelt és az is, egy előre jelezhető költségek kaphat, ha megnézi a havi számla. Ehhez a forgatókönyvhöz szükséges erőforrásokat az Azure-környezet részét képező egyes költségalapú vezénylését. Ebben a forgatókönyvben egy havi 1000 USD-t a az előfizetés költségvetése van beállítva. Értesítési küszöbérték is, néhány vezénylések aktiválásához vannak beállítva. Ebben a forgatókönyvben egy költség 80 %-os küszöbérték, amely le fog állni az erőforráscsoport virtuális gépeinek kezdődik **nem kötelező**. Ezt követően a 100 %-os költség küszöbértékkel összes Virtuálisgép-példány leáll.
Ez a forgatókönyv konfigurálásához a következő műveleteket fogja végrehajtani ebben az oktatóanyagban minden egyes szakaszában megadott lépéseket követve.

Ebben az oktatóanyagban szereplő műveletek engedélyezése, hogy:

- Hozzon létre egy Azure Automation-Runbook leállítani a virtuális gépek webhookok használatával.
- Hozzon létre egy Azure Logic Apps aktiválását a költségvetés küszöbérték alapján, és a megfelelő paraméterekkel a runbookot hívja meg.
- Hozzon létre egy Azure figyelő műveletcsoport, amely az Azure Logic Apps indítható, ha a költségvetés küszöbértéket lesz konfigurálva.
- Hozzon létre az Azure-költségvetési a kívánt küszöbértékeket, és műveleti csoporthoz fűznie azt.

## <a name="create-an-azure-automation-runbook"></a>Az Azure Automation-Runbook létrehozása

[Az Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) egy szolgáltatás, amely lehetővé teszi az erőforrás-felügyeleti feladatainak többsége szkriptet, és ezeket a feladatokat futtató ütemezett vagy igény szerinti. Ez a forgatókönyv részeként létrehozhat egy [Azure Automation-runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) leállítani a virtuális gépekhez használni kívánt. Használhatja a [állítsa le az Azure-V2 virtuális gép](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafikus runbookok a [katalógus](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) hozhat létre ebben a forgatókönyvben. A runbook importálása az Azure-fiókjával, és közzéteszi, azt fogja tudni a költségvetés küszöbérték elérése után állítsa le a virtuális gépek.

### <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókja hitelesítő adataival.
2. Kattintson a **erőforrás létrehozása** Azure bal felső sarkában található gomb.
3. Válassza ki **kezelőeszközök** > **Automation**.
   > [!NOTE]
   > Ha nem rendelkezik Azure-fiókra, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
4. Adja meg a fiók adatait. A **létrehozása Azure-beli futtató fiók**, válassza a **Igen** automatikusan engedélyezni a hitelesítést az Azure-hoz szükséges beállításokat.
5. Amikor végzett, kattintson a **Létrehozás** gombra az Automation-fiók üzembe helyezésének megkezdéséhez.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Runbook leállítása az Azure-V2 virtuális gép importálása

Használatával egy [Azure Automation-runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types), importálja a [állítsa le az Azure-V2 virtuális gép](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafikus forgatókönyv a katalógusból.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókja hitelesítő adataival.
2.  Nyissa meg az Automation-fiók kiválasztásával **minden szolgáltatás** > **Automation-fiókok**. Válassza ki az Automation-fiók.
3.  Kattintson a **runbookkatalógus** származó a **Folyamatautomatizálás** szakaszban.
4.  Állítsa be a **Galériaforrás** való **Script Center** válassza **OK**.
5.  Keresse meg és válassza ki a [állítsa le az Azure-V2 virtuális gép](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) Katalóguselem az Azure Portalon.
6.  Kattintson a **importálás** gombra kattintva jelenítse meg a **importálás** panelhez, és válassza **OK**. A runbook áttekintési panelen jelennek meg.
7.  A runbook az importálási folyamat befejezése után jelölje ki a **szerkesztése** megjelenítéséhez a grafikus forgatókönyv szerkesztő és a közzététel lehetőséget.

    ![Azure - grafikus forgatókönyv szerkesztése](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Kattintson a **közzététel** közzétenni a runbookot, és válassza ki a gomb **Igen** amikor a rendszer kéri. Amikor elérhetővé tesz egy forgatókönyvet, felülírja bármely meglévő közzétett verziót a piszkozattal. Ebben az esetben nincs közzétett verziója van, mivel a runbook hozott létre.

    További információ a runbook közzététele: [grafikus runbook létrehozása](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Webhookok a runbook létrehozása

Használatával a [állítsa le az Azure-V2 virtuális gép](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafikus forgatókönyv, létrehozhat két Webhookok az Azure Automationben a runbook elindításához egyetlen HTTP-kérés. Az első webhook meghívja a runbook egy 80 %-kal a költségvetés küszöbértékkel az erőforráscsoport nevét a paramétert, amely lehetővé teszi a választható virtuális gépeket le kell állítani. Második webhook ezután meghívja a runbook paraméterek nélkül (100 %-os), amely le fog állni az összes többi Virtuálisgép-példányt.

1. Az a **Runbookok** lapját a [az Azure portal](https://portal.azure.com/), kattintson a **StopAzureV2Vm** runbookot, amely megjeleníti a runbook áttekintési panelen.
2. Kattintson a **Webhook** nyissa meg a lap tetején a **Webhook hozzáadása** panelen.
3. Kattintson a **új webhook létrehozása** megnyitásához a **hozzon létre egy új webhook** panelen.
4. Állítsa be a **neve** a Webhook általi **választható**. A **engedélyezve** tulajdonság lehet **Igen**. A **lejárat** értéke nem lehet módosítani kell. Webhook-tulajdonságokkal kapcsolatos további információkért lásd: [webhook részletei](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Az URL-cím mellett kattintson a Másolás ikonjára a webhook URL-címét.
   > [!IMPORTANT]
   > Az URL-címét a nevű webhook mentése **nem kötelező** biztonságos helyen. Az URL-címet az oktatóanyag későbbi részében fogja használni. Biztonsági okokból a webhook létrehozása után nem tekinthetők meg és kérje le újra az URL-címet.
6. Kattintson a **OK** az új webhook létrehozása.
7. Kattintson a **konfigurálása paraméterek és futtatási beállítások** megtekintéséhez paraméter értékei a runbookot.
   > [!NOTE]
   > Ha a runbook rendelkezik a kötelező paraméterekhez, majd, nem lesznek létre tudja hozni a webhookot, kivéve, ha az értékek.
8. Kattintson a **OK** fogadására webhook paraméter értékét.
9. Kattintson a **létrehozás** a webhook létrehozása.
10. Ezután kövesse a fenti lépéseket, és hozzon létre egy második nevű webhook **Complete**.
    > [!IMPORTANT]
    > Mindenképpen mentse mindkét webhook URL-címek használatához az oktatóanyag későbbi részében. Biztonsági okokból a webhook létrehozása után nem tekinthetők meg és kérje le újra az URL-címet.

Most már két konfigurált webhookokat egyes elérhetők az mentett URL-címeket.

![Webhookok – nem kötelező, és teljes körű](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Most már elkészült, az Azure Automation-telepítés. A webhookok a Postman egyszerű teszt ellenőrzése, hogy működik-e a webhook tesztelheti. Következő lépésként létre kell hoznia a logikai alkalmazás vezényléshez.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Hozzon létre egy Azure Logic Apps vezényléshez

A Logic Apps segítségével hozhat létre, ütemezhet és automatizálhatja a folyamatokat, munkafolyamatok, lehetővé téve az alkalmazások, adatok, rendszerek és szolgáltatások vállalatok vagy szervezetek között. Ebben a forgatókönyvben a [logikai alkalmazás](https://docs.microsoft.com/azure/logic-apps/) hoz létre will egy kicsit több, mint a hívások létrehozott automation-webhook tegye.

Költségvetés indítható értesítést, ha egy megadott küszöbértéket állíthat be. Megadhatja, hogy értesítést kapjon több küszöbértékeit, és bemutatjuk, hogy a logikai alkalmazás arra, hogy az a küszöbérték teljesül alapján különböző műveleteket hajthat végre. Ebben a példában állítja be egy forgatókönyv, ahol néhány értesítéseket kap, az első értesítés je Pro elérte a 80 %-a költségvetés és a második értesítés akkor, ha 100 %-a költségvetés elérte-e. A logikai alkalmazás használható az erőforráscsoportban lévő összes virtuális gép leállítása. Először a **nem kötelező** küszöbértéke 80 %-os lesz visszaadva, akkor a második küszöbértéket lejár, az előfizetésben található összes virtuális gép le fog állni.

A Logic apps lehetővé teszi, hogy biztosítsa a minta-sémát a HTTP-eseményindítóval, de kell beállítani a **Content-Type** fejléc. A műveleti csoport nem rendelkezik a webhook egyéni fejlécek, mert Ön a tartalom egy külön lépésben kell elemzéséhez. Használhatja a **elemezni** művelet, és megad egy hasznosadat-minta.

### <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

A logikai alkalmazás számos műveletet végez. A következő lista ismertet magas szintű műveleteket, amelyeket a logikai alkalmazás hajtja végre:
- Felismeri a HTTP-kérés fogadásakor.
- A JSON-adatok megállapításához a küszöbérték, amely a rendszer elérte az átadott elemzése
- Feltételes utasítás segítségével ellenőrizze, hogy a küszöbérték elérte a 80 %-os vagy nagyobb költségvetési tartományban, de kevesebb, mint vagy egyenlő a 100 %-os.
    - Ha elérte a küszöbérték, küldjön egy HTTP POST használatával nevű webhook **nem kötelező**. Ez a művelet leállítja a virtuális gépek, a "Választható" csoportban.
- Feltételes utasítás segítségével ellenőrizze, hogy a küszöbérték elérte vagy túllépte a költségvetés értéke 100 %-át.
    - Ha elérte a küszöbérték, küldjön egy HTTP POST használatával nevű webhook **Complete**. Ez a művelet leállítja az összes fennmaradó virtuális gépek.

Az alábbi lépéseket, amelyek végrehajtják a fenti lépéseket a logikai alkalmazás létrehozásához szükségesek:

1.  Az a [az Azure portal](https://portal.azure.com/)válassza **erőforrás létrehozása** > **integrációs** > **logikai alkalmazás**.

    ![Azure-ban – válassza ki a logikai alkalmazás erőforrásainak](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Az a **logikai alkalmazás létrehozása** panelen adja meg a részleteket létre kell hoznia a logikai alkalmazást, válassza ki **rögzítés az irányítópulton**, és kattintson a **létrehozás**.

    ![Azure-ban – logikai alkalmazás létrehozása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Miután az Azure üzembe helyezte a logikai alkalmazást, a **Logic Apps Designerben** megnyílik, és megjeleníti a panel bemutató video- és a gyakran használt eseményindítókat.

### <a name="add-a-trigger"></a>Adjon hozzá egy triggert

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. A műveletek olyan fordulhat elő, az eseményindító aktiválása után minden lépést.

1.  A **sablonok** , a **Logic Apps Designerben** panelen válassza a **üres logikai alkalmazás**.
2.  Adjon hozzá egy [eseményindító](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) "http-kérelem" megadásával a **Logic Apps Designerben** keresőmezőbe keresése, és válassza az eseményindító nevű **kérelem – Ha egy HTTP-kérelem érkezett**.

    ![Az Azure - Logic app - Http-eseményindító](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Válassza ki **új lépés** > **művelet hozzáadása**.

    ![Az Azure - új lépés - művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Keressen rá a "JSON elemzése" kifejezésre a **Logic Apps Designerben** keresőmezőbe keresése és kiválasztása az **Adatműveletek – JSON elemzése** [művelet](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Az Azure - Logic app - hozzáadása művelet JSON elemzése](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Adja meg a "Tartalom" a **tartalom** hasznos JSON elemzése nevet, vagy használja a "Törzs" címke, a dinamikus tartalmat.
6.  Válassza ki a **Mintaadat használata séma létrehozásához** beállítást a **JSON elemzése** mezőbe.

    ![Az Azure - logikaialkalmazás - használat JSON-mintafájl adatairól séma generálásához](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Illessze be a következő JSON hasznosadat-minta a szövegmezőbe: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    A szövegmező jelenik meg a következők:

    ![Az Azure - Logic app – a JSON hasznosadat-minta](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Kattintson a **Done** (Kész) gombra.

### <a name="add-the-first-conditional-action"></a>Az első feltételes művelet hozzáadása

Feltételes utasítás segítségével ellenőrizze, hogy a küszöbérték elérte a 80 %-os vagy nagyobb költségvetési tartományban, de kevesebb, mint vagy egyenlő a 100 %-os. Ha elérte a küszöbérték, küldjön egy HTTP POST használatával nevű webhook **nem kötelező**. Ez a művelet leállítja a virtuális gépeket a **nem kötelező** csoport.

1.  Válassza ki **új lépés** > **feltétel hozzáadása**.

    ![Az Azure - Logic app - feltétel hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Az a **feltétel** párbeszédpanelen kattintson a szövegmezőbe tartalmazó **válasszon egy értéket** megjeleníthető rendelkezésre álló értékek listáját.

    ![Az Azure - Logic app - feltétel mezőben](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Kattintson a **kifejezés** a lista tetején, és a kifejezésszerkesztőbe írja be a következő kifejezést: `float()`

    ![Az Azure - Logic app - lebegőpontos kifejezés](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Válassza ki **dinamikus tartalom**, helyezze a kurzort a zárójelek (), és válassza ki **NotificationThresholdAmount** feltölti a teljes kifejezést a listából.

    A kifejezés a következő lesz:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Válassza ki **OK** , állítsa be a kifejezést.
6.  Válassza ki **nagyobb vagy egyenlő** a legördülő mezőben, a **feltétel**.
7.  Az a **válasszon egy értéket** a feltétel mezőben adja meg `.8`.

    ![Az Azure - Logic app - lebegőpontos kifejezés értékkel](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Kattintson a **Hozzáadás** > **sor hozzáadása** belül a feltétel bejelölésével vegye fel a feltétel további részét.
9.  Az a **feltétel** párbeszédpanelen kattintson a szövegmezőbe tartalmazó **válasszon egy értéket**.
10. Kattintson a **kifejezés** a lista tetején, és a kifejezésszerkesztőbe írja be a következő kifejezést: `float()`
11. Válassza ki **dinamikus tartalom**, helyezze a kurzort a zárójelek (), és válassza ki **NotificationThresholdAmount** feltölti a teljes kifejezést a listából.
12. Válassza ki **OK** , állítsa be a kifejezést.
13. Válassza ki **van kevesebb, mint** a legördülő mezőben, a **feltétel**.
14. Az a **válasszon egy értéket** a feltétel mezőben adja meg `1`.

    ![Az Azure - Logic app - lebegőpontos kifejezés értékkel](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Az a **ha igaz** jelölje ki **művelet hozzáadása**. Hozzáadja egy HTTP POST műveletet, amely a választható virtuális gépeket le fog állni.

    ![Az Azure - logikaialkalmazás - művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Adja meg **HTTP** keresse meg a HTTP-művelet, és válassza ki a **HTTP – HTTP** művelet.

    ![Az Azure - Logic app - hozzáadása HTTP-művelet](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Válassza ki **Post** , a számára a **metódus** értéket.
18. Adja meg az URL-cím nevű webhook **nem kötelező** , amely ebben az oktatóanyagban korábban létrehozott a **Uri** értéket.

    ![Az Azure - Logic app - HTTP-művelet URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Válassza ki **művelet hozzáadása** a a **ha igaz** mezőbe. E-mail-művelet, amely a választható virtuális gépek leállítása megtörtént a címzett értesítése e-mailt küldő hozzáadja.
20. Az "e-mail küldése" keresse és jelölje ki a *e-mailt* művelet az e-mail-szolgáltatás használata alapján.

    ![Az Azure - Logic app - e-mail küldése művelet](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget. Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget. Ha még nem rendelkezik kapcsolattal, a rendszer arra kéri, hogy jelentkezzen be az e-mail-fiókjába. A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

    Szüksége lesz, hogy a logikai alkalmazás az e-mailek adatai eléréséhez.

    ![Az Azure - logikai alkalmazás - hozzáférés értesítés](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Adja hozzá a **való**, **tulajdonos**, és **törzs** szöveg, amely értesíti, hogy a választható virtuális gépek leállítása megtörtént a címzett e-mail. Használja a **BudgetName** és a **NotificationThresholdAmount** dinamikus tartalom feltöltéséhez a tárgy és törzs mezőket.

    ![Az Azure - Logic app - e-mailek részletei](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>A második feltételes művelet hozzáadása

Feltételes utasítás segítségével ellenőrizze, hogy a küszöbérték elérte vagy túllépte a költségvetés értéke 100 %-át. Ha elérte a küszöbérték, küldjön egy HTTP POST használatával nevű webhook **Complete**. Ez a művelet leállítja az összes fennmaradó virtuális gépek.

1.  Válassza ki **új lépés** > **feltétel hozzáadása**.

    ![Az Azure - logikaialkalmazás - művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Az a **feltétel** párbeszédpanelen kattintson a szövegmezőbe tartalmazó **válasszon egy értéket** megjeleníthető rendelkezésre álló értékek listáját.
3.  Kattintson a **kifejezés** a lista tetején, és a kifejezésszerkesztőbe írja be a következő kifejezést: `float()`
4.  Válassza ki **dinamikus tartalom**, helyezze a kurzort a zárójelek (), és válassza ki **NotificationThresholdAmount** feltölti a teljes kifejezést a listából.

    A kifejezés a következő lesz:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Válassza ki **OK** , állítsa be a kifejezést.
6.  Válassza ki **nagyobb vagy egyenlő** a legördülő mezőben, a **feltétel**.
7.  Az a **válassza ki a egy értéke lista** adja meg a feltétel `1`.

    ![Az Azure - Logic app - feltétel értékét](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Az a **ha igaz** jelölje ki **művelet hozzáadása**. Egy HTTP POST műveletet, amely a leállítja az összes fennmaradó virtuális gépet hozzáadja.

    ![Az Azure - logikaialkalmazás - művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Adja meg **HTTP** keresse meg a HTTP-művelet, és válassza ki a **HTTP – HTTP** művelet.
10. Válassza ki **Post** , a számára a **metódus** értéket.
11. Adja meg az URL-cím nevű webhook **Complete** , amely ebben az oktatóanyagban korábban létrehozott a **Uri** értéket.

    ![Az Azure - logikaialkalmazás - művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Válassza ki **művelet hozzáadása** a a **ha igaz** mezőbe. E-mail-művelet, amely a fennmaradó virtuális gépek leállítása megtörtént a címzett értesítése e-mailt küldő hozzáadja.
13. Az "e-mail küldése" keresse és jelölje ki a *e-mailt* művelet az e-mail-szolgáltatás használata alapján.
14. Adja hozzá a **való**, **tulajdonos**, és **törzs** szöveg, amely értesíti, hogy a választható virtuális gépek leállítása megtörtént a címzett e-mail. Használja a **BudgetName** és a **NotificationThresholdAmount** dinamikus tartalom feltöltéséhez a tárgy és törzs mezőket.

    ![Az Azure - Logic app - küldése e-mailek részletei](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Kattintson a **mentése** felső részén a **Logikaialkalmazás-Tervező** panelen.

### <a name="logic-app-summary"></a>Logikai alkalmazás összegzése

Itt látható, hogy a logikai alkalmazás néz Miután elkészült. A legfontosabb forgatókönyvek, ahol nem kell semmilyen küszöbérték-alapú vezénylési, közvetlenül hívhatja az automation-szkript **figyelő** , és kihagyja a **logikai alkalmazás** . lépés.

   ![Az Azure - Logic app - teljes megtekintése](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Amikor a logikai alkalmazás mentette, egy URL-cím lett létrehozva, hogy tudnia hívni fogja. Ez az oktatóanyag következő szakaszában az URL-címet fogja használni.

## <a name="create-an-azure-monitor-action-group"></a>Az Azure Monitor műveletcsoport létrehozása

Műveletcsoport gyűjteménye, Ön által meghatározott értesítési beállításokat. Riasztást vált ki, ha egy adott műveletcsoport kapnak a riasztás által bejelentett. Az Azure riasztást proaktív módon kiváltja az értesítést a megadott feltételek alapján, és műveletet lehetőséget biztosít. Riasztás adatokat használhatja a több forrásból, beleértve a metrikák és naplók.

Műveletcsoportok a egyetlen végpontot, amely integrálva lesz a költségvetést. Értesítéseket állíthat be csatornákat számos, de ez az oktatóanyag korábbi részében létrehozott ebben a forgatókönyvben a logikai alkalmazás összpontosítanak.

### <a name="create-an-action-group-in-azure-monitor"></a>Műveletcsoport létrehozása az Azure monitorban

A műveleti csoport létrehozásakor, a logikai alkalmazás, amely ebben az oktatóanyagban korábban létrehozott fog mutatni.

1.  Ha Ön még nem jelentkezett be a [az Azure portal](https://portal.azure.com/), jelentkezzen be, és válassza ki **minden szolgáltatás** > **figyelő**.
2.  Válassza ki **műveletek csoportok** származó a **beállítás** szakaszban.
3.  Válassza ki **műveletcsoport hozzáadása** származó a **Műveletcsoportok** panelen.
4.  Adja hozzá, és ellenőrizze a következő elemeket:
    - Műveletcsoport neve
    - Rövid név
    - Előfizetés
    - Erőforráscsoport

    ![Az Azure - Logic app - műveletcsoport hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  Belül a **műveleti csoport hozzáadása** ablaktáblán LogicApp művelet hozzáadása. Nevezze el a művelet **költségvetés-BudgetLA**. Az a **logikai alkalmazás** panelen válassza a **előfizetés** és a **erőforráscsoport**. Ezután válassza ki a **logikai alkalmazás** , amely ebben az oktatóanyagban korábban létrehozott.
6.  Kattintson a **OK** beállítani a logikai alkalmazást. Ezután válassza ki **OK** a a **műveleti csoport hozzáadása** panelen a műveletcsoport létrehozásához.

Végzett a támogató összetevők hatékonyan előkészíthető a költségvetés szükséges. Most ehhez szüksége a költségvetés létrehozása és konfigurálása, hogy a létrehozott műveletcsoport használja.

## <a name="create-the-azure-budget"></a>Az Azure-költségvetési létrehozása

Az Azure portal használatával létrehozhat költségvetési a [költségvetés funkció](../cost-management/tutorial-acm-create-budgets.md) a Cost Management. Vagy létrehozhat költségvetési REST API-k, Powershell-parancsmagok használatával vagy a CLI használatával. Az alábbi eljárás a REST API-t használ. A REST API hívásának előtt szüksége lesz egy engedélyezési jogkivonatot. Hozzon létre egy engedélyezési jogkivonatot, használhatja a [ARMClient](https://github.com/projectkudu/ARMClient) projekt. A **ARMClient** lehetővé teszi, hogy végezzen hitelesítést az Azure Resource Manager és az API-k hívásához egy token beszerzéséhez.

### <a name="create-an-authentication-token"></a>Hitelesítési jogkivonat létrehozása

1.  Keresse meg a [ARMClient](https://github.com/projectkudu/ARMClient) projektet a Githubon.
2.  Klónozza a tárházat a helyi másolata.
3.  Nyissa meg a projektet a Visual Studióban és építse fel.
4.  Ha a build sikeres, a végrehajtható fájlt kell lennie a *\bin\debug* mappát.
5.  Futtassa a ARMClient. Nyisson meg egy parancssort, és keresse meg a *\bin\debug* mappát a projekt gyökérkönyvtárában.
6.  A bejelentkezéshez és hitelesítéshez, adja meg a következő parancsot a parancssorba:<br>
    `ARMClient login prod`
7.  Másolás a **előfizetési guid** a kimenetből.
8.  Egy engedélyezési jogkivonatot másolja a vágólapra, adja meg a következő parancsot a parancssorba, de használja a fenti lépésben másolt előfizetés-azonosító: <br>
    `ARMClient token <subscription GUID from previous step>`

    A fenti lépés befejezése után jelenik meg a következőket:<br>
    **Jogkivonat sikeresen másolja a vágólapra.**
9.  Mentse a token használható ez az oktatóanyag következő részében található lépésekkel.

### <a name="create-the-budget"></a>A költségvetés létrehozása

Ezután konfigurálhatja **Postman** költségvetés létrehozása az Azure Consumption REST API-k meghívásával. Postman egy API-fejlesztési környezetben. Postman környezet és a gyűjtemény fájlokat fog importálja. A gyűjtemény, amely az Azure Consumption REST API-k hívása a HTTP-kérések csoportosított definíciókat tartalmazza. A környezeti változókat, amelyek a gyűjteményt tartalmaz.

1.  Töltse le és nyissa meg a [Postman REST-ügyfél](https://www.getpostman.com/) a REST API-k végrehajtására.
2.  A Postman hozzon létre egy új kérelmet.

    ![Postman - új kérelem létrehozása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Mentse az új kérés egy gyűjteményt, hogy az új kérés nem rajta.

    ![Postman - az új kérés mentése](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Módosítsa a kérés egy `Get` , egy `Put` művelet.
5.  Mi a következő URL-cím módosítása `{subscriptionId}` az a **előfizetés-azonosító** ennek az oktatóanyagnak az előző szakaszban használt. Is, módosítsa az URL-cím tartalmazza az "SampleBudget" értéket `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Válassza ki a **fejlécek** Postman lapján.
7.  Vegyen fel egy új **kulcs** "Engedélyezés" nevű.
8.  Állítsa be a **érték** a token, amely az utolsó szakaszban végén a ArmClient használatával hoztuk létre.
9.  Válassza ki **törzs** Postman lapján.
10. Válassza ki a **nyers** beállítás gombra.
11. Be a szövegmezőbe, illessze be az alábbi minta költségvetés-definíciót, azonban le kell cserélnie a **subscriptionid**, **budgetname**, és **actiongroupname** paramétereket a előfizetés-azonosító egy egyedi nevet a költségvetést, és az URL-cím és a kérelem törzsében is létrehozott a műveletcsoport neve:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Nyomja meg **küldése** a kérelem elküldéséhez.

Most már minden a helyére, meg kell hívnia a [API környezetet](https://docs.microsoft.com/rest/api/consumption/budgets). A költségvetéshez API-referencia további részleteket is tartalmaz az adott kérelemhez, többek között a következőket:
    - **budgetName** – több költségvetése támogatottak.  Költségvetés egyedieknek kell lenniük.
    - **kategória** -kell lennie, vagy **költség** vagy **használati**. Az API támogatja mind felhasználási és költségkeret.
    - **timeGrain** -egy havi, negyedéves és éves költségvetést. Az összeg alaphelyzetbe állítja az időszak végén.
    - **szűrők** -szűrők lehetővé teszik a kijelölt hatókörön belüli erőforrások egy meghatározott készletének a költségvetés szűkítheti. Például egy szűrő lehet egy előfizetési szintű költségvetés-erőforráscsoportok gyűjteménye.
    - **értesítések** – azt határozza meg az értesítés részletei és küszöbértékeit. Több küszöbértékek beállítása, és adjon meg egy e-mail-cím vagy a műveletcsoport is megkapja az értesítéseket.

## <a name="summary"></a>Összegzés

Az oktatóanyag utasításait követve, a segítségével megtanulta:
- Hogyan lehet leállítani a virtuális gépek egy Azure Automation-Runbook létrehozása.
- Hogyan hozhat létre egy Azure logikai alkalmazást, amely akkor aktiválódik, a költségvetés küszöbértékek alapján, és a megfelelő paraméterekkel a kapcsolódó runbookot hívja meg.
- Hogyan hozhat létre az Azure Monitor műveletcsoport, amely az Azure Logic Apps indítható, ha a költségvetés küszöbértéket lett konfigurálva.
- Hogyan hozzon létre az Azure-költségvetési a kívánt küszöbértékeket és műveleti csoporthoz fűznie azt.

Most már teljes körűen használható költségvetési az előfizetéshez, amely fog állni a virtuális gépek Ha egyenlege eléri a beállított költségvetés küszöbértékeket.

## <a name="next-steps"></a>További lépések

- Az Azure számlázási forgatókönyvekkel kapcsolatos további információkért lásd: [számlázás és a cost management automation forgatókönyvek](billing-cost-management-automation-scenarios.md).
