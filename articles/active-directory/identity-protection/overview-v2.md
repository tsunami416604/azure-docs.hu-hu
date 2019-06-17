---
title: Mi az Azure Active Directory Identity Protection (frissíthetők)? | Microsoft Docs
description: Mi az Azure Active Directory Identity Protection (frissíthetők)?
services: active-directory
keywords: az Azure active directory identity protection a következőket cloud app discovery szolgáltatást, alkalmazások, biztonság, kockázati, kockázati szint, biztonsági rést, biztonsági házirend kezelése
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b4f8caf03aad339cea3c3fcc732fc1af6086ea7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108888"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Mi az Azure Active Directory Identity Protection (frissíthetők)?

Az Identity Protection élmény frissítve lett, amelyekkel jobban megvédheti a szervezet identitásait. A frissített felhasználói élményt nyújtja:

- Újratervezett felügyeleti felület, amely a kockázat you¬-felhasználói kockázat és bejelentkezési kockázat számára leginkább releváns identitásáról

- Hatékony vizsgálatokhoz tapasztalatát a szűrési, rendezési és intelligens letöltések támogatása

- Továbbfejlesztett felhasználói kockázat számítása segítséget nyújtanak, amelyek a legnagyobb valószínűséggel sérülhet a felhasználók felé irányuló rangsorolására

- Új API-támogatást kockázati adatok programozott hozzáférés engedélyezése

- Egyszerűsített felügyeleti visszajelzés folyamat, amely lehetővé teszi a felhasználók azonnal védelmét

- Új felügyelt gépi tanulás útján kockázatfelmérések pontosságának javítása



Biztonsági a mai nap legfontosabb szempont a szervezet számára. Biztonsági szabályok megsértése esetén alkalmazandók igénybe a legtöbb helyezze el, ha a támadók próbál a jeggyel hozzáférést egy környezethez lopásának megjelölése a felhasználó identitását. Az évek során a támadók egyre inkább hatékony kihasználva külső illetéktelen behatolásokat és adathalászati támadások használatával váltak. Amint a támadók alacsony jogosultsági szintű felhasználó fiókok is elérhet, viszonylag egyszerű, az oldalirányú mozgás keresztül fontos vállalati erőforrásokhoz való hozzáférést nyerhetnek a hozzájuk. 

Azure AD Identity Protection válaszolni a fenyegetések, lehetővé teszi, hogy: 

- Proaktív módon megakadályozza, hogy feltört identitásokat visszaélt folyamatban 

- Ha gyanús tevékenységet észlel automatikusan kockázatok 

- Kockázatos felhasználók és bejelentkezések cím potenciális biztonsági rések vizsgálata  

- Figyelmeztetést kapni, ha a felhasználó kockázati elér egy megadott küszöbértéket 

 

Az Azure AD Identity Protection az Azure Active Directory Premium P2, amely lehetővé teszi, hogy a házirendek úgy, hogy automatikusan válaszol, amikor a felhasználó identitásáról biztonsága sérül egyik szolgáltatása, vagy ha valaki más, mint a fiók tulajdonosa próbál jelentkezzen be a saját identitás. Ezek a szabályzatok mellett más feltételes hozzáférés-vezérlés az Azure AD által biztosított automatikusan vagy blokkolhatja hozzáférés vagy kezdeményezési kockázatcsökkentési műveleteket, például a jelszó-visszaállítás vagy a többtényezős hitelesítés kényszerítése. Identity Protection emellett figyelési és jelentéskészítési lehetőségeket a mélyebb betekintést nyerhet a kockázatokat és a szervezet esetleges támadások. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]


## <a name="risk-events"></a>Kockázati események

Az Azure AD Identity Protection észleli az alábbi kockázati események: 

 

