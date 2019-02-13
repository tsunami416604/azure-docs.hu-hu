---
title: A vészelérési fiókok kezelése az Azure ad-ben |} A Microsoft Docs
description: Ez a cikk ismerteti a vészelérési fiókok segítségével megakadályozhatja, hogy véletlenül zárolva kívül az Azure Active Directory (Azure AD) bérlő.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/21/2018
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d2a47360808c7c1c36d5e82b98582d9f6144fa
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176951"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>A vészelérési fiókok kezelése az Azure ad-ben

Fontos, mert nem jelentkezzen be vagy egy meglévő egyedi felhasználói fiók rendszergazdai aktiválása véletlenül kívül az Azure Active Directory (Azure AD) bérlő zárolva megakadályozza. Két vagy több létrehozásával csökkentheti a rendszergazdai hozzáférés véletlen hiánya hatását *vészelérési fiókok* a bérlőben.

A vészelérési fiókok vannak magas szintű jogosultságokkal rendelkeznek, és hogy nincsenek hozzárendelve a megadott személyeknek. A vészelérési fiókok korlátozva, válságkezelési vagy "vészhelyzeti" forgatókönyvek, ahol a szokásos rendszergazdai fiókok nem használható. Szervezetek kell tartania a cél a vészelérési fiókhoz használat korlátozása csak azokat az időpontokat, amikor nem feltétlenül szükséges.

Ez a cikk útmutatást nyújt a a vészelérési fiókok kezelése az Azure ad-ben.

## <a name="when-would-you-use-an-emergency-access-account"></a>Mikor használható vészhelyzeti hozzáférési fiókot?

Egy szervezet vészelérési fiók használatára a következő esetekben lehet szükség:

- A felhasználói fiókok összevont, és egy cella – hálózati break vagy identitásszolgáltató kimaradás miatt az összevonási jelenleg nem érhető el. Például ha az identitás szolgáltató állomás a környezetben a korábbihoz, felhasználók lehet nem tud bejelentkezni, ha az identitásszolgáltató az Azure AD átirányítja a felhasználót.
- Azure multi-factor Authentication révén a rendszergazdák regisztrált és nem érhető el az egyes eszközöket, vagy a szolgáltatás nem érhető el. Lehet, hogy a felhasználók nem lehet végrehajtani a multi-factor Authentication szolgáltatást a szerepkör aktiválásához. Például egy cella hálózatkimaradás miatt nem telefonhívásokat megválaszolását, vagy a szöveges üzenetek, csak két hitelesítési mechanizmusa, akkor a regisztrált eszköz őket.
- A legutóbbi globális rendszergazdai hozzáféréssel rendelkező személy elhagyta a vállalatot. Az Azure AD megakadályozza, hogy a legutolsó globális rendszergazdai fiók törlése folyamatban van, de nem akadályozza meg a fiók törlése folyamatban van vagy a letiltott helyi. Mindkét esetben előfordulhat, hogy ellenőrizze a szervezet nem lehet helyreállítani a fiókját.
- Előre nem látható körülmények között például a sürgős, természeti katasztrófa során, ami a mobiltelefon vagy egyéb hálózatok előfordulhat, hogy nem érhető el. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Két felhőalapú vészelérési fiókok létrehozása

Hozzon létre két vagy több vészelérési fiókok. Ezek a fiókok használata kizárólag felhőalapú fiókok kell lennie a \*. onmicrosoft.com tartomány és, nem összevont vagy szinkronizálja a helyszíni környezetben.

Ezek a fiókok konfigurálásakor az alábbi követelményeknek kell teljesülniük:

