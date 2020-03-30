---
title: Megosztott képgaléria csatolása vagy leválasztása az Azure Lab Servicesben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan csatolhat egy megosztott képgalériát egy osztályteremben az Azure Lab Services-ben.
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
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284316"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képgaléria csatolása vagy leválasztása az Azure Lab Servicesben
Tanárok/labor rendszergazda mentheti a sablon virtuális gép kép egy Azure [megosztott képgalériában,](../../virtual-machines/windows/shared-image-galleries.md) hogy azt újra mások is felhasználhatja. Első lépésként a labor rendszergazdája egy meglévő megosztott képgalériát csatol a laborfiókhoz. A megosztott képgaléria csatlakoztatása után a laborfiókban létrehozott laborok képeket menthet a megosztott képgalériába. Más tanárok is kiválaszthatják ezt a képet a megosztott képgalériából, hogy sablont hozzanak létre az osztályaikszámára. 

Amikor egy képet egy megosztott képtárba ment, az Azure Lab Services replikálja a mentett képet az azonos [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)területen elérhető más régiókba. Biztosítja, hogy a rendszerkép elérhető más régiókban, ugyanabban a földrajzi területen létrehozott laborok számára. A képek megosztott képgalériába mentése további költségekkel jár, ami az összes replikált kép költségét is magában foglalja. Ez a költség eltér az Azure Lab Services használati költség. A megosztott képtár díjszabásáról további információt a [Megosztott képtár – Számlázás című témakörben talál.]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)

Ez a cikk bemutatja, hogyan csatolhat vagy választhat le egy megosztott képgalériát egy tesztkörnyezet-fiókhoz. 

> [!NOTE]
> Jelenleg az Azure Lab Services támogatja a sablon virtuális gépek létrehozását, amelyek csak **általános virtuálisgép-lemezképeken** (nem speciális lemezképeken) alapulnak egy megosztott képgalériában. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a tesztkörnyezet-fiók létrehozásakor
Laborfiók létrehozásakor egy megosztott képgalériát csatolhat a tesztkörnyezet-fiókhoz. Kiválaszthat egy meglévő megosztott képgalériát a legördülő listából, vagy létrehozhat egy újat. Ha megosztott képgyűjteményt szeretne létrehozni és csatolni a tesztkörnyezet-fiókhoz, válassza az **Új létrehozása lehetőséget,** adja meg a gyűjtemény nevét, és írja be az **OK gombot.** 

![Megosztott képgaléria konfigurálása a tesztkörnyezet-fiók létrehozásakor](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurálás a tesztkörnyezet-fiók létrehozása után
A tesztkörnyezet-fiók létrehozása után a következő feladatokat teheti meg:

- Megosztott képgaléria létrehozása és csatolása
- Megosztott képtár csatolása a laborfiókhoz
- Megosztott képtár leválasztása a laborfiókról

## <a name="create-and-attach-a-shared-image-gallery"></a>Megosztott képgaléria létrehozása és csatolása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.** Válassza a **Lab Services** a **DEVOPS** szakaszban. Ha a Lab`*`Services **(Csillag)** lehetőséget választja a Lab Services mellett, az a bal oldali menü **KEDVENCEK** szakaszába kerül. A következő alkalommal válassza lab **szolgáltatások** alatt **KEDVENCEK**.

    ![Minden szolgáltatás -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Válassza ki a laborfiókot a **Labor-fiók** lap megtekintéséhez. 
4. Válassza a bal oldali menü **Megosztott képgyűjteményének** parancsát, majd az **eszköztáron** a + Create parancsot.  

    ![Megosztott képgaléria létrehozása gomb](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. A **Megosztott képgyűjtemény létrehozása** ablakban adja meg a gyűjtemény **nevét,** és írja be az **OK parancsot.** 

    ![Megosztott képgaléria ablakának létrehozása](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Az Azure Lab Services létrehozza a megosztott képgalériát, és csatolja a laborfiókhoz. A laborfiókban létrehozott összes tesztkörnyezet hozzáférhet a csatolt megosztott képgalériához. 

    ![Csatolt képgaléria](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Az alsó ablaktáblán képek láthatók a megosztott képgalériában. Ebben az új galériában nincsenek képek. Amikor képeket tölt fel a galériába, ezen az oldalon láthatja őket.     

    A csatolt megosztott képtárban lévő összes kép alapértelmezés szerint engedélyezve van. A kijelölt képeket engedélyezheti vagy letilthatja, ha kijelöli őket a listában, és a **Kijelölt képek engedélyezése** vagy a Kijelölt képek **letiltása** gombbal használja őket.

## <a name="attach-an-existing-shared-image-gallery"></a>Meglévő megosztott képtár csatolása
Az alábbi eljárás bemutatja, hogyan csatolhat egy meglévő megosztott képgalériát egy tesztkörnyezet-fiókhoz. 

1. A **Lab-fiók** lapon válassza a bal oldali menü **Megosztott képgyűjteményparancsát,** majd az eszköztár **Csatolás parancsát.** 

    ![Megosztott képgaléria - Hozzáadás gomb](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. A **Meglévő megosztott képtár csatolása** lapon jelölje ki a megosztott képgalériát, és kattintson az **OK gombra.**

    ![Meglévő gyűjtemény kiválasztása](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. A következő képernyő jelenik meg: 

    ![Saját galéria a listában](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Ebben a példában még nincsenek képek a megosztott képgalériában.

    Az Azure Lab Services identitása hozzáadódik a laborhoz csatolt megosztott képtár közreműködőjeként. Lehetővé teszi a tanárok / ADMIN menteni a virtuális gép iképeit a megosztott képgaléria. A laborfiókban létrehozott összes tesztkörnyezet hozzáférhet a csatolt megosztott képgalériához. 

    A csatolt megosztott képtárban lévő összes kép alapértelmezés szerint engedélyezve van. A kijelölt képeket engedélyezheti vagy letilthatja, ha kijelöli őket a listában, és a **Kijelölt képek engedélyezése** vagy a Kijelölt képek **letiltása** gombbal használja őket. 

## <a name="detach-a-shared-image-gallery"></a>Megosztott képtár leválasztása
Csak egy megosztott képgaléria csatolható egy tesztkörnyezethez. Ha egy másik megosztott képgalériát szeretne csatolni, az új csatolása előtt válassza le az aktuálisat. Ha le szeretne választani egy megosztott képgalériát a tesztkörnyezetről, válassza a **Leválasztás** lehetőséget az eszköztáron, és erősítse meg a leválási műveletet. 

![A megosztott képtár leválasztása a laborfiókról](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>További lépések
Ha többet szeretne tudni arról, hogy miként mentheti a tesztkörnyezet képét a megosztott képtárba, vagy hogyan hozhat létre egy képet a megosztott képgalériából, olvassa el a Megosztott képgaléria használata című [témakört.](how-to-use-shared-image-gallery.md)

A megosztott képgalériákról általában a [Megosztott képgalériában](../../virtual-machines/windows/shared-image-galleries.md)talál további információt.
