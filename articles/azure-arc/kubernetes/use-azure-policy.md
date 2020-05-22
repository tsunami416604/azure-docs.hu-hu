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
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779946"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>A Azure Policy használata a fürtök konfigurációjának alkalmazásához (előzetes verzió)

## <a name="overview"></a>Áttekintés

A Azure Policy használatával kényszerítheti ki, hogy az egyes `Microsoft.Kubernetes/connectedclusters` vagy a git-Ops-kompatibilis `Microsoft.ContainerService/managedClusters` erőforrások egyediek legyenek `Microsoft.KubernetesConfiguration/sourceControlConfigurations` alkalmazva.  A Azure Policy használatához válasszon ki egy meglévő szabályzat-definíciót, és hozzon létre egy szabályzat-hozzárendelést.  A szabályzat-hozzárendelés létrehozásakor meg kell adni a hozzárendelés hatókörét: ez egy Azure-erőforráscsoport vagy-előfizetés lesz.  A létrehozandó paramétereket is megadja `sourceControlConfiguration` .  A hozzárendelés létrehozása után a házirend-kezelő a `connectedCluster` `managedCluster` hatókörön belül található összes vagy erőforrást azonosítja, és alkalmazza a `sourceControlConfiguration` -t mindegyikre.

Ha több git-adattárakat használ az igazság forrásaként az egyes fürtökhöz (például egy központi IT-/fürt-kezelő és az alkalmazás-munkacsoportokhoz tartozó más adattárakhoz), akkor ezt több házirend-hozzárendelés használatával is engedélyezheti, és minden egyes házirend-hozzárendelés más git-tárház használatára van konfigurálva.

## <a name="create-a-custom-policy-definition"></a>Egyéni szabályzatdefiníció létrehozása

1. A Azure Portalban navigáljon a házirend elemre, és az oldalsáv **szerzői műveletek** területén válassza a **definíciók**lehetőséget.
2. Válassza a **+ szabályzat definíciója**lehetőséget.
3. Állítsa be a **definíció helyét** az előfizetéséhez vagy a felügyeleti csoportjához.  Ez határozza meg azt a legszélesebb hatókört, ahol a házirend-definíció használható.
4. Adja meg a szabályzat **nevét** és **leírását**.
5. A kategória területen válassza az **új létrehozása**és a *Kubernetes-fürt írása lehetőséget – Azure arc*
6. A **házirend-szabály** szerkesztése mezőben másolja vagy illessze be a példában szereplő [házirend-definíció](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json)tartalmát.
7. **Mentés**.

Erre a lépésre nincs szükség egyéni szabályzat-definíció létrehozásához, ha a munka befejeződött, hogy ez egy beépített szabályzat legyen.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

1. A Azure Portalban navigáljon a házirend elemre, és az oldalsáv **szerzői műveletek** területén válassza a **definíciók**lehetőséget.
2. Keresse meg az imént létrehozott definíciót, és válassza ki.
3. A lap műveletek területén válassza a **hozzárendelés**lehetőséget.
4. Állítsa be a **hatókört** arra a felügyeleti csoportra, előfizetésre vagy erőforráscsoportre, ahol a házirend-hozzárendelés érvényes lesz.
5. Ha ki szeretne zárni minden erőforrást a szabályzat hatóköréről, állítsa be a **kizárásokat**.
6. Adja meg a szabályzat hozzárendelésének **nevét** és **leírását** , hogy könnyen azonosítható legyen.
7. Győződjön meg arról, hogy a **házirend-kényszerítés** *engedélyezve*értékre van állítva.
8. Kattintson a **Tovább** gombra.
9. Állítsa be a paraméter értékét, amelyet a rendszer a létrehozásakor használni fog `sourceControlConfiguration` .
10. Kattintson a **Tovább** gombra.
11. **Szervizelési feladat létrehozásának**engedélyezése.
12. Gondoskodjon róla, hogy a **felügyelt identitás létrehozása jelölőnégyzet be** legyen jelölve, és hogy az identitás **közreműködői** engedélyekkel rendelkezzen.  A szükséges engedélyekkel kapcsolatos további információkért tekintse meg [ezt](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) a dokumentumot és [a jelen dokumentum megjegyzését](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) .
13. Válassza az **Áttekintés + létrehozás** lehetőséget.

A szabályzat-hozzárendelés létrehozása után minden olyan új `connectedCluster` erőforráshoz (vagy `managedCluster` a telepített GitOps-ügynökkel rendelkező erőforráshoz), amely a hozzárendelés hatókörén belül található, a `sourceControlConfiguration` rendszer alkalmazza.  Meglévő fürtök esetében manuálisan kell futtatnia egy szervizelési feladatot.  A házirend-hozzárendelés érvénybe léptetése általában 10-20 percet vesz igénybe.

## <a name="verify-a-policy-assignment"></a>Szabályzat-hozzárendelés ellenőrzése

1. A Azure Portal navigáljon az egyik `connectedCluster` erőforráshoz, és az oldalsáv **Beállítások** szakaszában válassza a **házirendek**elemet. (Az AK által felügyelt fürthöz tartozó UX még nincs implementálva, de hamarosan elérhető.)
2. A listában meg kell jelennie a fent létrehozott szabályzat-hozzárendelésnek, és a **megfelelőségi állapotnak** *megfelelőnek*kell lennie.
3. Az oldalsáv **Beállítások** szakaszában válassza a **konfigurációk**lehetőséget.
4. A listában látnia kell, `sourceControlConfiguration` hogy a szabályzat-hozzárendelés létrejött.
5. A **kubectl** használata a fürt lekérdezéséhez: a által létrehozott névtereket és összetevőket kell látnia `sourceControlConfiguration` .
6. 5 percen belül látnia kell a fürtben a beállított git-tárház jegyzékfájljában leírt összetevőket.

## <a name="next-steps"></a>További lépések

* [Azure Monitor beállítása az arc-kompatibilis Kubernetes-fürtökkel rendelkező tárolók számára](./deploy-azure-monitor-for-containers.md)
