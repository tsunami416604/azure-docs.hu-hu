---
title: Az Azure AD összevonás kompatibilitási listája
description: Ezen a lapon nem a Microsofttól Identitásszolgáltatók, amelyek segítségével az egyszeri bejelentkezés megvalósítása van.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: f5e85a551181a3dd4618db2611a99675329cfabd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592013"
---
# <a name="azure-ad-federation-compatibility-list"></a>Az Azure AD összevonás kompatibilitási listája
Az Azure Active Directory egyszeri bejelentkezést biztosít, és a bővített alkalmazásbiztonsági hozzáférés az Office 365 és más Microsoft Online services az hibrid és a csak felhőalapú hitelesítés megvalósításához anélkül, hogy bármely harmadik féltől származó megoldásban. Office 365, például a Microsoft Online services, a legtöbb Azure Active Directory címtárszolgáltatásban, hitelesítési és engedélyezési van integrálva. Egyszeri bejelentkezés az SaaS-alkalmazásokhoz ezer is biztosít az Azure Active Directory és a helyszíni webalkalmazások. Tekintse meg az Azure Active Directory [alkalmazáskatalógusában](https://azuremarketplace.microsoft.com/marketplace/apps) támogatott SaaS-alkalmazásokhoz. 

## <a name="idp-validation"></a>IDP érvényesítése
Ha a szervezet egy harmadik féltől származó összevonási megoldást használ, konfigurálható egyszeri bejelentkezést a helyszíni Active Directory-felhasználók a Microsoft Online szolgáltatások, például az Office 365, a harmadik féltől származó összevonási megoldás az Azure-ral kompatibilis megadott Active Directory.  Kompatibilitási kérdéseket lépjen kapcsolatba az identitásszolgáltató.  Ha szeretné Identitásszolgáltatók, aki korábban lettek tesztelve az Azure AD, Microsoft által kompatibilitási kattintson megtekintéséhez [Itt](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft már nem biztosítja az érvényesítési tesztek független Identitásszolgáltatók Azure Active Directory-való kompatibilitás érdekében. Ha szeretné a terméket a való együttműködés teszteléséhez tekintse meg a [irányelvek](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>További lépések

- [A helyszíni címtárak integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
- [Azure AD Connect és összevonás](active-directory-aadconnectfed-whatis.md)
