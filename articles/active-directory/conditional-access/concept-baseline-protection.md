---
title: Feltételes hozzáférés alapházirendjei – Azure Active Directory
description: Alapkonfigurációs feltételes hozzáférési házirendek a szervezetek gyakori támadások elleni védelmére
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767568"
---
# <a name="what-are-baseline-policies"></a>Mik az alapházirendek?

Az alapházirendek olyan előre definiált házirendek, amelyek segítenek megvédeni a szervezeteket számos gyakori támadásellen. Ezek a gyakori támadások közé tartozhat a jelszóspray, a visszajátszás és az adathalászat. Az alapszintű szabályzatok az Azure AD összes kiadásában elérhetők. A Microsoft mindenki számára elérhetővé teszi ezeket az alapszintű védelmi házirendeket, mivel az identitásalapú támadások száma az elmúlt néhány évben nőtt. A négy házirend célja annak biztosítása, hogy minden szervezet rendelkezik az alapszintű biztonsági engedélyezve van, többletköltség nélkül.

A testreszabott feltételes hozzáférési szabályzatok kezeléséhez Azure AD Premium-licenc szükséges.

> [!IMPORTANT]
> Az alapházirendek elavultak. További információaz [Azure Active Directory újdonságai című témakörben](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) található.

## <a name="baseline-policies"></a>Alapvető szabályzatok

