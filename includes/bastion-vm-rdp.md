---
title: fájlbefoglalás
description: fájlbefoglalás
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b079ede0845de3794507691bc3c252930e2a6604
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978127"
---
1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd válassza a **Kapcsolódás**lehetőséget. A legördülő listából válassza a **Bastion** lehetőséget.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Megerősített" border="false":::

1. Miután a legördülő menüből kiválasztotta a Bastion-t, megjelenik egy oldalsó sáv, amely három lapból áll: RDP, SSH és Bastion. Mivel a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, alapértelmezés szerint a megerősített lap aktív. Válassza a **bástya használata**lehetőséget.

   :::image type="content" source="./media/bastion-vm-rdp/use-bastion.png" alt-text="Megerősített" border="false":::

1. A **kapcsolat az Azure Bastion használatával** lapon adja meg a virtuális gép felhasználónevét és jelszavát, majd válassza a **kapcsolat**lehetőséget.

   :::image type="content" source="./media/bastion-vm-rdp/host.png" alt-text="Megerősített" border="false":::

1. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Megerősített" border="false":::
