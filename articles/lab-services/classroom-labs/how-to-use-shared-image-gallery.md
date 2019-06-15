---
title: Használja az Azure Lab Services egy megosztott képkatalógus |} A Microsoft Docs
description: Ismerje meg, hogy egy felhasználói lemezkép megoszthatja más, és a egy másik felhasználó rendszerképét is használhatják a virtuális gép sablon létrehozása a lab-ben használandó megosztott képkatalógus tesztkörnyezetfiók beállítása.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412854"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Egy megosztott kép katalógus az Azure Lab Services használatával
Ez a cikk bemutatja, hogyan tanárok/lab rendszergazdai takaríthat meg, hogy a mások által felhasználható sablonnal virtuálisgép-lemezkép. Ezek a lemezképek lesznek mentve az Azure-ban [megosztott lemezkép-katalógusában](../../virtual-machines/windows/shared-image-galleries.md). Első lépésként a labor rendszergazda csatolja egy meglévő megosztott lemezkép-katalógusában a labor-fiók. A csatolást a megosztott lemezkép-katalógusában labs labor-fiókban létrehozott megosztott lemezkép-katalógus rendszerképek mentse. Más tanárok ezt a képet, létrehozhat egy sablont a saját osztályok megosztott lemezkép-katalógus választhatók ki. 

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy megosztott lemezkép-katalógus használatával [Azure PowerShell-lel](../../virtual-machines/windows/shared-images.md) vagy [Azure CLI-vel](../../virtual-machines/linux/shared-images.md).
- A lab-fiókjához kapcsolt megosztott lemezkép-katalógus. Lépésenkénti útmutatásért lásd: [csatolása vagy leválasztása megosztott lemezkép-katalógusában](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Menti egy képet a megosztott lemezkép-katalógus
Egy megosztott képgyűjtemény van csatolva, miután egy oktató mentheti vagy töltsön fel egy képet megosztott lemezkép-katalógus, így felhasználhatók más oktatók által. Kép feltöltése a megosztott lemezkép-katalógusba, lásd: [megosztott Képkatalógus áttekintése](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Az osztályterem-tesztkörnyezetek felhasználói felületének (UI) curently, nem támogatja a megosztott lemezkép-katalógus mentése folyamatban van egy tesztlabor-lemezképet. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Rendszerkép használatához a megosztott lemezkép-katalógusban
Oktató/professzor is egyéni lemezkép kiválasztása érhető el a megosztott rendszerkép-katalógusában a sablon új labor létrehozása során.

![Virtuálisgép-rendszerkép használata a katalógusból](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>További lépések
Megosztott kép katalógusokkal kapcsolatos további információkért lásd: [megosztott lemezkép-katalógusában](../../virtual-machines/windows/shared-image-galleries.md).
