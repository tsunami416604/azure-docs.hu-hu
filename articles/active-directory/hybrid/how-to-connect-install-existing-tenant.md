---
title: 'Azure AD Connect: Ha már rendelkezik azure AD - | Microsoft dokumentumok'
description: Ez a témakör ismerteti, hogyan használhatja a Connect, ha egy meglévő Azure AD-bérlő.
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
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71336824"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Ha egy létező bérlővel rendelkezik
Az Azure AD Connect használatával kapcsolatos témakörök többsége feltételezi, hogy egy új Azure AD-bérlővel kezdi, és nincsenek ott felhasználók vagy más objektumok. De ha egy Azure AD-bérlővel kezdett, felhasználókkal és más objektumokkal töltötte fel, és most szeretné használni a Connect szolgáltatást, akkor ez a témakör az Ön számára.

## <a name="the-basics"></a>Az alapok
Az Azure AD-ben egy objektum a felhőben (Azure AD) vagy a helyszínen van elsajátítva. Egyetlen objektum esetén nem kezelheti a helyszíni attribútumokat és néhány más attribútumot az Azure AD-ben. Minden objektumnak van egy jelzője, amely jelzi, hogy hol van az objektum kezelése.

Egyes felhasználók a helyszínen és más a felhőben kezelheti. A konfiguráció gyakori forgatókönyve a könyvelési dolgozók és az értékesítési dolgozók vegyesen rendelkező szervezete. A könyvelési dolgozók rendelkeznek egy helyszíni AD-fiókkal, de az értékesítési dolgozók nem, rendelkeznek egy fiókot az Azure AD-ben. Egyes felhasználókat a helyszínen, és néhányat az Azure AD-ben kezelne.

Ha elkezdte kezelni a felhasználókat az Azure AD, amelyek szintén a helyszíni AD, és később szeretné használni connect, majd van néhány további aggályokat kell figyelembe venni.

