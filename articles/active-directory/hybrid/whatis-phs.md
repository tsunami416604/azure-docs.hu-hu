---
title: Mi az Azure AD-vel a Jelszókivonat-szinkronizálás? | Microsoft Docs
description: A Jelszókivonat-szinkronizálás ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: d5c0ce69b1c4dfdf5f688ae672f75350b6d1fddd
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490362"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Mi az Azure AD-vel a Jelszókivonat-szinkronizálás?
A Jelszókivonat-szinkronizálás a hibrid identitás elvégzéséhez szükséges bejelentkezési módszerek egyike. Az Azure AD Connect szinkronizálja a kivonat, a kivonat, a jelszó egy helyszíni Active Directory-példányból egy felhőalapú Azure AD-példányt.

A Jelszókivonat-szinkronizálás az a címtár szinkronizálási szolgáltatás által az Azure AD Connect-szinkronizálás megvalósítása a kiterjesztése. Ez a funkció segítségével jelentkezzen be az Azure AD szolgáltatások, például az Office 365-höz. Jelentkezzen be a szolgáltatás használatával jelentkezzen be a helyszíni Active Directory-példányra ugyanazzal a jelszóval.

![Mi az az Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

A Jelszókivonat-szinkronizálás lehetővé a jelszavak, csak egyetlen fenntartása érdekében a felhasználók számára szükséges számának csökkentését. A Jelszókivonat-szinkronizálás a következőket teheti:

* A felhasználók termelékenységének javítása.
* Csökkenti az ügyfélszolgálati kiadásokat.  

Igény szerint beállíthatja a Jelszókivonat-szinkronizálás biztonsági Ha úgy dönt, hogy használja [az Active Directory összevonási szolgáltatások (AD FS) összevonási](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) bejelentkezési módszerként.

Jelszókivonat-szinkronizálást használ a környezetében, meg kell:

* Az Azure AD Connect telepítése.  
* Konfigurálja a címtár-szinkronizálás a helyszíni Active Directory-példányból, és az Azure Active Directory-példány között.
* Jelszókivonat-szinkronizálás engedélyezése.



További információkért lásd: [Mi a hibrid identitás?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>További lépések

- [Mi az a hibrid identitás?](whatis-phs.md)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az átmenő hitelesítés (ESP)?](how-to-connect-pta.md)
- [Mi az összevonás?](whatis-fed.md)
- [Egyszeri bejelentkezés a Mi?](how-to-connect-sso.md)
- [A Jelszókivonat-szinkronizálás működése](how-to-connect-password-hash-synchronization.md)
