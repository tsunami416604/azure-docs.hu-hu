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
ms.openlocfilehash: 752e71f3c6b22a6d9f1e2392b58c01deef9de89c
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782164"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Az Azure Active Directory Power BI-tartalomcsomag használata

|  |
|--|
|Jelenleg az Azure AD Power BI-tartalomcsomag adatokat lekérni az Azure AD-bérlővel használja az Azure AD Graph API-kat. Ennek eredményeképpen látni a tartalomcsomagot a rendelkezésre álló adatok és a hitelesítő adatokat használja adatok néhány eltérése a [jelentéséért a Microsoft Graph API-k](concept-reporting-api.md). |
|  |

Rendszergazdaként elengedhetetlen, hogy tudja, hogyan használják felhasználói az Azure Active Directory funkcióit. Ez lehetővé teszi, hogy úgy tervezze meg az informatikai infrastruktúráját és kommunikációját, hogy növelni tudja annak használatát, és a lehető legtöbbet hozza ki az AAD funkcióiból. Az Azure Active Directory lehetővé teszi adatai részletesebb elemzését, így átláthatja, hogyan használhatja ezeket az adatokat részletesebb betekintések szerzéséhez abba, mi történik az Azure Active Directoryjukban az Ön számára legfontosabb képességek tekintetében.  Az Azure Active Directory API-jainak a Power BI-ba integrálásával könnyedén letöltheti az előre összeállított tartalomcsomagokat, valamint betekintést nyerhet az Azure Active Directoryn belüli tevékenységekbe a Power BI által biztosított részletes megjelenítések használatával. Létrehozhatja saját irányítópultját, és könnyedén megoszthatja szervezete bármelyik tagjával. 

Ez a témakör lépésről lépésre bemutatja, hogyan telepítheti és használhatja környezetében a tartalomcsomagot.

## <a name="installation"></a>Telepítés  

**A Power BI-tartalomcsomag telepítése:**

1. Jelentkezzen be a [Power BI-ba](https://app.powerbi.com/groups/me/getdata/services) Power BI-fiókjával (ez megegyezik az O365- vagy Azure AD-fiókjával).

2. A bal oldali navigációs panel alján kattintson az **Adatok lekérése** elemre.

    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/01.png)
 
3. A **Szolgáltatások** panelen kattintson a **Lekérés** gombra.
   
    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/02.png)

4.  Keresse meg az **Azure Active Directoryt**.

    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/03.png)
 
5.  Amikor a rendszer arra kéri, írja be Azure AD-bérlőazonosítóját, majd kattintson a **Tovább** gombra.

    > [!TIP] 
    > Gyorsan beszerezheti az Office 365-/Azure AD-bérlőazonosítóját, ha bejelentkezik az Azure AD portálra, megkeresi a könyvtárat, és kimásolja a **könyvtár-azonosítót** a [**Tulajdonságok** oldalról](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/04.png) 

6.  Kattintson a **Bejelentkezés** gombra. 
 
    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/05.png) 



7.  Írja be felhasználónevét és jelszavát, majd kattintson a **Bejelentkezés** gombra.
 
    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/06.png) 

8.  Az alkalmazással kapcsolatos beleegyezési párbeszédpanelen kattintson az **Elfogadás** elemre.
 
9.  Miután létrejött az Azure Active Directory-tevékenységnaplók irányítópultja, kattintson rá.
 
    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/08.png) 

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

A jelentéskészítés áttekintéséért lásd: [Jelentéskészítés az Azure Active Directoryban](overview-reports.md).
