---
title: Hogyan hozhat létre egy App Service környezet v1
description: Folyamat leírásának létrehozása az app service-környezet v1
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 289ff76e533497a731a4fc51b3e54101a9d34a68
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958366"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Hogyan hozhat létre egy App Service környezet v1 

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 szól. Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
> 

### <a name="overview"></a>Áttekintés
Az App Service Environment (ASE) az Azure App Service, amely egy továbbfejlesztett konfigurációs-képesség, amely nem érhető el a több-bérlős stampek biztosít prémium szintű szolgáltatás lehetőség. Az ASE funkció lényegében egy ügyfél virtuális hálózatban helyez üzembe az Azure App Service. A részletesebb információhoz juthat az App Service Environment-környezetek olvasása által nyújtott képességeket a [Mi az App Service-környezet] [ WhatisASE] dokumentációját.

### <a name="before-you-create-your-ase"></a>Az ASE létrehozása előtt
Fontos figyelembe venni a dolgokat, nem módosítható. Szempontjaival kapcsolatban az ASE nem módosíthatja, a létrehozást követően a következők:

* Hely
* Előfizetés
* Erőforráscsoport
* Használt virtuális hálózat
* Alhálózat használata 
* Alhálózat mérete

Ha kiválasztotta a virtuális hálózathoz, és adjon meg egy alhálózatot, győződjön meg arról, elég nagy a jövőbeli növekedésének. 

### <a name="creating-an-app-service-environment-v1"></a>Alkalmazás létrehozása Service környezet v1
Hozzon létre egy App Service Environment-környezet v1, az Azure Marketplace segítségével megkeresheti ***App Service Environment-környezet v1***, vagy haladjon végig **erőforrás létrehozása** -> **Web + mobil**  ->  **App Service Environment-környezet**. Az ASEv1 létrehozása:

1. Adja meg az ASE nevét. Az ASE környezetben létrehozott alkalmazások az ASE számára megadott név lesz használható. Ha az ASE neve appsvcenvdemo, az altartomány nevével együtt lenne: *appsvcenvdemo.p.azurewebsites.net*. Ha az alkalmazás neve az így létrehozott *mytestapp*, lenne a megcímezhető *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Nem használhat térközt be az ASE nevét. Nagybetűk használata a névben, ha a tartomány neve lesz ilyen nevű kisbetűs teljes verzióját. ILB használja, ha az ASE neve nem szerepel az altartomány, de ehelyett explicit módon meghatározva ASE létrehozása során.
   
    ![][1]
2. Válassza ki előfizetését. Az előfizetés, az ASE-t használ, hogy az ASE környezetben létrehozott összes alkalmazás is érvényesek. Az ASE-t, amely egy másik előfizetésben található virtuális hálózat nem helyezhető el.
3. Válassza ki, vagy adjon meg egy új erőforráscsoportot. Az ASE használt erőforráscsoport azonosnak kell lennie, amely a virtuális hálózat szolgál. Ha egy már meglévő virtuális hálózat, az erőforráscsoport kiválasztása az ASE, a virtuális hálózat megfelelően frissülnek.
   
    ![][2]
4. Adja meg a virtuális hálózat és a hely beállításokat. Ha szeretné, hozzon létre egy új virtuális hálózatot, vagy válasszon ki egy már meglévő virtuális hálózathoz. Ha egy új virtuális hálózat nevét és helyét is megadhat, majd válassza ki. Az új VNet fog rendelkezni, a cím-tartományt 192.168.250.0/23 és a egy nevű alhálózatot **alapértelmezett** , amely 192.168.250.0/24 nevezünk. Egyszerűen kiválaszthatja egy már létező klasszikus vagy Resource Manager virtuális hálózatot. A virtuális IP-CÍMEK adattípus-választást határozza meg, ha az ASE közvetlenül hozzáférni az internetről (külső), vagy ha egy belső Load Balancer (ILB) használja. További információ, olvassa el őket [belső terheléselosztó használata az App Service-környezet][ILBASE]. Ha egy külső virtuális IP-cím típusú majd kiválaszthatja a rendszer a létrehozott IPSSL célokra hány külső IP-címeket. Ha belső választja majd meg kell adnia az altartomány az ASE-t használó. ASE is telepíthető, virtuális hálózatok, amelyek az *vagy* nyilvános címtartományok *vagy* RFC1918 címterek (azaz a magánhálózati címek). Nyilvános címtartományt a virtuális hálózat használatához szüksége lesz a kívánt időben virtuális hálózat létrehozása. Amikor kiválaszt egy meglévő Vnetet kell hozzon létre egy új alhálózatot ASE létrehozása során. **A portálon egy előre létrehozott alhálózat nem használható. Az ASE létrehozhat egy meglévő alhálózat létrehozásakor az ASE-t egy resource manager-sablon használatával.** ASE létrehozása a sablon használata az adatok itt, [App Service Environment sablon alapján hoz] [ ILBAseTemplate] és itt [sablonbólegyILBAppServiceEnvironment-környezetlétrehozása] [ASEfromTemplate].

### <a name="details"></a>Részletek
Az ASE 2 előtér-és 2 feldolgozó jön létre. A kezelőfelületek működjön, a HTTP/HTTPS-végpontokat, és forgalmat küldeni a dolgozók, amelyek a szerepköröket, amelyek az alkalmazások üzemeltetéséhez. ASE létrehozása után módosítsa a mennyiséget, és még akkor is létre lehet hozni automatikus skálázási szabályok ezeknek az erőforráskészleteknek. Manuális skálázás körül további részletekért felügyeleti és monitorozási az App Service-környezet itt: [App Service-környezet konfigurálása][ASEConfig] 

Csak az egy ASE létezhet az ASE által használt alhálózaton. Az alhálózat nem használható az ASE csakis

### <a name="after-app-service-environment-v1-creation"></a>App Service Environment-környezet v1 létrehozása után
ASE létrehozása után módosíthatja:

* Az előtér-mennyiség (minimum: 2)
* A munkavállalók mennyiség (minimum: 2)
* IP-címeket az IP SSL-hez elérhető mennyiségét
* Az előtér- vagy a dolgozók által használt erőforrás méretek COMPUTE (előtér minimális mérete P2)

Nincsenek további információ a Manuális méretezés, felügyeleti és figyelési az App Service Environment-környezetek itt: [App Service-környezet konfigurálása][ASEConfig] 

Az automatikus skálázás információ van itt egy útmutató: [az automatikus méretezés App Service-környezet konfigurálása][ASEAutoscale]

Vannak, amelyek nem érhetők el a testreszabás, például az adatbázis- és további függőségeket. Ezek az Azure által kezelt és a rendszer kapható. A fájlrendszer-tárhely a teljes App Service-környezet támogatja a legfeljebb 500 GB-ig, és az adatbázis az Azure módosul, a rendszer a méretezési csoport szükség szerint.

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezet v1, lásd: [az App Service Environment v1 bemutatása][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
