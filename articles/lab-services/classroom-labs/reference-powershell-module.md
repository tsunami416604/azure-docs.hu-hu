---
title: PowerShell-modul az Azure Lab Services szolgáltatáshoz | Microsoft dokumentumok
description: Ez a cikk egy PowerShell-modul, amely segít az Azure Lab Services összetevők kezelésében.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609397"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell-modul (előzetes verzió)
Az.LabServices egy PowerShell-modul, amely leegyszerűsíti az Azure Lab-szolgáltatások felügyeletét. Kompozható függvényeket biztosít a laborfiókok, laborok, virtuális gépek és képek létrehozásához, lekérdezéséhez, frissítéséhez és törléséhez. Erről a modulról további információt az [Az.LabServices kezdőlapján talál a GitHubon.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

> [!NOTE]
> Ez a modul **előzetes verzióban**érhető el. 

## <a name="example-command"></a>Mintaparancs
Íme egy példa egy PowerShell-parancs használatával az összes futó virtuális gép leállításához az összes laborban.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Bevezetés
1. Telepítse [az Azure PowerShellt,](https://docs.microsoft.com/powershell/azure/overview) ha nem létezik a gépen. 
2. Töltse le [az Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) fájlt a gépére.
3. A modul importálása:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Futtassa a következő parancsot az előfizetésösszes laborlistájának listázásához.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>További lépések
Tekintse meg az [Az.LabServices kezdőlapját a GitHubon.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)
