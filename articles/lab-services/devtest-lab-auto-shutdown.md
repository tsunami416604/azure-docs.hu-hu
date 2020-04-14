---
title: Automatikus leállítási házirendek kezelése az Azure DevTest Labsben | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be az automatikus leállítási házirendet egy tesztkörnyezetben, hogy a virtuális gépek automatikusan leálljanak, amikor nincsenek használatban.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270910"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Az automatikus leállítás konfigurálása az Azure DevTest Labs ben a labor- és számítási virtuális gépekhez

Ez a cikk bemutatja, hogyan konfigurálhatja a laborvirtuális gépek automatikus leállítási beállításait a DevTest Labs és a számítási virtuális gépek. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Labor virtuális gépek automatikus leállításának konfigurálása (DevTest Labs)
Az Azure DevTest Labs lehetővé teszi a költségek szabályozását és a laborokban lévő hulladék minimalizálását az egyes laborok szabályzatai (beállításai) kezelésével. Ez a cikk bemutatja, hogyan konfigurálhatja az automatikus leállítási házirendet egy tesztkörnyezet-fiókhoz, és konfigurálhatja az automatikus leállítás i beállításait egy tesztkörnyezethez a tesztkörnyezet-fiókban. Az egyes tesztkörnyezet-szabályzatok beállításának megtekintéséhez olvassa [el a Laborszabályzatok definiálása az Azure DevTest Labs ben című témakört.](devtest-lab-set-lab-policy.md)  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Automatikus leállítási házirend beállítása egy tesztkörnyezethez
A labor tulajdonosaként konfigurálhatja a leállítási ütemezést a tesztkörnyezetben lévő összes virtuális géphez. Ezzel költségeket takaríthat meg a nem használt (tétlen) gépek futtatásával szemben. Központilag kényszerítheti ki az összes tesztkörnyezetes virtuális gép leállítási házirendjét, de a tesztkörnyezet felhasználóinak is mentheti az egyes gépek ütemezésének beállításával kapcsolatos erőfeszítéseket. Ez a funkció lehetővé teszi, hogy állítsa be a szabályzatot a labor ütemezése kezdve nem nyújt vezérlésteljes vezérlés, a tesztkörnyezet felhasználói. A tesztkörnyezet tulajdonosaként az alábbi lépésekkel konfigurálhatja ezt a házirendet:

