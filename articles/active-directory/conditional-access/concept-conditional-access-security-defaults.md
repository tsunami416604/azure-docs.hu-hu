---
title: Azure Active Directory biztonsági alapértelmezett beállítások
description: Biztonsági alapértelmezett házirendek, amelyek célja a szervezetek védelme az általános támadásokkal szemben
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453042"
---
# <a name="what-are-security-defaults"></a>Mik azok a biztonsági alapértékek?

A biztonság kezelése nehéz lehet, ha az identitással kapcsolatos gyakori támadások, például a jelszó-és adatlopások, valamint az adathalászat egyre népszerűbbek. A Azure Active Directory (AD) biztonsági alapértékeinek célja, hogy egyszerűbben lehessen létrehozni a szervezet védelmét a gyakori támadásoktól. A biztonsági alapértékek megkönnyítik a biztonságos biztonságot, és segít megvédeni a szervezetet az általános támadásokkal szemben. A biztonsági alapértelmezett beállítások előre konfigurált biztonsági beállításokat tartalmaznak ezekhez a gyakori támadásokhoz. A Microsoft mindenki számára elérhetővé teszi a biztonsági beállításokat. A cél annak biztosítása, hogy az összes szervezet külön díj nélkül engedélyezze az alapszintű biztonsági szintet.

