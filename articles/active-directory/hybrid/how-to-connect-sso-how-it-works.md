---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – hogyan működik | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan működik a Azure Active Directory zökkenőmentes egyszeri bejelentkezés funkció.
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176672"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Technikai Deep Dive

Ez a cikk technikai részleteket tartalmaz a Azure Active Directory zökkenőmentes egyszeri bejelentkezés (SSO) funkció működésének módjáról.

## <a name="how-does-seamless-sso-work"></a>Hogyan működik a zökkenőmentes egyszeri bejelentkezés?

Ez a szakasz három részből áll:

1. A zökkenőmentes egyszeri bejelentkezés funkciójának beállítása.
2. Egy felhasználói bejelentkezési tranzakció egy webböngészőben zökkenőmentes egyszeri bejelentkezéssel működik.
3. Egy felhasználói bejelentkezési tranzakció egy natív ügyfélen zökkenőmentes egyszeri bejelentkezéssel működik.

### <a name="how-does-set-up-work"></a>Hogyan működik a beállítás?

A zökkenőmentes egyszeri bejelentkezés a Azure AD Connect használatával engedélyezhető az [itt](how-to-connect-sso-quick-start.md)látható módon. A szolgáltatás engedélyezése közben a következő lépések történnek:

- A rendszer létrehoz egy`AZUREADSSOACC`számítógépfiókot () a helyszíni Active Directoryban (ad) az Azure ad-vel szinkronizált összes ad-erdőben (Azure ad Connect használatával).
- Emellett számos Kerberos egyszerű szolgáltatásnevet (SPN-t) hoz létre az Azure AD bejelentkezési folyamata során.
- A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan van megosztva az Azure AD-vel. Ha több AD-erdő van, minden számítógépfiók saját, egyedi Kerberos-visszafejtési kulccsal fog rendelkezni.

>[!IMPORTANT]
> Biztonsági `AZUREADSSOACC` okokból szigorúan védeni kell a számítógépfiókot. Csak a Tartománygazdák kezelhetik a számítógépfiókot. Győződjön meg `AZUREADSSOACC` arról, hogy a számítógépfiók Kerberos-delegálása le van tiltva, és nincs más fiók a Active Directory delegálási engedélyekkel rendelkezik a számítógépfiók számára. Tárolja a számítógépfiókot a szervezeti egységben (OU), ahol biztonságos a véletlen törléstől, és csak a Tartománygazdák férhetnek hozzá. A számítógép fiókjában a Kerberos-visszafejtési kulcsot is bizalmasként kell kezelni. Javasoljuk, hogy legalább 30 naponként átadja a `AZUREADSSOACC` számítógépfiók Kerberos- [visszafejtési kulcsát](how-to-connect-sso-faq.md) .

A beállítás befejezése után a zökkenőmentes SSO ugyanúgy működik, mint bármely más, integrált Windows-hitelesítést (IWA) használó bejelentkezés.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hogyan történik a bejelentkezés zökkenőmentes egyszeri bejelentkezést igénylő webböngészőn?

A bejelentkezés a böngészőben a következőképpen történik:

