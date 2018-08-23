---
title: Azure Stack gyors üzembe helyezés – Windows virtuális gép létrehozása
description: Azure Stack gyors üzembe helyezés – Windows virtuális gép létrehozása a portálon
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 08/15/2018
ms.author: mabrigg
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: efe6213e5c0261fb26ac40e74c2b0f6e0c9252dd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139449"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Gyors útmutató: egy Windows server virtuális gép létrehozása az Azure Stack portálon

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Létrehozhat egy Windows Server 2016 virtuális gép az Azure Stack-portál használatával. Kövesse a cikkben hozhat létre és használhat egy virtuális gépet.

## <a name="sign-in-to-the-azure-stack-portal"></a>Jelentkezzen be az Azure Stack portálon

Jelentkezzen be az Azure Stack portálon. Az Azure Stack portálon címe attól függ, mely az Azure Stack-termék, amelyhez csatlakozik:

* Az Azure Stack Development Kit (ASDK) az Ugrás: https://portal.local.azurestack.external.
* Az Azure Stackkel integrált rendszer nyissa meg az Azure Stack-operátorokról megadott URL-CÍMÉT.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson a **új** > **számítási** > **Windows Server 2016 Datacenter próbaverzió** > **létrehozása**. Ha nem lát **Windows Server 2016 Datacenter próbaverzió** bejegyzést, lépjen kapcsolatba az Azure Stack-operátorokról. Kérje meg, hogy azok adja hozzá a Marketplace-en leírtak a [a Windows Server 2016 virtuális gép lemezképének hozzáadása az Azure Stack piactéren](../azure-stack-add-default-image.md) cikk.

    ![A portál egy Windows virtuális gép létrehozásának lépései](media/azure-stack-quick-windows-portal/image01.png)
2. A **alapjai**, adjon meg egy **neve**, **felhasználónév**, és **jelszó**. Válasszon egy **előfizetés**. Hozzon létre egy **erőforráscsoport**, vagy válasszon ki egy meglévő egy, válassza ki a **hely**, és kattintson a **OK**.

    ![Az alapvető beállítások konfigurálása](media/azure-stack-quick-windows-portal/image02.png)
3. A **méret kiválasztása**, kattintson a **D1 Standard** > **kiválasztása**.
    ![Virtuális gép méretének kiválasztása](media/azure-stack-quick-windows-portal/image03.png)
4. A **beállítások**, fogadja el az alapértelmezett beállításokat, és kattintson a **OK**.
    ![A virtuális gép beállításainak konfigurálása](media/azure-stack-quick-windows-portal/image04.png)
5. A **összefoglalás**, kattintson a **OK** a virtuális gép létrehozásához.
    ![Összefoglalás megtekintése és a virtuális gép létrehozása](media/azure-stack-quick-windows-portal/image05.png)
6. Tekintse meg az új virtuális gépet, kattintson a **összes erőforrás**, és keresse meg a virtuális gép nevét, majd kattintson a nevére, a keresési eredmények között.
    ![Tekintse meg a virtuális gép](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, a virtuális gép használatával, törölje a virtuális gép és az erőforrások. Ehhez válassza ki az erőforráscsoportot a virtuális gép oldalon, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban üzembe helyezett egy alapszintű Windows Server virtuális gépet. További információ a virtuális gépekről az Azure Stack, továbbra is [szempontok a virtuális gépek az Azure Stackben](azure-stack-vm-considerations.md).
