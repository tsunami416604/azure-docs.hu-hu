---
title: Mi a jelszókivonat-szinkronizálás az Azure AD-vel? | Microsoft Docs
description: A jelszókivonat-szinkronizálás ismertetése.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83e172e61411c7c1c098706b5ff4566f565d6bf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "66253860"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Mi a jelszókivonat-szinkronizálás az Azure AD-vel?
A jelszókivonat-szinkronizálás a hibrid identitások megvalósításához használt bejelentkezési módszerek egyike. Az Azure AD Connect szinkronizálja a felhasználói jelszót egy helyszíni Active Directory-példányból egy felhőalapú Azure AD-példányból származó kivonatot a kivonatból.

A jelszókivonat-szinkronizálás az Azure AD Connect sync által megvalósított címtár-szinkronizálási szolgáltatás kiterjesztése. Ezzel a funkcióval bejelentkezhet az Azure AD-szolgáltatásokba, például az Office 365-be. A szolgáltatásba ugyanazzal a jelszóval jelentkezik be, mint amelyet a helyszíni Active Directory-példányba való bejelentkezéshez használ.

![Mi az az Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

A jelszókivonat-szinkronizálás segít a jelszavak számának csökkentésében, a felhasználóknak csak egy jelre kell fenntartaniuk. A jelszókivonat-szinkronizálás a következőket teheti:

* A felhasználók termelékenységének növelése.
* Csökkentse ügyfélszolgálati költségeit.  

Szükség esetén beállíthatja a jelszókivonat-szinkronizálást biztonsági másolatként, ha úgy dönt, hogy bejelentkezési módszerként az [Összevonást az Active Directory összevonási szolgáltatásokkal (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) használja.

A jelszókivonat-szinkronizálás környezetben való használatához a következőket kell alkalmaznia:

* Telepítse az Azure AD Connectet.  
* Konfigurálja a címtár-szinkronizálást a helyszíni Active Directory-példány és az Azure Active Directory-példány között.
* Engedélyezze a jelszókivonat-szinkronizálást.



További információ: [Mi a hibrid identitás?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Következő lépések

- [Mi az a hibrid identitás?](whatis-hybrid-identity.md)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az átmenő hitelesítés (PTA)?](how-to-connect-pta.md)
- [Mi az összevonás?](whatis-fed.md)
- [Mi az egyszeri bejelentkezés?](how-to-connect-sso.md)
- [A jelszókivonat-szinkronizálás működése](how-to-connect-password-hash-synchronization.md)
