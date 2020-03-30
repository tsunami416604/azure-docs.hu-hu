---
title: Az Azure Active Directory biztonsági beállításai
description: Biztonsági alapértelmezett házirendek, amelyek segítenek megvédeni a szervezeteket a gyakori támadásoktól
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248844"
---
# <a name="what-are-security-defaults"></a>Mik a biztonsági alapértékek?

A biztonság kezelése nehéz lehet, ha a közös identitással kapcsolatos támadások egyre népszerűbbek. Ezek a támadások közé tartozik a jelszó spray, visszajátszás, és az adathalászat.

Az Azure Active Directory (Azure AD) biztonsági beállításai megkönnyítik a biztonságot és a szervezet védelmét. A biztonsági alapértelmezések előre konfigurált biztonsági beállításokat tartalmaznak a gyakori támadásokhoz. 

A Microsoft mindenki számára elérhetővé teszi a biztonsági beállításokat. A cél annak biztosítása, hogy minden szervezet rendelkezik egy alapszintű biztonsági engedélyezve extra költség nélkül. Az Azure Portalon bekapcsolja a biztonsági alapértékeket.

![Képernyőkép az Azure Portalról a kapcsolóval a biztonsági alapértelmezések engedélyezéséhez](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Ha a bérlő2019. október 22-én vagy azt követően jött létre, lehetséges, hogy az új biztonságos alapértelmezett viselkedést tapasztalja, és már engedélyezve van a bérlőben a biztonsági alapértelmezések. Annak érdekében, hogy megvédje az összes felhasználó, biztonsági alapértelmezett a rendszer gördült ki az összes új bérlő létrehozott.

További részletek arról, hogy miért biztonsági alapértelmezett állnak rendelkezésre megtalálható Alex Weinert blogbejegyzést, [Bemutatkozik a biztonsági alapértelmezett](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414).

## <a name="unified-multi-factor-authentication-registration"></a>Egységes többtényezős hitelesítési regisztráció

A bérlő minden felhasználójának regisztrálnia kell a többtényezős hitelesítéshez (MFA) az Azure többtényezős hitelesítési szolgáltatás formájában. A felhasználóknak 14 nap áll a többtényezős hitelesítésre való regisztrációra a Microsoft Authenticator alkalmazás használatával. A 14 nap eltelte után a felhasználó nem tud bejelentkezni, amíg a többtényezős hitelesítés regisztrációbefejeződött.

Tisztában vagyunk azzal, hogy egyes felhasználók előfordulhat, hogy nem a hivatalból, vagy nem jelentkezik be a 14 nap alatt közvetlenül a biztonsági alapértelmezések engedélyezése után. Annak érdekében, hogy minden felhasználónak elegendő ideje legyen a többtényezős hitelesítésre való regisztrációra, a 14 napos időszak minden felhasználó számára egyedi. A felhasználó 14 napos időszaka az első sikeres interaktív bejelentkezés után kezdődik, miután engedélyezte a biztonsági beállításokat.

## <a name="multi-factor-authentication-enforcement"></a>Többtényezős hitelesítés kényszerítése

### <a name="protecting-administrators"></a>A rendszergazdák védelme

A kiemelt jogosultságú fiókokhoz hozzáféréssel rendelkező felhasználók nagyobb hozzáféréssel rendelkeznek a környezethez. Mivel a hatalom ezek a számlák, meg kell kezelni őket különös gonddal. A kiemelt jogosultságú fiókok védelmének javításának egyik gyakori módja a fiókellenőrzés erősebb formájának megkövetelése a bejelentkezéshez. Az Azure AD-ben a többtényezős hitelesítés megkövetelésével erősebb fiókellenőrzést kaphat.

A többtényezős hitelesítéssel történő regisztráció befejezése után a következő kilenc Azure AD-rendszergazdai szerepkörszükséges további hitelesítést kell végrehajtania minden bejelentkezéskor:

- Globális rendszergazda
- SharePoint-rendszergazda
- Exchange-rendszergazda
- Feltételes hozzáférés rendszergazdája
- Biztonsági rendszergazda
- Helpdesk-rendszergazda vagy jelszó-rendszergazda
- Számlázási rendszergazda
- Rendszergazda
- Hitelesítési rendszergazda

### <a name="protecting-all-users"></a>Az összes felhasználó védelme

Hajlamosak vagyunk azt gondolni, hogy a rendszergazdai fiókok az egyetlen olyan fiókok, amelyek további hitelesítési rétegeket igényelnek. A rendszergazdák széles körű hozzáféréssel rendelkeznek a bizalmas adatokhoz, és módosíthatják az előfizetések egészének beállításait. De a támadók hajlamosak a végfelhasználókat megcélozni. 

Miután ezek a támadók hozzáférhetnek, az eredeti fióktulajdonos nevében hozzáférést kérhetnek a kiemelt adatokhoz. Akár a teljes könyvtárat is letölthetik, hogy adathalász támadást hajtsanak végre az egész szervezet ellen. 

Az egyik gyakori módszer a védelem javítására minden felhasználó számára, hogy mindenki számára erősebb fiókellenőrzést, például többtényezős hitelesítést igényeljen. Miután a felhasználók befejezték a többtényezős hitelesítés regisztrációját, szükség esetén további hitelesítést kérnek.

### <a name="blocking-legacy-authentication"></a>Örökölt hitelesítés blokkolása

Annak érdekében, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, az Azure AD számos hitelesítési protokollt támogat, beleértve az örökölt hitelesítést is. *Az örökölt hitelesítés* olyan kifejezés, amely a következő hitelesítési kérelemre hivatkozik:

- Modern hitelesítést nem használó ügyfelek (például Office 2010-ügyfél).
- Minden olyan ügyfél, amely régebbi levelezőprotokollokat használ, például IMAP, SMTP vagy POP3.

Ma a kompromittáló bejelentkezési kísérletek többsége örökölt hitelesítésből származik. Az örökölt hitelesítés nem támogatja a többtényezős hitelesítést. Még ha a címtárban engedélyezve is van a többtényezős hitelesítési házirend, a támadó egy régebbi protokoll használatával hitelesítheti magát, és megkerülheti a többtényezős hitelesítést. 

Miután a biztonsági alapértelmezések engedélyezve vannak a bérlőben, a rendszer blokkolja a régebbi protokoll által benyújtott összes hitelesítési kérelmet. A biztonsági alapértelmezések szerint blokkolja az Exchange Active Sync alapfokú hitelesítést.

> [!WARNING]
> A biztonsági alapértelmezések engedélyezése előtt győződjön meg arról, hogy a rendszergazdák nem használnak régebbi hitelesítési protokollokat. További információ: [Hogyan lehet elmozdulni az örökölt hitelesítéstől.](concept-fundamentals-block-legacy-authentication.md)

### <a name="protecting-privileged-actions"></a>Kiemelt műveletek védelme

A szervezetek az Azure Resource Manager API-n keresztül kezelt számos Azure-szolgáltatást használnak, többek között a következőket:

- Azure portál 
- Azure PowerShell 
- Azure CLI

Az Azure Resource Manager használatával a szolgáltatások kezelése rendkívül magas szintű művelet. Az Azure Resource Manager módosíthatja a bérlői szintű konfigurációkat, például a szolgáltatás beállításait és az előfizetési számlázást. Az egylépéses hitelesítés számos támadással, például adathalászattal és jelszóspray-vel szemben érhető el. 

Fontos, hogy ellenőrizze a felhasználók identitását, akik szeretnék elérni az Azure Resource Manager és a konfigurációk frissítése. A hozzáférés engedélyezése előtt további hitelesítést igényel.

Miután engedélyezte a biztonsági alapértékeket a bérlőben, minden olyan felhasználónak, aki hozzáfér az Azure Portalhoz, az Azure PowerShellhez vagy az Azure CLI-hez, további hitelesítést kell végrehajtania. Ez a szabályzat minden olyan felhasználóra vonatkozik, aki az Azure Resource Manager hez fér hozzá, függetlenül attól, hogy rendszergazda vagy felhasználó. 

Ha a felhasználó nincs regisztrálva a többtényezős hitelesítéshez, a folytatáshoz regisztrálnia kell a Microsoft Authenticator alkalmazással. Nem 14 napos többtényezős hitelesítésregisztrációs időszak kerül rendelkezésre.

> [!NOTE]
> A 2017 előtti Exchange Online-bérlők korszerű hitelesítése alapértelmezés szerint le van tiltva. Annak érdekében, hogy elkerülje a bejelentkezési hurok lehetőségét a bérlőkhitelesítés során, engedélyeznie kell a [modern hitelesítést.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> Az Azure AD Connect szinkronizálási fiók ki van zárva a biztonsági alapértelmezett, és nem lesz rákérdezés, hogy regisztráljon, vagy többtényezős hitelesítést. A szervezetek nem szabad ezt a fiókot más célokra használniuk.

## <a name="deployment-considerations"></a>Telepítési szempontok

A következő további szempontok a bérlő biztonsági alapbeállításainak telepítésével kapcsolatosak.

### <a name="authentication-methods"></a>Hitelesítési módszerek

A biztonsági alapértelmezések lehetővé teszik az Azure többtényezős hitelesítésének regisztrációját és **használatát, csak a Microsoft Authenticator alkalmazás használatával, értesítések használatával.** A feltételes hozzáférés lehetővé teszi a rendszergazda által engedélyezni kívánt hitelesítési módszer használatát.

|   | Alapértelmezett biztonsági szabályok | Feltételes hozzáférés |
| --- | --- | --- |
| Értesítés mobilalkalmazáson keresztül | X | X |
| Ellenőrző kód mobilalkalmazásból vagy hardvertokenből |   | X |
| Sms-ben telefonon |   | X |
| Telefonhívás |   | X |
| Alkalmazásjelszavak. |   | X** |

** Az alkalmazásjelszavak csak felhasználónkénti többfunkciós hitelesítési környezetben érhetők el, csak akkor, ha a rendszergazdák engedélyezik.

### <a name="conditional-access"></a>Feltételes hozzáférés

A Feltételes hozzáférés segítségével a biztonsági alapértékekhez hasonló házirendeket konfigurálhat, de részletesebben, beleértve a felhasználói kizárásokat is, amelyek a biztonsági alapértékekben nem érhetők el. Ha feltételes hozzáférést használ, és a környezetében engedélyezve van a feltételes hozzáférési házirendek, a biztonsági beállítások nem lesznek elérhetők. Ha feltételes hozzáférést biztosít, de nincs engedélyezve feltételes hozzáférési házirend a környezetében, a feltételes hozzáférési házirendek engedélyezése ig használhatja a biztonsági alapértelmezett beállításokat. Az Azure AD-licencelésről az [Azure AD díjszabási oldalán](https://azure.microsoft.com/pricing/details/active-directory/)talál további információt.

![Figyelmeztető üzenet, hogy lehet, hogy a biztonsági alapértelmezések vagy a feltételes hozzáférés nem mindkettő](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Az alábbiakban részletes útmutatókat olvashat arról, hogyan konfigurálhatja a feltételes hozzáférést az egyenértékű házirendek konfigurálásához:

- [MFA megkövetelése a rendszergazdáktól](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA megkövetelése az Azure-beli felügyelethez](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Régi hitelesítési folyamat letiltása](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA megkövetelése minden felhasználótól](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA-regisztráció megkövetelése](../identity-protection/howto-identity-protection-configure-mfa-policy.md) – Azure AD-identitásvédelem szükséges

## <a name="enabling-security-defaults"></a>Biztonsági alapértelmezések engedélyezése

A címtár biztonsági alapértelmezéseinek engedélyezése:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) biztonsági rendszergazdaként, feltételes hozzáférés-rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **tulajdonságai .**
1. Válassza **a Biztonsági beállítások kezelése**lehetőséget.
1. Állítsa a **Biztonsági beállítások engedélyezése** beállítást **Igen**értékre.
1. Kattintson a **Mentés** gombra.

## <a name="disabling-security-defaults"></a>Biztonsági alapértelmezések letiltása

Azoknak a szervezeteknek, amelyek úgy döntenek, hogy olyan feltételes hozzáférési házirendeket valósítanak meg, amelyek helyettesítik a biztonsági alapértelmezett beállításokat, le kell tiltaniuk a biztonsági alapértelmezett értékeket. 

![Figyelmeztetési üzenet a feltételes hozzáférés engedélyezéséhez letiltja a biztonsági alapértelmezett értékeket](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

A házirend-ellenőrzés biztonsági beállításainak letiltása:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) biztonsági rendszergazdaként, feltételes hozzáférés-rendszergazdaként vagy globális rendszergazdaként.
1. Tallózással keresse meg az **Azure Active Directory** > **tulajdonságai .**
1. Válassza **a Biztonsági beállítások kezelése**lehetőséget.
1. Állítsa a **Biztonsági beállítások engedélyezése** beállítást, és állítsa **nem**értékre.
1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[Gyakori feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md)
