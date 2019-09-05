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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 5ce0a81f6c9c886fcbe9186dd7363c38170ca580
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382952"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képgyűjtemény használata Azure Lab Services
Ebből a cikkből megtudhatja, hogy a tanárok/labor-rendszergazdák hogyan menthetik a sablonhoz tartozó virtuálisgép-rendszerképeket, hogy azok újra felhasználhatók legyenek Ezeket a lemezképeket egy Azure-beli [megosztott rendszerkép](../../virtual-machines/windows/shared-image-galleries.md)-katalógusba menti a rendszer. Első lépésként a tesztkörnyezet rendszergazdája csatol egy meglévő megosztott képtárat a labor-fiókhoz. Miután csatolta a megosztott képtárat, a labor fiókban létrehozott laborok menthetik a lemezképeket a megosztott képkatalógusba. Más oktatók is kiválaszthatják ezt a rendszerképet a megosztott képkatalógusból, hogy sablont hozzanak létre az osztályaik számára. 

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy megosztott képtárat [Azure PowerShell](../../virtual-machines/windows/shared-images.md) vagy az [Azure CLI](../../virtual-machines/linux/shared-images.md)használatával.
- Csatlakoztatta a megosztott képtárat a labor-fiókhoz. Részletes útmutatásért lásd: [megosztott képgyűjtemény csatolása vagy leválasztása](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Rendszerkép mentése a megosztott képtárba
Egy megosztott képkatalógus csatolása után egy labor-fiók rendszergazdája vagy egy oktató mentheti a képeket a megosztott képkatalógusba, hogy más tanárok is felhasználhatják őket. 

1. A labor kezdőlapján kattintson a **rendszerkép mentése** gombra a **sablon** szakaszban található csempén.

    ![Rendszerkép mentése gomb](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  A **virtuális gép rendszerképének mentése** ablakban adja meg a rendszerkép nevét, majd kattintson a **Mentés**gombra. 

    ![Virtuális gép rendszerképének mentése ablak](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. Keresse meg az állapotot a labor csempén. 

    ![A rendszerkép mentése művelet állapota](../media/how-to-use-shared-image-gallery/save-image-status.png)

 Képeket is feltölthet a megosztott rendszerkép-katalógusba egy labor kontextusán kívül. További információ: [megosztott képgyűjtemény – áttekintés](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Rendszerkép használata a megosztott rendszerkép-gyűjteményből
Egy tanár/professzor kiválaszthat egy egyéni rendszerképet, amely a sablon megosztott képgalériájában elérhető az új tesztkörnyezet létrehozása során.

![Virtuálisgép-rendszerkép használata a katalógusból](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>További lépések
A megosztott képtárakkal kapcsolatos további információkért lásd a [megosztott képtárat](../../virtual-machines/windows/shared-image-galleries.md).
