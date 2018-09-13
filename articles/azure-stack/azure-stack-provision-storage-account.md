---
title: Tárfiókok az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure Stack tárfiókok.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: mabrigg
ms.openlocfilehash: ae6539900e201f0559d998ad2d9be24c39d42e3b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713493"
---
# <a name="storage-accounts-in-azure-stack"></a>Tárfiókok az Azure Stack szolgáltatásban
A tárfiókok tartalmazzák a Blob és a Table szolgáltatásokat és az egyedi névteret a tároló adatobjektumaihoz. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a tárfiók tulajdonosa számára érhetők el.

1. Az Azure Stack POC számítógépen jelentkezzen be `https://adminportal.local.azurestack.external` , [rendszergazda](azure-stack-connect-azure-stack.md), és kattintson a **+ erőforrás létrehozása** > **adatok + tárolás**  >  **Tárfiók**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. Az a **storage-fiók létrehozása** panelen írja be a tárfiók nevét. Hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévőt, majd kattintson a **létrehozás** a tárfiók létrehozásához.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Az új tárfiók megtekintéséhez kattintson **összes erőforrás**, majd keresse meg a storage-fiókot, és kattintson a nevére.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>További lépések
[Használjon Azure Resource Manager-sablonokat](user/azure-stack-arm-templates.md)

[További tudnivalók az Azure storage-fiókok](../storage/common/storage-create-storage-account.md)

[Töltse le az Azure Stack az Azure-konzisztens tároló-ellenőrzési útmutatóját](http://aka.ms/azurestacktp1doc)
