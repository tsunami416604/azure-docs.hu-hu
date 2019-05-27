---
title: Az Azure DevTest Labs szolgáltatásban autoshutdown szabályzatok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a labor autoshutdown szabályzatot úgy, hogy a virtuális gépek vannak automatikusan leáll, amelyek nincsenek használatban.
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873990"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Autoshutdown Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelése
Az Azure DevTest Labs lehetővé teszi, hogy a költségek és a maximális hatékonyság a Labs-környezetben, mivel kezeli az egyes lab (beállítások) házirendeket. Ez a cikk bemutatja, hogyan tesztkörnyezetfiók autoshutdown vonatkozó házirend konfigurálása és a egy tesztlabor autoshutdown beállításainak konfigurálása a labor-fiókban. Minden labor szabályának beállításáról, tekintse meg [laborszabályzatok definiálása az Azure DevTest Labs szolgáltatásban](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Automatikus leállítási szabályzat a tesztkörnyezet beállítása
Labortulajdonosként leállítási ütemezés szerint konfigurálhatja a virtuális gépek a lab-ben. Ezzel a módszerrel mentheti az éppen nem használt gépek fut (inaktív) költségek. A leállítási házirend az összes a labor virtuális gépeken, hanem központilag is menteni a labor felhasználók részéről az erőfeszítés egyes gépeikhez egy ütemezés beállításával kényszerítheti. Ez a funkció lehetővé teszi, hogy állítson be a házirendet a Labs-környezeti ütemezés kezdő ajánlat teljes hozzáférés nem szabályozza a labor számára. Labortulajdonosként konfigurálja a házirendet a következő lépések végrehajtásával:

1. Válassza ki a labor kezdőlapja, **Konfigurace a zásady**.
2. Válassza ki **automatikus leállítási házirend** a a **ütemezések** szakaszában a bal oldali menüben.
3. Válassza ki a beállítások. Az alábbi szakaszok ezen lehetőségekről további részleteket biztosítanak: A set-szabályzat vonatkozik, csak a lab-ben létrehozott új virtuális gépek és a már meglévő virtuális gépet. 

    ![Az automatikus leállítás házirend beállításai](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Automatikus leállítási beállítások konfigurálása
A autoshutdown szabályzattal meggyőződhetnek labor hatékonyság azáltal, hogy adja meg a labor virtuális gépek leállítása idejét.

Megtekintése (és módosítása) a tesztkörnyezet szabályzatainak, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
3. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.   
4. Válassza ki **Konfigurace a zásady**.

    ![A házirend-beállítások panel](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. A laborgyakorlat **Konfigurace a zásady** ablaktáblán válassza előbb **automatikus leállítási** alatt **ütemezések**.
   
    ![Autoshutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Válassza ki **a** ahhoz, hogy ezt a házirendet, és **ki** letiltja azt.
7. Ha engedélyezi ezt a házirendet, adja meg a jelenlegi laborkörnyezetben található összes virtuális gép leállítása idő (és időzóna).
8. Adja meg **Igen** vagy **nem** értesítés küldése a megadott autoshutdown idő előtt 15 perccel a beállítás. Ha úgy dönt, **Igen**, adjon meg egy webhook URL-végpontot, vagy e-mail-címét adja meg, ahol azt szeretné, hogy a közzétett vagy küldött értesítést. A felhasználó értesítést kap, és arra, hogy a leállás késleltetés van megadva. További információkért lásd: a [értesítések](#notifications) szakaszban. 
9. Kattintson a **Mentés** gombra.

    Alapértelmezés szerint engedélyezve van, ezt a házirendet alkalmazza az összes virtuális gépen a jelenlegi tesztkörnyezetben. Távolítsa el ezt a beállítást egy adott virtuális gépről, a virtuális gép kezelése panel megnyitásához, és módosítsa a **Autoshutdown** beállítás.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Felhasználó beállítja az ütemezés és kikapcsolhatja az újat
Ha a labor-e a házirend, a labor felhasználók felülbírálás vagy tilthatják le a Labs-környezeti ütemezés. Ez a beállítás a virtuális gépek automatikus leállítási ütemezés teljes hozzáférést biztosít a labor felhasználók. Labor felhasználók nem változik a virtuális gépek automatikus leállítási ütemezés lapon talál.

![Az automatikus leállítás házirend beállítása – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Felhasználói csoportok ütemezés szerint, és nem vesznek
Ha a labor-e a házirend, a labor felhasználók felülbírálhatják a Labs-környezeti ütemezés. Azonban ezek nem tilthatók le automatikus leállítási házirend. Ez a beállítás gondoskodik arról, hogy a lab-ben minden gép van-e egy automatikus leállítási ütemezés szerint. Labor a felhasználók a virtuális gépek automatikus leállítási ütemezés frissítése, és leállítást értesítések beállítása.

![Az automatikus leállítás házirend beállítása – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Felhasználó rendelkezik nem szabályozza a labor-rendszergazda által beállított ütemezés szerint
Ha a labor-e a házirend, a labor felhasználók nem bírálja felül, és tilthatják le a Labs-környezeti ütemezés. Ez a beállítás lehetővé teszi a labor rendszergazdai a teljes az ütemezés a lab-ben minden gép. Labor felhasználók csak értesítéseket állíthat be automatikus leállítás a virtuális gépek számára.

![Az automatikus leállítás házirend beállítása – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Értesítések
Miután autoshutdown állítsa be a labor tulajdonosa, értesítéseket a labor-felhasználók számára a aktiválódik, ha bármelyikét Gépeiket autoshutdown előtt 15 perccel hatással lesz. Ezt a lehetőséget biztosít a labor-felhasználóknak a és a leállítás előtt. Az értesítés hivatkozásokat is tartalmaz, mindegyik virtuális gép a következő műveleteket:

- Ez az idő a autoshutdown kihagyása
- Késleltetés a autoshutdown egy óráig vagy 2 óra, úgy, hogy a munka folytatásához azokat a virtuális Gépet.

Értesítés érkezik a konfigurált web hook végponton keresztül, vagy a autoshutdown beállításaiban labortulajdonosok által megadott e-mail-címmel. Webhookok hozhat létre, vagy hozzon létre, amely az egyes események feliratkozás Integrációk teszik lehetővé. Az események egyikét akkor aktiválódik, amikor DevTest Labs küld egy HTTP POST hasznos a webhook URL-cím. További információ a webhookok: [hozzon létre egy webhook vagy API Azure-függvény](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Azt javasoljuk, hogy a webhook használata, mivel már széles körben támogatott különféle alkalmazások (például Slack, az Azure Logic Apps és stb.), és lehetővé válik a saját módja az értesítések küldéséhez. Például ez a cikk ismerteti, hogyan autoshutdown értesítést kaphat e-mailek az Azure Logic Apps használatával. Először vegyük gyorsan fel azokon az alapvető lépéseken, a laborban autoshutdown értesítés engedélyezése.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Hozzon létre egy logikai alkalmazást, amely e-mail-értesítések fogadása
[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) biztosít a több-az-beépített összekötők, amely megkönnyíti a szolgáltatás integrálása más ügyfelekkel, mint például az Office 365 és a twitter. Magas szinten a lépések egy logikai alkalmazást, az e-mail-értesítések beállításához négy fázisra osztható: 

- Hozzon létre egy logikai alkalmazást. 
- Konfigurálja a beépített sablont.
- Az Ön e-mail ügyfélprogramját integrálása
- A Webhook URL-cím lekéréséhez.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása
Első lépésként hozzon létre egy logikai alkalmazást az Azure-előfizetéshez az alábbi lépések segítségével:

1. Válassza ki **+ erőforrás létrehozása** a bal oldali menüben válassza ki a **integrációs**, és válassza ki **logikai alkalmazás**. 

    ![Új logikai alkalmazás menüjében](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Az a **Logikaialkalmazás - létrehozása** lapon, kövesse az alábbi lépéseket: 
    1. Adjon meg egy **neve** a logikai alkalmazás.
    2. Jelölje ki az Azure-**előfizetést**.
    3. Hozzon létre egy új **erőforráscsoport** , vagy válasszon ki egy meglévő erőforráscsoportot. 
    4. Válassza ki a **hely** a logikai alkalmazás. 

        ![Új logikai alkalmazás - beállítások](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Az a **értesítések**válassza **erőforrás megnyitása** az értesítésre. 

    ![Erőforrás megnyitása](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Válassza ki **Logikaialkalmazás-Tervező** alatt **központi telepítési eszközök** kategória.

    ![Select HTTP Request/Response](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Az a **HTTP-kérés-válasz** lapon jelölje be **ezzel a sablonnal**. 

    ![Jelölje be ezt a beállítást a sablon használata](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Másolja a következő JSON-kódot a **kérelem Kéréstörzs JSON-sémája** szakaszban: 

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
    
    ![Kéréstörzs JSON-sémája](./media/devtest-lab-auto-shutdown/request-json.png)
7. Válassza ki **+ új lépés** a tervezőben, és kövesse az alábbi lépéseket:
    1. Keresse meg **Office 365 Outlook – e-mail küldése**. 
    2. Válassza ki **e-mail küldése** a **műveletek**. 
    
        ![Beállítás e-mailek küldése](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Válassza ki **jelentkezzen be a** jelentkezhetnek be az e-mail-fiókjába. 
    4. Válassza ki **TO** mezőben, majd válassza ki a tulajdonosa.
    5. Válassza ki **tulajdonos**, és adjon meg az e-mail-értesítés tárgya. Példa: "Leállítani a gép vmName laborban: labName."
    6. Válassza ki **törzs**, és határozza meg az értesítési e-mail szövegtörzse. Például: "vmName van ütemezve, 15 perc múlva leáll. Hagyja ki a Leállítás gombra kattintva: URL-CÍME. Egy óránál késleltetés Leállítás: delayUrl60. Késleltetés leállítási 2 óra: delayUrl120. "

        ![Kéréstörzs JSON-sémája](./media/devtest-lab-auto-shutdown/email-options.png)
1. Válassza az eszköztár **Save** (Mentés) elemét. Most is másolhatja a **HTTP POST URL-címe**. Válassza ki a Másolás gombra, hogy az URL-címet a vágólapra. 

    ![WebHook URL-címe](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan állíthatja be az összes szabályzat, lásd: [laborszabályzatok definiálása az Azure DevTest Labs szolgáltatásban](devtest-lab-set-lab-policy.md).
