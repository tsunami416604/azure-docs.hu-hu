---
title: Az Azure soros konzol energiagazdálkodási lehetőségei | Microsoft Docs
description: Az Azure soros konzolon elérhető virtuális gépek energiagazdálkodási lehetőségei
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451198"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Az Azure soros konzolon elérhető energiagazdálkodási lehetőségek

Az Azure soros konzol számos hatékony eszközt biztosít az energiagazdálkodáshoz a virtuális gépen vagy a virtuálisgép-méretezési csoporton. Ezek az energiagazdálkodási lehetőségek bizonyos esetekben zavaróak lehetnek, így ez az egyes eszközök és a kívánt használati eset áttekintése.

Soros konzol funkció | Leírás | Használati eset
:----------------------|:------------|:---------
Virtuális gép újraindítása | A virtuális gép vagy virtuálisgép-méretezési csoport példányának biztonságos újraindítása. Ez a művelet megegyezik az Áttekintés lapon elérhető újraindítási funkció meghívásával. | A legtöbb esetben ez a lehetőség az első eszköz a virtuális gép újraindítására tett kísérlet során. A soros konzol kapcsolata rövid megszakítást fog tapasztalni, és a virtuális gép újraindítása után automatikusan folytatódik.
Virtuális gép alaphelyzetbe állítása | A virtuális gép vagy a virtuálisgép-méretezési csoport erőteljes energiagazdálkodási ciklusa az Azure platformon. | Ezzel a beállítással azonnal újraindíthatja az operációs rendszert az aktuális állapottól függetlenül. Mivel ez a művelet nem biztonságos, fennáll az adatvesztés vagy a sérülés kockázata. A soros konzol kapcsolata nem szakítható meg, ami hasznos lehet a parancsok rendszerindítási idő előtti elküldéséhez (például Linux rendszerű virtuális gépen vagy csökkentett módban a GRUB-ra egy Windows virtuális gépen).
SysRq – újraindítás (b) | A vendég újraindítását kényszerítő rendszerkérelem. | Ez a funkció csak a Linux operációs rendszerekre alkalmazható, és az operációs rendszerben [engedélyezni kell a SYSRQ](./serial-console-nmi-sysrq.md#system-request-sysrq) . Ha az operációs rendszer megfelelően van konfigurálva a SysRq, ez a parancs az operációs rendszer újraindítását fogja eredményezni.
NMI (nem maszkolt megszakítás) | Egy megszakítási parancs, amely az operációs rendszerbe lesz továbbítva | Ez a művelet [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) -és [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) -alapú virtuális gépekhez is elérhető, és a NMI engedélyezését igényli. A NMI elküldése általában az operációs rendszer összeomlását okozza. Beállíthatja, hogy az operációs rendszer hozzon létre egy memóriaképfájl-fájlt, majd indítsa újra a NMI fogadása után, ami hasznos lehet az alacsony szintű hibakeresésben.

## <a name="next-steps"></a>További lépések
* További információ a [Linux rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-linux.md)
* További információ a [Windows rendszerű virtuális gépekhez készült Azure soros konzolról](./serial-console-windows.md)