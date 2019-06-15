---
title: Csatolása vagy leválasztása az Azure Lab Services egy megosztott képkatalógus |} A Microsoft Docs
description: Ismerje meg, egy megosztott képkatalógus csatlakoztatása az Azure Lab Services szolgáltatásban létrehozott tesztkörnyezet.
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
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413890"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Csatolása vagy leválasztása az Azure Lab Services egy megosztott lemezkép-katalógusában
Az oktatók és labor rendszergazdai mentheti a sablon Virtuálisgép-lemezkép az Azure-ban [megosztott lemezkép-katalógusában](../../virtual-machines/windows/shared-image-galleries.md) ahhoz, hogy a mások által felhasználható. Első lépésként a labor rendszergazda csatolja egy meglévő megosztott lemezkép-katalógusában a labor-fiók. A csatolást a megosztott lemezkép-katalógusában labs labor-fiókban létrehozott megosztott lemezkép-katalógus rendszerképek mentse. Más tanárok ezt a képet, létrehozhat egy sablont a saját osztályok megosztott lemezkép-katalógus választhatók ki. 

Ez a cikk bemutatja, hogyan csatolása vagy leválasztása a tesztkörnyezetfiók egy megosztott lemezkép-katalógusában. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>A labor létrehozása idején konfigurálása
Tesztkörnyezetfiók hoz létre, amikor egy megosztott lemezkép-katalógusában is csatlakoztatható a labor-fiókhoz. Válasszon egy meglévő megosztott lemezkép-katalógusában a legördülő listából, vagy hozzon létre egy újat. Szeretne létrehozni, és a egy megosztott lemezkép-katalógusában a labor fiókhoz csatolni, válassza ki a **új létrehozása**, adja meg a gyűjtemény nevét, és adja meg **OK**. 

![A labor létrehozása idején megosztott lemezkép-katalógusában konfigurálása](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>A lab-fiók létrehozása után konfigurálása
A lab-fiók létrehozása után a következő feladatokat is végezhet:

- Létrehozásához és csatlakoztatásához egy megosztott lemezkép-katalógusában
- Egy megosztott lemezkép-katalógusában a labor fiók csatolása
- A lab-fiókból egy megosztott képkatalógus leválasztása

## <a name="create-and-attach-a-shared-image-gallery"></a>Létrehozásához és csatlakoztatásához egy megosztott lemezkép-katalógusában
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben. Válassza ki **Lab Services** a a **fejlesztési és üzemeltetési** szakaszban. Ha bejelöli a csillag (`*`) melletti **Lab Services**, megjelenik a **Kedvencek** szakaszban a bal oldali menüben. És újabb verziók esetében a következő időpont választja **Lab Services** alatt **Kedvencek**.

    ![A Lab Services összes szolgáltatások ->](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Válassza ki a labor fiókját megtekintéséhez a **labor fiók** lapot. 
4. Válassza ki **megosztott képgyűjtemény** a bal oldali menüben, és válassza a **+ létrehozás** az eszköztáron.  

    ![Megosztott kép katalógus gomb létrehozása](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Az a **létrehozás megosztott képgyűjtemény** ablakban adja meg egy **neve** katalógus, és adja meg **OK**. 

    ![Megosztott kép katalógus időszak létrehozása](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Az Azure Lab Services hoz létre a megosztott lemezkép-katalógusban, és csatolja azt a labor-fiókot. A lab-fiókban létrehozott összes labs rendelkezik hozzáféréssel a csatlakoztatott megosztott lemezkép-katalógusában. 

    ![Csatlakoztatott lemezkép-katalógusában](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Az alsó ablaktáblában tekintse meg a megosztott lemezkép-katalógusában található rendszerképek. Az új gyűjtemény nincsenek nincs kép. Képek feltöltéséhez a katalógusban, amikor megjelennek ezen az oldalon.     

    A csatolt megosztott lemezkép-katalógusban található összes rendszerkép alapértelmezés szerint engedélyezve vannak. Engedélyezheti vagy letilthatja a megadott lemezképek válassza ki azokat a listában, és használatával a **engedélyezése a megadott lemezképek** vagy **tiltsa le a megadott lemezképek** gombra.

## <a name="attach-an-existing-shared-image-gallery"></a>Egy meglévő megosztott lemezkép-katalógus csatolása
Az alábbi eljárás bemutatja, hogyan tesztkörnyezetfiók csatlakoztassa egy meglévő megosztott lemezkép-katalógusában. 

1. A a **labor fiók** lapon válassza ki **megosztott lemezkép-katalógusában** a bal oldali menüben, és válassza a **Attach** az eszköztáron. 

    ![Megosztott kép gyűjteménye – gomb hozzáadása](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Az a **csatolja egy meglévő megosztott lemezkép-katalógusában** lapon válassza ki a megosztott lemezkép-katalógusában, és válassza ki **OK**.

    ![Egy meglévő katalógus kiválasztása](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. A következő képernyő jelenik meg: 

    ![A listában a katalógusban](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Ebben a példában nincsenek kép nem megosztott lemezkép-katalógus még.

    Az Azure Lab Services identity közreműködője hozzáadódik a megosztott lemezkép-katalógus, amely csatolva van a tesztkörnyezetben. Ez lehetővé teszi az oktatók / menteni a virtuális gép rendszergazdai lemezképek, a megosztott lemezkép-katalógusba. A lab-fiókban létrehozott összes labs rendelkezik hozzáféréssel a csatlakoztatott megosztott lemezkép-katalógusában. 

    A csatolt megosztott lemezkép-katalógusban található összes rendszerkép alapértelmezés szerint engedélyezve vannak. Engedélyezheti vagy letilthatja a megadott lemezképek válassza ki azokat a listában, és használatával a **engedélyezése a megadott lemezképek** vagy **tiltsa le a megadott lemezképek** gombra. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Menti egy képet a megosztott lemezkép-katalógus
Miután egy megosztott lemezkép-katalógus csatolva van, egy tesztlabor fiókadminisztrátor vagy egy oktató mentheti vagy töltsön fel egy képet megosztott lemezkép-katalógus, így felhasználhatók más oktatók által. Kép feltöltése a megosztott lemezkép-katalógusba, lásd: [megosztott Képkatalógus áttekintése](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Az osztályterem-tesztkörnyezetek felhasználói felületének (UI) curently, nem támogatja a megosztott lemezkép-katalógus mentése folyamatban van egy tesztlabor-lemezképet. 

## <a name="detach-a-shared-image-gallery"></a>Egy megosztott képkatalógus leválasztása
Csak egy megosztott lemezkép-katalógusában a laborokhoz csatolható. Ha szeretné, hogy egy másik megosztott lemezkép-katalógus csatolása, leválasztása a jelenlegivel, mielőtt újat csatolása. Leválasztja a tesztkörnyezetben egy megosztott lemezkép-katalógus, válassza ki a **leválasztási** az eszköztáron, majd erősítse meg a leválasztás művelet. 

![Válassza le a megosztott lemezkép-katalógusában a labor-fiókból](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>További lépések
Menti egy tesztlabor képet a megosztott lemezkép-katalógus vagy a rendszerkép használatához hozzon létre egy virtuális Gépet a megosztott lemezkép-katalógus kapcsolatos tudnivalókért lásd: [megosztott lemezkép-katalógus használata](how-to-use-shared-image-gallery.md).

További információ a lemezkép-katalógusok általában megosztott, lásd: [megosztott lemezkép-katalógusában](../../virtual-machines/windows/shared-image-galleries.md).
