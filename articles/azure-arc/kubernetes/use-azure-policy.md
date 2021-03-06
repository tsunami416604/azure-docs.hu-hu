---
title: A Azure Policy használata a fürtök konfigurációjának alkalmazásához (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: A Azure Policy használata a fürt konfigurációjának méretekben történő alkalmazásához
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050046"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>A Azure Policy használata a fürtök konfigurációjának alkalmazásához (előzetes verzió)

## <a name="overview"></a>Áttekintés

A Azure Policy használatával kényszerítheti ki, hogy minden `Microsoft.Kubernetes/connectedclusters` erőforrás vagy Git-Ops engedélyezett `Microsoft.ContainerService/managedClusters` erőforrás külön `Microsoft.KubernetesConfiguration/sourceControlConfigurations` legyen alkalmazva. A Azure Policy használatához válasszon ki egy meglévő szabályzat-definíciót, és hozzon létre egy szabályzat-hozzárendelést. A szabályzat-hozzárendelés létrehozásakor meg kell adni a hozzárendelés hatókörét: ez egy Azure-erőforráscsoport vagy-előfizetés lesz. A létrehozandó paramétereket is megadja `sourceControlConfiguration` . A hozzárendelés létrehozása után a házirend-kezelő a `connectedCluster` `managedCluster` hatókörön belül található összes vagy erőforrást azonosítja, és alkalmazza a `sourceControlConfiguration` -t mindegyikre.

Ha több git-adattárakat használ az igazság forrásaként az egyes fürtökhöz (például egy központi IT-/fürt-kezelő és az alkalmazás-munkacsoportokhoz tartozó más adattárakhoz), akkor ezt több házirend-hozzárendelés használatával is engedélyezheti, és minden egyes házirend-hozzárendelés más git-tárház használatára van konfigurálva.

## <a name="prerequisite"></a>Előfeltétel

Győződjön meg arról, hogy rendelkezik `Microsoft.Authorization/policyAssignments/write` a szabályzat-hozzárendelés létrehozásához szükséges hatókörrel (előfizetés vagy erőforráscsoport).

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

1. A Azure Portalban navigáljon a házirend elemre, és az oldalsáv **szerzői műveletek** területén válassza a **definíciók**lehetőséget.
2. A "Kubernetes" kategóriában válassza a "GitOps üzembe helyezése a Kubernetes-fürthöz" beépített szabályzatot, majd kattintson a **hozzárendelés**elemre.
3. Állítsa be a **hatókört** arra a felügyeleti csoportra, előfizetésre vagy erőforráscsoportre, ahol a házirend-hozzárendelés érvényes lesz.
4. Ha ki szeretne zárni minden erőforrást a szabályzat hatóköréről, állítsa be a **kizárásokat**.
5. Adja meg a szabályzat hozzárendelésének **nevét** és **leírását** , hogy könnyen azonosítható legyen.
6. Győződjön meg arról, hogy a **házirend-kényszerítés** *engedélyezve*értékre van állítva.
7. Kattintson a **Tovább** gombra.
8. Állítsa be a paraméter értékét, amelyet a rendszer a létrehozásakor használni fog `sourceControlConfiguration` .
9. Kattintson a **Tovább** gombra.
10. **Szervizelési feladat létrehozásának**engedélyezése.
11. Gondoskodjon róla, hogy a **felügyelt identitás létrehozása jelölőnégyzet be** legyen jelölve, és hogy az identitás **közreműködői** engedélyekkel rendelkezzen. A szükséges engedélyekkel kapcsolatos további információkért tekintse meg [ezt](../../governance/policy/assign-policy-portal.md) a dokumentumot és [a jelen dokumentum megjegyzését](../../governance/policy/how-to/remediate-resources.md) .
12. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.

A szabályzat-hozzárendelés létrehozása után minden olyan új `connectedCluster` erőforráshoz (vagy `managedCluster` a telepített GitOps-ügynökkel rendelkező erőforráshoz), amely a hozzárendelés hatókörén belül található, a `sourceControlConfiguration` rendszer alkalmazza. Meglévő fürtök esetében manuálisan kell futtatnia egy szervizelési feladatot. A házirend-hozzárendelés érvénybe léptetése általában 10-20 percet vesz igénybe.

## <a name="verify-a-policy-assignment"></a>Szabályzat-hozzárendelés ellenőrzése

1. A Azure Portal navigáljon az egyik `connectedCluster` erőforráshoz, és az oldalsáv **Beállítások** szakaszában válassza a **házirendek**elemet. (Az AK-hoz készült UX-fürthöz még nincs implementálva, de a rendszer már nem érhető el.)
2. A listában meg kell jelennie a fent létrehozott szabályzat-hozzárendelésnek, és a **megfelelőségi állapotnak** *megfelelőnek*kell lennie.
3. Az oldalsáv **Beállítások** szakaszában válassza a **konfigurációk**lehetőséget.
4. A listában látnia kell, `sourceControlConfiguration` hogy a szabályzat-hozzárendelés létrejött.
5. A **kubectl** használata a fürt lekérdezéséhez: a által létrehozott névtereket és összetevőket kell látnia `sourceControlConfiguration` .
6. 5 percen belül látnia kell a fürtben a beállított git-tárház jegyzékfájljában leírt összetevőket.

## <a name="next-steps"></a>További lépések

* [Azure Monitor beállítása az arc-kompatibilis Kubernetes-fürtökkel rendelkező tárolók számára](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