- A vészelérési fiókok nem lehet a szervezet minden egyes felhasználóhoz társítva. Győződjön meg arról, hogy a fiókok nem csatlakoznak az összes alkalmazott által megadott mobiltelefonok, hardver jogkivonatok egyes alkalmazottak, vagy más alkalmazott-specifikus hitelesítő adatokat, hogy utazás. Az eszközeikről magában foglalja a példányok, ahol egy alkalmazott nem érhető el, ha a hitelesítő adat van szükség. Fontos annak biztosítása érdekében, hogy regisztrált eszköze, amely rendelkezik az Azure AD-vel való kommunikációhoz több azt jelenti, hogy ismert, biztonságos helyen.
- A vészelérési fiókhoz használt hitelesítési mechanizmust kell lennie a többi rendszergazdai fiókok, beleértve a más vészelérési fiókok által használt különböző.  Például ha a szokásos rendszergazdai bejelentkezés a helyszíni MFA-n keresztül, majd az Azure MFA lenne egy másik mechanizmust.  Azonban ha az Azure MFA része az elsődleges hitelesítés a rendszergazdai fiókok, majd fontolja meg egy másik megközelítés ezeket, például a feltételes hozzáférés segítségével egy külső MFA-szolgáltató.
- Az eszköz vagy a hitelesítő adat nem jár le és használja hiánya miatt az automatikus tisztítás hatókörében kell.  
- Gondoskodnia kell, a globális rendszergazdai szerepkör-hozzárendelés állandó a vészelérési fiókok. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Zárja ki legalább egy fiókot a telefonon alapuló multi-factor Authentication használatát

Biztonsági kockázatot jelentő jelszó adódó támadások kockázatának csökkentése érdekében, az Azure AD azt javasolja, hogy a multi-factor Authentication hitelesítést minden egyes felhasználó számára szükséges. Ez a csoport tartalmazza a rendszergazdák és a többi (például a pénzügyi igazgatók) feltört fiók jelentős hatással lenne.

A vészelérési fiókok legalább egyike, nem kell ugyanazt a multi-factor authentication szolgáltatás mechanizmust, az egyéb nem sürgős fiókok. Ez magában foglalja a harmadik felek többtényezős hitelesítési megoldásokat. Ha rendelkezik megkövetelő feltételes hozzáférési szabályzata [többtényezős hitelesítés minden rendszergazda](../authentication/howto-mfa-userstates.md) az Azure ad és más csatlakoztatott szoftverfrissítési-szoftverszolgáltatások (SaaS) alkalmazásokat kell kizárni a vészelérési fiókok Ez követelmény, és a egy másik mechanizmust állítani helyette. Ezenkívül győződjön meg arról, hogy a fiókok nem rendelkeznek a felhasználónkénti többtényezős hitelesítési házirend.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Legalább egy fiókot kizárása a feltételes hozzáférési szabályzatok

Vészhelyzetben nem szeretné, hogy olyan szabályzatot, amely potenciálisan a blokkolhatja megoldani a problémát. Legalább egy vészelérési fiókok minden feltételes hozzáférési szabályzatokat kell zárni. Ha engedélyezte a [alapvető házirendet](../conditional-access/baseline-protection.md), ki kell zárnia a vészelérési fiókok.

## <a name="additional-guidance-for-hybrid-customers"></a>További útmutató a hibrid ügyfelek

Egy további lehetőséget AD tartományi szolgáltatások és az AD FS-t használó szervezetek vagy hasonló identitásszolgáltató összevonásához az Azure AD-ben, hogy egy vészelérési fiókok, amelynek MFA-jogcím sikerült szolgáltatja a identitásszolgáltató konfigurálása.  A vészelérési fiókok például, például egy intelligens kártyán tárolt tanúsítvány és kulcs két sikerült készíteni.  Ha az ad-hez, hogy a felhasználó hitelesítése AD FS megadhatja egy jogcímet, az Azure ad-ben, amely jelzi, hogy a felhasználó teljesítette az MFA-követelmények.  Akkor is ezt a módszert használja, a szervezetek továbbra is rendelkeznie kell felhőalapú vészelérési fiókok abban az esetben nem hozható létre összevonási. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Eszközök és a hitelesítő adatok Store egy biztonságos helyre

