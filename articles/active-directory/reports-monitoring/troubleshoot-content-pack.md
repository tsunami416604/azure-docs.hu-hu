---
title: Hibaelhárítás az Azure Active Directory-Tevékenységnaplók tartalomcsomag-hibái naplók |} A Microsoft Docs
description: Biztosít, javítsa ki őket az Azure Active Directory-Tevékenységnaplók tartalomcsomag és lépéseket hibaüzenetek listáját.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ee49ae56122fe596a4490914677d91d2f0348f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807529"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Hibaelhárítás az Azure Active Directory-Tevékenységnaplók tartalomcsomag-hibái naplók 

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

A Power BI-tartalomcsomag használata az Azure Active Directory (Azure AD), esetén lehetséges, hogy hibákba ütközik a következő: 

- [A frissítés nem sikerült](troubleshoot-content-pack.md#refresh-failed) 
- [Az adatforrás hitelesítő adatainak frissítése sikertelen](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Adatok importálása túl sokáig tart](#data-import-is-too-slow) 

A cikk ismerteti a lehetséges okok és a hibák kijavításához információt.
 
## <a name="refresh-failed"></a>A frissítés nem sikerült 
 
**Hogyan ezt a hibát az illesztett**: E-mailben a Power bi-ban vagy a frissítési előzmények sikertelen állapotát. 


| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| Frissítse a hibák is okozhatja, ha a hitelesítő adatait, a tartalomcsomag csatlakozó felhasználók új, de a kapcsolat beállításait a tartalomcsomag frissítése nem hiba. | A Power bi-ban keresse meg az adatkészlet, az Azure ad-ben Tevékenységnaplók irányítópultja megfelelő (**Azure Active Directory-Tevékenységnaplók**) válassza a frissítés ütemezése, majd adja meg az Azure AD hitelesítő adatait. |
| Frissítés nagy méretű adatkészletek miatt meghiúsulhat. | Az Azure AD tartalomcsomaggal a Power BI jelenleg csak kis adatkészletekhez (kevesebb mint 500,00 sorok) a Power BI szolgáltatásban időtúllépések körül bizonyos korlátozások miatt támogathat. Ha a szabályozási hibákat tapasztal, vagy ha a frissítés időtúllépési problémák miatt nem sikerül, lehet, hogy egy nagy méretű adathalmazt beolvasni kívánt. Csökkentse az időtartamot a lekérdezés, majd próbálkozzon újra.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Az adatforrás hitelesítő adatainak frissítése sikertelen 
 
**Hogyan ezt a hibát az illesztett**: A Power bi-ban, az Azure AD tevékenység naplók tartalomcsomaghoz való csatlakozáskor. 

| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| A kapcsolódó felhasználó nem globális rendszergazda vagy a biztonsági olvasó vagy biztonsági rendszergazda. | Egy globális rendszergazdai vagy biztonsági olvasói vagy egy rendszergazda eléréséhez a tartalomcsomagok olyan fiókot használjon. |
| A bérlő nem egy Premium-bérlő, vagy nem rendelkezik a prémium szintű licenccel fájl legalább egy felhasználót. | [Küldjön egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Adatok importálása az túl lassú 
 
**Hogyan ezt a hibát az illesztett**: A Power bi-ban, a tartalomcsomag csatlakoztatása után az importálási folyamat elindítja az irányítópulton előkészítése az Azure Active Directory naplói. A következő üzenet jelenik meg: **Adatok importálása...**  anélkül, hogy bármilyen további folyamatban van.  

| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| A bérlő méretétől függően ez a lépés sikerült eltarthat pár percet vagy akár 30 percig. | Ha az üzenet nem változik a megjelenítése az Irányítópulton egy órán belül [küldjön egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>További lépések

* [Az Azure AD-jelentések Power BI-tartalomcsomag telepítése](quickstart-install-power-bi-content-pack.md).
* [Használja a Power BI-tartalomcsomag jelenti a csomag az Azure ad-adatok megjelenítése](howto-power-bi-content-pack.md)
* [Hogyan kérhet támogatást az Azure Active Directoryhoz](../fundamentals/active-directory-troubleshooting-support-howto.md)
