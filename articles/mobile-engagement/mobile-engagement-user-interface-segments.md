---
title: Az Azure Mobile Engagement felhasználói felület - szegmensek
description: 'Útmutató: az Azure Mobile Engagement segítségével használati minták azonosításához felhasználók szegmenseinek létrehozása és kezelése'
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6a4f2205-4a3c-406e-a04f-5e6f2a36653f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 53c5b1b672a0d7212a3d298c3e411a9cc6ac0807
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-create-and-manage-segments-of-users-to-identify-usage-patterns"></a>Hogyan kell a használati minták azonosításához felhasználók szegmenseinek létrehozása és kezelése
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a cikk ismerteti a **SZEGMENSEK** lapján a **a Mobile Engagement** portálon. Használja a **a Mobile Engagement** portal felügyeletét és kezelését a mobile apps szolgáltatásban.

A felhasználói felület szegmensek szakasza lehetővé teszi a felhasználók különböző viselkedését és érheti el az alkalmazást, és a szegmensek API-n keresztül is elérheti analytics alapján szegmentálja a. Szegmensek először vannak számított 24 órával azt követően hozza létre őket, és azok vannak recomputed 24 óránként legújabb analytics információk alapján. Miután egy szegmens kiszámítása, azt jeleníti meg a "Day nap előzményei" naponta.

> [!NOTE]
> Sok szakasza a **a Mobile Engagement** portál felhasználói felületének tartalmaz a **megjelenítése SÚGÓ** gombra. Nyomja le az erre a gombra kattintva szakasz környezetfüggő ismertetése.
> 
> 

## <a name="create-segments"></a>Szegmensek létrehozása
Létrehozhat egy szegmens legfeljebb 10 feltétel alapján a meghatározott időtartamra fel az elmúlt 60 napra analytics szakaszából. Például egy szegmens a személyeket, akik bizonyos oldalait vagy az utolsó 10 napon belül az adott tartalomhoz az alkalmazáson belül keres alapján is létrehozhat. Ez az információ analytics szakaszában érhető el. Igen segítségével azt hoznia egy szegmenst, és majd állítsa be úgy, hogy a felhasználók részhalmazát célozza leküldéses értesítés azokat a térjen vissza az alkalmazást. 

> [!NOTE]
> Miután egy szegmens kiszámított, ezért nem lehet szerkeszteni; azt is csak klónozáshoz (másolt) vagy megsemmisül (törölve). A szegmens klónozható a (ugyanazon alkalmazásazonosítóval rendelkező azonos AppID) belül, és azt is klónozható más alkalmazásokba (a különböző AppID). 

 ![segments1][35] 

## <a name="examples-segments"></a>Példák szegmensek
 ![segments2][36]

Szegmensek lehetővé teszi a szegmenseket, a végfelhasználók számára az alkalmazás.
A felhasználók szegmentálásával egy fontos marketing stratégia. Az Azure Mobile Engagement lehetővé teszi a régebbi adatok, és hozzon létre egyéni szegmensek. Az erőteljes eszköz lehetővé teszi az alkalmazás a felhasználók tapasztalatai megismerése. Egyszerűen elemezheti a szegmenseket, és használja a szegmenseket leküldéses célként.
Egy gyakori használati eset az, hogy szeretne-e leküldéses bátorítva használatával minősítheti az alkalmazás az áruházban a végfelhasználók értesítést küldeni. Ahelyett, hogy a végfelhasználók értesítést küld, az alkalmazás minden nap használtak az elmúlt hónapban, és rendelkezésére állt-e a kiváló felhasználói felület csak felhasználók kellene megadnia szegmenssel hozhat létre. Kevesebb, jól célzott leküldéses értesítéseket küldeni, a jobb Megtérülési nyílik meg.

 ![segments3][37]

