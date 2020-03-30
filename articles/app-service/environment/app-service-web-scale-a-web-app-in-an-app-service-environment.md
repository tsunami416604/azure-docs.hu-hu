---
title: Alkalmazás méretezése az ASE 1-es v1-ben
description: Alkalmazás méretezése egy App Service-környezetben. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 43849ca7084f2237c37ad537c50f4e94ac4ea7c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688683"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Alkalmazások méretezése az App Service-környezet ben 1- es v1
Az Azure App Service-ben általában három dolgot méretezhet:

* árképzési terv
* dolgozó mérete 
* az esetek száma.

Az ASE-ben nincs szükség az árképzési terv kiválasztására vagy módosítására.  Ami a képességeket illeti, már prémium díjszabási képesség szinten van.  

A dolgozó méretetekintetében az ASE-rendszergazda hozzárendelheti az egyes feldolgozókészletekhez használandó számítási erőforrás méretét.  Ez azt jelenti, hogy a Munkavégző készlet 1 P4 számítási erőforrásokkal és a 2-es feldolgozókészlet P1 számítási erőforrásokkal rendelkezhet, ha szükséges.  Nem kell méretsorrendben lenniük.  A méretek és díjszabásuk részleteit itt találja az [Azure App Service díjszabása][AppServicePricing]című dokumentumban.  Így a webalkalmazások és az App Service-csomagok méretezési lehetőségei egy App Service-környezetben a következők:

* dolgozókészlet kiválasztása
* példányok száma

Mindkét elem módosítása az ASE által üzemeltetett App Service-csomagok számára megjelenített megfelelő felhasználói felületen keresztül történik.  

![][1]

Az ASP-t nem skálázhatja a munkavégző készletben rendelkezésre álló számítási erőforrások számán túl, amelyben az ASP található.  Ha számítási erőforrásokat, hogy a feldolgozókészletben szüksége van az ASE-rendszergazda hozzáadása.  Az ASE újrakonfigurálásával kapcsolatos információkért olvassa el az itt található információkat: [Az App Service-környezet konfigurálása.][HowtoConfigureASE]  Azt is érdemes kihasználni az ASE automatikus skálázási funkciók kapacitás hozzáadása ütemezés vagy metrikák alapján.  Az automatikus skálázás konfigurálásáról az ASE-környezetben című témakörben talál további [részleteket: Automatikus skálázás konfigurálása egy App Service-környezethez.][ASEAutoscale]

Több alkalmazásszolgáltatási tervet is létrehozhat különböző munkavégző készletek számítási erőforrásaival, vagy használhatja ugyanazt a munkavégző készletet.  Ha például (10) rendelkezésre áll számítási erőforrások at worker pool 1, választhat, hogy hozzon létre egy alkalmazás szolgáltatási csomag segítségével (6) számítási erőforrások, és egy második app service-csomag, amely (4) számítási erőforrásokat használ.

### <a name="scaling-the-number-of-instances"></a>A példányok számának méretezése
Amikor először hozza létre a webalkalmazást egy App Service-környezetben, az 1 példánysal kezdődik.  Ezután további példányokra skálázhatja, hogy további számítási erőforrásokat biztosítson az alkalmazáshoz.   

Ha az ASE elég kapacitással rendelkezik, akkor ez nagyon egyszerű.  Nyissa meg az App Service-csomagot, amely tartalmazza a felskálázni kívánt webhelyeket, és válassza a Méretezés lehetőséget.  Ez megnyitja azt a felhasználói felületet, ahol manuálisan beállíthatja az ASP-hez a skálázást, vagy konfigurálhatja az Automatikus skálázási szabályokat az ASP-hez.  Az alkalmazás manuális méretezéséhez egyszerűen állítsa be a ***Méretezést*** ***egy példányszámra, amelyet manuálisan adok meg***.  Innen húzza a csúszkát a kívánt mennyiségre, vagy írja be a csúszka melletti mezőbe.  

![][2] 

Az ASE-ben lévő ASP automatikus skálázási szabályai ugyanúgy működnek, mint általában.  A Méretezés szerint ***csoportban kiválaszthatja a CPU százalékos*** ***arányát,*** és automatikus skálázási szabályokat hozhat létre az ASP-hez a CPU százaléka alapján, vagy összetettebb szabályokat is létrehozhat ***ütemezési és teljesítményszabályok***használatával.  Az automatikus skálázás konfigurálásának további részleteiért tekintse meg az itt található alkalmazás méretezése az [Azure App Service-ben című útmutatót.][AppScale] 

### <a name="worker-pool-selection"></a>Dolgozókészlet kiválasztása
Ahogy korábban említettük, a dolgozói készlet kiválasztása az ASP felhasználói felületéről érhető el.  Nyissa meg a méretezni kívánt ASP paneljét, és válassza ki a munkavégző készletet.  Az App Service-környezetben konfigurált összes munkavégző készlet jelenik meg.  Ha csak egy feldolgozókészlete van, akkor csak a felsorolt készlet jelenik meg.  Ha módosítani szeretné, hogy az ASP milyen munkavégző készletben van, egyszerűen válassza ki azt a munkavégző készletet, amelybe az App Service-csomagát át szeretné helyezni.  

![][3]

Mielőtt áthelyezné az ASP-t az egyik feldolgozókészletből a másikba, fontos, hogy győződjön meg arról, hogy megfelelő kapacitással rendelkezik az ASP-hez.  A munkavégző készletek listájában nem csak a munkavégzőkészlet neve szerepel, hanem azt is láthatja, hogy hány dolgozó érhető el az adott munkavégző készletben.  Győződjön meg arról, hogy elegendő példány áll rendelkezésre az App Service-csomag tartalmazásához.  Ha több számítási erőforrásokra van szüksége a munkavégző készletben, amelybe át szeretne helyezni, majd az ASE-rendszergazdát vegye fel, hogy adja hozzá őket.  

> [!NOTE]
> Ha egy ASP-t egyik feldolgozókészletből helyez át, az adott ASP-ben lévő alkalmazások hideg indítást okoznak.  Ez azt eredményezheti, hogy a kérelmek lassan futnak, mivel az alkalmazás hidegen van az új számítási erőforrásokon.  A hidegindítás elkerülhető az [azure App Service-ben az alkalmazás bemelegedési képességének][AppWarmup] használatával.  A cikkben ismertetett alkalmazásinicializálási modul is működik a hideg indítások, mert az inicializálási folyamat is meghívja, ha az alkalmazások hidegindítással az új számítási erőforrások. 
> 
> 

## <a name="getting-started"></a>Első lépések
Az App Service-környezetek ismerkedése az [App Service-környezet létrehozása][HowtoCreateASE] című témakörben

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