| Kockázati esemény típusa | Leírás | Észlelés típusa |
| ---             | ---         | ---            |
| Bejelentkezés szokatlan utazás | Jelentkezzen be a felhasználó legutóbbi bejelentkezések alapján szokatlan helyről. | Offline |
| Anonymous IP address | Bejelentkezés névtelen IP-címről (például: Tor böngésző, anonymizer VPN-eket). | Valós idejű |
| Ismeretlen bejelentkezési tulajdonságok | Jelentkezzen be tulajdonságai nem volt utoljára az adott felhasználó. | Valós idejű |
| Kártevő szoftver társított IP-cím | Jelentkezzen be a kártevő szoftverek társított IP-címről | Offline |
| Kiszivárogtatott hitelesítő adatokkal | A kockázati esemény azt jelzi, hogy a felhasználó érvényes hitelesítő adatokkal rendelkezik lett kiszivárgott | Offline |





## <a name="types-of-risk"></a>A kockázat típusa 

Identity Protection kockázati két típusú alapul:

- Bejelentkezési kockázat

- Felhasználói kockázat

### <a name="sign-in-risk"></a>Bejelentkezési kockázat

A bejelentkezési kockázat jelöli annak a valószínűsége, hogy egy adott hitelesítési kérelem identitás tulajdonosa nem engedélyezett.

Nincsenek bejelentkezésikockázat-két értékelést: 

- **Bejelentkezési kockázati (valós idejű)** – a bejelentkezési kockázat (valós idejű) alapján a valós idejű észlelések az feldolgozásakor a bejelentkezési eseményindító.  

- **Bejelentkezési kockázati (összesítés)** – a bejelentkezési kockázat (összesítés) egy bejelentkezési teljes kockázatát. A machine learning-modell, amely úgy ítéli meg kiszámítása történik:

    - Valós idejű észlelések (lásd fent)
    
    - A kapcsolat nélküli észlelések (olyan esetekre, a bejelentkezés végrehajtása után) 
    
    - Minden más szolgáltatását a bejelentkezés


### <a name="user-risk"></a>Felhasználói kockázat

A felhasználói kockázat jelöli annak a valószínűsége, hogy egy adott identitás biztonsága sérült. 

A felhasználói kockázat figyelembe véve a felhasználóhoz társított összes kockázat alapján számítjuk ki:

- Minden kockázatos bejelentkezések
- Az összes, nem kapcsolódik egy bejelentkezési kockázati események
- Az aktuális felhasználói kockázat
- Kockázati szervizelési vagy elbocsátása végrehajtott összes műveletet a felhasználóra, dátum:



## <a name="how-identity-protection-detects-risk"></a>Hogyan észleli az Identity Protection a kockázat  

Azure ad-ben a gépi tanulási észlelheti a rendellenességeket és gyanús tevékenységek, mindkét észlelhető a jelek használata valós idejű bejelentkezések, valamint során nem valós idejű jeleket, kapcsolódó felhasználókat, és jelentkezzen be tevékenységeik. Ezen adatok alapján Identity Protection kiszámítja egy valós idejű bejelentkezési kockázat minden alkalommal, amikor egy felhasználó hitelesíti magát, és a egy általános felhasználói kockázati szint reach felhasználó meghatározásához. Identity Protection automatikusan reagálhat az kockázati észlelések konfigurálása Identity Protection felhasználói kockázat és bejelentkezési kockázati házirend teszi lehetővé.  

 

Szeretné megtudni, hogyan észleli az Identity Protection a kockázat, két fontos szempontot: felhasználói kockázat és bejelentkezési kockázat. Bejelentkezési kockázati tükrözi a valószínűsége annak, hogy egy adott hitelesítési kérelem identitás tulajdonosa nem engedélyezett. Bejelentkezési kockázat két típusa van: valós idejű és teljes. Valós idejű bejelentkezési kockázat észlelve (például a bejelentkezések névtelen IP-címekről) megadott bejelentkezési kísérlet alkalmával. Összes bejelentkezési kockázat az összesítése észlelt valós idejű bejelentkezési kockázatát, valamint bármely ezt követő nem valós idejű kockázati eseményeket a felhasználóhoz társított bejelentkezések (például a lehetetlen utazás). Felhasználói kockázat jeleníti meg, hogy kiderítsék feltört egy adott identitás összesített valószínűségét. Felhasználói kockázat tartalmaz egy adott felhasználó az összes kockázati tevékenység többek között:

