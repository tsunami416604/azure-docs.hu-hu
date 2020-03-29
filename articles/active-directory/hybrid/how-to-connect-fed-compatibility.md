---
title: Az Azure AD összevonás kompatibilitási listája
description: Ez a lap nem a Microsoft identitásszolgáltatóival rendelkezik, amelyek egyszeri bejelentkezés megvalósításához használhatók.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244765"
---
# <a name="azure-ad-federation-compatibility-list"></a>Az Azure AD összevonás kompatibilitási listája
Az Azure Active Directory egyszeri bejelentkezést és továbbfejlesztett alkalmazás-hozzáférési biztonságot biztosít az Office 365 és más Microsoft Online-szolgáltatások számára a hibrid és csak felhőalapú megvalósításokhoz, anélkül, hogy harmadik féltől származó megoldást kellene igényelnie. Az Office 365 a Microsoft legtöbb online szolgáltatásához hasonlóan integrálva van az Azure Active Directoryval a címtárszolgáltatások, a hitelesítés és az engedélyezés számára. Az Azure Active Directory is biztosít egyszeri bejelentkezés több ezer SaaS-alkalmazások és helyszíni webalkalmazások. Tekintse meg az Azure Active Directory [alkalmazásgalériában](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) a támogatott SaaS-alkalmazások. 

## <a name="idp-validation"></a>IDP-érvényesítés
Ha szervezete külső összevonási megoldást használ, konfigurálhatja az egyszeri bejelentkezést a helyszíni Active Directory-felhasználók számára a Microsoft Online-szolgáltatásokkal, például az Office 365-tel, feltéve, hogy a külső összevonási megoldás kompatibilis az Azure-ral Az Active Directory t.  A kompatibilitással kapcsolatos kérdéseivel forduljon személyazonosság-szolgáltatójához.  Ha szeretné látni azoknak az identitásszolgáltatóknak a listáját, akiket korábban tesztelt az Azure AD-vel való kompatibilitás szempontjából, a Microsoft kattintson [ide.](https://www.microsoft.com/download/details.aspx?id=56843) 

>[!NOTE]
>A Microsoft már nem biztosít érvényesítési tesztelést független identitásszolgáltatók számára az Azure Active Directoryval való kompatibilitás érdekében. Ha szeretné kipróbálni a termék interoperabilitását, kérjük, olvassa el ezeket az [irányelveket.](https://www.microsoft.com/download/details.aspx?id=56843) 

## <a name="next-steps"></a>Következő lépések

- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
