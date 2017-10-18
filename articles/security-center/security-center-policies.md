---
title: "Biztonsági szabályzatok beállítása az Azure Security Centerben | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti az Azure Security Center biztonsági szabályzatainak konfigurálásához szükséges lépéseket."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Biztonsági szabályzatok beállítása az Azure Security Centerben
Ez a dokumentum végigvezeti a Security Center biztonsági szabályzatainak beállításához szükséges lépéseken.


## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. A szabályzatot a Security Centerben szerkesztheti, vagy az [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) használatával létrehozhat új meghatározásokat, meghatározhat további szabályzatokat, valamint hozzárendelhet szabályzatokat a Felügyeleti csoportokhoz (ezek jelenthetik a teljes céget vagy szervezetet, egy üzleti egységet stb.), és monitorozhatja az adott szabályzatokra vonatkozó megfelelőséget ezeken a hatókörökön belül.

> [!NOTE]
> Az Azure Policy jelenleg csak korlátozott előzetes verzióban érhető el. Kattintson [ide](https://aka.ms/getpolicy) a csatlakozáshoz. Az Azure-szabályzatokkal kapcsolatos további információkért olvassa el a [megfelelőség szabályzatok létrehozásával és kezelésével történő kikényszerítésével](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) foglalkozó témakört.

## <a name="how-to-change-security-policies-in-security-center"></a>Biztonsági szabályzatok módosítása a Security Centerben
Az egyes Azure-előfizetések alapértelmezett biztonsági szabályzatait a Security Centerben szerkesztheti. Biztonsági szabályzat módosításához az előfizetésben vagy az azt tartalmazó felügyeleti csoportban tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie. A Security Center biztonsági szabályzatainak megtekintéséhez jelentkezzen be az Azure Portalra, és kövesse az alábbi lépéseket:

1. A **Security Center** irányítópultjának **Általános** területén kattintson a **Biztonsági szabályzat** elemre.
2. Válassza ki azt az előfizetést, amelyhez engedélyezni kívánja a biztonsági szabályzatot.

    ![Szabályzatkezelés](./media/security-center-policies/security-center-policies-fig10.png)

3. A **SZABÁLYZAT ÖSSZETEVŐI** szakaszban kattintson a **Biztonsági szabályzat** elemre.

    ![Szabályzat összetevői](./media/security-center-policies/security-center-policies-fig12.png)

4. Ez a Security Centerhez az Azure Policyn keresztül hozzárendelt alapértelmezett szabályzat. Törölheti a **SZABÁLYZATOK ÉS PARAMÉTEREK** alatt felsorolt összetevőket, vagy új szabályzatdefiníciókat adhat meg az **ELÉRHETŐ LEHETŐSÉGEK** alatt. Ehhez kattintson egyszerűen a definíció neve mellett a plusz jelre.

    ![Szabályzatdefiníciók](./media/security-center-policies/security-center-policies-fig11.png)

5. Ha részletesebb ismertetőre van szüksége valamely szabályzattal kapcsolatban, kattintson a szabályzatra, és a megnyíló oldalon tekintse meg a részleteket és a [szabályzat-definíció(https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure) szerkezetű JSON-kódot:

    ![JSON](./media/security-center-policies/security-center-policies-fig13.png)

6. A szerkesztés befejeztével kattintson a **Mentés** gombra.

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md). A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md). A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md). A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
