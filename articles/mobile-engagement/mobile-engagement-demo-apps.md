---
title: Az Azure Mobile Engagement bemutatóalkalmazást |} Microsoft Docs
description: Ismerteti a letöltési helyét, használata és az Azure Mobile Engagement bemutatóalkalmazást használatának előnyei
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: b74afcadc52973a4d824afddc0abff6ac9f71d17
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement bemutató alkalmazás
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Azt az Azure Mobile Engagement bemutató alkalmazás közzététele **iOS**, **Android**, és **Windows** segítséget nyújtanak hasznos forrásokat és tudjon meg többet a Mobile platformok Bevonási.

Az alkalmazás nyújt segítséget:

* Könnyedén megtalálhatja a Mobile Engagement erőforrásokhoz, mint a videók, dokumentáció, a támogatási fórum és hol kell kiadni a funkciókra vonatkozó kérések hasznos hivatkozásokat.
* Felhasználói élmény minta értesítések a Mobile Engagement ötleteket saját mobilalkalmazás által támogatott.
* A hivatkozás megvalósításának segítségével tanulmányozza a Mobile Engagement megvalósítható a saját alkalmazásba. Megismerheti, hogy:
  
  * Elemzés adatainak gyűjtéséről.
  * Speciális notification típusú például megvalósítása *teljes képernyős közbeszúrt* vagy *előugró*.
  * Felmérésekkel, szavazások valósítják meg.
  * Csendes leküldéses adatok és leküldéses forgatókönyvek megvalósításához.   

## <a name="app-installation"></a>Alkalmazás telepítése
Ez az alkalmazás a következő alkalmazás-áruházak érhető el:

