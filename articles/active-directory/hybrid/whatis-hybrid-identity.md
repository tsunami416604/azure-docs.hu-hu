---
title: Az Active Directory csatlakoztatása az Azure Active Directoryhoz | Microsoft Docs
description: Az Azure AD Connect integrálja a helyszíni címtárakat az Azure Active Directoryval. Így közös identitást biztosíthat az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz.
keywords: az Azure AD Connect bemutatása, az Azure AD Connect áttekintése, mi az Azure AD Connect, az Active Directory telepítése
services: active-directory
author: billmath
manager: mtillman
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1e85ab5eb0d8c96fb4c90332a2fbc41d216369f7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098967"
---
# <a name="what-is-hybrid-identity"></a>Mi az a hibrid identitás? 

Ma cégek és vállalatok egy egyre több és több helyszíni vegyesen program, és a felhőbeli alkalmazásokhoz.  Felhasználók igényelnek, ezeknek az alkalmazásoknak hozzáférést a helyszínen és a felhőben. Ez a követelmény kihívást a forgatókönyv elérhetetlenné válik. 

A Microsoft identitáskezelési megoldások kiterjednek a helyszíni és felhőalapú képességek.  Ezeket a megoldásokat hozzon létre egy általános felhasználói identitást, hitelesítés és engedélyezés az összes erőforráshoz, helytől függetlenül. Erre **hibrid identitás**.

Hibrid identitás eléréséhez három hitelesítési módszerek közül az használható, attól függően, a forgatókönyvekhez.   A következők: 

- **[A Jelszókivonat-szinkronizálás (nál)](whatis-phs.md)**  
- **[Az átmenő hitelesítés (ESP)](how-to-connect-pta.md)**  
- **[Összevonási](whatis-fed.md)** 

Ezek a hitelesítési módszerek is biztosítanak [egyszeri bejelentkezéses](how-to-connect-sso.md) képességeket.  Egyszeri bejelentkezéses automatikusan bejelentkezik a felhasználók mikor legyenek a vállalati eszközeiket a vállalati hálózathoz csatlakozik.

További információkért lásd: [válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok 

Szeretnénk bemutatni néhány gyakori hibrid identitás- és hozzáféréskezelési helyzetet és a hozzájuk ajánlott hibrid identitáskezelési lehetősége(ke)t. 

|Cél:|PHS és SSO<sup>1</sup>| PTA és SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|A helyszíni Active Directoryban létrehozott új felhasználói, kapcsolattartói és csoportfiókok automatikus szinkronizálása a felhőbe|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A bérlő beállítása hibrid Office 365-forgatókönyvekhez|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A felhőszolgáltatásokba való bejelentkezés engedélyezése a felhasználók helyszíni jelszavaival|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A vállalati hitelesítő adatokat használó egyszeri bejelentkezés implementálása|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|  
|Gondoskodás arról, hogy a jelszókivonatok ne tárolódjanak a felhőben| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Felhőbeli többtényezős hitelesítési megoldások engedélyezése| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Helyszíni többtényezős hitelesítési megoldások engedélyezése| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Intelligens kártyával végzett hitelesítés támogatása a felhasználók számára<sup>4</sup>| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Jelszólejárati értesítések megjelenítése az Office portálon és a Windows 10 asztali verziójában| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Jelszókivonat-szinkronizálás egyszeri bejelentkezéssel. 
> 
> <sup>2</sup> Átmenő hitelesítés és egyszeri bejelentkezés.  
> 
> <sup>3</sup> Összevont egyszeri bejelentkezés AD FS-sel.  
>  
> <sup>4</sup> Az AD FS a vállalati nyilvános kulcsú infrastruktúrával is integrálható, hogy tanúsítványokkal is be lehessen jelentkezni. Ezek a tanúsítványok lehetnek megbízható regisztrációs csatornákon (például MDM-en vagy csoportházirend-objektumon) keresztül kiosztott szoftveres tanúsítványok, intelligenskártya-tanúsítványok (beleértve a PIV/CAC-kártyákat) vagy Vállalati Windows Hello- (tanúsítvány-megbízhatósági) megoldások. Az intelligens kártyás hitelesítés támogatásával kapcsolatos további információkat [ebben a blogban](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/) talál. 
> 

## <a name="next-steps"></a>További lépések 

- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md) 
- [Mi az a Jelszókivonat-szinkronizálás (nál)?](whatis-phs.md) 
- [Mi az átmenő hitelesítés (ESP)?](how-to-connect-pta.md) 
- [Mi az összevonás?](whatis-fed.md) 
- [Egyszeri bejelentkezés a Mi?](how-to-connect-sso.md) 

