---
title: Egy új Azure Stack megoldás ellenőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti egy új megoldást az Azure Stack érvényesítési szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 7949e764baa7a4e20eb988c78817b6b4f0045593
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333768"
---
# <a name="validate-a-new-azure-stack-solution"></a>Egy új Azure Stack megoldás ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, hogyan használható a **megoldás érvényesítési** hitelesíttetni az új Azure Stack megoldásokat a munkafolyamatot.

Egy Azure Stack megoldás, a hardver anyagjegyzék (AJ), amelyek közösen egyeztetett a Microsoft és a partnerek között a Windows Server embléma minősítési követelményeinek teljesítése után. A megoldás kell kell recertified, ha a hardver AJ változás történt. Segítségével újból hitelesítheti a megoldásokkal kapcsolatos további kérdéseikkel, forduljon a csapathoz a következő címen [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

A megoldás tanúsításához kétszer futtatni a megoldás Szűrőérvényesítési munkafolyamat. Futtassa egyszer a *minimálisan* konfiguráció támogatott. A második alkalommal futtassa a *standardként* konfiguráció támogatott. A Microsoft igazolja a megoldás, ha mindkét adja át az összes teszt.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>A megoldás Szűrőérvényesítési munkafolyamat létrehozása

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]
2. Válassza ki **Start** a a **megoldás ellenőrzések** csempére.

    ![Megoldás ellenőrzések munkafolyamat csempe](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
4. Válassza ki a **megoldáskonfiguráció**.
    - **Minimális**: a megoldás úgy van konfigurálva, a csomópontok minimális támogatott számát.
    - **Maximális**: a megoldás úgy van konfigurálva, a csomópontok támogatott maximális számát.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Megoldás érvényesítésre vonatkozó információk](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Környezeti paraméter nem lehet módosítani egy munkafolyamat létrehozása után.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    A tesztek összefoglaló oldalra irányítja.

## <a name="execute-solution-validation-tests"></a>Megoldás ellenőrző tesztek végrehajtása

Az a **megoldás Érvényesítési tesztek összefoglalása** lapon látni fogja a szükséges érvényesítési teszteket listáját.

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

![Ütemezés megoldás ellenőrző teszt](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)