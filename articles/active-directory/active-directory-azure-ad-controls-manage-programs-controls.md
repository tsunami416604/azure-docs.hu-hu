---
title: Programok kezeléséhez, és szabályozza az Azure AD hozzáférési értékelést |} Microsoft Docs
description: A szervezet Azure Active Directory hozzáférési értékelést vezérlőelemként gyűjtése és gyors minden irányítás, kockázatkezelés és megfelelőségi kezdeményezésére további programokat hozhat létre.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: fe2cddbe47f49ee93d3e58beb63d9d82286ec441
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080928"
---
# <a name="manage-programs-and-their-controls"></a>Programok telepítése és a vezérlők kezelése 

Azure Active Directory (Azure AD) hozzáférési értékelést csoport tagjai és alkalmazás-hozzáférés tartalmazza. Ezekben a példákban a vezérlők győződjön meg arról, ki férhet hozzá a szervezet csoporttagságot és az alkalmazások a felügyeletet. Szervezet ezen vezérlők segítségével hatékonyan cím a cégirányítási, a kockázatkezelés és a megfelelőségi követelmények.

## <a name="create-and-manage-programs-and-their-controls"></a>Programok telepítése és a vezérlők létrehozása és kezelése
Egyszerűbbé teheti az nyomon követése és hozzáférési értékelést gyűjtése többféle célra programokba rendezésével. A program minden áttekintése lehet társítani. Ezután jelentések egy auditor előkészületeként, összpontosíthat a hozzáférési értékelést egy adott Initiative hatókörében.  Programok telepítése és a hozzáférés felülvizsgálati eredményeinek láthatók a felhasználók számára a globális rendszergazda, a felhasználói fiók rendszergazdájához, a biztonsági rendszergazda vagy a biztonsági olvasó szerepkört.

Programok listájának megtekintéséhez keresse fel a [hozzáférés ellenőrzi, hogy lap](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) válassza **programok**.

**Alapértelmezett Program** mindig szerepel. Ha egy globális rendszergazda vagy a felhasználói fiók rendszergazdai szerepkörrel, további programok is létrehozhat. Választhat, például, hogy minden megfelelőségi Initiative egy program vagy üzleti cél.

Ha már nincs szüksége egy programot, és nincs beállítva a kapcsolni vezérlőket, törölheti azt.

## <a name="next-steps"></a>További lépések

- [Hozzáférési felülvizsgálat létrehozása egy csoport tagjai számára vagy egy alkalmazáshoz való hozzáférés céljából](active-directory-azure-ad-controls-create-access-review.md)
- [Egy áttekintése eredményének beolvasása](active-directory-azure-ad-controls-retrieve-access-review.md)
