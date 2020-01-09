---
title: Egyéni biztonsági szabályzatok létrehozása a Azure Security Centerban | Microsoft Docs
description: A Azure Security Center által figyelt egyéni Azure-házirend-definíciók.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 34dbace304ccf70891ef53dd768de60d87e26967
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666635"
---
# <a name="using-custom-security-policies-preview"></a>Egyéni biztonsági házirendek használata (előzetes verzió)

A rendszerek és a környezet biztonságossá tételéhez Azure Security Center biztonsági javaslatokat hoz létre. Ezek a javaslatok az iparági ajánlott eljárásokon alapulnak, amelyek az összes ügyfél számára biztosított általános, alapértelmezett biztonsági szabályzatba vannak beépítve. Security Center az iparági és a szabályozási szabványok ismeretében is származhatnak.

Ezzel az előzetes verziójú funkcióval saját *Egyéni* kezdeményezéseket adhat hozzá. Ezután javaslatokat fog kapni, ha a környezet nem követi a létrehozott házirendeket. Az Ön által létrehozott egyéni kezdeményezések az oktatóanyagban ismertetett, a szabályzatoknak [való megfelelést javító](security-center-compliance-dashboard.md)beépített kezdeményezésekkel együtt jelennek meg.

Az Azure Policy [dokumentációjában ismertetett](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) módon, ha az egyéni kezdeményezéshez ad meg egy helyet, akkor azt felügyeleti csoportnak vagy előfizetésnek kell lennie. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Egyéni kezdeményezés hozzáadása az előfizetéshez 

1. A Security Center oldalsávján nyissa meg a **biztonsági házirend** lapot.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez egyéni kezdeményezést szeretne hozzáadni.

    [![válassza ki azt az előfizetést, amelyhez létre kívánja hozni az egyéni szabályzatot](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Az előfizetési szinten (vagy magasabban) egyéni szabványokat kell hozzáadnia ahhoz, hogy kiértékeljék és megjelenjenek Security Centerban. 
    >
    > Egyéni szabvány hozzáadásakor egy *kezdeményezést* rendel hozzá ehhez a hatókörhöz. Ezért javasoljuk, hogy válassza ki az adott hozzárendeléshez szükséges legszélesebb hatókört.

1. A biztonsági szabályzat lapon az egyéni kezdeményezések (előzetes verzió) területen kattintson az **Egyéni kezdeményezés hozzáadása**lehetőségre.

    [![kattintson * * egyéni kezdeményezés hozzáadása * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A következő oldal jelenik meg:

    ![Szabályzat létrehozása vagy hozzáadása](media/custom-security-policies/create-or-add-custom-policy.png)

1. Az egyéni kezdeményezések hozzáadása oldalon tekintse át a szervezetében már létrehozott egyéni szabályzatok listáját. Ha megjelenik az előfizetéshez hozzárendelni kívánt fiók, kattintson a **Hozzáadás**gombra. Ha nincs olyan kezdeményezés a listában, amely megfelel az igényeinek, hagyja ki ezt a lépést.

1. Új egyéni kezdeményezés létrehozása:

    1. Kattintson az **új létrehozása**gombra.
    1. Adja meg a definíció helyét és nevét.
    1. Válassza ki a felvenni kívánt házirendeket, majd kattintson a **Hozzáadás**gombra.
    1. Adja meg a kívánt paramétereket.
    1. Kattintson a **Mentés** gombra.
    1. Az egyéni kezdeményezések hozzáadása lapon kattintson a frissítés lehetőségre, és az új kezdeményezés elérhetőként jelenik meg.
    1. Kattintson a **Hozzáadás** gombra, és rendelje hozzá az előfizetéséhez.

    > [!NOTE]
    > Az új kezdeményezések létrehozásához előfizetés-tulajdonosi hitelesítő adatokra van szükség. Az Azure-szerepkörökkel kapcsolatos további információkért tekintse [meg a Azure Security Center engedélyeit](security-center-permissions.md).

    Az új kezdeményezés érvénybe lép, és a következő két módon láthatja a hatást:

    * A Security Center oldalsávon a szabályzat & megfelelőség területen válassza a **jogszabályi megfelelőség**lehetőséget. Megnyílik a megfelelőségi irányítópult, amely megjeleníti az új egyéni kezdeményezést a beépített kezdeményezésekkel együtt.
    
    * Ha a környezet nem követi a definiált szabályzatokat, megkezdheti a javaslatok fogadását.

1. A Szabályzathoz kapcsolódó javaslatok megtekintéséhez kattintson a **javaslatok** elemre az oldalsávon, és nyissa meg a javaslatok lapot. A javaslatok az "egyéni" címkével jelennek meg, és körülbelül egy órán belül elérhetők lesznek.

    [Egyéni javaslatok ![](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egyéni biztonsági házirendeket. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [A biztonsági házirendek áttekintése](tutorial-security-policy.md)
- [A beépített biztonsági szabályzatok listája](security-center-policy-definitions.md)