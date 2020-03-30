---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés - Hogyan működik | Microsoft dokumentumok'
description: Ez a cikk ismerteti, hogyan működik az Azure Active Directory zökkenőmentes egyszeri bejelentkezési szolgáltatás.
services: active-directory
keywords: mi az Azure AD Connect, telepítse az Active Directoryt, szükséges összetevőket az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71176672"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Technikai mélymerülés

Ez a cikk az Azure Active Directory zökkenőmentes egyszeri bejelentkezés (Seamless SSO) szolgáltatás működésének technikai részleteit tartalmazza.

## <a name="how-does-seamless-sso-work"></a>Hogyan működik a zökkenőmentes egyszeri bejelentkezés?

Ez a rész három részből áll:

1. A zökkenőmentes egyszeri bejelentkezés funkció beállítása.
2. Hogyan működik egy egyetlen felhasználó bejelentkezési tranzakció a webböngészőben a zökkenőmentes egyszeri bejelentkezéssel?
3. Hogyan működik egy egyfelhasználós bejelentkezési tranzakció egy natív ügyfélen a zökkenőmentes egyszeri bejelentkezéssel?

### <a name="how-does-set-up-work"></a>Hogyan működik a beállítás?

A zökkenőmentes egyszeri bejelentkezés engedélyezve van az Azure AD Connect használatával, ahogy [itt](how-to-connect-sso-quick-start.md)látható. A szolgáltatás engedélyezése közben a következő lépések lépnek be:

- A számítógépfiók`AZUREADSSOACC`( ) jön létre a helyszíni Active Directory (AD) minden AD-erdőben, amely szinkronizálja az Azure AD-vel (az Azure AD Connect használatával).
- Emellett számos Kerberos szolgáltatás egyszerű nevek (SPNs) jönnek létre az Azure AD bejelentkezési folyamat során használható.
- A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan van megosztva az Azure AD-vel. Ha több AD-erdő van, minden számítógépfiók nak saját egyedi Kerberos-visszafejtési kulcsa lesz.

>[!IMPORTANT]
> A `AZUREADSSOACC` számítógépfiókot biztonsági okokból erősen védeni kell. Csak a tartományi rendszergazdák kezelhetik a számítógépfiókot. Győződjön meg arról, hogy a Kerberos-delegálás le van tiltva a számítógépfiókon, és hogy az Active Directory ban egyetlen más fiók sem rendelkezik delegálási engedéllyel a `AZUREADSSOACC` számítógépfiókon.. A számítógépfiókot egy szervezeti egységben (OU) tárolja, ahol biztonságban vannak a véletlen törléstől, és ahol csak a tartománygazdák rendelkeznek hozzáféréssel. A számítógépfiók Kerberos-visszafejtési kulcsát is bizalmasként kell kezelni. Javasoljuk, hogy legalább 30 naponta gördülje `AZUREADSSOACC` át a számítógépfiók [Kerberos-visszafejtési kulcsát.](how-to-connect-sso-faq.md)

A beállítás befejezése után a zökkenőmentes egyszeri bejelentkezés ugyanúgy működik, mint bármely más, integrált Windows-hitelesítést (IWA) használó bejelentkezés.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Hogyan működik a zökkenőmentes egyszeri bejelentkezéssel rendelkező webböngészőkbe való bejelentkezés?

A webböngésző bejelentkezési folyamata a következő:

