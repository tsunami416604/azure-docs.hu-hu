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
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 8e0009bf0fc34d3e0d22755d93d941b85db62ffd
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334465"
---
# <a name="validate-software-updates-from-microsoft"></a>A Microsoft szoftverfrissítések ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeresen frissítéseket az Azure Stack szoftver. Ezek a frissítések állnak rendelkezésre az Azure Stack közös mérnöki partnerek előtt kerül sor nyilvánosan elérhető, így azok elleni megoldásaikat a frissítések ellenőrzése és visszajelzés küldése a Microsoftnak.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="apply-monthly-update"></a>Havi frissítés alkalmazása

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-workflow"></a>Munkafolyamat létrehozása

Frissítés-ellenőrzések használja ugyanabban a munkafolyamatban, **csomag**. Kövesse az utasításokat, [csomag érvényesítése munkafolyamat létrehozása](azure-stack-vaas-validate-oem-package.md#create-a-package-validation-workflow).

## <a name="run-tests"></a>Tesztek futtatása

Frissítés-ellenőrzések használja ugyanabban a munkafolyamatban, **csomag**. Kövesse az utasításokat, [hajtsa végre a csomag Érvényesítési tesztek](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests).

Nem kell igényelnie a csomag aláírása a frissítés-ellenőrzések.

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)
