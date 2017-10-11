---
title: "Azure Active Directory jelentéskészítés – gyakori kérdések |} Microsoft Docs"
description: "Az Azure Active Directory reporting gyakran ismételt kérdések."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: accf292f70bf0eafdefc00c3feeaf8e346605401
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-reporting-faq"></a>Azure Active Directory jelentéskészítés – gyakori kérdések

Ez a cikk kapcsolatos gyakori kérdések (GYIK) Azure Active Directory reporting rájuk adott válaszokat tartalmazza.  
További részletekért lásd: [Azure Active Directory reporting](active-directory-reporting-azure-portal.md). 

**K: Mi az az adatok megőrzése (naplózási és bejelentkezések) tevékenységi naplóit Azure-portálon?** 

**V:** nyújtunk adatok 7 nap szabad ügyfelei és az Azure AD Premium 1 vagy Premium 2 licenc váltásával, akár 30 napig lehet elérni az adatokat. A megőrzési további részletekért lásd: [Azure Active Directory-jelentés adatmegőrzési szabályai](active-directory-reporting-retention.md)

--- 

**K: mennyi ideig tart amíg I a feladat befejezése után is látható a tevékenységek adatai?**

**V:** tevékenység naplókat a 15 perc és egy óra közötti késéssel rendelkeznek. Bejelentkezési tevékenység naplók rendelkezik a legtöbb rekordok 15 perc közötti késleltetés és néhány rekord két óra.

---

**K: kell lennie a globális rendszergazdának kell tekintse meg a tevékenység naplózza az Azure portálon vagy az adatok lekérése az API-n keresztül?**

**V.:** Nem. Akkor lehet egy **biztonsági olvasó**, egy **biztonsági rendszergazda** vagy egy **globális rendszergazda** megtekintéséhez a jelentéskészítéshez szükséges adatok az Azure portálon vagy az API-n keresztül elérésével.

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

**V:** legfeljebb 1 millió rekordot lekérheti (Ha nem adja meg a top operátor, többsége a rekord rendező legutóbbi). Ha a "top" operátort használja, lekérheti mentése 500 KB-os rekordok. Mintalekérdezések a API használatáról itt található [Itt](active-directory-reporting-api-getting-started.md).

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

**V:** Igen, Identity Protection kiértékeli minden hitelesítési forgalom kockázatát e ha interaktív vagy nem interaktív. Azonban minden bejelentkezések csak jelentésben csak a interaktív bejelentkezések.

---

**K: hogyan töltheti Azure-portálon a "Kockázat megjelölt felhasználók" jelentést?**

**V:** letöltése beállítás *felhasználók meg van jelölve, a kockázat* jelentés hamarosan megjelenik.

---

**K: Hogyan állapítható meg, hogy miért a bejelentkezés vagy a felhasználó lett megjelölve kockázatos Azure-portálon?**

**V:** Premium edition ügyfelek tudhat meg többet az alapul szolgáló kockázati események a felhasználó a "Kockázat megjelölt felhasználók" vagy a "kockázatos bejelentkezések" parancsával. Szabad és alapvető edition ügyfelek eléréséhez at-risk felhasználók és bejelentkezések nélkül az alapul szolgáló kockázati események adatait.

---

**K: hogyan kiszámítása a bejelentkezéseket és a kockázatos bejelentkezések jelentés IP-címek??**

**V:** IP-címeket úgy, hogy nincs-e végleges kapcsolat közötti IP-cím és a számítógépre, hogy a cím fizikailag helyét adják. Ez például a mobil szolgáltatók és a kiállító központi készletek IP-címek nagyon gyakran távol, amelyekben ténylegesen szerepel az ügyféleszköz VPN tényezőktől bonyolítja. A fentiek IP-cím átalakítása a fizikai hely a nyomkövetési adatokat, beállításjegyzék-adatok, fordított keresések és egyéb információk alapján a lehető legkedvezőbb módon. 

---
