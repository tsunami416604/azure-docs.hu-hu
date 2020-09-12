---
title: Azure AD Connect felhőalapú jogosultságkiosztás – gyakori kérdések
description: Ez a dokumentum a Felhőbeli üzembe helyezéssel kapcsolatos gyakori kérdéseket ismerteti.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 622d38e09f65d05d7cba7a34f30a070c27d3fd37
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658379"
---
# <a name="azure-active-directory-connect-cloud-provisioning-faq"></a>Azure Active Directory Connect Cloud kiépítés – gyakori kérdések

További információ az Azure Active Directory (Azure AD) felhőalapú kiépítés összekapcsolásával kapcsolatos gyakori kérdésekről.

## <a name="general-installation"></a>Általános telepítés

**K: milyen gyakran fut a Felhőbeli kiépítés?**

A Felhőbeli kiépítés ütemezve van, hogy 2 percenként fusson. 2 percenként minden felhasználó, csoport és jelszó kivonata az Azure AD-be lesz kiépítve.

**K: a jelszó-kivonatolási szinkronizálás hibáinak észlelése az első futtatáskor. miért?**

Ez a várható eredmény. A hibák oka, hogy a felhasználói objektum nem szerepel az Azure AD-ben. Ha a felhasználó az Azure AD-be lett kiépítve, a jelszó-kivonatokat a következő futtatáskor kell kiépíteni. Várjon néhány futtatást, és győződjön meg arról, hogy a jelszó-kivonat szinkronizálása már nem rendelkezik a hibákkal.

**K: mi történik, ha a Active Directory-példány olyan attribútumokkal rendelkezik, amelyeket nem támogat a felhőalapú kiépítés (például a címtár-bővítmények)?**

A Felhőbeli kiépítés futtatja és kiépíti a támogatott attribútumokat. A nem támogatott attribútumok nem lesznek kiépítve az Azure AD-be. Tekintse át a Active Directory Directory-bővítményeit, és győződjön meg arról, hogy nincs szüksége ezekre az attribútumokra az Azure AD-ba való adatforgalomhoz Ha egy vagy több attribútumra van szükség, érdemes lehet Azure AD Connect szinkronizálást használni, vagy áthelyezni a szükséges információkat a támogatott attribútumok egyikére (például a 1-15-es bővítmény-attribútumokra).

**K: mi a különbség a Azure AD Connect szinkronizálás és a felhő kiépítés között?**

A Azure AD Connect Sync szolgáltatással a kiépítés a helyszíni szinkronizáló kiszolgálón fut. A konfigurációt a helyszíni szinkronizáló kiszolgáló tárolja. Azure AD Connect felhőalapú kiépítés esetén a létesítési konfiguráció a felhőben tárolódik, és a felhőben fut az Azure AD kiépítési szolgáltatás részeként. 

**K: használhatok Felhőbeli kiépítés több Active Directory erdőből való szinkronizálásra?**

Igen. A Felhőbeli kiépítés több Active Directory erdőből való szinkronizálásra is használható. A többerdős környezetben az összes referenciának (például a vezetőnek) a tartományon belül kell lennie.  

**K: Hogyan frissült az ügynök?**

Az ügynököket a Microsoft automatikusan frissíti. Az informatikai csapat számára ez csökkenti az új ügynökök verzióinak tesztelésére és érvényesítésére vonatkozó terheket. 

**K: le tudom tiltani az automatikus frissítést?**

Nincs támogatott mód az automatikus frissítés letiltására.

**K: módosíthatom a felhőalapú kiépítés forrás-horgonyát?**

Alapértelmezés szerint a Felhőbeli kiépítés az MS-DS-konzisztencia-GUID azonosítót használja tartalékként a ObjectGUID. Nincs támogatott mód a forrás-horgony módosítására.

**K: a Felhőbeli kiépítés használatakor az Active Directory-tartománynév (ek) ben új egyszerű szolgáltatásokat látok. Várható?**

Igen, a Felhőbeli kiépítés létrehoz egy egyszerű szolgáltatásnevet a kiépítési konfigurációhoz, amely a tartománynevet adja meg az egyszerű szolgáltatásnév neveként. Ne módosítsa az egyszerű szolgáltatásnév konfigurációját.

**K: mi történik, ha szinkronizált felhasználóra van szükség a jelszó módosítására a következő bejelentkezéskor?**

Ha a jelszó-kivonat szinkronizálása engedélyezve van a Felhőbeli kiépítésben, és a szinkronizált felhasználó számára szükséges a jelszó módosítása a helyszíni AD-ben a következő bejelentkezéskor, a Felhőbeli kiépítés nem teszi elérhetővé az Azure AD-hoz a "módosítható" jelszó-kivonatot. Ha a felhasználó megváltoztatja a jelszót, a rendszer az AD-ből az Azure AD-be kiépíti a felhasználói jelszó kivonatát.

**K: a felhő-kiépítés támogatja az MS-DS-consistencyGUID visszaírási bármely objektum esetében?**

Nem, a Felhőbeli kiépítés nem támogatja az MS-DS-consistencyGUID visszaírási bármely objektumhoz (beleértve a felhasználói objektumokat is). 

**K: kiépítem a felhasználókat a felhőalapú kiépítés használatával. Törölte a konfigurációt. Miért látom tovább a régi szinkronizált objektumokat az Azure AD-ben?** 

A konfiguráció törlésekor a Felhőbeli kiépítés nem távolítja el automatikusan a szinkronizált objektumokat az Azure AD-ben. Annak biztosítása érdekében, hogy a régi objektumok ne legyenek, módosítsa a konfiguráció hatókörét egy üres csoportra vagy szervezeti egységre. Miután a kiépítés megtisztítja az objektumokat, tiltsa le és törölje a konfigurációt. 

**K: mit jelent az, hogy az Exchange Hybrid nem támogatott?**

Az Exchange hibrid üzembe helyezési funkciója lehetővé teszi az Exchange-postaládák együttes létezését helyszíni és Microsoft 365 is. Az Azure AD Connect visszaszinkronizálja az attribútumok egy adott halmazát az Azure AD szolgáltatásból a helyszíni címtárba.  A felhőalapú kiépítési ügynök jelenleg nem szinkronizálja ezeket az attribútumokat a helyszíni címtárba, így a Azure AD Connect helyett nem használható.

**K: telepíthetem a Cloud kiépítési ügynököt a Windows Server Core-ra?**

Nem, a Server Core-ügynök telepítése nem támogatott.

**K: használhatok átmeneti kiszolgálót a felhőalapú kiépítési ügynökkel?**

Nem, az átmeneti kiszolgálók nem támogatottak.

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
