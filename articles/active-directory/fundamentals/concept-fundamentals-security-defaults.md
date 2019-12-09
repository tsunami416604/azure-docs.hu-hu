---
title: Azure Active Directory biztonsági alapértelmezett beállítások
description: Biztonsági alapértelmezett házirendek, amelyek segítenek a szervezetek számára a gyakori támadások elleni védelemben
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d899f477612e4c738314187f61551fe5c0b17f8d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932410"
---
# <a name="what-are-security-defaults"></a>Mik azok a biztonsági alapértékek?

A biztonság kezelése nehéz lehet, ha a közös identitással kapcsolatos támadások egyre népszerűbbek. A támadások közé tartozik a jelszó-és a visszajátszás, valamint az adathalászat.

A Azure Active Directory (Azure AD) biztonsági alapértékei megkönnyítik a biztonságos biztonságot, és segítenek a szervezet védelmében. A biztonsági alapértékek előre konfigurált biztonsági beállításokat tartalmaznak a gyakori támadásokhoz. 

A Microsoft mindenki számára elérhetővé teszi a biztonsági beállításokat. A cél annak biztosítása, hogy az összes szervezet külön díj nélkül engedélyezze az alapszintű biztonsági szintet. A biztonsági alapértelmezéseket be kell kapcsolni a Azure Portal.

