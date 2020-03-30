---
title: A biztonsági házirendek használata | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan működik a biztonsági szabályzatok az Azure Security Centerben.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906857"
---
# <a name="working-with-security-policies"></a>Biztonsági szabályzatok használata

Ez a cikk bemutatja, hogyan vannak konfigurálva a biztonsági házirendek, és hogyan tekintheti meg őket a Security Centerben. 

## <a name="introduction-to-security-policies"></a>Bevezetés a biztonsági házirendekbe

A biztonsági házirend határozza meg a számítási feladatok kívánt konfigurációját, és segít biztosítani, hogy megfeleljen a vállalat vagy a szabályozó hatóságok biztonsági követelményeinek.

Az Azure Security Center a kiválasztott szabályzatok alapján teszi a biztonsági javaslatokat. A Security Center-szabályzatok az Azure Policyben létrehozott házirend-kezdeményezéseken alapulnak. Az [Azure Policy](../governance/policy/overview.md) használatával kezelheti szabályzatait, és szabályzatokat állíthat be felügyeleti csoportok és több előfizetés között.

A Security Center a következő lehetőségeket kínálja a biztonsági házirendek használata érdekében:

* **A beépített alapértelmezett házirend megtekintése és szerkesztése** – Amikor engedélyezi a Security Center t, az "ASC default" nevű beépített kezdeményezés automatikusan hozzá lesz rendelve a Security Center összes regisztrált előfizetéséhez (ingyenes vagy standard szintű). A kezdeményezés testreszabásához engedélyezheti vagy letilthatja az egyes házirendeket. Tekintse meg a [beépített biztonsági házirendek](security-center-policy-definitions.md) listáját a rendelkezésre álló lehetőségek megismeréséhez.

* **Saját egyéni szabályzatok hozzáadása** – Ha testre szeretné szabni az előfizetésére alkalmazott biztonsági kezdeményezéseket, ezt a Security Center en belül teheti meg. Ezután javaslatokat kap, ha a gépek nem követik a létrehozott szabályzatokat. Az egyéni házirendek létrehozásáról és hozzárendelésével kapcsolatos tudnivalókért olvassa el [az Egyéni biztonsági házirendek használata témakört.](custom-security-policies.md)

* **Szabályozási megfelelőségi szabályzatok hozzáadása** – A Security Center szabályozási megfelelőségi irányítópultja egy adott szabvány vagy szabályozás (például Az Azure CIS, A NIST SP 800-53 R4, SWIFT CSP CSCF-v2020) környezetében mutatja a környezeten belüli összes értékelés állapotát. További információ: [A jogszabályi megfelelőség javítása.](security-center-compliance-dashboard.md)


## <a name="managing-your-security-policies"></a>A biztonsági házirendek kezelése

Biztonsági szabályzatok megtekintése a Security Centerben:

1. A **Biztonsági központ** irányítópultján válassza a **Biztonsági házirend**lehetőséget.

    ![A Szabályzatkezelés panel](./media/security-center-policies/security-center-policy-mgt.png)

   A **Szabályzatkezelés** képernyőn láthatja a felügyeleti csoportok, előfizetések és munkaterületek számát, valamint a felügyeleti csoport struktúráját.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek szabályzatait meg szeretné tekinteni.

