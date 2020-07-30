---
title: Jogcímbarát alkalmazások – Azure AD alkalmazás proxy | Microsoft Docs
description: Helyszíni ASP.NET-alkalmazások közzététele, amelyek elfogadják az ADFS jogcímeit a felhasználók biztonságos távoli eléréséhez.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97def0608251f65aa222e13760877d4287135d08
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386979"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Jogcímbarát alkalmazások használata az Application proxyban
A [jogcímbarát alkalmazások](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) átirányítást hajtanak végre a biztonsági jogkivonat szolgáltatásban (STS). Az STS hitelesítő adatokat kér a felhasználótól egy jogkivonat számára, majd átirányítja a felhasználót az alkalmazáshoz. Az alkalmazásproxy használatának néhány módja van az ilyen átirányításokkal való együttműködéshez. Ez a cikk a jogcímbarát alkalmazások központi telepítésének konfigurálására használható. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a jogcímbarát alkalmazás által átirányított STS elérhető a helyszíni hálózaton kívülről. Az STS elérhetővé tételét proxyn keresztül vagy a külső kapcsolatok engedélyezésével teheti meg. 

## <a name="publish-your-application"></a>Az alkalmazás közzététele

1. Tegye közzé az alkalmazást az Application [proxyval történő alkalmazások közzététele](application-proxy-add-on-premises-application.md)című részben leírt utasítások szerint.
2. Navigáljon az alkalmazás lapra a portálon, és válassza az **egyszeri bejelentkezés**lehetőséget.
3. Ha a **Azure Active Directory** az **előhitelesítési módszerként**választotta, akkor a **belső hitelesítési módszerként**válassza az **Azure ad egyszeri bejelentkezés letiltva** lehetőséget. Ha a **továbbítót** választotta **előhitelesítési módszerként**, nem kell semmit módosítania.

## <a name="configure-adfs"></a>Az ADFS konfigurálása

Az ADFS-t kétféleképpen is konfigurálhatja a jogcímbarát alkalmazásokhoz. Az első az egyéni tartományok használata. A második a WS-Federation. 

### <a name="option-1-custom-domains"></a>1. lehetőség: egyéni tartományok

Ha az alkalmazásokhoz tartozó összes belső URL-cím teljes tartománynevet (FQDN) használ, akkor [Egyéni tartományokat](application-proxy-configure-custom-domain.md) konfigurálhat az alkalmazásaihoz. Az egyéni tartományok használatával olyan külső URL-címeket hozhat létre, amelyek megegyeznek a belső URL-címekkel. Ha a külső URL-címek megfelelnek a belső URL-címeknek, az STS-átirányítások akkor működnek, ha a felhasználók helyszíni vagy távoliak. 

### <a name="option-2-ws-federation"></a>2. lehetőség: WS-Federation

1. Nyissa meg az ADFS-kezelőt.
2. Nyissa meg a **függő entitás megbízhatóságait**, kattintson a jobb gombbal az alkalmazásproxy által közzétett alkalmazásra, majd válassza a **Tulajdonságok**lehetőséget.  

   ![Függő entitás Megbízhatóságai – kattintson a jobb gombbal az alkalmazás nevére – képernyőkép](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. A **végpontok** lap **végpont típusa**területén válassza a **WS-Federation**elemet.
4. A **megbízható URL**-cím területen adja meg a **külső URL-cím** alatt megadott alkalmazásproxy URL-címét, majd kattintson **az OK**gombra.  

   ![Egy végponthoz beállított megbízható URL-érték hozzáadása – képernyőfelvétel](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Következő lépések
* [Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz](application-proxy-configure-native-client-application.md)


