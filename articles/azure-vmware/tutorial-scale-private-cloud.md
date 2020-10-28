---
title: Oktatóanyag – privát felhő méretezése
description: Ebben az oktatóanyagban a Azure Portal használatával méretezheti az Azure VMware-megoldás saját felhőjét.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791239"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás – saját felhő méretezése

Annak érdekében, hogy a lehető legtöbbet hozza ki az Azure VMware-megoldás saját felhőalapú környezetében, méretezheti a fürtöket és a gazdagépeket, hogy azok tükrözzék a tervezett munkaterhelésekhez szükséges igényeket. Az alkalmazás számítási feladataihoz szükség szerint méretezheti a fürtöket és a gazdagépeket egy privát felhőben. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni. A fürt és a gazdagép korlátai a [Private Cloud Concept](concepts-private-clouds-clusters.md) cikkben találhatók.

Ebben az oktatóanyagban a Azure Portal a következőkre fogja használni:

> [!div class="checklist"]
> * Fürt hozzáadása meglévő privát felhőhöz
> * Gazdagépek hozzáadása meglévő fürthöz

## <a name="prerequisites"></a>Előfeltételek

Egy privát felhő az oktatóanyag elvégzéséhez. Ha még nem hozott létre privát felhőt, a Create [a Private Cloud oktatóanyag](tutorial-create-private-cloud.md) használatával hozzon létre egyet. Konfigurálja a VPN-t a VMware Private-felhőben az Azure-ban a szükséges virtuális hálózat beállításához.

## <a name="add-a-new-cluster"></a>Új fürt hozzáadása

1. Egy meglévő privát felhő áttekintés lapján kattintson a **kezelés** alatt a **saját felhő méretezése** lehetőségre. Ezután válassza **a + fürt hozzáadása** elemet.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Válassza a fürt hozzáadása lehetőséget" border="true":::

1. A **fürt hozzáadása** lapon a csúszka segítségével válassza ki a gazdagépek számát. Válassza a **Mentés** lehetőséget.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Válassza a fürt hozzáadása lehetőséget" border="true":::

   Ekkor megkezdődik az új fürt üzembe helyezése.

## <a name="scale-a-cluster"></a>Fürt skálázása 

1. Egy meglévő privát felhő áttekintés lapján válassza a **privát felhő méretezése** lehetőséget, és a ceruza ikont választva szerkessze a fürtöt.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="Válassza a fürt hozzáadása lehetőséget" border="true":::

1. A **fürt szerkesztése** lapon a csúszka segítségével válassza ki a gazdagépek számát. Válassza a **Mentés** lehetőséget.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Válassza a fürt hozzáadása lehetőséget" border="true":::

   Elindul a gazdagépek hozzáadása a fürthöz.

## <a name="next-steps"></a>Következő lépések

Ha szüksége van egy másik Azure VMware-megoldás saját felhőre, [hozzon létre egy másik privát felhőt](tutorial-create-private-cloud.md), és kövesse ugyanazt a hálózatkezelési előfeltételeket, a fürtöt és a gazdagép korlátait.

<!-- LINKS - external-->

<!-- LINKS - internal -->
