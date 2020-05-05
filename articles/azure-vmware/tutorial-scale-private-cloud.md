---
title: 'Oktatóanyag: privát felhő méretezése'
description: Ebben az oktatóanyagban a Azure Portal használatával méretezheti az Azure VMware-megoldás (AVS) előzetes verziójának privát felhőjét.
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740289"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás (AVS) – előzetes verziójú privát felhő méretezése

Annak érdekében, hogy a lehető legtöbbet hozza ki a saját AVS előzetes verziójához, a fürtöket és a gazdagépeket úgy méretezheti, hogy megfeleljenek a tervezett munkaterhelésekhez szükséges igényeknek. Mivel az AVS nem támogatja a helyszíni vCenter az előzetes verzióban, a már létrehozott Azure Portal használatával kell használnia.

Az alkalmazás számítási feladataihoz szükség szerint méretezheti a fürtök számát és a privát felhőben lévő gazdagépek számát. Az adott szolgáltatások teljesítmény-és rendelkezésre állási korlátozásait eseti alapon kell kezelni az AVS előzetes felhőalapú környezetében. A privát felhőben a fürt és a gazdagép korlátai a [Private Cloud Concept című cikkben](concepts-private-clouds-clusters.md)találhatók.

Ebben az oktatóanyagban a Azure Portal a következőket használja:

> [!div class="checklist"]
> * Fürt hozzáadása meglévő privát felhőhöz
> * Gazdagépek hozzáadása meglévő fürthöz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy privát felhőre. Ha még nem hozott létre privát felhőt, használja a [create a Private Cloud oktatóanyagot](tutorial-create-private-cloud.md) , és hozzon létre egy privát felhőt, és konfigurálja a VMware Private-felhő hálózatkezelését az Azure-ban a szükséges virtuális hálózat beállításához.

## <a name="add-a-new-cluster"></a>Új fürt hozzáadása

Egy meglévő privát felhő áttekintés lapján kattintson a **kezelés**alatt a **saját felhő méretezése**lehetőségre. Ezután válassza **a + fürt hozzáadása**elemet.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Válassza a fürt hozzáadása lehetőséget" border="true":::

A **fürt hozzáadása** lapon a csúszka segítségével válassza ki a gazdagépek számát. Kattintson a **Mentés** gombra.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="Új privát felhőalapú fürt konfigurálása" border="true":::

Ekkor megkezdődik az új fürt üzembe helyezése.

## <a name="scale-a-cluster"></a>Fürt skálázása 

Egy meglévő privát felhő áttekintés lapján válassza a **privát felhő méretezése** lehetőséget, és a ceruza ikont választva szerkessze a fürtöt.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="A privát felhő méretezése az Áttekintés lapon" border="true":::

A **fürt szerkesztése** lapon a csúszka segítségével válassza ki a gazdagépek számát. Kattintson a **Mentés** gombra.

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="Új privát felhőalapú fürt konfigurálása" border="true":::

Elindul a gazdagépek hozzáadása a fürthöz.

## <a name="next-steps"></a>További lépések

Ha egy másik AVS Private-felhőre van szüksége, [hozzon létre egy másik privát felhőt](tutorial-create-private-cloud.md), amely a hálózat előfeltételeit, a fürt és a gazdagép korlátait követi...

<!-- LINKS - external-->

<!-- LINKS - internal -->