1. A felhasználó egy webalkalmazáshoz (például az Outlook Web https://outlook.office365.com/owa/) alkalmazáshoz) próbál hozzáférni egy, a vállalati hálózaton belüli, tartományhoz csatlakoztatott vállalati eszközről.
2. Ha a felhasználó még nincs bejelentkezve, a rendszer átirányítja a felhasználót az Azure AD bejelentkezési oldalára.
3. A felhasználó felhasználónevét az Azure AD bejelentkezési oldalára írja be.

   >[!NOTE]
   >[Bizonyos alkalmazások](./how-to-connect-sso-faq.md)esetén a 2. & 3. lépés kimarad.

4. A JavaScript használata a háttérben az Azure AD egy 401 jogosulatlan válaszon keresztül vitatja meg a böngészőt a Kerberos-jegy biztosításához.
5. A böngészőben viszont egy jegyet kér Active Directorytól a számítógépfiók számára (amely `AZUREADSSOACC` az Azure ad-t jelöli).
6. Active Directory megkeresi a számítógépfiókot, és a számítógép fiókjának titkával titkosított Kerberos-jegyet ad vissza a böngészőnek.
7. A böngésző továbbítja a Active Directory által az Azure AD-be beszerzett Kerberos-jegyet.
8. Az Azure AD visszafejti a Kerberos-jegyet, amely magában foglalja a vállalati eszközre bejelentkezett felhasználó identitását a korábban megosztott kulccsal.
9. Az értékelés után az Azure AD visszaadja a tokent az alkalmazásnak, vagy megkéri a felhasználót, hogy végezzen további bizonyítékokat, például a Multi-Factor Authentication.
10. Ha a felhasználói bejelentkezés sikeres, a felhasználó hozzáférhet az alkalmazáshoz.

A következő ábra az összes összetevőt és a benne foglalt lépéseket mutatja be.

![Zökkenőmentes egyszeri bejelentkezés – webalkalmazás-folyamat](./media/how-to-connect-sso-how-it-works/sso2.png)

A zökkenőmentes egyszeri bejelentkezés az opportunista, ami azt jelenti, hogy ha nem sikerül, a bejelentkezési folyamat visszatér a szokásos viselkedésére – azaz a felhasználónak be kell írnia a jelszavát a bejelentkezéshez.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hogyan működik a bejelentkezett natív ügyfélen a zökkenőmentes egyszeri bejelentkezés?

A natív ügyfél bejelentkezési folyamata a következő:

1. A felhasználó egy natív alkalmazást (például az Outlook-ügyfelet) próbál hozzáférni egy tartományhoz csatlakoztatott vállalati eszközről a vállalati hálózaton belül.
2. Ha a felhasználó még nem jelentkezett be, a natív alkalmazás lekéri a felhasználó felhasználónevét az eszköz Windows-munkamenetében.
3. Az alkalmazás elküldi a felhasználónevet az Azure AD-nek, és lekéri a bérlő WS-Trust MEX-végpontját. Ezt a WS-Trust végpontot kizárólag a zökkenőmentes SSO szolgáltatás használja, és nem a WS-Trust protokoll általános megvalósítása az Azure AD-ben.
4. Az alkalmazás ezután lekérdezi a WS-Trust MEX-végpontot, hogy ellenőrizze, hogy elérhető-e az integrált hitelesítési végpont. Az integrált hitelesítési végpontot kizárólag a zökkenőmentes egyszeri bejelentkezés funkció használja.
5. Ha a 4. lépés sikeres, a rendszer Kerberos-kihívást állít ki.
6. Ha az alkalmazás képes a Kerberos-jegy beolvasására, azt az Azure AD integrált hitelesítési végpontja továbbítja.
7. Az Azure AD visszafejti a Kerberos-jegyet, és érvényesíti azt.
8. Az Azure AD aláírja a felhasználót a alkalmazásban, és SAML-jogkivonatot bocsát ki az alkalmazásnak.
9. Az alkalmazás ezután elküldi az SAML-tokent az Azure AD OAuth2 token-végpontjának.
10. Az Azure AD érvényesíti az SAML-jogkivonatot, valamint a megadott erőforráshoz tartozó hozzáférési jogkivonatot és frissítési jogkivonatot, valamint egy azonosító jogkivonatot.
11. A felhasználó hozzáférést kap az alkalmazás erőforrásához.

A következő ábra az összes összetevőt és a benne foglalt lépéseket mutatja be.

![Zökkenőmentes egyszeri bejelentkezés – natív alkalmazás folyamata](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>További lépések

- [**Gyorskonfigurálás**](how-to-connect-sso-quick-start.md) – az Azure ad zökkenőmentes egyszeri bejelentkezésének megkezdése és futtatása.
- [**Gyakori kérdések**](how-to-connect-sso-faq.md) – válaszok a gyakori kérdésekre.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.
