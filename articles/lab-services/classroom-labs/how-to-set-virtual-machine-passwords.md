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
ms.openlocfilehash: 3701c69ff97d840f6cba175df8f02bc55ece498b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123239"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Állítsa be vagy osztályterem-tesztkörnyezetek a virtuális gépek számára a jelszó alaphelyzetbe állítása
Ez a cikk beállítás és resettings jelszavak különböző módszert is biztosít az osztályterem-tesztkörnyezetek elérése a virtuális gépek. 

## <a name="lab-owners-teachers"></a>Labortulajdonosok (tanárok)
Egy tesztlabor tulajdonosa (oktatói) állíthatják be egy jelszót a virtuális gépek a lab-ben a **hitelesítő adatok beállítása a** a labor létrehozása varázsló.

![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)

Ezután a tesztkörnyezet tulajdonos alaphelyzetbe állíthatja a jelszót (ha szükséges) az a **konfigurálása sablon** a labor létrehozása varázsló. 

![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

A lab tulajdonos is alaphelyzetbe állíthatja a jelszót az irányítópulton a labor létrehozása után. 

![A kezdőlapon jelszó menü alaphelyzetbe állítása](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)

Ezután adja meg az új jelszót a **jelszó beállítása** lapon, és válassza ki **jelszó beállítása**. 

![A kezdőlapon jelszó menü alaphelyzetbe állítása](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Tesztlabor-felhasználók (hallgatóknak)
A labor létrehozásának időpontjában. Egy időben a labor tulajdonos engedélyezheti vagy letilthatja a lehetővé tevő tanulóknak, hogy állítsa be a saját jelszavukat a virtuális gépek számára. A lab-tulajdonos (oktatói) lehetővé teszi, hogy ezt a beállítást, ha a lab-felhasználó (student) lesz lehetőség a jelszó beállítása a virtuális gép számára, amikor a tanulói jelentkezik be a virtuális gép első alkalommal. Az egy **Windows virtuális gép**, nyomja le az **CTRL + ALT + END**, és válassza ki **jelszó módosítása**. 

## <a name="next-steps"></a>További lépések
Ismerje meg más tanulói használati lehetőségeit, (labortulajdonosként) is konfigurálja, a következő cikkben talál: [Konfigurálja a tanulói használati](how-to-configure-student-usage.md).
