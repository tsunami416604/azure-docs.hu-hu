---
title: Virtuális gépek leállításának engedélyezése a kapcsolat bontásakor Azure Lab Services | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja le a virtuális gépek automatikus leállítását, ha a távoli asztali kapcsolat le van választva.
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117127"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor
Ebből a cikkből megtudhatja, hogyan engedélyezheti vagy letilthatja a **Windows 10** laboros virtuális gépek (sablon vagy tanuló) automatikus leállítását egy távoli asztali kapcsolat leválasztása után. Azt is megadhatja, hogy a virtuális gépek mennyi ideig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt.

A labor-fiók rendszergazdája konfigurálhatja ezt a beállítást azon labor-fiók esetében, amelyben a Labs-t létrehozta. További információ: a [virtuális gépek automatikus leállításának beállítása a kapcsolat bontásakor egy Lab-fiók esetében](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). A labor tulajdonosaként a tesztkörnyezet létrehozásakor vagy a labor létrehozása után felülbírálhatja a beállítást. 

## <a name="configure-when-creating-a-lab"></a>Konfigurálás labor létrehozásakor
A labor-létrehozási varázsló 3. lépés lapján engedélyezheti vagy letilthatja a funkciót, és azt is megadhatja, hogy a virtuális gép mennyi ideig várjon, amíg a felhasználó újracsatlakozik az automatikus leállítás előtt. 

![Konfigurálás a labor létrehozásakor](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után
Ezt a beállítást a **Beállítások** lapon konfigurálhatja, ahogy az az alábbi képen is látható: 

![Konfigurálás a tesztkörnyezet létrehozása után](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Ha a Windows operációs rendszert (OS) leállítja egy virtuális gépre, mielőtt leválaszt egy RDP-munkamenetet a virtuális géphez, az automatikus leállítás funkció nem fog megfelelően működni.  

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Az osztályterem Labs irányítópultja](use-dashboard.md)