- Valós idejű bejelentkezési kockázat
- További bejelentkezési kockázat
- Kockázatos felhasználó észleléseket.   

 

 
 ![Folyamat](./media/overview-v2/01.png)
 

 

Az Identity Protection kockázatészlelés alapkonfiguráció folyamat-bármely adott bejelentkezéshez válasz összegzése a fenti ábrán.  

 

 

 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek 

A példában a Contoso-alkalmazott, nézzük meg. 

1. Egy alkalmazott próbál bejelentkezni az Exchange online-hoz a Tor böngészőből. Bejelentkezés időben az Azure AD észleli a valós idejű kockázati események. 

2. Az Azure AD észleli, hogy az alkalmazott jelentkezik be egy névtelen IP-cím, egy közepes méretű bejelentkezési kockázati szint elindítása. 

3. Az alkalmazott van próbálnak futtatni rajta egy MFA rendszer, mert a Contoso rendszergazda beállította az Identity Protection bejelentkezési kockázat feltételes hozzáférési szabályzat. A házirend többtényezős Hitelesítést követel meg a bejelentkezést a kockázata, közepes vagy nagyobb. 

4. Az alkalmazott továbbítja a többtényezős hitelesítés használatával, és fér hozzá az Exchange online-hoz, és azok felhasználói kockázati szint nem módosulnak. 

Mi történt a háttérben? A bejelentkezési kísérlet a Tor böngészőből egy valós idejű bejelentkezési kockázat névtelen IP-cím az Azure AD-ben indul el. Az Azure AD dolgozza fel a kérelmet, alkalmazza a a bejelentkezési kockázati szabályzat konfigurálva az Identity Protection az alkalmazott bejelentkezési kockázati szint telepítve a küszöbérték (közepes). Mivel az alkalmazott volt korábban regisztrálva az MFA-hoz, a felhasználók sikeresen válaszolni, és adja át az MFA-hitelesítést. Sikeresen adja át az MFA-hitelesítést képesek jelzést az Azure AD, hogy voltak valószínűleg azonosságát törvényes tulajdonosa, és azok felhasználói kockázati szint nem növekszik. 


De mi történik, ha az alkalmazott volt, egy megpróbál bejelentkezni? 

1. Jelentkezzen be az Exchange Online-fiókot a Tor böngészőből, mivel az IP-címüket elrejteni kívánt megkísérli egy rosszindulatú aktor az alkalmazott hitelesítő adatokkal. 

2. Az Azure AD észleli, hogy a bejelentkezési kísérlet egy névtelen IP-címről, egy valós idejű bejelentkezési kockázat elindítása. 

3. A rosszindulatú aktor van próbálnak futtatni rajta egy MFA rendszer, mivel a Contoso rendszergazda beállította az Identity Protection bejelentkezési kockázati feltételes hozzáférési szabályzatot az többtényezős hitelesítés esetén a bejelentkezési kockázat közepes vagy nagyobb. 

4. A rosszindulatú aktor meghiúsul az MFA-hitelesítést, és nem tudja elérni az alkalmazott Exchange Online-fiókját. 

5. A sikertelen többtényezős hitelesítés kérése által aktivált egy kockázati esemény rögzítését, a felhasználói kockázat a későbbi bejelentkezések előléptetése. 

Most, hogy egy rosszindulatú aktor próbált Sarah a fiókjába, lássuk, mi történik, a következő alkalommal, amikor az alkalmazott megpróbál bejelentkezni. 

