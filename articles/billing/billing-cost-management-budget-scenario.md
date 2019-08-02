---
title: Azure számlázási és Cost Management – költségvetési forgatókönyv | Microsoft Docs
description: Ismerje meg, hogyan állíthatja le a virtuális gépeket meghatározott költségvetési küszöbértékek alapján az Azure Automation használatával.
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443474"
---
# <a name="manage-costs-with-azure-budgets"></a>Költségek kezelése az Azure Budgetsszel

A Cost Control egy kritikus összetevő, amellyel maximalizálható a Felhőbeli befektetés értéke. A folyamatos üzleti műveleteknek számos forgatókönyve van, ahol a költséghatékonyság, a jelentéskészítés és a Cost-alapú előkészítés kritikus fontosságú. [Azure Cost Management API](https://docs.microsoft.com/rest/api/consumption/) -k az egyes forgatókönyvek támogatásához API-kat biztosítanak. Az API-k használati adatokat biztosítanak, így megtekintheti a részletes példányok szintjének költségeit.

A költségkereteket általában a Cost Control részeként használják. A költségkeretek az Azure-ban is felhasználhatók. Például leszűkítheti a költségvetési nézetet előfizetés, erőforráscsoport vagy erőforrások gyűjteménye alapján. Amellett, hogy a költségvetés API használatával e-mailben értesítjük a költségvetési küszöbérték elérésekor, [Azure monitor műveleti csoportokkal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) is elindíthatják a tevékenységek koordinált készletét egy költségvetési esemény eredményeként.

A nem kritikus fontosságú munkaterhelést futtató ügyfelek közös költségvetési forgatókönyve akkor fordulhat elő, ha egy költségvetést szeretne felügyelni, és a havi számla megvizsgálása után kiszámítható költséggel is jár. Ehhez a forgatókönyvhöz az Azure-környezet részét képező erőforrásokra vonatkozó, Cost-alapú előkészítés szükséges. Ebben az esetben az előfizetés $1000-es havi költségkerete van beállítva. Emellett az értesítési küszöbértékek úgy vannak beállítva, hogy elindítsák a néhány összeszerelést. Ez a forgatókönyv 80%-os küszöbértéket vesz igénybe, így az erőforráscsoport összes virtuális gépe nem **választható**. Ezután a 100%-os költségbecslés után minden virtuálisgép-példány le lesz állítva.
A forgatókönyv konfigurálásához hajtsa végre az alábbi műveleteket az oktatóanyag egyes szakaszaiban ismertetett lépéseket követve.

Az oktatóanyagban szereplő műveletek a következőket teszik lehetővé:

- Hozzon létre egy Azure Automation Runbook a virtuális gépek webhookok használatával történő leállításához.
- Hozzon létre egy Azure Logic app-alkalmazást, amely a költségvetési küszöbérték alapján aktiválódik, és hívja meg a runbook a megfelelő paraméterekkel.
- Hozzon létre egy Azure Monitor műveleti csoportot, amely úgy lesz konfigurálva, hogy a rendszer a költségvetési küszöbérték teljesülése esetén aktiválja az Azure logikai alkalmazást.
- Hozza létre az Azure-költségvetést a kívánt küszöbértékekkel, és továbbítsa azt a műveleti csoportnak.

## <a name="create-an-azure-automation-runbook"></a>Azure Automation Runbook létrehozása

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) egy olyan szolgáltatás, amely lehetővé teszi az erőforrás-kezelési feladatok nagy részét, és a feladatok ütemezett vagy igény szerinti futtatását. Ennek a forgatókönyvnek a részeként létre fog hozni egy [Azure Automation runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) , amely a virtuális gépek leállítására szolgál. Ezt a forgatókönyvet az [Azure v2 virtuális gépek](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafikus Runbook leállítása [a](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) katalógusból című témakörben fogja használni. Ha importálja ezt a runbook az Azure-fiókjába, és közzéteszi, akkor leállíthatja a virtuális gépeket, ha eléri a költségvetési küszöbértéket.

### <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókja hitelesítő adataival.
2. Kattintson az Azure bal felső sarkában található **erőforrás létrehozása** gombra.
3. Válassza a **felügyeleti eszközök** > **automatizálása**lehetőséget.
   > [!NOTE]
   > Ha nem rendelkezik Azure-fiókkal, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
4. Adja meg a fiók adatait. Az **Azure-beli futtató fiók létrehozásához**az **Igen gombot** választva automatikusan engedélyezheti az Azure-ba történő hitelesítés egyszerűbbé tételéhez szükséges beállításokat.
5. Amikor végzett, kattintson a **Létrehozás** gombra az Automation-fiók üzembe helyezésének megkezdéséhez.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Az Azure v2 virtuális gépek runbook leállítása

[Azure Automation runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)használatával importálja az [Azure v2 virtuális gépek](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) leállítása grafikus runbook a gyűjteményből.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókja hitelesítő adataival.
2.  Nyissa meg az Automation-fiókját az **összes szolgáltatás** > **Automation-fiók**kiválasztásával. Ezután válassza ki az Automation-fiókját.
3.  Kattintson a **folyamat-automatizálás** szakasz **runbookok** -katalógus elemére.
4.  Állítsa a  katalógus forrását a **Script Center** elemre, majd kattintson **az OK gombra**.
5.  Keresse meg és jelölje be az [Azure v2 virtuális gépek](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) gyűjteményének leállítása a Azure Portalon belül elemet.
6.  Kattintson az **Importálás** gombra az **Importálás** panel megjelenítéséhez, majd válassza az **OK**gombot. Ekkor megjelenik az runbook áttekintés panel.
7.  Miután a runbook végrehajtotta az importálási folyamatot, válassza a **Szerkesztés** lehetőséget a grafikus runbook-szerkesztő és a közzétételi lehetőség megjelenítéséhez.

    ![Azure – grafikus runbook szerkesztése](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Kattintson a **Közzététel** gombra a runbook közzétételéhez, majd válassza az **Igen** lehetőséget, ha a rendszer kéri. Runbook közzétételekor minden meglévő közzétett verziót felülbírál a Piszkozat verziójával. Ebben az esetben nincs közzétett verziója, mert létrehozta a runbook.

    A runbook közzétételével kapcsolatos további információkért tekintse meg [a grafikus Runbook létrehozása](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical)című témakört.

## <a name="create-webhooks-for-the-runbook"></a>Webhookok létrehozása a runbook

Az [Azure v2 virtuális gépek](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) grafikus runbook leállításakor két webhookot fog létrehozni a runbook Azure Automation elindításához egyetlen HTTP-kérelem használatával. Az első webhook a runbook-t egy 80%-os költségvetési küszöbértékben hívja meg az erőforráscsoport nevével, amely lehetővé teszi a választható virtuális gépek leállítását. Ezután a második webhook meghívja a runbook paraméter nélkül (100%), amely leállítja az összes többi VM-példányt.

1. A Azure Portal **runbookok** lapján kattintson a [](https://portal.azure.com/)runbook áttekintés paneljén látható **StopAzureV2Vm** -runbook.
2. Kattintson  a webhook elemre az oldal tetején a Webhook **hozzáadása** panel megnyitásához.
3. Kattintson az **új webhook létrehozása** elemre az **új webhook létrehozása** panel megnyitásához.
4. Állítsa be a webhook **nevét** a **választható**értékre. Az **enabled** tulajdonságnak **Igen értékűnek**kell lennie. A **lejárat** értékét nem kell módosítani. A webhook tulajdonságaival kapcsolatos további információkért tekintse meg [a webhook részleteit](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Az URL-cím értéke mellett kattintson a másolás ikonra a webhook URL-címének másolásához.
   > [!IMPORTANT]
   > Mentse a nem **kötelező** nevű webhook URL-címét egy biztonságos helyen. Az oktatóanyag későbbi részében az URL-címet fogjuk használni. Biztonsági okokból a webhook létrehozása után nem tekintheti meg és nem kérheti le újra az URL-címet.
6. Az új webhook létrehozásához kattintson **az OK** gombra.
7. Kattintson a **Paraméterek konfigurálása és a beállítások futtatása** elemre a runbook paramétereinek megtekintéséhez.
   > [!NOTE]
   > Ha a runbook kötelező paraméterekkel rendelkezik, akkor nem tudja létrehozni a webhookot, ha nincsenek megadva értékek.
8. Az **OK** gombra kattintva fogadja el a webhook paraméter értékeit.
9. A webhook létrehozásához kattintson a **Létrehozás** gombra.
10. Ezután a fenti lépéseket követve hozzon létre egy **kész**nevű második webhookot.
    > [!IMPORTANT]
    > Ügyeljen arra, hogy a webhook URL-címeit is mentse az oktatóanyag későbbi részében való használatra. Biztonsági okokból a webhook létrehozása után nem tekintheti meg és nem kérheti le újra az URL-címet.

Most két konfigurált webhookot kell használnia, amelyek mindegyike elérhető a mentett URL-címekkel.

![Webhookok – nem kötelező és kész](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Ezzel elkészült a Azure Automation telepítővel. A webhookokat egy egyszerű Poster-teszttel tesztelve ellenőrizheti, hogy a webhook működik-e. Ezután létre kell hoznia a logikai alkalmazást a létrehozáshoz.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Azure logikai alkalmazás létrehozása az előkészítéshez

Logic Apps segítségével munkafolyamatként hozhat létre, ütemezhet és automatizálhat folyamatokat, így integrálhatja az alkalmazásokat, az adategységeket, a rendszereket és a szolgáltatásokat vállalatok és szervezetek között. Ebben a forgatókönyvben a létrehozott [logikai alkalmazás](https://docs.microsoft.com/azure/logic-apps/) valamivel többet fog tenni, mint az Ön által létrehozott Automation-webhook.

A költségvetés beállítható úgy, hogy egy adott küszöbérték teljesülése esetén értesítést indítson. Több küszöbértéket is megadhat, amelyekről értesítést kaphat, és a logikai alkalmazás bemutathatja, hogy a teljesített küszöbérték alapján hogyan végezhet el különböző műveleteket. Ebben a példában egy olyan forgatókönyvet állítunk be, amelyben néhány értesítés jelenik meg, az első értesítés a költségvetés 80%-ában érhető el, a második értesítés pedig akkor, ha a költségvetés 100%-a elérte a költségvetést. A rendszer az erőforráscsoport összes virtuális gépe leállítására használja a logikai alkalmazást. Első lépésként a **választható** küszöbérték 80%-os lesz, majd a második küszöbérték lesz elérhető, ahol az előfizetésben lévő összes virtuális gép le lesz állítva.

A Logic apps lehetővé teszi, hogy egy minta sémát biztosítson a HTTP-triggerhez, de a **Content-Type** fejlécet is be kell állítania. Mivel a műveleti csoport nem rendelkezik egyéni fejlécekkel a webhookhoz, a hasznos adatokat külön lépésben kell elemezni. Az **elemzési** műveletet fogja használni, és egy minta hasznos adattartalommal lássa el.

### <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

A logikai alkalmazás több műveletet is végrehajt. Az alábbi lista a logikai alkalmazás által elvégezhető műveletek magas szintű műveleteit tartalmazza:
- Felismeri, ha HTTP-kérés érkezik
- Az átadott JSON-adatelemzések elemzése az elért küszöbérték megállapításához
- Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte-e a költségvetési tartomány 80%-át, de nem nagyobb vagy egyenlő, mint 100%.
    - Ha elérte ezt a küszöbértéket, küldjön egy HTTP-BEJEGYZÉST a nem **kötelező**nevű webhook használatával. Ez a művelet leállítja a virtuális gépeket a "nem kötelező" csoportban.
- Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte vagy túllépte a költségvetési érték 100%-át.
    - Ha elérte a küszöbértéket, küldjön egy HTTP-BEJEGYZÉST **a nevű**webhook használatával. Ez a művelet leállítja az összes többi virtuális gépet.

A fenti lépéseket elvégző logikai alkalmazás létrehozásához a következő lépések szükségesek:

1.  A [Azure Portal](https://portal.azure.com/)válassza az **erőforrás** > létrehozása**integrációs** > **logikai alkalmazás**lehetőséget.

    ![Azure – a logikai alkalmazás erőforrásának kiválasztása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  A **logikai alkalmazás létrehozása** panelen adja meg a logikai alkalmazás létrehozásához szükséges adatokat, válassza a **rögzítés az irányítópulton**lehetőséget, majd kattintson a **Létrehozás**gombra.

    ![Azure – logikai alkalmazás létrehozása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a **Logic apps Designer** , és egy bemutató videót és gyakran használt eseményindítókat tartalmazó panelt jelenít meg.

### <a name="add-a-trigger"></a>Adjon hozzá egy triggert

Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Logic Apps-motor az eseményindító minden elindulásakor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. A műveletek az indítás után megjelenő lépések.

1.  A **Logic apps Designer** panel **sablonok** területén válassza az **üres logikai alkalmazás**lehetőséget.
2.  Egy [trigger](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) hozzáadásához írja be a "http-kérelem" kifejezést a **Logic apps Designer** keresőmezőbe, ahol megkeresheti és kiválaszthatja a Request nevű TRIGGERt, **Ha HTTP-kérés érkezik**.

    ![Azure-Logic app – http-trigger](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Válassza az **új lépés** > **művelet hozzáadása**lehetőséget.

    ![Azure – új lépés – művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Keressen rá a "JSON elemzése" kifejezésre a **Logic apps Designer** keresőmezőbe, ahol megkeresheti és kiválaszthatja az adatműveletek **– JSON-elemzés** [műveletet](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Azure-Logic app – elemzési JSON-művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Adja meg a "hasznos adatok" nevet a JSON-adatelemzéshez használt **tartalom** neveként, vagy használja a "Body" címkét a dinamikus tartalomból.
6.  Válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget az **elemzés JSON** -ban mezőben.

    ![Azure-Logic app – JSON-alapú példák használata séma létrehozásához](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Illessze be a következő JSON-minta adattartalmat a szövegmezőbe:`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    A szövegmező a következőként fog megjelenni:

    ![Azure-Logic app – a minta JSON-adattartalom](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Kattintson a **Done** (Kész) gombra.

### <a name="add-the-first-conditional-action"></a>Az első feltételes művelet hozzáadása

Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte-e a költségvetési tartomány 80%-át, de nem nagyobb vagy egyenlő, mint 100%. Ha elérte ezt a küszöbértéket, küldjön egy HTTP-BEJEGYZÉST a nem **kötelező**nevű webhook használatával. Ez a művelet leállítja a virtuális gépeket a **választható** csoportban.

1.  Válassza az **új lépés** > **feltétel hozzáadása**lehetőséget.

    ![Azure-Logic app – feltétel hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Az elérhető értékek listájának megjelenítéséhez kattintson a **feltétel** mezőben a **Válasszon értéket** tartalmazó szövegmezőre.

    ![Azure-Logic app – Condition Box](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Kattintson a lista tetején a **kifejezés** elemre, és írja be a következő kifejezést a kifejezés-szerkesztőbe:`float()`

    ![Azure-Logic app-float kifejezés](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Válassza a **dinamikus tartalom**lehetőséget, vigye a kurzort a zárójelen belül (), és a listából válassza a **NotificationThresholdAmount** lehetőséget a teljes kifejezés feltöltéséhez.

    A kifejezés a következő lesz:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  A kifejezés beállításához kattintson **az OK gombra** .
6.  A **feltétel**legördülő listájában válassza a **nagyobb vagy egyenlő** elemet.
7.  A feltétel **értékének kiválasztása** mezőben adja meg `.8`a feltételt.

    ![Azure-Logic app – lebegőpontos kifejezés értékkel](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Kattintson a feltétel mezőben a**Hozzáadás sor** hozzáadása lehetőségre a feltétel további részének hozzáadásához.  > 
9.  A **feltétel** mezőben kattintson a **Válasszon értéket**tartalmazó szövegmezőre.
10. Kattintson a lista tetején a **kifejezés** elemre, és írja be a következő kifejezést a kifejezés-szerkesztőbe:`float()`
11. Válassza a **dinamikus tartalom**lehetőséget, vigye a kurzort a zárójelen belül (), és a listából válassza a **NotificationThresholdAmount** lehetőséget a teljes kifejezés feltöltéséhez.
12. A kifejezés beállításához kattintson **az OK gombra** .
13. A Select **érték kisebb, mint** a **feltétel**legördülő listájában.
14. A feltétel **értékének kiválasztása** mezőben adja meg `1`a feltételt.

    ![Azure-Logic app – lebegőpontos kifejezés értékkel](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. A **Ha TRUE (igaz** ) mezőben válassza a **művelet hozzáadása**lehetőséget. Egy HTTP POST műveletet fog hozzáadni, amely nem kötelezően kikapcsolja a választható virtuális gépeket.

    ![Azure-Logic app – művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Adja **meg a** http-t a http-művelet megkereséséhez, majd válassza a **http – http** műveletet.

    ![Azure-Logic app – HTTP-művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Válassza a **post** lehetőséget a **metódus** értékeként.
18. Adja meg az oktatóanyag korábbi részében létrehozott  webhook URL-címét **URI** -értékként.

    ![Azure-Logic app – HTTP-művelet URI-ja](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Válassza a **művelet hozzáadása** lehetőséget a **Ha igaz** mezőben. Hozzá fog adni egy e-mail-műveletet, amely értesíti a címzettet arról, hogy a választható virtuális gépek le lettek állítva.
20. Keressen rá az "e-mail küldése" kifejezésre, és válassza ki az *e-mail küldése* műveletet a használt e-mail szolgáltatás alapján.

    ![Azure-Logic app – e-mail küldése művelet](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget. Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget. Ha még nem rendelkezik kapcsolattal, a rendszer arra kéri, hogy jelentkezzen be az e-mail-fiókjába. A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

    Engedélyeznie kell a logikai alkalmazásnak az e-mail-információk elérését.

    ![Azure-Logic app – hozzáférési nyilatkozat](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Adja hozzá az e-mailhez a **címzett,** **Tárgy**és **szövegtörzs** szövegét, amely értesíti a címzettet arról, hogy a választható virtuális gépek le vannak állítva. A **BudgetName** és a **NotificationThresholdAmount** dinamikus tartalmat használva töltse fel a tárgy és a törzs mezőket.

    ![Azure-Logic app-e-mail-részletek](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>A második feltételes művelet hozzáadása

Feltételes utasítás használatával győződjön meg arról, hogy a küszöbérték elérte vagy túllépte a költségvetési érték 100%-át. Ha elérte a küszöbértéket, küldjön egy HTTP-BEJEGYZÉST **a nevű**webhook használatával. Ez a művelet leállítja az összes többi virtuális gépet.

1.  Válassza az **új lépés** > **feltétel hozzáadása**lehetőséget.

    ![Azure-Logic app – művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Az elérhető értékek listájának megjelenítéséhez kattintson a **feltétel** mezőben a **Válasszon értéket** tartalmazó szövegmezőre.
3.  Kattintson a lista tetején a **kifejezés** elemre, és írja be a következő kifejezést a kifejezés-szerkesztőbe:`float()`
4.  Válassza a **dinamikus tartalom**lehetőséget, vigye a kurzort a zárójelen belül (), és a listából válassza a **NotificationThresholdAmount** lehetőséget a teljes kifejezés feltöltéséhez.

    A kifejezés a következő lesz:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  A kifejezés beállításához kattintson **az OK gombra** .
6.  A **feltétel**legördülő listájában válassza a **nagyobb vagy egyenlő** elemet.
7.  A feltétel **értékének kiválasztása mezőben** adja meg `1`a feltételt.

    ![Azure-Logic app-set feltétel értéke](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  A **Ha TRUE (igaz** ) mezőben válassza a **művelet hozzáadása**lehetőséget. Egy HTTP POST műveletet fog hozzáadni, amely leállítja az összes többi virtuális gépet.

    ![Azure-Logic app – művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Adja **meg a** http-t a http-művelet megkereséséhez, majd válassza a **http – http** műveletet.
10. Válassza a **post** lehetőséget a **metódus** értékeként.
11. Adja meg az oktatóanyag korábbi részében létrehozott  webhook URL-címét **URI** -értékként.

    ![Azure-Logic app – művelet hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Válassza a **művelet hozzáadása** lehetőséget a **Ha igaz** mezőben. Egy e-mail-műveletet fog hozzáadni, amely értesíti a címzettet arról, hogy a fennmaradó virtuális gépek le lettek állítva.
13. Keressen rá az "e-mail küldése" kifejezésre, és válassza ki az *e-mail küldése* műveletet a használt e-mail szolgáltatás alapján.
14. Adja hozzá az e-mailhez a **címzett,** **Tárgy**és **szövegtörzs** szövegét, amely értesíti a címzettet arról, hogy a választható virtuális gépek le vannak állítva. A **BudgetName** és a **NotificationThresholdAmount** dinamikus tartalmat használva töltse fel a tárgy és a törzs mezőket.

    ![Azure-Logic app – e-mail küldése – részletek](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Kattintson a **Save (Mentés** ) gombra a **Logic app Designer** panel tetején.

### <a name="logic-app-summary"></a>Logikai alkalmazás összegzése

A logikai alkalmazás a következőképpen néz ki: Ha elkészült. A legfontosabb forgatókönyvekben, ahol nincs szükség küszöbérték-alapú előkészítésre, közvetlenül meghívhatja az Automation-parancsfájlt a **figyelőből** , és kihagyhatja a **logikai alkalmazás** lépéseit.

   ![Azure-Logic app – teljes nézet](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

A logikai alkalmazás mentésekor a rendszer létrehoz egy URL-címet, amelyet meg tud hívni. Ezt az URL-címet az oktatóanyag következő szakaszában fogja használni.

## <a name="create-an-azure-monitor-action-group"></a>Azure Monitor műveleti csoport létrehozása

A műveleti csoport a megadott értesítési beállítások gyűjteménye. Riasztás aktiválásakor egy adott műveleti csoport értesítést kap a riasztásról. Az Azure-riasztások proaktív módon, meghatározott feltételek alapján emelnek fel egy értesítést, és lehetőséget biztosítanak a beavatkozásra. A riasztások több forrásból származó adatokat is használhatnak, beleértve a metrikákat és a naplókat is.

A műveleti csoportok az egyetlen végpont, amelybe integrálni fogja a költségvetést. Több csatornán is beállíthat értesítéseket, de ebben a forgatókönyvben az oktatóanyag korábbi részében létrehozott logikai alkalmazásra fog összpontosítani.

### <a name="create-an-action-group-in-azure-monitor"></a>Műveleti csoport létrehozása Azure Monitor

A műveleti csoport létrehozásakor az oktatóanyag korábbi részében létrehozott logikai alkalmazásra mutat.

1.  Ha még nem jelentkezett be a [Azure Portalba](https://portal.azure.com/), jelentkezzen be, és válassza a **minden szolgáltatás** > **figyelője**lehetőséget.
2.  Válassza a **műveleti csoportok** lehetőséget a **beállítás** szakaszban.
3.  A **műveleti csoportok** panelen válassza a **műveleti csoport hozzáadása** lehetőséget.
4.  Adja hozzá és ellenőrizze a következő elemeket:
    - Műveletcsoport neve
    - Rövid név
    - Subscription
    - Resource group

    ![Azure-Logic app – műveleti csoport hozzáadása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  A **műveleti csoport hozzáadása** panelen adjon hozzá egy LogicApp műveletet. Nevezze el a műveleti **költségvetést – BudgetLA**. A **logikai alkalmazás** ablaktáblán válassza ki az  előfizetést és az **erőforráscsoportot**. Ezután válassza ki az oktatóanyag során korábban létrehozott **logikai alkalmazást** .
6.  A logikai alkalmazás beállításához kattintson **az OK** gombra. Ezután kattintson az **OK gombra** a **műveleti csoport hozzáadása** panelen a műveleti csoport létrehozásához.

A költségkeret hatékony előkészítéséhez szükséges összes támogató összetevővel elkészült. Most mindössze annyit kell tennie, hogy létrehozza a költségvetést, és konfigurálja a létrehozott műveleti csoport használatára.

## <a name="create-the-azure-budget"></a>Az Azure-költségvetés létrehozása

A Azure Portal a Cost Management [költségvetési funkciójának](../cost-management/tutorial-acm-create-budgets.md) használatával hozhat létre költségvetést. Emellett a REST API-k, a PowerShell-parancsmagok vagy a parancssori felület használatával is létrehozhat költségvetést. Az alábbi eljárás a REST API használja. A REST API meghívása előtt szüksége lesz egy engedélyezési jogkivonatra. Engedélyezési jogkivonat létrehozásához használhatja a [ARMClient](https://github.com/projectkudu/ARMClient) projektet. A **ARMClient** lehetővé teszi, hogy hitelesítse magát a Azure Resource Manageron, és megkapja az API-k meghívásához szükséges tokent.

### <a name="create-an-authentication-token"></a>Hitelesítési jogkivonat létrehozása

1.  Navigáljon a [ARMClient](https://github.com/projectkudu/ARMClient) projekthez a githubon.
2.  A tárház klónozásával helyi másolatot kaphat.
3.  Nyissa meg a projektet a Visual Studióban, és hozza létre.
4.  A létrehozás sikerességét követően a végrehajtható fájlnak a *\Bin\Debug* mappában kell lennie.
5.  Futtassa a ARMClient. Nyisson meg egy parancssort, és navigáljon a *\Bin\Debug* mappához a projekt gyökeréből.
6.  A bejelentkezéshez és a hitelesítéshez írja be a következő parancsot a parancssorba:<br>
    `ARMClient login prod`
7.  Másolja az **előfizetés GUID azonosítóját** a kimenetből.
8.  Ha egy engedélyezési tokent szeretne másolni a vágólapra, írja be a következő parancsot a parancssorba, de ügyeljen arra, hogy a fenti lépésből a másolt előfizetés AZONOSÍTÓját használja: <br>
    `ARMClient token <subscription GUID from previous step>`

    A fenti lépés elvégzése után a következő jelenik meg:<br>
    **A jogkivonat sikeresen átmásolva a vágólapra.**
9.  Mentse az oktatóanyag következő szakaszában található lépésekhez használni kívánt jogkivonatot.

### <a name="create-the-budget"></a>A költségvetés létrehozása

Ezután konfigurálja a Poster  -t, és hozzon létre egy költségvetést az Azure-használati REST API-k meghívásával. A Poster egy API-fejlesztési környezet. A környezet és a gyűjtemény fájljait postán kell importálnia. A gyűjtemény az Azure-használati REST API-kat meghívó HTTP-kérelmek csoportosított definícióit tartalmazza. A környezeti fájl a gyűjtemény által használt változókat tartalmazza.

1.  Töltse le és nyissa meg a [Poster Rest](https://www.getpostman.com/) -ügyfelet a REST API-k végrehajtásához.
2.  A Poster-ben hozzon létre egy új kérelmet.

    ![Poster – új kérelem létrehozása](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Mentse az új kérést gyűjteményként, hogy az új kérelemhez ne legyen rá semmi.

    ![Poster – az új kérés mentése](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Módosítsa a kérést egy `Get` `Put` műveletre.
5.  Módosítsa a következő URL-címet `{subscriptionId}` úgy, hogy lecseréli az oktatóanyag előző szakaszában használt **előfizetés** -azonosítóra. Módosítsa az URL-címet is, hogy a "SampleBudget" értéket adja `{budgetName}`meg a következő értékként:`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Válassza a **headers** (fejlécek) fület a Poster alatt.
7.  Adjon hozzá egy "Authorization" nevű új **kulcsot** .
8.  Állítsa be az **értéket** az utolsó szakasz végén található ArmClient használatával létrehozott jogkivonatra.
9.  Válassza a **törzs** fület a Poster területen.
10. Válassza a **RAW** gomb lehetőséget.
11. A szövegmezőbe illessze be az alábbi minta költségvetési definíciót, azonban a **subscriptionid**, a **budgetname**és a **ACTIONGROUPNAME** paramétereket az előfizetés-azonosítójával, a költségvetés egyedi nevével és a műveleti csoport nevével kell helyettesítenie. az URL-címben és a kérelem törzsében is létrehozva:

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
12. A kérelem elküldéséhez kattintson a **Küldés** gombra.

Most már rendelkezik a [költségvetés API](https://docs.microsoft.com/rest/api/consumption/budgets)meghívásához szükséges összes darabtal. A költségvetés API-referenciája további részleteket tartalmaz az adott kérelmekkel kapcsolatban, beleértve az alábbiakat:
    - **budgetName** – több költségvetés is támogatott.  A költségvetés nevének egyedinek kell lennie.
    - **Kategória** – a **Cost** vagy a **használati**értéknek kell lennie. Az API mind a költség-, mind a használati költségvetést támogatja.
    - **timeGrain** – havonta, negyedévente vagy évenkénti költségvetés. Az összeg az időszak végén alaphelyzetbe áll.
    - **szűrők** – a szűrők lehetővé teszik, hogy a költségvetést a kijelölt hatókörön belüli erőforrások egy adott készletére szűkítse. Egy szűrő például az előfizetési szint költségvetéséhez tartozó erőforráscsoportok gyűjteménye lehet.
    - **értesítések** – meghatározza az értesítés részleteit és a küszöbértékeket. Beállíthat több küszöbértéket, és megadhat egy e-mail-címet vagy egy műveleti csoportot, amely értesítést kap.

## <a name="summary"></a>Összegzés

Ezt az oktatóanyagot követve megtanulta a következőket:
- Azure Automation Runbook létrehozása a virtuális gépek leállításához.
- Egy olyan Azure logikai alkalmazás létrehozása, amely a költségvetési küszöbértékek alapján aktiválódik, és a megfelelő paraméterekkel hívja meg a kapcsolódó runbook.
- Azure Monitor műveleti csoport létrehozása, amely úgy lett konfigurálva, hogy kiváltsa az Azure Logic apps alkalmazást a költségvetési küszöbérték teljesülése esetén.
- Hogyan hozhatja létre az Azure-költségvetést a kívánt küszöbértékekkel, és hogyan továbbíthatja azt a műveleti csoportba.

Most már rendelkezik egy teljes körű működési költségkerettel az előfizetéséhez, amely leállítja a virtuális gépeket, amikor eléri a beállított költségvetési küszöbértékeket.

## <a name="next-steps"></a>További lépések

- Az Azure számlázási forgatókönyvekkel kapcsolatos további információkért lásd: [számlázási és Cost Management Automation-forgatókönyvek](billing-cost-management-automation-scenarios.md).
