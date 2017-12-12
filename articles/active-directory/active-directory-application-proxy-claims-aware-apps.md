---
title: "Jogcímbarát alkalmazások – az Azure AD alkalmazás Proxy |} Microsoft Docs"
description: "Fogadja el az AD FS-jogcímek az biztonságos távoli hozzáférést a felhasználók által a helyszínen ASP.NET alkalmazások közzétételének módját."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: harshja
ms.assetid: 91e6211b-fe6a-42c6-bdb3-1fff0312db15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.openlocfilehash: 4546f194e570282d331dde98cab98dd2f0981b72
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Az alkalmazásproxy jogcímbarát alkalmazásokkal való munka
[Jogcímbarát alkalmazások](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) végre átirányítás a biztonsági jogkivonat szolgáltatás (STS). Az STS cserébe jogkivonat a felhasználó kéri a hitelesítő adatokat, és ezután átirányítja a felhasználót az alkalmazáshoz. Néhány módon történő együttműködésre ezek átirányítások alkalmazásproxy engedélyezése. Ez a cikk segítségével konfigurálhatja a jogcímbarát alkalmazások központi telepítését. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy az STS, amely átirányítja a jogcímbarát alkalmazáshoz érhető el a helyszíni hálózaton kívülről. Elérhetővé teheti az STS megjelenítése révén olyan proxyn keresztül, vagy külső kapcsolatok engedélyezése. 

## <a name="publish-your-application"></a>Az alkalmazás közzététele

1. Az ismertetett utasításoknak megfelelően az alkalmazás közzétételére [alkalmazások közzétételére az alkalmazásproxy](application-proxy-publish-azure-portal.md).
2. Nyissa meg az alkalmazás oldalát a portálon, és válasszon **egyszeri bejelentkezés**.
3. Ha úgy döntött, hogy **Azure Active Directory** , a **előhitelesítési módszer**, jelölje be **az Azure AD az egyszeri bejelentkezés le van tiltva** , a **belső Hitelesítési módszer**. Ha úgy döntött, hogy **csatlakoztatott** , a **előhitelesítési módszer**, nem kell bármit módosíthat.

## <a name="configure-adfs"></a>AD FS konfigurálása

Az AD FS a jogcímbarát alkalmazások az alábbi két módszer egyikével állíthatja be. Az egyik egyéni tartományok használatával. A második pedig a WS-Federation. 

### <a name="option-1-custom-domains"></a>1. lehetőség: Egyéni tartományok

Ha minden belső URL-címéből az alkalmazások teljes tartománynevek (FQDN), majd konfigurálhatja [egyéni tartományok](active-directory-application-proxy-custom-domains.md) az alkalmazások számára. Az egyéni tartományok segítségével hozzon létre a külső URL-címek, amelyek ugyanaz, mint a belső URL-címeket. Ha a külső URL-címek felel meg a belső URL-címeket, akkor az STS-átirányítások munkahelyi, hogy a felhasználók is a helyszíni vagy távoli. 

### <a name="option-2-ws-federation"></a>2. lehetőség: WS-Federation

1. Nyissa meg az AD FS kezelése.
2. Ugrás a **függő entitás Megbízhatóságai**, kattintson a jobb gombbal az alkalmazásproxy közzétett alkalmazás, és válassza a **tulajdonságok**.  

   ![Függő entitás Megbízhatóságai kattintson a jobb gombbal az alkalmazás neve – képernyőkép](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  

3. Az a **végpontok** lap **típusú végpont**, jelölje be **WS-Federation**.
4. A **megbízható URL-cím**, az URL-címét az alkalmazásproxy alatt megadott **külső URL-cím** kattintson **OK**.  

   ![Adja hozzá a végpont - állítsa be a megbízható URL-cím érték – képernyőkép](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Következő lépések
* [Egyszeri bejelentkezés engedélyezése a](application-proxy-sso-overview.md) , amelyek nem jogcímbarát alkalmazásokhoz
* [Proxy alkalmazások együttműködhet natív ügyfél alkalmazások engedélyezése](active-directory-application-proxy-native-client.md)


