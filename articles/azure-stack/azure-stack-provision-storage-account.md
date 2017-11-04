---
title: "Storage-fiókok Azure verem |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Azure verem tárfiókot."
services: azure-stack
documentationcenter: 
author: vhorne
manager: byronr
editor: 
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/1/2017
ms.author: victorh
ms.openlocfilehash: 41c9ee37c43d4ad41c51ea2ed023d3b47d460dd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storage-accounts-in-azure-stack"></a>Tárfiókok az Azure Stack szolgáltatásban
A tárfiókok tartalmazzák a Blob és a Table szolgáltatásokat és az egyedi névteret a tároló adatobjektumaihoz. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. Az Azure verem Koncepció számítógépen jelentkezzen be `https://adminportal.local.azurestack.external` , [rendszergazda](azure-stack-connect-azure-stack.md), és kattintson a **új** > **adatok + tárolás**  >  **Tárfiók**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Az a **storage-fiók létrehozása** panelen írja be a tárfiók nevét. Hozzon létre egy új **erőforráscsoport**, vagy válasszon egy meglévőt, majd kattintson a **létrehozása** a tárfiók létrehozásához.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Az új tárfiók megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a tárfiók, és kattintson a nevére.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Következő lépések
[Használjon Azure Resource Manager-sablonokat](user/azure-stack-arm-templates.md)

[További tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)

[Töltse le az Azure verem Azure-konzisztens tárolási érvényesítési útmutatója](http://aka.ms/azurestacktp1doc)
