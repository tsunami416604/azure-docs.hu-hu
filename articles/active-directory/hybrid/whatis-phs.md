---
title: Mi a jelszó-kivonatoló szinkronizálás az Azure AD-vel? | Microsoft Docs
description: Leírja a jelszó-kivonatok szinkronizálását.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66253860"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Mi a jelszó-kivonatoló szinkronizálás az Azure AD-vel?
A jelszó-kivonatolás szinkronizálása a hibrid identitások megvalósításához használt bejelentkezési módszerek egyike. Azure AD Connect szinkronizálja egy helyi Active Directory-példány kivonatát, a kivonatot egy felhőalapú Azure AD-példányra.

A jelszó-kivonatolási szinkronizálás a Azure AD Connect Sync által megvalósított címtár-szinkronizálási szolgáltatás kiterjesztése. Ezzel a szolgáltatással jelentkezhet be az Azure AD-szolgáltatásokba, például az Office 365-be. A szolgáltatásba való bejelentkezéshez ugyanazt a jelszót használja, amelyet a helyszíni Active Directory-példányba való bejelentkezéshez használ.

![Mi az az Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

A jelszó-kivonatolás szinkronizálása a jelszavak számának csökkentésével segíti a felhasználókat, hogy csak eggyel maradjanak. A jelszó-kivonatolás szinkronizálása a következőket teheti:

* Javítsa a felhasználók termelékenységét.
* Csökkentse ügyfélszolgálati költségeit.  

Szükség esetén beállíthatja a jelszó-kivonat szinkronizálását biztonsági másolatként, ha úgy dönt, hogy a bejelentkezési módszerként [Active Directory összevonási szolgáltatások (AD FS) (AD FS) összevonást](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) használ.

A jelszó-kivonatoló szinkronizálás a környezetben való használatához a következőket kell tennie:

* Telepítse a Azure AD Connect.  
* Konfigurálja a címtár-szinkronizálást a helyszíni Active Directory-példány és a Azure Active Directory-példány között.
* Jelszó-kivonat szinkronizálásának engedélyezése.



További információ: [Mi az a Hybrid Identity?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Következő lépések

- [Mi az a hibrid identitás?](whatis-hybrid-identity.md)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az átmenő hitelesítés (PTA)?](how-to-connect-pta.md)
- [Mi az összevonás?](whatis-fed.md)
- [Mi az az egyszeri bejelentkezés?](how-to-connect-sso.md)
- [A jelszó-kivonatolási szinkronizálás működése](how-to-connect-password-hash-synchronization.md)
