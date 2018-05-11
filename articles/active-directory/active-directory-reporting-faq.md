---
title: Azure Active Directory jelentéskészítés – gyakori kérdések |} Microsoft Docs
description: Az Azure Active Directory reporting gyakran ismételt kérdések.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 8963dfb3c2d8b81309f44fcdcaab7ddc0b403e7e
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory jelentéskészítés – gyakori kérdések

Ez a cikk reporting Azure Active Directory (Azure AD) kapcsolatos gyakran feltett kérdésekre adott válaszokat tartalmazza. További információk: [Jelentéskészítés az Azure Active Directoryban](active-directory-reporting-azure-portal.md). 

**K: használom a https://graph.windows.net/ &lt;bérlőnévvel&gt;/reports/ végpont API-k lekéréses az Azure AD naplózási és integrált alkalmazás használati jelentések jelentéskészítő rendszer programozott módon. Mit kell váltani a?**

**V:** kereshet a [API-referenciadokumentáció](https://developer.microsoft.com/graph/) használatát az új API-k elérésére megjelenítéséhez [Tevékenységjelentések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). A végpont két jelentés (naplózási és bejelentkezések) mindazokat az adatokat, a régi API-végpont portáltól biztosító rendelkezik. Az új végpont használó alkalmazások használatát, az eszköz használata és a felhasználó bejelentkezési adatait az Azure AD Premium licenccel rendelkező bejelentkezések jelentést is rendelkezik.


--- 

**K: használom a https://graph.windows.net/ &lt;bérlőnévvel&gt;/reports/ végpont való lekérésére az Azure AD biztonsági jelentések (észlelések, például a kiszivárgott hitelesítő adatok vagy névtelen IP-címekről bejelentkezéseket adott típusú) jelentéskészítő rendszer API-k programozott módon. Mit kell váltani a?**

**V:** használhatja a [Identity Protection kockázati események API](active-directory-identityprotection-graph-getting-started.md) való hozzáférés biztonsági észlelések Microsoft Graph használatával. Az új formátumot hogyan kérdezheti le adatokat, a speciális szűrési, mező kiválasztása, és több, nagyobb rugalmasságot biztosít, és egy típus a siem-ektől és egyéb adatok gyűjtemény eszközök egyszerűbb integrálása a kockázati eseményekről szabványosítja. Mivel az adatok egy, a régi lekérdezések nem helyettesítse be a egy új lekérdezést. Azonban [az új API-t használja a Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), vagyis a Microsoft Office 365 vagy Azure AD ilyen API-k szabvány. Így a munka szükséges vagy kiterjesztheti az aktuális MS Graph beruházások vagy súgó megkezdése az új szabványos platform az átállás.

--- 

**K: Mi az az adatok megőrzése (naplózási és bejelentkezések) tevékenységi naplóit Azure-portálon?** 

**V:** talál [mennyi ideig van az összegyűjtött adatok tárolt?} (Az azure Active Directory-jelentés adatmegőrzési](active-directory-reporting-retention.md#q-for-how-long-is-the-collected-data-stored) a választ a kérdésére.

--- 

**K: mennyi ideig tart amíg I a feladat befejezése után is látható a tevékenységek adatai?**

**V:** tevékenység naplókat a 15 perc és egy óra közötti késéssel rendelkeznek. Bejelentkezési tevékenység naplók legfeljebb 2 órával az egyes rekordok 15 perces is tarthat.

---

**K: van-e globális rendszergazda az Azure-portálon tevékenység bejelentkezéseket, vagy az adatok lekérése az API-n keresztül?**

**V.:** Nem. Kell egy **biztonsági olvasó**, egy **biztonsági rendszergazda**, vagy egy **globális rendszergazda** beolvasása Reporting adatokat az Azure portálon vagy az API-n keresztül.

---

**K: kaphatok Office 365 műveletnapló adatai az Azure portálon keresztül?**

**V:** annak ellenére, hogy Office 365 tevékenységet, és az Azure AD tevékenység naplók megosztás a könyvtár-erőforrások számos, ha azt szeretné, hogy az Office 365 tevékenységi naplóit, teljes egészében meg kell lépjen az Office 365 felügyeleti központban Office 365 tevékenység naplózási adatok beszerzése.

---


**K: milyen API-k használata Office 365 tevékenységi naplóit kapcsolatos adatok**

**V:** eléréséhez használja az Office 365 felügyeleti API-kat a [Office 365 tevékenység naplózza az API-n keresztül](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**K: hogyan sok rekord letölthető Azure-portálon?**

**V:** legfeljebb 120 K rekordok tölthet le az Azure-portálon. A rekordok alapján vannak rendezve *legutóbbi* és alapértelmezés szerint le a legfrissebb 120 K rögzíti. 

---

**K: hogyan sok rekord tudja lekérdezni a tevékenységek API keresztül?**

**V:** legfeljebb 1 millió rekordot lekérheti (Ha nem adja meg a top operátor, többsége a rekord rendező legutóbbi). Ha a "top" operátort használja, lekérheti mentése 500 KB-os rekordok. Az API használatával mintalekérdezések található [Itt](active-directory-reporting-api-getting-started.md).

---

**K: Hogyan tudom működtetni premium licenc?**

**V:** lásd [Ismerkedés az Azure Active Directory Premium](active-directory-get-started-premium.md) a választ a kérdésére.

---

**K: hogyan hamarosan kell meg a tevékenységek adatok premium licenc beolvasása után?**

**V:** Ha szabad licenc tevékenységek az adatok már rendelkezik, akkor ugyanazokat az adatokat láthatja. Ha még nem rendelkezik az adatokat, majd tart egy vagy két napot.

---

**K: jelennek meg adatok előző hónap után egy Azure AD premium-licenc beolvasásakor?**

**V:** nemrég váltott egy prémium verzió (beleértve a rendszer próbaverzióját), ha látható adatok mentése 7 napra kezdetben. Összesít adatokat, ha akár 30 napig jelenik meg.

---

**K: van a kockázati események azonosító adatok védelmét, de nem látható az megfelelő bejelentkezhet az összes bejelentkezéseket. Ez várható?**

**V:** Igen, Identity Protection kiértékeli-e minden hitelesítési forgalom kockázatát interaktív vagy nem interaktív. Azonban minden bejelentkezések csak jelentésben csak a interaktív bejelentkezések.

---

**K: hogyan töltheti Azure-portálon a "Kockázat megjelölt felhasználók" jelentést?**

**V:** letöltése beállítás *felhasználók meg van jelölve, a kockázat* jelentés hamarosan megjelenik.

---

**K: Hogyan állapítható meg, hogy miért a bejelentkezés vagy a felhasználó lett megjelölve kockázatos Azure-portálon?**

**V:** Premium edition ügyfelek tudhat meg többet az alapul szolgáló kockázati események a felhasználó a "Kockázat megjelölt felhasználók" vagy a "kockázatos bejelentkezések" parancsával. Szabad és alapvető edition ügyfelek eléréséhez at-risk felhasználók és bejelentkezések nélkül az alapul szolgáló kockázati események adatait.

---

**K: hogyan kiszámítása a bejelentkezéseket és a kockázatos bejelentkezések jelentés IP-címek?**

**V:** IP-címeket úgy, hogy nincs-e végleges kapcsolat közötti IP-cím és a számítógépre, hogy a cím fizikailag helyét adják. Ez például a mobil szolgáltatók és a kiállító központi készletek IP-címek nagyon gyakran távol, amelyekben ténylegesen szerepel az ügyféleszköz VPN tényezőktől bonyolítja. A fentiek IP-cím átalakítása a fizikai hely a nyomkövetési adatokat, beállításjegyzék-adatok, fordított keresések és egyéb információk alapján a lehető legkedvezőbb módon. 

---

**K: Mi nem a "Bejelentkezés az észlelt további kockázat" jelölésére kockázat eseményt?**

**V:** , és betekintést az összes kockázatos bejelentkezések a környezetben, a "Bejelentkezés az további veszélyt a észlelt" funkciókkal, mint a bejelentkezések, amelyek kizárólag az Azure AD Identity Protection előfizetők észlelések helyőrző.

---
