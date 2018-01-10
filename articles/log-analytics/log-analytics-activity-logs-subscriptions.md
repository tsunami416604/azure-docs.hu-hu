---
title: "Azure tevékenységi naplóit gyűjtése a Log Analyticshez való előfizetések |} Microsoft Docs"
description: "Az Event Hubs és a Logic Apps segítségével adatokat gyűjteni a Azure tevékenységnapló, majd elküldhetik azt egy Azure Naplóelemzési munkaterület különböző bérlőjében."
services: log-analytics, logic-apps, event-hubs
documentationcenter: 
author: richrundmsft
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: richrund; bwren
ms.openlocfilehash: d8c970b322be7f735e51c7b246b2421589c91813
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="collect-azure-activity-logs-into-log-analytics-across-subscriptions"></a>Azure tevékenységi naplóit gyűjtése a Log Analyticshez való előfizetések

Ez a cikk lépéseit egy metódust az Azure tevékenység gyűjtését a Naplóelemzési munkaterület az adatok Analytics Azure naplógyűjtő-összekötővel a Logic Apps be. A folyamat használata ebben a cikkben, ha szeretné elküldeni a naplókat a munkaterület különböző Azure Active Directoryban. Például ha egy felügyelt szolgáltató, érdemes lehet gyűjteni a tevékenység egy ügyfél-előfizetést, és tárolja őket a saját előfizetésének a Naplóelemzési munkaterület.

Ha a Naplóelemzési munkaterület azonos Azure-előfizetést, vagy egy másik előfizetésben, de az azonos Azure Active Directory, kövesse a lépéseket a [Azure tevékenység napló megoldás](../log-analytics/log-analytics-activity.md) Azure tevékenység naplók gyűjtéséhez.

## <a name="overview"></a>Áttekintés

Az ebben a forgatókönyvben használt stratégia az, hogy elküldje az eseményeket az Azure tevékenységnapló egy [Eseményközpont](../event-hubs/event-hubs-what-is-event-hubs.md) ahol egy [logikai alkalmazás](../logic-apps/logic-apps-what-are-logic-apps.md) elküldi azokat a Naplóelemzési munkaterület. 

![a tevékenység napló a log analyticshez adatfolyama](media/log-analytics-activity-logs-subscriptions/data-flow-overview.png)

A módszer előnyei a következők:
- Kis késés, mivel az Azure tevékenységnapló az Eseményközpontba való továbbítja adatfolyamként.  A logikai alkalmazást majd elindul, és elküldi az adatokat szolgáltatáshoz. 
- A minimális kód szükséges, és nem kiszolgálói infrastruktúra központi telepítéséhez.

Ez a cikk végigvezeti hogyan:
1. Létrehoz egy Eseményközpontot. 
2. Az Eseményközpontok Azure tevékenységnapló exportált profil használatával exportálja tevékenységi naplóit.
3. Az Event Hubs olvasni, és elküldje az eseményeket Naplóelemzési logikai alkalmazás létrehozása.

## <a name="requirements"></a>Követelmények
Ebben a forgatókönyvben használt Azure-erőforrások követelményei a következők:.

- Az Event Hubs névtér nem kell ugyanazt az előfizetést, mint az előfizetés naplók kibocsátó lehet. A beállítás konfiguráló felhasználónak mindkét előfizetéshez megfelelő hozzáférési engedélyekkel kell rendelkeznie. Ha több előfizetéssel rendelkezik az azonos Azure Active Directoryban, a műveletnaplóban előfizetéseket küldhet egy eseményközpontba.
- A logikai alkalmazás egy másik előfizetésben található az eseményközpontból lehet, és nem kell az azonos Azure Active Directoryban. A Logic App beolvassa az Event Hubs az Event Hubs megosztott elérési kulcsával.
- A Naplóelemzési munkaterület lehet egy másik előfizetést, és a logikai alkalmazást az Azure Active Directoryban, de az egyszerűség érdekében azt javasoljuk, hogy vannak-e ugyanazt az előfizetést. A logikai alkalmazás küld Naplóelemzési használatával a Naplóelemzési munkaterület azonosítója és kulcsa.



