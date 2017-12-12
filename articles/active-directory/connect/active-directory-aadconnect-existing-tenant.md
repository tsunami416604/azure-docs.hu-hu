---
title: "Az Azure AD Connect: Ha már rendelkezik az Azure AD |} Microsoft Docs"
description: "Ez a témakör ismerteti a csatlakozás, ha egy meglévő Azure AD-bérlő használ."
services: active-directory
documentationcenter: 
author: andkjell
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: b3efcdd8c3f7173711c005e0fe4d7080bd902663
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Az Azure AD Connect: Ha rendelkezik egy létező bérlő
Az Azure AD Connect használatával a témaköröket a legtöbb azt feltételezi, hogy a kiindulási pont egy új Azure AD-bérlő és, hogy nincsenek-e egy felhasználó sem, vagy nincs más objektumokat. Azonban ha az Azure AD-bérlő indította megadva, akkor a felhasználók és más objektumok, és most szeretné csatlakozás, akkor ebben a témakörben, hogy a felhasználó.

## <a name="the-basics"></a>Az alapok
Az Azure AD-objektum vagy a felhőben (Azure AD), vagy a helyszíni értékűre. Egy egyetlen objektumhoz nem kezelheti az egyes attribútumait a helyszíni és néhány egyéb attribútumai Azure AD-ben. Minden objektum rendelkezik egy jelzőt, amely megadja, ahol az objektum kezelése.

Egyes felhasználók a helyszíni és az egyéb kezelheti a felhőben. Egy általános forgatókönyv ehhez a konfigurációhoz fiókkezelési dolgozó munkatársak és az értékesítési munkavállalók rendelkező szervezeteknél. A nyilvántartási munkavállalók rendelkezik egy helyszíni AD-fiókot, de az értékesítési munkavállalók nem, az Azure AD-fiókkal rendelkeznek. Egyes felhasználók a helyszíni és néhány Azure AD-ben ugyanúgy felügyelheti.

Ha kezeléséhez használatba felhasználók az Azure ad-ben, amelyek szerepelnek a helyszíni AD és később meg szeretne csatlakozás, akkor néhány további szempontok figyelembe kell vennie.

## <a name="sync-with-existing-users-in-azure-ad"></a>A meglévő felhasználók szinkronizálni az Azure ad-ben
Az Azure AD Connectet telepíti, és szinkronizálásának elindítása, az Azure AD sync szolgáltatás (az Azure AD) egy ellenőrzés minden új objektumon, és próbálkozzon egyezik az objektum kereséséhez. Ez a folyamat használt három attribútum: **userPrincipalName**, **proxyAddresses**, és **sourceAnchor**/**immutableID**. Egyeztetés az **userPrincipalName** és **proxyAddresses** is ismert, egy **egyeznek helyreállítható**. Egyeztetés az **sourceAnchor** nevezik **rögzített egyezés**. Az a **proxyAddresses** attribútum csak az érték **SMTP:**, vagyis az elsődleges e-mail címét, a kiértékelés szolgál.

Az egyeztetés csak értékeli ki a Connect érkező új objektumokat. Ha objektum módosítja, akkor van megfelelő ezek az attribútumok, majd hibaüzenet jelenik meg helyette.

Ha az Azure AD talál, ahol attribútumértékek megegyeznek az adatforrásból származó kapcsolati objektum és, amely már létezik, az Azure AD-objektum, majd az Azure AD-objektum veszi át az irányítást Connect. A korábban a felhőben felügyelt objektum meg van jelölve, a helyszínen felügyelt. Összes attribútumot az Azure AD a érték szerepel a helyszíni AD a rendszer felülírja a helyi értékű. A kivétel akkor, ha az attribútumhoz egy **NULL** helyszíni érték. Ebben az esetben az Azure AD marad, de az érték csak továbbra is módosítható a helyszíni valami mással.

> [!WARNING]
> Óta az összes attribútum az Azure ad-ben a helyszíni érték felül tervezi, győződjön meg arról, hogy a helyes adatok a helyszínen. Például ha csak rendelkezik felügyelt e-mail címet az Office 365-ben, és nem tartja azt frissítése az helyszíni Active Directory tartományi Szolgáltatásokban, majd az Azure AD/Office 365 rendszerben az AD DS-ben nem található értékeket elvesznek.

> [!IMPORTANT]
> Ha jelszó-szinkronizálás, amelyet a gyorsbeállítások mindig használ, akkor az Azure AD-jelszó felülírja a jelszót a helyszíni AD. Ha a felhasználók különböző jelszavak kezelése szolgálnak, akkor szüksége tájékoztatja őket, hogy a helyszíni jelszó Connect telepítését akkor kell használnia.

Az előző szakaszban és a figyelmeztetést meg kell tervezni a tervezés. Ha sok módosításokat végzett az Azure ad-ben nem kerülnek a helyszíni Active Directory tartományi Szolgáltatásokban, akkor szükséges azon Active Directory tartományi szolgáltatások, a frissített értékekkel feltöltéséhez az objektumok az Azure AD Connect szinkronizálása előtt.

A soft-egyezés, objektumok egyező, akkor a **sourceAnchor** hozzáadódik az objektum az Azure ad-ben, rögzített egyezés később is használható.

### <a name="hard-match-vs-soft-match"></a>Merevlemez-match vs Soft-egyezés
Új telepítés Connect nincs gyakorlati különbség a soft - és merevlemez-egyezés között. A különbség a vész-helyreállítási helyzet. Ha a kiszolgáló és az Azure AD Connect elvesztette, telepítse újra egy új példányt adatvesztés nélkül. A sourceAnchor objektum Connect küldött kezdeti telepítése során. Az ügyfél (az Azure AD Connect), amely sokkal gyorsabb, mint ez megegyezik az Azure AD majd kiértékelése a találatra. Csatlakozás és az Azure AD rögzített egyezés értékeli. Az Azure AD csak kiértékeli a letölthető egyezés.

### <a name="other-objects-than-users"></a>Más objektumok, mint a felhasználók
Levelezési csoportok és a partnerek akkor is helyreállítható-egyezés a proxyAddresses. Merevlemez-match esetén nem alkalmazható, mert csak akkor frissíthető a sourceAnchor/immutableID (a PowerShell használatával) a felhasználók csak. Amely nem a levelezési csoportok jelenleg nem támogatott soft-match vagy rögzített egyezés.

## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Hozzon létre egy új helyszíni Active Directory adatokból az Azure ad-ben
Egyes ügyfelek egy kizárólag felhőalapú megoldást az Azure ad-val kezdődő, és nem rendelkeznek helyszíni AD. Később kívánt helyi erőforrást, és szeretne egy helyszíni AD az Azure AD-adatok alapján. Az Azure AD Connect nem tud segítséget ehhez a forgatókönyvhöz. Nem hoz létre helyszíni felhasználók, és nincs bármely beállítani a jelszót a helyi ugyanaz, mint az Azure AD lehetőséget.

Ha a csak ok miért hozzáadását tervezi a helyszíni AD támogatására szolgál LOB (az üzletági alkalmazások), akkor talán érdemes használni [az Azure AD tartományi szolgáltatások](../../active-directory-domain-services/index.md) helyette.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