![Képernyőfelvétel az új biztonsági alapértékek UX-ről](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
A következő biztonsági konfigurációk lesznek bekapcsolva a bérlőben. 

## <a name="unified-mfa-registration"></a>Egységes MFA-regisztráció

A bérlő összes felhasználójának regisztrálnia kell az Azure Multi-Factor Authentication (MFA) számára. A felhasználók 14 napig regisztrálhatnak az Azure MFA-ra az Microsoft Authenticator alkalmazás használatával. A 14 nap eltelte után a felhasználó nem fog tudni bejelentkezni, amíg az MFA-regisztráció be nem fejeződik.

Tisztában vagyunk azzal, hogy előfordulhat, hogy egyes felhasználók házon kívül vannak, és/vagy nem jelentkeznek be a biztonsági alapértékek engedélyezését követő 14 napban. Annak biztosítása érdekében, hogy minden felhasználó számára elegendő idő legyen az MFA regisztrálására, a 14 napos időszak minden felhasználó esetében egyedi. A felhasználó 14 napos időszaka az első sikeres interaktív bejelentkezés után kezdődik, amint a biztonsági beállítások engedélyezve lettek.

## <a name="mfa-enforcement"></a>MFA-kényszerítés

### <a name="protecting-administrators"></a>A rendszergazdák védelme

A Kiemelt fiókokhoz hozzáféréssel rendelkező felhasználók nagyobb hozzáférést biztosítanak a környezethez. Ezeknek a fiókoknak a kihasználása miatt speciális gondossággal kell kezelnie őket. A rendszerjogosultságú fiókok védelmének egyik gyakori módszere, ha a bejelentkezéshez a fiókok ellenőrzésének erősebb formáját igényli. Azure Active Directory a többtényezős hitelesítés megkövetelésével erősebb fiókokat lehet ellenőrizni.

Az MFA-regisztráció befejezése után a következő kilenc Azure AD-rendszergazdai szerepkörre lesz szükség a többtényezős hitelesítés végrehajtásához, amikor bejelentkeznek.

- Globális rendszergazda
- SharePoint-rendszergazda
- Exchange-rendszergazda
- Feltételes hozzáférésű rendszergazda
- Biztonsági rendszergazda
- Segélyszolgálat rendszergazdája/jelszó-rendszergazda
- Számlázási rendszergazda
- Felhasználói rendszergazda
- Hitelesítés rendszergazdája

### <a name="protecting-all-users"></a>Az összes felhasználó védelme

Úgy gondoljuk, hogy a rendszergazdai fiókok csak a többtényezős hitelesítéssel (MFA) szembeni védelmet igénylő fiókok. A rendszergazdák széles körű hozzáféréssel rendelkeznek a bizalmas adatokhoz, és módosíthatják az előfizetési szintű beállításokat. Azonban a rossz színészek általában a végfelhasználók számára célozzák meg. A hozzáférés megszerzése után ezek a rossz szereplők az eredeti fiók tulajdonosának nevében igényelhetnek hozzáférést a Kiemelt információkhoz, vagy a teljes könyvtárat letöltve elvégezhetik az adathalászat elleni támadást a teljes szervezeten belül. Az egyik gyakori módszer az, hogy javítsa az összes felhasználó védelmét, így a fiókok ellenőrzésének erősebb formáját kell megkövetelni, például a többtényezős hitelesítést (MFA) mindenki számára. Az MFA-regisztráció befejezése után a rendszer szükség esetén a felhasználókat kéri az MFA-ra.

### <a name="blocking-legacy-authentication"></a>Örökölt hitelesítés blokkolása

Ahhoz, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, Azure Active Directory (Azure AD) a hitelesítési protokollok széles választékát támogatja, beleértve az örökölt hitelesítést is. Az örökölt hitelesítés olyan kifejezés, amely a által végzett hitelesítési kérelemre hivatkozik:

- Régebbi Office-ügyfelek, amelyek nem használnak modern hitelesítést (például Office 2010-ügyfél)
- Bármely olyan ügyfél, amely örökölt levelezési protokollokat (például IMAP/SMTP/POP3) használ

Napjainkban az összes kiegyezéses bejelentkezési kísérlet többsége örökölt hitelesítésből származik. Az örökölt hitelesítés nem támogatja a többtényezős hitelesítést (MFA). Ha az MFA-szabályzat engedélyezve van a címtárban, a rossz színész egy örökölt protokollal hitelesítheti magát, és kikerülheti az MFA-t. Ha a biztonsági alapértékek engedélyezve vannak a bérlőben, az örökölt protokoll összes hitelesítési kérelmét letiltja a rendszer. A biztonsági alapértelmezett beállítások nem gátolják meg az Exchange ActiveSync szolgáltatást.

### <a name="protecting-privileged-actions"></a>A Kiemelt műveletek védelme

A szervezetek a Azure Resource Manager API-n keresztül felügyelt különböző Azure-szolgáltatásokat használják, beleértve a következőket:

- Azure Portal 
- Azure PowerShell 
- Azure parancssori felület (CLI)

A Azure Resource Manager használata a szolgáltatások kezeléséhez magas jogosultsági szintű művelet. Azure Resource Manager megváltoztathatja a bérlői szintű konfigurációkat, például a szolgáltatás beállításait és az előfizetés számlázását. Az egytényezős hitelesítés számos különböző támadáshoz, például az adathalászathoz és a jelszó-permetezéshez van kitéve. Ezért fontos annak ellenőrzése, hogy a hozzáférés engedélyezése előtt a többtényezős hitelesítés megkövetelése érdekében a felhasználók identitását szeretné-e elérni a Azure Resource Manager és a frissítési konfigurációkhoz.

Ha a biztonsági alapértékek engedélyezve vannak a bérlőben, a Azure Portalhoz, Azure PowerShellhoz vagy az Azure CLI-hez hozzáférő felhasználók a többtényezős hitelesítés végrehajtásához szükségesek. Ez a szabályzat a Azure Resource Managerhoz hozzáférő összes felhasználóra vonatkozik, függetlenül attól, hogy rendszergazda vagy felhasználó. Ha a felhasználó nincs regisztrálva az MFA-ban, akkor a folytatáshoz a felhasználónak regisztrálnia kell a Microsoft Authenticator alkalmazás használatával. A rendszer 14 napos MFA regisztrációs időszakot nem biztosít.

## <a name="deployment-considerations"></a>Telepítési szempontok

Az alábbiakban néhány további szempontot is figyelembe kell venni a bérlő biztonsági alapértékeinek üzembe helyezésével kapcsolatban.

### <a name="legacy-protocols"></a>Örökölt protokollok

A levelezési ügyfelek a korábbi hitelesítési protokollokat (IMAP, SMTP, POP3 stb.) használják a hitelesítési kérések elvégzésére. Ezek a protokollok nem támogatják az MFA-t. A Microsoft által észlelt fiókok többségét az MFA megkerülését megkísérlő örökölt protokollok elleni támadások okozzák. Annak biztosítása érdekében, hogy az MFA-t rendszergazdai fiókba való bejelentkezéskor, a rossz résztvevők ne kerüljék el az MFA-t, a biztonsági alapértékek az örökölt protokollokból származó rendszergazdai fiókokra vonatkozó összes hitelesítési kérelmet blokkolják.

> [!WARNING]
> A beállítás engedélyezése előtt győződjön meg arról, hogy a rendszergazdák nem használnak örökölt hitelesítési protokollokat. További információkért tekintse meg a következő cikket: [Hogyan helyezhető el az örökölt hitelesítésből](concept-conditional-access-block-legacy-authentication.md).

### <a name="conditional-access"></a>Feltételes hozzáférés

A feltételes hozzáférés használatával olyan házirendeket konfigurálhat, amelyek a biztonsági alapértékek által engedélyezett viselkedést biztosítják. Ha feltételes hozzáférést használ, és a környezetében engedélyezve vannak feltételes hozzáférési szabályzatok, a biztonsági beállítások nem lesznek elérhetők az Ön számára. Ha olyan licenccel rendelkezik, amely feltételes hozzáférést biztosít, de nem rendelkezik a környezetében engedélyezett feltételes hozzáférési házirendekkel, akkor a biztonsági Alapértelmezések használata csak a HITELESÍTÉSSZOLGÁLTATÓI házirendek engedélyezése esetén hasznos.

![A biztonsági alapértékek vagy a feltételes hozzáférés nem mindkettő](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Az alábbi lépésről lépésre haladó útmutatók azt ismertetik, hogyan használható a feltételes hozzáférés az egyenértékű házirendek konfigurálásához:

- [MFA megkövetelése rendszergazdák számára](howto-conditional-access-policy-admin-mfa.md)
- [MFA megkövetelése az Azure-felügyelethez](howto-conditional-access-policy-azure-management.md)
- [Örökölt hitelesítés tiltása](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Biztonsági alapértékek engedélyezése

A biztonsági alapértelmezett beállítások engedélyezése a címtárban:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) as a biztonsági rendszergazda, a feltételes hozzáférésű rendszergazda vagy a globális rendszergazda szerepkörrel.
1. Tallózással keresse meg **Azure Active Directory**  @ no__t-2 **Tulajdonságok**
1. Válassza a **biztonsági Alapértelmezések kezelése** lehetőséget.
1. Állítsa a **biztonsági Alapértelmezések engedélyezése** kapcsolót **Igen**értékre.
1. Kattintson a Mentés gombra.

## <a name="next-steps"></a>Következő lépések

[Általános feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)

[Mi a feltételes hozzáférés?](overview.md)
