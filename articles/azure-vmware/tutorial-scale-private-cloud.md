---
title: 'Oktatóanyag: privát felhő méretezése'
description: Ebben az oktatóanyagban a Azure Portal használatával méretezheti az Azure VMware-megoldás előzetes privát felhőjét.
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: dddfbddd57f3ad6b541d11c360aeadea4383044a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750434"
---
# <a name="tutorial-scale-an-azure-vmware-solution-preview-private-cloud"></a>Oktatóanyag: Azure-beli VMware-megoldás előnézetének saját felhőbe méretezése

Annak érdekében, hogy a lehető legtöbbet hozza ki az Azure VMware-megoldás előzetes verziójával, a fürtöket és a gazdagépeket úgy méretezheti, hogy azok tükrözzék a tervezett munkaterhelésekhez szükséges igényeket. Mivel az Azure VMware-megoldás nem támogatja a helyszíni vCenter az előzetes verzióban, a Azure Portalon keresztül már létrehozott módon kell használnia.

Az alkalmazás számítási feladataihoz szükség szerint méretezheti a fürtök számát és a privát felhőben lévő gazdagépek számát. Az egyes szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni az Azure VMware-megoldás előzetes felhőalapú környezetében. A privát felhőben a fürt és a gazdagép korlátai a [Private Cloud Concept című cikkben](concepts-private-clouds-clusters.md)találhatók.

Ebben az oktatóanyagban a Azure Portal a következőket használja:

> [!div class="checklist"]
> * Fürt hozzáadása meglévő privát felhőhöz
> * Gazdagépek hozzáadása meglévő fürthöz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy privát felhőre. Ha még nem hozott létre privát felhőt, használja a [create a Private Cloud oktatóanyagot](tutorial-create-private-cloud.md) , és hozzon létre egy privát felhőt, és konfigurálja a VMware Private-felhő hálózatkezelését az Azure-ban a szükséges virtuális hálózat beállításához.

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

## <a name="next-steps"></a>További lépések

Ha szüksége van egy másik Azure VMware-megoldás saját felhőre, [hozzon létre egy másik privát felhőt](tutorial-create-private-cloud.md), amely a hálózat előfeltételeit, a fürt és a gazdagép korlátait követi.

<!-- LINKS - external-->

<!-- LINKS - internal -->
