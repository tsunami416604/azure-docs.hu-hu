---
title: Virtuális gépek leállításának engedélyezése a kapcsolat bontásakor Azure Lab Services | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja le a virtuális gépek automatikus leállítását, ha a távoli asztali kapcsolat le van választva.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445712"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor
Ebből a cikkből megtudhatja, hogyan engedélyezheti vagy letilthatja a **Windows 10** laboros virtuális gépek (sablon vagy tanuló) automatikus leállítását egy távoli asztali kapcsolat leválasztása után. Azt is megadhatja, hogy a virtuális gépek mennyi ideig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt.

A labor-fiók rendszergazdája konfigurálhatja ezt a beállítást azon labor-fiók esetében, amelyben a Labs-t létrehozta. További információ: a [virtuális gépek automatikus leállításának beállítása a kapcsolat bontásakor egy Lab-fiók esetében](how-to-configure-lab-accounts.md). A labor tulajdonosaként a tesztkörnyezet létrehozásakor vagy a labor létrehozása után felülbírálhatja a beállítást. 

## <a name="configure-when-creating-a-lab"></a>Konfigurálás labor létrehozásakor
A labor-létrehozási varázsló 3. lépés lapján engedélyezheti vagy letilthatja a funkciót, és azt is megadhatja, hogy a virtuális gép mennyi ideig várjon, amíg a felhasználó újracsatlakozik az automatikus leállítás előtt. 

![Konfigurálás a labor létrehozásakor](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után
Ezt a beállítást a **Beállítások** lapon konfigurálhatja, ahogy az az alábbi képen is látható: 

![Konfigurálás a tesztkörnyezet létrehozása után](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Ha a Windows operációs rendszert (OS) leállítja egy virtuális gépre, mielőtt leválaszt egy RDP-munkamenetet a virtuális géphez, az automatikus leállítás funkció nem fog megfelelően működni.  

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Az osztályterem Labs irányítópultja](use-dashboard.md)