---
title: Egyéni biztonsági házirendek létrehozása az Azure Security Centerben | Microsoft dokumentumok
description: Az Azure Security Center által figyelt egyéni szabályzatdefiníciók.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258260"
---
# <a name="using-custom-security-policies"></a>Egyéni biztonsági házirendek használata

A rendszerek és a környezet védelme érdekében az Azure Security Center biztonsági javaslatokat készít. Ezek a javaslatok az iparág legjobb gyakorlatain alapulnak, amelyek beépülnek az összes ügyfélnek biztosított általános, alapértelmezett biztonsági házirendbe. Ők is származhatnak A Security Center ismerete az ipari és szabályozási szabványok.

Ezzel a funkcióval hozzáadhatja saját *egyéni* kezdeményezéseit. Ezután javaslatokat kap, ha a környezet nem követi a létrehozott szabályzatokat. A létrehozott egyéni kezdeményezések a beépített kezdeményezések mellett jelennek meg az oktatóanyagban ismertetett szabályozási megfelelőségi [irányítópulton.](security-center-compliance-dashboard.md)

Az Azure-szabályzat [dokumentációjában](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) tárgyaltak, amikor megadja az egyéni kezdeményezés helyét, felügyeleti csoportnak vagy előfizetésnek kell lennie. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Egyéni kezdeményezés hozzáadása az előfizetéshez 

1. A Biztonsági központ oldalsávjáról nyissa meg a **Biztonsági házirend** lapot.

1. Válasszon ki egy előfizetést vagy felügyeleti csoportot, amelyhez egyéni kezdeményezést szeretne hozzáadni.

    [![Olyan előfizetés kiválasztása, amelyhez létre kell hoznia az egyéni szabályzatot](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Egyéni szabványokat kell hozzáadnia az előfizetés szintjén (vagy magasabb szinten), hogy kilehessen őket értékelni és megjeleníteni a Security Centerben. 
    >
    > Amikor hozzáad egy egyéni szabványt, az *egy kezdeményezést* rendel az adott hatókörhöz. Ezért azt javasoljuk, hogy válassza ki a hozzárendeléshez szükséges legszélesebb hatókört.

1. A Biztonsági házirend lap Egyéni kezdeményezések csoportban kattintson **az Egyéni kezdeményezés hozzáadása**elemre.

    [![Kattintson a **Egyéni kezdeményezés hozzáadása**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A következő oldal jelenik meg:

    ![Házirend létrehozása vagy hozzáadása](media/custom-security-policies/create-or-add-custom-policy.png)

1. Az Egyéni kezdeményezések hozzáadása lapon tekintse át a szervezetben már létrehozott egyéni házirendek listáját. Ha lát egyet, amelyet hozzá szeretne rendelni az előfizetéséhez, kattintson a **Hozzáadás**gombra. Ha a listában nincs olyan kezdeményezés, amely megfelel az igényeinek, hagyja ki ezt a lépést.

1. Új egyéni kezdeményezés létrehozása:

    1. Kattintson **az Új létrehozása gombra.**
    1. Adja meg a definíció helyét és nevét.
    1. Jelölje ki a felvenni kívánt házirendeket, és kattintson a **Hozzáadás gombra.**
    1. Adja meg a kívánt paramétereket.
    1. Kattintson a **Mentés** gombra.
    1. Az Egyéni kezdeményezések hozzáadása lapon kattintson a frissítés gombra, és az új kezdeményezés elérhetőként jelenik meg.
    1. Kattintson **a Hozzáadás** gombra, és rendelje hozzá az előfizetéséhez.

    > [!NOTE]
    > Új kezdeményezések létrehozásához előfizetés-tulajdonosi hitelesítő adatokra van szükség. Az Azure-szerepkörökről az [Engedélyek az Azure Security Centerben](security-center-permissions.md)című témakörben talál további információt.

    Az új kezdeményezés életbe lép, és a következő két módon láthatja a hatást:

    * A Biztonsági központ oldalsávján a Szabályzat & megfelelőség csoportban válassza a **Szabályozási megfelelőség**lehetőséget. A megfelelőségi irányítópult megnyílik, hogy a beépített kezdeményezések mellett az új egyéni kezdeményezést is megjelenítse.
    
    * Ha a környezet nem felel meg a megadott irányelveknek, akkor elkezd javaslatokat kapni.

1. A házirendhez kapott javaslatok megtekintéséhez kattintson az **oldalsávon** a Javaslatok elemre a javaslatok lap megnyitásához. A javaslatok egy "Egyéni" címkével jelennek meg, és körülbelül egy órán belül elérhetők lesznek.

    [![Egyéni javaslatok](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egyéni biztonsági házirendeket. 

Egyéb kapcsolódó anyagokról lásd a következő cikkeket: 

- [A biztonsági házirendek áttekintése](tutorial-security-policy.md)
- [A beépített biztonsági házirendek listája](security-center-policy-definitions.md)