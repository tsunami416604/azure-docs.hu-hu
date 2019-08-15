---
title: Az Azure Active Directory Power BI-tartalomcsomag használata | Microsoft Docs
description: Útmutató az Azure Active Directory Power BI-tartalomcsomag használatához | Microsoft Docs
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d56802e96028b6b01b6be749405c56df2648161
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988235"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>A Azure Active Directory Power BI Content Pack használata

|  |
|--|
|Az Azure AD Power BI-tartalomcsomag jelenleg az Azure AD Graph API-jaival kéri le az adatokat az Azure AD-bérlőről. Ezért a tartalomcsomagban elérhető adatok, valamint a [jelentéskészítési Microsoft Graph API-k](concept-reporting-api.md) által lekért adatok között lehet némi eltérés. |
|  |

A Azure Active Directory (Azure AD) Power BI tartalomkezelő csomagja előre elkészített jelentéseket tartalmaz, amelyek segítségével megismerheti, hogy a felhasználók hogyan fogadják el és használják az Azure AD szolgáltatásait. Ez lehetővé teszi, hogy betekintést nyerjen a címtárban lévő összes tevékenységbe, a Power BI gazdag vizualizációs felületét használva. Létrehozhatja saját irányítópultját is, és megoszthatja azt bárkivel a szervezetében. 

## <a name="prerequisites"></a>Előfeltételek

A Content Pack használatához Azure AD prémium (P1/P2) licencre van szükség. A Azure Active Directory kiadásának frissítéséhez tekintse meg a [prémium szintű Azure Active Directory első lépéseivel foglalkozó](../fundamentals/active-directory-get-started-premium.md) témakört.

## <a name="install-the-content-pack"></a>A Content Pack telepítése

Tekintse meg [](quickstart-install-power-bi-content-pack.md) az Azure ad Power bi Content Pack telepítéséhez szükséges rövid útmutatót.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Az Azure AD-naplók jelen verziójában található jelentések

Az alábbi jelentések az Azure AD Power BI Content Pack csomagban találhatók. A jelentések az **elmúlt 30 nap**adatait tartalmazzák.

**Alkalmazások használatának és trendek jelentés**:  Ez a jelentés betekintést nyújt a szervezetében használt alkalmazásokba. Megtekintheti a legnépszerűbb alkalmazások listáját, vagy megtudhatja, hogyan használja a rendszer a közelmúltban a cégen belül kivetített alkalmazást. Így nyomon követheti és javíthatja a használatot az idő múlásával.

**Bejelentkezések hely és felhasználók szerint**: Ez a jelentés az Azure Identity használatával végrehajtott bejelentkezések adatait tartalmazza. Ezzel a jelentéssel megtekintheti az egyes bejelentkezéseket, és választ kaphat a következő kérdésekre:

- Honnan jelentkezett be a felhasználó?
- Melyik felhasználó jelentkezett be a legtöbbször, és honnan jelentkezett be? 
- Sikeres volt a bejelentkezés?  
 
Az eredményeket egy adott dátum vagy hely kiválasztásával is szűrheti.

**Egyedi felhasználók száma egy alkalmazásban**:  Ez a jelentés egy adott alkalmazást használó egyedi felhasználók nézetét jeleníti meg. Csak azokat a felhasználókat tartalmazza, akik*sikeresen*bejelentkezett egy alkalmazásba.

**Eszközök bejelentkezései**: Ez a jelentés segít megérteni a szervezeten belül használt különböző eszközök profilokat, és meghatározza a használat alapján az eszközök szabályzatait. Adatokat biztosít az alkalmazásokhoz való bejelentkezéshez használt operációs rendszer és böngészők típusáról, valamint részletes információkat nyújt a felhasználókról, beleértve a következőket:

- Felhasználónév
- IP-cím
- Location 
- Bejelentkezési állapot 

**SSPR tölcsér**: Ez a jelentés segít megérteni, hogyan használják a SSPR eszközt a szervezeten belül. Megtekintheti, hogy hány jelszó-visszaállítást próbáltak meg a SSPR eszközön, és hogy hányan voltak sikeresek. Mélyebben is kihasználhatja a hibákat, és megtudhatja, miért történtek bizonyos hibák. 

## <a name="customize-azure-ad-activity-content-pack"></a>Az Azure AD-tevékenység tartalmi csomagjának testreszabása

**Vizualizáció módosítása**:  A jelentés-vizualizáció módosításához kattintson a **jelentés szerkesztése** lehetőségre, és válassza ki a kívánt vizualizációt.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/09.png) 
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/10.png) 

**További mezők**belefoglalása:  Hozzáadhat egy mezőt a jelentéshez, vagy eltávolíthatja azt úgy, hogy kiválasztja azt a vizualizációt, amelyhez hozzá kívánja adni/el szeretné távolítani a mezőt. Például hozzáadhatja a "bejelentkezési állapot" mezőt a táblázat nézethez az alább látható módon. 
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/11.png) 

