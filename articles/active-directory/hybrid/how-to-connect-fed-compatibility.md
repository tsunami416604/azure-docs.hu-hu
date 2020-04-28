---
title: Az Azure AD összevonás kompatibilitási listája
description: Ezen a lapon az egyszeri bejelentkezés megvalósítására szolgáló nem Microsoft-identitású szolgáltatók találhatók.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5090101c486562e33de56402db348c6038c8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "60244765"
---
# <a name="azure-ad-federation-compatibility-list"></a>Az Azure AD összevonás kompatibilitási listája
A Azure Active Directory az Office 365 és más Microsoft online szolgáltatásainak egyszeri bejelentkezését és továbbfejlesztett alkalmazás-hozzáférését teszi lehetővé a hibrid és a kizárólag felhőalapú megvalósításokhoz anélkül, hogy harmadik féltől származó megoldást kellene igényelni. Az Office 365, mint a Microsoft online szolgáltatásainak többsége, integrálva van a címtárszolgáltatások, a hitelesítés és az engedélyezés Azure Active Directoryával. Azure Active Directory emellett egyszeri bejelentkezést is biztosít több ezer SaaS-alkalmazáshoz és helyszíni webalkalmazásokhoz. Tekintse meg a támogatott SaaS-alkalmazások Azure Active Directory [alkalmazás-gyűjteményét](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) . 

## <a name="idp-validation"></a>IDENTITÁSSZOLGÁLTATÓ érvényesítése
Ha a szervezete egy külső gyártótól származó összevonási megoldást használ, az egyszeri bejelentkezést a helyszíni Active Directory felhasználók számára is beállíthatja a Microsoft Online Services szolgáltatással, például az Office 365-mel, feltéve, hogy a harmadik féltől származó összevonási megoldás kompatibilis a Azure Active Directoryval.  A kompatibilitással kapcsolatos kérdésekért forduljon az identitás-szolgáltatóhoz.  Ha szeretné megtekinteni a Microsoft által az Azure AD-vel való kompatibilitásra korábban tesztelt identitás-szolgáltatók listáját, kattintson [ide](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft már nem biztosít érvényesítési teszteket a független identitás-szolgáltatók számára a Azure Active Directory való kompatibilitás érdekében. Ha tesztelni szeretné a terméket az együttműködéshez, tekintse meg ezeket az [irányelveket](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Következő lépések

- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