1. Megjelenik az adott előfizetésvagy felügyeleti csoport biztonsági házirendlapja. A rendelkezésre álló és hozzárendelt házirendeket jeleníti meg.

   ![házirend képernyő](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Ha az alapértelmezett szabályzat mellett van egy "MG Inherited" címke, az azt jelenti, hogy a szabályzat egy felügyeleti csoporthoz van rendelve, és a megtekintett előfizetés örökölte.


1. Válasszon az ezen az oldalon elérhető lehetőségek közül:

    1. Az iparági irányelvekkel való munkához kattintson **a További szabványok hozzáadása**gombra. További információt a Dinamikus megfelelőségi csomagok frissítése című [témakörben talál.](update-regulatory-compliance-packages.md)

    1. Egyéni kezdeményezések hozzárendeléséhez és kezeléséhez kattintson **az Egyéni kezdeményezések hozzáadása gombra.** További információt az [Egyéni biztonsági házirendek használata](custom-security-policies.md)című témakörben talál.

    1. Az alapértelmezett házirend megtekintéséhez és szerkesztéséhez kattintson a **Hatályos házirend megtekintése** elemre, és folytassa az alábbiakban leírtak szerint. 

       ![házirend képernyő](./media/security-center-policies/policy-screen.png)
       
       Ez **a Biztonsági házirend** képernyő a kiválasztott előfizetési vagy felügyeleti csoporthoz rendelt házirendek által végrehajtott műveleteket tükrözi.
       
       * A felül található hivatkozások segítségével megnyithat egy, az előfizetésre vagy a felügyeleti csoportra vonatkozó **házirend-hozzárendelést.** Ezek a hivatkozások lehetővé teszik a hozzárendelés elérését, valamint a házirend szerkesztését vagy letiltását. Ha például azt látja, hogy egy adott házirend-hozzárendelés hatékonyan megtagadja a végpontok védelmét, a hivatkozás segítségével szerkesztheti vagy letilthatja a házirendet.
       
       * A szabályzatok listájában láthatja a szabályzat hatékony alkalmazását az előfizetésen vagy a felügyeleti csoporton. A program figyelembe veszi a hatókörre vonatkozó házirendek beállításait, és megjelenik a házirend által végrehajtott műveletek összesített eredménye. Ha például a házirend egyik hozzárendelésében le van tiltva, egy másikban pedig AuditIfNotExist értékre van állítva, akkor a göngyölt hatás az AuditIfNotExist függvényt alkalmazza. Az aktívabb hatás mindig elsőbbséget élvez.
       
       * A házirend hatása lehet: Hozzáfűzés, Naplózás, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. A hatások alkalmazásáról a [Házirend-effektusok ismertetése](../governance/policy/concepts/effects.md)című témakörben talál további információt.

       > [!NOTE]
       > A hozzárendelt házirendek megtekintésekor több hozzárendelést láthat, és láthatja, hogy az egyes hozzárendelések hogyan vannak konfigurálva.


## <a name="who-can-edit-security-policies"></a>Ki szerkesztheti a biztonsági házirendeket?

A biztonsági szabályzatok az Azure Policy portalon keresztül, a REST API-n keresztül vagy a Windows PowerShell használatával szerkeszthetők.

A Security Center szerepköralapú hozzáférés-vezérlést (RBAC) használ, amelynek beépített szerepköreit az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti. Amikor a felhasználók megnyitják a Biztonsági központot, csak az olyan erőforrásokkal kapcsolatos információkat látják, amelyekhez hozzáférésük van. Ez azt jelenti, hogy a felhasználók az erőforrás előfizetéséhez *a tulajdonos,* *közreműködő*vagy *olvasó* szerepkört kapják. Ezeken a szerepkörökön kívül két speciális Security Center-szerepkör is létezik:

- **Biztonsági olvasó:** Megtekintheti a Security Center jogosultságait, amely javaslatokat, riasztásokat, házirendeket és állapotot tartalmaz, de nem módosíthatja.
- **Biztonsági rendszergazda:** Ugyanolyan megtekintési jogosultságokkal rendelkezik, mint a *biztonsági olvasó,* és frissíthetik a biztonsági házirendet, és elvethetik a javaslatokat és riasztásokat.


## <a name="disable-security-policies"></a>Biztonsági házirendek letiltása
Ha az alapértelmezett biztonsági házirend olyan javaslatot hoz létre, amely nem releváns a környezetében, leállíthatja azt a házirend-definíció letiltásával, amely elküldi a javaslatot.
A javaslatokról a [Biztonsági javaslatok kezelése](security-center-recommendations.md)című témakörben talál további információt.

1. A Biztonsági központ **Házirend & megfelelősége** szakaszban kattintson a **Biztonsági házirend gombra.**

   ![házirend-kezelés](./media/tutorial-security-policy/policy-management.png)

2. Kattintson arra az előfizetésre vagy felügyeleti csoportra, amelynek a javaslatát le szeretné tiltani.

   > [!NOTE]
   > A felügyeleti csoport az előfizetésekre alkalmazza a szabályzatokat. Így ha letiltja egy előfizetés szabályzatát, az előfizetés pedig egy olyan felügyeleti csoporthoz tartozik, amely ugyanazt a szabályzatot alkalmazza, továbbra is megkapja a szabályzatra vonatkozó javaslatokat. A rendszer továbbra is a felügyeleti szinten alkalmazza a szabályzatot, valamint létrehoz javaslatokat.

1. Kattintson **a Hatályos házirend megtekintése gombra.**

   ![házirend letiltása](./media/tutorial-security-policy/view-effective-policy.png)

1. Kattintson a hozzárendelt házirendre.

   ![házirend letiltása](./media/tutorial-security-policy/security-policy.png)

1. A **PARAMETERS szakaszban** keresse meg a letiltani kívánt javaslatot megmeghívó házirendet, és a legördülő listában válassza a **Letiltva** lehetőséget.

   ![házirend letiltása](./media/tutorial-security-policy/disable-policy.png)

1. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > A letiltási házirend módosításai akár 12 órát is igénybe vehetnek.



## <a name="next-steps"></a>További lépések
Ebben a cikkben a biztonsági házirendekről szerzett tudomást. A kapcsolódó információkat a következő cikkekben talál:

* A PowerShell használatával a szabályzatok beállításával kapcsolatos tudnivalókat a [Rövid útmutató: Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosítására az Azure PowerShell-modul használatával című](../governance/policy/assign-policy-powershell.md) témakörben találja.

* A biztonsági szabályzatok Azure-szabályzatban való szerkesztéséről a [Megfelelőség idikciójának létrehozása és kezelése](../governance/policy/tutorials/create-and-manage.md)című témakörben talál.

* A szabályzat előfizetések közötti beállításával vagy az Azure Policy használatával a Felügyeleti csoportok ra vonatkozó tudnivalókat [a Mi az Azure-szabályzat?](../governance/policy/overview.md)