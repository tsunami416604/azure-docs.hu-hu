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
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2cfdd5b903b8ffd9702745df150fe578d42e5a11
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735832"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Az Azure Active Directory Power BI-tartalomcsomag használata

|  |
|--|
|Jelenleg az Azure AD Power BI-tartalomcsomag adatokat lekérni az Azure AD-bérlővel használja az Azure AD Graph API-kat. Ennek eredményeképpen látni a tartalomcsomagot a rendelkezésre álló adatok és a hitelesítő adatokat használja adatok néhány eltérése a [jelentéséért a Microsoft Graph API-k](concept-reporting-api.md). |
|  |

INFORMATIKAI rendszergazdaként kell megértéséhez a felhasználók elfogadják és az Azure Active Directory-szolgáltatások használata. Ez lehetővé teszi, hogy az informatikai infrastruktúráját és kommunikációját, használatára és a legtöbbet az Azure AD-funkciók tervezése. A Power BI-tartalomcsomag az Azure Active Directory teszi lehetővé a részletesebb betekintések, mi történik a címtár adatainak további elemzéséhez. Az Azure Active Directory API-k a Power BI-ba történő integrációnak köszönhetően könnyedén letöltheti az előre elkészített tartalomcsomag és betekintést a biztosított részletes megjelenítések használatával, amely a Power bi-ban az Azure Active Directoryn belüli tevékenységekbe. Létrehozhatja saját irányítópultját, és könnyedén megoszthatja szervezete bármelyik tagjával. 

## <a name="install-the-content-pack"></a>A tartalomcsomag telepítésére

Tekintse meg a [rövid](quickstart-install-power-bi-content-pack.md) az Azure AD Power BI-tartalomcsomag telepítése.

## <a name="what-can-i-do-with-this-content-pack"></a>Mire használhatom ezt a tartalomcsomagot?

Mielőtt mélyebben beleásnánk magunkat, mi mindenre használhatja ezt a tartalomcsomagot, tekintse meg ezt a rövid áttekintést a tartalomcsomagban található különböző jelentésekről. **Az elmúlt 30 nap** jelentésadatai érhetők el.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Az Azure Active Directory-naplók tartalomcsomagjának jelen verziójában található jelentések

**Alkalmazáshasználat és -trendek jelentés**: Többet tudhat meg a szervezetében használt alkalmazásokról: melyiket használják a legtöbbet és mikor. Ebből a jelentésből megtudhatja, hogy a szervezetében a közelmúltban kiadott alkalmazásokat hogyan használják, vagy hogy mely alkalmazások népszerűek. Így javíthatja a kihasználtságot, ha azt tapasztalja, hogy az alkalmazást nem használják.

**Bejelentkezések hely és felhasználók szerint**: Megtekintheti az összes Azure-identitással végzett bejelentkezést, és láthatja a felhasználók identitását. Ennek segítségével részletesebb betekintést nyerhet az egyes bejelentkezésekbe, és választ kaphat többek között a következő kérdésekre:

- Honnan jelentkezett be ez a felhasználó?
- Melyik felhasználó jelentkezett be a legtöbbször, és honnan jelentkezett be? 
- Sikeres volt a bejelentkezés?  
 
Megtekintheti a részleteket is egy konkrét dátumra vagy helyre kattintva.

**Egyedi felhasználók alkalmazásonként**: Egy adott alkalmazás összes egyedi felhasználója. Ebbe csak azok a felhasználók számítanak bele, akik „*sikeresen*” bejelentkeztek az adott alkalmazásba.

**Eszközbejelentkezések**: Tekintse meg a szervezete felhasználói által használt operációs rendszereket és böngészőket a felhasználókra vonatkozó részletes információkkal együtt, például a következőkkel:

- Felhasználónév
- IP-cím
- Hely 
- Bejelentkezési állapot 

Ebből a jelentésből megismerheti a szervezetében használt különböző eszközprofilokat, és ez alapján dönthet az eszközházirendekről.

**SSPR tölcsér**: Megismerheti a szervezete jelszó-visszaállítási folyamatait. Betekintést nyerhet abba, hány új jelszót igényeltek az SSPR-eszközből, és hány volt ezek közül sikeres. Többet tudhat meg az SSPR tölcsérrel végzett sikertelen jelszó-visszaállításokról, és megvizsgálhatja, mi lehetett a sikertelenség oka. Ez jelentés részletesebb betekintést biztosít az SSPR eszköz szervezeten belüli használatába, és ezáltal segít a helyes döntések meghozatalában.

## <a name="customizing-azure-ad-activity-content-pack"></a>Az Azure AD-tevékenység tartalomcsomag személyre szabása

**Megjelenítés módosítása**: Megváltoztathatja egy jelentés megjelenítését a **Jelentés szerkesztése** elemre kattintással és a kívánt megjelenítés kiválasztásával.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/10.png) 

**További mezők belefoglalása**: Hozzáadhat mezőket a jelentéshez, vagy eltávolíthat mezőket azon vizualizáció kiválasztásával, amelyhez a hozzáadási/eltávolítási műveletet végre kívánja hajtani. A következő példában a „Sign-in status” (Bejelentkezési állapot) mezőt adom hozzá a táblanézethez. 
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/11.png) 

**Megjelenítések rögzítése az irányítópulton**: Személyre szabhatja irányítópultját, és felveheti saját megjelenítéseit a jelentésbe, amelyet azután az irányítópulton rögzíthet. A következő példában egy új, „Sign-in Status” (Bejelentkezési állapot) nevű szűrőt adtam hozzá és vettem fel a jelentésbe. Ezenkívül megváltoztattam a megjelenítést oszlopdiagramról vonaldiagramra. Ez az új vizualizáció rögzíthető az irányítópulton.

![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/12.png) 

![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/13.png) 
 

 


**Irányítópult megosztása**: Miután létrehozta a kívánt tartalmat, megoszthatja irányítópultját szervezete felhasználóival. Vegye figyelembe, hogy ha megoszt egy jelentést, azokat a mezőket láthatják, amelyeket a jelentésben kiválasztott.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>A Power BI-jelentés napi frissítésének ütemezése

A Power BI-jelentés napi frissítésének ütemezéséhez lépjen az **Adatkészletek > Beállítások > Frissítés ütemezése** részbe, és állítsa be az alábbiaknak megfelelően.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Frissítés a tartalomcsomag újabb verziójára

A tartalomcsomag frissítése új verzió beszerzéséhez:

- Töltse le az új tartalomcsomagot, és állítsa be az itt szereplő utasításoknak megfelelően.

- Ha elkészült a beállítással, lépjen az **Adatforrás > Beállítások > Adatforráshoz tartozó hitelesítő adatok** részbe, és adja meg újra a hitelesítő adatait az alábbiaknak megfelelően.

    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/16.png) 

Amint a tartalomcsomag új verziója működőképes, a korábbi tartalomcsomag alapjául szolgáló jelentések és a tartalomcsomaghoz tartozó adatkészletek törlésével eltávolíthatja a régi verziót, ha szükséges.

## <a name="still-having-issues"></a>Továbbra is problémákat tapasztal? 

Tekintse meg a [hibaelhárítási útmutatót](troubleshoot-content-pack.md). A Power BI-jal kapcsolatos általános útmutatásért tekintse meg ezeket a [súgótémaköröket](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>További lépések

* [A Power BI-tartalomcsomag telepítése](quickstart-install-power-bi-content-pack.md).
* [Tartalomcsomag-hibák elhárítása](troubleshoot-content-pack.md).
* [Mik az Azure AD-jelentések? ](overview-reports.md).
