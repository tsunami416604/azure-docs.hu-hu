---
title: Az Azure AD összevonás kompatibilitási listája
description: Ez a lap nem a Microsofttól Identitásszolgáltatók, amelyek segítségével az egyszeri bejelentkezés megvalósítása rendelkezik.
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
ms.date: 08/23/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4a31e7ed12e75bd7dee3d53a8d650179d8e118d8
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46310981"
---
# <a name="azure-ad-federation-compatibility-list"></a>Az Azure AD összevonás kompatibilitási listája
Az Azure Active Directory egyszeri bejelentkezést biztosít, és a bővített alkalmazás hozzáférés-biztonságot Office 365-höz és más Microsoft Online services hibrid és a csak felhőalapú megvalósításokhoz, anélkül, hogy bármely harmadik féltől származó megoldás. Office 365-höz, például a Microsoft Online szolgáltatásaihoz, a legtöbb integrálja az Azure Active Directory címtárszolgáltatásokat, a hitelesítés és engedélyezés a. Egyszeri bejelentkezés több ezer SaaS-alkalmazásokat is biztosít az Azure Active Directory, és a helyszíni webalkalmazásokat. Tekintse meg az Azure Active Directory [alkalmazáskatalógusában](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) támogatott SaaS-alkalmazásokhoz. 

## <a name="idp-validation"></a>Identitásszolgáltató érvényesítése
Ha a szervezet használ egy harmadik féltől származó összevonási megoldás, konfigurálható egyszeri bejelentkezés a helyszíni Active Directory-felhasználók Microsoft Online szolgáltatásokkal, például az Office 365, a harmadik féltől származó összevonási megoldás az Azure-ral kompatibilis a megadott Az Active Directoryban.  Kompatibilitási kapcsolatos kérdése van lépjen kapcsolatba az identitásszolgáltató.  Ha szeretné ki előzetesen teszteltük, kompatibilitási és az Azure AD, Microsoft, kattintson az Identitásszolgáltatók listájának megtekintéséhez [Itt](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft már nem biztosít ellenőrzési tesztelés célja annak független Identitásszolgáltatók Azure Active Directoryval való kompatibilitás érdekében. Ha szeretné, hogy a termék együttműködés teszteléséhez tekintse meg ezeket [irányelvek](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>További lépések

- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
