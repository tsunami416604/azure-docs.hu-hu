---
title: Azure verem Quick Start - Windows virtuális gép létrehozása
description: Azure verem Quick Start - Windows virtuális gép létrehozása a portálon
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Gyors üzembe helyezés: Windows server virtuális gép létrehozása a verem Azure portállal

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A Windows Server 2016-os virtuális gép a verem Azure portál használatával hozhat létre. Kövesse a cikkben történő létrehozásáról és használatáról a virtuális gép.

## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be a verem Azure portálra

Jelentkezzen be a verem Azure portálra. A cím az Azure-verem portál függ, hogy melyik Azure verem termékre, amelyhez csatlakozik:

* Az Azure verem Development Kit (ASDK) az Ugrás: https://portal.local.azurestack.external.
* Integrált Azure verem rendszert nyissa meg az Azure-verem operátor megadott URL-címre.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson a **új** > **számítási** > **Windows Server 2016 Datacenter próbaverzió** > **létrehozása**. Ha nem lát **Windows Server 2016 Datacenter próbaverzió** bejegyzést, lépjen kapcsolatba az Azure-verem operátor. Kérje meg, hogy azok adja hozzá a piactér leírtak szerint a [a Windows Server 2016 Virtuálisgép-lemezkép hozzáadása a verem Azure piactér](../azure-stack-add-default-image.md) cikk.

    ![A portál egy Windows rendszerű virtuális gép létrehozásának lépései](media/azure-stack-quick-windows-portal/image01.png)
2. A **alapjai**, adjon meg egy **neve**, **felhasználónév**, és **jelszó**. Válasszon egy **előfizetés**. Hozzon létre egy **erőforráscsoport**, vagy válasszon ki egy meglévő egy, válassza ki a **hely**, és kattintson a **OK**.

    ![Az alapvető beállítások konfigurálása](media/azure-stack-quick-windows-portal/image02.png)
3. A **méret kiválasztása**, kattintson a **D1 szabványos** > **válasszon**.
    ![Válassza ki a virtuális gép mérete](media/azure-stack-quick-windows-portal/image03.png)
4. A **beállítások**, fogadja el az alapértelmezett beállításokat, majd kattintson **OK**.
    ![Virtuális gép beállításainak konfigurálása](media/azure-stack-quick-windows-portal/image04.png)
5. A **összegzés**, kattintson a **OK** a virtuális gép létrehozásához.
    ![Összefoglalás megtekintése és a virtuális gép létrehozása](media/azure-stack-quick-windows-portal/image05.png)
6. Az új virtuális gép megtekintéséhez kattintson **összes erőforrás**, keresse meg a virtuális gép nevét, és kattintson a nevére, a keresési eredmények között.
    ![Tekintse meg a virtuális gép](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte, használja a virtuális gépet, törölje a virtuális gép és az erőforrások. Ehhez jelölje ki az erőforráscsoportot, a virtuális gép lapon, majd kattintson **törlése**.

## <a name="next-steps"></a>További lépések

A gyors üzembe helyezési telepítette egy alapvető Windows Server virtuális gép. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