## <a name="step-1---create-an-event-hub"></a>1. lépés – létrehoz egy Eseményközpontot

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Válassza ki az Azure-portálon **új**> **az eszközök internetes hálózatát** > **Event Hubs**.

   ![Új eseményközpont piactér](media/log-analytics-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. A **névtér létrehozása**, vagy adjon meg egy új névtér vagy egy meglévő kiválasztása. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.

   ![a lemezkép létrehozása event hub párbeszédpanel](media/log-analytics-activity-logs-subscriptions/create-event-hub1.png)

4. Válassza ki a tarifacsomag (alapszintű vagy Standard), egy Azure-előfizetéssel, erőforráscsoport és az új erőforrás helye.  A névtér létrehozásához kattintson a **Létrehozás** parancsra. Előfordulhat, hogy Várjon néhány percet, hogy a rendszer teljesen kiépíteni az erőforrásokat.
6. Kattintson az imént létrehozott listájából névtérre.
7. Válassza ki **megosztott elérési házirendek**, és kattintson a **RootManageSharedAccessKey**.

   ![esemény megosztott központi hozzáférési házirendek képe](media/log-analytics-activity-logs-subscriptions/create-event-hub7.png)
   
8. Kattintson a másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati karakterláncot a vágólapra másolja. 

   ![event hub megosztott elérési kulcsával képe](media/log-analytics-activity-logs-subscriptions/create-event-hub8.png)

9. Egy ideiglenes helyre, például a Jegyzettömbben az megőrzi az Eseményközpont nevét, vagy az elsődleges vagy másodlagos Eseményközpont kapcsolati karakterlánc. A logikai alkalmazást kell ezeket az értékeket.  Az Event Hubs kapcsolati karakterláncot, használhatja a **RootManageSharedAccessKey** kapcsolati karakterlánc, vagy külön létrehozásához.  A kapcsolati karakterláncot, használja a kezdéshez `Endpoint=sb://` és az olyan házirendet, amely rendelkezik a **kezelése** házirendhez.


## <a name="step-2---export-activity-logs-to-event-hub"></a>2. lépés – exportálás tevékenységi naplóit eseményközpontba

A műveletnapló adatfolyamként válasszon ki egy Event Hub Namespace és egy megosztott elérési házirendet a névtérhez. Az Eseményközpontok a névtér jön létre az első új tevékenységnapló esemény bekövetkezésekor. 

Egy esemény hub névtér, amely nincs ugyanazt az előfizetést, mint az előfizetés kibocsátó naplókat, azonban az előfizetések kell lennie az azonos Azure Active Directoryban is használhatja. Konfigurálja a beállítást felhasználónak rendelkeznie kell a megfelelő RBAC mindkét előfizetéshez eléréséhez. 

1. Válassza ki az Azure-portálon **figyelő** > **tevékenységnapló**.
3. Kattintson a **exportálása** gombra az oldal tetején.

   ![navigációs sáv azure figyelő képe](media/log-analytics-activity-logs-subscriptions/activity-log-blade.png)

4. Válassza ki a **előfizetés** exportálása, majd **válassza ki az összes** a a **régiók** legördülő listán válassza ki az erőforrások eseményeket minden régióban. Kattintson a **eseményközpontokba való exportálás** jelölőnégyzetet.
7. Kattintson a **Service bus-névtér**, majd válassza ki a **előfizetés** az event hubs a **event hub névtér**, és egy **eseményközpont házirend neve**.

    ![Exportálás az event hub lap képe](media/log-analytics-activity-logs-subscriptions/export-activity-log2.png)

11. Kattintson a **OK** , majd **mentése** ezek a beállítások mentéséhez. A beállítások azonnal érvényesek az előfizetéséhez.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>3. lépés - a logikai alkalmazás létrehozása

Miután az event hubs ír a tevékenységi naplóit, hozzon létre egy logikai alkalmazást a naplóinak gyűjtése az eseményközpont, és szolgáltatáshoz.

A logikai alkalmazást az alábbiakat tartalmazza:
- Egy [Eseményközpont összekötő](https://docs.microsoft.com/connectors/eventhubs/) eseményindító olvasni az Eseményközpontba.
- A [elemzése JSON művelet](../logic-apps/logic-apps-content-type.md) a JSON-események kibontásához.
- A [művelet összeállítása](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) való konvertálja a JSON-objektumot.
- A [Naplóelemzési küldése adatösszekötő](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) úgy az adatok a szolgáltatáshoz.

   ![eseményindító hub hozzáadása a logic apps képe](media/log-analytics-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Logic App követelmények
A logikai alkalmazás létrehozása, előtt győződjön meg arról, hogy az előző lépést a következő információkat:
- Eseményközpont neve
- Esemény központ kapcsolati karakterlánc (az elsődleges vagy másodlagos) az Event Hubs-névtérhez.
- Napló Analytics munkaterület azonosítója
- A Naplóelemzési megosztott kulcs

Az event Hub nevét és a kapcsolati karakterlánc lekéréséhez kövesse [ellenőrizze az Event Hubs névtérengedélyekről és a Keresés a kapcsolati karakterlánc](../connectors/connectors-create-api-azure-event-hubs.md#check-event-hubs-namespace-permissions-and-find-the-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Üres új logikai alkalmazás létrehozása

1. Válassza ki az Azure-portálon **hozzon létre egy erőforrást** > **vállalati integrációs** > **logikai alkalmazás**.

    ![Piactér új logikai alkalmazás](media/log-analytics-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Adja meg a beállításokat az alábbi táblázatban.

    ![Logikai alkalmazás létrehozása](media/log-analytics-activity-logs-subscriptions/create-logic-app.png)

   |Beállítás | Leírás  |
   |:---|:---|
   | Name (Név)           | Egyedi nevet a logikai alkalmazást. |
   | Előfizetés   | Válassza ki az Azure-előfizetés, amely a logikai alkalmazást fogja tartalmazni. |
   | Erőforráscsoport | Válasszon ki egy meglévő Azure erőforráscsoportot, vagy hozzon létre egy újat az a logikai alkalmazást. |
   | Hely       | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez. |
   | Log Analytics  | Válassza ki, ha azt szeretné, hogy minden futtatásakor a logikai alkalmazás állapotának bejelentkezni a Naplóelemzési.  |

    
3. Kattintson a **Létrehozás** gombra. Ha **sikeres telepítés** értesítés jelenik meg, kattintson a **forrást** a logikai alkalmazás megnyitásához.

4. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet. 

A Logic Apps Designer felhasználható összekötőt és az eseményindítók, amellyel mely a logic app munkafolyamat indítása most látható.

<!-- Learn [how to create a logic app](../logic-apps/logic-apps-create-a-logic-app.md). -->

### <a name="add-event-hub-trigger"></a>Az Event Hubs eseményindító hozzáadása

1. Írja be a keresőmezőbe a Logic App Designer *az event hubs* a szűrőhöz. Válassza ki az eseményindító **Event Hubs - amikor események állnak rendelkezésre az Eseményközpont**.

   ![eseményindító hub hozzáadása a logic apps képe](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Amikor a rendszer kéri a hitelesítő adatokat, az Event Hubs névtér csatlakozni. Adjon meg egy nevet a kapcsolatot, majd kimásolt kapcsolati karakterláncot.  Kattintson a **Létrehozás** gombra.

   ![event hub kapcsolat hozzáadása a logic apps képe](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Miután létrehozta a kapcsolatot, az indítási beállításait módosíthatja. Start kiválasztásával **insights műveleti naplókat** a a **Eseményközpont nevét** legördülő listán.

   ![Ha az eseményeket is elérhető párbeszédpanel](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Bontsa ki a **speciális beállítások megjelenítése** , és módosítsa **tartalomtípus** való *az application/json*

   ![Event hub konfigurációs párbeszédpanel](media/log-analytics-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Elemzése JSON-művelet hozzáadása

Az Event Hubs kimenete egy JSON-adattartalom rekordok tömbjét tartalmazza. A [elemzése JSON](../logic-apps/logic-apps-content-type.md) Naplóelemzési küldje el az rögzíti a ponttömbnek kibontásához használt művelet.

1. Kattintson a **új lépés** > **művelet hozzáadása**
2. Írja be a keresőmezőbe, *elemzése json* a szűrőhöz. Válassza ki a **adatok műveletek - elemzése JSON**.

   ![A logic apps elemzési json művelet hozzáadása](media/log-analytics-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Kattintson a **tartalom** mezőbe, majd válassza ki *törzs*.

4. Másolja és illessze be a következő séma azokat a **séma** mező.  Ebben a sémában az Eseményközpont művelet eredményének megfelelő.  

   ![Elemzése json párbeszédpanel](media/log-analytics-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> Egy minta hasznos kaphat kattintva **futtatása** és megnézi a **nyers kimeneti** az eseményközpontból.  A kimenet használhatók **séma létrehozásához használja a minta hasznos** a a **elemzése JSON** tevékenység a séma létrehozásához.

### <a name="add-compose-action"></a>Új művelet hozzáadása
A [Compose](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) művelet JSON-kimenetét, és létrehoz egy objektumot a Naplóelemzési művelet által használható.

1. Kattintson a **új lépés** > **művelet hozzáadása**
2. Típus *összeállítása* a szűrőt, és válassza ki a művelet **adatok műveletek - Compose**.

    ![Új művelet hozzáadása](media/log-analytics-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Kattintson a **bemenetek** mezőben, majd válassza ki **törzs** alatt a **elemzése JSON** tevékenység.


### <a name="add-log-analytics-send-data-action"></a>Log Analytics-küldése adatok művelet hozzáadása
A [Azure naplógyűjtő Analytics adatok](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) művelet időt vesz igénybe a Compose művelet az objektumot, és elküldi a Naplóelemzési.

1. Kattintson a **új lépés** > **művelet hozzáadása**
2. Típus *analytics jelentkezzen* a szűrőt, és válassza ki a művelet **Azure napló Analytics adatgyűjtő - adatok küldése**.

   ![Hozzáadását naplóelemzési adatok művelet küldése a logic Apps alkalmazások](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Adjon meg egy nevet a kapcsolathoz, és illessze be a **munkaterület azonosítója** és **Munkaterületkulcsot** Naplóelemzési munkaterület.  Kattintson a **Create** (Létrehozás) gombra.

   ![A logic apps-napló analytics kapcsolat hozzáadása](media/log-analytics-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Miután létrehozta a kapcsolatot, az alábbi táblázat a beállításainak szerkesztése 

    ![Küldési adatok művelet konfigurálása](media/log-analytics-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Beállítás        | Érték           | Leírás  |
   |---------------|---------------------------|--------------|
   |JSON-kérelem törzsét.  | **Kimeneti** a a **Compose** művelet | A rekordok átveszi a Compose művelet törzsét. |
   | Egyéni napló neve | AzureActivity | Az egyéni napló tábla létrehozása az importált adatok tárolásához Naplóelemzési a neve. |
   | idő generált mező | time | Ne válassza a JSON mezőt **idő** -csak írja be a word időt. Ha bejelöli a JSON mezőt a Tervező helyezi a **adatok küldése** a művelet egy *minden* hurkot állítana elő, ami nem szeretné. |




10. Kattintson a **mentése** menti a Logic Apps alkalmazást végrehajtott módosításokat.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>4. lépés - teszt, és a logikai alkalmazás hibaelhárítása
A teljes munkafolyamat tesztelheti a tervezőben hiba nélkül működésének ellenőrzéséhez.

A Logic App tervezőben kattintson **futtatása** a logikai alkalmazás teszteléséhez. A logikai alkalmazás lévő egyes lépések a állapot ikon látható, a sikert jelző zöld kör fehér négyzetet.

   ![Teszt logikai alkalmazás](media/log-analytics-activity-logs-subscriptions/test-logic-app.png)

Az egyes lépések részletes információk megjelenítéséhez kattintson a bontsa ki a lépés neve. Kattintson a **nyers bemeneti adatok megjelenítése** és **nyers kimenetének megjelenítése** további részletek az adatokat fogad, és a rendszer a következő lépésre.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Nézet Azure tevékenységnapló a Naplóelemzési, 5 -. lépés
Az utolsó lépés a Naplóelemzési munkaterület győződjön meg arról, hogy gyűjtenek adatokat várt módon kereséséhez.

1. Válassza ki az Azure-portálon **Naplóelemzési**.
2. A munkaterület kiválasztása, majd a **naplófájl-keresési** csempére.
3. A keresési lekérdezés sávon, írja be a `AzureActivity_CL` , majd kattintson a Keresés gombra. Ha az egyéni napló nem name *AzureActivity*, írja be a választott név és a hozzáfűző `_CL`.

>[!NOTE]
> Egy új egyéni naplót a rendszer elküldi a Naplóelemzési először azt is tarthat egy órát kell kereshető egyéni napló.

>[!NOTE]
> A tevékenység naplók egyéni táblához vannak megírva, és nem jelenik meg a [tevékenységnapló megoldás](./log-analytics-activity.md).


![Teszt logikai alkalmazás](media/log-analytics-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozta a logikai alkalmazás tevékenységi naplóit Azure beolvasni az Eseményközpontok, és küldje el a Naplóelemzési elemzés céljából. Tudhat meg többet a Naplóelemzési, beleértve az irányítópultokat, létrehozása az adatok megjelenítése tekintse át az oktatóanyag a képi megjelenítés opcióra adatokhoz.

> [!div class="nextstepaction"]
> [Naplófájl-keresési adatok oktatóanyag megjelenítése](./log-analytics-tutorial-dashboards.md)
