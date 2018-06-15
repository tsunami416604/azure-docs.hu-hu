---
title: Egy alkalmazás az App Service-környezet méretezése
description: Egy alkalmazás az App Service-környezetek skálázás
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: jimbe
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.openlocfilehash: d04a5fce920dae25507cdf2f64832574e24c51dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836552"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Alkalmazások méretezése App Service-környezetben
Az Azure App Service általában három dolog méretezheti:

* terv díjszabása
* munkavégző mérete 
* példányok száma.

-Környezetben nincs szükség válassza ki, vagy a tarifacsomag módosítása.  Képességek szempontjából ez ugyanis már azonos a prémium tarifacsomag a képesség szintjét.  

Munkavégző méretek tekintetében a ASE admin rendelhet hozzá a számítási erőforrással minden feldolgozókészletek használandó méretét.  Amely: munkavégző készlet 1 P4 számítási erőforrással rendelkezhet, és munkavégző Pool 2 P1 a számítási erőforrásokat, ha szükséges.  Nem rendelkeznek mérete ahhoz, hogy.  További információ a méretek és tarifacsomagját: a dokumentum itt [Azure App Service szolgáltatás díjszabása][AppServicePricing].  Az App Service-környezetek kell lennie a mindeddig webes alkalmazások és az App Service-csomagok méretezési lehetőségek:

* munkavégző készlet kiválasztása
* példányok száma

Módosítása vagy a cikk a megfelelő felhasználói felület esetében a ASE üzemeltetett App Service-csomagok látható segítségével történik.  

![][1]

A worker-készlet, amely az ASP elérhető számítási erőforrások száma meghaladja az ASP nem növelheti.  Ha a munkavégző készlethez tartozó erőforrások szükséges számítási kell beolvasni a ASE rendszergazdájához, és adja hozzá.  Az információk körül újrakonfigurálása a ASE itt az információk elolvasásához: [az App Service-környezetek konfigurálása][HowtoConfigureASE].  Érdemes lehet is, a ASE automatikus skálázási funkciót hozzáadása ütemezés vagy mérőszámok alapján kapacitás kihasználását.  Konfigurálásával kapcsolatos további információkért lásd maga ASE környezetre automatikus skálázási [automatikus skálázás konfigurálása az App Service-környezetek][ASEAutoscale].

Létrehozhat több app service-csomagokról különböző feldolgozókészletek a számítási erőforrásokat használ, vagy használhatja a munkavégző készlethez.  Ha például a Worker-készlet 1 (10) elérhető számítási erőforrások rendelkezik, esetén dönthet úgy, hogy hozzon létre egy app service-csomag (6) számítási erőforrásokat használ, és egy második app service-csomag (4) használó számítási erőforrásokat.

### <a name="scaling-the-number-of-instances"></a>A példányok száma skálázás
Ha a webalkalmazás létrehozása az App Service-környezetek 1 példány kezdődik.  Majd méretezheti ki további számítási erőforrásokat biztosít az alkalmazás további példányait.   

Ha a ASE rendelkezik elegendő kapacitással a akkor közérthető egyszerű.  Ugrás az az App Service-csomag, amely tárolja a helyek növelheti, és válassza ki a skála.  Ekkor megnyílik a felhasználói felület, ahol manuálisan beosztásának beállítására szolgál az ASP és a ASP automatikus skálázási szabályok konfigurálása.  Manuálisan méretezhető, az alkalmazás egyszerűen állítsa ***szerint*** való ***manuálisan megadott példányszám***.  Itt húzza a csúszkát a kívánt mennyiség, vagy adja meg azt a csúszka melletti jelölőnégyzetbe.  

![][2] 

Az automatikus skálázási szabályok egy ASP-környezetben működnek azonos, általában.  Kiválaszthatja ***processzor*** alatt ***szerint*** és automatikus skálázás szabályt hoz létre, a processzor, vagy alapján az ASP használatával összetettebb szabályokat hozhat létre ***ütemezés és a teljesítmény szabályok*** .  További részleteket konfigurálása az automatikus skálázási használja az útmutatóban Itt [alkalmazás skálázása az Azure App Service][AppScale]. 

### <a name="worker-pool-selection"></a>Munkavégző készlet kiválasztása
Ahogy azt korábban említettük, a munkavégző készlet kiválasztása az ASP felhasználói felületi elérése.  A panel megnyitásához, amely méretezhető, és válassza ki a feldolgozókészleten ASP.  Megjelenik a feldolgozókészletek, amely az App Service-környezet be vannak állítva.  Ha csak egy feldolgozókészletek majd csak akkor jelenik meg a felsorolt egy készletet.  Milyen munkavégző készletét. a rendszer az ASP, akkor egyszerűen jelölje ki a feldolgozókészleten szeretne az App Service-csomag áthelyezése.  

![][3]

Mielőtt az ASP egy munkavégző készletből egy másik fontos győződjön meg arról, hogy az ASP megfelelő kapacitása.  A feldolgozókészletek közül nem csak a munkavégző alkalmazáskészlet neve szerepel, de azt is láthatja, hogy hány munkavállalók érhetők el, hogy a munkavégző készletét.  Győződjön meg arról, hogy vannak-e elegendő elérhető tartalmazzák az App Service-csomag példányok.  Ha áthelyezi a feldolgozókészleten erőforrásokat kell több számítja ki, majd kérje le a ASE rendszergazdájához, és adja hozzá.  

> [!NOTE]
> Az, hogy az ASP-alkalmazások áthelyezése egy munkavégző készletből egy ASP hatására cold kezdődik.  Ennek hatására kérelmek lassabban működik, mert az alkalmazás cold az új számítási erőforrásokat a következőn:.  A hidegindítás segítségével elkerülhetők a [meleg funkció mentése alkalmazás] [ AppWarmup] az Azure App Service-ben.  Az alkalmazásinicializálás modul, a cikkben ismertetett is működik hidegindítások, mert az inicializálási folyamatot is nyílik meg, ha azok cold kezdete: új számítási erőforrásokat. 
> 
> 

## <a name="getting-started"></a>Bevezetés
App Service Environment-környezetek megkezdéséhez, lásd: [hogyan számára hozzon létre egy App Service-környezet][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../web-sites-scale.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