1. A felhasználó megpróbál hozzáférni egy webalkalmazáshoz (például https://outlook.office365.com/owa/) az Outlook Web Apphoz – a vállalati hálózaton belüli tartományhoz csatlakozó vállalati eszközről.
2. Ha a felhasználó még nincs bejelentkezve, a felhasználó átirányítja az Azure AD bejelentkezési lapra.
3. A felhasználó írja be a felhasználónevét az Azure AD bejelentkezési lap.

   >[!NOTE]
   >[Bizonyos alkalmazások](./how-to-connect-sso-faq.md)esetén a 2 & 3.

4. JavaScript használatával a háttérben, az Azure AD kihívást jelent a böngésző, egy 401 jogosulatlan válasz, kerberos jegy biztosításához.
5. A böngésző viszont jegyet kér az Active `AZUREADSSOACC` Directorytól a számítógépfiókhoz (amely az Azure AD-t jelöli).
6. Az Active Directory megkeresi a számítógépfiókot, és kerberos-jegyet ad vissza a számítógépfiók titkával titkosított böngészőnek.
7. A böngésző továbbítja az Active Directoryból beszerzett Kerberos-jegyet az Azure AD-nek.
8. Az Azure AD visszafejti a Kerberos-jegyet, amely tartalmazza a vállalati eszközre bejelentkezett felhasználó identitását a korábban megosztott kulcs használatával.
9. Kiértékelés után az Azure AD vagy visszaad egy jogkivonatot az alkalmazásnak, vagy további igazolások, például a többtényezős hitelesítés végrehajtására kéri a felhasználót.
10. Ha a felhasználó bejelentkezése sikeres, a felhasználó hozzáférhet az alkalmazáshoz.

Az alábbi ábra az összes összetevőt és a szükséges lépéseket mutatja be.

![Zökkenőmentes egyszeri bejelentkezés – webalkalmazás-folyamat](./media/how-to-connect-sso-how-it-works/sso2.png)

A zökkenőmentes egyszeri bejelentkezés opportunista, ami azt jelenti, hogy ha nem sikerül, a bejelentkezési élmény visszaáll a szokásos viselkedéséhez – azaz a felhasználónak meg kell adnia a jelszavát a bejelentkezéshez.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Hogyan működik a bejelentkezés egy natív ügyfél zökkenőmentes egyszeri bejelentkezéssel?

A bejelentkezési folyamat egy natív ügyfélen a következő:

1. A felhasználó megpróbál hozzáférni egy natív alkalmazáshoz (például az Outlook-ügyfélhez) a vállalati hálózaton belüli tartományhoz csatlakozó vállalati eszközről.
2. Ha a felhasználó még nincs bejelentkezve, a natív alkalmazás lekéri a felhasználó felhasználónevét az eszköz Windows-munkamenetéből.
3. Az alkalmazás elküldi a felhasználónevet az Azure AD-nek, és lekéri a bérlő WS-Trust MEX végpontját. Ezt a WS-Trust-végpontot kizárólag a zökkenőmentes egyszeri bejelentkezés szolgáltatás használja, és nem a WS-Trust protokoll általános megvalósítása az Azure AD-n.
4. Az alkalmazás ezután lekérdezi a WS-Trust MEX végpontot, hogy lássa, elérhető-e integrált hitelesítési végpont. Az integrált hitelesítési végpontot kizárólag a zökkenőmentes egyszeri bejelentkezés szolgáltatás használja.
5. Ha a 4.
6. Ha az alkalmazás képes lekérni a Kerberos-jegyet, továbbítja azt az Azure AD integrált hitelesítési végpontjára.
7. Az Azure AD visszafejti a Kerberos-jegyet, és érvényesíti azt.
8. Az Azure AD bejelentkezik a felhasználó, és kiad egy SAML-jogkivonatot az alkalmazásnak.
9. Az alkalmazás ezután elküldi az SAML-jogkivonatot az Azure AD OAuth2 token végpontjára.
10. Az Azure AD érvényesíti az SAML-jogkivonatot, és kiad egy hozzáférési jogkivonatot és egy frissítési jogkivonatot az alkalmazásnak a megadott erőforráshoz, és egy id jogkivonatot.
11. A felhasználó hozzáférést kap az alkalmazás erőforrásához.

Az alábbi ábra az összes összetevőt és a szükséges lépéseket mutatja be.

![Zökkenőmentes egyszeri bejelentkezés – natív alkalmazásfolyamat](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>További lépések

- [**Gyors indítás**](how-to-connect-sso-quick-start.md) – Az Azure AD seamless sso üzembe helyezett szolgáltatása.
- [**Gyakori kérdések**](how-to-connect-sso-faq.md) – Válaszok a gyakran ismételt kérdésekre.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - A bejelentés új funkció kérelmeket.