![Képernyőkép a Azure Portalról a biztonsági Alapértelmezések engedélyezésére szolgáló váltógomb használatával](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
A következő biztonsági konfigurációk lesznek bekapcsolva a bérlőben. 

## <a name="unified-multi-factor-authentication-registration"></a>Egységesített Multi-Factor Authentication regisztráció

A bérlő összes felhasználójának regisztrálnia kell a többtényezős hitelesítést (MFA) az Azure Multi-Factor Authentication szolgáltatás formájában. A felhasználók 14 napon belül regisztrálhatnak Multi-Factor Authenticationre a Microsoft Authenticator alkalmazás használatával. A 14 nap eltelte után a felhasználó nem fog tudni bejelentkezni, amíg Multi-Factor Authentication regisztráció be nem fejeződik.

Tisztában vagyunk azzal, hogy előfordulhat, hogy egyes felhasználók házon kívül vannak, vagy nem jelentkeznek be a biztonsági beállítások engedélyezése után 14 napon belül. Annak biztosítása érdekében, hogy minden felhasználó számára elegendő idő legyen a Multi-Factor Authentication regisztrálására, a 14 napos időszak minden felhasználó esetében egyedi. A felhasználók 14 napos időszaka az első sikeres interaktív bejelentkezés után kezdődik, miután engedélyezte a biztonsági beállításokat.

## <a name="multi-factor-authentication-enforcement"></a>Multi-Factor Authentication kényszerítés

### <a name="protecting-administrators"></a>A rendszergazdák védelme

A Kiemelt fiókokhoz hozzáféréssel rendelkező felhasználók nagyobb hozzáférést biztosítanak a környezethez. Ezeknek a fiókoknak a kihasználása miatt speciális gondossággal kell kezelnie őket. A rendszerjogosultságú fiókok védelmének egyik gyakori módszere a fiók-ellenőrzés erősebb formájának megkövetelése a bejelentkezéshez. Az Azure AD-ben a Multi-Factor Authentication megkövetelésével erősebb fiókokat lehet ellenőrizni.

A Multi-Factor Authentication való regisztráció után a következő kilenc Azure AD-rendszergazdai szerepkör szükséges további hitelesítés végrehajtásához minden alkalommal, amikor bejelentkeznek:

- Globális rendszergazda
- SharePoint-rendszergazda
- Exchange-rendszergazda
- Feltételes hozzáférésű rendszergazda
- Biztonsági rendszergazda
- Segélyszolgálat rendszergazdája vagy jelszavas rendszergazdája
- Számlázási rendszergazda
- Felhasználói rendszergazda
- Hitelesítés rendszergazdája

### <a name="protecting-all-users"></a>Az összes felhasználó védelme

Általában úgy gondoljuk, hogy a rendszergazdai fiókok az egyetlen olyan fiókok, amelyeknek további hitelesítési rétegre van szükségük. A rendszergazdák széles körű hozzáféréssel rendelkeznek a bizalmas adatokhoz, és módosíthatják az előfizetési szintű beállításokat. A támadók azonban általában a végfelhasználók számára célozzák meg. 

Miután ezek a támadók hozzáférnek, az eredeti fiók tulajdonosának nevében kérhetnek hozzáférést az emelt szintű információhoz. Akár le is tölthetik a teljes könyvtárat, hogy elvégezzenek egy adathalászat elleni támadást a teljes szervezetben. 

Az egyik gyakori módszer az, hogy javítsa az összes felhasználó védelmét, hogy mindenki számára a fiókok ellenőrzésének erősebb formáját kell megkövetelni, például Multi-Factor Authentication. Miután a felhasználók befejezték Multi-Factor Authentication regisztrációt, a rendszer szükség esetén további hitelesítést kér.

### <a name="blocking-legacy-authentication"></a>Örökölt hitelesítés blokkolása

Ahhoz, hogy a felhasználók könnyen hozzáférjenek a felhőalapú alkalmazásokhoz, az Azure AD számos hitelesítési protokollt támogat, beleértve az örökölt hitelesítést is. Az *örökölt hitelesítés* olyan kifejezés, amely a által végzett hitelesítési kérelemre hivatkozik:

- Régebbi Office-ügyfelek, amelyek nem használnak modern hitelesítést (például Office 2010-ügyfél).
- Minden olyan ügyfél, amely régebbi levelezési protokollokat használ, mint például az IMAP, az SMTP vagy a POP3.

Napjainkban a kiegyezéses bejelentkezési kísérletek többsége örökölt hitelesítésből származik. Az örökölt hitelesítés nem támogatja a Multi-Factor Authentication. Ha a címtárban engedélyezve van a Multi-Factor Authentication szabályzat, a támadó egy régebbi protokoll használatával is hitelesítheti magát, és megkerülheti a Multi-Factor Authentication. 

Ha a biztonsági alapértékek engedélyezve vannak a bérlőben, a régebbi protokoll által végrehajtott összes hitelesítési kérelem le lesz tiltva. A biztonsági alapértelmezett beállítások nem gátolják meg az Exchange ActiveSync szolgáltatást.

### <a name="protecting-privileged-actions"></a>A Kiemelt műveletek védelme

A szervezetek a Azure Resource Manager API-n keresztül felügyelt különböző Azure-szolgáltatásokat használják, beleértve a következőket:

- Azure Portal 
- Azure PowerShell 
- Azure parancssori felület (CLI)

A Azure Resource Manager használata a szolgáltatások kezeléséhez magas jogosultsági szintű művelet. Azure Resource Manager megváltoztathatja a bérlői szintű konfigurációkat, például a szolgáltatás beállításait és az előfizetés számlázását. Az egytényezős hitelesítés számos különböző támadáshoz, például az adathalászathoz és a jelszó-permetezéshez van kitéve. 

Fontos ellenőrizni azon felhasználók identitását, akik szeretnének hozzáférni Azure Resource Manager és frissíteni a konfigurációkat. A hozzáférés engedélyezése előtt ellenőrizze az identitását további hitelesítés megkövetelésével.

Miután engedélyezte a biztonsági alapértelmezett beállításokat a bérlőben, a Azure Portalt, Azure PowerShellt vagy az Azure CLI-t elérő bármely felhasználónak további hitelesítést kell végeznie. Ez a szabályzat minden olyan felhasználóra vonatkozik, aki Azure Resource Managerhoz fér hozzá, függetlenül attól, hogy rendszergazda vagy felhasználó. 

Ha a felhasználó nincs regisztrálva a Multi-Factor Authenticationhoz, a folytatáshoz a Microsoft Authenticator alkalmazás használatával kell regisztrálnia a felhasználót. A rendszer nem biztosít 14 napos Multi-Factor Authentication regisztrációs időszakot.

## <a name="deployment-considerations"></a>Telepítési szempontok

A következő további szempontok a bérlő biztonsági alapértékeinek üzembe helyezésével kapcsolatosak.

### <a name="older-protocols"></a>Régebbi protokollok

A levelezési ügyfelek a régebbi hitelesítési protokollokat (például az IMAP, az SMTP és a POP3) használják a hitelesítési kérések elvégzéséhez. Ezek a protokollok nem támogatják a Multi-Factor Authentication. A fiók többsége megsérti a Microsoftot, hogy olyan régebbi protokollokkal szembeni támadásokkal szemben, amelyek a Multi-Factor Authentication megkerülésére próbálnak kerülni. 

Annak biztosítása érdekében, hogy Multi-Factor Authentication szükséges a rendszergazdai fiókba való bejelentkezéshez, és hogy a támadók ne tudják megkerülni azt, a biztonsági alapértékek blokkolják a rendszergazdai fiókokra vonatkozó összes hitelesítési kérelmet a régebbi protokollok közül.

> [!WARNING]
> A beállítás engedélyezése előtt győződjön meg arról, hogy a rendszergazdák nem használnak régebbi hitelesítési protokollokat. További információkért lásd: [a korábbi hitelesítéstől való elmozdulás](concept-fundamentals-block-legacy-authentication.md).

### <a name="conditional-access"></a>Feltételes hozzáférés

A feltételes hozzáférés használatával olyan házirendeket konfigurálhat, amelyek a biztonsági Alapértelmezések által engedélyezett viselkedést biztosítják. Ha feltételes hozzáférést használ, és a környezetében engedélyezve vannak feltételes hozzáférési szabályzatok, a biztonsági beállítások nem lesznek elérhetők az Ön számára. Ha olyan licenccel rendelkezik, amely feltételes hozzáférést biztosít, de nem rendelkezik a környezetben engedélyezett feltételes hozzáférési szabályzatokkal, akkor a feltételes hozzáférési szabályzatok engedélyezése előtt üdvözli a biztonsági beállítások használatát.

![Figyelmeztető üzenet arról, hogy a biztonsági alapértelmezett beállítások vagy a feltételes hozzáférés nem mindkét esetben](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Az alábbi lépések részletesen ismertetik, hogyan használható a feltételes hozzáférés az egyenértékű szabályzatok konfigurálásához:

- [MFA megkövetelése rendszergazdák számára](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA megkövetelése az Azure-felügyelethez](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Örökölt hitelesítés tiltása](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA megkövetelése minden felhasználó számára](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA-regisztráció megkövetelése](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – Azure ad Identity Protection szükséges

## <a name="enabling-security-defaults"></a>Biztonsági alapértékek engedélyezése

A biztonsági alapértelmezett beállítások engedélyezése a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) biztonsági rendszergazdaként, feltételes hozzáférésű rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory** > **tulajdonságokat**.
1. Válassza a **biztonsági beállítások kezelése**lehetőséget.
1. Állítsa a **biztonsági Alapértelmezések engedélyezése** kapcsolót **Igen**értékre.
1. Kattintson a **Mentés** gombra.

## <a name="disabling-security-defaults"></a>Biztonsági alapértékek letiltása

Azok a szervezetek, amelyek a biztonsági alapértékeket lecserélő feltételes hozzáférési házirendek megvalósítását választják, le kell tiltaniuk a biztonsági alapértékeket. 

![Figyelmeztető üzenet a feltételes hozzáférés engedélyezéséhez tiltsa le a biztonsági alapértelmezéseket](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

A biztonsági alapértelmezett beállítások letiltása a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) biztonsági rendszergazdaként, feltételes hozzáférésű rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg **Azure Active Directory** > **tulajdonságokat**.
1. Válassza a **biztonsági beállítások kezelése**lehetőséget.
1. Állítsa be a **biztonsági beállítások engedélyezése** beállítást a **nem**értékre.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

[Általános feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md)
