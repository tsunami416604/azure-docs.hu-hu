---
title: A válságkezelési-hozzáférési rendszergazdai fiókok kezelése az Azure ad-ben |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan használhatja a vészelérési fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 185f6730babe077332be9f054ba338ff48295eca
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449887"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>A válságkezelési-hozzáférési rendszergazdai fiókok kezelése az Azure ad-ben 

A legtöbb napi tevékenységek *globális rendszergazdai* jogosultságok nem szükségesek a felhasználók által. Felhasználók nem végleges rendel hozzá a szerepkört, mert előfordulhat, hogy véletlenül általuk egy feladatot, amely kell rendelkezniük, mint a magasabb szintű engedélyekkel kell rendelkeznie. Amikor a felhasználóknak nincs szükségük van ahhoz globális rendszergazdaként, a szerepkör-hozzárendelés az Azure Active Directory (Azure AD) Privileged Identity Management (PIM), vagy a saját fiókjukat, vagy egy másik rendszergazdai fiók használatával kell aktiválnak.

Felhasználók saját maguk tart a felügyeleti hozzáférési jogosultságokat, mellett kell akadályozni, hogy véletlenül zárolva felügyeletének részeként az Azure AD-bérlő kívül is, sem a bejelentkezés sem aktiválásához egy meglévő egyéni felhasználói fiókot, mert egy rendszergazda. Két vagy több tárolásával csökkentheti a rendszergazdai hozzáférés véletlen hiánya hatását *vészelérési fiókok* a bérlőben.

A vészelérési fiókok megkönnyíti a szervezetek számára korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül. Az ilyen fiókok vannak magas szintű jogosultságokkal rendelkeznek, és hogy nincsenek hozzárendelve a megadott személyeknek. A vészelérési fiókok korlátozva, válságkezelési vagy "vészhelyzeti" forgatókönyvek, ahol a szokásos rendszergazdai fiókok nem használható olyan helyzetekben. Szervezetek kell tartania a cél a vészelérési fiókhoz használat korlátozása csak az az időpont, amikor szükség.

Egy szervezet vészelérési fiók használatára a következő esetekben lehet szükség:

 - A felhasználói fiókok összevont, és egy cella – hálózati break vagy identitásszolgáltató kimaradás miatt az összevonási jelenleg nem érhető el. Például ha az identitás szolgáltató állomás a környezetben a korábbihoz, felhasználók lehet nem tud bejelentkezni, ha az identitásszolgáltató az Azure AD átirányítja a felhasználót. 
 - A rendszergazdák regisztrálva vannak az Azure multi-factor Authentication segítségével, és az egyes eszközök nem érhetők el. Lehet, hogy a felhasználók nem lehet végrehajtani a multi-factor Authentication szolgáltatást a szerepkör aktiválásához. Például egy cella hálózatkimaradás miatt nem telefonhívásokat megválaszolását, vagy a szöveges üzenetek, csak két hitelesítési mechanizmusa, akkor a regisztrált eszköz őket. 
 - A legutóbbi globális rendszergazdai jogosultságokkal rendelkező személy elhagyta a vállalatot. Az Azure AD megakadályozza, hogy az utolsó *globális rendszergazdai* -fiók törlése folyamatban van, de nem akadályozza meg a fiók törlése folyamatban van vagy a letiltott helyi. Mindkét esetben előfordulhat, hogy ellenőrizze a szervezet nem lehet helyreállítani a fiókját.

## <a name="initial-configuration"></a>Kezdeti konfiguráció

Hozzon létre két vagy több vészelérési fiókok. Ezek csak felhőalapú fiókok használó kell lennie a \*. onmicrosoft.com tartomány és, nem összevont vagy szinkronizálja a helyszíni környezetben. 

A fiókok nem lehet a szervezet minden egyes felhasználóhoz társítva. Annak biztosítása érdekében, hogy ezek a fiókok hitelesítő adatait biztonságos és ismert használják őket, akik csak a vállalatnál szükség van. 

> [!NOTE]
> Egy fiók jelszavát egy vészelérési fiókok általában két vagy három részre tagolt, különálló darabokra papír azon, és biztonságos, tűzbiztos széf, amely biztonságos, különböző helyeken vannak tárolva. 
>
> Győződjön meg arról, hogy a vészelérési fiókok nem csatlakoznak az összes alkalmazott által megadott mobiltelefonok, hardver jogkivonatok egyes alkalmazottak, vagy más alkalmazott-specifikus hitelesítő adatokat, hogy utazás. Az eszközeikről magában foglalja a példányok, ahol egy alkalmazott nem érhető el, ha a hitelesítő adat van szükség. 

### <a name="initial-configuration-with-permanent-assignments"></a>Az állandó hozzárendelés kezdeti konfiguráció

Az egyik lehetőség, hogy a felhasználók állandó tagjai a *globális rendszergazdai* szerepkör. Ez a beállítás akkor lehet megfelelő, szervezetek, amelyek nem rendelkeznek Azure AD Premium P2-előfizetéseket.

Biztonsági kockázatot jelentő jelszó adódó támadások kockázatának csökkentése érdekében, az Azure AD azt javasolja, hogy a multi-factor Authentication hitelesítést minden egyes felhasználó számára szükséges. Ez a csoport tartalmaznia kell a rendszergazdák és minden egyéb (például a pénzügyi igazgatók) feltört fiók jelentős hatással lenne. 

