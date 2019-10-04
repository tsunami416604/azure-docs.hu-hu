---
title: A feltételes hozzáférési alapkonfiguráció védelmi házirendjei – Azure Active Directory
description: Alapkonfiguráció feltételes hozzáférési szabályzatok a szervezetek számára a gyakori támadásokkal szembeni védelem érdekében
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
ms.openlocfilehash: 572371f4abec413be5a2320c7d69d8126f26924f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533052"
---
# <a name="what-are-baseline-policies"></a>Mik azok az alapkonfigurációs házirendek?

Az alapkonfigurációs házirendek olyan előre definiált szabályzatok, amelyek számos gyakori támadás elleni védelmet nyújtanak a szervezeteknek. Ezek a gyakori támadások például a jelszó-és a visszajátszást, valamint az adathalászatot is tartalmazhatják. Az alapkonfiguráció házirendjei az Azure AD összes kiadásában elérhetők. A Microsoft ezen alapkonfiguráció-védelmi szabályzatokat mindenki számára elérhetővé teszi, mivel az identitás-alapú támadások az elmúlt néhány évben növekedtek. Ennek a négy házirendnek a célja annak biztosítása, hogy az összes szervezet külön költség nélkül engedélyezze az alapszintű biztonsági szintet.  

A testreszabott feltételes hozzáférési szabályzatok kezeléséhez prémium szintű Azure AD licenc szükséges.

## <a name="baseline-policies"></a>Alapvető szabályzatok

