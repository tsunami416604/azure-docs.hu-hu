---
title: Egyéni biztonsági szabályzatok létrehozása a Azure Security Centerban | Microsoft Docs
description: A Azure Security Center által figyelt egyéni Azure-házirend-definíciók.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: d703ea38c39ed556102271ac0cf9a609ce449bc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195918"
---
# <a name="using-custom-security-policies"></a>Egyéni biztonsági házirendek használata

A rendszerek és a környezet biztonságossá tételéhez Azure Security Center biztonsági javaslatokat hoz létre. Ezek a javaslatok az iparági ajánlott eljárásokon alapulnak, amelyek az összes ügyfél számára biztosított általános, alapértelmezett biztonsági szabályzatba vannak beépítve. Security Center az iparági és a szabályozási szabványok ismeretében is származhatnak.

Ezzel a funkcióval saját *Egyéni* kezdeményezéseket adhat hozzá. Ezután javaslatokat fog kapni, ha a környezet nem követi a létrehozott házirendeket. Az Ön által létrehozott egyéni kezdeményezések a szabályozási megfelelőségi irányítópult beépített kezdeményezései mellett jelennek meg, [az oktatóanyagban](security-center-compliance-dashboard.md)ismertetett módon.

Amint azt [a Azure Policy dokumentációban](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)tárgyaljuk, az egyéni kezdeményezés helyének megadásakor felügyeleti csoportnak vagy előfizetésnek kell lennie. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Egyéni kezdeményezés hozzáadása az előfizetéshez 

1. A Security Center oldalsávján nyissa meg a **biztonsági házirend** lapot.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez egyéni kezdeményezést szeretne hozzáadni.

    [![Válassza ki azt az előfizetést, amelyhez létre kívánja hozni az egyéni szabályzatot](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Az előfizetési szinten (vagy magasabban) egyéni szabványokat kell hozzáadnia ahhoz, hogy kiértékeljék és megjelenjenek Security Centerban. 
    >
    > Egyéni szabvány hozzáadásakor egy *kezdeményezést* rendel hozzá ehhez a hatókörhöz. Ezért javasoljuk, hogy válassza ki az adott hozzárendeléshez szükséges legszélesebb hatókört.

1. A biztonsági szabályzat lap egyéni kezdeményezések területén kattintson az **Egyéni kezdeményezés hozzáadása**lehetőségre.

    [![Kattintson * * egyéni kezdeményezés hozzáadása * *](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    A következő oldal jelenik meg:

    ![Szabályzat létrehozása vagy hozzáadása](media/custom-security-policies/create-or-add-custom-policy.png)

1. Az egyéni kezdeményezések hozzáadása oldalon tekintse át a szervezetében már létrehozott egyéni szabályzatok listáját. Ha megjelenik az előfizetéshez hozzárendelni kívánt fiók, kattintson a **Hozzáadás**gombra. Ha nincs olyan kezdeményezés a listában, amely megfelel az igényeinek, hagyja ki ezt a lépést.

1. Új egyéni kezdeményezés létrehozása:

    1. Kattintson az **új létrehozása**gombra.
    1. Adja meg a definíció helyét és nevét.
    1. Válassza ki a felvenni kívánt házirendeket, majd kattintson a **Hozzáadás**gombra.
    1. Adja meg a kívánt paramétereket.
    1. Kattintson a **Save** (Mentés) gombra.
    1. Az egyéni kezdeményezések hozzáadása lapon kattintson a frissítés elemre. Az új kezdeményezés elérhetőként jelenik meg.
    1. Kattintson a **Hozzáadás** gombra, és rendelje hozzá az előfizetéséhez.

    > [!NOTE]
    > Az új kezdeményezések létrehozásához előfizetés-tulajdonosi hitelesítő adatokra van szükség. Az Azure-szerepkörökkel kapcsolatos további információkért tekintse [meg a Azure Security Center engedélyeit](security-center-permissions.md).

    Az új kezdeményezés érvénybe lép, és a következő két módon láthatja a hatást:

    * A Security Center oldalsávon a szabályzat & megfelelőség területen válassza a **jogszabályi megfelelőség**lehetőséget. Megnyílik a megfelelőségi irányítópult, amely megjeleníti az új egyéni kezdeményezést a beépített kezdeményezésekkel együtt.
    
    * Ha a környezet nem követi a definiált szabályzatokat, megkezdheti a javaslatok fogadását.

1. A Szabályzathoz kapcsolódó javaslatok megtekintéséhez kattintson a **javaslatok** elemre az oldalsávon, és nyissa meg a javaslatok lapot. A javaslatok az "egyéni" címkével jelennek meg, és körülbelül egy órán belül elérhetők lesznek.

    [![Egyéni javaslatok](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhancing-your-custom-recommendations-with-detailed-information"></a>Részletes információ az egyéni javaslatok tökéletesítéséről

A Azure Security Centerhez mellékelt beépített ajánlások olyan részleteket tartalmaznak, mint például a súlyossági szintek és a Szervizelési utasítások. Ha ezt a típusú információt szeretné felvenni az egyéni javaslataiba, hogy az megjelenjen a Azure Portalban, vagy bárhol hozzáférjen a javaslataihoz, akkor a REST API kell használnia. 

A felvehető két típusú információ a következőkből áll:

- **RemediationDescription** – sztring
- **Súlyosság** – enumerálás [alacsony, közepes, magas]

A metaadatokat fel kell venni az egyéni kezdeményezés részét képező szabályzathoz tartozó szabályzat-definícióba. A "securityCenter" tulajdonságnak kell lennie, ahogy az alábbi ábrán látható:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

Az alábbi példa egy egyéni házirendre mutat példát, beleértve a metaadatok/securityCenter tulajdonságot:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High",
        },
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

A securityCenter tulajdonság használatának egy másik példáját a [REST API dokumentációjának ezen szakasza](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)tartalmazza.


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egyéni biztonsági házirendeket. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [A biztonsági házirendek áttekintése](tutorial-security-policy.md)
- [A beépített biztonsági szabályzatok listája](security-center-policy-definitions.md)