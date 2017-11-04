---
title: "Jelentkezzen be a klasszikus Azure virtuális gépek |} Microsoft Docs"
description: "Az Azure portál használatával jelentkezzen be a klasszikus üzembe helyezési modellel létrehozott Windows virtuális gépek."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 43d54de7e875de9212c23c49ad0539bf2272a312
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Bejelentkezés windowsos virtuális gépre az Azure Portal használatával
Az Azure portálon, használja a **Connect** gombra kattintva indítsa el a távoli asztali munkamenet, és jelentkezzen be a Windows virtuális gépek.

Végrehajtja a Linux virtuális gépek csatlakozni? Lásd: [Linuxot futtató virtuális gép bejelentkezés](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Jelentkezzen be a virtuális gépek erőforrás-kezelő modellel kapcsolatos további információkért lásd: [Itt](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Jelentkezzen be az Azure portálra.
2. Kattintson a virtuális gépen, amelyet el szeretne érni. A szerepel-e a **összes erőforrás** ablaktáblán.

    ![Virtuális-machine-helyek](./media/connect-logon/azureportaldashboard.png)

3. Kattintson a **Connect** elveire a virtuális gép irányítópult a parancssávon.

    ![Csatlakozás a virtuális gép ikonja](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Következő lépések
* Ha a **Connect** gomb inaktív vagy egyéb problémák a távoli asztali kapcsolattal, vagy próbálja meg visszaállítani a konfiguráció. Kattintson a **távelérés alaphelyzetbe állítása** a virtuális gép irányítópulton.

    ![Távelérés alaphelyzetbe állítása](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* A jelszó kapcsolatos problémák próbálkozzon a visszaállításával. Kattintson a **jelszó-átállítási** mentén a bal oldali széle virtuális gép irányítópult, a **támogatási + hibaelhárítás**.

    ![Jelszó alaphelyzetbe állítása](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Ha ezek a tippek nem működnek, vagy nem kapcsolódnak, mire van szüksége, tekintse meg [távoli asztali kapcsolatainak hibaelhárítása a Windows-alapú Azure virtuális gép](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a cikk útmutatást nyújt a gyakori problémák diagnosztizálásához és elhárításához.
