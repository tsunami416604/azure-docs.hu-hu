---
title: A biztonsági szabályzatok használata | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a biztonsági házirendek a Azure Security Centerban.
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
ms.openlocfilehash: 76695d4d8dbc3e3a6b04b89b8f34e41b83a92963
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965187"
---
# <a name="working-with-security-policies"></a>Biztonsági szabályzatok használata

Ez a cikk ismerteti a biztonsági házirendek konfigurálásának módját, valamint azt, hogy miként lehet megtekinteni őket a Security Centerban. 

## <a name="introduction-to-security-policies"></a>A biztonsági szabályzatok bemutatása

A biztonsági szabályzat határozza meg a számítási feladatok kívánt konfigurációját, és segít biztosítani, hogy megfeleljen a vállalat vagy a szabályozók biztonsági követelményeinek.

A Azure Security Center a kiválasztott szabályzatok alapján hozza meg a biztonsági javaslatait. Security Center házirendek a Azure Policyban létrehozott házirend-kezdeményezéseken alapulnak. A [Azure Policy](../governance/policy/overview.md) segítségével kezelheti a házirendeket, és házirendeket állíthat be a felügyeleti csoportok és több előfizetés között.

Security Center a következő lehetőségeket kínálja a biztonsági házirendek használatához:

* **A beépített alapértelmezett házirend megtekintése és szerkesztése** – a Security Center engedélyezésekor az "ASC default" nevű beépített kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez. A kezdeményezés testreszabásához engedélyezheti vagy letilthatja az egyes házirendeket. Tekintse meg a [beépített biztonsági szabályzatok](./policy-reference.md) listáját, amelyből megismerheti a rendelkezésre álló lehetőségeket.

* **Saját egyéni szabályzatok hozzáadása** – Ha testre szeretné szabni az előfizetésre alkalmazott biztonsági kezdeményezéseket, akkor a Security Centeron belül megteheti. Ezután javaslatokat fog kapni, ha a gépek nem követik a létrehozott házirendeket. Az egyéni házirendek létrehozásával és hozzárendelésével kapcsolatos utasításokért lásd: [egyéni biztonsági házirendek használata](custom-security-policies.md).

* **Szabályozási megfelelőségi szabályzatok hozzáadása** – a Security Center szabályozási megfelelőségi irányítópultja egy adott szabvány vagy szabályozás (például Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020) kontextusában jeleníti meg a környezet összes értékelésének állapotát. További információ: [a szabályozás megfelelőségének javítása](security-center-compliance-dashboard.md).


## <a name="manage-your-security-policies"></a>Biztonsági szabályzatok kezelése

Biztonsági szabályzatok megtekintése a Security Centerben:

1. A **Security Center** irányítópulton válassza a **biztonsági házirend** elemet.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="A házirend-kezelés lap":::

   A **házirend kezelése** képernyőn láthatja a felügyeleti csoportok, előfizetések és munkaterületek számát, valamint a felügyeleti csoport struktúráját.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek a szabályzatait meg szeretné tekinteni.

