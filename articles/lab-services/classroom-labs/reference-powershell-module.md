---
title: PowerShell-modul a Azure Lab Serviceshoz | Microsoft Docs
description: Ez a cikk egy PowerShell-modulról nyújt információkat, amely segítséget nyújt az összetevők Azure Lab Servicesban való kezeléséhez.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609397"
---
# <a name="azlabservices-powershell-module-preview"></a>Az az. LabServices PowerShell-modul (előzetes verzió)
Az az. LabServices egy PowerShell-modul, amely leegyszerűsíti az Azure Lab Services felügyeletét. Lehetővé teszi a laboratóriumi fiókok, laborok, virtuális gépek és lemezképek létrehozását, lekérdezését, frissítését és törlését. További információ erről a modulról: az az [. LabServices Kezdőlap a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Ez a modul **előzetes**verzióban érhető el. 

## <a name="example-command"></a>Példa parancs
Az alábbi példa egy PowerShell-parancs használatával állítja le az összes futó virtuális gépet az összes laborban.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Bevezetés
1. Telepítse [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , ha az nem létezik a gépen. 
2. Töltse le az [az. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) programot a gépre.
3. Importálja a modult:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Futtassa az alábbi parancsot az előfizetésben található összes Labs listázásához.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>További lépések
Lásd az az [. LabServices kezdőlapját a githubon](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
