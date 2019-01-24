---
title: Az Azure Active Directory-jelentések – gyakori kérdések |} A Microsoft Docs
description: Gyakori quesitons Azure Active Directory-jelentések körül.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d6062720e3b747966c60426a5db76e6dd18109a7
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813016"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Gyakori kérdések az Azure Active Directory-jelentések körül

Ez a cikk – gyakori kérdések az Azure Active Directory (Azure AD) jelentéskészítési adott válaszokat tartalmazza. További információk: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md). 

## <a name="getting-started"></a>Első lépések 

**K: Jelenleg használja a https://graph.windows.net/&lt; bérlő neve&gt;lekéréses az Azure AD naplózási és használati integrált alkalmazás API-k programozott módon jelentések jelentéskészítő rendszer /reports/ végpont. Mit kell váltani a?**

**VÁLASZ:** Keresse meg a [API-referencia](https://developer.microsoft.com/graph/) való bemutatja, hogyan [tevékenységre vonatkozó jelentések eléréséhez használja az API-k](concept-reporting-api.md). Ez a végpont rendelkezik két jelentés (**naplózási** és **bejelentkezések**) biztosító, itt van a régi API-végpont az összes adatot. Az új végpont bejelentkezésekre, a prémium szintű Azure AD-licenccel, amelyek segítségével az alkalmazások használatát, eszközhasználat és felhasználói bejelentkezési adatokat is tartalmaz.

--- 

**K: Jelenleg használja a https://graph.windows.net/&lt; bérlő neve&gt;/reports/ végpont lekérni az Azure AD biztonsági jelentések (észlelések, mint a kiszivárgott hitelesítő adatok vagy névtelen IP-címekről történő bejelentkezések adott típusú) jelentéskészítési rendszer API-k programozott módon. Mit kell váltani a?**

**VÁLASZ:** Használhatja a [Identity Protection kockázati események API](../identity-protection/graph-get-started.md) való hozzáférés biztonsági észlelések Microsoft Graphon keresztül. Az új formátum hogyan kérdezheti le adatokat, a Speciális szűrés, mező kiválasztása és több, nagyobb rugalmasságot biztosít, és a kockázati események egységesíti az siem-EK és más adatok gyűjtése eszközök egyszerűbb integráció egy típust. Mivel az adatok különböző, a régi lekérdezések nem helyettesítse be a egy új lekérdezést. Azonban [az új API-t használja a Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), azaz a Microsoft standard ilyen API-t az Office 365 vagy Azure ad-ben. Így a kell elvégeznie, vagy végrehajthatja a meglévő az MS Graph-befektetéseit vagy súgó megkezdése az új standard szintű platform az átállás.

--- 

**K: Hogyan szerezhetem be a prémium szintű licenccel?**

**VÁLASZ:** Lásd: [Ismerkedés az Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) az Azure Active Directory-kiadás frissítése.

---

**K: Hogy mikor kell tevékenységek adatainak megtekintéséhez prémium licencre bevezetés után?**

**VÁLASZ:** Ha már rendelkezik tevékenységek adatainak, ingyenes licenccel, akkor azonnal látható. Ha nem rendelkezik semmilyen adatot, majd vesz igénybe a jelentések megjelennek az adatok egy vagy két napot.

---

**K: Ha prémium szintű Azure AD-licenccel is láthatók múlt havi adatokat?**

**VÁLASZ:** Ha a közelmúltban vált a prémium szintű verzió (beleértve a próbaverzió), láthatja adatok mentése és 7 nap kezdetben. Amikor gyűlnek az adatok, láthatja a adatok az elmúlt 30 nap során.

---

**K: Van szükségem az Azure Portalon tevékenység bejelentkezések megtekintéséhez vagy az API-n keresztül az adatok a globális rendszergazdai?**

**VÁLASZ:** Nem, emellett a jelentési adatokat a portálon keresztül vagy az API-n keresztül, ha egy **biztonsági olvasó** vagy **biztonsági rendszergazda** a bérlő számára. Természetesen **globális rendszergazdák** is hozzáférhetnek ezekhez az adatokhoz.

---


## <a name="activity-logs"></a>Tevékenységnaplók


**K: Mi az adatmegőrzés (naplózás és a bejelentkezések) tevékenységi naplóit az Azure Portalon?** 

**VÁLASZ:** A következő táblázat felsorolja az Adatmegőrzés időtartama tevékenységi naplóit. További információkért lásd: [adatmegőrzési házirendek az Azure AD-jelentések](reference-reports-data-retention.md).

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Naplók             | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezések               | –           | 30 nap             | 30 nap             |
| Azure MFA Usage        | 30 nap       | 30 nap             | 30 nap             |

--- 

**K: Mennyi ideig tart mindaddig, amíg az látható, hogy a tevékenységek adatai I saját feladat befejeződése után?**

**VÁLASZ:** Auditnaplók egy óra és a 15 perc késéssel rendelkeznek. Bejelentkezési tevékenységeket tartalmazó naplók is igénybe vehet 15 perctől akár 2 órát az egyes bejegyzéseket.

---

**K: Szerezhető be az Office 365-Tevékenységnaplók adatait az Azure Portalon keresztül?**

**VÁLASZ:** Az Office 365- és az Azure AD-tevékenységnaplók a Directory-erőforrások jelentős részén osztoznak, de ha teljes képet szeretne az Office 365-tevékenységnaplókról, látogasson el az Office 365 Felügyeleti központba, ahol elérheti az Office 365-tevékenységnaplók adatait.

---

**K: Melyik API-k használható Office 365-Tevékenységnaplók információt szeretne kapni?**

**VÁLASZ:** Használja a [Office 365 felügyeleti API-k](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) eléréséhez az Office 365 Tevékenységnaplók az API-n keresztül.

---

**K: Hány rekordok is letölthető az Azure Portalon?**

**VÁLASZ:** Legfeljebb 5000 rekordok letöltheti az Azure Portalról. A rekordok alapján vannak rendezve *legutóbbi* és alapértelmezés szerint az utolsó 5000 rekordok beolvasása.

---

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

**K: Identity Protection egy kockázati esemény, de nem látok kapcsolódó jelentkezzen be a bejelentkezési jelentésben. Ez várható?**

**VÁLASZ:** Igen, az Identity Protection kockázatát az összes hitelesítési folyamatok kiértékeli, interaktív vagy nem interaktív. Az összes bejelentkezési csak jelentések azonban csak az interaktív bejelentkezések jeleníti meg.

---

**K: Honnan tudhatom meg, hogy miért vagy egy felhasználó egy bejelentkezési lett megjelölve kockázatos az Azure Portalon?**

**VÁLASZ:** Ha rendelkezik egy **prémium szintű Azure AD** -előfizetéssel, további információ a mögöttes kockázatos eseményekről kiválasztásával a felhasználó a **kockázatosként megjelölt felhasználók** vagy egy rekordot a kiválasztásával a **kockázatos bejelentkezések** jelentést. Ha rendelkezik egy **ingyenes** vagy **alapszintű** előfizetést, akkor megtekintheti a felhasználói kockázat és a kockázatos bejelentkezések jelentéseit, de nem látja az alapul szolgáló kockázati események adatait.

---

**K: Hogyan számítják ki IP-címek a bejelentkezéseket és a kockázatos bejelentkezések jelentés?**

**VÁLASZ:** IP-címeket úgy, hogy ez nem végleges közötti IP-cím és a számítógép ezzel a címmel fizikailag helyét adják ki. IP-címek leképezése tovább bonyolította tényezők, például a mobil-szolgáltatók és a VPN-EK központi készletek IP-címek nagyon gyakran kiállító távol, ahol az ügyféleszközön ténylegesen szolgál. Jelenleg az Azure AD-jelentések, IP-cím átalakítása egy fizikai helyhez célja a legjobb nyomkövetéseket, beállításjegyzék-adatok, fordított keresések és egyéb információk alapján. 

---

**K: A "Sign-in további észlelt kockázattal rendelkező" jelölésére kockázati esemény funkciója?**

**VÁLASZ:** Hogy betekintést a kockázatos bejelentkezések a környezetében, a "Sign-in további észlelt kockázattal rendelkező" függvény helyőrző, a bejelentkezések, amelyek a rendszer kizárólag az Azure AD Identity Protection előfizetőknek érhető el.

---

## <a name="conditional-access"></a>Feltételes hozzáférés

**K: Ez a szolgáltatás újdonságai**

**VÁLASZ:** Ügyfeleink most elháríthatja a feltételes hozzáférési szabályzatok minden bejelentkezésekre keresztül. Ügyfelek tekintheti meg a feltételes hozzáférési állapotát és adatait, a bejelentkezés és az eredmény az egyes szabályzatok alkalmazott házirendek bemutatása.

**K: Hogyan kezdhetem?**

**VÁLASZ:** Első lépések:
    * Keresse meg a bejelentkezési jelentést a [az Azure portal](https://portal.azure.com). 
    * Kattintson a bejelentkezés hibaelhárítása kívánt.
    * Keresse meg a **feltételes hozzáférési** fülre. Itt tekintheti meg a bejelentkezési és az eredmény minden szabályzat érintett összes szabályzatot. 
    
**K: Mik a feltételes hozzáférési állapot lehetséges értékeinek?**

**VÁLASZ:** Feltételes hozzáférési állapot a következő értékeket veheti fel:
    * **Nem alkalmazott**: Ez azt jelenti, hogy hiba történt a nem a felhasználó és az alkalmazás hatókörében a feltételes hozzáférési szabályzat. 
    * **Sikeres**: Ez azt jelenti, hogy hiba történt egy feltételes hozzáférési szabályzat és a hatókör az alkalmazás és a feltételes hozzáférési szabályzatok is sikeresen teljesül. 
    * **Hiba**: Ez azt jelenti, hogy hiba történt egy feltételes hozzáférési szabályzat és a hatókör az alkalmazás és a feltételes hozzáférési szabályzatok nem teljesültek. 
    
**K: Mik a feltételes hozzáférési szabályzat eredmény lehetséges értékeinek?**

**VÁLASZ:** Feltételes hozzáférési szabályzat az alábbi eredményeket veheti fel:
    * **Sikeres**: A szabályzat sikeresen teljesült.
    * **Hiba**: A szabályzat nem teljesült.
    * **Nem alkalmazott**: Ez lehet, mert nem felelt meg a szabályzat feltételeit.
    * **Nincs engedélyezve a**: A letiltott állapotban házirend okozza. 
    
**K: A házirend nevére a jelentésben az összes be nem egyezik meg a szabályzat nevét, a hitelesítésszolgáltató. Hogy miért?**

**VÁLASZ:** A házirend nevére a jelentésben az összes bejelentkezési a bejelentkezés során a hitelesítésszolgáltató házirend neve alapul. Ez a házirend neve a hitelesítésszolgáltató inkonzisztens lehet, ha frissítette a házirend nevére később, a bejelentkezés után.

**K: A bejelentkezés le lett tiltva a feltételes hozzáférési szabályzat miatt, de a bejelentkezési Tevékenységjelentés jeleníti meg, hogy a bejelentkezés sikeres volt. Hogy miért?**

**VÁLASZ:** Jelenleg a bejelentkezési jelentésben előfordulhat, hogy nem pontos eredmények megjelenítése az Exchange ActiveSync-forgatókönyvek a feltételes hozzáférés alkalmazása esetén. Lehet esetek, amikor a bejelentkezési eredménye a jelentés megjeleníti a bejelentkezés sikeres, de a bejelentkezés ténylegesen nem sikerült, mert a feltételes hozzáférési szabályzat. 
