---
title: ISV-partneri katalógus a Azure AD B2Choz
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható az ISV-partnereinkkel a végfelhasználói élmény igényei szerint. A partneri hálózat kibővíti a megoldási képességeiket; az MFA, a biztonságos ügyfél-hitelesítés, a szerepköralapú Access Control engedélyezése; csalás elleni küzdelem a személyazonosság-ellenőrzés igazolásával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ab9a9785f2f995a6f8df766603e3fc3b9a894be5
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814227"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>ISV-partnerek Azure Active Directory B2C

Az ISV-partneri hálózat kibővíti a megoldási képességeiket a zökkenőmentes végfelhasználói élmények kiépítésének megkönnyítésére. A Azure AD B2C lehetővé teszi az ISV-partnerekkel való integrációt a többtényezős hitelesítési módszerek, a biztonságos ügyfél-hitelesítés (SCA) engedélyezése, a szerepköralapú Access Control végrehajtása, valamint a csalás elleni védelem a személyazonosság-ellenőrzés és a próbanyomat használatával. A részletes példákból megismerheti, hogyan integrálhatja az alkalmazásokat az alább felsorolt ISV-partnerekkel.

>[!NOTE]
>A [githubon található Azure Active Directory B2C közösségi webhely](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/) a Közösségtől származó egyéni szabályzatokat is biztosít.

## <a name="integration-isv-partners"></a>Integrációs ISV-partnerek

| ISV-partner | Leírás és integrációs útmutató  |
| :--- | :--- |
| ![embléma](./media/partner-gallery/arkose-logo.png) | A [Arkóz Labs](./partner-arkose-labs.md) egy csalások elleni megelőzési megoldás szolgáltatója, amely segítséget nyújt a szervezeteknek a robotok elleni támadásokkal, a fiókok átvételi támadásokkal és a csalárd fiókok megnyitásával
| ![embléma](./media/partner-gallery/experian-logo.png) | A [Experian](./partner-experian.md) egy személyazonosság-ellenőrző és ellenőrző szolgáltató, amely a felhasználói attribútumok alapján kockázatértékeléseket végez a csalások megelőzése érdekében.|
| ![embléma](./media/partner-gallery/idology-logo.png) | A [IDology](./partner-idology.md) egy azonosító-ellenőrzési megoldásokkal, csalás-megelőzési megoldásokkal, megfelelőségi megoldásokkal és egyebekkel rendelkező személyazonosság-ellenőrzési és-ellenőrző szolgáltató.|
| ![embléma](./media/partner-gallery/itsme-logo.png) | a [itsme](./partner-itsme.md) egy elektronikus azonosítási, hitelesítési és megbízhatósági szolgáltatás (eiDAS) által kompatibilis digitális azonosító megoldás, amely lehetővé teszi a felhasználók számára, hogy a kártyák olvasói, jelszavas, kétfaktoros hitelesítés és több PIN-kód nélkül is biztonságosan bejelentkezzenek. |
| ![embléma](./media/partner-gallery/jumio-logo.png) | A [Jumio](./partner-jumio.md) egy azonosító-ellenőrzési szolgáltatás, amely lehetővé teszi a valós idejű automatizált azonosítók ellenőrzését, így biztosítva az ügyféladatok védelmét. |
| ![embléma](./media/partner-gallery/lexisnexis-logo.png) | A [LexisNexis](./partner-lexisnexis.md) egy profilkészítési és identitás-ellenőrzési szolgáltató, amely ellenőrzi a felhasználó azonosítását, és átfogó kockázatértékelést biztosít a felhasználó eszköze alapján. |
| ![embléma](./media/partner-gallery/onfido-logo.png) | A [Onfido](./partner-onfido.md) egy dokumentum-azonosító és egy arc biometria-ellenőrzési megoldás, amely lehetővé teszi a vállalatok számára, hogy valós időben tudják kielégíteni *az ügyfél* és az identitás követelményeit.  |
| ![embléma](./media/partner-gallery/trusona-logo.png) | A [Trusona](./partner-trusona.md) -integráció segítségével biztonságosan bejelentkezhet, és engedélyezheti a jelszó nélküli hitelesítést, a többtényezős hitelesítést és a digitális licencek vizsgálatát.|
| ![embléma](./media/partner-gallery/twilio-logo.png) | A [Twilio ellenőrzi](./partner-twilio.md) , hogy az alkalmazás több megoldást biztosít a többtényezős hitelesítés (MFA) SMS egyszeri jelszó (OTP), az időalapú egyszeri jelszó (TOTP) és a leküldéses értesítések, valamint a Payment Services-irányelv 2 (PSD2) direktíva követelményeinek teljesítéséhez.|
| ![embléma](./media/partner-gallery/typingdna-logo.png) | A [TypingDNA](./partner-typingdna.md) egy, a felhasználó gépelési mintáján alapuló személyazonosság-ellenőrzési megoldás, amely a többtényezős hitelesítést kényszeríti, és segít megfelelni a 2. szintű Payment Services-irányelv (PSD2) SCA-követelményeinek. |
| ![embléma](./media/partner-gallery/whoiam-logo.png) | A [WhoIAM](./partner-whoiam.md) egy márkás identitáskezelésrendszer-(BRIMS-) alkalmazás, amely lehetővé teszi, hogy a szervezetek hang-, SMS-és e-mail-cím alapján ellenőrizzék a felhasználói bázist. 

## <a name="next-steps"></a>További lépések

Válasszon egy partnert a fenti táblázatban, hogy megtudja, hogyan integrálhatja megoldásait a Azure AD B2Cával.

További információ:

- [B2C közösségi webhely a GitHubon](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)
- [Egyéni szabályzatok Azure AD B2C](custom-policy-overview.md)
