---
title: Azure Active Directory-jelentések – gyakori kérdések | Microsoft dokumentumok
description: Gyakori kérdések az Azure Active Directory-jelentések körül.
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266506"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Gyakori kérdések az Azure Active Directory-jelentésekről

Ez a cikk az Azure Active Directory (Azure AD) jelentéskészítéssel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza. További információk: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md). 

## <a name="getting-started"></a>Első lépések 

**K: Jelenleg a `https://graph.windows.net/<tenant-name>/reports/` végpontAPI-k segítségével lekéri az Azure AD-naplózást és integrált alkalmazáshasználati jelentéseket a jelentéskészítő rendszereinkbe programozott módon. Mire váltsak?**

**A.** Keresse meg az [API-hivatkozást,](https://developer.microsoft.com/graph/) hogy lássa, hogyan [használhatja az API-kat a tevékenységjelentések eléréséhez.](concept-reporting-api.md) Ez a végpont két jelentést **(naplózásés** bejelentkezések ) rendelkezik, amelyek biztosítják a régi **API-végpontban**kapott összes adatot. Ez az új végpont is rendelkezik egy bejelentkezési jelentést az Azure AD Premium-licenccel, amely segítségével lekaphatja az alkalmazás használatát, az eszközhasználatot és a felhasználói bejelentkezési adatokat.

---

**K: Jelenleg a `https://graph.windows.net/<tenant-name>/reports/` végpontAPI-k segítségével lekéri az Azure AD biztonsági jelentések (bizonyos típusú észlelések, például a kiszivárgott hitelesítő adatok vagy bejelentkezések névtelen IP-címekről) a jelentési rendszerek programozott módon. Mire váltsak?**

**A.** Az [Identity Protection kockázatészlelési API-val](../identity-protection/graph-get-started.md) a Microsoft Graph-on keresztül érheti el a biztonsági észleléseket. Ez az új formátum nagyobb rugalmasságot biztosít az adatok lekérdezésének módjában, fejlett szűréssel, mezőválasztással és egyebekkel, és egységesíti a kockázatészleléseket egyetlen típusba, hogy könnyebben integrálható legyen a SIEM-ekbe és más adatgyűjtési eszközökbe. Mivel az adatok más formátumúak, nem helyettesíthet új lekérdezést a régi lekérdezésekkel. Az új API azonban [a Microsoft Graph-ot használja,](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)amely a Microsoft szabványa az olyan API-khoz, mint az O365 vagy az Azure AD. Így a szükséges munka kiterjesztheti a jelenlegi Microsoft Graph-befektetéseit, vagy segíthet az új szabványos platformra való áttérésben.

---

**K: Hogyan kaphatok prémium licencet?**

**A.** Az [Azure Active Directory-kiadás](../fundamentals/active-directory-get-started-premium.md) frissítéséhez olvassa el az Azure Active Directory Premium szolgáltatás első lépéseit.

---

**K: Milyen hamar kell látni tevékenységek adatait, miután egy prémium licenc?**

**A.** Ha már rendelkezik tevékenységi adatokkal, mint ingyenes licenc, akkor azonnal láthatja. Ha nem rendelkezik adatokkal, akkor egy vagy két napba telik, amíg az adatok megjelennek a jelentésekben.

---

**K: Láthatom a múlt havi adatokat, miután megkaptam az Azure AD prémium licencét?**

**A.** Ha a közelmúltban váltott prémium verzióra (beleértve a próbaverziót is), kezdetben legfeljebb 7 napig láthatja az adatokat. Amikor az adatok felhalmozódnak, láthatja az elmúlt 30 nap adatait.

---

**K: Globális rendszergazdának kell lennem ahhoz, hogy láthassam a tevékenységbejelentkezéseket az Azure Portalon, vagy hogy adatokat jussak be az API-n keresztül?**

**A.** Nem, a jelentési adatok a portálon keresztül vagy az API-n keresztül is hozzáférhet, ha **biztonsági olvasó** vagy a bérlő **biztonsági rendszergazdája.** Természetesen a **globális rendszergazdák** is hozzáférhetnek ezekhez az adatokhoz.

---


## <a name="activity-logs"></a>Tevékenységnaplók


**K: Mi a tevékenységnaplók (naplózás és bejelentkezések) adatmegőrzése az Azure Portalon?** 

**A.** Az alábbi táblázat a tevékenységnaplók adatmegőrzési időszakát sorolja fel. További információt az [Azure AD-jelentések adatmegőrzési szabályzatai című témakörben talál.](reference-reports-data-retention.md)

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Naplók             | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezések               | N/A           | 30 nap             | 30 nap             |
| Az Azure MFA-használat        | 30 nap       | 30 nap             | 30 nap             |

---

**K: Mennyi ideig tart, amíg a tevékenységadatok at látom a feladat befejezése után?**

**A.** A naplónaplók késése 15 perctől egy óráig terjed. A bejelentkezési tevékenységnaplók egyes rekordok esetében 15 perctől akár 2 óráig is eltarthatnak.

---

**K. Beszerezhetem az Office 365 tevékenységnaplóadatait az Azure Portalon keresztül?**

**A.** Annak ellenére, hogy az Office 365 tevékenységi és Azure AD-tevékenységnaplói sok címtár-erőforrást osztanak meg, ha teljes nézetet szeretne az Office 365 tevékenységnaplóiról, az Office 365 tevékenységnaplójának adatainak lehívásához keresse fel a [Microsoft 365 Felügyeleti központot.](https://admin.microsoft.com)

---

**K. Milyen API-kat használok az Office 365 tevékenységnaplóival kapcsolatos információk hozadékához?**

**A.** Az [Office 365 Felügyeleti API-k segítségével](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) API-n keresztül érheti el az Office 365 tevékenységnaplókat.

---

**K: Hány rekordot tölthetek le az Azure Portalról?**

**A.** Legfeljebb 5000 rekordot tölthet le az Azure Portalról. A rekordok a *legfrissebb* szerint vannak rendezve, és alapértelmezés szerint a legutóbbi 5000 rekord.

---

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

**K: Van egy kockázatészlelés az Identitásvédelem, de nem látom a megfelelő bejelentkezési a bejelentkezések jelentésben. Ez várható?**

**A.** Igen, az Identity Protection kiértékeli az összes hitelesítési folyamat kockázatát, függetlenül attól, hogy interaktív vagy nem interaktív. Azonban az összes bejelentkezéscsak jelentés csak az interaktív bejelentkezések.

---

**K: Honnan tudhatom, hogy miért egy bejelentkezési vagy egy felhasználó volt megjelölve kockázatos az Azure Portalon?**

**A.** Ha rendelkezik egy **Azure AD** Premium-előfizetéssel, az alapul szolgáló kockázatészlelésekről úgy tudhat meg többet, ha kiválasztja a felhasználót a **kockázatra megjelölt felhasználók** között, vagy a **Kockázatos bejelentkezések** jelentésben kiválaszt egy rekordot. Ha **ingyenes** vagy **alapszintű** előfizetéssel rendelkezik, akkor megtekintheti a kockázatos és kockázatos bejelentkezési jelentéseket, de nem láthatja az alapul szolgáló kockázatészlelési információkat.

---

**K: Hogyan történik az IP-címek kiszámítása a bejelentkezések és a kockázatos bejelentkezések jelentésben?**

**A.** Az IP-címek kiadása oly módon történik, hogy nincs végleges kapcsolat az IP-cím és az adott címmel rendelkező számítógép fizikai elhelyezkedése között. Az IP-címek leképezését tovább bonyolítják olyan tényezők, mint a mobilszolgáltatók és a központi készletekből IP-címeket kibocsátó VPN-ek, gyakran nagyon messze attól, ahol az ügyféleszközt ténylegesen használják. Jelenleg az Azure AD-jelentésekben az IP-cím fizikai helyre való konvertálása a legjobb megoldás a nyomon követések, a beállításjegyzék-adatok, a névfeloldási és egyéb információk alapján. 

---

**K: Mit jelent a kockázatészlelés "Bejelentkezés további észlelt kockázattal" jelent?**

**A.** A környezetben lévő összes kockázatos bejelentkezésbe való betekintést nyújt, a "Bejelentkezés további kockázattal észlelt" helyőrzőként működik az Azure AD Identity Protection-előfizetők számára kizárólagos észlelések bejelentkezések esetén.

---

## <a name="conditional-access"></a>Feltételes hozzáférés

**K: Újdonságok ezzel a funkcióval?**

**A.** Az ügyfelek mostantól az összes bejelentkezési jelentésben elháríthatják a feltételes hozzáférésre vonatkozó házirendeket. Az ügyfelek áttekinthetik a feltételes hozzáférés állapotát, és beletekinthetnek a bejelentkezésre alkalmazott szabályzatok részleteibe és az egyes szabályzatok eredményébe.

**K: Hogyan kezdhetek?**

**A.** Első lépések:

* Keresse meg a bejelentkezések jelentést az [Azure Portalon.](https://portal.azure.com)
* Kattintson a hibaelhárításhoz szükséges bejelentkezésre.
* Nyissa meg a **Feltételes hozzáférés** lapot. Itt megtekintheti az összes olyan szabályzatot, amely hatással volt a bejelentkezésre és az egyes házirendek eredményére. 
    
**K: Melyek a feltételes hozzáférés állapotának lehetséges értékei?**

**A.** A feltételes hozzáférés állapota a következő értékeket tartalmazhatja:

* **Nincs alkalmazva:** Ez azt jelenti, hogy nem volt hitelesítésbiztosítási házirend a felhasználó és az alkalmazás hatókörében. 
* **Sikeres**: Ez azt jelenti, hogy a felhasználó és az alkalmazás hatókörében volt egy hitelesítésbiztosítási házirend, és a hitelesítésbiztosítási házirendek sikeresen teljesültek. 
* **Hiba**: Ez azt jelenti, hogy a felhasználó és az alkalmazás hatókörében volt egy hitelesítésbiztosítási házirend, és a hitelesítésbiztosítási házirendek nem voltak teljesek. 
    
**K: Melyek a feltételes hozzáférési házirend eredményének lehetséges értékei?**

**A.** A feltételes hozzáférési házirendek a következő eredményekkel rendelkezhetnek:

* **Siker**: A házirend sikeresen teljesült.
* **Hiba**: A házirend nem teljesült.
* **Nem alkalmazva**: Ennek az lehet az oka, hogy a házirend-feltételek nem feleltek meg.
* **Nincs engedélyezve**: Ez a házirend letiltott állapotban történt. 
    
**K: A házirend neve az összes bejelentkezési jelentésnem egyezik a házirend neve a hitelesítésbiztosítási. miért?**

**A.** A házirend neve az összes bejelentkezési jelentésben a hitelesítésházirend-házirend nevén alapul a bejelentkezés időpontjában. Ez nem lehet összeegyeztethető a házirend nevével a hitelesítésbiztosítási ban, ha később frissítette a házirend nevét, azaz a bejelentkezés után.

**K: A feltételes hozzáférési szabályzat miatt letiltotta a bejelentkezést, de a bejelentkezési tevékenységjelentés azt mutatja, hogy a bejelentkezés sikerült. miért?**

**A.** Feltételes hozzáférés alkalmazása esetén előfordulhat, hogy a bejelentkezési jelentés nem jeleníti meg az Exchange ActiveSync-forgatókönyvek pontos eredményeit. Lehetnek olyan esetek, amikor a jelentésben a bejelentkezés eredménye sikeres bejelentkezést mutat, de a bejelentkezés egy feltételes hozzáférési szabályzat miatt ténylegesen sikertelen volt. 
