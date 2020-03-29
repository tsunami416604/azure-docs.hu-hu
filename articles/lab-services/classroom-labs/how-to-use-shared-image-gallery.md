---
title: Megosztott képgaléria használata az Azure Lab Servicesben | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhatja a tesztkörnyezet-fiókot egy megosztott képtár használatára, hogy a felhasználó megoszthassa a lemezképet másokkal, és egy másik felhasználó a lemezkép segítségével hozzon létre egy sablonvirtuális gép a laborban.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190449"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képtár használata az Azure Lab Servicesben
Ez a cikk bemutatja, hogyan tanárok / labor admin mentheti a sablon virtuálisgép-kép, hogy azt újra mások is. Ezeket a képeket a rendszer egy megosztott [Azure-képgalériába](../../virtual-machines/windows/shared-image-galleries.md)menti. Első lépésként a labor rendszergazdája egy meglévő megosztott képgalériát csatol a laborfiókhoz. A megosztott képgaléria csatlakoztatása után a laborfiókban létrehozott laborok képeket menthet a megosztott képgalériába. Más tanárok is kiválaszthatják ezt a képet a megosztott képgalériából, hogy sablont hozzanak létre az osztályaikszámára. 

> [!NOTE]
> Jelenleg az Azure Lab Services támogatja a sablon virtuális gépek létrehozását, amelyek csak **általános virtuálisgép-lemezképeken** (nem speciális lemezképeken) alapulnak egy megosztott képgalériában. 

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy megosztott képgalériát [az Azure PowerShell](../../virtual-machines/windows/shared-images.md) vagy az [Azure CLI](../../virtual-machines/linux/shared-images.md)használatával.
- A megosztott képgalériát csatolta a tesztkörnyezet-fiókhoz. A megosztott [képgaléria csatolása és leválasztása](how-to-attach-detach-shared-image-gallery.md)témakörben talál részletes útmutatást.


## <a name="save-an-image-to-the-shared-image-gallery"></a>Kép mentése a megosztott képtárba
A megosztott képgaléria csatlakoztatása után a laborfiók rendszergazdája vagy a tanár mentheti a képet a megosztott képgalériába, hogy azt más tanárok újra feltudják használni. 

1. A **tesztkörnyezet Sablon** lapján válassza az **Exportálás megosztott képtárba** lehetőséget az eszköztáron.

    ![Kép mentése gomb](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Az **Exportálás megosztott képtárba** párbeszédpanelen adja meg **a kép nevét**, majd kattintson az Exportálás **gombra.** 

    ![Exportálás a Megosztott képtárba párbeszédpanel](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. A művelet előrehaladását a Sablon lapon **láthatja.** Ez a művelet eltarthat valamikor. 

    ![Folyamatban lévő exportálás](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Ha az exportálási művelet sikeres, a következő üzenet jelenik meg:

    ![Exportálás befejezve](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

A megosztott képtárba is feltölthet egy képet egy tesztkörnyezeten kívül. További információt a [Megosztott képgaléria – áttekintés című témakörben talál.](../../virtual-machines/windows/shared-images.md) 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Kép használata a megosztott képtárból
A tanár/professzor az új laborlétrehozása során kiválaszthatja a megosztott képgalériában elérhető egyéni képet.

![Virtuálisgép-kép használata a galériából](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>További lépések
A megosztott képgalériákról a [megosztott képgalériában](../../virtual-machines/windows/shared-image-galleries.md)talál további információt.
