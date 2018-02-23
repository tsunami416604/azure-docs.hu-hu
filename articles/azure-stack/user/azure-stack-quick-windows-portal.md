---
title: "Azure verem Quick Start - Windows virtuális gép létrehozása"
description: "Azure verem Quick Start - Windows virtuális gép létrehozása a portálon"
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/15/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: 9df976fd26318038ab4152863638e262d7f284aa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Windows rendszerű virtuális gép létrehozása a verem Azure portállal

A Windows rendszerű virtuális gép a verem Azure portál használatával hozhat létre. A portál nem egy webböngésző-alapú felhasználói felület, ahol létrehozása, konfigurálása és erőforrások kezelése.

## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be a verem Azure portálra

Jelentkezzen be a verem Azure portálra. A cím az Azure-verem portál függ, hogy melyik Azure verem termékre való kapcsolódás esetén:

* Az Azure verem Development Kit (ASDK) az Ugrás: https://portal.local.azurestack.external.
* Integrált Azure verem rendszert nyissa meg az Azure-verem operátor megadott URL-címre.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson a **új** > **számítási** > **Windows Server 2016 Datacenter próbaverzió** > **létrehozása**. Ha nem lát **Windows Server 2016 Datacenter próbaverzió** bejegyzést, lépjen kapcsolatba az Azure-verem operátor. Kérje meg, hogy azok adja hozzá a piactér leírtak szerint a [a Windows Server 2016 Virtuálisgép-lemezkép hozzáadása a verem Azure piactér](../azure-stack-add-default-image.md) cikk. 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. A **alapjai**, adjon meg egy **neve**, **felhasználónév**, és **jelszó**. Válasszon egy **előfizetés**. Hozzon létre egy **erőforráscsoport**, vagy válasszon ki egy meglévő egy, válassza ki a **hely**, és kattintson a **OK**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. A **méret kiválasztása**, kattintson a **D1 szabványos** > **válasszon**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. A **beállítások**, fogadja el az alapértelmezett beállításokat, majd kattintson **OK**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. A **összegzés**, kattintson a **OK** a virtuális gép létrehozásához. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Az új virtuális gép megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a virtuális gép, és kattintson a nevére.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor már nincs szüksége a virtuális gépet, törölje az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások. Ehhez a virtuális gép lapon jelölje ki az erőforráscsoportot, majd kattintson **törlése**.

## <a name="next-steps"></a>További lépések
A gyors egyszerű Windows rendszerű virtuális gép telepítése után. További információt a verem Azure virtuális gépek, továbbra is [szempontok a virtuális gépek Azure-készletben](azure-stack-vm-considerations.md).