Azonban ha a szervezet nem rendelkezik megosztott eszközök, a multi-factor Authentication szolgáltatás előfordulhat, hogy nem lehetséges a vészelérési fiókok. Ha konfigurál egy feltételes hozzáférési szabályzat megkövetelése [többtényezős hitelesítési regisztráció minden rendszergazda](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) Azure AD-hez és a kapcsolódó egyéb szoftver (SaaS) szolgáltatottszoftver-alkalmazásokat, szüksége lehet a házirend konfigurálása Ez a követelmény a vészelérési fiókok kizárását kizárások.

### <a name="initial-configuration-with-approvals"></a>Jóváhagyások használata a kezdeti konfiguráció

Egy másik lehetőség az, hogy a felhasználók konfigurálása jogosult és aktiválása a jóváhagyók a *globális rendszergazdai* szerepkör. Ez a beállítás megköveteli a szervezet Azure AD Premium P2-előfizetéssel rendelkezik. Azt is megköveteli a multi-factor Authentication beállítás, amely lehetővé teszi több szolgáltatásra és a hálózati környezet között megosztott használatra való. Ezek a követelmények azért, mert az aktiválás a *globális rendszergazdai* a szerepkör megköveteli a felhasználók számára korábban végzett a multi-factor Authentication. További információkért lásd: [az Azure AD Privileged Identity Management többtényezős hitelesítés kötelezővé tétele](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

A vészelérési fiókok személyes eszközök társított multi-factor Authentication használatát nem javasoljuk. Tényleges vészhelyzet esetén a személy, aki egy multi-factor Authentication regisztrált eszközt hozzá kell férnie a személyes eszközt rendelkező egy nem feltétlenül. 

Is figyelembe kell venni a fenyegetést. Például egy előre nem látható függött, például a sürgős természeti katasztrófa esetén előfordulhat, hogy merülnek fel, amely során a mobiltelefon vagy egyéb hálózatok előfordulhat, hogy nem érhető el. Fontos annak biztosítása érdekében, hogy regisztrált eszköze, amely rendelkezik az Azure AD-vel való kommunikációhoz több azt jelenti, hogy ismert, biztonságos helyen.

## <a name="ongoing-monitoring"></a>Folyamatos figyelés

A figyelő a [naplózza az Azure AD-be, és a naplózási](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) tartozó bármely bejelentkezések és naplózási tevékenységet a válságkezelési-hozzáférési fiókokat. Rendszerint ezeket a fiókokat nem kell jelentkezik be, és kell nem lehet változtatásokat, így használata, valószínű, hogy a rendellenes és biztonsági vizsgálat szükséges.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Fiók-ellenőrzési érvényesítési rendszeres időközönként kell észlelnie.

A fiók érvényesítéséhez, hajtsa végre, legalább a következő lépéseket:
- 90 naponta.
- Ha az informatikai részleg, például egy feladat módosítása, egy indító vagy egy új felvételi legutóbbi változás történt.
- Ha a szervezet Azure AD-előfizetések is megváltoztak.

A vészelérési fiókok használata a munkatársak betanítást, tegye a következőket:

* Győződjön meg arról, hogy a személyzet biztonsági monitoringgal vegye figyelembe, hogy a fiók-ellenőrzési tevékenység folyamatban.
* Ellenőrizze, hogy a felhőalapú felhasználói fiókok jelentkezzen be, és saját szerepkörök aktiválása és, hogy a folyamat során vészhelyzet esetén a következő lépésekkel esetleg kívánó felhasználók tanulnak.
* Győződjön meg arról, hogy azok nem regisztrált multi-factor Authentication vagy az önkiszolgáló jelszó-visszaállítás (SSPR) minden egyes felhasználó eszközére vagy szermélyi adatok. 
* Ha a fiókok a multi-factor Authentication regisztrált eszközre, szerepkör-aktiválás közben győződjön meg arról, hogy az eszköz érhető el minden olyan rendszergazdák, akik rendkívüli során használandó igényelhet. Emellett győződjön meg arról, hogy az eszköz regisztrálva van-e, hogy ne ossza meg egy közös hibaállapot legalább két mechanizmusokon keresztül. Például az eszköz képes kommunikálni az internethez a létesítmény vezeték nélküli hálózat és a egy cella szolgáltató hálózaton keresztül.
* Frissítse a fiók hitelesítő adatait.

## <a name="next-steps"></a>További lépések
- [Adjon hozzá egy felhőalapú felhasználói](../fundamentals/add-users-azure-active-directory.md) és [az új felhasználó hozzárendelése a globális rendszergazdai szerepkör](../fundamentals/active-directory-users-assign-role-azure-portal.md).
- [Regisztráljon az Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md), ha még nem regisztrált már.
- [Az Azure többtényezős hitelesítés megkövetelése az egyes felhasználók rendszergazdák hozzárendelt](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [A globális rendszergazdák erősebb védelmét konfigurálja az Office 365-ben](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), ha Office 365-höz használ.
- [A globális rendszergazdák hozzáférési felülvizsgálat végrehajtása](../active-directory-privileged-identity-management-how-to-start-security-review.md) és [pontosabb rendszergazdai szerepkörök meglévő globális rendszergazda az átmenet](directory-assign-admin-roles.md).


