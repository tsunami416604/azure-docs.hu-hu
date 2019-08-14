---
title: Alapkonfiguráció-házirend blokkolása örökölt hitelesítéssel (előzetes verzió) – Azure Active Directory
description: Az örökölt hitelesítési protokollok blokkolására szolgáló feltételes hozzáférési szabályzat
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 413df402d5acc878130a47821efd5fa3a512257d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931937"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Alapterv házirend: Örökölt hitelesítés blokkolása (előzetes verzió)

Ahhoz, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, Azure Active Directory (Azure AD) a hitelesítési protokollok széles választékát támogatja, beleértve az örökölt hitelesítést is. Az örökölt hitelesítés olyan kifejezés, amely a által végzett hitelesítési kérelemre hivatkozik:

* Régebbi Office-ügyfelek, amelyek nem használnak modern hitelesítést (például Office 2010-ügyfél)
* Bármely olyan ügyfél, amely örökölt levelezési protokollokat (például IMAP/SMTP/POP3) használ

Napjainkban az összes kiegyezéses bejelentkezési kísérlet többsége örökölt hitelesítésből származik. Az örökölt hitelesítés nem támogatja a többtényezős hitelesítést (MFA). Ha az MFA-szabályzat engedélyezve van a címtárban, a rossz színész egy örökölt protokollal hitelesítheti magát, és kikerülheti az MFA-t.

A fióknak az örökölt protokollok által végzett rosszindulatú hitelesítési kérelmekből való védelmének legjobb módja az, hogy letiltja ezeket a kísérleteket. Annak érdekében, hogy megkönnyítse az Ön környezetének védelmét, a rendszer létrehozta ezt az alapkonfiguráció-szabályzatot az örökölt hitelesítés blokkolásához.

Az **örökölt hitelesítés blokkolása** olyan alapkonfigurációs [szabályzat](concept-baseline-protection.md) , amely blokkolja az örökölt protokollok összes hitelesítési kérelmét. A modern hitelesítést minden felhasználó számára sikeres bejelentkezéshez kell használni. A többi alapszabályzattal együtt használatos, az örökölt protokolloktól érkező összes kérés le lesz tiltva, és minden felhasználónak szüksége lesz az MFA-ra, amikor szükséges. Ez a szabályzat nem blokkolja az Exchange ActiveSync szolgáltatást.

## <a name="identify-legacy-authentication-use"></a>Örökölt hitelesítési használat azonosítása

Mielőtt blokkolni tudja az örökölt hitelesítést a címtárban, először meg kell értenie, hogy a felhasználók rendelkeznek-e örökölt hitelesítést használó alkalmazásokkal, és hogy ez milyen hatással van a teljes címtárra. Az Azure AD bejelentkezési naplói segítségével megtudhatja, hogy örökölt hitelesítést használ-e.

1. Navigáljon a **Azure Portal** > **Azure Active Directory** > a**bejelentkezések**elemre.
1. Ha nem látható, akkor adja hozzá az ügyfélalkalmazás oszlopot, ha az **oszlopok** > **ügyfélalkalmazás**elemre kattint.
1. Szűrők > hozzáadása az**ügyfélalkalmazás** > Válassza ki a **többi ügyfél** összes beállítását, és kattintson az **alkalmaz**gombra.

A szűrés csak az örökölt hitelesítési protokollok által végrehajtott bejelentkezési kísérleteket fogja megjeleníteni. Az egyes bejelentkezési kísérletekre kattintva további részleteket is megtudhat. Az alapinformációk lapon található **ügyfélalkalmazás** mező jelzi, hogy melyik örökölt hitelesítési protokollt használta a rendszer.

Ezek a naplók azt jelzik, hogy mely felhasználók maradnak az örökölt hitelesítéstől függően, és mely alkalmazások örökölt protokollokat használnak a hitelesítési kérések elvégzéséhez. Azoknál a felhasználóknál, akik nem jelennek meg a naplókban, és a rendszer megerősíti, hogy nem használ örökölt hitelesítést, implementáljon egy feltételes hozzáférési szabályzatot vagy engedélyezze az alapkonfigurációt **: tiltsa** le az örökölt hitelesítést ezen felhasználók számára.

## <a name="moving-away-from-legacy-authentication"></a>A régi hitelesítéstől való elmozdulás

Ha jobb ötlete van arról, hogy ki örökölt hitelesítést használ a címtárban, és hogy mely alkalmazások függenek tőle, a következő lépés a felhasználókat a modern hitelesítés használatára frissíti. A modern hitelesítés olyan Identitáskezelés-módszer, amely biztonságosabb felhasználói hitelesítést és engedélyezést biztosít. Ha MFA-szabályzattal rendelkezik a címtárban, a modern hitelesítés biztosítja, hogy a felhasználó számára szükség esetén az MFA-t kéri a rendszer. Ez az örökölt hitelesítési protokollok biztonságosabb alternatívája.

