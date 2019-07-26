---
title: Mi a Azure Active Directory Identity Protection (frissítve)? | Microsoft Docs
description: Mi a Azure Active Directory Identity Protection (frissítve)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5d82f665cc837e5dbd2d7efbf16c7ae94e1b2b7
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333919"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Mi a Azure Active Directory Identity Protection (frissítve)?

Az Identity Protection-élmény frissült a szervezet identitásának jobb védelme érdekében. Ez a frissített élmény a következőket biztosítja:

- Az átalakított felügyeleti felület, amely a leginkább fontos kockázati tényező körül forog – a felhasználói kockázat és a bejelentkezés kockázata
- Hatékony vizsgálatok a szűrési, rendezési és intelligens letöltések támogatásával
- Továbbfejlesztett felhasználói kockázati számítás, amely segít rangsorolni a nagy valószínűséggel veszélyeztetett felhasználókra vonatkozó erőfeszítéseket
- Új API-támogatás a kockázati adatszolgáltatásokhoz való programozott hozzáférés engedélyezéséhez
- Egyszerűsített rendszergazdai visszajelzési folyamat, amely lehetővé teszi a felhasználók azonnali védelemmel való ellátását
- Új felügyelt gépi tanulás a kockázatértékelések pontosságának javításához

A biztonság kiemelten fontos a szervezetek számára. A biztonsági rések többsége akkor kerül sor, amikor a támadók a felhasználó identitásának ellopásával hozzáférnek a környezetekhez. Az évek során a támadók egyre hatékonyabban hasznosítják a harmadik féltől származó szabálysértéseket és kifinomult adathalászat-támadásokat használnak. Amint a támadók még alacsony jogosultsági szintű felhasználói fiókokhoz férnek hozzá, viszonylag könnyen hozzáférhetnek a fontos vállalati erőforrásokhoz az oldalirányú mozgással. 

A fenyegetésekre való reagáláshoz Azure AD Identity Protection a következőket teszi lehetővé: 

- Proaktív módon megakadályozhatja a feltört identitások visszaélését 
- A kockázat automatikus csökkentése a gyanús tevékenységek észlelésekor 
- Kockázatos felhasználók és bejelentkezések vizsgálata a lehetséges sebezhetőségek orvoslására  
- Riasztást kap, ha egy felhasználó kockázata eléri a megadott küszöbértéket 

Azure AD Identity Protection a prémium szintű Azure Active Directory P2 egyik funkciója, amely lehetővé teszi, hogy a szabályzatok konfigurálásával automatikusan válaszoljanak a felhasználó személyazonosságának sérülése esetén, vagy ha a fiók tulajdonosán kívül más személy is megpróbál bejelentkezni identitás. Ezek a szabályzatok az Azure AD által biztosított egyéb feltételes hozzáférés-vezérlések mellett automatikusan letilthatják a hozzáférést vagy enyhítő műveleteket kezdeményeznek, például a jelszó-visszaállítást vagy a többtényezős hitelesítés kényszerítését. Emellett a személyazonosság védelme olyan figyelési és jelentéskészítési képességeket biztosít, amelyek mélyebb betekintést nyújtanak a szervezeten belüli kockázatokra és potenciális veszélyekre. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Kockázati események

Azure AD Identity Protection észleli a következő kockázati eseményeket: 

| Kockázati esemény típusa | Leírás | Észlelés típusa |
| --- | --- | --- |
| Szokatlan útvonal | Jelentkezzen be egy atipikus helyről a felhasználó legutóbbi bejelentkezései alapján. | Offline |
| Névtelen IP cím | Bejelentkezés névtelen IP-címről (például: Tor-böngésző, névtelenül VPN-ek). | Valós idejű |
| Szokatlan bejelentkezési tulajdonságok | Jelentkezzen be a nemrégiben nem látott tulajdonságokkal az adott felhasználó számára. | Valós idejű |
| Kártevő szoftverrel összekapcsolt IP-cím | Bejelentkezés kártevővel összekapcsolt IP-címről | Offline |
| Kiszivárgott hitelesítő adatok | Ez a kockázati esemény azt jelzi, hogy a felhasználó érvényes hitelesítő adatai kiszivárogtak | Offline |

## <a name="types-of-risk"></a>Kockázati típusok 

Az Identity Protection két típusú kockázati tényezőn alapul:

- Bejelentkezési kockázat
- Felhasználói kockázat

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat azt jelzi, hogy egy adott hitelesítési kérelem nem jogosult az identitás tulajdonosa számára.

A bejelentkezési kockázat két értékelésből áll: 

- **Bejelentkezési kockázat (valós idejű)** – a bejelentkezési kockázat (valós idejű) az összes olyan valós idejű észlelésen alapul, amely a bejelentkezés feldolgozásakor aktiválódik.  
- **Bejelentkezési kockázat (aggregált)** – a bejelentkezési kockázat (aggregált) a bejelentkezés teljes kockázata. Ezt a gépi tanulási modell számítja ki, amely a következőket veszi figyelembe:
   - Valós idejű észlelések (fent leírt)
   - Offline észlelések (a bejelentkezést követően a tűz) 
   - A bejelentkezés minden egyéb funkciója

