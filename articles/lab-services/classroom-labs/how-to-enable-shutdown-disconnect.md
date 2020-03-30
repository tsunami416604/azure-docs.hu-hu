---
title: Virtuális gépek leállításának engedélyezése az Azure Lab Services bontásakor | Microsoft dokumentumok
description: Megtudhatja, hogy miként engedélyezheti vagy tilthatja le a virtuális gépek automatikus leállítását, ha egy távoli asztali kapcsolat megszakad.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117127"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Virtuális gépek automatikus leállításának engedélyezése a kapcsolat bontásakor
Ez a cikk bemutatja, hogyan engedélyezheti vagy tilthatja le a **Windows 10** laboros virtuális gépek (sablon vagy diák) automatikus leállítását a távoli asztali kapcsolat leválasztása után. Azt is megadhatja, hogy a virtuális gépek nek mennyi ideig kell várniuk a felhasználó újracsatlakozására, mielőtt automatikusan leállna.

A tesztkörnyezet-fiók rendszergazdája konfigurálhatja ezt a beállítást a laborfiókhoz, amelyben laborokat hoz létre. További információ: [A virtuális gépek automatikus leállításának konfigurálása kapcsolat bontása kor egy tesztkörnyezet-fiókhoz](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)című témakörben talál. A tesztkörnyezet tulajdonosaként felülbírálhatja a beállítást egy tesztkörnyezet létrehozásakor vagy a tesztkörnyezet létrehozása után. 

## <a name="configure-when-creating-a-lab"></a>Konfigurálás tesztkörnyezet létrehozásakor
A laborkészítő varázsló 3. 

![Konfigurálás a labor létrehozásakor](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után
Ezt a beállítást a **Beállítások** lapon az alábbi képen látható módon állíthatja be: 

![Konfigurálás a tesztkörnyezet létrehozása után](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Ha leállítja a Windows operációs rendszert (OS) egy virtuális gépen, mielőtt leválasztaná az RDP-munkamenetet a virtuális gépről, az automatikus leállítás i szolgáltatás nem fog megfelelően működni.  

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Irányítópult tantermi laborokhoz](use-dashboard.md)