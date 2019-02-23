---
title: Programok és vezérlők kezelése az Azure AD hozzáférési felülvizsgálatokkal |} A Microsoft Docs
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
ms.openlocfilehash: 0ea210c79833ad9e070253a9511cae539730a8d5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731412"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Programok és vezérlők kezelése az Azure AD hozzáférési felülvizsgálatok

Az Azure Active Directory (Azure AD) csoport tagjai és alkalmazás-hozzáférés hozzáférési felülvizsgálatai tartalmazza. Ezekben a példákban a vezérlőelemek győződjön meg arról, ki férhet hozzá a szervezet csoporttagságot és az alkalmazások a felügyeletet. Szervezet ezek a vezérlők segítségével hatékonyan oldja meg a szabályozási, kockázatkezelési és megfelelőségi követelmények.

## <a name="create-and-manage-programs-and-their-controls"></a>Hozzon létre, és a programok és vezérlőik kezelése
Egyszerűsítheti a nyomon követni, és a hozzáférési felülvizsgálatok gyűjtése a különböző felhasználási célokra szervezetspecifikus programokban rendezve kezelje. Minden hozzáférési felülvizsgálat program lehet kapcsolódni. Ezután egy auditor jelentések előkészületeként, összpontosíthat a hozzáférési felülvizsgálatok egy adott kezdeményezés terjed ki.  Programok és a hozzáférési felülvizsgálati eredmények láthatóak a felhasználók számára a globális rendszergazdai, felhasználóifiók-adminisztrátor, biztonsági rendszergazdai vagy biztonsági olvasói szerepkör.

Programok listájának megtekintéséhez nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) válassza **programok**.

**Alapértelmezett Program** mindig szerepel. Ha Ön globális rendszergazda vagy felhasználói fiók rendszergazdai szerepkör, az további programokat is létrehozhat. Választhat, például, hogy az egyes megfelelőségi-kezdeményezéshez egy program vagy üzleti cél.

Ha már nincs szüksége egy programot, és nem kell minden olyan vezérlőelemek, hozzá kell kapcsolni, törölheti azt.

## <a name="next-steps"></a>További lépések

- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)
- [A csoportok és alkalmazások a hozzáférési felülvizsgálatok eredményeinek beolvasása](retrieve-access-review.md)