Ez a szakasz részletes áttekintést nyújt arról, hogyan frissítheti környezetét modern hitelesítésre. Olvassa el az alábbi lépéseket, mielőtt engedélyezi egy örökölt hitelesítési blokkolási házirendet a szervezetében.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1\. lépés: Modern hitelesítés engedélyezése a címtárban

A modern hitelesítés engedélyezésének első lépéseként győződjön meg arról, hogy a címtár támogatja a modern hitelesítést. A modern hitelesítés alapértelmezés szerint engedélyezve van a 2017. augusztus 1-jén vagy azt követően létrehozott címtárakban. Ha a címtár ezen dátum előtt lett létrehozva, akkor a következő lépések végrehajtásával manuálisan kell engedélyeznie a címtár modern hitelesítését:

1. Ellenőrizze, hogy a címtár már támogatja-e a modern hitelesítést `Get-CsOAuthConfiguration` a [Skype vállalati online PowerShell-modul](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)futtatásával.
1. Ha a parancs üres `OAuthServers` tulajdonságot ad vissza, a modern hitelesítés le van tiltva. Frissítse a beállítást a modern hitelesítés engedélyezéséhez `Set-CsOAuthConfiguration`a használatával. Ha a `OAuthServers` tulajdonság egy bejegyzést tartalmaz, akkor a megfelelő lépés.

A továbblépés előtt mindenképpen hajtsa végre ezt a lépést. Fontos, hogy a címtár-konfigurációk először módosítva legyenek, mivel azt írják elő, hogy az összes Office-ügyfél melyik protokollt fogja használni. Még ha olyan Office-ügyfeleket is használ, amelyek támogatják a modern hitelesítést, alapértelmezés szerint örökölt protokollokat használnak, ha a modern hitelesítés le van tiltva a címtárban.

### <a name="step-2-office-applications"></a>2\. lépés: Office-alkalmazások

Miután engedélyezte a modern hitelesítést a címtárban, az Office-ügyfelek modern hitelesítésének engedélyezésével elkezdheti az alkalmazások frissítését. Az Office 2016 vagy újabb verziójú ügyfelek alapértelmezés szerint támogatják a modern hitelesítést. Nincs szükség további lépésekre.

Ha Office 2013 Windows-ügyfeleket vagy régebbit használ, javasoljuk, hogy frissítsen az Office 2016-es vagy újabb verziójára. A régebbi Office-alkalmazások továbbra is a régi hitelesítési protokollok használatát követően is a modern hitelesítés engedélyezésének befejezése után is elvégezték a korábbi lépéseket. Ha Office 2013-ügyfeleket használ, és nem tud azonnal frissíteni az Office 2016-es vagy újabb verziójára, kövesse a következő cikkben ismertetett lépéseket, hogy [engedélyezze a modern hitelesítést az office 2013](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)-ben Windows-eszközökön. Ha az örökölt hitelesítés használata közben szeretné védetté tenni a fiókját, javasoljuk, hogy használjon erős jelszavakat a címtárban. Tekintse meg az [Azure ad jelszavas védelmet](../authentication/concept-password-ban-bad.md) , amely tiltja a gyenge jelszavakat a címtárban.

Az Office 2010 nem támogatja a modern hitelesítést. Az Office 2010-et használó felhasználókat az Office újabb verziójára kell frissítenie. Javasoljuk, hogy az Office 2016-es vagy újabb verziójára frissítsen, mivel alapértelmezés szerint blokkolja az örökölt hitelesítést.

Ha MacOS-t használ, javasoljuk, hogy frissítsen az Office for Mac 2016 vagy újabb verzióra. Ha a natív levelezési ügyfelet használja, az összes eszközön rendelkeznie kell a MacOS 10,14-es vagy újabb verziójával.

### <a name="step-3-exchange-and-sharepoint"></a>3\. lépés: Exchange és SharePoint

Ahhoz, hogy a Windows-alapú Outlook-ügyfelek modern hitelesítést használjanak, az Exchange Online-nak a modern hitelesítést is engedélyeznie kell. Ha a modern hitelesítés le van tiltva az Exchange Online-hoz, a modern 2013 hitelesítést támogató Windows-alapú Outlook-ügyfelek az alapszintű hitelesítést használják az Exchange Online-postaládákhoz való kapcsolódáshoz.