* **Univerzális Windows-bemutatóalkalmazást**:
  
  * Töltse le az alkalmazást a [Windows alkalmazás-áruház](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * Az alkalmazás egy univerzális Windows 10-alkalmazásként jött létre. A forráskód nem érhető el a [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **iOS app bemutató**:
  
  * Töltse le az alkalmazást a [Apple store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * Az alkalmazás iOS Swift jött létre. A forráskód nem érhető el a [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Android bemutatóalkalmazást**:
  
  * Töltse le az alkalmazást a [Google Play áruház](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * A forráskód nem érhető el a [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Univerzális Windows-bemutatóalkalmazást][1]

![iOS bemutató alkalmazás][2]
![Android bemutatóalkalmazást][3]

## <a name="usage"></a>Használat
Ez az alkalmazás a következőképpen használhatja:

**Töltse le az alkalmazást az eszközön az alkalmazás tárolóban található hivatkozásokra (korábbi):**

> [!IMPORTANT]
> Nem kell egy Azure-fiók és nem szükséges az alkalmazás csatlakoztatása a háttérből. Az alkalmazás függetlenül működik.
> 
> 

* Miután az alkalmazást az eszközön, majd lépjen a Mobile Engagement kapcsolatos hasznos forrásokat a bal oldali menü-kapcsolaton keresztül.
* Fel lett véve a [szolgáltatás RSS-hírcsatorna](https://aka.ms/azmerssfeed) az alkalmazásba, hogy még mindig frissítése a legújabb frissítéseket kapcsolatban.
* Tapasztalhat az értesítések a Mobile Engagement által az egyes platformokon támogatott típusa mintaforgatókönyvek értesítési keresztül is végrehajthatja. Ezek az értesítések is kell észlelte a helyi--, amely esetében a képernyőkön mutatjuk be, az értesítések gyakorlat, amely megegyezik az értesítések küldése a Mobile Engagement platformról gombokra.

![A Windows App menü][4]

![Az iOS App menü][5]
![Android App menü][6]

**Töltse le a forráskód a Githubon található hivatkozásokra (korábbi):**

* A forráskód letöltése után nyissa meg a megfelelő fejlesztői környezetben – az iOS, Android és a Windowshoz készült Visual Studio Android Studio xcode-ban.
* Ezután kövesse a [SDK-integráció lépéseken](mobile-engagement-windows-store-dotnet-get-started.md) , hogy Ön az alkalmazás csatlakozni tudjanak a saját a Mobile Engagement háttér-példány.
  * Kell egy kapcsolati karakterlánc konfigurálása az alkalmazásban.
  * Azt is konfigurálnia kell a leküldéses értesítési platform az alkalmazásra vonatkozóan.
* Láthatja, hogy az alkalmazás maga a Mobile Engagement van-e tagolva. Ezért, azt a háttérben történő kapcsolódás után nyissa meg a alkalmazást, képes lesz a felhasználói munkamenet, tevékenységek, eseményeket és így tovább tekintheti meg a **figyelő** fülre.
* Akkor is képes lesz az értesítések küldését az alkalmazás a saját a Mobile Engagement-példányból, helyi értesítések használata helyett.
  
  * Itt is hozzáadhat az eszköz egy vizsgálati eszköz használatával a **az Eszközazonosító lekérése** menüpont az alkalmazásban. Ez lehetővé teszi egy eszköz azonosítója, amely majd regisztrál egy vizsgálati eszköz, a platform háttér-példányát.
    
    ![A Windows eszköz azonosítója][7]
    
    ![Eszközazonosító IOS][8]
    ![az Android eszköz azonosítója][9]

## <a name="key-features-of-the-demo-app"></a>A bemutató alkalmazás a legfontosabb jellemzők
* Amint már említettük, az alkalmazással, vannak a legfontosabb erőforrásokat a Mobile Engagement az aktuális. A bal oldali menü Lépkedjen végig a hivatkozásokat.
* Alkalmazáson belüli out értesítések az egyes platformokon fedezheti. Ezek az értesítések kézbesítése **csak értesítés**, ahol az értesítés kattintva egyszerűen megnyitja az alkalmazás natív képernyőt (használatával **mély linking**) – vagy a regisztrációja, mivel egy **webes bejelentés**, amennyiben a Mobile Engagement háttérből az értesítés történő kattintáskor megjelenítendő biztosíthat további HTML-tartalmakat.
  
    ![Alkalmazáson belüli out értesítések][29]
* IOS rendszerű eszközökön akkor zárja be az alkalmazás az alkalmazáson belüli out vagy a rendszer leküldéses értesítést. Megnézheti a megvalósítás itt hozzáadásának **akciógombok**, az alkalmazáson belüli out értesítésre adott megfelelően, például *visszajelzés* és *megosztás* (úgy, hogy a felhasználói is igénybe vehet magát az értesítést a művelet jobbra).
  
    ![Alkalmazáson belüli out értesítések iOS][11] ![IOS alkalmazáson belüli out értesítés megjelenítése][14]
* Az Android, a támogatott beállítások adunk többsoros (**nagy szöveg**) vagy egy értesítés (**nagy vonalakban tekinti**) az értesítésre, valamint a **akciógombok**(mivel az iOS által támogatott).
  
    ![Alkalmazáson belüli out értesítések Android rendszeren][12] ![Az Android alkalmazáson belüli out értesítés megjelenítése][15]
* A Windows 10 láthatja, hogyan keresse meg az értesítéseket a számítógépen. Ezt az értesítést is megjelennek a Windows 10 **értesítési központba**. Nem támogatott hozzáadása **akciógombok** jelenleg a Windows SDK-ban.
  
    ![Alkalmazáson belüli out értesítések Windows rendszeren][10] ![Alkalmazáson belüli out megjelenítési Windows rendszeren][13]
* Az egyes platformokon alapértelmezett "alkalmazásbeli" értesítések fedezheti. Ez az egy kétlépéses élmény ahol egy **értesítési** ablak akkor jelenik meg először. Ha a hivatkozásra tárja fel a teljes képernyős **közlemény**, ahogy az alábbi képernyőképen látható. A közlemény tartalmát a Mobile Engagement háttér-példány származik. Az SDK-val rendelkezik értesítések és a közlemények a sablonokat. Könnyen teste szabhatja őket, ez az embléma és színezés hozzáadását bemutató alkalmazás látható módon.  
  
    ![Az alkalmazásbeli értesítésekben Windows rendszeren][16]
  
    ![Alkalmazáson belüli értesítések iOS][17]  ![Az alkalmazásbeli értesítésekben Android rendszeren][18]
  
    **iOS**, **Android**
* Használhatja a **kategória** a Mobile Engagement az alapértelmezett testreszabásához szolgáltatása. A bemutató alkalmazás azt korábban bemutatott módosítása értesítést élmény két gyakori módjai. Vegye figyelembe, hogy a kategória szolgáltatása még nem támogatott a Windows SDK-t.
  
    **Teljes képernyős közbeszúrt:**
  
    ![Az alkalmazáson belüli értesítés - közbeszúrt kategória][30]
  
    ![IOS közbeszúrt kategória][21]     ![Az Android közbeszúrt kategória][22]
  
    **Előugró értesítések:**
  
    ![Az alkalmazáson belüli értesítés - előugró kategória][31]
  
    ![IOS előugró értesítések][19]    ![Az Android előugró értesítések][20]

**iOS**, **Android**

* A Mobile Engagement is támogat egy speciális típusa az alkalmazáson belüli értesítés nevű **szavazások**. Ez lehetővé teszi, hogy a szegmentált felhasználók gyors felmérést küldhet. Kérdések és minden kérdés, ahogy az alábbi képernyőfelvételen a beállítások is hozzáadhat. Ez fogja majd beolvasása jelenik meg az alkalmazás felhasználó számára az alkalmazáson belüli értesítések.   
  
    ![Lekérdezési értesítések][32]
  
    ![Windows felmérése][26]
  
    ![IOS-felmérés][27]   ![Android felmérése][28]

**iOS**, **Android**

* A Mobile Engagement is támogatja csendes **adatok leküldéses** értesítések. Ezek az értesítések úgy küldhet adatokat a szolgáltatásból (például a JSON-adatokat az alábbi példában), amely az alkalmazás kezelni, és bizonyos művelet végrehajtása. Példa: hogyan azt épp módosított cikk árának szelektív adatok leküldéses értesítések segítségével.
  
    ![Adatok leküldéses értesítés][33]
  
    ![Adatok leküldéses értesítés az Windows][23]
  
    ![Adatok leküldéses értesítések IOS rendszerű eszközökön][24]  ![Adatok leküldéses értesítések Android rendszeren][25]

**iOS**, **Android**

> [!NOTE]
> Ezek az értesítések további részletes útmutatásért kattintva megtekintheti **kattintson ide az értesítések küldése a Mobile Engagement platform útmutatást** bármely minta értesítési képernyőn.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
