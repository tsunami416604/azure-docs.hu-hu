---
title: Az Azure Sentinel eltávolítása| Microsoft dokumentumok
description: Az Azure Sentinel-példány törlése.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581684"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Az Azure Sentinel eltávolítása a munkaterületről

Ha már nem szeretné használni az Azure Sentinel, ez a cikk ismerteti, hogyan távolíthatja el a munkaterületről.

## <a name="how-to-remove-azure-sentinel"></a>Az Azure Sentinel eltávolítása

Kövesse ezt a folyamatot az Azure Sentinel eltávolításához a munkaterületről:

1. Nyissa meg az **Azure Sentinel**t, majd a **Beállítások**lehetőséget, és válassza az Azure **Sentinel eltávolítása**lapot.

1. Az Azure Sentinel eltávolítása előtt használja a jelölőnégyzeteket, hogy tudassa velünk, miért távolítja el.

1. Válassza **az Azure Sentinel eltávolítása lehetőséget a munkaterületről.**
    
    ![A SecurityInsights-megoldás törlése](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Mi történik a színfalak mögött?

A megoldás eltávolításakor az Azure Sentinel akár 48 órát is igénybe vesz a törlési folyamat első fázisának befejezése.

A kapcsolat leválasztásának azonosítása után megkezdődik a kiszállási folyamat.

**Az összekötők konfigurációja törlődik:**
-   Office 365

-   AWS

-   Microsoft-szolgáltatások biztonsági riasztásai (Azure ATP, Microsoft Cloud App Security, beleértve a Cloud Discovery árnyékinformatikai jelentéskészítést, az Azure AD Identity Protection, a Microsoft Defender ATP, az Azure Security Center)

-   Fenyegetések felderítése

-   Gyakori biztonsági naplók (beleértve a CEF-alapú naplókat, a Barracuda és a Syslog) (Ha rendelkezik az Azure Security Centerrel, ezek a naplók továbbra is gyűjteni fognak.)

-   Windows biztonsági események (Ha rendelkezik az Azure Security Center, ezek a naplók továbbra is gyűjteni.)

Az első 48 órában az adatok és az analitikus szabályok (beleértve a valós idejű automatizálási konfiguráció) már nem lesz elérhető vagy lekérdezhető az Azure Sentinelben.

**30 nap elteltével ezek az erőforrások törlődnek:**

-   Incidensek (beleértve a vizsgálat metaadatait)

-   Analitikus szabályok

-   Könyvjelzők

A program nem távolítja el a forgatókönyveket, a mentett munkafüzeteket, a mentett vadászati lekérdezéseket és a jegyzetfüzeteket. **Néhány an szakíthatja meg az eltávolított adatok miatt. Ezeket manuálisan is eltávolíthatja.**

A szolgáltatás eltávolítása után van egy 30 napos türelmi idő, amely alatt újra engedélyezheti a megoldást, és az adatok és az analitikus szabályok visszaállnak, de a leválasztott csatlakoztatott csatlakoztatott összekötőket újra kell csatlakoztatni.

> [!NOTE]
> Ha eltávolítja a megoldást, az előfizetés továbbra is regisztrálva lesz az Azure Sentinel erőforrás-szolgáltatónál. **Manuálisan is eltávolíthatja.**




## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan távolíthatja el az Azure Sentinel szolgáltatást. Ha meggondolja magát, és újra telepíteni szeretné:
- Első lépések [a beszálláshoz az Azure Sentinelben.](quickstart-onboard.md)
