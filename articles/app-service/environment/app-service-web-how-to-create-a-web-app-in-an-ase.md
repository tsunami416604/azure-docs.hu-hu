---
title: Webes alkalmazás létrehozása egy App Service Environment-környezet v1-ben
description: Ismerje meg, hogyan hozhat létre web apps és az app service-csomagok egy App Service Environment-környezet v1-ben
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
ms.openlocfilehash: 78a1f3bb1d49a8a2e8a44d665abd211c32711d7c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961427"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Webes alkalmazás létrehozása egy App Service Environment-környezet v1-ben

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 szól.  Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan hozhat létre webalkalmazásokat és az App Service-csomagok egy [App Service Environment-környezet v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Ha szeretné megtudni, hogyan hozhat létre egy webalkalmazást, de mindezt az App Service Environment-környezetben, lásd: nem szükséges [.NET-webalkalmazás létrehozása](../app-service-web-get-started-dotnet.md) vagy a kapcsolódó oktatóanyagokban más nyelv és keretrendszer.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy létrehozott egy App Service Environment-környezet. Ha még nem tette, hogy, [hozzon létre egy App Service Environment-környezet](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása
1. Az a [az Azure Portal](https://portal.azure.com/), kattintson a **erőforrás létrehozása > Web + mobil > webalkalmazás**. 
   
    ![][1]
2. Válassza ki előfizetését.  
   
    Ha több előfizetéssel rendelkezik, vegye figyelembe, hogy szeretne létrehozni egy alkalmazást az App Service-környezet, meg kell használni ugyanahhoz az előfizetéshez, amely a környezet létrehozásakor használt. 
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
   
    *Erőforráscsoportok* felügyelheti a kapcsolódó Azure-erőforrások egy egységként, és akkor hasznos, ha a létrehozó *szerepköralapú hozzáférés-vezérlés* (RBAC) szabályok az alkalmazások számára. További információkért lásd: [Azure Resource Manager áttekintése][ResourceGroups]. 
4. Válassza ki vagy hozzon létre egy App Service-csomagot.
   
    *App Service-csomagok* webalkalmazások felügyelt csoportja.  Általában amikor díjszabási lehetőséget választja, a felszámított díj alkalmazza az App Service-csomag helyett az egyes alkalmazásokra. Az ASE környezetben után kell fizetni, az ASE számára lefoglalt számítási példányokért ahelyett, hogy az ASP van felsorolva.  Példányok egy webalkalmazás az App Service a példányok vertikális skálázáshoz tervet, és ez hatással van az összes, a web apps-csomag.  Egyes funkciók, például a webhelyek átmeneti tárhelyei vagy a VNET-integráció a csomagon belüli mennyiség korlátozásai is.  További információkért lásd: [Azure App Service-csomagok áttekintése](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    Az App Service-csomagok a helyre, amely alatt a csomag nevét megadó megtekintésével azonosíthatja az ASE környezetben.  
   
    ![][5]
   
    Ha szeretné használni az App Service-csomag, amely az App Service-környezet már létezik, válassza ki a csomagot. Ha szeretne létrehozni egy új App Service-csomag, tekintse meg a következő rész a jelen oktatóanyag [App Service-csomag létrehozása az App Service-környezet](#createplan).
5. Adja meg a webalkalmazás nevét, és kattintson **létrehozás**. 
   
    Ha az ASE-t használ egy külső virtuális IP-cím van-e az alkalmazás URL-címét egy ASE: [*sitename*]. [ *az App Service-környezet neve*]. p.azurewebsites.net helyett [*sitename*]. azurewebsites.net
   
    Ha az ASE-t használja egy belső virtuális IP-cím, majd az alkalmazás URL-CÍMÉT, hogy az ASE nem: [*sitename*]. [ *ASE létrehozása során megadott altartomány*]   
    Miután kiválasztotta az ASP ASE létrehozása során látni fogja az alábbi frissítés altartomány **neve**

## <a name="createplan"></a> App Service-csomag létrehozása
App Service-csomag az App Service-környezet létrehozásakor a feldolgozói választási lehetőségek: különböző, mert nincs közös feldolgozók az ASE környezetben.  A feldolgozók kell használni azok, amelyekre az ASE-t a rendszergazda által lefoglalt  Ez azt jelenti, hogy hozzon létre egy új csomagot, hogy szüksége lesz az ASE munkavégző készletét, mint a példányok száma a csomagok keretében, már a feldolgozói készlethez tartozó összes lefoglalt további feldolgozóra.  Ha az ASE feldolgozókészletben a terv létrehozásához nem rendelkezik elegendő feldolgozók, azokat hozzá az ASE adminisztrátorral együttműködve szeretné.

Az App Service-környezet által üzemeltetett App Service-csomagok egy másik különbség díjszabás kiválasztása hiánya.  App Service-környezet, ha a rendszer által felhasznált számítási erőforrásokért kellene fizetnie vannak, és a tervek hozzáadott díjai nem rendelkezik az adott környezetben.  Általában egy App Service-csomag létrehozásakor kiválaszthatja egy díjszabási csomagra, amely meghatározza, hogy a számlázás.  App Service-környezet tulajdonképpen egy privát helyen, ahol létre tartalmat.  A környezet és a tartalom üzemeltetéséhez, nem kell fizetnie.

A következőkben megtudhatja, hogyan hozhat létre az App Service-csomag, az oktatóanyag az előző szakaszban leírtak szerint webes alkalmazás létrehozása közben.

1. Kattintson a **hozzon létre új** terv kiválasztása felhasználói felületén, és adja meg a csomag nevét, ahogy azt szokásosan tenné az ASE-en kívül.
2. Válassza az ASE Környezetet, amely a hely kiválasztása a használni kívánt.
   
    Mivel az App Service-környezet lényegében egy saját telepítési helyét, a hely látható. 
   
    ![][2]
   
    Egy ASE környezethez a hely kiválasztása a kijelölés után az App Service-csomag létrehozása felhasználói felületi frissítések.  Van, és a egy feldolgozói készlethez választó helyére a díjszabási csomag választó a helyét most az ASE rendszer és a régió nevét jeleníti meg.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>A feldolgozókészletek kiválasztása
Általában az Azure App Service és az App Service Environment-en kívül nincsenek 3 dedikált tarifacsomag kiválasztása a compute-méretet.  Hasonló módon az ASE is munkavállalók legfeljebb 3 készletek definiálása és adja meg, hogy feldolgozókészlet használt számítási méretét.  Ennek köszönhetően a bérlők számára az ASE, hogy inkább kiválasztása az App Service-csomag számítási méret rendelkező díjszabási csomagot, úgynevezett válassza egy *feldolgozókészlet*.  

A feldolgozói készlethez kijelölt felhasználói felület megmutatja, hogy feldolgozókészlet neve alatt használt számítási méretét.  A rendelkezésre álló mennyiség hány számítási példányok akkor érhetőek el, hogy a készletben való használatra vonatkozik.  A teljes készlet ténylegesen előfordulhat, hogy több példányban nagyobb számú, de ez az érték hivatkozik egyszerűen hány nem szerepelnek használja.  Ha módosítani szeretné az App Service Environment hozzáadásához a nagyobb számítási erőforrás megtekintéséhez [az App Service Environment konfigurálása](app-service-web-configure-an-app-service-environment.md).

![][4]

Ebben a példában csak két elérhető feldolgozókészletek láthatja. Ennek oka az, az ASE-rendszergazda csak lefoglalásra gazdagépek két feldolgozó készletekben.  A harmadik jelennek meg bele a lefoglalt virtuális gépek esetén.  

## <a name="after-web-app-creation"></a>Webalkalmazás létrehozása után
Néhány szempontot futó webes alkalmazások és az ASE környezetben, figyelembe kell venni az App Service-csomagok kezelése.  

Korábban feljegyzett felelőssége méretét, a rendszer az ASE tulajdonosa és eredményeként is, hogy nincs-e a kívánt App Service-csomagok tárolására elegendő kapacitással biztosításáért felelős. Ha nincs rendelkezésre álló feldolgozók, nem lesz képes az App Service-csomag létrehozása.  Ez akkor is igaz értéket a webes alkalmazás vertikális felskálázásával.  Ha több példány kell majd meg kellene az App Service Environment-környezet rendszergazdát, hogy adjon hozzá további feldolgozóra beolvasása.

A webalkalmazás és App Service-csomag létrehozása után célszerű vertikálisan.  Az ASE környezetben mindig szüksége lesz az App Service-csomag, az alkalmazások hibatűrő képességének biztosítása érdekében legalább 2 példányait.  Méretezés App Service-csomag az ASE környezetben megegyezik a normál az App Service-csomag felhasználói felületén keresztül.  További információ a skálázás [webes alkalmazás méretezése App Service Environment-környezetben](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

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
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
