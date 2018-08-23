---
title: Azure Active Directory jelentéskészítés – gyakori kérdések |} A Microsoft Docs
description: Jelentéskészítés – gyakori kérdések az Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f1683321e23eff82e73dc9bb44941fc390633b8c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42059175"
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory jelentéskészítés – gyakori kérdések

Ez a cikk – gyakori kérdések az Azure Active Directory (Azure AD) jelentéskészítési adott válaszokat tartalmazza. További információk: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md). 

## <a name="getting-started"></a>Első lépések 

**K: használom a https://graph.windows.net/&lt; bérlő neve&gt;lekéréses az Azure AD naplózási és használati integrált alkalmazás API-k programozott módon jelentések jelentéskészítő rendszer /reports/ végpont. Mit kell váltani a?**

**V:** keresse ki a [API referenciadokumentációra](https://developer.microsoft.com/graph/) megtekintéséhez, hogyan használhatja az új API-k eléréséhez [tevékenységre vonatkozó jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Ez a végpont rendelkezik két jelentés (naplózás és a bejelentkezések) kapott a régi API-végpont az összes adat biztosító. Az új végpont bejelentkezésekre, a prémium szintű Azure AD-licenccel, amelyek segítségével az alkalmazások használatát, eszközhasználat és felhasználói bejelentkezési adatokat is tartalmaz.

--- 

**K: használom a https://graph.windows.net/&lt; bérlő neve&gt;/reports/ végpont lekérni az Azure AD biztonsági jelentések (észlelések, mint a kiszivárgott hitelesítő adatok vagy névtelen IP-címekről történő bejelentkezések adott típusú) jelentéskészítési rendszer API-k programozott módon. Mit kell váltani a?**

**V:** használhatja a [Identity Protection kockázati események API](../identity-protection/graph-get-started.md) való hozzáférés biztonsági észlelések Microsoft Graphon keresztül. Az új formátum hogyan kérdezheti le adatokat, a Speciális szűrés, mező kiválasztása és több, nagyobb rugalmasságot biztosít, és a kockázati események egységesíti az siem-EK és más adatok gyűjtése eszközök egyszerűbb integráció egy típust. Mivel az adatok különböző, a régi lekérdezések nem helyettesítse be a egy új lekérdezést. Azonban [az új API-t használja a Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), azaz a Microsoft standard ilyen API-t az Office 365 vagy Azure ad-ben. Így a kell elvégeznie, vagy végrehajthatja a meglévő az MS Graph-befektetéseit vagy súgó megkezdése az új standard szintű platform az átállás.

--- 

**K: Hogyan szerezhetem be a prémium licencre?**

**V:** lásd [Ismerkedés az Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) a választ a kérdésére.

---

**K: hogyan hamarosan kell tevékenységek adatainak megtekintéséhez prémium licencre első után?**

**V:** már tevékenységek adatainak, ingyenes licenccel rendelkezik, akkor láthatja, hogy ugyanazokat az adatokat. Ha nem rendelkezik semmilyen adatot, majd vesz igénybe egy vagy két napot.

---

**K: láthatók a múlt havi adatok lekérdezése az Azure AD premium-licenc után?**

**V:** nemrég egy prémium szintű verzió (beleértve a próbaverzió) vált, amennyiben látható adatok mentése és 7 nap kezdetben. Gyűlnek az adatok, látni fogja legfeljebb 30 napig.

---

**K: van szükségem egy globális rendszergazda az Azure Portalon tevékenység bejelentkezések megtekintéséhez vagy az API-n keresztül az adatok a?**

**V.:** Nem. Kell egy **biztonsági olvasó**, amely egy **biztonsági rendszergazda**, vagy egy **globális rendszergazdai** lekérése a jelentéskészítés az Azure Portalon vagy az API-n keresztül.

---


## <a name="activity-logs"></a>Tevékenységnaplók


**Kérdés: Mi az adatmegőrzés (naplózás és a bejelentkezések) tevékenységi naplóit az Azure Portalon?** 

**V:** lásd [mennyi ideig kell a begyűjtött adatok tárolt?](reference-reports-data-retention.md#q-for-how-long-is-the-collected-data-stored) a választ a kérdésére.

--- 

**K: mennyi ideig tart mindaddig, amíg az látható, hogy a tevékenységek adatai I saját feladat befejeződése után?**

**V:** auditnaplók tevékenység egy óra és a 15 perc késéssel rendelkeznek. Bejelentkezési tevékenységeket tartalmazó naplók is igénybe vehet 15 perctől akár 2 órát az egyes bejegyzéseket.

---


**K: be az Office 365-Tevékenységnaplók adatait az Azure Portalon keresztül?**

**V:** annak ellenére, hogy az Office 365-Tevékenységnaplók és az Azure AD tevékenység naplók a könyvtár-erőforrások jelentős részén osztoznak, ha azt szeretné, hogy az Office 365-Tevékenységnaplók teljes listáját meg kell nyissa meg az Office 365 felügyeleti központ, az Office 365-Tevékenységnaplók adatait beolvasni.

---


**K: mely API-k használható Office 365-Tevékenységnaplók információt szeretne kapni?**

**V:** eléréséhez használja az Office 365 felügyeleti API-k a [Office 365-Tevékenységnaplók az API-n keresztül naplók](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**K: mennyi rekord is letölthető az Azure Portalon?**

**V:** legfeljebb 5000 rekordok tölthet le az Azure Portalon. A rekordok alapján vannak rendezve *legutóbbi* és alapértelmezés szerint az utolsó 5000 rekordok beolvasása.

---

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

**K: van egy kockázati esemény az Identity Protection, de nem látok kapcsolódó jelentkezzen be az összes bejelentkezés. Ez várható?**

**V:** Igen, Identity Protection kiértékeli-e az összes hitelesítési folyamatok kockázati e interaktív vagy nem interaktív. Az összes bejelentkezési csak jelentések azonban csak az interaktív bejelentkezések jeleníti meg.

---

**K: Hogyan tölthetem le a "Kockázatosként megjelölt felhasználók" jelentést az Azure Portalon?**

**V:** a melyre kattintva letölthető *kockázatosként megjelölt felhasználók* jelentés hamarosan közzétesszük.

---

**K: Hogyan tudhatom meg, hogy miért vagy egy felhasználó egy bejelentkezési lett megjelölve kockázatos az Azure Portalon?**

**V:** Premium Editiont használó ügyfelek többet is megtudhat a mögöttes kockázatos eseményekről kattintva a felhasználó a "Kockázatosként megjelölt felhasználók" vagy a "kockázatos bejelentkezések" gombjára kattintva. Ingyenes és alapszintű termékváltozatot használó ügyfelek tekintheti meg a kockázatos besorolású felhasználók és bejelentkezések nélkül az alapul szolgáló kockázati események adatait beolvasása.

---

**K: hogyan IP-címek alapján számítjuk ki a bejelentkezési és a kockázatos bejelentkezések jelentés?**

**V:** IP-címek kibocsátott oly módon, hogy ez nem végleges közötti IP-cím és a számítógép ezzel a címmel fizikailag helyét. Ez az összetett tényezők, például a mobil-szolgáltatók és a VPN-EK központi készletek IP-címek nagyon gyakran kiállító távol, ahol az ügyféleszközön ténylegesen szolgál. A fentiek IP-cím átalakítása egy fizikai helyhez célja a legjobb nyomkövetéseket, beállításjegyzék-adatok, fordított keresések és egyéb információk alapján. 

---

**K: Mi a nem a "Sign-in további észlelt kockázattal rendelkező" jelölésére kockázati esemény?**

**V:** , hogy az összes kockázatos bejelentkezések betekintést a környezetében, helyőrző bejelentkezések, amelyek az Azure AD Identity Protection-előfizetők exkluzív észlelések "bejelentkezés-növeli annak kockázatát a észlelt" működik.

---

**K: Mi a nem a "Sign-in további észlelt kockázattal rendelkező" jelölésére kockázati esemény?**

**V:** , hogy az összes kockázatos bejelentkezések betekintést a környezetében, helyőrző bejelentkezések, amelyek az Azure AD Identity Protection-előfizetők exkluzív észlelések "bejelentkezés-növeli annak kockázatát a észlelt" működik.

---

## <a name="conditional-access"></a>Feltételes hozzáférés

**K: Mi új ezzel a funkcióval?**

**V:** ügyfelek mostantól hibáinak elhárítása a feltételes hozzáférési szabályzatok minden bejelentkezésekre keresztül. Ügyfelek tekintheti meg a feltételes hozzáférési állapotát és adatait, a bejelentkezés és az eredmény az egyes szabályzatok alkalmazott házirendek bemutatása.

**K: Hogyan kezdhetem?**

**V:** a kezdéshez:
    * Keresse meg a bejelentkezési jelentést a [az Azure portal](https://portal.azure.com). 
    * Kattintson a bejelentkezés hibaelhárítása kívánt.
    * Keresse meg a **feltételes hozzáférési** fülre. Itt tekintheti meg a bejelentkezési és az eredmény minden szabályzat érintett összes szabályzatot. 
    
**Kérdés: melyek az összes lehetséges értékek a feltételes hozzáférési állapot?**

**V:** feltételes hozzáférési állapot a következő értékeket veheti fel:
    * **Nem alkalmazott**: Ez azt jelenti, hogy hiba történt a nem a felhasználó és az alkalmazás hatókörében a feltételes hozzáférési szabályzat. 
    * **Sikeres**: Ez azt jelenti, hogy hiba történt egy feltételes hozzáférési szabályzat és a hatókör az alkalmazás és a feltételes hozzáférési szabályzatok is sikeresen teljesül. 
    * **Hiba**: Ez azt jelenti, hogy hiba történt egy feltételes hozzáférési szabályzat és a hatókör az alkalmazás és a feltételes hozzáférési szabályzatok nem teljesültek. 
    
**Kérdés: melyek az összes lehetséges értékei a feltételes hozzáférési szabályzat eredménye?**

**V:** feltételes hozzáférési szabályzat az alábbi eredményeket rendelkezhet:
    * **Sikeres**: A szabályzat sikeresen teljesült.
    * **Hiba**: A házirend nem teljesült.
    * **Nem alkalmazott**: lehetséges, hogy a nem felelt meg a szabályzat feltételeit.
    * **Nincs engedélyezve a**: letiltott állapotban a házirend miatt nem lehetséges. 
    
**Kérdés: a házirend nevére a jelentésben az összes be nem egyezik meg a szabályzat nevét, a hitelesítésszolgáltató. Hogy miért?**

**V:** a házirend nevére a jelentésben az összes bejelentkezési alapul a hitelesítésszolgáltató házirend neve a bejelentkezés során. Ez a házirend neve a hitelesítésszolgáltató inkonzisztens lehet, ha frissítette a házirend nevére később, a bejelentkezés után.