![A feltételes hozzáférési alapszabályzatok a Azure Portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

A szervezetek négy alapszabályzattal rendelkezhetnek, amelyek lehetővé teszik a következőket:

* [MFA megkövetelése a rendszergazdák számára (előzetes verzió)](howto-baseline-protect-administrators.md)
* [Végfelhasználói védelem (előzetes verzió)](howto-baseline-protect-end-users.md)
* [Örökölt hitelesítés blokkolása (előzetes verzió)](howto-baseline-protect-legacy-auth.md)
* [MFA megkövetelése a Service Management szolgáltatáshoz (előzetes verzió)](howto-baseline-protect-azure.md)

Ezeknek a szabályzatoknak mind a négye befolyásolja az örökölt hitelesítési folyamatokat, például a POP-, az IMAP-és a régebbi Office asztali ügyfeleket.

### <a name="require-mfa-for-admins-preview"></a>MFA megkövetelése a rendszergazdák számára (előzetes verzió)

A rendszergazdai fiókok teljesítményének és hozzáférésének köszönhetően különleges gondossággal kezelheti őket. A rendszerjogosultságú fiókok védelmének egyik gyakori módszere, ha a bejelentkezéshez a fiókok ellenőrzésének erősebb formáját igényli. Azure Active Directory az Azure multi-Factor Authentication regisztrálásához és használatához a rendszergazdának be kell szereznie egy erősebb fiók ellenőrzését.

Az [MFA a rendszergazdák számára (előzetes verzió)](howto-baseline-protect-administrators.md) olyan alapszintű szabályzat, amely a következő címtárbeli szerepkörökhöz szükséges többtényezős HITELESÍTÉST (MFA) igényli, amely a legalkalmasabb Azure ad-szerepköröknek tekintendő:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférési rendszergazda
* Biztonsági rendszergazda
* Segélyszolgálat rendszergazdája/jelszó-rendszergazda
* Számlázási adminisztrátor
* Felhasználói adminisztrátor

Ha a szervezete ezeket a fiókokat parancsfájlokban vagy kódban használja, érdemes lehet a [felügyelt identitásokkal](../managed-identities-azure-resources/overview.md)helyettesíteni őket.

### <a name="end-user-protection-preview"></a>Végfelhasználói védelem (előzetes verzió)

A magas jogosultsági szintű rendszergazdák nem csupán a támadásokat célozzák meg. A hibás szereplők általában a normál felhasználókat célozzák meg. A hozzáférés megszerzése után ezek a rossz szereplők az eredeti fiók tulajdonosának nevében igényelhetnek hozzáférést a privilegizált információhoz, vagy le is tölthetik a teljes könyvtárat, és elvégezhetik az adathalászat elleni támadást a teljes szervezetben. Az egyik gyakori módszer az, hogy javítsa az összes felhasználó védelmét, ha a rendszer kockázatos bejelentkezést észlel, a fiókok ellenőrzésének erősebb formája szükséges.

A **végfelhasználói védelem (előzetes verzió)** egy alapkonfigurációs szabályzat, amely a címtár összes felhasználóját védi. Ennek a szabályzatnak az engedélyezéséhez az összes felhasználónak 14 napon belül regisztrálnia kell az Azure multi-Factor Authentication szolgáltatásban. A regisztrációt követően a rendszer csak a kockázatos bejelentkezési kísérletek során kéri a felhasználókat az MFA-ra. A rendszer letiltja a feltört felhasználói fiókokat, amíg a jelszó alaphelyzetbe nem áll, és a kockázat elbocsátás 

[!NOTE]
A korábban a kockázatra megjelölt felhasználókat a rendszer letiltja, amíg a jelszó alaphelyzetbe állítása és a kockázat elbocsátása a házirend aktiválása után megtörténik

### <a name="block-legacy-authentication-preview"></a>Örökölt hitelesítés blokkolása (előzetes verzió)

Örökölt hitelesítési protokollok (pl.: Az IMAP, az SMTP, a POP3) az a protokoll, amelyet általában a régebbi levelezési ügyfelek használnak a hitelesítéshez. Az örökölt protokollok nem támogatják a többtényezős hitelesítést. Ha a címtárhoz többtényezős hitelesítést igénylő szabályzat is tartozik, a rossz színész a következő örökölt protokollok egyikével képes hitelesíteni a többtényezős hitelesítést.

A legjobb módszer, ha a fiókját az örökölt protokollok által kezdeményezett rosszindulatú hitelesítési kérésekkel szeretné védelemmel ellátni.

Az **örökölt hitelesítés (előzetes verzió)** alapkonfiguráció házirendje blokkolja az örökölt protokollok használatával létrehozott hitelesítési kérelmeket. A modern hitelesítést minden felhasználó számára sikeres bejelentkezéshez kell használni. A többi alapszabályzattal együtt használva a rendszer letiltja az örökölt protokolloktól érkező kérelmeket. Emellett minden felhasználónak szüksége lesz az MFA-ra, amikor szükséges. Ez a szabályzat nem blokkolja az Exchange ActiveSync szolgáltatást.

### <a name="require-mfa-for-service-management-preview"></a>MFA megkövetelése a Service Management szolgáltatáshoz (előzetes verzió)

A szervezetek különböző Azure-szolgáltatásokat használnak és felügyelik azokat Azure Resource Manager-alapú eszközökről, például:

* Azure Portal
* Azure PowerShell
* Azure CLI

Ezen eszközök bármelyikének használata az erőforrás-kezelés végrehajtásához magas jogosultsági szintű művelet. Ezek az eszközök megváltoztathatják az előfizetésre kiterjedő konfigurációkat, például a szolgáltatás beállításait és az előfizetés számlázását.

Az emelt szintű jogosultságok elleni védelem érdekében az MFA-t **a Service Management (előzetes verzió)** szabályzatához a többtényezős hitelesítés megkövetelése minden olyan felhasználó számára, aki hozzáfér Azure Portalhoz, Azure PowerShellhoz vagy Azure CLI-hez.

## <a name="enable-a-baseline-policy"></a>Alapterv engedélyezése

Alapházirend engedélyezése:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory** > a**feltételes hozzáférést**.
1. A szabályzatok listájában válassza ki az engedélyezni kívánt alaptervet.
1. **Engedélyezze a házirend engedélyezése beállítást** **a**következőre:.
1. Kattintson a Mentés gombra.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md)
* [Mi a feltételes hozzáférés a Azure Active Directory?](overview.md)
* [MFA megkövetelése a rendszergazdák számára (előzetes verzió)](howto-baseline-protect-administrators.md)
* [Végfelhasználói védelem (előzetes verzió)](howto-baseline-protect-end-users.md)
* [Örökölt hitelesítés blokkolása (előzetes verzió)](howto-baseline-protect-legacy-auth.md)
* [MFA megkövetelése a Service Management szolgáltatáshoz (előzetes verzió)](howto-baseline-protect-azure.md)