1. Megjelenik az adott előfizetés vagy felügyeleti csoport biztonsági szabályzat lapja. Megjeleníti az elérhető és hozzárendelt házirendeket.

   ![házirend lap](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Ha az alapértelmezett szabályzat mellett "MG örökölt" címkével rendelkezik, az azt jelenti, hogy a szabályzat hozzá van rendelve egy felügyeleti csoporthoz, és a megtekintett előfizetés örökli.


1. Válasszon az ezen az oldalon elérhető lehetőségek közül:

    1. Az iparági szabályzatokkal való munkavégzéshez válassza a **további szabványok hozzáadása** lehetőséget. További információ: [frissítés a dinamikus megfelelőségi csomagokra](update-regulatory-compliance-packages.md).

    1. Egyéni kezdeményezések hozzárendeléséhez és kezeléséhez válassza az **Egyéni kezdeményezések hozzáadása** lehetőséget. További információ: [egyéni biztonsági házirendek használata](custom-security-policies.md).

    1. Az alapértelmezett házirend megtekintéséhez és szerkesztéséhez válassza a **hatályos házirend megtekintése** lehetőséget, és folytassa az alább leírtak szerint. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Érvényes házirend képernyő":::

       Ez a **biztonsági házirend** képernyő a kiválasztott előfizetéshez vagy felügyeleti csoporthoz hozzárendelt szabályzatok által végrehajtott műveletet tükrözi.
       
       * A felül található hivatkozásokkal nyisson meg egy szabályzat- **hozzárendelést** , amely az előfizetésre vagy a felügyeleti csoportra vonatkozik. Ezekkel a hivatkozásokkal hozzáférhet a hozzárendeléshez, és szerkesztheti vagy letilthatja a szabályzatot. Ha például úgy látja, hogy egy adott házirend-hozzárendelés ténylegesen megtagadja az Endpoint Protectiont, akkor a hivatkozásra kattintva szerkesztheti vagy tilthatja le a házirendet.
       
       * A szabályzatok listájában megtekintheti az előfizetéséhez vagy a felügyeleti csoportjához tartozó házirend hatályos alkalmazását. A rendszer figyelembe veszi a hatókörre vonatkozó egyes szabályzatok beállításait, és a szabályzat által végrehajtott műveletek összesített eredményét jeleníti meg. Ha például a házirend egyetlen hozzárendelésében le van tiltva, de egy másikban a AuditIfNotExist értékre van állítva, akkor a kumulatív hatás a AuditIfNotExist alkalmazza. Az aktívabb hatás mindig elsőbbséget élvez.
       
       * A házirendek hatása a következő lehet: Hozzáfűzés, naplózás, AuditIfNotExists, megtagadás, DeployIfNotExists, letiltva. További információ a hatások alkalmazásáról: a [szabályzatok hatásainak megismerése](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > A hozzárendelt házirendek megtekintésekor több hozzárendelést láthat, és megtekintheti, hogy az egyes hozzárendelések hogyan legyenek konfigurálva.


## <a name="who-can-edit-security-policies"></a>Kik módosíthatják a biztonsági házirendeket?

A biztonsági szabályzatokat a Azure Policy-portálon, REST API vagy a Windows PowerShell használatával szerkesztheti.

A Security Center Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ, amely beépített szerepköröket biztosít az Azure-felhasználók,-csoportok és-szolgáltatások számára. Security Center megnyitásakor a felhasználók csak az elérhető erőforrásokhoz kapcsolódó információkat látják. Ez azt jelenti, hogy a felhasználók a *tulajdonos*, *közreműködő* vagy *olvasó* szerepkört rendelik hozzá az erőforrás előfizetéséhez. Két konkrét Security Center szerepkör is létezik:

- **Biztonsági olvasó**: jogosult a Security Center elemek, például javaslatok, riasztások, szabályzatok és állapotok megtekintésére. Nem lehet módosítani.
- **Biztonsági rendszergazda**: a *biztonsági olvasóval* megegyező megtekintési jogosultsággal rendelkezik. A a biztonsági házirend frissítése és a riasztások elvetése is megtehető.


## <a name="disable-security-policies-and-disable-recommendations"></a>Biztonsági házirendek letiltása és javaslatok letiltása

Ha a biztonsági kezdeményezés olyan javaslatot indít, amely a környezet szempontjából lényegtelen, megakadályozhatja, hogy a javaslat újból megjelenjen. Egy javaslat letiltásához tiltsa le a javaslatot létrehozó konkrét szabályzatot.

A letiltani kívánt javaslat továbbra is megjelenik, ha a Security Center szabályozási megfelelőségi eszközeivel alkalmazott szabályozási szabványhoz szükséges. Még ha a szabályzatot is letiltotta a beépített kezdeményezésben, a szabályozási standard kezdeményezési szabályzata továbbra is elindítja a javaslatot, ha a megfelelőséghez szükséges. A szabályzatokat nem lehet letiltani a szabályozási standard kezdeményezésekben.

A javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

1. Security Center a **házirend & megfelelőség** szakaszban válassza a **biztonsági házirend** elemet.

   ![házirendek kezelése](./media/tutorial-security-policy/policy-management.png)

2. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez le szeretné tiltani a javaslatot.

   > [!NOTE]
   > A felügyeleti csoport az előfizetésekre alkalmazza a szabályzatokat. Így ha letiltja egy előfizetés szabályzatát, az előfizetés pedig egy olyan felügyeleti csoporthoz tartozik, amely ugyanazt a szabályzatot alkalmazza, továbbra is megkapja a szabályzatra vonatkozó javaslatokat. A rendszer továbbra is a felügyeleti szinten alkalmazza a szabályzatot, valamint létrehoz javaslatokat.

1. Válassza a **hatályos szabályzat megtekintése** lehetőséget.

   ![házirend megtekintése](./media/tutorial-security-policy/view-effective-policy.png)

1. Válassza ki a hozzárendelt szabályzatot.

   ![házirend kiválasztása](./media/tutorial-security-policy/security-policy.png)

1. A **Parameters (paraméterek** ) szakaszban keresse meg a letiltani kívánt javaslatot meghívó házirendet, és a legördülő listából válassza a **Letiltva** lehetőséget.

   ![házirend letiltása](./media/tutorial-security-policy/disable-policy.png)

1. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > A házirend-letiltási módosítások érvénybe léptetéséhez akár 12 órát is igénybe vehet.



## <a name="next-steps"></a>Következő lépések
Ez a cikk a biztonsági szabályzatokat ismerteti. A kapcsolódó információk a következő cikkekben találhatók:

- [Ismerje meg, hogyan állíthatja be a szabályzatokat a PowerShell használatával](../governance/policy/assign-policy-powershell.md) - 
- [Megtudhatja, hogyan szerkesztheti a biztonsági házirendeket Azure Policy](../governance/policy/tutorials/create-and-manage.md) - 
- [Megtudhatja, hogyan állíthatja be a szabályzatot az előfizetések és a felügyeleti csoportok között Azure Policy használatával](../governance/policy/overview.md).
- [Megtudhatja, hogyan engedélyezheti Security Center a felügyeleti csoportban lévő összes előfizetéshez](onboard-management-group.md)