### <a name="user-risk"></a>Felhasználói kockázat

A felhasználói kockázat azt jelzi, hogy egy adott identitás biztonsága sérült. 

A felhasználói kockázat kiszámítása a felhasználóval kapcsolatos összes kockázat figyelembevételével történik:

- Minden kockázatos bejelentkezés
- A bejelentkezéshez nem kapcsolódó összes kockázati esemény
- Az aktuális felhasználói kockázat
- A felhasználó által a dátumig végrehajtott összes kockázati szervizelési vagy elbocsátási művelet

## <a name="how-identity-protection-detects-risk"></a>Az Identity Protection által észlelt kockázat  

Az Azure AD gépi tanulással észleli a rendellenességeket és a gyanús tevékenységeket, a bejelentkezések során valós időben észlelt mindkét jelet, valamint a felhasználókhoz és a bejelentkezési tevékenységekhez kapcsolódó nem valós idejű jelzéseket. Ezeknek az adatoknak a használatával az Identity Protection minden alkalommal, amikor egy felhasználó hitelesíti magát, kiszámítja a valós idejű bejelentkezési kockázatokat, valamint meghatározza az egyes felhasználók általános felhasználói kockázati szintjét. Az Identity Protection lehetővé teszi, hogy automatikusan végrehajtsa a kockázatok észlelését az Identity Protection felhasználói kockázatok és a bejelentkezési kockázati házirendek konfigurálásával.  

Annak megismeréséhez, hogy az Identity Protection Hogyan észleli a kockázatokat, két fontos fogalom létezik: a felhasználói kockázat és a bejelentkezési kockázat. A bejelentkezési kockázat azt jelzi, hogy egy adott hitelesítési kérelem nem jogosult az identitás tulajdonosa számára. A bejelentkezési kockázatok két típusa létezik: valós idejű és teljes. A rendszer a megadott bejelentkezési kísérlet idején (például a névtelen IP-címekről érkező bejelentkezések) a valós idejű bejelentkezési kockázatot észleli. A teljes bejelentkezési kockázat az észlelt valós idejű bejelentkezési kockázatok összesített száma, valamint a felhasználó bejelentkezési moduljaihoz (például a lehetetlen utazáshoz) kapcsolódó, nem valós idejű kockázati események összessége. A felhasználói kockázat azt a teljes valószínűséget tükrözi, hogy egy rossz szereplő egy adott identitást feltört. A felhasználói kockázat egy adott felhasználó összes kockázati tevékenységét tartalmazza, beleértve a következőket:

- Valós idejű bejelentkezési kockázat
- Későbbi bejelentkezési kockázat
- Kockázatos felhasználói észlelések.   

 ![Folyamat](./media/overview-v2/01.png)

A fenti ábrán összefoglaljuk az Identitáskezelés kockázatának észlelésére és az adott bejelentkezésre adott válaszra vonatkozó alapfolyamatot.  

## <a name="common-scenarios"></a>Gyakori forgatókönyvek 

Nézzük meg a contoso alkalmazottjának példáját. 

1. Egy alkalmazott megpróbál bejelentkezni az Exchange Online-ba a Tor-böngészőből. A bejelentkezés időpontjában az Azure AD valós idejű kockázati eseményeket észlel. 
2. Az Azure AD észleli, hogy az alkalmazott névtelen IP-címről jelentkezik be, és egy közepes bejelentkezési kockázati szintet indít el. 
3. Az alkalmazottat egy MFA-kérés kéri, mert a contoso rendszergazdája konfigurálta az Identity Protection bejelentkezési kockázat feltételes hozzáférési szabályzatát. A szabályzat megköveteli az MFA használatát közepes vagy magasabb bejelentkezési kockázat esetén. 
4. Az alkalmazott átadja az MFA-kérést, és hozzáfér az Exchange Online-hoz, és a felhasználói kockázati szintje nem módosul. 

Mi történt a színfalak mögött? A Tor-böngésző bejelentkezési kísérlete valós idejű bejelentkezési kockázatot váltott ki az Azure AD-ben a névtelen IP-címekhez. Ahogy az Azure AD feldolgozta a kérést, alkalmazta a bejelentkezési kockázati házirendet az Identity Protectionben, mert az alkalmazott bejelentkezési kockázati szintje megfelel a küszöbértéknek (közepes). Mivel az alkalmazott korábban már regisztrált az MFA-ra, képesek voltak válaszolni az MFA-kihívásra, és továbbadni azokat. Az Azure AD-nek küldött MFA-kihívás sikeres átadásának képessége, amely valószínűleg a jogos identitás tulajdonosa volt, és a felhasználói kockázati szintje nem növekszik. 

Mi a teendő, ha az alkalmazott nem volt az, aki bejelentkezik? 

