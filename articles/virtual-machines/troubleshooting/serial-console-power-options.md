---
title: Az Azure Soros konzol energiagazdálkodási lehetőségei | Microsoft dokumentumok
description: Az Azure soros konzolon elérhető virtuális gép energiagazdálkodási lehetőségei
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451198"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Az Azure soros konzolon elérhető energiagazdálkodási lehetőségek

Az Azure Soros konzol számos hatékony eszközt biztosít az energiagazdálkodáshoz a virtuális gép vagy a virtuális gép méretezési csoportban. Ezek az energiagazdálkodási lehetőségek zavaróak lehetnek néhány, így ez egy áttekintést az egyes eszközök és a tervezett használati eset.

Soros konzol szolgáltatás | Leírás | Kis- és nagybetű használata
:----------------------|:------------|:---------
Virtuális gép újraindítása | A virtuális gép vagy a virtuális gép méretezési példányának kecses újraindítása. Ez a művelet megegyezik az Áttekintés lapon elérhető újraindítási szolgáltatás hívása. | A legtöbb esetben ez a beállítás legyen az első eszköz a virtuális gép újraindítása. A soros konzol kapcsolat a szolgáltatás egy rövid megszakítás, és automatikusan folytatódik, amint a virtuális gép újraindítása.
Virtuális gép alaphelyzetbe állítása | Az Azure platform által beállított virtuális gép vagy virtuálisgép-méretezés erőteljes energiaciklusa. | Ezzel a beállítással azonnal újraindíthatja az operációs rendszert, függetlenül annak jelenlegi állapotától. Mivel ez a művelet nem kecses, adatvesztés vagy sérülés veszélye áll fenn. Nincs megszakítás a soros konzol kapcsolatában, ami hasznos lehet a parancsok korai indításkori küldéséhez (például a GRUB-hoz linuxos virtuális gépen vagy csökkentett módban egy Windows virtuális gépen).
SysRq - Újraindítás (b) | A vendég újraindításának kényszerítésére irányuló rendszerkérés. | Ez a funkció csak Linux operációs rendszerekre alkalmazható, és a [SysRq-nak engedélyeznie kell](./serial-console-nmi-sysrq.md#system-request-sysrq) az operációs rendszerben. Ha az operációs rendszer megfelelően van konfigurálva a SysRq-hoz, ez a parancs az operációs rendszer újraindítását okozza.
NMI (nem maszkolható megszakítás) | Megszakításparancs, amely et az operációs rendszerbe szállítják | Ez a művelet [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) és [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) virtuális gépeken is elérhető, és engedélyezni kell az NMI-t. Az NMI küldése általában az operációs rendszer összeomlását okozza. Beállíthatja, hogy az operációs rendszer memóriaképfájlt hozzon létre, majd az NMI fogadása után újrainduljon, ami hasznos lehet az alacsony szintű hibakeresésnél.

## <a name="next-steps"></a>További lépések
* További információ az [Azure Serial Console for Linux virtuális gépekről](./serial-console-linux.md)
* További információ az [Azure Serial Console for Windows virtuális gépekről](./serial-console-windows.md)