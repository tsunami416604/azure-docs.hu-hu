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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7fa03cdf52fc3218e9556c9664daafdc60243f3
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593217"
---
# <a name="validate-software-updates-from-microsoft"></a>A Microsoft szoftverfrissítések ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

A Microsoft rendszeresen frissítéseket az Azure Stack szoftver. Ezek a frissítések állnak rendelkezésre az Azure Stack coengineering partnerek. A nyilvánosan elérhető előzetes frissítésekkel. A frissítések keresése a megoldás szemben, és visszajelzést a Microsoftnak.

Szoftverfrissítéseket a Microsoft Azure Stack használatával a elnevezési jelölik, például 1803 jelzi a frissítést a 2018 március. Az Azure Stack-frissítési szabályzat kapcsolatban-váltás gyakoriságáról és a kibocsátási megjegyzések érhető el, lásd: [karbantartási szabályzat az Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

## <a name="prerequisites"></a>Előfeltételek

Gyakorolja a havi frissítési folyamat az VaaS, mielőtt, ismerkedjen meg a következő elemek:

- [Mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md)
- [Interaktív funkció ellenőrzési tesztelés](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Szükséges tesztek

A következő ellenőrzés havi szoftver ellenőrzés céljából a következő sorrendben kell végrehajtani:

1. Az Azure Stack havi frissítés-ellenőrzés
2. Felhőbeli szimuláció motor

## <a name="validating-software-updates"></a>A szoftverfrissítések érvényesítésekor

1. Hozzon létre egy új **csomag** munkafolyamat.
1. A fenti szükséges teszteket, kövesse az utasításokat a [futtatása csomag Érvényesítési tesztek](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Az alábbi szakaszban további útmutatást a **havi Azure Stack frissítés ellenőrzés** teszteléséhez.

### <a name="apply-the-monthly-update"></a>A havi frissítés alkalmazása

1. Válasszon ki egy ügynököt elleni tesztek végrehajtásához.
1. Ütemezés **havi az Azure Stack-frissítés ellenőrzés**.
1. Adja meg a helyét, az OEM-kiterjesztés csomagjának a stamp jelenleg telepített, és a helyét, az OEM-kiterjesztési csomag, amely a frissítés során lépnek érvénybe. Ezek a csomagok URL-beállítása: [érvényesítéshez-csomagok kezelése](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. A kiválasztott ügynök kövesse a lépéseket, a felhasználói felületen.

Ha kérdése vagy aggodalma van, lépjen kapcsolatba [VaaS súgó](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)