---
title: "Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – hogyan működik |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Active Directory zökkenőmentes egyszeri bejelentkezés funkció működése."
services: active-directory
keywords: "Mi az Azure AD Connect telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 9d17a4038f2171b74c8ba1dbc21e8335e6893691
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: technikai részletes bemutatója

Ez a cikk lehetőséget ad olyan technikai részleteket be az Azure Active Directory zökkenőmentes egyszeri bejelentkezést (zökkenőmentes SSO) funkció működése.

## <a name="how-does-seamless-sso-work"></a>Hogyan működik a zökkenőmentes SSO?

Ez a szakasz a következő három részből hozzá:
1. A telepítő a zökkenőmentes SSO-funkció.
2. A tranzakció egy felhasználói bejelentkezés egy webböngészőben működése a zökkenőmentes egyszeri Bejelentkezést.
3. Egy tranzakció egyetlen felhasználó bejelentkezési natív ügyfél működése a zökkenőmentes egyszeri Bejelentkezést.

### <a name="how-does-set-up-work"></a>Hogyan állítsa be a munkahelyi?

Zökkenőmentes SSO engedélyezve van az Azure AD Connect használatával, ahogy [Itt](active-directory-aadconnect-sso-quick-start.md). Engedélyezze a szolgáltatást, a következő lépések következnek:
- Nevű számítógépfiók `AZUREADSSOACC` (amely jelöli az Azure AD) jön létre a helyszíni Active Directory (AD).
- A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
- Továbbá két Kerberos egyszerű szolgáltatásnév (SPN) két URL-címek az Azure AD-bejelentkezés során használt képviselő jönnek létre.

>[!NOTE]
> A számítógép fiókjának és a Kerberos SPN-ek minden Active Directory-erdőben szinkronizálja az Azure AD (Azure AD Connect használatával) és, amelynek felhasználói számára zökkenőmentes SSO kívánt jönnek létre. Helyezze át a `AZUREADSSOACC` számítógépfiókja számára egy szervezeti egységet (OU) más számítógépfiókokat tároló annak biztosítására, hogy ugyanúgy kezeli, és nem törlődik.

>[!IMPORTANT]
>Erősen ajánlott, hogy Ön [a Kerberos visszafejtési kulcs váltása](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) , a `AZUREADSSOACC` számítógépfiók legalább 30 nap.

Ha a telepítés befejeződött, a zökkenőmentes SSO működik ugyanúgy, mint bármely más bejelentkezés integrált Windows-hitelesítéssel (IWA) használó.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hogyan bejelentkezhet egy webböngészőben való zökkenőmentes SSO munkahelyi?

A bejelentkezési folyamat egy webböngészőben a következőképpen történik:

1. A felhasználó megpróbál hozzáférni egy webalkalmazást (például az Outlook Web App - https://outlook.office365.com/owa/) a tartományhoz csatlakoztatott vállalati eszköz a vállalati hálózaton belül.
2. Ha a felhasználó nem jelentkezett, a felhasználót a rendszer átirányítja az Azure AD bejelentkezési oldalára.
3. A felhasználó beírja az Azure AD bejelentkezési oldal a felhasználó nevét.

  >[!NOTE]
  >A [bizonyos alkalmazások](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), 2. és 3 kimarad.

4. A JavaScript használatával a háttérben, az Azure AD felkéri a böngésző keresztül 401-es jogosulatlan választ, adja meg a Kerberos jegyet.
5. A böngésző jegyet, az Active Directory kér a `AZUREADSSOACC` számítógépfiók (amely az Azure AD).
6. Az Active Directory megkeresi a számítógépfiók, és visszaadja a Kerberos jegyet a böngészőben a számítógépfiók titkos kulcs titkosított.
7. A böngészőben a Kerberos-jegy azért szerzett, az Active Directoryból az Azure AD továbbítja.
8. Az Azure AD visszafejti a Kerberos jegy, amely tartalmazza a felhasználó a vállalati eszköz be van jelentkezve a korábban megosztott kulcs használatával.
9. Kiértékelésével, olyan után az Azure AD vagy egy token visszaadja az alkalmazásnak vagy megkérdezi a felhasználót a kiegészítő igazolás, például a többtényezős hitelesítés végrehajtásához.
10. Ha a felhasználói bejelentkezés sikeres, a felhasználó nem tud hozzáférni az alkalmazáshoz.

A következő ábra összetevőit és a lépéseit mutatja be.

![Zökkenőmentes egyszeri bejelentkezési-webalkalmazás-alkalmazás létrehozási folyamata](./media/active-directory-aadconnect-sso/sso2.png)

Zökkenőmentes SSO alkalmi, ami azt jelenti, ha nem sikerül, a bejelentkezés során tapasztal élmény visszaáll a rendszeres viselkedését - Egytényezős, a felhasználó igényeinek meg kell adnia a jelszót a bejelentkezéshez.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hogyan bejelentkezhet a zökkenőmentes SSO munkahelyi rendelkező natív ügyfelet?

A bejelentkezési folyamat natív ügyfelet a következőképpen történik:

1. A felhasználó megpróbál hozzáférni egy natív alkalmazás (például az Outlook ügyfélben) a tartományhoz csatlakoztatott vállalati eszköz a vállalati hálózaton belül.
2. Ha a felhasználó már nem jelentkezett be, a natív alkalmazás lekéri annak a felhasználónak a felhasználóneve az eszköz Windows-munkamenet.
3. Az alkalmazás a felhasználónév küld az Azure AD, és lekéri a bérlő WS-Trust metaadatcsere közzétett végpontját.
4. Az alkalmazás ezután lekérdezi a WS-Trust MEX-végpont, ha az integrált hitelesítési végpont elérhető.
5. 4. lépés sikeres, ha egy Kerberos kérdés jelenik meg.
6. Ha az alkalmazás képes lekérni a Kerberos jegy, továbbítja, akár az Azure AD integrált hitelesítési végpontot.
7. Az Azure AD visszafejti a Kerberos jegy, majd érvényesíti azt.
8. Az Azure AD aláírja a felhasználónak, és egy SAML-jogkivonatot állít ki az alkalmazást.
9. Az alkalmazás majd elküldi a SAML-jogkivonat az Azure AD OAuth2 token végponthoz.
10. Az Azure AD érvényesíti a SAML-jogkivonatot, és egy hozzáférési jogkivonatot, és egy frissítési a megadott erőforrás, és egy azonosító jogkivonatot állít ki a az alkalmazás.
11. A felhasználó élvezheti az alkalmazás erőforrás elérésére.

A következő ábra összetevőit és a lépéseit mutatja be.

![Zökkenőmentes egyszeri bejelentkezés – natív alkalmazás létrehozási folyamata](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>További lépések

- [**Gyors üzembe helyezési** ](active-directory-aadconnect-sso-quick-start.md) - létrehozásához, és az Azure AD zökkenőmentes SSO futtatása.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-sso-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
