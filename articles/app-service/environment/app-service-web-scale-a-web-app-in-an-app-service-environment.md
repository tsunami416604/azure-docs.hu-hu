---
title: Alkalmazások méretezése App Service Environment-környezetben – Azure
description: Alkalmazások méretezése App Service Environment-környezetben
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
ms.custom: seodec18
ms.openlocfilehash: c94e7f761dcbc7e05965e359d7f9a864335c9c6b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269345"
---
# <a name="scaling-apps-in-an-app-service-environment"></a>Alkalmazások méretezése App Service-környezetben
Az Azure App Service-ben nincsenek megfelelően skálázhatja három dolgot:

* Díjszabási csomag
* feldolgozó mérete 
* példányok száma.

Az ASE környezetben van, nem szükséges, válassza ki, vagy módosítsa a tarifacsomagot.  Képességek tekintetében már meghatározhat képesség szint díjszabása.  

Megállapodást feldolgozókkal az ASE-rendszergazda rendelhet hozzá minden feldolgozói készlethez használható a számítási erőforrás méretét.  Ez azt jelenti, feldolgozói készlethez 1 rendelkező P4 szintű számítási erőforrások és a feldolgozói készlethez 2 P1 a számítási erőforrásokat, ha szükséges.  Nem rendelkeznek mérete sorrendben kell.  A méretek és a díjszabási részleteket itt a dokumentum [Azure App Service díjszabását][AppServicePricing].  Ennek következtében a méretezési lehetőségeket biztosít a web apps és az App Service-csomagban kell App Service Environment-környezetben:

* feldolgozó készlet kiválasztása
* példányok száma

Módosítása vagy elem látható, az ASE az App Service-csomagokban üzemeltetett megfelelő felhasználói felületen keresztül történik.  

![][1]

A feldolgozó készlet, amely az ASP elérhető számítási erőforrások száma meghaladja az ASP nem vertikális.  Ha a szükséges számítási erőforrásokért, hogy feldolgozókészletben kell az ASE-rendszergazda adja hozzá őket beolvasása.  Az ASE információk újrakonfigurálása olvassa el az adatok itt: [App Service environment konfigurálása][HowtoConfigureASE].  Érdemes kapacitás alapján ütemezés vagy metrikák hozzáadása az ASE automatikus skálázási funkciókat is.  Konfigurálásáról további információért lásd az ASE környezetben magát az automatikus skálázási [konfigurálása az automatikus méretezés App Service-környezet][ASEAutoscale].

Több app service-csomagok különböző feldolgozókészletek a számítási erőforrások használatával is létrehozhat, vagy használhatja ugyanazt a feldolgozó készletet.  Például ha rendelkezik (10) elérhető számítási erőforrások feldolgozói készlethez 1, ha szeretné, hozzon létre egy app service-csomag (6) a számítási erőforrások használatával, és egy másik app service-csomag (4) használó számítási erőforrásokat.

### <a name="scaling-the-number-of-instances"></a>Példányok számának méretezése
Ha a webes alkalmazás létrehozása az App Service Environment 1 példány kezdődik.  Majd horizontálisan, további példányokat, adja meg az alkalmazás további számítási erőforrásokat.   

Ha az ASE elegendő a kapacitása akkor viszonylag egyszerű.  Nyissa meg az App Service-csomag vertikális felskálázás, és válassza ki a méretet a helyek társításához.  Ekkor megnyílik a felhasználói felület, ahol manuálisan beállítására szolgál az ASP és a ASP automatikus skálázási szabályok konfigurálása.  Alkalmazás manuális skálázása egyszerűen állítsa ***a skálázás*** való ***manuálisan megadott példányszám***.  Innen húzza a csúszkát a megfelelő mennyiségre, vagy adja meg azt a csúszka melletti mezőbe.  

![][2] 

Az automatikus méretezési szabályok egy ASP, az ASE környezetben működni, mint általában.  Kiválaszthatja ***processzorhasználat*** alatt ***a skálázás*** és automatikus méretezési szabályokat létrehozni a Processzorhasználat (%), vagy alapján az ASP használatával összetett szabályokat hozhat létre ***ütemezési és teljesítményszabályok*** .  További részleteket konfigurálásáról az automatikus méretezés használja az útmutató Itt [Skálázhatja őket egy Azure App Service-ben][AppScale]. 

### <a name="worker-pool-selection"></a>Feldolgozó készlet kiválasztása
Korábban feljegyzett a feldolgozói készlethez kijelölt az ASP felhasználói felületen érhető el.  Az ASP, amelyet szeretne méretezni, és válassza ki a feldolgozókészlet panel megnyitásához.  Megjelenik a feldolgozókészletek, amelyek az App Service-környezet be vannak állítva.  Ha csak egy munkavégző készletét ezután csak látni fogja a felsorolt egy készletet.  Milyen szerepel-e az ASP feldolgozókészlet módosításához, egyszerűen válassza ki a feldolgozókészletet szeretne az App Service-csomagra szeretne váltani.  

![][3]

Az ASP áthelyezése egy feldolgozó készletből a másikba előtt fontos, hogy az ASP megfelelő kapacitást kell.  Feldolgozókészletek listájában nem csak a worker-készlet neve szerepel, de hány feldolgozók érhetők el, hogy feldolgozókészlet is látható.  Győződjön meg arról, hogy nincsenek-e elegendő példányig tartalmazzák az App Service-csomagra.  Ha több szükséges számítási erőforrásokért az áthelyezni kívánt feldolgozókészletben, kérje le az ASE-rendszergazda adja hozzá őket.  

> [!NOTE]
> Az, hogy az ASP-alkalmazások áthelyezése egy feldolgozó készletből egy ASP következtében offline elindul.  Emiatt lassan futnak, mivel az alkalmazás offline indult el az új számítási erőforrásoknak a kérelmeket.  A hidegindítás használatával elkerülhető az [alkalmazás mentése funkció meleg] [ AppWarmup] Azure App Service-ben.  Az alkalmazásinicializálás modul, a cikkben ismertetett kiküszöbölik az is működik, mert az inicializálási folyamatot is akkor aktiválódnak, ha az alkalmazások olyan ritkán használt új számítási erőforrásoknak a következőn:. 
> 
> 

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [hogyan, hozzon létre egy App Service-környezet][HowtoCreateASE]

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
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../web-sites-scale.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
