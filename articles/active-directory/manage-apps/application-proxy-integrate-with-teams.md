---
title: Férhetnek hozzá a Teams szolgáltatásban az Azure AD-alkalmazásproxyn alkalmazásokhoz |} A Microsoft Docs
description: Azure AD-alkalmazásproxy használatával a helyszíni alkalmazások hozzáférhetnek a Microsoft Teams keresztül.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 07c71c898df27c53e8f7f7b480bb04c0f09d8699
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962757"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Microsoft Teams keresztül a helyszíni alkalmazások eléréséhez

Az Azure Active Directory Application Proxy biztosít egyszeri bejelentkezést a helyszíni alkalmazások bárhol is. Microsoft Teams leegyszerűsíti az együttműködésen alapuló erőfeszítések egy helyen. A kettő együtt integrálása, az azt jelenti, hogy a felhasználók számára hatékony munkavégzést és a csapattársai bármilyen helyzetben. 

A felhasználók felhőbeli alkalmazásokat adhat hozzá a Teams-csatornákkal [lapok használatával](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), de mi a helyzet a SharePoint-webhelyek vagy tervezési eszköz, amelyek a helyszínen üzemeltetett? Application Proxy szolgáltatást ajánljuk. Miképpen vehetnek fel saját csatornákhoz a azonos külső URL-címekkel mindig elérésére használt alkalmazások távolról alkalmazásproxyn keresztül közzétett alkalmazásokat. És mivel kezeli az Azure Active Directory Application Proxy végzi a hitelesítést, a felhasználók egyszeri bejelentkezés használatát.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Az alkalmazásproxy-összekötő telepítése és az alkalmazás közzététele

Ha még nem tette, [Proxy konfigurálása a bérlő számára, és telepítse az összekötőt](application-proxy-add-on-premises-application.md). Ezt követően [a helyszíni alkalmazás közzététele](application-proxy-add-on-premises-application.md) a távoli hozzáféréshez. Az alkalmazás közzétételekor használt jegyezze meg a külső URL-cím, mert használja az alkalmazás hozzáadásához a csapatoknak.

Ha már rendelkezik a közzétett alkalmazásokat, de nem emlékszik a külső URL-címeit, keresse meg azokat a [az Azure portal](https://portal.azure.com). Jelentkezzen be, majd nyissa meg **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** > Válassza ki az alkalmazást >  **Az alkalmazásproxy**.

## <a name="add-your-app-to-teams"></a>Az alkalmazás hozzáadása csoportokhoz

Miután közzéteszi az alkalmazás alkalmazásproxyn keresztül, lehetővé teszik a felhasználók felvehetnek, egy lapon közvetlenül a Teams-csatornákkal, és ezután az alkalmazás használatára a csoport minden tagja számára érhető el. Kell őket az alábbi három lépést követve:

1. Keresse meg a Teams-csatornához, ahol szeretné hozzáadni ezt az alkalmazást, és válassza ki **+** ikont egy lap hozzáadásához.

   ![Válassza a lap hozzáadása](./media/application-proxy-integrate-with-teams/add-tab.png)

2. Válassza ki **webhely** , a beállítások lapon.

   ![Webhely hozzáadása](./media/application-proxy-integrate-with-teams/website.png)

3. Nevezze el a lapot, és állítsa be az URL-címet az alkalmazásproxy külső URL-címre. 

   ![Lap neve és az URL-cím konfigurálása](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Ha egy csapat egyik tagja felveszi a lapon, megjelenik a csatorna minden tagja számára. Az alkalmazáshoz hozzáféréssel rendelkező felhasználók beolvasása egyszeri bejelentkezéses hozzáférést a Microsoft Teams használt hitelesítő adatokkal. Minden olyan felhasználók, akik nem fér hozzá az alkalmazást a Teams szolgáltatásban lapon látható, de le vannak tiltva, amíg nem adja meg azokat engedélyeket a helyszíni alkalmazás és az alkalmazás az Azure portal közzétett verziója. 

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [közzététele a helyszíni SharePoint-webhelyek](application-proxy-integrate-with-sharepoint-server.md) az alkalmazásproxy használatával.
- Konfigurálja, hogy az alkalmazások [egyéni tartományok](application-proxy-configure-custom-domain.md) a külső URL-címéhez. 
