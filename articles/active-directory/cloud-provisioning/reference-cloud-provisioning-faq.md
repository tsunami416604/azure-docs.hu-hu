---
title: Azure AD Connect Cloud kiépítés – gyakori kérdések
description: Ez a dokumentum a Felhőbeli üzembe helyezéssel kapcsolatos gyakori kérdéseket ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c88b167884c455ffb995f35356b121bce8a207
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793555"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – gyakori kérdések

További információ az Azure Active Directory (Azure AD) felhőalapú kiépítés összekapcsolásával kapcsolatos gyakori kérdésekről.

## <a name="general-installation"></a>Általános telepítés

**K: milyen gyakran fut a Felhőbeli kiépítés?**

A Felhőbeli kiépítés ütemezve van, hogy 2 percenként fusson. 2 percenként minden felhasználó, csoport és jelszó kivonata az Azure AD-be lesz kiépítve.

**K: a jelszó-kivonatolási szinkronizálás hibáinak észlelése az első futtatáskor. miért?**

Ez a várható eredmény. A hibák oka, hogy a felhasználói objektum nem szerepel az Azure AD-ben. Ha a felhasználó az Azure AD-be lett kiépítve, a jelszó-kivonatokat a következő futtatáskor kell kiépíteni. Várjon néhány futtatást, és győződjön meg arról, hogy a jelszó-kivonat szinkronizálása már nem rendelkezik a hibákkal.

**K: mi a különbség a Azure AD Connect szinkronizálás és a felhő kiépítés között?**

A Azure AD Connect Sync szolgáltatással a kiépítés a helyszíni szinkronizáló kiszolgálón fut. A konfigurációt a helyszíni szinkronizáló kiszolgáló tárolja. Azure AD Connect felhőalapú kiépítés esetén a létesítési konfiguráció a felhőben tárolódik, és a felhőben fut az Azure AD kiépítési szolgáltatás részeként. 

**K: használhatok Felhőbeli kiépítés több Active Directory erdőből való szinkronizálásra?**

Igen. A Felhőbeli kiépítés több Active Directory erdőből való szinkronizálásra is használható. A többerdős környezetben az összes referenciának (például a vezetőnek) a tartományon belül kell lennie.  

**K: Hogyan frissült az ügynök?**

Az ügynököket a Microsoft automatikusan frissíti. Ez csökkenti az új ügynökök verzióinak teszteléséhez és ellenőrzéséhez szükséges terheket. 

**K: le tudom tiltani az automatikus frissítést?**

Nincs támogatott mód az automatikus frissítés letiltására.

**K: módosíthatom a felhőalapú kiépítés forrás-horgonyát?**

Alapértelmezés szerint a Felhőbeli kiépítés az MS-DS-konzisztencia-GUID azonosítót használja tartalékként a ObjectGUID. Nincs támogatott mód a forrás-horgony módosítására.

**K: a Felhőbeli kiépítés használatakor az Active Directory-tartománynév (ek) ben új egyszerű szolgáltatásokat látok. Várható?**

Igen, a Felhőbeli kiépítés létrehoz egy egyszerű szolgáltatásnevet a kiépítési konfigurációhoz, amely a tartománynevet adja meg az egyszerű szolgáltatásnév neveként. Ne módosítsa az egyszerű szolgáltatásnév konfigurációját.

**K: mi történik, ha szinkronizált felhasználóra van szükség a jelszó módosítására a következő bejelentkezéskor?**

Ha a jelszó-kivonat szinkronizálása engedélyezve van a Felhőbeli kiépítésben, és a szinkronizált felhasználó számára szükséges a jelszó módosítása a helyszíni AD-ben a következő bejelentkezéskor, a Felhőbeli kiépítés nem építi ki a jelszót az Azure AD-be való módosításra. Ha a felhasználó megváltoztatja a jelszót, a rendszer az AD-ből az Azure AD-be kiépíti a felhasználói jelszó kivonatát.

**K: a felhő-kiépítés támogatja az MS-DS-consistencyGUID visszaírási bármely objektum esetében?**

Nem, a Felhőbeli kiépítés nem támogatja az MS-DS-consistencyGUID visszaírási bármely objektumhoz (beleértve a felhasználói objektumokat is). 

**K: kiépítem a felhasználókat a felhőalapú kiépítés használatával. Törölte a konfigurációt. Miért látom tovább a régi szinkronizált objektumokat az Azure AD-ben?** 

A konfiguráció törlésekor a Felhőbeli kiépítés nem törli a szinkronizált objektumokat az Azure AD-ben. Annak biztosítása érdekében, hogy a régi objektumok ne legyenek, módosítsa a konfiguráció hatókörét egy üres csoportra vagy szervezeti egységre. Miután a kiépítés megtisztítja az objektumokat, tiltsa le és törölje a konfigurációt. 

## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