1. Az alkalmazott hitelesítő adataival rendelkező rosszindulatú színész megpróbál bejelentkezni az Exchange Online-fiókjába a Tor-böngészőből, mert megpróbálják elrejteni az IP-címüket. 
2. Az Azure AD észleli, hogy a bejelentkezési kísérlet névtelen IP-címről származik, és valós idejű bejelentkezési kockázatot váltott ki. 
3. A kártékony szereplőt egy MFA-kérés megkérdőjelezi, mivel a contoso rendszergazdája konfigurálta az Identity Protection bejelentkezési kockázat feltételes hozzáférési szabályzatát, hogy az MFA megkövetelése, ha a bejelentkezési kockázat közepes vagy magasabb. 
4. A rosszindulatú színész nem tudja elérni az MFA-feladatot, és nem fér hozzá az alkalmazott Exchange Online-fiókjához. 
5. A sikertelen MFA-kérés elindított egy kockázati eseményt, amely felveszi a felhasználót a jövőbeli bejelentkezésekhez. 

Most, hogy egy rosszindulatú színész megpróbálta elérni Sarah fiókját, lássuk, mi történik a következő alkalommal, amikor az alkalmazott megpróbál bejelentkezni. 

1. Az alkalmazott megpróbál bejelentkezni az Exchange Online-ba az Outlookból. A bejelentkezés időpontjában az Azure AD valós idejű kockázati eseményeket észlel, valamint minden korábbi felhasználói kockázatot. 
2. Az Azure AD nem észleli a valós idejű bejelentkezési kockázatokat, de az előző helyzetekben fennálló kockázatos tevékenység miatt magas felhasználói kockázatot észlel.  
3. Az alkalmazottat a jelszó-visszaállítási kérdés kéri, mert a contoso rendszergazdája úgy konfigurálta az Identity Protection felhasználói kockázati házirendjét, hogy a jelszó megváltoztatására van szükség, amikor a felhasználó magas kockázatú naplókkal rendelkezik. 
4. Mivel az alkalmazott regisztrálva van a SSPR és az MFA számára, sikeresen visszaállították a jelszavukat. 
5. A jelszó alaphelyzetbe állításával az alkalmazott hitelesítő adatai már nem sérülnek, és az identitásuk egy biztonságos állapotba tér vissza. 
6. Az alkalmazott korábbi kockázati eseményei megoldódott, és a felhasználói kockázati szint automatikusan visszaáll a hitelesítő adatok biztonságának enyhítésére adott válaszként. 

## <a name="how-do-i-configure-identity-protection"></a>Hogyan konfigurálja az Identitáskezelés védelmét? 

Az Identitáskezelés megkezdéséhez először be kell állítania egy felhasználói kockázati házirendet és egy bejelentkezési kockázati házirendet. Miután konfigurálta és alkalmazta ezeket a házirendeket egy tesztelési csoportra, szimulálhatja a kockázati eseményeket, hogy megtudja, hogyan válaszol az Identity Protection a környezetében. Az alábbi rövid útmutatók útmutatást nyújtanak a fent említett szabályzatok és tesztelés beállításához a környezetben. 

Az Identity Protection három szerepkört támogat az Azure AD-ben az üzembe helyezéshez kapcsolódó felügyeleti tevékenységek kiegyensúlyozásához: 

| Role | Elvégezhető | Nem hajtható végre |
| --- | --- | --- |
| Globális rendszergazda | Teljes hozzáférés az Identity Protection szolgáltatáshoz, a személyazonosság védelme | |
| Biztonsági rendszergazda | Teljes hozzáférés az Identity Protection szolgáltatáshoz | A felhasználók jelszavainak alaphelyzetbe állítása |
| Biztonsági olvasó | Írásvédett hozzáférés az Identity Protectionhez | A személyazonosság védelme, a felhasználók szervizelése, a házirendek konfigurálása, a jelszavak alaphelyzetbe állítása| 

További információ: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
 
## <a name="licensing"></a>Licencelés

>[!NOTE]
> Az Identity Protection (frissítve) nyilvános előzetese során csak prémium szintű Azure AD P2-ügyfelek férhetnek hozzá a kockázatos felhasználók jelentéséhez és a kockázatos bejelentkezések jelentéséhez.

| Képesség | Azure AD Premium P2 | Azure AD Premium P1 | Alapszintű Azure AD/ingyenes |
| --- | --- | --- | --- |
| Felhasználói kockázati házirend | Igen | Nem | Nem |
| Bejelentkezési kockázati házirend | Igen | Nem | Nem |
| Kockázatos felhasználók jelentés | Teljes hozzáférés | Korlátozott információ | Korlátozott információ |
| Kockázatos bejelentkezési jelentések | Teljes hozzáférés | Korlátozott információ | Korlátozott információ |
| MFA-regisztrációs szabályzat | Igen | Nem | Nem |

## <a name="next-steps"></a>További lépések 

Az Identity Protection megismeréséhez tekintse meg a [bejelentkezési kockázati házirend konfigurálása](quickstart-sign-in-risk-policy.md)című témakört. 