### <a name="segments-you-can-create-based-on-the-major-azure-mobile-engagement-elements"></a>Szegmensek hozhat létre a fő Azure Mobile Engagement elemet alapján:
* Esemény: hozzon létre egy szegmenst, hogy célokat egy adott esemény az alkalmazás, amely hetente legfeljebb kétszer került sor. 
* Munkamenet: hozzon létre egy részének a az alkalmazás több mint 5 alkalommal múlt héten használó felhasználókat.
* Tevékenység: hozzon létre egy részének a használó felhasználókat egy oldal, vagy a tartalom több vagy kevesebb mint 10 ideje elmúlt hónapban.
* Feladat: a szegmens, amely naponta legfeljebb kétszer befejeződött egy feladat létrehozása.
* Összeomlási: hozzon létre egy szegmens az összes olyan felhasználó, amely 10 szintnél mélyebben múlt héten crash volt. (Ebben a szegmensben egy Bocsánatkérő vagy akár egy ráta sikerült leküldéses!)
* Hiba: a szegmens az összes olyan felhasználó, amelyek több mint 100 alkalommal 3 napnál korábban hibába hozható létre.
* Alkalmazásadat: hozzon létre egy szegmenst, amelynek célja egy egyéni App-Info, amelyek az elmúlt 25 napban történtek.
  
  ![segments4][38]

Szegmens optimálisan használatához még kell meg egy testreszabott integrációt az SDK az alkalmazás "Alkalmazásadatok" címkék címkézési tervvel.
Ezt követően folytassa a felület kezdőlapján, válassza ki az alkalmazást, majd kattintson a "Szegmensek" szakasz.

1. Válassza ki a "Szegmensek" szakaszt.
2. Kattintson az "új szegmens" gombra kattintva hozzon létre egy új szegmenst.

## <a name="real-life-example-create-a-simple-segment-based-on-session-information"></a>Valós élettartama. példa: Hozzon létre egy egyszerű szegmens "Munkamenet" adatok alapján
Hozzon létre egy szegmens az összes a végfelhasználók számára használták az alkalmazást legalább 50 alkalommal az elmúlt hét. Ott csak a végfelhasználók számára, amelyekkel töltötték legalább 30 másodperces munkamenetenként az alkalmazásban található. Ez az alkalmazás egy pozitív élményt rendelkező összes végfelhasználók jelennek meg. Ezt követően létre szegmens is leküldéses értesítést a végfelhasználóknak, amiben megkéri, hogy értékeljék az alkalmazást a tárolóban.

 ![segments5][39]

1. Nevezze el a szegmensek ahhoz, hogy gyorsan megtalálhatja a "Szegmens" listában.
2. Kattintson a "Létrehozás" gombra.
   
   ![segments6][40]

Jelölje ki a munkamenetet.

 ![segments7][41]

1. Válassza ki az "Elmúlt hét" időszak.
2. Kattintson a Tovább gombra.
   
   ![segments8][42]
3. Válassza ki azt az operátort, fontos listájában: =; ≥, ≤.
4. Adja meg a kívánt szám.
5. Válassza ki a kívánt előfordulása. 
6. Kattintson a Tovább gombra.
   Ebben a példában van beállítva, így a múlt héten keresztül megfelelő felhasználók, amelyek legalább 50 munkamenetek történtek.
   
   ![segments9][43]

A munkamenet Szegmentálás esetén dönthet úgy, a munkamenet feltételként hossza.

1. Válassza ki a listából az operátor.
2. Adja meg a munkamenet hossza.
3. Kattintson a Next (Tovább) gombra.
   Ebben a példában a felirat látható, hogy a munkamenet, amely rendelkezik a előfordulási szakasz lett szegmentált, jelölje be a felhasználókra, akik rendelkeznek a munkamenetenként több mint 30 másodperc.
   
   ![segments10][44]

A feltétel neve a teljes tölcsér a webkiszolgálótól, és kattintson a Befejezés gombra.

 ![segments11][45]

Miután végzett a beállítás mentése a feltételnek, a szegmens tölcsérhez jelenik meg.
A szegmens analytics adatokon alapul, mert a szegmensek naponta egyszer kiszámítása a történik.
Ebben a példában 47,7 a teljes végfelhasználók % megfelel a feltételnek. A felhasználók rendelkezésére állt-e a megfelelő környezet és várhatóan magasabb minősítést adja meg, ha a értékelje az áruházban az alkalmazás kéri a felhasználót értesítést leküldéses őket kell őket.

## <a name="see-also"></a>Lásd még
* [Alapfogalmak][Link 6]
* [Hibaelhárítási útmutató szolgáltatás][Link 24]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