Annak biztosítása érdekében, hogy a vészelérési fiókok hitelesítő adatait biztonságos és ismert használják őket, akik csak a vállalatnál szükség van. Egyes ügyfelek intelligens kártya és más jelszót használni. Egy a vészelérési fiókhoz tartozó jelszó általában két vagy három részre tagolt, különálló darabokra papír azon, és biztonságos, tűzbiztos széf, amely biztonságos, különböző helyeken vannak tárolva.

Jelszavak használata esetén győződjön meg, hogy a fiókok, amelyek nem járnak le a jelszó erős jelszavakat. Ideális esetben a jelszavak hosszú és véletlenszerűen előállított legalább 16 karakter hosszúnak kell lennie.


## <a name="monitor-sign-in-and-audit-logs"></a>Jelentkezzen be figyelése és naplók

A figyelő a [naplózza az Azure AD-be, és a naplózási](../reports-monitoring/concept-sign-ins.md) az minden olyan bejelentkezések és naplózási tevékenységet a vészelérési fiókok. Ezek a fiókok normális esetben nem kell jelentkezik be, és kell nem lehet változtatásokat, így használata, valószínű, hogy a rendellenes és biztonsági vizsgálat szükséges.

## <a name="validate-accounts-at-regular-intervals"></a>Rendszeres időközönként fiókok ellenőrzése

A vészelérési fiókok használhatja, és ellenőrizze a vészelérési fiókok stáblistája betanítást, hajtsa végre a következő minimális lépéseket rendszeres időközönként:

- Győződjön meg arról, hogy a személyzet biztonsági monitoringgal vegye figyelembe, hogy a fiók-ellenőrzési tevékenység folyamatban.
- Győződjön meg arról, hogy ezeket a fiókokat, a vészhelyzeti break üveg folyamat dokumentált és az aktuális.
- Győződjön meg arról, hogy a rendszergazdák és a biztonsági tisztviselők, akik előfordulhat, hogy ezeket a lépéseket vészhelyzetben tanított-e a folyamat.
- Frissítse a fiók hitelesítő adatait, különösen jelszavakat, a vészelérési fiókok, és majd ellenőrizze, hogy a vészelérési fiókok is be- és felügyeleti feladatokat hajthat végre.
- Győződjön meg arról, hogy felhasználók nem regisztrált multi-factor Authentication vagy az önkiszolgáló jelszó-visszaállítás (SSPR) minden egyes felhasználó eszközére vagy szermélyi adatok. 
- Ha a fiókok a multi-factor Authentication regisztrált eszközre, jelentkezzen be, vagy a szerepkör aktiválása során használatra győződjön meg arról, hogy az eszköz érhető el minden olyan rendszergazdák, akik rendkívüli során használandó igényelhet. Győződjön meg arról is, hogy az eszköz legalább két hálózati elérési úttal, hogy ne ossza meg egy közös hibaállapot keresztül kommunikálhassanak. Például az eszköz képes kommunikálni az internethez a létesítmény vezeték nélküli hálózat és a egy cella szolgáltató hálózaton keresztül.

Ezeket a lépéseket kell elvégezni, rendszeres időközönként, és a legfontosabb változások:

- Legalább 90 naponként
- Ha az informatikai részleg, például egy feladat módosítása, egy indító vagy egy új felvételi legutóbbi változás történt
- Ha a szervezet Azure AD-előfizetések változásai

## <a name="next-steps"></a>További lépések

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](directory-admin-roles-secure.md)
- [Adja hozzá a felhasználók Azure AD-vel](../fundamentals/add-users-azure-active-directory.md) és [az új felhasználó hozzárendelése a globális rendszergazdai szerepkör](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Regisztráljon az Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), ha még nem regisztrált már
- [Egy felhasználó kétlépéses ellenőrzést igénylése](../authentication/howto-mfa-userstates.md)
- [A globális rendszergazdák erősebb védelmét konfigurálja az Office 365-ben](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), ha Office 365-höz használ.
- [A globális rendszergazdák hozzáférési felülvizsgálat indítása](../privileged-identity-management/pim-how-to-start-security-review.md) és [áttérés meglévő globális rendszergazdák pontosabb rendszergazdai szerepkörök](directory-assign-admin-roles.md)
