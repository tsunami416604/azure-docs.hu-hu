---
title: A feltételes hozzáférés az Azure Active Directory (Azure AD) az örökölt hitelesítési blokkolása |} A Microsoft Docs
description: Ismerje meg, hogyan javíthatja biztonsági helyzetét blokkolja az örökölt hitelesítés használata az Azure AD feltételes hozzáférés.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/25/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42f46e6759b0803b49316219f2ca2a6a7beb3818
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434755"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Útmutató: Örökölt hitelesítés letiltása az Azure AD feltételes hozzáférés   

A felhasználók egyszerűen hozzáférhetnek számára a felhőalapú alkalmazások, Azure Active Directory (Azure AD) széles hitelesítési protokollok, beleértve az örökölt hitelesítési támogatja. Azonban régebbi protokollokra nem támogatják a többtényezős hitelesítés (MFA). MFA sok környezetben a közös követelmény, hogy a cím adatokkal való visszaélés. 


A környezet örökölt hitelesítés letiltása a bérlő védelmi javítása érdekében készen áll, a feltételes hozzáférés a cél végezheti el. Ez a cikk azt ismerteti, hogy hogyan konfigurálhat feltételes hozzáférési szabályzatok letiltása örökölt hitelesítés a bérlő számára.



## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy Ön ismeri a: 

- A [alapvető fogalmait](overview.md) az Azure AD feltételes hozzáférés 
- A [ajánlott eljárások](best-practices.md) a feltételes hozzáférési szabályzatok konfigurálása az Azure Portalon



## <a name="scenario-description"></a>Forgatókönyv leírása

Az Azure AD számos, beleértve az örökölt hitelesítési leggyakrabban használt hitelesítési és engedélyezési protokollokat támogatja. Az örökölt hitelesítési protokollok alapszintű hitelesítést használó hivatkozik. Ezeket a protokollokat általában bármilyen típusú második tényezős hitelesítést nem érvényesíthetők. Példák az örökölt hitelesítési alapuló alkalmazások a következők:

- Régebbi Microsoft Office-alkalmazások

- Alkalmazások levelezési protokollok, mint POP, IMAP és az SMTP használatával

Egyetlen többtényezős hitelesítés (például felhasználónév és jelszó) nem áll elég napjainkban. Jelszavak rendszer rossz, könnyen kitalálni, és hogy (emberek) rossz jelszavak jó választás. Jelszavak is ki téve támadásoktól, például az adathalászatot vagy a jelszó megfelelő különböző. A legegyszerűbb lehetőség jelszó fenyegetésekkel egyik MFA megvalósítása. Az MFA még akkor is, ha egy támadó lekérdezi egy felhasználó jelszavát, birtokában egyedül a jelszó azonban nem sikeresen hitelesíteni, és az adatok eléréséhez.

Hogyan megakadályozhatja az elérését a bérlői erőforrásokhoz örökölt hitelesítést használó alkalmazások? A javaslat, hogy csak letiltja a feltételes hozzáférési szabályzattal együtt. Szükség esetén engedélyezi a csak bizonyos felhasználók és a megfelelő hálózati helyeket az örökölt hitelesítési alapuló alkalmazások használatához.

Az első többtényezős hitelesítés befejezése után a rendszer érvényesíti a feltételes hozzáférési szabályzatokat. Ezért a feltételes hozzáférés nem célja az első sor védelmi például-szolgáltatásmegtagadásos (DDoS-) támadások, de használhat fel ezeket az eseményeket (például a bejelentkezési kockázati szintet, helyét a kérelmet, és így tovább) érkező általi hozzáférés megállapításához.




## <a name="implementation"></a>Megvalósítás

Ez a szakasz ismerteti az örökölt hitelesítés letiltása feltételes hozzáférési szabályzat konfigurálása. 

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása 

A feltételes hozzáférési szabályzatot beállíthat egy feltételt, amely az ügyfélalkalmazásokat az erőforrások eléréséhez használt van kötve. Az ügyfél alkalmazások feltétel lehetővé teszi a kiválasztásával örökölt hitelesítést használó alkalmazások hatókörét leszűkítheti **más ügyfelek** a **mobilalkalmazások és asztali ügyfelek**.

![Más ügyfelek](./media/block-legacy-authentication/01.png)

