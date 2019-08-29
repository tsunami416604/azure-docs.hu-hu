---
title: Azure Active Directory jelentések – GYIK | Microsoft Docs
description: Gyakori kérdések a Azure Active Directory-jelentések quesitons.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8c3138b82c7dc4a7217e8cb67448a5d824398ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127024"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Gyakori kérdések Azure Active Directory jelentésekről

Ez a cikk a Azure Active Directory (Azure AD) jelentéskészítéssel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza. További információk: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md). 

## <a name="getting-started"></a>Első lépések 

**K: Jelenleg a Endpoint API `https://graph.windows.net/<tenant-name>/reports/` -k használatával lehívhatjuk az Azure ad audit és az integrált alkalmazás-használati jelentéseket a jelentéskészítő rendszerbe programozott módon. Mire érdemes váltani?**

**V:** Tekintse meg az [API](https://developer.microsoft.com/graph/) -referenciát, amelyből megtudhatja, hogyan [használhatja az API-kat a tevékenységek jelentésekhez való hozzáféréshez](concept-reporting-api.md). Ez a végpont két jelentést tartalmaz (naplózási és **bejelentkezési**), amelyek biztosítják a régi API-végponton kapott összes adathalmazt. Ez az új végpont olyan bejelentkezési jelentést is tartalmaz, amely az alkalmazás használatának, az eszköz használatának és a felhasználói bejelentkezési adatok beszerzésére használható prémium szintű Azure AD licenccel rendelkezik.

---

**K: Jelenleg a Endpoint API `https://graph.windows.net/<tenant-name>/reports/` -k segítségével lehívhatjuk az Azure ad biztonsági jelentéseit (az észlelések bizonyos típusai, például a kiszivárgott hitelesítő adatok vagy a névtelen IP-címekről való bejelentkezések) a jelentéskészítési rendszerekhez programozott módon. Mire érdemes váltani?**

**V:** Az [Identity Protection kockázati észlelések API](../identity-protection/graph-get-started.md) -val Microsoft Graph használatával férhet hozzá a biztonsági észlelésekhez. Ez az új formátum nagyobb rugalmasságot biztosít az adatlekérdezéshez, a speciális szűréssel, a mezők kiválasztásával és egyebekkel, valamint a kockázati észlelések egyetlen típusra történő egységesítésével, így könnyebben integrálható a SIEM és más adatgyűjtési eszközökbe. Mivel az adatformátumok eltérő formátumúak, nem helyettesíthet új lekérdezést a régi lekérdezésekhez. [Az új API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)azonban a Microsoft Graph-t használja, amely a O365 vagy az Azure ad-hez hasonló API-k Microsoft-szabványa. Így a szükséges munka kiterjesztheti az aktuális MS Graph-beruházásokat, vagy megkezdheti az áttérést az új standard platformra.

---

**K: Hogyan prémium szintű licencet?**

**V:** A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört.

---

**K: Milyen gyorsan kell látni a tevékenységek információit a prémium szintű licenc beszerzése után?**

**V:** Ha már rendelkezik a tevékenységek és az ingyenes licencek között, akkor azonnal megtekintheti. Ha nem rendelkezik az adataival, akkor a jelentésekben az adatai közül egy vagy két nap fog megjelenni.

---

**K: A prémium szintű Azure AD-licenc beszerzése után láthatom a múlt havi adatszolgáltatást?**

**V:** Ha nemrég áttért a prémium verzióra (beleértve a próbaverziót is), a kezdeti időszakban akár 7 napig is megtekintheti az adatvesztést. Az adatgyűjtés során az elmúlt 30 napban láthatja az adatmennyiséget.

---

**K: Globális rendszergazdának kell lennie ahhoz, hogy megjelenjenek a tevékenységek a Azure Portal, vagy az API-n keresztül kapják meg az adatgyűjtést?**

**V:** Nem, a jelentéskészítési adatgyűjtést a portálon keresztül vagy az API-n keresztül is elérheti, ha Ön **biztonsági olvasó** vagy **biztonsági rendszergazda** a bérlő számára. Természetesen a **globális rendszergazdák** is hozzáférhetnek ezekhez az adatszolgáltatásokhoz.

---


## <a name="activity-logs"></a>Tevékenységnaplók


**K: Mi a tevékenységi naplók (auditálás és bejelentkezések) adatmegőrzési funkciója a Azure Portalban?** 

**V:** A következő táblázat felsorolja a tevékenységek naplóinak adatmegőrzési időszakát. További információ: adatmegőrzési [szabályzatok az Azure ad](reference-reports-data-retention.md)-jelentésekhez.

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Naplók             | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezések               | –           | 30 nap             | 30 nap             |
| Azure MFA-használat        | 30 nap       | 30 nap             | 30 nap             |

---

**K: Mennyi ideig tart, amíg meg nem jelenik a tevékenység adatai a feladat befejezése után?**

**V:** A naplózási naplók 15 perc és egy óra közötti késéssel rendelkeznek. Egyes rekordok esetén a bejelentkezési tevékenység naplófájljai 15 perctől akár 2 óráig is eltarthat.

---

**K: Lekérhetem az Office 365-tevékenység naplójának adatait a Azure Portalon keresztül?**

**V:** Annak ellenére, hogy az Office 365-tevékenység és az Azure AD-tevékenység naplói nagy mennyiségű címtár-erőforrást osztanak meg, ha az Office 365-tevékenység naplóinak teljes nézetét szeretné megtekinteni, lépjen a [Microsoft 365 felügyeleti](https://admin.microsoft.com) központba az Office 365-tevékenység naplójának adatainak beszerzéséhez.

---

**K: Mely API-k használatával kaphatok adatokat az Office 365-tevékenységek naplóiról?**

**V:** Az [office 365 Management API](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) -k használatával érheti el az Office 365-tevékenységek naplóit egy API-n keresztül.

---

**K: Hány rekord tölthető le a Azure Portal?**

**V:** Legfeljebb 5000 rekordot tölthet le a Azure Portalból. A rekordok a *legutóbbi* és alapértelmezés szerint rendezve jelennek meg a legutóbbi 5000-rekordokban.

---

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

**K: A rendszer kockázati észlelést észlel az Identity Protectionben, de nem látok megfelelő bejelentkezést a bejelentkezési jelentésekben. Ez várható?**

**V:** Igen, az Identity Protection kiértékeli az összes hitelesítési folyamat kockázatát, akár interaktív, akár nem interaktív. Azonban az összes bejelentkezési jelentés csak az interaktív bejelentkezéseket jeleníti meg.

---

**K: Hogyan tudni, miért van a bejelentkezés vagy a felhasználó kockázatos jelölése a Azure Portalban?**

**V:** Ha **prémium szintű Azure ad** -előfizetéssel rendelkezik, további információkat tudhat meg az alapul szolgáló kockázati észlelésekről, ha kijelöli a felhasználót a kockázatnak kijelölve, vagy egy, a **kockázatos bejelentkezési** jelentésben szereplő rekordot választ. Ha **ingyenes** vagy alapszintű előfizetéssel rendelkezik, akkor a kockázatos és kockázatos bejelentkezési jelentésekben megtekintheti a felhasználókat, de a mögöttes kockázati észlelési információk nem láthatók.

---

**K: Hogyan számítják ki az IP-címeket a bejelentkezések és a kockázatos bejelentkezések jelentésében?**

**V:** Az IP-címek oly módon vannak kiadva, hogy az IP-cím és az azt tartalmazó számítógép fizikailag ne legyen végleges kapcsolat. A leképezési IP-címeket tovább bonyolítja olyan tényezők, mint például a mobil szolgáltatók és a virtuális magánhálózatok, amelyek a központi készletekből származó IP-címeket állítanak elő, gyakran nagyon távol vannak az eszköz ténylegesen használt helyétől Jelenleg az Azure AD-jelentésekben az IP-címek fizikai helyre konvertálása a legjobb megoldás a Nyomkövetések, a beállításjegyzék-adatok, a fordított irányú keresés és egyéb információk alapján. 

---

**K: Mit jelent a kockázatkezelési "Bejelentkezés további kockázattal" kifejezés?**

**V:** Annak érdekében, hogy betekintést kapjon az összes kockázatos bejelentkezésbe a környezetében, a "Bejelentkezés további kockázattal" funkció helyőrzőként funkcionál az olyan észlelésekhez, amelyek kizárólag Azure AD Identity Protection előfizetők számára készültek.

---

## <a name="conditional-access"></a>Feltételes hozzáférés

**K: A szolgáltatás újdonságai**

**V:** Az ügyfelek mostantól a bejelentkezési jelentéseken keresztül is elhárítják a feltételes hozzáférési házirendeket. Az ügyfelek áttekinthetik a feltételes hozzáférés állapotát, és bemutatják a bejelentkezésre alkalmazott szabályzatok részleteit, valamint az egyes szabályzatok eredményét.

**K: Hogyan első lépések?**

**V:** Első lépések:

* Navigáljon a Azure Portalban található bejelentkezési jelentésre [](https://portal.azure.com).
* Kattintson a hibakereséshez használni kívánt bejelentkezésre.
* Navigáljon a **feltételes hozzáférés** lapra. Itt megtekintheti az összes olyan házirendet, amely érintette a bejelentkezést és az egyes házirendek eredményét. 
    
**K: Mi a feltételes hozzáférés állapotának minden lehetséges értéke?**

**V:** A feltételes hozzáférés állapota a következő értékekkel rendelkezhet:

* **Nincs alkalmazva**: Ez azt jelenti, hogy a hatókörben nem található a felhasználóval és az alkalmazással kapcsolatos HITELESÍTÉSSZOLGÁLTATÓI házirend. 
* **Sikeres**: Ez azt jelenti, hogy a hatókörben és a CA-házirendekben a felhasználóval és az alkalmazással kapcsolatos HITELESÍTÉSSZOLGÁLTATÓI házirend sikeresen teljesült. 
* **Hiba**: Ez azt jelenti, hogy a hatókörben és a CA-házirendekben a felhasználóval és az alkalmazással kapcsolatos HITELESÍTÉSSZOLGÁLTATÓI házirend nem teljesült. 
    
**K: Mik a feltételes hozzáférési szabályzat eredményének lehetséges értékei?**

**V:** A feltételes hozzáférési szabályzatnak a következő eredményei lehetnek:

* **Sikeres**: A szabályzat sikeresen teljesült.
* **Hiba**: A házirend nem teljesült.
* **Nincs alkalmazva**: Ennek az lehet az oka, hogy a házirend feltételei nem teljesültek.
* **Nincs engedélyezve**: Ennek oka, hogy a házirend letiltott állapotban van. 
    
**K: A házirend neve az összes bejelentkezési jelentésben nem egyezik meg a házirend nevével a CA-ban. Hogy miért?**

**V:** A házirend neve az összes bejelentkezési jelentésben a bejelentkezés időpontjában a HITELESÍTÉSSZOLGÁLTATÓ házirend neve alapján történik. Ez inkonzisztens lehet a HITELESÍTÉSSZOLGÁLTATÓ házirendjének nevével, ha később frissítette a szabályzat nevét, azaz a bejelentkezést követően.

**K: A bejelentkezés egy feltételes hozzáférési szabályzat miatt blokkolva lett, de a bejelentkezési tevékenység jelentés azt mutatja, hogy a bejelentkezés sikeres volt. Hogy miért?**

**V:** Előfordulhat, hogy a bejelentkezési jelentés nem jeleníti meg az Exchange ActiveSync-forgatókönyvek pontos eredményeit a feltételes hozzáférés alkalmazása során. Előfordulhat, hogy a jelentés bejelentkezési eredménye sikeres bejelentkezést mutat be, de a bejelentkezés a feltételes hozzáférési szabályzat miatt nem sikerült. 
