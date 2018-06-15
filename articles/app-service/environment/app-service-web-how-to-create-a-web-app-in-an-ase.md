---
title: Hozzon létre egy webalkalmazást az App Service Environment-környezet v1
description: Ismerje meg, hogyan hozhat létre webes alkalmazások és az app service-csomagokról a egy App Service Environment-környezet v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 1e8540409c6174ad02bd2d9d57c53e0279f49871
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386911"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Hozzon létre egy webalkalmazást az App Service Environment-környezet v1

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 tárgya.  Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan web Apps alkalmazások létrehozásához, és az App Service-csomagok egy [App Service Environment-környezet v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Ha szeretné, hogy annak megismerése, hogyan hozhat létre egy webalkalmazást, de nem kell azt egy App Service Environment-környezetben, olvassa el [.NET-webalkalmazás létrehozása](../app-service-web-get-started-dotnet.md) vagy más nyelv és keretrendszer a kapcsolódó oktatóprogramok valamelyikét.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy létrehozott egy App Service Environment-környezet. Ha, amely még nem végzett, lásd: [egy App Service Environment-környezet létrehozása](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása
1. Az a [Azure Portal](https://portal.azure.com/), kattintson a **hozzon létre egy erőforrást > Web + mobil > webalkalmazás**. 
   
    ![][1]
2. Válassza ki előfizetését.  
   
    Ha több előfizetéssel rendelkezik vegye figyelembe, hogy az App Service-környezet egy alkalmazás létrehozásához kell használnia, amely a környezet létrehozásakor használt ugyanahhoz az előfizetéshez. 
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
   
    *Erőforráscsoportok* lehetővé teszik a kapcsolódó Azure-erőforrások kezeléséhez egységet, és akkor hasznos, ha létrehozó *szerepköralapú hozzáférés-vezérlés* (RBAC) szabályok az alkalmazásokhoz. További információkért lásd: [Azure Resource Manager áttekintése][ResourceGroups]. 
4. Válassza ki vagy hozzon létre egy App Service-csomagot.
   
    *App Service-csomagok* kezelt készlet azokból a webalkalmazásokból.  Normális esetben ha árképzés lehetőséget választja, az ár alkalmazza az App Service-csomagra, nem pedig az egyes alkalmazásokra. -Környezetben kell fizetnie a számítási példányokért az ASE rendelt ahelyett, hogy az ASP van felsorolva.  Méretezést kívánó egy webalkalmazást, akkor növelheti az App Service példányai példányainak száma terv és az összes hatással van a webalkalmazások, hogy a tervben.  Néhány funkció, például hely tárhelyek vagy virtuális integráció belül a csomag mennyiség korlátozások is.  További információkért lásd: [Azure App Service-csomagok áttekintése](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Az App Service-csomagokról alapján a terv neve alatt kell jegyezni helyen azonosíthatja a ASE a.  
   
    ![][5]
   
    Ha szeretné használni, amely már szerepel az App Service-környezet App Service-csomagot, jelölje ki azt. Ha szeretne létrehozni egy új App Service-csomag, a következő részben az ebben az oktatóanyagban [az App Service-csomag létrehozása az App Service-környezetek](#createplan).
5. Adja meg a webalkalmazás nevét, és kattintson **létrehozása**. 
   
    Ha a ASE használ egy külső VIP van-e egy app Service-környezetben URL-címe: [*sitename*]. [ *az App Service-környezet nevét*]. ahelyett, hogy p.azurewebsites.net [*sitename*]. azurewebsites.net
   
    Ha a ASE egy belső VIP és egy alkalmazás URL-CÍMÉT használja abban, hogy ASE elem: [*sitename*]. [ *ASE létrehozásakor megadott altartomány*]   
    Miután kiválasztotta az ASP ASE létrehozása során látni fogja az alábbi frissítése altartomány **neve**

## <a name="createplan"></a> Az App Service-csomag létrehozása
Az App Service-környezetek App Service-csomagot hoz létre, amikor munkavégző a választott eltérőek, ahány megosztott dolgozókat-környezetben.  A munkavállalók kell használni azok, amelyekre a ASE a rendszergazda által lefoglalt  Ez azt jelenti, hogy hozzon létre egy új csomagot, meg kell rendelkeznie a ASE feldolgozókészletek, mint a példányok száma összesen rendelt összes már az adott munkavégző készletét a tervek további munkavállalók.  Nincs elég munkavállalók a ASE munkavégző készletét. a csomag létrehozása, ha szüksége ASE rendszergazdától őket hozzá dolgozni.

Az App Service-csomagokról egy App Service Environment-környezet által üzemeltetett másik különbség a kijelölés árképzési hiánya.  Ha az App Service-környezetek, a rendszer által felhasznált számítási erőforrások fizet, és a csomagok hozzáadott díja nem rendelkeznek az adott környezetben.  Általában az App Service-csomag létrehozásakor, válassza ki a tarifacsomagot, amely megadja, hogy a számlázási.  Az App Service Environment-környezet alapvetően helyen tartalom létrehozásához használható.  A környezet és a tartalom tárolására szolgáló nem kell fizetnie.

Az alábbi utasítások alapján létrehozását mutatják be az App Service-csomag az oktatóanyag az előző szakaszban leírtak szerint egy webalkalmazás létrehozása közben.

1. Kattintson a **hozzon létre új** a a terv kiválasztási Felületet, és adja meg a csomag nevét, ahogy normális esetben tenné egy ASE kívül.
2. Válassza ki a ASE, amely a hely kiválasztása a használni kívánt.
   
    Mivel az App Service Environment-környezet tulajdonképpen egy saját telepítési helyét, a Location alatt látható. 
   
    ![][2]
   
    A hely kiválasztása az egy ASE kiválasztását, miután a App Service-csomag létrehozása felhasználói felület frissülne.  A hely most van, és egy alkalmazáskészlet munkavégző objektumválasztó cseréli a árképzési terv objektumválasztó jeleníti meg a ASE rendszer és a régió nevét.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>A feldolgozókészleten kiválasztása
Általában az Azure App Service és az App Service-környezetek kívül vannak 3 számítási méretét, hogy a kijelölés egy dedikált ár terv.  Hasonló módon az egy ASE is munkavállalók 3 készleteinek határozza meg és adja meg, hogy feldolgozókészletek használt számítási méretét.  Mi, amely azt jelenti, hogy a bérlők a hajlamosnak helyette, amely egy tarifacsomagot számítási méretű az App Service-csomag kiválasztása, úgynevezett választja egy *feldolgozókészletek*.  

A munkavégző készlet kiválasztása felhasználói felület megmutatja, hogy a neve alatt feldolgozókészletek használt számítási méretét.  A rendelkezésre álló mennyiség hivatkozik hány számítási példányok érhetők el a készlethez tartozó használja.  A teljes licenckészletben lehet ténylegesen a megadottnál több példány, de ez az érték hivatkozik egyszerűen hány nincsenek használatban.  Ha módosítani szeretné az App Service-környezet hozzáadása a további számítási erőforrásokat lásd [az App Service-környezet konfigurálása](app-service-web-configure-an-app-service-environment.md).

![][4]

Ebben a példában csak két elérhető feldolgozókészletek láthatja. Ennek oka az, a ASE rendszergazda csak lefoglalt állomások e két munkavégző készletekbe.  A harmadik volna jelenik meg, a virtuális gép lefoglalt bele.  

## <a name="after-web-app-creation"></a>Webalkalmazás létrehozása után
Nincsenek webalkalmazások futtatásáért és felügyeletéért felelős App Service-csomagokról-környezetben, amelyeket figyelembe kell venni néhány szempontjai.  

Ahogy azt korábban említettük, a rendszer méretétől felelős a ASE tulajdonosa, és emiatt azok is, hogy nincs-e a kívánt App Service-csomagok tárolására elegendő kapacitással biztosításáért felelős. Ha nincs elérhető munkavállalók, nem kell az App Service-csomag létrehozásához.  Ez egyben a webes alkalmazás vertikális felskálázásával az IGAZ értéket.  Ha további példányokat kell majd meg kellene beolvasni az App Service Environment-környezet rendszergazdáját, hogy további dolgozók hozzáadása.

A web app és az App Service-csomag létrehozása után célszerű növelheti azt.  -Környezetben mindig szükség lehet az App Service-csomag hibatűrésének biztosítása az alkalmazásokhoz legalább 2 példánya.  Az App Service-csomag skálázás-környezetben megegyezik a normál az App Service-csomag felhasználói felületén keresztül.  További információ a méretezés [méretezése egy webalkalmazást az App Service-környezetben](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
