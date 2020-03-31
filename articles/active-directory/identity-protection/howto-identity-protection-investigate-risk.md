---
title: Kockázat vizsgálata Az Azure Active Directory-identitásvédelem
description: Megtudhatja, hogy miként vizsgálhatja meg a kockázatos felhasználókat, észleléseket és bejelentkezéseket az Azure Active Directory identitásvédelemben
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253480"
---
# <a name="how-to-investigate-risk"></a>Hogyan: Vizsgálja meg a kockázatot

Az Identity Protection három jelentést biztosít a szervezeteknek, amelyek segítségével kivizsgálhatják a környezetükben lévő identitáskockázatokat. Ezek a jelentések a **kockázatos felhasználók**, **kockázatos bejelentkezések**és **a kockázatészlelések**. Az események vizsgálata kulcsfontosságú a biztonsági stratégia gyenge pontjainak jobb megértéséhez és azonosításához.

Mindhárom jelentés lehetővé teszi az események letöltését a alkalmazásba. CSV formátum további elemzéshez az Azure Portalon kívül. A kockázatos felhasználók és a kockázatos bejelentkezések jelentések lehetővé teszik a legutóbbi 2500 bejegyzés letöltését, míg a kockázatészlelési jelentés lehetővé teszi a legutóbbi 5000 rekord letöltését.

A szervezetek kihasználhatják a Microsoft Graph API-integrációelőnyeit, hogy összesítsék az adatokat más forrásokkal, amelyekhez szervezetként hozzáférhetnek.

A három jelentés az Azure > **Active** > **Directory-biztonság** **azure-portálon**található.

## <a name="navigating-the-reports"></a>Navigálás a jelentésekben

Minden jelentés a jelentés tetején látható időszak összes észlelési listájával jelenik meg. Minden jelentés lehetővé teszi az oszlopok hozzáadását vagy eltávolítását a rendszergazdai preferenciák alapján. A rendszergazdák a rendszerbe tölthetik le az adatokat. CSV vagy . JSON formátumban. A jelentések szűrhetők a jelentés tetején lévő szűrők kel.

Az egyes bejegyzések kiválasztása további bejegyzéseket is lehetővé tesz a jelentés tetején, például a bejelentkezés biztonsága szempontjából való megerősítését, a felhasználó biztonságának megerősítését, illetve a felhasználói kockázat megszüntetését.

Az egyes bejegyzések kijelölésével kibonthatja az észlelések alatti részletek ablakot. A részletek nézet lehetővé teszi a rendszergazdák számára, hogy minden egyes észlelési műveletet megvizsgáljanak és végrehajtsanak. 

![Példa az identitásvédelem jelentésre, amely kockázatos bejelentkezéseket és részleteket jelenít meg](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Kockázatos felhasználók

A kockázatos felhasználók jelentése által megadott információk alapján a rendszergazdák a következőket találhatják:

- Mely felhasználók vannak veszélyben, volt kockázat javító, vagy volt kockázat elutasította?
- Részletek az észlelésekről
- Az összes kockázatos bejelentkezés története
- Kockázati előzmények
 
A rendszergazdák ezután dönthetnek úgy, hogy végrehajtják ezeket az eseményeket. A rendszergazdák a következőket választhatják:

- A felhasználói jelszó alaphelyzetbe állítása
- A felhasználói biztonság megerősítésének megerősítése
- Felhasználói kockázat elvetése
- A felhasználó bejelentkezésének letiltása
- Az Azure ATP további használatának vizsgálata

## <a name="risky-sign-ins"></a>Kockázatos bejelentkezések

A kockázatos bejelentkezések jelentés szűrhető adatokat tartalmaz az elmúlt 30 nap (1 hónap).

A kockázatos bejelentkezések jelentésáltal megadott információk alapján a rendszergazdák a következőket találhatják:

- Mely bejelentkezések minősülnek veszélyeztetettnek, megerősített veszélynek, megerősített biztonságosnak, elbocsátottnak vagy helyreállítva.
- Valós idejű és összesített kockázati szintek a bejelentkezési kísérletekhez társítva.
- Aktivált észlelési típusok
- Feltételes hozzáférési házirendek alkalmazva
- MFA részletek
- Eszközadatok
- Alkalmazásadatok
- Helyadatok

A rendszergazdák ezután dönthetnek úgy, hogy végrehajtják ezeket az eseményeket. A rendszergazdák a következőket választhatják:

- Bejelentkezési kompromisszum megerősítése
- Bejelentkezési biztonság megerősítése

## <a name="risk-detections"></a>Kockázatészlelések

A kockázatészlelési jelentés szűrhető adatokat tartalmaz az elmúlt 90 napra (3 hónapra).

A kockázatészlelési jelentésben megadott információk alapján a rendszergazdák a következőket találhatják:

- Az egyes kockázatészlelési adatok, beleértve a típust is.
- Az egyidejűleg felmerülő egyéb kockázatok
- Bejelentkezési kísérlet helye
- A Microsoft Cloud App Security (MCAS) további részletekre hivatkozik.

A rendszergazdák ezután dönthetnek úgy, hogy visszatérnek a felhasználó kockázat- vagy bejelentkezési jelentéséhez, hogy az összegyűjtött információk alapján műveleteket hajtanak végre.

## <a name="next-steps"></a>További lépések

- [A kockázatok csökkentésére rendelkezésre álló politikák](concept-identity-protection-policies.md)

- [Bejelentkezési és felhasználói kockázati házirendek engedélyezése](howto-identity-protection-configure-risk-policies.md)
