---
title: Megosztott rendszerkép csatolása vagy leválasztása Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan csatolhat egy megosztott képtárat egy laborhoz Azure Lab Services.
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
ms.openlocfilehash: 4f3f435110f0cba5ea2370f2900d4e7031904067
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890330"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képgyűjtemény csatolása vagy leválasztása Azure Lab Services
A tanárok/laborok rendszergazdái egy sablon virtuálisgép-rendszerképét menthetik az Azure [megosztott rendszerkép](../../virtual-machines/windows/shared-image-galleries.md) -katalógusában, amelyet mások is felhasználhatnak. Első lépésként a tesztkörnyezet rendszergazdája csatol egy meglévő megosztott képtárat a labor-fiókhoz. Miután csatolta a megosztott képtárat, a labor fiókban létrehozott laborok menthetik a lemezképeket a megosztott képkatalógusba. Más oktatók is kiválaszthatják ezt a rendszerképet a megosztott képkatalógusból, hogy sablont hozzanak létre az osztályaik számára. 

Ebből a cikkből megtudhatja, hogyan csatolhat vagy leválaszthatja a megosztott képgyűjteményt egy labor-fiókhoz. 

> [!NOTE]
> Ha egy képet megosztott képkatalógusba ment, Azure Lab Services replikálja a mentett rendszerképet más, azonos [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)régiókban elérhető régiókba. Gondoskodik arról, hogy a rendszerkép elérhető legyen a más régiókban, ugyanabban a földrajzi régióban létrehozott laborokhoz. Ha a képeket egy megosztott képkatalógusba menti, további költségekkel jár, ami magában foglalja az összes replikált rendszerkép díját. Ez a díj eltér a Azure Lab Services használati díjaktól. A megosztott képkatalógus díjszabásával kapcsolatos további információkért lásd: [megosztott képgyűjtemény – számlázás]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a labor-fiók létrehozásakor
Labor-fiók létrehozásakor csatlakoztathat egy megosztott képtárat a labor-fiókhoz. Választhat egy meglévő megosztott képtárat a legördülő listából, vagy létrehozhat egy újat. Megosztott képtárat a labor-fiókhoz való létrehozásához és csatolásához válassza az **új létrehozása**lehetőséget, adja meg a katalógus nevét, majd írja be **az OK gombot**. 

![A megosztott képtárat a labor-fiók létrehozásakor konfigurálja](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurálás a labor-fiók létrehozása után
A labor-fiók létrehozása után a következő feladatokat végezheti el:

- Megosztott Képtár létrehozása és csatolása
- Megosztott képgyűjtemény csatolása a labor-fiókhoz
- Megosztott képgyűjtemény leválasztása a labor-fiókból

## <a name="create-and-attach-a-shared-image-gallery"></a>Megosztott Képtár létrehozása és csatolása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. Válassza a **labor Services** elemet a **DEVOPS** szakaszban. Ha a **Lab szolgáltatások**mellett a csillag (`*`) lehetőséget választja, a rendszer hozzáadja a bal oldali menü **Kedvencek** szakaszához. A következő időponttól kezdve válassza a **Lab szolgáltatások** lehetőséget a **Kedvencek**alatt.

    ![Minden szolgáltatás – > labor Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Válassza ki a labor-fiókját, és tekintse meg a **labor-fiók** lapot. 
4. A bal oldali menüben válassza a **megosztott képgyűjtemény** lehetőséget, majd válassza a **+ Létrehozás** lehetőséget az eszköztáron.  

    ![Megosztott képgyűjtemény létrehozása gomb](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. A **megosztott képgyűjtemény létrehozása** ablakban adja meg a katalógus **nevét** , majd írja be az **OK gombot**. 

    ![Megosztott képgyűjtemény létrehozása ablak](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services létrehozza a megosztott rendszerkép-katalógust, és csatolja azt a labor-fiókhoz. Az ebben a labor-fiókban létrehozott összes labor hozzáfér a csatolt megosztott képtárhoz. 

    ![Csatolt rendszerkép-gyűjtemény](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Az alsó ablaktáblán a lemezképek láthatók a megosztott Képtárban. Ebben az új galériában nincsenek lemezképek. Amikor képeket tölt fel a katalógusba, ezen az oldalon láthatja őket.     

    A csatolt megosztott képtárban lévő összes rendszerkép alapértelmezés szerint engedélyezve van. A kijelölt lemezképek engedélyezéséhez vagy letiltásához jelölje ki őket a listában, és a **kijelölt lemezképek engedélyezése** vagy a **kijelölt képek letiltása** gomb használatával.

## <a name="attach-an-existing-shared-image-gallery"></a>Meglévő megosztott Képtár csatolása
A következő eljárás azt mutatja be, hogyan csatolhat egy meglévő megosztott képtárat egy labor-fiókhoz. 

1. A **labor-fiók** lapon válassza a bal oldali menüben a **megosztott képtára** lehetőséget, majd válassza az eszköztár **csatolás** elemét. 

    ![Megosztott képgyűjtemény – Hozzáadás gomb](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. A **meglévő megosztott rendszerkép csatolása** lapon válassza ki a megosztott képtárat, majd kattintson **az OK gombra**.

    ![Meglévő gyűjtemény kiválasztása](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. A következő képernyő jelenik meg: 

    ![Saját katalógus a listában](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Ebben a példában még nincsenek lemezképek a megosztott rendszerkép-katalógusban.

    Azure Lab Services identitást a rendszer a laborhoz csatolt megosztott Képtár közreműködőiként adja hozzá. Lehetővé teszi a tanárok és a rendszergazda számára a virtuálisgép-lemezképek mentését a megosztott lemezképek gyűjteményében. Az ebben a labor-fiókban létrehozott összes labor hozzáfér a csatolt megosztott képtárhoz. 

    A csatolt megosztott képtárban lévő összes rendszerkép alapértelmezés szerint engedélyezve van. A kijelölt lemezképek engedélyezéséhez vagy letiltásához jelölje ki őket a listában, és a **kijelölt lemezképek engedélyezése** vagy a **kijelölt képek letiltása** gomb használatával. 

## <a name="detach-a-shared-image-gallery"></a>Megosztott Képtár leválasztása
Csak egy megosztott képtárat lehet csatlakoztatni egy laborhoz. Ha egy másik megosztott képtárat szeretne csatolni, válassza le az aktuálisat, mielőtt csatolja az újat. Ha egy megosztott képtárat szeretne leválasztani a laborból, válassza a **Leválasztás** lehetőséget az eszköztáron, és erősítse meg a leválasztási műveletet. 

![A megosztott képgyűjtemény leválasztása a labor-fiókból](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>További lépések
Ha szeretne többet megtudni arról, hogyan mentheti a tesztkörnyezet rendszerképét a megosztott képkatalógusba, vagy hogyan hozhat létre virtuális gépet a megosztott rendszerkép-katalógusban, olvassa el a következő témakört: [a megosztott képgyűjtemény használata](how-to-use-shared-image-gallery.md).

A megosztott képtárakkal kapcsolatos további információkért lásd: [megosztott rendszerkép](../../virtual-machines/windows/shared-image-galleries.md)-katalógus.
