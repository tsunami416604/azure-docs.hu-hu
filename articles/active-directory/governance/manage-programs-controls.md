---
title: Programok és vezérlők a hozzáférési felülvizsgálatok – Azure Active Directory kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre minden egyes szabályozási, kockázatkezelési és megfelelőségi-kezdeményezéshez az további programokat a szervezet Azure Active Directory hozzáférési felülvizsgálatok másként vezérlőinek vizualizációkká.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577263"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Kezelheti a programok és vezérlők az Azure ad hozzáférési felülvizsgálatokkal

Az Azure Active Directory (Azure AD) csoport tagjai és alkalmazás-hozzáférés hozzáférési felülvizsgálatai tartalmazza. Ezekben a példákban a vezérlőelemek győződjön meg arról, ki férhet hozzá a szervezet csoporttagságot és az alkalmazások a felügyeletet. Szervezet ezek a vezérlők segítségével hatékonyan oldja meg a szabályozási, kockázatkezelési és megfelelőségi követelmények.

## <a name="create-and-manage-programs-and-their-controls"></a>Hozzon létre, és a programok és vezérlőik kezelése
Egyszerűsítheti a nyomon követni, és a hozzáférési felülvizsgálatok gyűjtése a különböző felhasználási célokra szervezetspecifikus programokban rendezve kezelje. Minden hozzáférési felülvizsgálat program lehet kapcsolódni. Ezután egy auditor jelentések előkészületeként, összpontosíthat a hozzáférési felülvizsgálatok egy adott kezdeményezés terjed ki.  Programok és a hozzáférési felülvizsgálat eredményei láthatók a globális rendszergazdai, felhasználó rendszergazdai, biztonsági rendszergazdai vagy biztonsági olvasói szerepkör felhasználói számára.

Programok listájának megtekintéséhez nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) válassza **programok**.

**Alapértelmezett Program** mindig szerepel. Ha Ön globális rendszergazda vagy felhasználói rendszergazda szerepkört, az további programokat is létrehozhat. Választhat, például, hogy az egyes megfelelőségi-kezdeményezéshez egy program vagy üzleti cél.

Ha már nincs szüksége egy programot, és nem kell minden olyan vezérlőelemek, hozzá kell kapcsolni, törölheti azt.

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)
- [A csoportok és alkalmazások a hozzáférési felülvizsgálatok eredményeinek beolvasása](retrieve-access-review.md)
