---
title: Mi a hibrid identitás az Azure Active Directoryval?
description: A hibrid identitás közös felhasználói identitással rendelkezik a hitelesítéshez és az engedélyezéshez a helyszíni és a felhőben egyaránt.
keywords: az Azure AD Connect bemutatása, az Azure AD Connect áttekintése, mi az Azure AD Connect, az Active Directory telepítése
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d681dd06f07f6174e31b59cccf42df5dc16a1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "68779853"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Mi a hibrid identitás az Azure Active Directoryval?

Napjainkban a vállalkozások és a vállalatok egyre inkább a helyszíni és a felhőalapú alkalmazások keverékévé válnak.  A felhasználóknak hozzáférést kell biztosítaniuk ezekhez az alkalmazásokhoz a helyszínen és a felhőben egyaránt. A felhasználók kezelése mind a helyszíni, mind a felhőben kihívást jelentő forgatókönyveket jelent. 

A Microsoft identitáskezelési megoldásai a helyszíni és a felhőalapú képességekre is kiterjednek.  Ezek a megoldások közös felhasználói identitást hoznak létre a hitelesítéshez és az engedélyezéshez az összes erőforráshoz, helytől függetlenül. Ezt hibrid **identitásnak hívjuk.**

Az Azure AD és a hibrid identitáskezelés hibrid identitásának használatával ezek a forgatókönyvek lehetővé válnak.

Az Azure AD-vel való hibrid identitás eléréséhez a forgatókönyvektől függően három hitelesítési módszer egyike is használható.   A három módszer a következő: 

- **[Jelszókivonat-szinkronizálás (PHS)](whatis-phs.md)**  
- **[Átmenő hitelesítés (PTA)](how-to-connect-pta.md)**  
- **[Összevonás (AD FS)](whatis-fed.md)** 

Ezek a hitelesítési módszerek [egyszeri bejelentkezési](how-to-connect-sso.md) képességeket is biztosítanak.  Az egyszeri bejelentkezés automatikusan bejelentkezik a felhasználókba, amikor a vállalati eszközökön vannak, és csatlakoznak a vállalati hálózathoz.

További információt [az Azure Active Directory hibrid identitáskezelési megoldásának megfelelő hitelesítési módszer kiválasztása](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)című témakörben talál. 

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok 

Szeretnénk bemutatni néhány gyakori hibrid identitás- és hozzáféréskezelési helyzetet és a hozzájuk ajánlott hibrid identitáskezelési lehetősége(ke)t. 

|Cél:|PHS és SSO<sup>1</sup>| PTA és SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|A helyszíni Active Directoryban létrehozott új felhasználói, kapcsolattartói és csoportfiókok automatikus szinkronizálása a felhőbe|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A bérlő beállítása az Office 365 hibrid forgatókönyveihez.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Engedélyezze a felhasználók nak, hogy bejelentkezhessenek és hozzáférjenek a felhőszolgáltatásokhoz a helyszíni jelszavuk használatával.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Egyszeri bejelentkezés megvalósítása vállalati hitelesítő adatok használatával.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|  
|Győződjön meg arról, hogy a felhőnem tárolja a jelszókimondottakat.| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Felhőalapú többtényezős hitelesítési megoldások engedélyezése.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A helyszíni többtényezős hitelesítési megoldások engedélyezése.| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Intelligenskártyás hitelesítés támogatása a felhasználók számára. <sup>4.</sup>| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Jelszólejárati értesítések megjelenítése az Office Portalportálon és a Windows 10 asztalán.| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Jelszókivonat-szinkronizálás egyszeri bejelentkezéssel. 
> 
> <sup>2</sup> Átmenő hitelesítés és egyszeri bejelentkezés.  
> 
> <sup>3</sup> Összevont egyszeri bejelentkezés AD FS-sel.  
>  
> <sup>4</sup> Az AD FS a vállalati nyilvános kulcsú infrastruktúrával is integrálható, hogy tanúsítványokkal is be lehessen jelentkezni. Ezek a tanúsítványok lehetnek megbízható regisztrációs csatornákon (például MDM-en vagy csoportházirend-objektumon) keresztül kiosztott szoftveres tanúsítványok, intelligenskártya-tanúsítványok (beleértve a PIV/CAC-kártyákat) vagy Vállalati Windows Hello- (tanúsítvány-megbízhatósági) megoldások. Az intelligens kártyás hitelesítés támogatásával kapcsolatos további információkat [ebben a blogban](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/) talál. 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Az Azure AD Connect használatának licenckövetelményei

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md) 
- [Mi a jelszókivonat-szinkronizálás (PHS)?](whatis-phs.md) 
- [Mi az átmenő hitelesítés (PTA)?](how-to-connect-pta.md) 
- [Mi az összevonás?](whatis-fed.md) 
- [Mi az egyszeri bejelentkezés?](how-to-connect-sso.md) 