1. A tesztkörnyezet kezdőlapján válassza a **Konfiguráció és házirendek**lehetőséget.
2. A bal oldali menü **Ütemezések** szakaszában válassza az **Automatikus leállítás házirend** lehetőséget.
3. Válasszon a lehetőségek közül. A következő szakaszok további részleteket tartalmaznak ezekről a lehetőségekről: A készletházirend csak a laborban létrehozott új virtuális gépekre vonatkozik, a már meglévő virtuális gépekre nem. 

    ![Házirend-beállítások automatikus leállítása](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Automatikus leállítási beállítások konfigurálása
Az automatikus leállítási házirend segít minimalizálni a laborhulladékot azáltal, hogy lehetővé teszi a labor virtuális gépei leállításának idejét.

A tesztkörnyezet házirendjeinek megtekintéséhez (és módosításához kövesse az alábbi lépéseket):

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
3. A laborok listájából válassza ki a kívánt labort.   
4. Válassza a **Konfiguráció és házirendek**lehetőséget.

    ![Házirend-beállítások ablaktábla](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. A tesztkörnyezet **Konfiguráció és házirendek** ablaktábláján válassza az **Automatikus leállítás** lehetőséget az **Ütemezések csoportban.**
   
    ![Automatikus leállítás](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. A házirend engedélyezéséhez válassza **a Be** lehetőséget, a házirend letiltásához pedig a **Be** lehetőséget.
7. Ha engedélyezi ezt a házirendet, adja meg az időt (és az időzónát) az aktuális tesztkörnyezetösszes virtuális gépének leállításához.
8. Adja meg az **Igen** vagy a **Nem** értéket ahhoz a beállításhoz, hogy a megadott automatikus leállítási idő előtt 30 perccel küldjön értesítést. Ha az **Igen**lehetőséget választja, adjon meg egy webhook URL-végpontot vagy e-mail címet, amely megadja, hogy hol szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és lehetőséget kap a leállítás késleltetésére. További információt az [Értesítések](#notifications) szakaszban talál. 
9. Kattintson a **Mentés** gombra.

    Alapértelmezés szerint, ha engedélyezve van, ez a szabályzat az aktuális tesztkörnyezetben lévő összes virtuális gépre vonatkozik. Ha el szeretné távolítani ezt a beállítást egy adott virtuális gépről, nyissa meg a virtuális gép felügyeleti ablaktábláját, és módosítsa az **automatikus leállítási** beállítást.
    
> [!NOTE]
> Ha frissíti a labor vagy egy adott tesztkörnyezet virtuális gép automatikus leállítási ütemezését az aktuális ütemezett időpont 30 percen belül, a frissített leállítási idő a következő napi ütemezésre vonatkozik. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>A felhasználó ütemezést állít be, és leiratkozhat
Ha a tesztkörnyezet et erre a házirendre állítja be, a tesztkörnyezet felhasználói felülbírálhatják vagy leiratkozhatnak a tesztkörnyezetütemezésről. Ez a beállítás biztosítja a tesztkörnyezet felhasználói nak teljes körű vezérlést a virtuális gépek automatikus leállítási ütemezése felett. A laborfelhasználók nem látnak változást a virtuális gép automatikus leállítási ütemezési lapján.

![Házirend automatikus leállítása beállítás - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>A felhasználó ütemezést állít be, és nem tud leiratkozni
Ha a tesztkörnyezetet erre a házirendre állítja be, a tesztkörnyezet felhasználói felülbírálhatják a tesztkörnyezet ütemezését. Azonban nem tilthatják le az automatikus leállítási házirendet. Ez a beállítás gondoskodik arról, hogy a tesztkörnyezet minden gépe automatikus leállítási ütemezés alatt szerepeljön. A laborfelhasználók frissíthetik a virtuális gépek automatikus leállítási ütemezését, és beállíthatják a leállítási értesítéseket.

![Házirend automatikus leállítása - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>A felhasználónak nincs befolyása a laboradminisztrátor által beállított ütemezésre
Ha a tesztkörnyezet et erre a házirendre állítja be, a tesztkörnyezet felhasználói nem bírálhatják felül a tesztkörnyezet ütemezését, illetve nem iratkozhatnak le róluk. Ez a beállítás a labor adminisztrátora számára a teljes ellenőrzést az ütemezés minden gép a laborban. A laborfelhasználók csak automatikus leállítási értesítéseket állíthatnak be a virtuális gépekhez.

![Automatikus leállítási házirend beállítás - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Értesítések
Miután a labor tulajdonosa beállította az automatikus leállítást, az értesítéseket a rendszer 30 perccel az automatikus leállítás előtt küldi el a laborfelhasználóknak, ha a virtuális gépek érintettek lesznek. Ez a beállítás lehetőséget ad a tesztkörnyezet felhasználóinak, hogy a leállítás előtt mentsék a munkájukat. Az értesítés az egyes virtuális gépekhez mutató hivatkozásokat is tartalmaz a következő műveletekhez:

- Az automatikus leállítás kihagyása erre az időre
- Szundi az automatikus leállítás egy óra vagy 2 óra, hogy azok továbbra is dolgozik a virtuális gép.

Értesítés küldése a konfigurált webhook-végponton keresztül, vagy egy e-mail-címet a labor tulajdonosok az automatikus leállítási beállításokat. A webhookok lehetővé teszik bizonyos eseményekre feliratkozó integrációk létrehozásához vagy beállításához. Ha az egyik ilyen esemény aktiválódik, devtest labs küld egy HTTP POST hasznos adat a webhook konfigurált URL-cím. A webhookokról további információt a [Webhook vagy AZ API Azure-függvény létrehozása című témakörben talál.](../azure-functions/functions-create-a-web-hook-or-api-function.md) 

Javasoljuk, hogy használjon webes horgokat, mert azokat széles körben támogatja a különböző alkalmazások (például a Tartalékidő, az Azure Logic Apps és így tovább.), és lehetővé teszi, hogy a saját módon az értesítések küldéséhez. Ez a cikk például bemutatja, hogyan kaphat automatikus leállítási értesítést az Azure Logic Apps használatával az e-mailekből. Először is, gyorsan végig az alapvető lépéseket, hogy autoshutdown értesítést a laborban.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>E-mailes értesítéseket fogadó logikai alkalmazás létrehozása
[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) számos beépített összekötőt biztosít, amelyek megkönnyítik a szolgáltatások integrálását más ügyfelekkel, például az Office 365-tel és a Twitterrel. A magas szinten a logikai alkalmazás e-mail értesítéshez való beállításának lépései négy szakaszra oszthatók: 

- Hozzon létre egy logikai alkalmazást. 
- Konfigurálja a beépített sablont.
- Integrálás az e-mail kliensbe
- A Webhook URL-címének beszereznie.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
Első lépésként hozzon létre egy logikai alkalmazást az Azure-előfizetésében az alábbi lépésekkel:

1. Válassza **a + Erőforrás létrehozása** lehetőséget a bal oldali menüben, válassza az **Integráció**lehetőséget, és válassza a **Logikai alkalmazás**lehetőséget. 

    ![Új logikai alkalmazás menü](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. A **Logikai alkalmazás – Létrehozás** lapon hajtsa végre az alábbi lépéseket: 
    1. Adja meg a logikai alkalmazás **nevét.**
    2. Válassza ki **Azure-előfizetését.**
    3. Hozzon létre egy új **erőforráscsoportot,** vagy jelöljön ki egy meglévő erőforráscsoportot. 
    4. Válassza ki a logikai alkalmazás **helyét.** 

        ![Új logikai alkalmazás - beállítások](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Az **Értesítések területen**válassza az **Ugrás az erőforrásra** lehetőséget az értesítésen. 

    ![Erőforrás megnyitása](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Válassza **a Logikai alkalmazástervező** elemet a **Telepítési eszközök** kategóriában.

    ![HTTP-kérelem/válasz kiválasztása](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. A **HTTP-kérelem-válasz** lapon válassza **a Sablon használata lehetőséget.** 

    ![Válassza a Sablon használata jelölőnégyzetet.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Másolja a következő JSON-t a **Kérelemtörzs JSON-séma** szakaszába: 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Kérelem törzse JSON-séma](./media/devtest-lab-auto-shutdown/request-json.png)
7. Válassza a **+ Új lépés** lehetőséget a tervezőben, és kövesse az alábbi lépéseket:
    1. Keresés az **Office 365 Outlook – E-mail küldése**. 
    2. Válassza **az E-mail küldése** a Műveletek **ből**lehetőséget. 
    
        ![E-mail küldése lehetőség](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Válassza **a Bejelentkezés** lehetőséget az e-mail fiókba való bejelentkezéshez. 
    4. Válassza **a TO** mezőt, és válassza a tulajdonost.
    5. Válassza **a TÉMA**lehetőséget, és adja meg az e-mail értesítés tárgyát. Például: "A gép vmName leállítása labor: labname."
    6. Válassza a **TÖRZS**lehetőséget, és adja meg a törzs tartalmát az e-mailértesítéshez. Például: "vmName van ütemezve, hogy állítsa le 15 perc alatt. Hagyja ki ezt a leállítást a következő URL-címre kattintva. Leállítás késleltetése egy órával: delayUrl60. A leállítás késleltetése 2 órára: delayUrl120."

        ![Kérelem törzse JSON-séma](./media/devtest-lab-auto-shutdown/email-options.png)
1. Válassza az eszköztár **Save** (Mentés) elemét. Most már másolhatja a **HTTP POST URL-címét.** Jelölje ki a másolás gombot, ha az URL-címet a vágólapra szeretné másolni. 

    ![WebHook URL-címe](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Automatikus leállítás konfigurálása számítási virtuális gépekhez

1. A **Virtuális gép** lapon válassza az **Automatikus leállítás** lehetőséget a **műveletek** szakasz bal oldali menüjében. 
2. Az **Automatikus leállítás** lapon válassza a **Be** lehetőséget a házirend engedélyezéséhez, a **Ki** lehetőséget pedig a házirend letiltásához.
3. Ha engedélyezi ezt a házirendet, adja meg azt az **időt** (és **időzónát),** amelyen a virtuális gép le kell állítani.
4. Adja meg az **Igen** vagy a **Nem** értéket ahhoz a beállításhoz, hogy a megadott automatikus leállítási idő előtt 30 perccel küldjön értesítést. Ha az **Igen**lehetőséget választja, adjon meg egy webhook URL-végpontot vagy e-mail címet, amely megadja, hogy hol szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és lehetőséget kap a leállítás késleltetésére. További információt az [Értesítések](#notifications) szakaszban talál. 
9. Kattintson a **Mentés** gombra.

    ![Automatikus leállítás konfigurálása számítási virtuális géphez](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Az automatikus leállítási frissítések tevékenységnaplóinak megtekintése
Az automatikus leállítási beállítás frissítésekén láthatja a virtuális gép tevékenységnaplójában naplózott tevékenységet. 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a virtuális gép kezdőlapját.
2. Válassza a bal oldali menü **Tevékenységnapló parancsát.** 
3. **Erőforrás eltávolítása: mycomputevm** a szűrőkből.
3. Ellenőrizze, hogy megjelenik-e az **Ütemezések hozzáadása vagy módosítása** művelet a tevékenységnaplóban. Ha nem látja, várjon egy ideig, és frissítse a tevékenységnaplót.

    ![Tevékenységnapló-bejegyzés](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Az **Ütemezések hozzáadása vagy módosítása** lehetőséget választva az alábbi információkat szeretné látni az **Összegzés** lapon:

    - Művelet neve (Ütemezések hozzáadása vagy módosítása)
    - Az automatikus leállítás beállítás frissítésének dátuma és időpontja.
    - A beállítást frissített felhasználó e-mail címe. 

        ![Tevékenységnapló bejegyzésének összegzése](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Váltson át az **Ütemezések hozzáadása vagy módosítása** lap **Módosítási előzmények** fülére, és megjelenik a beállítás módosítási előzményei. A következő példában a leállítási idő 2020. április 10-én 15:18:47-kor 15:18:47-kor változott. És a beállítás le volt tiltva 15:25:09 EST-nél. 

    ![Tevékenységnapló – módosítási előzmények](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. A művelettel kapcsolatos további részletek megtekintéséhez váltson az **Ütemezések hozzáadása vagy módosítása** lap **JSON** fülére.

## <a name="next-steps"></a>További lépések
Az összes szabályzat beállításáról az [Azure DevTest Labs laborszabályzatának definiálása.](devtest-lab-set-lab-policy.md)

