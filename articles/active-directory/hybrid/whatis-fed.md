---
title: Mi az Azure AD összevonása? | Microsoft Docs
description: Az Összevonás az Azure AD-vel.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd7a5b186ebc98babe1baacc9f56f39dc8fbc093
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049376"
---
# <a name="what-is-federation-with-azure-ad"></a>Mi az Azure AD összevonása?

Az összevonás olyan tartományok gyűjteménye, amelyek bizalmi kapcsolatot hoztak létre. A megbízhatósági szint eltérő lehet, de általában magában foglalja a hitelesítést, és szinte mindig tartalmaz engedélyt. Egy tipikus összevonás tartalmazhat számos olyan szervezetet, amelyek bizalmi kapcsolatot létesítenek az erőforrások egy készletéhez való megosztott hozzáféréshez.

A helyszíni környezetben az Azure AD-vel összeegyeztetheti, és ezt az összevonást használhatja hitelesítéshez és engedélyezéshez.  Ez a bejelentkezési módszer biztosítja, hogy minden felhasználói hitelesítés a helyszínen történjen.  Ez a módszer lehetővé teszi a rendszergazdák számára, hogy szigorúbb hozzáférés-vezérlési szinteket valósítsanak meg. Összevonás a AD FS és PingFederate áll rendelkezésre.

![Összevont identitások](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Ha az összevont identitáskezelést az Active Directory összevonási szolgáltatásokkal (AD FS) együtt használja, dönthet úgy, hogy biztonsági tartalékként a jelszókivonat-szinkronizálást is konfigurálja, hogy lehessen mit használni, ha az AD FS-infrastruktúra leáll.


## <a name="next-steps"></a>Következő lépések

- [Mi az a hibrid identitás?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md)
- [Mi az a jelszókivonat-szinkronizálás?](whatis-phs.md)
- [Mi az összevonás?](whatis-fed.md)
- [Mi az egyszeri bejelentkezés?](how-to-connect-sso.md)
- [Az összevonás működése](how-to-connect-fed-whatis.md)
- [Összevonás a PingFederate-tel](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