## <a name="sync-with-existing-users-in-azure-ad"></a>Szinkronizálás meglévő felhasználókkal az Azure AD-ben
Amikor telepíti az Azure AD Connectet, és elkezdi a szinkronizálást, az Azure AD szinkronizálási szolgáltatása (az Azure AD-ben) minden új objektumot ellenőriz, és megpróbál egy meglévő objektumot találni. A folyamathoz három attribútum használatos: **userPrincipalName**, **proxyAddresses**, és **sourceAnchor**/**immutableID**. A **userPrincipalName** és a proxyAddresses egyezést **soft match-nek nevezzük.** **proxyAddresses** A **sourceAnchor** egyezését **kemény egyezésnek**nevezik. A **proxyAddresses** attribútum hoz csak az **smtp:**, azaz az elsődleges e-mail cím, a kiértékeléshez használt érték.

Az egyezést a rendszer csak a Connectből érkező új objektumok esetében értékeli ki. Ha úgy módosít egy meglévő objektumot, hogy az megfeleljen ezeknek az attribútumoknak, akkor egy hiba jelenik meg helyette.

Ha az Azure AD talál egy objektumot, ahol az attribútumértékek megegyeznek a Connectből érkező objektumhoz, és hogy már szerepel az Azure AD-ben, akkor az Azure AD-ben lévő objektumot a Connect átveszi. A korábban felhőáltal felügyelt objektum a helyszíni felügyeltként van megjelölve. Az Azure AD-ben a helyszíni AD-ben értékkel rendelkező összes attribútum felülírja a helyszíni értéket. A kivétel az, ha egy attribútum **null értékkel** rendelkezik a helyszínen. Ebben az esetben az Azure AD értéke megmarad, de továbbra is csak a helyszíni módosítása, hogy valami más.

> [!WARNING]
> Mivel az Azure AD összes attribútumát felül írja felül a helyszíni érték, győződjön meg arról, hogy jó adatokat a helyszínen. Ha például csak az Office 365-ben kezelte az e-mail-címet, és nem frissítette azt a helyszíni AD DS-ben, akkor elveszíti az Azure AD/Office 365 nem az AD DS-ben nem található értékeket.

> [!IMPORTANT]
> Ha jelszó-szinkronizálást használ, amelyet mindig a gyorsbeállítások használnak, majd az Azure AD-ben a jelszót felülírja a jelszóval a helyszíni AD-ben. Ha a felhasználók különböző jelszavak kezelésére szolgálnak, akkor tájékoztatnia kell őket arról, hogy a Csatlakozás telepítésekor a helyszíni jelszót kell használniuk.

Az előző szakaszt és a figyelmeztetést figyelembe kell venni a tervezéssorán. Ha számos olyan módosítást hajtott végre az Azure AD-ben, amely nem jelenik meg a helyszíni AD DS-ben, akkor meg kell terveznie, hogyan népetheti fel az AD DS-t a frissített értékekkel, mielőtt szinkronizálná az objektumokat az Azure AD Connecttel.

Ha az objektumokat egy soft-match egyező, majd a **sourceAnchor** hozzáadódik az objektumhoz az Azure AD így a kemény egyezés később használható.

>[!IMPORTANT]
> A Microsoft nyomatékosan javasolja, hogy ne szinkronizálja a helyszíni fiókokat az Azure Active Directory már meglévő felügyeleti fiókokkal.

### <a name="hard-match-vs-soft-match"></a>Hard-match vs Soft-match
A Connect új telepítéséhez nincs gyakorlati különbség a puha és a kemény mérkőzés között. A különbség a katasztrófa-helyreállítási helyzet. Ha elvesztette a kiszolgálót az Azure AD Connect, újratelepítheti az új példány adatvesztés nélkül. Egy objektum sourceAnchor küldött Connect a kezdeti telepítés során. Az egyezést ezután az ügyfél (Azure AD Connect) kiértékelheti, ami sokkal gyorsabb, mint az Azure AD-ben. A nehezen illeszkedő egyezést a Connect és az Azure AD is kiértékeli. A soft match csak az Azure AD által kiértékelt.

### <a name="other-objects-than-users"></a>A felhasználókon kívüli egyéb objektumok
A levelezést támogató csoportok és névjegyek esetében a proxycímek alapján is lágyan egyezhet. A hard-match nem alkalmazható, mivel csak a sourceAnchor/immutableID (PowerShell használatával) csak a felhasználók számára frissíthető. A nem levelezési engedélyezésű csoportok esetében jelenleg nem támogatja a soft-match vagy a hard-match.

### <a name="admin-role-considerations"></a>Rendszergazdai szerepkörre vonatkozó szempontok
Annak megakadályozása érdekében, hogy a nem megbízható helyszíni felhasználók egyezés egy felhőalapú felhasználó, amely bármilyen rendszergazdai szerepkörrel rendelkezik, az Azure AD Connect nem egyezik a helyszíni felhasználói objektumok olyan objektumokkal, amelyek rendszergazdai szerepkörrel rendelkeznek. Ez alapértelmezés szerint. A probléma kerülő megoldásához tegye a következőket:

1.  Távolítsa el a címtárszerepköröket a csak felhőalapú felhasználói objektumból.
2.  Ha sikertelen felhasználói szinkronizálási kísérlet történt, törölje a karanténba helyezett objektumot a felhőből.
3.  Szinkronizálás tanuska.
4.  Igény szerint adja hozzá a címtárszerepköröket a felhőbeli felhasználói objektumhoz, miután az egyeztetés megtörtént.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Új helyszíni Active Directory létrehozása az Azure AD-ben tárolt adatokból
Egyes ügyfelek az Azure AD-vel egy csak felhőalapú megoldással kezdik, és nem rendelkeznek helyszíni AD-vel. Később a helyszíni erőforrásokat szeretnék felhasználni, és az Azure AD-adatok on-premise AD-t szeretnének építeni. Az Azure AD Connect nem tud segíteni ebben a forgatókönyvben. It does not create users on-premises and it does not have any ability to set the password on-premises to the same as in Azure AD.

Ha az egyetlen ok, amiért azt tervezi, hogy a helyszíni AD hozzáadása a LOBs (Business-alkalmazások) támogatása, akkor talán érdemes az [Azure AD tartományi szolgáltatások](../../active-directory-domain-services/index.yml) használata helyett.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
