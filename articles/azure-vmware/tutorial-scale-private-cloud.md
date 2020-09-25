---
title: Oktatóanyag – privát felhő méretezése
description: Ebben az oktatóanyagban a Azure Portal használatával méretezheti az Azure VMware-megoldás saját felhőjét.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: e1226eb98607a34869bda1f998c7cecea2e50919
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254414"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás – saját felhő méretezése

Annak érdekében, hogy a lehető legtöbbet hozza ki az Azure VMware-megoldás saját felhőalapú környezetében, méretezheti a fürtöket és a gazdagépeket, hogy azok tükrözzék a tervezett munkaterhelésekhez szükséges igényeket. Az alkalmazás számítási feladataihoz szükség szerint méretezheti a fürtök számát és a privát felhőben lévő gazdagépek számát. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni az Azure VMware-megoldás saját felhőalapú környezetében. A fürt és a gazdagép korlátai a [Private Cloud Concept](concepts-private-clouds-clusters.md) cikkben találhatók.

Ebben az oktatóanyagban a Azure Portal a következőkre fogja használni:

> [!div class="checklist"]
> * Fürt hozzáadása meglévő privát felhőhöz
> * Gazdagépek hozzáadása meglévő fürthöz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy privát felhőre. Ha még nem hozott létre privát felhőt, a [create a Private Cloud tutorial (privát felhő létrehozása) oktatóanyag](tutorial-create-private-cloud.md) használatával hozzon létre egyet, és konfigurálja a VPN-t az Azure-ban, hogy beállítsa a szükséges virtuális hálózatot.

## <a name="add-a-new-cluster"></a>Új fürt hozzáadása

1. Egy meglévő privát felhő áttekintés lapján kattintson a **kezelés**alatt a **saját felhő méretezése**lehetőségre. Ezután válassza **a + fürt hozzáadása**elemet.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Válassza a fürt hozzáadása lehetőséget" border="true":::

1. A **fürt hozzáadása** lapon a csúszka segítségével válassza ki a gazdagépek számát. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="A fürt hozzáadása lapon a csúszka segítségével válassza ki a gazdagépek számát. Válassza a mentés lehetőséget." border="true":::

   Ekkor megkezdődik az új fürt üzembe helyezése.

## <a name="scale-a-cluster"></a>Fürt skálázása 

1. Egy meglévő privát felhő áttekintés lapján válassza a **privát felhő méretezése** lehetőséget, és a ceruza ikont választva szerkessze a fürtöt.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="A privát felhő méretezése az Áttekintés lapon" border="true":::

1. A **fürt szerkesztése** lapon a csúszka segítségével válassza ki a gazdagépek számát. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="A fürt szerkesztése lapon a csúszka segítségével válassza ki a gazdagépek számát. Válassza a mentés lehetőséget." border="true":::

   Elindul a gazdagépek hozzáadása a fürthöz.

## <a name="next-steps"></a>Következő lépések

Ha szüksége van egy másik Azure VMware-megoldás saját felhőre, [hozzon létre egy másik privát felhőt](tutorial-create-private-cloud.md), amely a hálózat előfeltételeit, a fürt és a gazdagép korlátait követi.

<!-- LINKS - external-->

<!-- LINKS - internal -->
