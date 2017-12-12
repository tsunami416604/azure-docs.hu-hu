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
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0a28cd9016588d266670aa5a7fcbdd854d7ebce0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: technikai részletes bemutatója

Ez a cikk lehetőséget ad olyan technikai részleteket be az Azure Active Directory zökkenőmentes egyszeri bejelentkezést (zökkenőmentes SSO) funkció működése.

## <a name="how-does-seamless-sso-work"></a>Hogyan működik a zökkenőmentes SSO?

Ez a szakasz két részből áll rá:
1. A telepítő a zökkenőmentes SSO-funkció.
2. Egyetlen felhasználó bejelentkezési tranzakcióban működése a zökkenőmentes egyszeri Bejelentkezést.

### <a name="how-does-set-up-work"></a>Hogyan állítsa be a munkahelyi?

Zökkenőmentes SSO engedélyezve van az Azure AD Connect használatával, ahogy [Itt](active-directory-aadconnect-sso-quick-start.md). Engedélyezze a szolgáltatást, a következő lépések következnek:
- Nevű számítógépfiók `AZUREADSSOACC` (amely jelöli az Azure AD) jön létre a helyszíni Active Directory (AD).
- A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
- Továbbá két Kerberos egyszerű szolgáltatásnév (SPN) két URL-címek az Azure AD-bejelentkezés során használt képviselő jönnek létre.

>[!NOTE]
> A számítógép fiókjának és a Kerberos SPN-ek minden Active Directory-erdőben szinkronizálja az Azure AD (Azure AD Connect használatával) és, amelynek felhasználói számára zökkenőmentes SSO kívánt jönnek létre. Helyezze át a `AZUREADSSOACC` számítógépfiókja számára egy szervezeti egységet (OU) más számítógépfiókokat tároló annak biztosítására, hogy ugyanúgy kezeli, és nem törlődik.

>[!IMPORTANT]
>Erősen ajánlott, hogy Ön [a Kerberos visszafejtési kulcs váltása](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) , a `AZUREADSSOACC` számítógépfiók legalább 30 nap.

### <a name="how-does-sign-in-with-seamless-sso-work"></a>Hogyan jelentkezzen be munkahelyi zökkenőmentes SSO?

Ha a telepítés befejeződött, a zökkenőmentes SSO működik ugyanúgy, mint bármely más bejelentkezés integrált Windows-hitelesítéssel (IWA) használó. A folyamat a következőképpen történik:

1. A felhasználó megpróbál hozzáférni az alkalmazáshoz (például az Outlook Web App - https://outlook.office365.com/owa/) a tartományhoz csatlakoztatott vállalati eszköz a vállalati hálózaton belül.
2. Ha a felhasználó nem jelentkezett, a felhasználót a rendszer átirányítja az Azure AD bejelentkezési oldalára.

  >[!NOTE]
  >Ha az Azure AD bejelentkezési kérelemben egy `domain_hint` (azonosító a bérlő – például, contoso.onmicrosoft.com) vagy `login_hint` (például a felhasználó - azonosító user@contoso.onmicrosoft.com vagy user@contoso.com) paramétert, majd a 2. lépés kimarad.

3. A felhasználó beírja az Azure AD bejelentkezési oldal a felhasználó nevét.
4. A JavaScript használatával a háttérben, az Azure AD felkéri a böngésző keresztül 401-es jogosulatlan választ, adja meg a Kerberos jegyet.
5. A böngésző jegyet, az Active Directory kér a `AZUREADSSOACC` számítógépfiók (amely az Azure AD).
6. Az Active Directory megkeresi a számítógépfiók, és visszaadja a Kerberos jegyet a böngészőben a számítógépfiók titkos kulcs titkosított.
7. A böngésző továbbítja a Kerberos-jegy azért szerzett, az Active Directoryból az Azure AD (az egyik a [korábban hozzáadott, a böngésző Intranet zóna beállításait az Azure AD URL](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature)).
8. Az Azure AD visszafejti a Kerberos jegy, amely tartalmazza a felhasználó a vállalati eszköz be van jelentkezve a korábban megosztott kulcs használatával.
9. Kiértékelésével, olyan után az Azure AD vagy egy token visszaadja az alkalmazásnak vagy megkérdezi a felhasználót a kiegészítő igazolás, például a többtényezős hitelesítés végrehajtásához.
10. Ha a felhasználói bejelentkezés sikeres, a felhasználó nem tud hozzáférni az alkalmazáshoz.

A következő ábra összetevőit és a lépéseit mutatja be.

![Zökkenőmentes egyszeri bejelentkezés](./media/active-directory-aadconnect-sso/sso2.png)

Zökkenőmentes SSO alkalmi, ami azt jelenti, ha nem sikerül, a bejelentkezés során tapasztal élmény visszaáll a rendszeres viselkedését - Egytényezős, a felhasználó igényeinek meg kell adnia a jelszót a bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések

- [**Gyors üzembe helyezési** ](active-directory-aadconnect-sso-quick-start.md) - létrehozásához, és az Azure AD zökkenőmentes SSO futtatása.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-sso-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
