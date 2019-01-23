---
title: Mi az az Azure AD összevonási? | Microsoft Docs
description: Az Azure AD összevonási ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ac58992d81be8d3e275dc89a26ed4cb8b6a3242c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475968"
---
# <a name="what-is-federation-with-azure-ad"></a>Mi az az Azure AD összevonási?

Összevonási megbízhatósági álló tartományokban gyűjteménye. Megbízhatósági szintjét eltérőek lehetnek, de általában olyan hitelesítési, és szinte mindig az engedélyezéssel együtt. Egy tipikus összevonási tartalmazhatnak olyan szervezetek által létrehozott erőforráscsoport megosztott hozzáférés megbízhatósági száma.

A helyszíni környezet és az Azure AD összevonása, és ennek az összevonásnak használja a hitelesítéshez és engedélyezéshez.  A bejelentkezési módszer biztosítja, hogy a felhasználói hitelesítést a helyszíni rendszeren történik.  Ez a módszer lehetővé teszi a rendszergazdák szigorúbb szintű hozzáférés-vezérlés megvalósításához. Összevonás az AD FS és a PingFederate érhető el.

![Összevont identitások](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Ha az összevont identitáskezelést az Active Directory összevonási szolgáltatásokkal (AD FS) együtt használja, dönthet úgy, hogy biztonsági tartalékként a jelszókivonat-szinkronizálást is konfigurálja, hogy lehessen mit használni, ha az AD FS-infrastruktúra leáll.


## <a name="next-steps"></a>További lépések

- [Mi az a hibrid identitás?](whatis-phs.md)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az a Jelszókivonat-szinkronizálás?](whatis-phs.md)
- [Mi az összevonás?](whatis-fed.md)
- [Egyszeri bejelentkezés a Mi?](how-to-connect-sso.md)
- [Összevonási működése](how-to-connect-fed-whatis.md)
- [Összevonás a pingfederate-tel](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)