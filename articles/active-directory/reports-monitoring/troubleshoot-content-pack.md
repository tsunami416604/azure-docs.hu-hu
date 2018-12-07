---
title: Hibaelhárítás az Azure Active Directory-Tevékenységnaplók tartalomcsomag-hibái naplók |} A Microsoft Docs
description: Biztosít, javítsa ki őket az Azure Active Directory-Tevékenységnaplók tartalomcsomag és lépéseket hibaüzenetek listáját.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 65747da92a3cad770cd9d474d27645782f7306b9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998741"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Hibaelhárítás az Azure Active Directory-Tevékenységnaplók tartalomcsomag-hibái naplók 

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

A Power BI-tartalomcsomag használata az Azure Active Directory (Azure AD), esetén lehetséges, hogy hibákba ütközik a következő: 

- [A frissítés nem sikerült](troubleshoot-content-pack.md#refresh-failed) 
- [Az adatforrás hitelesítő adatainak frissítése sikertelen](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Adatok importálása túl sokáig tart](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 

A cikk ismerteti a lehetséges okok és a hibák kijavításához információt.
 
## <a name="refresh-failed"></a>A frissítés nem sikerült 
 
**Hogyan ezt a hibát az illesztett**: e-mailt a Power bi-ban vagy a frissítési előzmények sikertelen állapotát. 


| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| Frissítse a hibák is okozhatja, ha a hitelesítő adatait, a tartalomcsomag csatlakozó felhasználók új, de a kapcsolat beállításait a tartalomcsomag frissítése nem hiba. | A Power bi-ban keresse meg az adatkészlet, az Azure ad-ben Tevékenységnaplók irányítópultja megfelelő (**Azure Active Directory-Tevékenységnaplók**) válassza a frissítés ütemezése, majd adja meg az Azure AD hitelesítő adatait. |
| Frissítés az alapul szolgáló tartalomcsomag az adatokkal kapcsolatos problémák miatt meghiúsulhat. | [Küldjön egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Az adatforrás hitelesítő adatainak frissítése sikertelen 
 
**Hogyan ezt a hibát az illesztett**: A Power bi-ban, amikor csatlakozik az Azure AD tevékenység-naplók. 

| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| A kapcsolódó felhasználó nem globális rendszergazda vagy a biztonsági olvasó vagy biztonsági rendszergazda. | Egy globális rendszergazdai vagy biztonsági olvasói vagy egy rendszergazda eléréséhez a tartalomcsomagok olyan fiókot használjon. |
| A bérlő nem egy Premium-bérlő, vagy nem rendelkezik a prémium szintű licenccel fájl legalább egy felhasználót. | [Küldjön egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Adatok importálása az túl lassú 
 
**Hogyan ezt a hibát az illesztett**: A Power bi-ban, a tartalomcsomag csatlakoztatása után az importálási folyamat elindítja az irányítópulton előkészítése az Azure Active Directory naplói. Az üzenet jelenik meg: **adatok importálása...**  anélkül, hogy bármilyen további folyamatban van.  

| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| A bérlő méretétől függően ez a lépés sikerült eltarthat pár percet vagy akár 30 percig. | Ha az üzenet nem változik a megjelenítése az Irányítópulton egy órán belül [küldjön egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>További lépések

* [Az Azure AD-jelentések a Power BI contect csomag telepítése](quickstart-install-power-bi-content-pack.md).
* [Használja a Power BI-tartalomcsomag jelenti a csomag az Azure ad-adatok megjelenítése](howto-power-bi-content-pack.md)
* [Hogyan kérhet támogatást az Azure Active Directoryhoz](../fundamentals/active-directory-troubleshooting-support-howto.md)
