---
title: A Microsoft Azure Stack érvényesítési szolgáltatásként szoftverfrissítések ellenőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti a szoftverfrissítéseket a Microsoft-szolgáltatás érvényesítéssel.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 8ea0f36f3d167109fc0dc0d91707a08cff1c6a17
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243984"
---
# <a name="validate-software-updates-from-microsoft"></a>A Microsoft szoftverfrissítések ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeresen frissítéseket az Azure Stack szoftver. Ezek a frissítések állnak rendelkezésre az Azure Stack coengineering partnerek. A nyilvánosan elérhető előzetes frissítésekkel. A frissítések keresése a megoldás szemben, és visszajelzést a Microsoftnak.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Havi frissítés alkalmazása

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Munkafolyamat létrehozása

Frissítés-ellenőrzések használja ugyanabban a munkafolyamatban, **megoldás érvényesítési**.

## <a name="run-tests"></a>Tesztek futtatása

1. Frissítés-ellenőrzések használja ugyanabban a munkafolyamatban, **megoldás érvényesítési**. 

2. Kövesse az utasításokat, [futtatása megoldás Érvényesítési tesztek](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Ehelyett válassza ki a következő ellenőrzés:
    - Az Azure Stack havi frissítés-ellenőrzés
    - Felhőbeli szimuláció motor

Nem kell igényelnie a csomag aláírása a frissítés-ellenőrzések.

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)