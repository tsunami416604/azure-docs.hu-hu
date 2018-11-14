---
title: Az Azure Active Directory Power BI-tartalomcsomag használata | Microsoft Docs
description: Útmutató az Azure Active Directory Power BI-tartalomcsomag használatához | Microsoft Docs
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d810b8be496bbfd1c5bd88d8221e77dd5b76c889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622965"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Az Azure Active Directory Power BI-tartalomcsomag használata

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

A Power BI-tartalomcsomag csomag az Azure Active Directory (Azure AD) segítségével előre elkészített jelentéseket tartalmazza megértéséhez, a felhasználók elfogadják és az Azure AD-funkciók használatához. Ez lehetővé teszi, hogy betekintést nyerhet a biztosított részletes megjelenítések használatával a Power bi-ban, a címtárhoz tartozó összes tevékenység. Is létrehozhatja saját irányítópultját és a szervezet bárkivel megoszthatja azt. 

## <a name="prerequisites"></a>Előfeltételek

A tartalomcsomag használatához egy Azure AD prémium verzió (P1 vagy P2)-licencre van szüksége. 

## <a name="install-the-content-pack"></a>A tartalomcsomag telepítésére

Tekintse meg a [rövid](quickstart-install-power-bi-content-pack.md) az Azure AD Power BI-tartalomcsomag telepítése.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Az Azure ad-ben jelen verziójában található jelentések-naplók

Az alábbi jelentések az Azure AD Power BI-tartalomcsomag tartalmazza. A jelentések adatait tartalmazza a **elmúlt 30 napban**.

**Az alkalmazások használatát és a trendek jelentés**: Ez a jelentés tartalmazza a szervezetben használt alkalmazások betekintést. A legnépszerűbb alkalmazások listáját, vagy Ön nemrégiben egyik tagján jelennek meg a szervezet alkalmazás használatának megértéséhez. Ez lehetővé teszi, hogy nyomon követheti, és javíthatja a használat idővel.

**Bejelentkezések hely és a felhasználók által**: Ez a jelentés adatokat tartalmazza az összes a bejelentkezés Azure-identitással végzett. Ez a jelentés egyes részletesen elemezheti, és válaszoljon a kérdésekre, például:

- Hol volt a felhasználó be?
- Melyik felhasználó jelentkezett be a legtöbbször, és honnan jelentkezett be? 
- Sikeres volt a bejelentkezés?  
 
Egy konkrét dátumra vagy helyre kattintva is szűrheti az eredményeket.

**Egyedi felhasználók alkalmazásonként**: Ez a jelentés egy adott alkalmazás összes egyedi felhasználója nézetét jeleníti meg. Csak tartalmazza, akik rendelkeznek "*sikeresen*" bejelentkeztek az alkalmazásba.

**Eszközbejelentkezések**: Ez a jelentés segít megérteni a szervezetében használt különböző eszközprofilokat, és a használat alapján határozza meg. Az operációs rendszer, és jelentkezzen be alkalmazások, valamint a felhasználók többek között részletes adatai a használt böngészőket típusú adatokat biztosít:

- Felhasználónév
- IP-cím
- Hely 
- Bejelentkezési állapot 

**SSPR tölcsér**: Ez a jelentés segítségével megismerheti az SSPR eszköz szervezeten belüli használatáról. Megtekintheti, hogy hány új jelszót igényeltek az SSPR-eszközből, és ezek közül hány sikeres volt. Többet tudhat meg a jelszó-visszaállítási hibák is, és miért történt az egyes hibák ismertetése. 

## <a name="customize-azure-ad-activity-content-pack"></a>Azure AD-tevékenység tartalomcsomag testreszabása

**Megjelenítés módosítása**: Megváltoztathatja egy jelentés megjelenítését a **Jelentés szerkesztése** elemre kattintással és a kívánt megjelenítés kiválasztásával.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/10.png) 

**További mezők belefoglalása**: Hozzáadhat mezőket a jelentéshez, vagy eltávolíthat mezőket azon vizualizáció kiválasztásával, amelyhez a hozzáadási/eltávolítási műveletet végre kívánja hajtani. Például a "bejelentkezési állapot" mezőben adhat hozzá a táblázatos nézetre alább látható módon. 
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/11.png) 

**Rögzítheti a vizualizációt az irányítópulton**: testre szabhatja az irányítópulton, többek között a saját megjelenítéseit a jelentésbe, és azt rögzítés az irányítópulton. 

![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/13.png) 
 
**Az irányítópult megosztása**: is megoszthatja az irányítópultot a felhasználókat a szervezetben. Miután megoszt egy jelentést, a felhasználók láthatják a jelentésben kijelölt mezők.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>A Power BI-jelentés napi frissítésének ütemezése

A Power BI-jelentés napi frissítésének ütemezéséhez, lépjen a **adatkészletek** > **beállítások** > **frissítés ütemezése** , és állítsa be alább látható módon.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>A tartalomcsomag újabb verziójára való frissítéséhez

Ha a tartalomcsomag frissítése újabb verzióra szeretné:

- Töltse le az új tartalomcsomagot, és állítsa be az ebben a cikkben szereplő utasítások segítségével.

- Után állított be, lépjen a **adatforrás** > **beállítások** > **adatforráshoz tartozó hitelesítő adatok** írja be újra a hitelesítő adatait.

    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/16.png) 

Miután meggyőződött arról, hogy a tartalomcsomag új verziója a várt módon működik-e, eltávolíthatja a régi verziót, ha az alapul szolgáló jelentések és a tartalomcsomaghoz tartozó adatkészletek törlésével szükséges.

## <a name="still-having-issues"></a>Továbbra is problémákat tapasztal? 

Tekintse meg a [hibaelhárítási útmutatót](troubleshoot-content-pack.md). A Power BI-jal kapcsolatos általános útmutatásért tekintse meg ezeket a [súgótémaköröket](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
 
 
## <a name="next-steps"></a>További lépések

* [A Power BI-tartalomcsomag telepítése](quickstart-install-power-bi-content-pack.md).
* [A tartalomcsomag hibáinak elhárítása](troubleshoot-content-pack.md).
* [Mik az Azure AD-jelentések? ](overview-reports.md).
