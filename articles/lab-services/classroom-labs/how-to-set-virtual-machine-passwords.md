---
title: Virtuális gépek jelszavának beállítása Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be és állíthatja alaphelyzetbe a virtuális gépek (VM) jelszavát a Azure Lab Services osztályterem Labs szolgáltatásban.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645014"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>A virtuális gépek jelszavának beállítása vagy alaphelyzetbe állítása a tanterem Labs-ben (oktató)
A labor tulajdonosa (tanár) beállíthatja/alaphelyzetbe állíthatja a virtuális gépek jelszavát a labor létrehozásakor (labor létrehozása varázsló) vagy a labor létrehozása után (az irányítópulton). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Jelszó beállítása a labor létrehozásakor
A labor tulajdonosa (tanár) a labor létrehozása varázsló **hitelesítő adatok beállítása** lapján állíthatja be a virtuális gépek jelszavát a laborban.

![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)

Ha engedélyezi vagy letiltja az **azonos jelszó használata az összes virtuális gép számára** beállítást ezen az oldalon, a tanár dönthet úgy, hogy ugyanazt a jelszót használja a tesztkörnyezetben lévő összes virtuális géphez, vagy lehetővé teszi a tanulók számára a virtuális gépek jelszavának beállítását. Alapértelmezés szerint ez a beállítás az Ubuntu kivételével minden Windows-és Linux operációsrendszer-lemezkép esetében engedélyezve van. Ha ez a beállítás le van tiltva, a diákoknak meg kell adnia egy jelszót, amikor első alkalommal próbálnak csatlakozni a virtuális géphez. 

A tesztkörnyezet tulajdonosa visszaállíthatja ezt a jelszót (ha szükséges) a labor létrehozási varázsló **sablon konfigurálása** lapján. 

![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

A labor tulajdonosa a tesztkörnyezet létrehozása után is alaphelyzetbe állíthatja a jelszót az irányítópulton. 

## <a name="reset-password-on-the-dashboard"></a>Jelszó alaphelyzetbe állítása az irányítópulton

1. Válassza ki a túlfolyó menüt (függőleges három pont) a labor csempén, és válassza a **jelszó**alaphelyzetbe állítása lehetőséget. 

    ![Jelszó alaphelyzetbe állítása menü a kezdőlapon](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. A **jelszó beállítása** párbeszédpanelen adja meg a jelszót, majd válassza a **jelszó beállítása**lehetőséget.
    
    ![Jelszó beállítása párbeszédpanel](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>További lépések
Ha szeretne többet megtudni a tanulók által konfigurálható egyéb használati lehetőségekről, tekintse meg a következő cikket: A [tanulói használat konfigurálása](how-to-configure-student-usage.md).

A virtuális gépek jelszavának alaphelyzetbe állításáról a következő témakörben olvashat: [Virtual Machines-jelszó beállítása vagy alaphelyzetbe állítása a tanterem Labs-ban (diákok)](how-to-set-virtual-machine-passwords-student.md).