A SharePoint Online-ban engedélyezve van a modern hitelesítés alapértelmezett értéke. A 2017. augusztus 1. után létrehozott könyvtárak esetében a modern hitelesítés alapértelmezés szerint engedélyezve van az Exchange Online-ban. Ha azonban korábban már letiltotta a modern hitelesítést, vagy a dátum előtt létrehozott könyvtárat használ, kövesse az alábbi cikk lépéseit a [modern hitelesítés engedélyezéséhez az Exchange Online-ban](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>4\. lépés: Skype Vállalati verzió

Ha meg szeretné akadályozni, hogy a Skype vállalati verzió a korábbi hitelesítési kérelmeket használja, a modern hitelesítést engedélyeznie kell a Skype vállalati online verzióhoz. A 2017. augusztus 1. után létrehozott könyvtárak esetében alapértelmezés szerint engedélyezve van a modern hitelesítés a Skype vállalati verzióhoz.

Javasoljuk, hogy térjen át a Microsoft Teams szolgáltatásra, amely alapértelmezés szerint támogatja a modern hitelesítést. Ha azonban jelenleg nem tudja áttelepíteni a rendszert, engedélyeznie kell a modern hitelesítést a Skype vállalati online verzióhoz, hogy a Skype vállalati ügyfelei a modern hitelesítést használják. Kövesse az ebben a cikkben ismertetett, [modern hitelesítéssel támogatott Skype for Business](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)-topológiákat a Skype vállalati verziójának modern hitelesítésének engedélyezéséhez.

A modern hitelesítés a Skype vállalati online verzióban való engedélyezése mellett azt javasoljuk, hogy engedélyezze a modern hitelesítést az Exchange Online-hoz, ha engedélyezi a modern hitelesítést a Skype vállalati verzióban. Ez a folyamat segít a modern hitelesítés állapotának szinkronizálásában az Exchange Online-ban és a Skype vállalati online verzióban, és megakadályozza a Skype vállalati ügyfelek számára a különböző bejelentkezési kéréseket.

### <a name="step-5-using-mobile-devices"></a>5\. lépés: Mobileszközök használata

A mobileszközön lévő alkalmazásoknak le kell tiltaniuk az örökölt hitelesítést is. Javasoljuk, hogy használja az Outlookot a Mobile használatához. Az Outlook for Mobile alapértelmezés szerint támogatja a modern hitelesítést, és a többi MFA alapkonfiguráció-védelmi szabályzatot is kielégíti.

A natív iOS-es levelezési ügyfélprogram használatához az iOS 11,0-es vagy újabb verzióját kell futtatnia ahhoz, hogy a levelezési ügyfél frissítve legyen az örökölt hitelesítés blokkolására.

### <a name="step-6-on-premises-clients"></a>6\. lépés: Helyszíni ügyfelek

Ha olyan hibrid ügyfelet használ, amely a helyszíni Exchange Servert és a Skype vállalati helyszíni szolgáltatást használja, akkor mindkét szolgáltatást frissíteni kell a modern hitelesítés lehetővé tételéhez. A modern hitelesítés hibrid környezetben való használatakor a helyszíni felhasználók továbbra is hitelesítve vannak. Az erőforrásokhoz (fájlokhoz vagy e-mailekhez) való hozzáférés engedélyezésének története.

Mielőtt megkezdené a modern hitelesítés engedélyezését a helyszínen, ellenőrizze, hogy teljesülnek-e az előfeltételek.
Most már készen áll a modern hitelesítés engedélyezésére a helyszínen.

A modern hitelesítés engedélyezésének lépései a következő cikkekben találhatók:

* [A helyszíni Exchange Server konfigurálása hibrid modern hitelesítés használatára](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Modern hitelesítés (ADAL) használata a Skype vállalati verzióban](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Az alapkonfiguráció házirend engedélyezése

A házirend **alapkonfigurációjának házirendje: Az örökölt hitelesítés (előzetes** verzió) blokkolása előre konfigurálva van, és a felső részen jelenik meg, amikor a Azure Portal a feltételes hozzáférés panelre lép. Ez a beállítás csak a sikeres bejelentkezés után lép érvénybe, így a felhasználók továbbra is megpróbálkoznak az örökölt hitelesítés használatával.

A szabályzat engedélyezése és a szervezet megóvása:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. A szabályzatok listájában válassza ki **az alapházirend elemet: Örökölt hitelesítés (előzetes verzió**) letiltása.
1. Állítsa be a házirend **engedélyezése** beállítást a **szabályzat azonnali használatára**.
1. Kattintson a **Mentés**gombra.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [A feltételes hozzáférés alapkonfigurációjának védelmi házirendjei](concept-baseline-protection.md)
* [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md)
* [Mi a feltételes hozzáférés a Azure Active Directory?](overview.md)
