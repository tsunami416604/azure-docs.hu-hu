---
title: Az Azure Lab Services tantermi tesztkörnyezetbeli virtuális gépeijelszavainak alaphelyzetbe állítása | Microsoft dokumentumok
description: Megtudhatja, hogyan állíthatja alaphelyzetbe a virtuális gépek (VM-ek) jelszavait az Azure Lab Services tantermi laborjaiban.
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
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583694"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Virtuális gépek jelszavának beállítása vagy alaphelyzetbe állítása az osztálytermi laborokban (diákok)
Ez a cikk bemutatja, hogyan állíthatják be/állíthatják be a diákok a virtuális gépek jelszavát. 

## <a name="enable-resetting-of-passwords"></a>Jelszavak alaphelyzetbe állításának engedélyezése
A labor létrehozásakor a tesztgazda engedélyezheti vagy letilthatja **az Azonos jelszó használata az összes virtuális géphez.** Ha ez a beállítás engedélyezve van, a diákok nem állíthatnak alaphelyzetbe a jelszót. A laborokban lévő összes virtuális gép ugyanazt a jelszót fogja beállítani, mint az oktató. 

Ha ez a beállítás le van tiltva, a felhasználóknak be kell állítaniuk egy jelszót, amikor először próbálnak csatlakozni a virtuális géphez. A diákok később is visszaállíthatják a jelszót a cikk utolsó részében látható módon. 

## <a name="reset-password-for-the-first-time"></a>Jelszó alaphelyzetbe állítása az első alkalommal
Ha **az Azonos jelszó használata az összes virtuális géphez** beállítás le van tiltva, amikor a felhasználók (diákok) a **Virtuális gépek** lap laborcsempéjének **Csatlakozás** gombját választják, a felhasználó a következő párbeszédpanelen állíthatja be a virtuális gép jelszavát: 

![A tanuló jelszavának alaphelyzetbe állítása](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Jelszó visszaállítása később
A tanuló úgy is beállíthatja a jelszót, hogy a laborcsempén a túlcsordulás menüre **(függőleges három pont)** kattint, majd a **Jelszó alaphelyzetbe állítása parancsra**kattint. 

![Jelszó visszaállítása később](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>További lépések
A tanulói használati lehetőségekről, amelyeket a labortulajdonos konfigurálhat, a következő cikkben olvashat: [A diákok használatának konfigurálása](how-to-configure-student-usage.md).
