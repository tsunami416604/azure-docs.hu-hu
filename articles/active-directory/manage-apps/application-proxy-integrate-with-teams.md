---
title: Access Azure AD App Proxy alkalmazások teams | Microsoft dokumentumok
description: Az Azure AD alkalmazásproxy használatával a Microsoft Teamsen keresztül érheti el a helyszíni alkalmazást.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807761"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>A helyszíni alkalmazások elérése a Microsoft Teamsen keresztül

Az Azure Active Directory alkalmazásproxy egyszeri bejelentkezést biztosít a helyszíni alkalmazásokba, függetlenül attól, hogy hol tartózkodik. A Microsoft Teams egy helyen egyszerűsíti az együttműködésen alapuló erőfeszítéseket. A kettő együttes integrálása azt jelenti, hogy a felhasználók bármilyen helyzetben hatékonyan dolgozhatnak csapattársaikkal.

A felhasználók [lapok segítségével](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)adhatnak hozzá felhőalapú alkalmazásokat a Teams-csatornáikhoz, de mi a helyzet a helyszínen üzemeltetett SharePoint-webhelyekkel vagy tervezési eszközzel? Az alkalmazásproxy a megoldás. Az alkalmazásproxyn keresztül közzétett alkalmazásokat hozzáadhatják csatornáikhoz ugyanazokat a külső URL-címeket használva, amelyeket mindig az alkalmazásaik távoli eléréséhez használnak. És mivel az alkalmazásproxy az Azure Active Directoryn keresztül hitelesíti magát, a felhasználók egyszeri bejelentkezési élményt kapnak.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Az Alkalmazásproxy-összekötő telepítése és az alkalmazás közzététele

Ha még nem tette meg, [konfigurálja az alkalmazásproxyt a bérlőhöz, és telepítse az összekötőt.](application-proxy-add-on-premises-application.md) Ezután [tegye közzé a helyszíni alkalmazást](application-proxy-add-on-premises-application.md) a táveléréshez. Amikor közzéteszi az alkalmazást, jegyezze fel a külső URL-címet, mert az alkalmazás teamshez való hozzáadására szolgál.

Ha már közzétette az alkalmazásokat, de nem emlékszik a külső URL-címekre, keresse meg őket az [Azure Portalon.](https://portal.azure.com) Jelentkezzen be, majd keresse meg az **Azure Active Directory** > Enterprise**alkalmazásokat:** **Enterprise applications** > Minden alkalmazás > válassza ki az alkalmazás > **alkalmazásproxyját.**

## <a name="add-your-app-to-teams"></a>Alkalmazás hozzáadása a Teamshez

Miután közzétette az alkalmazást az alkalmazásproxyn keresztül, tudassa a felhasználókkal, hogy közvetlenül a Teams-csatornáikban adhatják hozzá lapként, és az alkalmazás a csapat minden tagja számára elérhető. Az alábbi három lépést kell követniük:

1. Nyissa meg azt a Teams-csatornát, **+** amelyhez hozzá szeretné adni az alkalmazást, és válassza ki a lap hozzáadását.

   ![Válassza a + lehetőséget, ha tabulátort szeretne hozzáadni a Teamsben](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Válassza a **Webhely** lehetőséget a lap beállításai között.

   ![Válassza ki a Webhely lehetőséget a Lap hozzáadása képernyőről](./media/application-proxy-integrate-with-teams/website.png)

1. Adjon nevet a lapnak, és állítsa be az URL-címet az alkalmazásproxy külső URL-címére.

   ![A lap elnevezése és a külső URL hozzáadása](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Amint egy csapat egyik tagja hozzáadja a lapot, az megjelenik a csatorna minden tagja számára. Az alkalmazáshoz hozzáféréssel rendelkező felhasználók egyszeri bejelentkezési hozzáférést kapnak a Microsoft Teamshez használt hitelesítő adatokkal. Azok a felhasználók, akik nem férnek hozzá az alkalmazáshoz, láthatják a Teams lapot, de le vannak tiltva, amíg nem ad nekik engedélyt a helyszíni alkalmazáshoz és az alkalmazás közzétett Azure Portal-verziójához.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogy [miként tehet közzé helyszíni SharePoint-webhelyeket](application-proxy-integrate-with-sharepoint-server.md) alkalmazásproxyval.
- Állítsa be az alkalmazásokat úgy, hogy [egyéni tartományokat](application-proxy-configure-custom-domain.md) használjanak a külső URL-címükhöz.
