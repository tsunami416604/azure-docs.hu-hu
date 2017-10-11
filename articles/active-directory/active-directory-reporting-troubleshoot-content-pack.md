---
title: "Hibaelhárítás az Azure Active Directory-tevékenység tartalomcsomag hibákat naplózza |} Microsoft Docs"
description: "Biztosít javítja őket az Azure Active Directory-tevékenység tartalomcsomag és lépéseket hibaüzenetek listáját."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: c880e9eb6d48bd1e38075fbd867d3906ec67b547
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>A tartalomcsomag hibákat naplózza hibaelhárítása az Azure Active Directory-tevékenység 


A Power BI tartalomcsomag használata az Azure Active Directory előzetes, esetén lehetséges, hogy futtatja-e be a következő hibákat: 

- [A frissítés nem sikerült](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Az adatforrás hitelesítő adatainak frissítése sikertelen](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Adatok importálása túl sokáig tart](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Ez a témakör a lehetséges okok és ezek a hibák megoldásával kapcsolatos információkat nyújt.
 
## <a name="refresh-failed"></a>A frissítés nem sikerült 
 
**Hogyan van illesztett hibára**: e-mailt a Power bi-ban vagy a frissítési előzmények "sikertelen" állapota. 


| Ok | Megoldásával kapcsolatban |
| ---   | ---        |
| Frissítési hiba hibákat is okozhat, ha a tartalomcsomag csatlakozik a felhasználói hitelesítő adatok alaphelyzetbe állítása, de a kapcsolat beállításai nem frissíthetők a tartalomcsomag. | A Power bi-ban keresse meg az adatkészletnek az Azure Active Directory-tevékenység naplók irányítópult (Azure Active Directory tevékenységi naplóit) megfelelő, válassza ki a frissítésütemezés, és adja meg Azure AD hitelesítő adatait. |
| Egy frissítés sikertelen lehet az alapul szolgáló tartalomcsomag adatok hibái miatt. | A fájl egy támogatási jegy. További részletekért lásd: [hogyan kérhet támogatást az Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Az adatforrás hitelesítő adatainak frissítése sikertelen 
 
**Hogyan van illesztett hibára**: A Power BI-ban az Azure Active Directory-tevékenység naplókat (preview) tartalomcsomag való kapcsolódás esetén. 

| Ok | Megoldásával kapcsolatban |
| ---   | ---        |
| A csatlakozó felhasználó, sem globális rendszergazda, és nem biztonsági olvasó vagy a biztonsági rendszergazdával. | Olyan fiókot, amely globális rendszergazda vagy egy biztonsági olvasó vagy egy biztonsági rendszergazdai a tartalomcsomagok eléréséhez használjon. |
| A bérlő nincs Premium-bérlő, vagy nem rendelkezik a fájl Premium licenccel rendelkező legalább egy felhasználót. | A fájl egy támogatási jegy. További részletekért lásd: [hogyan kérhet támogatást az Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Adatok importálása túl sokáig tart 
 
**Hogyan van illesztett hibára**: A Power BI-ban a tartalomcsomaghoz csatlakozás után az importálási folyamat elindítja az irányítópulton előkészítése az Azure Active Directory tevékenységnapló. Az üzenet jelenik meg: "*... adatok importálása* "  

| Ok | Megoldásával kapcsolatban |
| ---   | ---        |
| A bérlő méretétől függően ez a lépés sikerült igénybe vehet néhány perc és 30 perc. | Csak türelemmel. Ha az üzenet nem változtatja meg az irányítópult megjelenítése egy órán belül, adjon fájl egy támogatási jegy. További részletekért lásd: [hogyan kérhet támogatást az Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Következő lépések

A Power BI tartalomcsomag az Azure Active Directory előzetes telepítéséhez kattintson [Itt](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


