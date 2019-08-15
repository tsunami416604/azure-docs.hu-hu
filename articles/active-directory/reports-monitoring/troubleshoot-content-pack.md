---
title: Hibaelhárítás Azure Active Directory tevékenység naplók a Content Pack-hibák | Microsoft Docs
description: A Azure Active Directory tevékenység tartalmi csomagjához tartozó hibaüzenetek listáját és a kijavításának lépéseit tartalmazza.
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a0a5b5306414eb50a1928ec8a1854f56055681
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987904"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Hibaelhárítás Azure Active Directory Activity naplók a Content Pack hibái 

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

A Azure Active Directory (Azure AD) Power BI-csomagjának használatakor előfordulhat, hogy a következő hibákba ütközik: 

- [Sikertelen frissítés](troubleshoot-content-pack.md#refresh-failed) 
- [Nem sikerült frissíteni az adatforrás hitelesítő adatait](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Az adatimportálás túl sokáig tart](#data-import-is-too-slow) 

Ez a cikk a lehetséges okokkal és a hibák megoldásával kapcsolatos információkat tartalmaz.
 
## <a name="refresh-failed"></a>Nem sikerült a frissítés. 
 
**A hiba felszínének módja**: A frissítési előzményekben Power BI vagy sikertelen állapotú e-mail-cím. 


| Ok | A javítás módja |
| ---   | ---        |
| A hibák frissítése akkor okozhat hibát, ha a Content Packhez csatlakozó felhasználók hitelesítő adatai alaphelyzetbe lettek állítva, de nem frissültek a Content Pack csatlakozási beállításaiban. | A Power BIban keresse meg az Azure AD-tevékenységek naplói irányítópultjának megfelelő adatkészletet (**Azure Active Directory tevékenység naplóit**), válassza az ütemezett frissítés lehetőséget, majd adja meg az Azure ad-beli hitelesítő adatait. |
| Nagy adathalmazok miatt a frissítés sikertelen lehet. | Jelenleg az Azure AD Content Pack Power BI csak kisméretű adatkészleteket tud támogatni (kevesebb, mint 500, 00 sor), mert néhány korlátozás miatt a Power BI szolgáltatásban időtúllépések fordulnak elő. Ha szabályozási hibák merülnek fel, vagy ha a frissítés időtúllépési problémák miatt meghiúsul, ennek oka az lehet, hogy nagy adatkészletet próbál beolvasni. Csökkentse a lekérdezés időszakát, és próbálkozzon újra.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nem sikerült frissíteni az adatforrás hitelesítő adatait 
 
**A hiba felszínének módja**: Power BI az Azure AD-tevékenység naplóihoz való csatlakozáskor. 

| Ok | A javítás módja |
| ---   | ---        |
| A csatlakozó felhasználó nem globális rendszergazda vagy biztonsági olvasó vagy biztonsági rendszergazda. | Olyan fiókot használjon, amely globális rendszergazda vagy biztonsági olvasó vagy biztonsági rendszergazda a tartalmi csomagokhoz való hozzáféréshez. |
| A bérlő nem prémium szintű bérlő, vagy nem rendelkezik legalább egy, prémium szintű licenccel rendelkező felhasználóval. | [Támogatási jegy](../fundamentals/active-directory-troubleshooting-support-howto.md)küldése.|
 


## <a name="data-import-is-too-slow"></a>Az adatimportálás túl lassú 
 
**A hiba felszínének módja**: Power BI a Content Pack összekapcsolását követően az Adatimportálási folyamat megkezdi az irányítópult előkészítését az Azure AD-tevékenység naplóihoz. A következő üzenet jelenik meg: **Az adatimportálás..** . további előrehaladás nélkül.  

| Ok | A javítás módja |
| ---   | ---        |
| A bérlő méretétől függően ez a lépés néhány perctől akár 30 percig is eltarthat. | Ha az üzenet nem változik, hogy egy órán belül megjelenítse az irányítópultot, a [támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>További lépések

* [Telepítse Power bi Content Pack for Azure ad-jelentéseket](quickstart-install-power-bi-content-pack.md).
* [Az Azure AD-jelentésekhez Power BI Content Pack használatával jelenítheti meg az adatokat](howto-power-bi-content-pack.md)
* [Hogyan kérhet támogatást az Azure Active Directoryhoz](../fundamentals/active-directory-troubleshooting-support-howto.md)
