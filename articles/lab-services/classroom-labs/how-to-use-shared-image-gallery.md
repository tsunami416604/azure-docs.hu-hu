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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653067"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Egy megosztott kép katalógus az Azure Lab Services használatával
Ez a cikk bemutatja, hogyan tanárok/lab rendszergazdai takaríthat meg, hogy a mások által felhasználható sablonnal virtuálisgép-lemezkép. Ezek a lemezképek lesznek mentve az Azure-ban [megosztott lemezkép-katalógusában](../../virtual-machines/windows/shared-image-galleries.md). Első lépésként a labor rendszergazda csatolja egy meglévő megosztott lemezkép-katalógusában a labor-fiók. A csatolást a megosztott lemezkép-katalógusában labs labor-fiókban létrehozott megosztott lemezkép-katalógus rendszerképek mentse. Más tanárok ezt a képet, létrehozhat egy sablont a saját osztályok megosztott lemezkép-katalógus választhatók ki. 

## <a name="prerequisites"></a>Előfeltételek
Hozzon létre egy megosztott lemezkép-katalógus használatával [Azure PowerShell-lel](../../virtual-machines/windows/shared-images.md) vagy [Azure CLI-vel](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Tesztkörnyezetfiók egy megosztott lemezkép-katalógus csatolása
Az alábbi eljárás bemutatja, hogyan tesztkörnyezetfiók csatlakoztassa egy megosztott lemezkép-katalógusában. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben. Válassza ki **Lab Services** a a **fejlesztési és üzemeltetési** szakaszban. Ha bejelöli a csillag (`*`) melletti **Lab Services**, megjelenik a **Kedvencek** szakaszban a bal oldali menüben. És újabb verziók esetében a következő időpont választja **Lab Services** alatt **Kedvencek**.

    ![A Lab Services összes szolgáltatások ->](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Válassza ki a labor fiókját megtekintéséhez a **labor fiók** lapot. 
4. Válassza ki **megosztott képgyűjtemény** a bal oldali menüben, és válassza a **Attach** az eszköztáron. 

    ![Megosztott kép gyűjteménye – gomb hozzáadása](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Az a **csatolja egy meglévő megosztott lemezkép-katalógusában** lapon válassza ki a megosztott lemezkép-katalógusában, és válassza ki **OK**.

    ![Egy meglévő katalógus kiválasztása](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. A következő képernyő jelenik meg: 

    ![A listában a katalógusban](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Ebben a példában nincsenek kép nem megosztott lemezkép-katalógus még.

Az Azure Lab Services identity közreműködője hozzáadódik a megosztott lemezkép-katalógus, amely csatolva van a tesztkörnyezetben. Ez lehetővé teszi az oktatók / menteni a virtuális gép rendszergazdai lemezképek, a megosztott lemezkép-katalógusba. A lab-fiókban létrehozott összes labs rendelkezik hozzáféréssel a csatlakoztatott megosztott lemezkép-katalógusában. 

A csatolt megosztott lemezkép-katalógusban található összes rendszerkép alapértelmezés szerint engedélyezve vannak. Engedélyezheti vagy letilthatja a megadott lemezképek válassza ki azokat a listában, és használatával a **engedélyezése a megadott lemezképek** vagy **tiltsa le a megadott lemezképek** gombra. 

## <a name="detach-a-shared-image-gallery"></a>Egy megosztott képkatalógus leválasztása
Csak egy megosztott lemezkép-katalógusában a laborokhoz csatolható. Ha szeretné, hogy egy másik megosztott lemezkép-katalógus csatolása, leválasztása a jelenlegivel, mielőtt újat csatolása. Leválasztja a tesztkörnyezetben egy megosztott lemezkép-katalógus, válassza ki a **leválasztási** az eszköztáron, majd erősítse meg a leválasztás művelet. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Menti egy képet a megosztott lemezkép-katalógus
Miután egy megosztott lemezkép-katalógus csatolva van, egy oktató mentheti egy sablon rendszerképet megosztott lemezkép-katalógus, a többi tanároknak újrafelhasználása.

![Virtuálisgép-lemezkép mentéséhez a katalógusban](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Rendszerkép használatához a megosztott lemezkép-katalógusban
Oktató/professzor is egyéni lemezkép kiválasztása érhető el a megosztott rendszerkép-katalógusában a sablon új labor létrehozása során.

![Virtuálisgép-rendszerkép használata a katalógusból](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>További lépések
Megosztott kép katalógusokkal kapcsolatos további információkért lásd: [megosztott lemezkép-katalógusában](../../virtual-machines/windows/shared-image-galleries.md).
