---
title: Jelszavak beállítása az Azure Lab Services-beli virtuális gépek |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be, és állítsa vissza a virtuális gépek (VM) az Azure Lab Services osztályterem-tesztkörnyezetek a jelszavát.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144098"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Állítsa be vagy osztályterem-tesztkörnyezetek a virtuális gépek számára a jelszó alaphelyzetbe állítása
Ez a cikk beállítás és resettings jelszavak különböző módszert is biztosít az osztályterem-tesztkörnyezetek elérése a virtuális gépek. 

## <a name="lab-owners-teachers"></a>Labortulajdonosok (tanárok)
A lab tulajdonosa (oktatói) is set/jelszavának alaphelyzetbe állítása a virtuális gépek időben a tesztkörnyezet (tesztkörnyezet létrehozása varázsló) létrehozása, vagy (az irányítópulton) a tesztkörnyezet létrehozása után. 

### <a name="set-password-at-the-time-of-lab-creation"></a>A labor létrehozása idején jelszó beállítása
Egy tesztlabor tulajdonosa (oktatói) állíthatják be egy jelszót a virtuális gépek a lab-ben a **hitelesítő adatok beállítása a** a labor létrehozása varázsló.

![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)

Által engedélyezése vagy letiltása a **használja ugyanazt a jelszót az összes virtuális gép** használja ugyanazt a jelszót az összes virtuális gép a lab-ben, vagy engedélyezze a tanulóknak, hogy a jelszavak beállítása a virtuális gépek ezen a lapon egy oktató lehetőséget választhat. Ez a beállítás alapértelmezés szerint engedélyezve van a minden Windows és Linux operációs rendszerek lemezképeinek Ubuntu kivételével. Ha ez a beállítás le van tiltva, diákoknak jelszót állíthat be, amikor megpróbálnak csatlakozni a virtuális gép első alkalommal kéri. 

A lab-tulajdonos alaphelyzetbe állíthatja a jelszót (ha szükséges) a a **konfigurálása sablon** a labor létrehozása varázsló. 

![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

A lab tulajdonos is alaphelyzetbe állíthatja a jelszót az irányítópulton a labor létrehozása után. 

### <a name="reset-password-on-the-dashboard"></a>Jelszó alaphelyzetbe állítása az irányítópulton

1. Válassza ki a labor csempére a túlcsordulás menü (függőleges három pontra), és válassza ki **jelszó alaphelyzetbe állítása**. 

    ![A kezdőlapon jelszó menü alaphelyzetbe állítása](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Az a **jelszó beállítása** párbeszédpanelen adjon meg egy jelszót, és válassza ki **jelszó beállítása**.
    
    ![Set-jelszó párbeszédpanel](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Tesztlabor-felhasználók (hallgatóknak)
A tesztkörnyezet létrehozása idején már a labor tulajdonos engedélyezheti vagy letilthatja a **használja ugyanazt a jelszót az összes virtuális gép**. Ha ez a beállítás engedélyezve van, a tanulók nem állítható vissza a jelszót. A labs szolgáltatásban létrehozott virtuális gépek ugyanazt a jelszót, amely szerint az oktatói fog rendelkezni. 

Ez a beállítás le van tiltva, ha a felhasználók jelszót állíthat be, amikor megpróbál csatlakozni a virtuális gép első alkalommal lesz. Amikor a felhasználók (hallgatóknak) válassza ki a **Connect** gombot a labor csempére a **a virtual machines** lapon a felhasználónál a következő párbeszédpanelen állítsa be a jelszót a virtuális gép számára: 

![Jelszó alaphelyzetbe állítása a diákoknak](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Tanulói is állíthatja a jelszót a túlcsordulás menü gombra kattintva (**három függőleges pontot alkotó ikon**) a labor csempére, majd válasszon **jelszó alaphelyzetbe állítása**. 

## <a name="next-steps"></a>További lépések
Ismerje meg más tanulói használati lehetőségeit, (labortulajdonosként) is konfigurálja, a következő cikkben talál: [Konfigurálja a tanulói használati](how-to-configure-student-usage.md).
