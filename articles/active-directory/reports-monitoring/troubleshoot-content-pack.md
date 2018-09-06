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
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf50dbf942dc7a82afbb60455be45b6c4b287ccd
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782172"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Hibaelhárítás az Azure Active Directory-Tevékenységnaplók tartalomcsomag-hibái naplók 

|  |
|--|
|Jelenleg az Azure AD Power BI-tartalomcsomag adatokat lekérni az Azure AD-bérlővel használja az Azure AD Graph API-kat. Ennek eredményeképpen látni a tartalomcsomagot a rendelkezésre álló adatok és a hitelesítő adatokat használja adatok néhány eltérése a [jelentéséért a Microsoft Graph API-k](concept-reporting-api.md). |
|  |

A Power BI-tartalomcsomag használata az Azure Active Directory előzetes kiadásában, esetén lehetséges, hogy hibákba ütközik a következő: 

- [A frissítés nem sikerült](troubleshoot-content-pack.md#refresh-failed) 
- [Az adatforrás hitelesítő adatainak frissítése sikertelen](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Adatok importálása túl sokáig tart](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
A cikk ismerteti a lehetséges okok és a hibák kijavításához információt.
 
## <a name="refresh-failed"></a>A frissítés nem sikerült 
 
**Hogyan ezt a hibát az illesztett**: e-mailt a Power bi-ban vagy a frissítési előzmények sikertelen állapotát. 


| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| Frissítse a hibák is okozhatja, ha a hitelesítő adatait, a tartalomcsomag csatlakozó felhasználók új, de a kapcsolat beállításait a tartalomcsomag frissítése nem hiba. | A Power bi-ban keresse meg az adatkészlet, az Azure Active Directory-Tevékenységnaplók irányítópultja (az Azure Active Directory-Tevékenységnaplók naplók) megfelelő, válassza a frissítés ütemezése, és adja meg a az Azure AD hitelesítő adatait. |
| Frissítés az alapul szolgáló tartalomcsomag az adatokkal kapcsolatos problémák miatt meghiúsulhat. | Küldjön egy támogatási jegyet. További információkért lásd: [hogyan kérhet támogatást az Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Az adatforrás hitelesítő adatainak frissítése sikertelen 
 
**Hogyan ezt a hibát az illesztett**: A Power bi-ban történő csatlakozás esetében az Azure Active Directory-Tevékenységnaplók naplók (előzetes verzió) tartalomcsomaghoz. 

| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| A kapcsolódó felhasználó nem egy globális rendszergazdai vagy biztonsági olvasói vagy egy biztonsági rendszergazdával. | Olyan fiókot használjon, amely egy globális rendszergazdai vagy biztonsági olvasói vagy a biztonsági rendszergazda a tartalomcsomagok eléréséhez. |
| A bérlő nem egy Premium-bérlő, vagy nem rendelkezik a prémium szintű licenccel fájl legalább egy felhasználót. | Küldjön egy támogatási jegyet. További információkért lásd: [hogyan kérhet támogatást az Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Adatok importálása túl sokáig tart 
 
**Hogyan ezt a hibát az illesztett**: A Power bi-ban, ha csatlakozott a tartalomcsomaghoz az importálási folyamat elindítja az irányítópulton előkészítése az Azure Active Directory-Tevékenységnaplók log. Az üzenet jelenik meg: "*... az adatok importálása* "  

| Ok | Hogyan háríthatja el a |
| ---   | ---        |
| A bérlő méretétől függően ez a lépés sikerült igénybe vehet néhány perc és 30 perc. | Csak türelemmel. Ha az üzenet megjelenítése az Irányítópulton egy órán belül nem változik, nyújtson be egy támogatási jegyet. További információkért lásd: [hogyan kérhet támogatást az Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>További lépések

Az Azure Active Directory előzetes kiadásában a Power BI-tartalomcsomag telepítéséhez kattintson [Itt](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/).


