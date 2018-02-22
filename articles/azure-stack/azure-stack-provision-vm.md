---
title: "A teszteléshez használt virtuális gép létrehozása Azure-készletben |} Microsoft Docs"
description: "Útmutató a teszteléshez használt virtuális gép Azure verem felhő operátorként kiépítéséhez."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 41096f68e5e7d9e31098d1e8919101418abe4c03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>A teszt virtuális gép létrehozása Azure-verem

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

Azure verem kezelőként létrehozhat a tesztcélú virtuális gépek ellenőrzése a [Azure verem](azure-stack-poc.md) szoftverfejlesztői készlet központi telepítés.

> [!NOTE]
> Virtuális gépek oszthat, előtt [adja hozzá a Windows Server 2016 értékelése a verem Azure piactér](azure-stack-add-default-image.md).
> 
> 

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
1. Az Azure verem szoftverfejlesztői készlet állomáson [bejelentkezés](azure-stack-connect-azure-stack.md) a felügyeleti portálra (`https://adminportal.local.azurestack.external`), és kattintson a **új** > **számítási**  >  **Windows Server 2016 Datacenter próbaverzió** > **létrehozása**.  
2. Az a **alapjai** panelen adjon meg egy **neve**, **felhasználónév**, és **jelszó**. Válasszon egy **előfizetés**. Hozzon létre egy **erőforráscsoport**, vagy válasszon egy meglévőt, és kattintson **OK**.  
3. Az a **méret kiválasztása** panelen kattintson **A1 szabványos**, és kattintson a **kiválasztása**.  
4. Az a **beállítások** panelen fogadja el az alapértelmezett beállításokat, majd kattintson **OK**
5. Az **Összegzés** panelen kattintson az **OK** gombra a virtuális gép létrehozásához.  
6. Az új virtuális gép megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a virtuális gép, és kattintson a nevére.
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>További lépések
[A rendszergazda és a felhasználói portálon Azure verem használata](azure-stack-manage-portals.md)