![Feltételes hozzáférés alapházirendjei az Azure Portalon](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Négy alapházirend létezik:

* Többkori faszükségtelével kell elévülve rendszergazdáknak (előzetes verzió)
* Végfelhasználói védelem (előzetes verzió)
* Örökölt hitelesítés blokkolása (előzetes verzió)
* Többéves kor–szolgáltatáskezelés megkövetelése (előzetes verzió)

Mind a négy házirend hatással lesz az örökölt hitelesítési folyamatokra, például a POP, az IMAP és a régebbi asztali Office-ügyfelekre.

### <a name="exclusions"></a>Kizárások

Amikor az alapszintű szabályzatok bekerültek a kezdeti nyilvános előzetes verzióba, lehetőség volt kizárni a felhasználókat a szabályzatokból. Ez a képesség az előzetes verzióban fejlődött ki, és 2019 júliusában eltávolították. Azok a szervezetek, amelyek már létrehoztak kizárásokat, továbbra is megtarthatták őket, hogy az új felhasználók ne tudjanak kizárásokat hozzáadni a házirendekhez.

### <a name="require-mfa-for-admins-preview"></a>Többkori faszükségtelével kell elévülve rendszergazdáknak (előzetes verzió)

A rendszergazdai fiókok hatásköre és hozzáférése miatt különös gonddal kell kezelniőket. A kiemelt jogosultságú fiókok védelmének javításának egyik gyakori módja a fiókellenőrzés erősebb formájának megkövetelése a bejelentkezéshez. Az Azure Active Directoryban erősebb fiókellenőrzést kaphat, ha a rendszergazdáknak regisztrálniuk kell az Azure többtényezős hitelesítésre, és használniuk kell azokat.

A rendszergazdák számára az MFA megkövetelése (előzetes verzió) egy olyan alapkonfigurációs szabályzat, amely többtényezős hitelesítést (MFA) igényel a következő címtárszerepkörökhöz, amelyeket a legnagyobb jogosultsággal rendelkező Azure AD-szerepköröknek tekintenek:

* Globális rendszergazda
* SharePoint-rendszergazda
* Exchange-rendszergazda
* Feltételes hozzáférés rendszergazdája
* Biztonsági rendszergazda
* Helpdesk rendszergazda / Jelszó-rendszergazda
* Számlázási rendszergazda
* Rendszergazda

Ha a szervezet ezeket a fiókokat parancsfájlokban vagy kódokban használja, fontolja meg, hogy lecseréli őket [felügyelt identitásokra.](../managed-identities-azure-resources/overview.md)

### <a name="end-user-protection-preview"></a>Végfelhasználói védelem (előzetes verzió)

Nem csak a magas szintű rendszergazdák at célzott támadások. A rossz szereplők általában normál felhasználókat céloznak meg. A hozzáférés megszerzése után ezek a hibás szereplők hozzáférést kérhetnek a kiemelt információkhoz az eredeti fióktulajdonos nevében, vagy letölthetik a teljes könyvtárat, és adathalász támadást hajthatnak végre az egész szervezet ellen. Az egyik gyakori módszer a védelem javítása minden felhasználó számára, hogy szükség van egy erősebb formája a fiók ellenőrzése, ha kockázatos bejelentkezést észlel.

**A végfelhasználói védelem (előzetes verzió)** egy alapkonfiguráció-házirend, amely a címtár összes felhasználóját védi. A szabályzat engedélyezéséhez minden felhasználónak 14 napon belül regisztrálnia kell az Azure többtényezős hitelesítéséhez. A regisztrációt követően a felhasználók csak a kockázatos bejelentkezési kísérletek során kérik az MFA-t. A feltört felhasználói fiókok blokkolva vannak a jelszó alaphelyzetbe állításáig és a kockázat elbocsátását. 

> [!NOTE]
> A korábban kockázatra megjelölt felhasználók a jelszó alaphelyzetbe állításáig és a házirend aktiválásakor az elbocsátás kockázatának megnem jelentek.

### <a name="block-legacy-authentication-preview"></a>Örökölt hitelesítés blokkolása (előzetes verzió)

Az örökölt hitelesítési protokollok (pl. IMAP, SMTP, POP3) olyan protokollok, amelyeket a régebbi levelezőügyfelek általában hitelesítésre használnak. Az örökölt protokollok nem támogatják a többtényezős hitelesítést. Még akkor is, ha a címtárhoz többtényezős hitelesítést igénylő házirenddel rendelkezik, a hibás aktor hitelesítheti magát ezen örökölt protokollok egyikével, és megkerülheti a többtényezős hitelesítést.

A fiók régebbi protokollok által imitátor által imitátor által imitátor által imitátor tól való védelmének legjobb módja, ha letiltja azokat.

Az **örökölt hitelesítés blokkolása (előzetes verzió)** blokkolja az örökölt protokollok használatával végrehajtott hitelesítési kérelmeket. Az összes felhasználó sikeres bejelentkezéséhez modern hitelesítést kell használni. A többi alaptervi házirenddel együtt használva az örökölt protokollokból érkező kérelmek le lesznek tiltva. Ezenkívül minden felhasználónak szükség esetén mfa-t kell igényelnie. Ez a házirend nem blokkolja az Exchange ActiveSync szolgáltatását.

### <a name="require-mfa-for-service-management-preview"></a>Többéves kor–szolgáltatáskezelés megkövetelése (előzetes verzió)

A szervezetek számos Azure-szolgáltatást használnak, és az Azure Resource Manager-alapú eszközökből kezelik őket, például:

* Azure portál
* Azure PowerShell
* Azure CLI

Ezen eszközök bármelyikének használata erőforrás-kezelés végrehajtásához magas szintű kiemelt fontosságú művelet. Ezek az eszközök módosíthatják az előfizetések egészének konfigurációit, például a szolgáltatásbeállításokat és az előfizetési számlázást.

A kiemelt jogosultságú műveletek védelme érdekében ez **a megkövetelése MFA szolgáltatásfelügyeleti (előzetes verzió)** szabályzat többtényezős hitelesítést igényel minden olyan felhasználó, aki az Azure Portalon, az Azure PowerShell vagy az Azure CLI eléréséhez.

## <a name="next-steps"></a>További lépések

További információkért lásd:

* [Biztonsági alapértelmezések engedélyezése](../fundamentals/concept-fundamentals-security-defaults.md)
* [Gyakori feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)
* [Öt lépés az identitás-infrastruktúra biztosításához](../../security/fundamentals/steps-secure-identity.md)
