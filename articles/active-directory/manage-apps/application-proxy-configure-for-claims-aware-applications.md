---
title: Jogcímbarát alkalmazások – Azure AD alkalmazásproxy | Microsoft dokumentumok
description: A helyszíni ASP.NET alkalmazások közzététele, amelyek elfogadják az ADFS-jogcímeket a felhasználók biztonságos táveléréséhez.
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
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbc5c356ea5a542fdc12b11aff236c56b146b3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68477240"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Jogcímbarát alkalmazások használata az Alkalmazásproxyban
[A jogcímbarát alkalmazások](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) átirányítást hajtanak végre a biztonsági jogkivonat-szolgáltatás (STS) szolgáltatásra. Az STS hitelesítő adatokat kér a felhasználótól egy jogkivonatért cserébe, majd átirányítja a felhasználót az alkalmazásba. Többféleképpen is engedélyezheti, hogy az alkalmazásproxy együttműködjön ezekkel az átirányításokkal. Ebben a cikkben konfigurálhatja a telepítést a jogcímbarát alkalmazásokhoz. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a jogcímbarát alkalmazás átirányítása a helyszíni hálózaton kívül érhető el. Az STS-t elérhetővé teheti, ha proxyn keresztül teszi elérhetővé, vagy külső kapcsolatokat engedélyez. 

## <a name="publish-your-application"></a>Az alkalmazás közzététele

1. Tegye közzé az alkalmazást az [Alkalmazások közzététele alkalmazásproxyval](application-proxy-add-on-premises-application.md)című könyvben leírtaknak megfelelően.
2. Nyissa meg az alkalmazás lapját a portálon, és válassza **az Egyszeri bejelentkezés**lehetőséget.
3. Ha az **Azure Active Directoryt** választotta **előhitelesítési módszerként,** válassza az Azure **AD egyszeri bejelentkezésletiltva** **belső hitelesítési módszerként**lehetőséget. Ha **az Áterdei átszámítási** módszert választotta **előhitelesítési módszerként,** semmit sem kell módosítania.

## <a name="configure-adfs"></a>A DFS konfigurálása

Az ADFS-t kétféleképpen konfigurálhatja jogcímbarát alkalmazásokhoz. Az első az egyéni tartományok használatával. A második a WS-Federation-nél van. 

### <a name="option-1-custom-domains"></a>1. lehetőség: Egyéni tartományok

Ha az alkalmazások összes belső URL-címe teljesen minősített tartománynév (FQDN), akkor [egyéni tartományokat](application-proxy-configure-custom-domain.md) konfigurálhat az alkalmazásokhoz. Az egyéni tartományok segítségével hozzon létre olyan külső URL-címeket, amelyek megegyeznek a belső URL-címekkel. Ha a külső URL-címek megfelelnek a belső URL-ek, majd az STS átirányítások munka, hogy a felhasználók a helyszíni vagy távoli. 

### <a name="option-2-ws-federation"></a>2. lehetőség: WS-Federation

1. Nyissa meg az ADFS-kezelést.
2. Nyissa meg **a Függő entitás megbízhatósági beállításait,** kattintson a jobb gombbal az alkalmazásproxyval közzékívánt alkalmazásra, és válassza a **Tulajdonságok parancsot.**  

   ![A függő entitás megbízhatóságai a jobb gombbal az alkalmazás nevére kattintanak - képernyőkép](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. A **Végpontok** lap **Végponttípusa csoportjában**válassza a **WS-Összevonás**lehetőséget.
4. A **Megbízható URL csoportban**adja meg az alkalmazásproxyban megadott URL-címet a **Külső URL csoportban,** majd kattintson az **OK**gombra.  

   ![Végpont hozzáadása - megbízható URL-érték beállítása - képernyőkép](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>További lépések
* [Egyszeri bejelentkezés engedélyezése](configure-single-sign-on-non-gallery-applications.md) olyan alkalmazásokhoz, amelyek nem jogcímbarát
* [A natív ügyfélalkalmazások proxyalkalmazásokkal való interakciójának engedélyezése](application-proxy-configure-native-client-application.md)


