---
title: Az Azure AD alkalmazás Proxy alkalmazások csoportban eléréséhez |} Microsoft Docs
description: Azure AD alkalmazásproxy segítségével Microsoft Teams keresztül férnek hozzá a helyszíni alkalmazások.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: b2d240e6339902ce24061b5db3ad1b8c2915eb59
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303601"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Microsoft Teams keresztül férnek hozzá a helyszíni alkalmazások

Az Azure Active Directory Alkalmazásproxyjával lehetővé teszi az egyszeri bejelentkezést a helyszíni alkalmazások függetlenül attól, hogy hol áll. Microsoft Teams leegyszerűsíti a együttműködési próbálkozások egy helyen. A kettő együtt integrálása, az azt jelenti, hogy a felhasználók hatékony legyen a teammates minden helyzetben lehetnek. 

A felhasználók felhőalapú alkalmazások hozzáadása a saját csoportjai csatornák [lapok használatával](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), de mi a helyzet a SharePoint-webhelyek vagy a tervezési eszközzel, amelyek helyben tárolt? Alkalmazásproxy használata a megoldás. Azokat hozzáadják a proxyn keresztül történő a csatornák a azonos külső URL-címekkel mindig használata alkalmazások távoli eléréséhez a közzétett alkalmazásokhoz. És mivel alkalmazásproxy Azure Active Directory használatával hitelesíti magát, a felhasználók egy egyszeri bejelentkezéses felhasználói élmény biztosítása.


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Az alkalmazásproxy-összekötő telepítése és az alkalmazás közzététele

Ha még nem tette, [-Proxy konfigurálása a bérlő számára, és az összekötő telepítéséhez](application-proxy-enable.md). Ezt követően [a helyszíni alkalmazások közzététele](application-proxy-publish-azure-portal.md) táveléréshez. Az alkalmazás közzétételekor most jegyezze fel a a külső URL-cím, mert az alkalmazás hozzáadása csoportokhoz szolgál.

Ha már rendelkezik a közzétett alkalmazásokhoz, de nem emlékszik a külső URL-címeit, keresse meg azokat a [Azure-portálon](https://portal.azure.com). Jelentkezzen be, majd navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás** > Válassza ki az alkalmazást > **alkalmazásproxy**.

## <a name="add-your-app-to-teams"></a>Az alkalmazás hozzáadása csoportokhoz

Ha az alkalmazás-proxyn keresztül történő közzétételéhez tudassa a felhasználókkal, arról, hogy azok is hozzáadható egy lapon közvetlenül a csapatok csatornák rendszerhez, és majd az alkalmazás érhető el a csoport használata mindenki számára. Kövesse a fenti három lépést rendelkezik:

1. Keresse meg a csoportok csatorna, vegye fel ezt az alkalmazást, illetve megadhatja, ahová **+** hozzáadása egy lapon.

   ![Válassza a lap](./media/application-proxy-integrate-with-teams/add-tab.png)

2. Válassza ki **webhely** az a beállítások lapon.

   ![Webhely hozzáadása](./media/application-proxy-integrate-with-teams/website.png)

3. Nevezze el a lapot, és állítsa be az URL-címet az alkalmazásproxy külső URL-címet. 

   ![Lap neve és az URL-cím konfigurálása](./media/application-proxy-integrate-with-teams/tab-name-url.png)

A csoport egyik tagjának hozzáadja a lapon, ha azt jeleníti meg a csatorna mindenki számára. Az alkalmazáshoz hozzáféréssel rendelkező felhasználók egyszeri bejelentkezéses hozzáférést a Microsoft Teams használt hitelesítő adatokkal beolvasása. A felhasználók nem férnek hozzá az alkalmazás a csapatok lapján látható, de le vannak tiltva, amíg nem adja meg őket engedélyeket a helyszíni alkalmazásra és az alkalmazás az Azure portálon közzétett verziója. 

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [tegye közzé a helyszíni SharePoint helyeket](application-proxy-integrate-with-sharepoint-server.md) az alkalmazásproxy.
- Az alkalmazások használatára konfigurálja [egyéni tartományok](application-proxy-configure-custom-domain.md) a külső URL-címhez. 