1. Az alkalmazott próbál bejelentkezni az Exchange online-hoz az Outlookban. Bejelentkezés időben az Azure AD észleli a valós idejű kockázati események, valamint bármely korábbi felhasználói kockázat. 

2. Az Azure AD nem észleli a bármely valós idejű bejelentkezési kockázat, de a felhasználó magas kockázati miatt a múltbeli kockázatos tevékenység észleli a korábbi helyzetekben.  

3. Az alkalmazott merül fel a jelszó alaphelyzetbe állítására szerint, mert a Contoso a rendszergazda beállította az Identity Protection felhasználói kockázati házirend a jelszómódosítás megkövetelése, ha a magas kockázatú felhasználók. 

4. Az alkalmazott SSPR és az MFA regisztrálva van, mivel azok sikeresen alaphelyzetbe állítja a jelszavát. 

5. Ehhez állítsa alaphelyzetbe a jelszavát, az alkalmazott hitelesítő adatok már nem sérült a biztonsága és -azonosítót fog kérni biztonságos állapotba tér vissza. 

6. Az alkalmazott előző kockázati események elhárulnak, és a saját felhasználói kockázat szintjének csökkentése a hitelesítő adatok biztonsági sérülés válaszul automatikusan alaphelyzetbe áll. 

## <a name="how-do-i-configure-identity-protection"></a>Hogyan konfigurálhatom az Identity Protection? 

Az Identity Protection használatának megkezdéséhez először konfigurálja egy felhasználói kockázati házirend és a egy bejelentkezési kockázati házirend. Ezek a szabályzatok konfigurálva, és a egy teszt alkalmazott, szimulálhatja a kockázati események megérteni, hogyan fog válaszolni az Identity Protection a környezetben. Az alábbi rövid útmutatókban bemutató a fent említett házirendeket állíthat be és tesztelje a környezetben. 

 

Identity Protection 3 szerepkört a felügyeleti tevékenységek, az üzemelő példány körüli elosztása érdekében az Azure AD-ben támogatja: 

| Szerepkör | Teheti meg | Nem hajtható végre |
| --- | --- | --- |
| Globális rendszergazda | Teljes hozzáférés az Identity Protection előkészítése Identity Protection | |
| Biztonsági rendszergazda | Teljes hozzáférés az Identity Protection | Identity Protection előkészítése, a felhasználó a jelszavak alaphelyzetbe állítása |
| Biztonsági olvasó | Csak olvasási hozzáférés az Identity Protection | Identity Protection előkészítése, felhasználók szervizelése, a szabályzatok konfigurálására, jelszavak alaphelyzetbe állítása| 

További részletekért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>Licencek

>[!NOTE]
> Az Identity Protection (frissítés) a nyilvános előzetes verzióban csak a prémium szintű Azure AD P2-ügyfeleknek lesz hozzáférése a kockázatos felhasználók jelentéshez és a kockázatos bejelentkezések jelentés.



| Képesség | Prémium szintű Azure AD P2 | Prémium szintű Azure AD P1 | Az Azure AD Basic vagy ingyenes |
| --- | --- | --- | --- |
| Felhasználói kockázati házirend | Igen | Nem | Nem |
| Bejelentkezési kockázati házirend | Igen | Nem | Nem |
| Kockázatos felhasználók jelentés | Teljes hozzáférés | Korlátozott mennyiségű információt | Korlátozott mennyiségű információt |
| Kockázatos bejelentkezési jelentések | Teljes hozzáférés | Korlátozott mennyiségű információt | Korlátozott mennyiségű információt |
| Az MFA regisztrációs szabályzatának | Igen | Nem | Nem |







## <a name="next-steps"></a>További lépések 

Az Identity Protection használatának megkezdéséhez lásd [bejelentkezési kockázati házirend konfigurálása](quickstart-sign-in-risk-policy.md). 