**Vizualizációk rögzítése az irányítópulton**:  Az irányítópultot testreszabhatja úgy, hogy saját vizualizációkat is felhasznál a jelentéshez, és az irányítópultra rögzíti azt. 

![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/13.png) 
 
**Az irányítópult megosztása**: Az irányítópultot megoszthatja a szervezetében lévő felhasználókkal is. A jelentés megosztása után a felhasználók láthatják a jelentésben kiválasztott mezőket.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>A Power BI jelentés napi frissítésének ütemezett időpontja

A Power bi jelentés napi frissítésének megadásához nyissa meg > az adatkészletek**Beállítások** > **ütemezett frissítés** elemét, és állítsa be az alábbiak szerint.
 
![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Frissítés a Content Pack újabb verziójára

Ha a Content Pack csomagot egy újabb verzióra szeretné frissíteni:

- Töltse le az új csomagot, és állítsa be a jelen cikkben található utasítások alapján.

- Miután beállította, > lépjen az adatforrás-**Beállítások** > **adatforrás hitelesítő** adataihoz, és adja meg újra a hitelesítő adatait.

    ![Azure Active Directory Power BI-tartalomcsomag](./media/howto-power-bi-content-pack/16.png) 

Miután meggyőződött arról, hogy a Content Pack új verziója a várt módon működik, a régi verziót eltávolíthatja, ha törli az ahhoz tartozó jelentéseket és adatkészleteket.

## <a name="troubleshoot-content-pack-errors"></a>A Content Pack hibáinak elhárítása

A Content Pack használatakor lehetséges, hogy a következő hibákba ütközik: 

- [Sikertelen frissítés](#refresh-failed) 
- [Nem sikerült frissíteni az adatforrás hitelesítő adatait](#failed-to-update-data-source-credentials) 
- [Az adatimportálás túl sokáig tart](#data-import-is-too-slow) 

A Power BI-jal kapcsolatos általános útmutatásért tekintse meg ezeket a [súgótémaköröket](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Nem sikerült a frissítés. 
 
**A hiba felszínének módja**: A frissítési előzményekben Power BI vagy sikertelen állapotú e-mail-cím. 


| Ok | A javítás módja |
| ---   | ---        |
| A hibák frissítése akkor okozhat hibát, ha a Content Packhez csatlakozó felhasználók hitelesítő adatai alaphelyzetbe lettek állítva, de nem frissültek a Content Pack csatlakozási beállításaiban. | A Power BIban keresse meg az Azure AD-tevékenységek naplói irányítópultjának megfelelő adatkészletet (**Azure Active Directory tevékenység naplóit**), válassza az ütemezett frissítés lehetőséget, majd adja meg az Azure ad-beli hitelesítő adatait. |
| Az alapul szolgáló Content Pack adatproblémái miatt a frissítés sikertelen lehet. | [Támogatási jegy](../fundamentals/active-directory-troubleshooting-support-howto.md)küldése.|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Nem sikerült frissíteni az adatforrás hitelesítő adatait 
 
**A hiba felszínének módja**: Power BI az Azure AD-tevékenység naplóihoz való csatlakozáskor. 

| Ok | A javítás módja |
| ---   | ---        |
| A csatlakozó felhasználó nem globális rendszergazda vagy biztonsági olvasó vagy biztonsági rendszergazda. | Olyan fiókot használjon, amely globális rendszergazda vagy biztonsági olvasó vagy biztonsági rendszergazda a tartalmi csomagokhoz való hozzáféréshez. |
| A bérlő nem prémium szintű bérlő, vagy nem rendelkezik legalább egy, prémium szintű licenccel rendelkező felhasználóval. | [Támogatási jegy](../fundamentals/active-directory-troubleshooting-support-howto.md)küldése.|
 
### <a name="data-import-is-too-slow"></a>Az adatimportálás túl lassú 
 
**A hiba felszínének módja**: Power BI a Content Pack összekapcsolását követően az Adatimportálási folyamat megkezdi az irányítópult előkészítését az Azure AD-tevékenység naplóihoz. A következő üzenet jelenik meg: **Az adatimportálás..** . további előrehaladás nélkül.  

| Ok | A javítás módja |
| ---   | ---        |
| A bérlő méretétől függően ez a lépés néhány perctől akár 30 percig is eltarthat. | Ha az üzenet nem változik, hogy egy órán belül megjelenítse az irányítópultot, a [támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>További lépések

* [Telepítse Power bi Content Pack csomagot](quickstart-install-power-bi-content-pack.md).
* [Mi az Azure ad-jelentések?](overview-reports.md)
