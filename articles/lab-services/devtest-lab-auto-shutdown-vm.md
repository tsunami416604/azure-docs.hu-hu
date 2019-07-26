---
title: Azure DevTest Labs-beli virtuális gép automatikus leállítási beállításainak konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a virtuális gépek automatikus leállítási beállításait úgy, hogy a virtuális gép automatikusan leálljon, ha nincs használatban.
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
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361569"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Azure DevTest Labs-beli virtuális gép automatikus leállítási beállításainak konfigurálása
A Azure DevTest Labs lehetővé teszi, hogy az egyes laborokhoz tartozó szabályzatok (beállítások) kezelésével szabályozza a költségeket, és csökkentse a saját laborokban lévő hulladékokat. Ebből a cikkből megtudhatja, hogyan konfigurálhatja az automatikus leállítási szabályzatot egy Lab-fiókhoz, és hogyan konfigurálhatja a laborok automatikus leállítási beállításait a laborban Ha meg szeretné tekinteni, hogyan kell beállítani az összes tesztkörnyezet házirendjét, tekintse meg [a labor-házirendek definiálása a Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Tesztkörnyezet automatikus leállítási szabályzata
A labor tulajdonosaként beállíthatja a tesztkörnyezetben lévő összes virtuális gép leállítási ütemtervét. Ennek köszönhetően a nem használt gépektől (inaktív) származó költségek is megmenthetők. Központilag is kényszerítheti a leállítási szabályzatokat az összes laboratóriumi virtuális gépen, de mentheti a labor-felhasználókat arra, hogy ütemezést állítsanak be az egyes gépekhez. Ezzel a szolgáltatással beállíthatja a szabályzatot a labor-ütemtervre, kezdve a teljes hozzáférés szabályozása nélkül, a labor felhasználói számára. A labor tulajdonosaként a következő lépésekkel konfigurálhatja ezt a házirendet:

1. A labor kezdőlapján válassza a **konfiguráció és szabályzatok**lehetőséget.
2. Válassza a bal oldali menü **ütemtervek** szakaszának **automatikus leállítás házirend** elemét.
3. Válasszon egyet a lehetőségek közül. A következő fejezetekben részletesebb információkat talál ezekről a lehetőségekről: A beállított házirend csak a tesztkörnyezetben létrehozott új virtuális gépekre vonatkozik, nem pedig a már meglévő virtuális gépekre. 

    ![Házirend-beállítások automatikus leállítása](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Tesztkörnyezet automatikus leállítási beállításainak konfigurálása
Az automatikus leállítási szabályzat segít a laborban lévő virtuális gépek leállítási idejének meghatározásában.

A labor szabályzatának megtekintéséhez (és módosításához) kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort.   
4. Válassza **a konfiguráció és szabályzatok**lehetőséget.

    ![Házirend-beállítások ablaktábla](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. A labor konfigurációjának **és házirendjeinek** ablaktábláján válassza az **automatikus leállítás** lehetőséget az **ütemtervek**területen.
   
    ![Automatikus leállítás](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Ezzel **a** beállítással engedélyezheti a szabályzatot **, és** letilthatja azt.
7. Ha engedélyezi ezt a házirendet, az aktuális laborban lévő összes virtuális gép leállításához válassza ki az időt (és az időzónát).
8. Adja meg az **Igen** vagy a **nem** lehetőséget az értesítés 15 perccel a megadott automatikus leállítási idő előtti elküldéséhez. Ha az **Igen**lehetőséget választja, adjon meg egy WEBHOOK URL-címet vagy e-mail-címet, amely megadja, hogy hová szeretné közzétenni vagy elküldeni az értesítést. A felhasználó értesítést kap, és a Leállítás késleltetését adja meg. További információ: [értesítések](#notifications) szakasz. 
9. Kattintson a **Mentés** gombra.

    Alapértelmezés szerint, ha engedélyezve van, ez a házirend az aktuális laborban található összes virtuális gépre vonatkozik. Ha egy adott virtuális gépről el szeretné távolítani ezt a beállítást, nyissa meg a virtuális gép  felügyeleti paneljét, és módosítsa az automatikus leállítási beállítást.

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Virtuális gép automatikus leállítási beállításainak konfigurálása

### <a name="user-sets-a-schedule-and-can-opt-out"></a>A felhasználó beállít egy ütemtervet, és letilthatja
Ha ez a házirend-beállítás be van állítva a laborhoz, a felhasználók felülbírálják vagy eldönthetik a labor-ütemtervet. Ez a beállítás lehetővé teszi a tesztkörnyezet felhasználói számára a virtuális gépek automatikus leállítási ütemtervének teljes körű felügyeletét. A labor felhasználói nem változnak a virtuális gép automatikus leállításának ütemterve lapon.

![Automatikus leállítási házirend-beállítás – 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>A felhasználó beállít egy ütemtervet, és nem tud letiltani
Ha ez a házirend-beállítás be van állítva a laborhoz, a felhasználók felülbírálják a labor-ütemtervet. Azonban nem tudják letiltani az automatikus leállítási szabályzatot. Ezzel a beállítással gondoskodhat arról, hogy a laborban lévő összes gép automatikusan leállítási ütemterv alá kerüljön. A labor-felhasználók frissíthetik a virtuális gépek automatikus leállítási ütemtervét, és beállítják a leállítási értesítéseket.

![Automatikus leállítási házirend-beállítás – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>A felhasználónak nincs hozzáférése a labor rendszergazdája által beállított ütemtervhez
Ha ez a házirend-beállítás be van állítva a laborhoz, a felhasználók nem tudják felülbírálni vagy letiltani a labor-ütemtervet. Ez a beállítás a tesztkörnyezet rendszergazdájának teljes körű vezérlését kínálja a laborban lévő összes gép számára. A labor felhasználói csak automatikus leállítási értesítéseket állíthatnak be a virtuális gépekhez.

![Automatikus leállítás házirend-beállítás – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Értesítések
Ha a labor tulajdonosa állítja be az automatikus leállítást, az értesítéseket a labor felhasználói számára 15 perccel az automatikus leállítás előtt küldi el a rendszer, ha a virtuális gépek érintettek lesznek. Ez a beállítás lehetővé teszi a laboratóriumi felhasználók számára, hogy a leállítás előtt megmentsek a munkájukat. Az értesítés az egyes virtuális gépekre mutató hivatkozásokat is tartalmaz a következő műveletekhez:

- Automatikus leállítás kihagyása erre az időre
- Az automatikus leállítás késleltetése egy órán vagy 2 órán keresztül, hogy továbbra is működőképesek maradjanak a virtuális gépen.

A rendszer értesítést küld a konfigurált webhook-végponton vagy egy, az automatikus leállítási beállításokban a labor tulajdonosai által megadott e-mail-címen. A webhookok lehetővé teszik bizonyos eseményekre előfizetett integrációk készítését vagy beállítását. Az események egyikének aktiválásakor a DevTest Labs HTTP POST-adattartalmat küld a webhook konfigurált URL-címére. A webhookokkal kapcsolatos további információkért lásd: [webhook vagy API Azure-függvény létrehozása](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Javasoljuk, hogy a webhookokat használja, mivel azokat számos különböző alkalmazás (például Slack, Azure Logic Apps stb.) széles körben támogatja, és lehetővé teszi, hogy saját módszert alkalmazzon az értesítések küldéséhez. Az e-mailek Azure Logic Apps használatával történő automatikus leállításáról szóló értesítést a következő témakörben talál:[e-mail-értesítéseket fogadó logikai alkalmazás létrehozása](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>További lépések
Lásd: [Azure DevTest Labs-tesztkörnyezet](devtest-lab-auto-shutdown.md) automatikus leállítási házirendjeinek kezelése
