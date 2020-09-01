---
title: Azure rendszerindítási diagnosztika
description: Az Azure rendszerindítási diagnosztika és a felügyelt rendszerindítási diagnosztika áttekintése
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d425953b278a98af35a172d8777ab758db52709e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257755"
---
# <a name="azure-boot-diagnostics"></a>Azure rendszerindítási diagnosztika

A rendszerindítási diagnosztika az Azure Virtual Machines (VM) hibakeresési funkciója, amely lehetővé teszi a virtuális gépek rendszerindítási hibáinak diagnosztizálását. A rendszerindítási diagnosztika lehetővé teszi a felhasználók számára, hogy betartsák a virtuális gép állapotát, mivel a soros napló információi és képernyőképek összegyűjtésével indulnak.

## <a name="boot-diagnostics-storage-account"></a>Rendszerindítási diagnosztika Storage-fiókja
Amikor Azure Portalban hoz létre virtuális gépet, a rendszerindítási diagnosztika alapértelmezés szerint engedélyezve van. Az ajánlott rendszerindítási diagnosztika a felügyelt Storage-fiók használata, mivel az Azure-beli virtuális gép létrehozásához szükséges idő jelentős teljesítménybeli javítást eredményez. Ennek az az oka, hogy egy Azure által felügyelt Storage-fiókot kell használni, és el kell távolítani a rendszerindítási diagnosztikai adatait tároló új felhasználói Storage-fiók létrehozásához szükséges időt.

Egy alternatív rendszerindítási diagnosztikai megoldás a felhasználó által felügyelt Storage-fiók használata. Egy felhasználó létrehozhat egy új Storage-fiókot, vagy használhat egy meglévőt is.

> [!IMPORTANT]
> Az Azure-ügyfelek nem számítanak fel díjat a felügyelt Storage-fiókkal a 2020-es számú rendszerindítási diagnosztikát használó associted.

## <a name="boot-diagnostics-view"></a>Rendszerindítási diagnosztika nézet
A virtuális gép paneljén a rendszerindítási diagnosztika lehetőség a Azure Portal *támogatási és hibaelhárítási* szakaszában található. A rendszerindítási diagnosztika lehetőségre kattintva megjelenik a képernyőkép és a soros napló adatai. A soros napló kernel-üzenetkezelést tartalmaz, és a képernyőkép a virtuális gépek aktuális állapotának pillanatképe. Attól függően, hogy a virtuális gép Windows vagy Linux rendszert futtat-e, meghatározza, hogy a várt képernyőkép hogyan fog kinézni. A Windowsban a felhasználók asztali hátteret és Linux rendszerű felhasználókat látnak, és a felhasználók egy bejelentkezési kérést fognak látni.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="A Linux rendszerindítási diagnosztika képernyőképe":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="A Windows rendszerindítási diagnosztika képernyőképe":::


## <a name="limitations"></a>Korlátozások
- A rendszerindítási diagnosztika csak Azure Resource Manager virtuális gépek esetében érhető el. 
- A rendszerindítási diagnosztika nem támogatja a Premium Storage-fiókokat, ha prémium szintű Storage-fiókot használ a rendszerindítási diagnosztikai felhasználóknak a `StorageAccountTypeNotSupported` virtuális gép indításakor hibaüzenet jelenik meg. 
- Az Azure soros konzol jelenleg nem kompatibilis a rendszerindítási diagnosztika felügyelt tárolási fiókjával. További információ az [Azure soros konzolról](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview).

## <a name="next-steps"></a>További lépések

További információ az [Azure soros konzolról](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) , valamint a [virtuális gépek Azure-ban való hibakeresésének](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)rendszerindítási diagnosztika használatával történő használatáról.
