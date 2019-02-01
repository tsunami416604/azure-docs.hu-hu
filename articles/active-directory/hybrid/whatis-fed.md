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
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 0374db9ae207d3d9c713e6f1a2e2a25c96505b74
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490413"
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
