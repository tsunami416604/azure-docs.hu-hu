---
title: "Az Azure Application Insightsban Analytics hibaelhárítása |} Microsoft Docs"
description: "Problémák az Application Insights analytics? Itt érdemes kezdenie. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: mbullwin
ms.openlocfilehash: 3ef9150cad62e9f9fc43f7afcbbbc01d89884a12
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Elemzés hibaelhárítása az Application Insights szolgáltatásban
Problémák [Application Insights Analytics](app-insights-analytics.md)? Itt érdemes kezdenie. Elemzés a hatékony keresési eszköz az Azure Application Insights.

## <a name="limits"></a>Korlátok
* Jelenleg lekérdezési eredmények korlátozódnak csak múltbeli adatok hetente keresztül.
* A teszteljük böngészők: Chrome, a peremhálózati és az Internet Explorer legújabb verziója.

## <a name="known-incompatible-browser-extensions"></a>Ismert kompatibilis böngészőbővítmények
* Ghostery

A bővítmény letiltása, vagy használjon egy másik böngészőben.

## <a name="e-a"></a>"Nem várt hiba"
![Váratlan hiba képernyő](./media/app-insights-analytics-troubleshooting/010.png)

Belső hiba történt a portál futásidőben – nem kezelt kivétel.

* A gyorsítótár törlése. 

## <a name="e-b"></a>403... Töltse be újra próbálja meg
![403... Töltse be újra próbálja meg](./media/app-insights-analytics-troubleshooting/020.png)

Hitelesítési hiba történt (a hitelesítési eljárás, illetve a hozzáférési jogkivonat létrehozása közben). Előfordulhat, hogy a portál nem tudja helyreállítani a böngésző beállításainak módosítása nélkül.

* Győződjön meg arról [külső cookie-k engedélyezettek](#cookies) a böngészőben. 

## <a name="authentication"></a>403... Ellenőrizze a biztonsági zóna
![403 ...verify biztonsági zóna](./media/app-insights-analytics-troubleshooting/030.png)

Hitelesítési hiba történt (a hitelesítési eljárás, illetve a hozzáférési jogkivonat létrehozása közben). Előfordulhat, hogy a portál nem tudja helyreállítani a böngésző beállításainak módosítása nélkül.

1. Győződjön meg arról [külső cookie-k engedélyezettek](#cookies) a böngészőben. 
2. Kedvenc, könyvjelző vagy mentett hivatkozás használatával próbált hozzáférni az Analytics portálhoz? Más hitelesítő adatokkal jelentkezett be, mint amikor mentette a hivatkozást?
3. Próbáljon privát/inkognitó módban lévő böngészőablakot használni (a többi hasonló ablak bezárása után). Meg kell adnia hitelesítő adatait. 
4. Nyisson meg egy másik (normál) böngészőablakot, és navigáljon a [Azure](https://portal.azure.com). Jelentkezzen ki. Ezután nyissa meg a hivatkozást, és jelentkezzen be a helyes hitelesítő adatokkal.
5. Az Edge és az Internet Explorer felhasználói akkor is láthatják ezt a hibát, ha a rendszer nem támogatja a megbízható zóna beállításait.
   
    Győződjön meg arról is [Analytics portál](https://analytics.applicationinsights.io) és [Azure Active Directory portálon](https://portal.azure.com) a ugyanazt biztonsági szintű zónában lévő:
   
   * Az Internet Explorerben nyissa meg a **Internetbeállítások**, **biztonsági**, **megbízható helyek**, **helyek**:
     
     ![Internetbeállítások párbeszédpanel, ha egy helyet a megbízható helyek](./media/app-insights-analytics-troubleshooting/033.png)
     
     Ha az alábbi URL-címek bármelyike szerepel a Webhelyek listában, győződjön meg a többi jelenlétéről is:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Nem található az erőforrás
![404... erőforrás nem található](./media/app-insights-analytics-troubleshooting/040.png)

Alkalmazás-erőforrás törölve lett az Application Insights, és már nem érhető el. Ez akkor fordulhat elő, ha az URL-cím mentette az Analytics oldalra.

## <a name="e-e"></a>403 ... Nincs engedély
![403... nem engedélyezett](./media/app-insights-analytics-troubleshooting/050.png)

Nincs engedélye az alkalmazás megnyitásához Analytics.

* Kapott a hivatkozás a valaki más? Győződjön meg arról, hogy a [olvasók vagy az erőforráscsoport közreműködők](app-insights-resources-roles-access-control.md).
* Menti a hivatkozás eltérő hitelesítő adatok használatával? Nyissa meg a [Azure-portálon](https://portal.azure.com), jelentkezzen ki, és próbálkozzon újra ide ebben az esetben adja meg a megfelelő hitelesítő adatokat.

## <a name="html-storage"></a>403 ... HTML5-tárolóra
A portál HTML5 localStorage és sessionStorage használja.

* Chrome: Beállítások, adatvédelmi, tartalombeállításait.
* Az Internet Explorer: Internetbeállítások, Speciális lap biztonsági, DOM tárolási engedélyezése

![403... Próbálja meg engedélyezni a HTML5-tárolóra](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Nem található előfizetés
![404 ... Nem található előfizetés](./media/app-insights-analytics-troubleshooting/070.png)

Az URL-cím érvénytelen. 

* Nyissa meg az alkalmazás erőforrás [Application Insights portál](https://portal.azure.com). Ezután használja az Analytics gombra.

## <a name="e-h"></a>404... a lap nem található
![404 ... A lap nem található.](./media/app-insights-analytics-troubleshooting/080.png)

Az URL-cím érvénytelen.

* Nyissa meg az alkalmazás erőforrás [Application Insights portál](https://portal.azure.com). Ezután használja az Analytics gombra.

## <a name="cookies"></a>Cookie-k engedélyezése
  Lásd: [külső cookie-k letiltása](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), de ellenőriznünk kell a hirdetmény **engedélyezése** őket.


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

