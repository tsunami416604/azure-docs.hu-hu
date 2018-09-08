---
title: Szolgáltatását az Azure Stack érvényesítési áttekintése |} A Microsoft Docs
description: Ismert problémák szolgáltatásként az Azure Stack érvényesítési áttekintése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 56251245a23df031f3bc8fe3d36de43e194fbcc7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159673"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Mit jelent a szolgáltatását az Azure Stack érvényesítési?

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Érvényesítés szolgáltatásként (VaaS) egy natív Azure-szolgáltatás, amelynek megoldás azon partnereinknek, akik a Microsoft Azure Stack-ajánlatok közös vannak mérnöki. Megoldáspartnerek a szolgáltatás használatával ellenőrizze, hogy megoldásaikat a Microsoft követelményeinek, és működnek megfelelően, az Azure Stack használatával.

Az elsődleges felhasználási VaaS van:

- Új Azure Stack-megoldások ellenőrzése
- Az Azure Stack szoftver ellenőrzése
- A digitálisan aláírt felhőszolgáltatói partner csomagok központi telepítése során használt beolvasása
- Az Azure Stack érvényesítési biztosíték előzetes verzió

## <a name="validate-new-azure-stack-solution"></a>Új Azure Stack megoldás ellenőrzése

Partnerek a megoldás szűrőérvényesítési munkafolyamat segítségével ellenőrizze az új Azure Stack megoldásokat. A megoldás sikeresen teljesítenie kell a szükséges hardver Lab Kit (HKL) az Azure Stack tesztek összetevő tesztjét. Csak a munkafolyamat minden új megoldás kétszer futtatni kell: a minimális és maximális konfiguráció egyszer.

További információkért lásd: [érvényesíteni egy új Azure Stack megoldás](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-software"></a>Az Azure Stack szoftver ellenőrzése

Partnerek a csomag érvényesítése munkafolyamat használatával ellenőrizze, hogy a megoldás együttműködik az Azure Stack szoftverfrissítés legutóbbi. Legalább a csomag érvényesítése munkafolyamat kell futtatni a Microsoft által ajánlott hardver környezet és a egy megoldást, a javítási és frissítési (P & U) használták a alkalmazni a frissítést. A referenciakonfiguráció build a futtatnia kell a csomag érvényességének ellenőrzése.

További információkért lásd: [ellenőrzése a Microsoft szoftverfrissítések](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>A digitálisan aláírt megoldás partner csomagok beszerzése

Mellett az Azure Stack-frissítések ellenőrzése, használhatja a csomag szűrőérvényesítési munkafolyamat a számítógép-Gyártói testreszabási csomagok, többek között az Azure Stack-partner-specifikus illesztőprogramok, a belső vezérlőprogram és az egyéb szoftvereket, az Azure Stack üzembe helyezése során használt frissítések ellenőrzése szoftver. Az Azure Stack szoftver használatával legalább a minimális méretű megoldást, amelyek támogatják az aktuális verzión ellenőrzi csomagok telepítését. A frissített csomag fel kell tölteni a szolgáltatás a Teszt indítása előtt. Ha a vizsgálatok sikeresek, vaashelp@microsoft.com. Ossza meg az Azure Stack-partner, hogy a csomag vizsgálat befejeződött, és a digitálisan alá kell az Azure Stack digitális aláírással. A Microsoft aláírja a csomagot, és értesíti az Azure Stack-partner, hogy a csomag letölthető a portálon.

További információkért lásd: [ellenőrzése OEM csomagok](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-azure-stack-validation-collateral"></a>Az Azure Stack érvényesítési biztosíték előzetes verzió

A Microsoft rendszeresen elérhetővé új funkciókat az Azure Stackben. Ezek a funkciók kidolgozását piacra a fejlesztési folyamat részeként a új teszt biztosíték szeretné elérhetővé tenni a tesztmenetek munkafolyamatban. A tesztmenetek munkafolyamat a munkafolyamatok lehetővé teszik a munkaköröket külsős informatikusok szoftvertesztek végrehajtása a teszt biztosíték tartalmaz. Ne használja a tesztmenetek munkafolyamat elküldése jóváhagyásra eredményeket. A megoldás érvényesítése és a csomagmegosztás ellenőrzések munkafolyamat használatával a megoldás hivatalos jóváhagyást kérhet a.

## <a name="next-steps"></a>További lépések

- Első lépésként és [, amely szolgáltatásfiókként az ellenőrzés beállítása](azure-stack-vaas-validate-solution-new.md)
