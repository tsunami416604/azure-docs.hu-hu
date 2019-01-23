---
title: Jogcímbarát alkalmazások – az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Hogyan tehet közzé helyi fogadja el az AD FS-jogcímek biztonságos távoli hozzáférést a felhasználók által az ASP.NET-alkalmazások.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 6313bc8f3245966d22e904f96a90674b27d396f7
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472928"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Az alkalmazásproxy jogcímbarát alkalmazások használata
[Jogcímbarát alkalmazások](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) hajtsa végre egy átirányítási a biztonsági jogkivonat szolgáltatás (STS). Az STS engedélyezések egy jogkivonatot a felhasználó kéri a hitelesítő adatokat, és ezután átirányítja a felhasználót az alkalmazáshoz. Néhány módon engedélyezze az alkalmazásproxyt ezek átirányítások dolgozhat. Ez a cikk segítségével konfigurálhatja a központi telepítés, jogcímeket használó alkalmazásokhoz. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy az STS-re, a jogcímeket figyelembe vevő alkalmazás átirányítja a felhasználót a helyi hálózaton kívüli elérhető. Elérhetővé teheti az STS verzióinformációk proxyn keresztül, vagy külső kapcsolatok engedélyezése. 

## <a name="publish-your-application"></a>Az alkalmazás közzététele

1. Közzéteheti az alkalmazását a szakaszban ismertetett utasításoknak megfelelően [alkalmazásait közzéteheti az alkalmazásproxy](application-proxy-add-on-premises-application.md).
2. Nyissa meg az alkalmazás oldalát a portálon, válassza a **egyszeri bejelentkezési**.
3. Ha úgy döntött **Azure Active Directory** , a **előhitelesítést metódus**válassza **az Azure AD egyszeri bejelentkezés le van tiltva** , a **belső Hitelesítési módszer**. Ha úgy döntött **csatlakoztatott** , a **előhitelesítést metódus**, nem kell bármin változtatni.

## <a name="configure-adfs"></a>ADFS konfigurálása

Konfigurálhatja az AD FS jogcímeket figyelembe vevő alkalmazásokkal való használatával két módszer egyikével. Az első az egyéni tartományok használatával. A második pedig a WS-Federation. 

### <a name="option-1-custom-domains"></a>Option 1: Egyéni tartományok

Ha a belső URL-címeket az alkalmazások összes teljesen minősített tartománynevek (FQDN), majd konfigurálhatja [egyéni tartományok](application-proxy-configure-custom-domain.md) alkalmazásai számára. Az egyéni tartományok használatával hozzon létre a külső URL-címek, ugyanazok, mint a belső URL-címeket. A külső URL-címek a belső URL-címek egyeznek, majd az STS-átirányítások működik-e a felhasználók a helyi vagy távoli is. 

### <a name="option-2-ws-federation"></a>Option 2: WS-Federation

1. Nyissa meg az AD FS kezelése.
2. Lépjen a **függő entitás Megbízhatóságai**, kattintson a jobb gombbal az alkalmazásproxy használatával tesz közzé az alkalmazást, és válassza a **tulajdonságok**.  

   ![Függő entitás Megbízhatóságai kattintson a jobb gombbal az alkalmazás neve – képernyőkép](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Az a **végpontok** lap **típusú végpont**válassza **WS-Federation**.
4. A **megbízható URL-cím**, adja meg a Proxy alatt megadott URL **külső URL-cím** kattintson **OK**.  

   ![Adja hozzá a végpont - állítsa be a megbízható URL-érték – képernyőkép](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>További lépések
* [Az egyszeri bejelentkezés engedélyezése](configure-single-sign-on-portal.md) , amelyek nem jogcímbarát alkalmazások
* [Együttműködhet a proxy-alkalmazások natív ügyfélalkalmazások engedélyezése](application-proxy-configure-native-client-application.md)


