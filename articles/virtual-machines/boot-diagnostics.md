---
title: Azure rendszerindítási diagnosztika
description: Az Azure rendszerindítási diagnosztika és a felügyelt rendszerindítási diagnosztika áttekintése
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042960"
---
# <a name="azure-boot-diagnostics"></a>Azure rendszerindítási diagnosztika

A rendszerindítási diagnosztika az Azure Virtual Machines (VM) hibakeresési funkciója, amely lehetővé teszi a virtuális gépek rendszerindítási hibáinak diagnosztizálását. A rendszerindítási diagnosztika lehetővé teszi a felhasználók számára, hogy betartsák a virtuális gép állapotát, mivel a soros napló információi és képernyőképek összegyűjtésével indulnak.

## <a name="boot-diagnostics-storage-account"></a>Rendszerindítási diagnosztika Storage-fiókja
Alapértelmezés szerint a rendszerindítási diagnosztika engedélyezve van a Azure Portal használatával létrehozott összes virtuális gép számára, és felügyelt Storage-fiókot használ. A felügyelt Storage-fiókokkal a felhasználók jelentős mértékben javítják a virtuális gépek központi telepítésének idejét. Ezért javasoljuk, hogy az ügyfelek felügyelt Storage-fiókkal használják a rendszerindítási diagnosztikát az összes virtuális gépen.

> [!NOTE]
> Az Azure-ügyfelek nem számítanak fel díjat a tároláshoz, ha a rendszer a rendszerindítási diagnosztikát egy felügyelt Storage-fiókkal a 2020. október 1. előtt használja.

Egy alternatív rendszerindítási diagnosztikai megoldás egy egyéni Storage-fiók használata. Egy felhasználó létrehozhat egy új Storage-fiókot, vagy használhat egy meglévőt is. További információ az egyéni Storage-fiókokról: a [Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="A rendszerindítási diagnosztika engedélyezését bemutató képernyőkép":::

## <a name="boot-diagnostics-view"></a>Rendszerindítási diagnosztika nézet
A virtuális gép paneljén a rendszerindítási diagnosztika lehetőség a Azure Portal *támogatási és hibaelhárítási* szakaszában található. A rendszerindítási diagnosztika lehetőségre kattintva megjelenik a képernyőkép és a soros napló adatai. A soros napló kernel-üzenetkezelést tartalmaz, és a képernyőkép a virtuális gépek aktuális állapotának pillanatképe. Attól függően, hogy a virtuális gép Windows vagy Linux rendszert futtat-e, meghatározza, hogy a várt képernyőkép hogyan fog kinézni. A Windowsban a felhasználók asztali hátteret és Linux rendszerű felhasználókat látnak, és a felhasználók egy bejelentkezési kérést fognak látni.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="A Linux rendszerindítási diagnosztika képernyőképe":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="A Windows rendszerindítási diagnosztika képernyőképe":::


## <a name="limitations"></a>Korlátozások
- A rendszerindítási diagnosztika csak Azure Resource Manager (ARM) virtuális gépek számára érhető el. 
- A rendszerindítási diagnosztika nem támogatja a Premium Storage-fiókokat, ha prémium szintű Storage-fiókot használ a rendszerindítási diagnosztikai felhasználóknak a `StorageAccountTypeNotSupported` virtuális gép indításakor hibaüzenet jelenik meg. 
- Az Azure soros konzol jelenleg nem támogatja a felügyelt Storage-fiókot a rendszerindítási diagnosztika számára.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure soros konzolról](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) , valamint a [virtuális gépek Azure-ban való hibakeresésének](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)rendszerindítási diagnosztika használatával történő használatáról.
