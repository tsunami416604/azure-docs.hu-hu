---
title: 'Azure AD Connect: Ha már rendelkezik az Azure ad-ben |} A Microsoft Docs'
description: Ez a témakör ismerteti, hogyan használja a csatlakozás, ha van meglévő Azure AD-bérlővel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6f0c16a86d4e3dbe582f1471d2b94e1ec85b70b5
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475919"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Ha már rendelkezik meglévő bérlővel
A témakörök használata az Azure AD Connect a legtöbb feltételezi, hogy először a egy új Azure AD-bérlővel, és hogy egyik felhasználó sem, vagy nincs más objektumokat. Ha már elindította az Azure AD-bérlővel, de fel kell töltenie azt a felhasználók és más objektumok, és most szeretné használni a csatlakozás, majd ez a témakör Önnek szól.

## <a name="the-basics"></a>Az alapok
Az Azure AD-objektum vagy egyedalakzatokkal, a felhőben (Azure AD) vagy a helyszínen. Egy egyetlen objektumhoz nem kezelheti a bizonyos attribútumait a helyszíni és néhány más attribútum az Azure ad-ben. Minden objektum rendelkezik, azt a jelzőt, amely azt jelzi, az objektum van kezelve.

Néhány helyi felhasználók és más kezelheti a felhőben. Egy gyakori forgatókönyv, ehhez a konfigurációhoz egy szervezet a munkavállalók fiókkezelési és értékesítési alkalmazottak vegyesen. A nyilvántartási feldolgozók rendelkezik egy helyszíni AD-fiókot, de az értékesítési feldolgozók nem, rendelkeznek fiókkal az Azure ad-ben. Bizonyos felhasználók a helyszíni és az Azure ad-ben egyes lenne kezelhető.

Ha használatba kezelése az Azure ad-ben is vannak a felhasználók a helyszíni AD és később szeretné használni Connect, akkor néhány további szempontok figyelembe kell vennie.

## <a name="sync-with-existing-users-in-azure-ad"></a>Az Azure ad-ben a meglévő felhasználók szinkronizálása
Az Azure AD Connectet telepíti, és szinkronizálásának elindítása, amikor az Azure AD sync szolgáltatást (az Azure AD) minden új objektum-ellenőrzés, és próbálja meg megkeresni a megfelelő objektum. Ez a folyamat használt három attribútum: **userPrincipalName**, **proxyAddresses**, és **sourceAnchor**/**immutableid azonosítója** . Egyeztetés **userPrincipalName** és **proxyAddresses** néven egy **helyreállítható egyezés**. Egyeztetés **sourceAnchor** néven **rögzített egyezés**. Az a **proxyAddresses** attribútum értékének csak **SMTP:**, vagyis az elsődleges e-mail-címét, a kiértékelés szolgál.

A match csak akkor történik meg a Connect webhelyről érkező új objektumok. Ha módosítja egy meglévő objektum így ezeket az attribútumokat, a megfelelő, majd hibaüzenet jelenik meg helyette.

Ha az Azure ad-ben talál egy objektumot, ahol az attribútumértékek ugyanazok a Connect webhelyről érkező objektumhoz és, amely már létezik, az Azure ad-ben, majd az Azure ad-objektum átvesz Connect. A korábban felhő által felügyelt objektum meg van jelölve, a helyszínen felügyelt. Az összes attribútum az Azure ad-ben a érték szerepel a helyszíni AD a helyszíni értékű felülíródnak. A kivétel akkor, ha az attribútumhoz egy **NULL** helyszíni értékét. Ebben az esetben az Azure ad-ben marad, de az érték csak továbbra is módosíthatja a helyszíni valami mást.

> [!WARNING]
> Mivel az Azure AD-attribútumok tervezi a helyszíni érték felül, ellenőrizze, hogy helyes adatokat a helyszíni. Például ha csak rendelkezik felügyelt Office 365 e-mail-címét, és nem tartott, frissítve a helyszíni Active Directory tartományi Szolgáltatásokban, majd minden olyan Azure AD és Office 365-ben az AD DS-ben nem található értékek elvesznek.

> [!IMPORTANT]
> Használhatja a jelszó-szinkronizálás gyorsbeállítások mindig használja, akkor a jelszót az Azure ad-ben felülírja a jelszót a helyszíni AD. Ha a felhasználók különböző jelszavakat kezelésére használhatók, majd szeretné tájékoztatja őket, hogy használják inkább a helyi jelszó Connect telepítését.

Az előző szakaszban, és figyelmeztetés kell tekinteni a tervezés. Ha sok módosítást végzett az Azure ad-ben, nem kerülnek időben a helyszíni Active Directory tartományi Szolgáltatásokban, akkor meg kell terveznie, hogy hogyan töltse fel a frissített értékeket tartalmazó Active Directory tartományi szolgáltatások, az objektumok az Azure AD Connect szinkronizálása előtt.

Ha, egyezik az objektum egy egyezéssel, akkor a **sourceAnchor** bekerül az objektum az Azure ad-ben, így nehéz egyezést később is használható.

>[!IMPORTANT]
> A Microsoft javasolja, a helyszíni fiókok szinkronizálása az Azure Active Directoryban már meglévő rendszergazdai fiókok ellen.

### <a name="hard-match-vs-soft-match"></a>Merevlemez-match egyezéssel vs
Csatlakozás az új telepítéshez nem nincs gyakorlati különbség egy helyreállítható - és merevlemez-match. A különbség az olyan vész-helyreállítási helyzet. Ha elvesztette a kiszolgáló és az Azure AD Connect, adatvesztés nélkül telepítheti egy új példányát. Csatlakozás a sourceAnchor egy objektumot küld kezdeti telepítése során. A match majd kiértékelése az ügyfél (Azure AD Connect), amely sokkal gyorsabb, mint ez ugyanaz az Azure ad-ben. Rögzített egyezést abban az esetben is Connect és az Azure AD által. Helyreállítható egyezést csak akkor történik meg az Azure AD által.

### <a name="other-objects-than-users"></a>Más objektumok, mint a felhasználók
Levelezési csoportok és a névjegyeket akkor is egyezéssel alapján proxyAddresses. Merevlemez-match nem alkalmazható, mivel csak akkor frissíthető a sourceAnchor/immutableid azonosítója (a PowerShell használatával), a felhasználók csak. Nem a levelezési csoportok esetén nincs használata jelenleg nem támogatott egyezéssel vagy a merevlemez-match.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Hozzon létre egy új helyszíni Active Directory-adatokból az Azure ad-ben
Egyes ügyfeleink kezdődhet egy kizárólag felhőalapú megoldásának az Azure ad-vel, és nem rendelkeznek helyszíni AD. Később, szeretné használják a helyszíni erőforrásokhoz, és létre szeretné hozni a helyszíni AD az Azure AD-adatok alapján. Az Azure AD Connect nem tud segítséget ehhez a forgatókönyvhöz. Ez nem hoz létre a helyszíni felhasználók, és nem tartalmaz semmilyen lehetővé teszi a jelszót a helyi értéke ugyanaz, mint az Azure AD.

Miért hozzáadását tervezi csak azért a helyszíni AD-e támogatásához objekty lobs s Hodnotou (– üzletági alkalmazások), akkor esetleg érdemes lehet használandó [Azure AD tartományi szolgáltatások](../../active-directory-domain-services/index.yml) helyette.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
