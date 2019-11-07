---
title: Közös rendszerkép-katalógus használata Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Lab-fiókot megosztott képkatalógus használatára úgy, hogy egy felhasználó megoszthat egy képet a másikkal, és egy másik felhasználó is használhatja a rendszerképet a sablon létrehozásához a laborban.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: f438c32deb7e923f08396b0580d807d6e5b5e69a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585042"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képgyűjtemény használata Azure Lab Services
Ebből a cikkből megtudhatja, hogy a tanárok/labor-rendszergazdák hogyan menthetik a sablonhoz tartozó virtuálisgép-rendszerképeket, hogy azok újra felhasználhatók legyenek Ezeket a lemezképeket egy Azure-beli [megosztott rendszerkép](../../virtual-machines/windows/shared-image-galleries.md)-katalógusba menti a rendszer. Első lépésként a tesztkörnyezet rendszergazdája csatol egy meglévő megosztott képtárat a labor-fiókhoz. Miután csatolta a megosztott képtárat, a labor fiókban létrehozott laborok menthetik a lemezképeket a megosztott képkatalógusba. Más oktatók is kiválaszthatják ezt a rendszerképet a megosztott képkatalógusból, hogy sablont hozzanak létre az osztályaik számára. 

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy megosztott képtárat [Azure PowerShell](../../virtual-machines/windows/shared-images.md) vagy az [Azure CLI](../../virtual-machines/linux/shared-images.md)használatával.
- Csatlakoztatta a megosztott képtárat a labor-fiókhoz. Részletes útmutatásért lásd: [megosztott képgyűjtemény csatolása vagy leválasztása](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Rendszerkép mentése a megosztott képtárba
Egy megosztott képkatalógus csatolása után egy labor-fiók rendszergazdája vagy egy oktató mentheti a képeket a megosztott képkatalógusba, hogy más tanárok is felhasználhatják őket. 

1. A labor **sablon** lapján válassza az **Exportálás a közös rendszerkép** -katalógusba lehetőséget az eszköztáron.

    ![Rendszerkép mentése gomb](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Az **Exportálás megosztott rendszerkép** -katalógusba párbeszédpanelen adja meg a **rendszerkép nevét**, majd válassza az **Exportálás**lehetőséget. 

    ![Exportálás megosztott képgyűjteménybe párbeszédpanel](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. A művelet előrehaladását a **sablon** lapon tekintheti meg. A művelet eltarthat egy ideig. 

    ![Exportálás folyamatban](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Ha az exportálási művelet sikeres, a következő üzenet jelenik meg:

    ![Az Exportálás befejeződött](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Képeket is feltölthet a megosztott rendszerkép-katalógusba egy labor kontextusán kívül. További információ: [megosztott képgyűjtemény – áttekintés](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Rendszerkép használata a megosztott rendszerkép-gyűjteményből
Egy tanár/professzor kiválaszthat egy egyéni rendszerképet, amely a sablon megosztott képgalériájában elérhető az új tesztkörnyezet létrehozása során.

![Virtuálisgép-rendszerkép használata a katalógusból](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>További lépések
A megosztott képtárakkal kapcsolatos további információkért lásd a [megosztott képtárat](../../virtual-machines/windows/shared-image-galleries.md).