Ezek az alkalmazások hozzáférésének blokkolása, kell választania **blokkolhatja**.

![Hozzáférés letiltása](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>A kiválasztott felhasználók és a felhőalapú alkalmazások

Ha a szervezet számára az örökölt hitelesítés letiltása, valószínűleg úgy gondolja, hogy ez elvégezhető kiválasztásával:

- Minden felhasználó

- Minden felhőalkalmazás

- Hozzáférés letiltása
 

![Hozzárendelések](./media/block-legacy-authentication/03.png)



Az Azure rendelkezik olyan biztonsági szolgáltatás, amely megakadályozza, hogy ehhez hasonló szabályzat létrehozása, mert ez a konfiguráció sérti a [ajánlott eljárások](best-practices.md) a feltételes hozzáférési szabályzatokat.
 
![A szabályzatkonfiguráció nem támogatott](./media/block-legacy-authentication/04.png)


Szükség a biztonsági funkció mert *letiltása minden felhasználó és az összes felhőalapú alkalmazások* magában hordozza a bejelentkezést a bérlő vállalata blokkolására. Ki kell zárnia a minimális ajánlott gyakorlat követelmény teljesítéséhez legalább egy felhasználót. Sikerült zárnia olyan címtárbeli szerepkörrel.

![A szabályzatkonfiguráció nem támogatott](./media/block-legacy-authentication/05.png)


Egy felhasználó kizárása a szabályzat által a biztonsági funkció képes kielégíteni. Ideális esetben meg kell határozni néhány [válságkezelési hozzáférés rendszergazdai fiókok az Azure ad-ben](../users-groups-roles/directory-emergency-access.md) , és kizárhatja azokat a szabályzat alól.
 

## <a name="policy-deployment"></a>Házirend központi telepítése

Mielőtt a szabályzat az éles környezetbe, gondoskodik:
 
- **Szolgáltatásfiókok** -felhasználói fiókok, szolgáltatásfiókok vagy az eszközök, például konferencia szoba telefonok használt azonosításához. Ellenőrizze, hogy ezek a fiókok erős jelszavakat, és hozzáadhatja őket a kizárt csoportba.
 
- **Jelentkezzen be a jelentések** – tekintse át a bejelentkezési jelentést, és keressen **más ügyfél** forgalmat. Felső kihasználtsága határozza meg és vizsgálja meg, miért használatban van. A forgalom általában, ne használja a modern hitelesítést, vagy néhány külső levelezési alkalmazás régebbi Office-ügyfelek által jön létre. Ezek az alkalmazások alacsony használat áthelyezése, vagy a hatással, ha a felhasználók értesítése, hogy nem használhatják ezeket az alkalmazásokat többé megtervezik.
 
További információkért lásd: [hogyan kell egy új házirendet telepít?](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>Alapismeretek

Hozzáférés letiltása **más ügyfelek** is letiltja az Exchange Online PowerShell alapvető Outlookhoz használatával

A házirend beállítása **más ügyfelek** letiltja az egyes ügyfelek, például SPConnect az egész munkahely számára. Ezt a blokkot, mert a régebbi ügyfelek hitelesítése nem várt módon történik. A probléma nem vonatkozik a fő Office-alkalmazások, például a régebbi Office-ügyfelekhez.

A házirend érvénybe lép, akár 24 órát is igénybe vehet.

Kiválaszthatja a feltételhez más ügyfelek; az összes rendelkezésre álló engedély azonban a végfelhasználói mindig azonos, - hozzáférés blokkolva.

Ha letiltja az örökölt hitelesítési ügyfelek vonatkozó feltétellel, az eszköz platform és hely feltételt is beállíthat. Például ha csak szeretné mobileszközökhöz az örökölt hitelesítés letiltása, állítsa be a **eszközplatformok** kiválasztásával feltétel:

- Android

- iOS

- Windows Phone

![A szabályzatkonfiguráció nem támogatott](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>További lépések

- Ha nem ismeri még a feltételes hozzáférési szabályzatok konfigurálása, lásd: [többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md) példaként.

- Modern hitelesítés nélküli támogatásával kapcsolatos további információkért lásd: [Office 2013 és az Office 2016 ügyfélalkalmazások számára a modern hitelesítés működéséről](https://docs.microsoft.com/en-us/office365/enterprise/modern-auth-for-office-2013-and-2016) 
