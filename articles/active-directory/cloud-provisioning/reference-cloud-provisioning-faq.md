---
title: Azure AD Connect felhőalapú jogosultságkiosztás – gyakori kérdések
description: Ez a dokumentum ismerteti a felhőkiépítéshez gyakran feltett kérdéseket.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77916574"
---
# <a name="azure-active-directory-connect-faq"></a>Az Azure Active Directory csatlakozásról – gyakori kérdések

Az Azure Active Directory (Azure AD) Connect felhőalapú kiépítéssel kapcsolatos gyakori kérdésekről olvashat.

## <a name="general-installation"></a>Általános telepítés

**K: Milyen gyakran fut a felhőkiépítés?**

A felhőkiépítés a tervek szerint 2 percenként fut. Minden 2 perc, minden felhasználó, csoport és jelszó kivonat változások lesznek kiépítve az Azure AD.Every 2 mins, any user, group and password hash changes will be provisioned to Azure AD.

**K: A jelszókivonat-szinkronizálási hibák látása az első futtatáskor. miért?**

Ez a várható eredmény. A hibák oka, hogy a felhasználói objektum nem jelenik meg az Azure AD-ben. Miután a felhasználó ki van építve az Azure AD-be, jelszókimondottak kell kiépítése a következő futtatásban. Várjon néhány futtatásra, és győződjön meg arról, hogy a jelszókivonat-szinkronizálás már nem tartalmazza a hibákat.

**K: Mi történik, ha az Active Directory-példány olyan attribútumokkal rendelkezik, amelyeket a felhőbeli kikötés nem támogat (például címtárbővítmények)?**

A felhőkiépítés futni fog, és a támogatott attribútumok kiépítése. A nem támogatott attribútumok nem lesznek kiépítve az Azure AD-be. Tekintse át a címtárbővítményeket az Active Directoryban, és győződjön meg arról, hogy nincs szüksége ezekre az attribútumokra az Azure AD-be való áramláshoz. Ha egy vagy több attribútumra van szükség, fontolja meg az Azure AD Connect szinkronizálásának használatát, vagy a szükséges adatok áthelyezése a támogatott attribútumok egyikére (például az 1-15-ös bővítményattribútumok).

**K: Mi a különbség az Azure AD Connect szinkronizálása és a felhőkiépítés között?**

Az Azure AD Connect szinkronizálása, kiépítése fut a helyszíni szinkronizálási kiszolgálón. A konfiguráció a helyszíni szinkronizálási kiszolgálón tárolódik. Az Azure AD Connect felhőkiépítés, a kiépítési konfiguráció a felhőben tárolódik, és fut a felhőben az Azure AD-kiépítési szolgáltatás részeként. 

**K: Használhatom a felhőkiépítést több Active Directory-erdőből való szinkronizáláshoz?**

Igen. A felhőkiépítés több Active Directory-erdőből történő szinkronizálásra használható. A többerdős környezetben az összes hivatkozásnak (például a kezelőnek) a tartományon belül kell lennie.  

**K: Hogyan történik az ügynök frissítése?**

Az ügyintézőket a Microsoft automatikusan frissíti. Az informatikai csapat számára ez csökkenti az új ügynökverziók tesztelésének és érvényesítésének terhét. 

**K: Letudom tiltani az automatikus frissítést?**

Az automatikus frissítés letiltására nincs támogatott mód.

**K: Módosíthatom a felhőkiépítés forráshorgonyát?**

Alapértelmezés szerint a felhőkiépítés ms-ds-consistency-GUID-ot használ, és az ObjectGUID-re való tartalék forráshorgonyként tartalékot használ. A forráshorgony módosítása nem támogatott.

**K: A felhőkiépítés használatakor az AD tartománynévvel rendelkező új szolgáltatásnevek jelennek meg. Ez várható?**

Igen, a felhőkiépítés létrehoz egy egyszerű szolgáltatás a létesítési konfiguráció a tartománynév, mint a szolgáltatás egyszerű név. Ne módosítsa a szolgáltatásegyszerű konfigurációt.

**K: Mi történik, ha egy szinkronizált felhasználónak módosítania kell a jelszót a következő bejelentkezéskor?**

Ha a jelszókivonat-szinkronizálás engedélyezve van a felhőkiépítésben, és a szinkronizált felhasználónak módosítania kell a jelszót a következő bejelentkezéskor a helyszíni AD-ben, a felhőalapú kiépítés nem építi ki a módosítandó jelszókivonatot az Azure AD-be. Miután a felhasználó módosítja a jelszót, a felhasználói jelszó kivonat a D-ből az Azure AD-be.

**K: A felhőkiépítés támogatja az ms-ds-consistencyGUID visszaírását bármely objektumhoz?**

Nem, a felhőkiépítés nem támogatja az ms-ds-consistencyGUID visszaírását bármely objektumhoz (beleértve a felhasználói objektumokat is). 

**K: A felhasználók kiépítése a felhőkiépítés használatával. Töröltem a konfigurációt. Miért jelennek meg továbbra is a régi szinkronizált objektumok az Azure AD-ben?** 

A konfiguráció törlésekor a felhőkiépítés nem törli a szinkronizált objektumokat az Azure AD-ben. Annak érdekében, hogy ne legyenek a régi objektumok, módosítsa a konfiguráció hatókörét üres csoportra vagy szervezeti egységekre. Miután a kiépítés fut, és törli az objektumokat, tiltsa le és törölje a konfigurációt. 

**K: Mit jelent, hogy az Exchange hibrid nem támogatott?**

Az Exchange hibrid telepítés lehetővé teszi, hogy az Exchange postafiókok a helyszínen és az Office 365-ben egy időben létezzenek. Az Azure AD Connect visszaszinkronizálja az attribútumok egy adott halmazát az Azure AD szolgáltatásból a helyszíni címtárba.  A felhőkiépítési ügynök jelenleg nem szinkronizálja ezeket az attribútumokat a helyszíni címtárba, és így nem támogatott az Azure AD Connect helyettesítésére.

**K: Telepíthetem a felhőkiépítési ügynököt a Windows Server Core rendszerre?**

Nem, az ügynök telepítése a kiszolgálómagra nem támogatott.

## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
