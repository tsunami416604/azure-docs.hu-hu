---
title: Az App Service létrehozása környezet 1-es verzió
description: Egy app service környezetben v1 leírását létrehozási folyamata
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
ms.openlocfilehash: 2741ea2931ddd7989fc05e1cddbeedb80bf30410
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386622"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Az App Service létrehozása környezet 1-es verzió 

> [!NOTE]
> Ez a cikk az App Service Environment-környezet v1 tárgya. Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
> 

### <a name="overview"></a>Áttekintés
Az App Service környezetben (ASE) az Azure App Service egy továbbfejlesztett konfigurációs funkció, amely nem érhető el a több-bérlős bélyegzők letölti a Premium szolgáltatás lehetőség. A ASE funkció az Azure App Service lényegében telepíti az ügyfél virtuális hálózathoz. Ahhoz, hogy az App Service Environment-környezetek olvasási által biztosított képességeket megértése a [Mi az az App Service-környezetek] [ WhatisASE] dokumentációját.

### <a name="before-you-create-your-ase"></a>A ASE létrehozása előtt
Fontos figyelembe vennie a dolgokhoz, nem módosíthatja. Nem módosítható a ASE kapcsolatos létrehozásukat követően aspektusait a következők:

* Hely
* Előfizetés
* Erőforráscsoport
* A virtuális hálózaton használt
* Használt 
* Alhálózat mérete

Amikor a kiadási VNet, és adja meg egy alhálózatot, győződjön meg arról, hogy nem elég nagy ahhoz, hogy jövőbeli növekedésének. 

### <a name="creating-an-app-service-environment-v1"></a>Alkalmazás létrehozása szolgáltatás környezet 1-es verzió
Az App Service Environment-környezet v1 létrehozásához Azure piactéren kereshet ***App Service Environment-környezet v1***, vagy halad át **hozzon létre egy erőforrást** -> **Web + mobil**  ->  **App Service Environment-környezet**. Egy ASEv1 létrehozása:

1. Adja meg a ASE nevét. A ASE számára megadott név lesz a ASE létrehozott alkalmazások. Ha a név a hajlamosnak appsvcenvdemo, altartománynév lenne: *appsvcenvdemo.p.azurewebsites.net*. Ha így hozott létre az alkalmazás neve *mytestapp*, lenne a következő megcímezhető *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Szóköz nem használható a ASE nevében. Nagybetűből kell állnia a nevet használja, ha a tartomány neve lesz nevű kisbetűs teljes verzióját. Egy ILB használatakor a ASE neve nem szerepel a altartomány, de ehelyett explicit módon megadott ASE létrehozása során.
   
    ![][1]
2. Válassza ki előfizetését. Az előfizetés, használja a ASE is olyan alkalmazásokra lesznek érvényesek az összes adott ASE hoz létre. A ASE nem helyez egy virtuális hálózatot, amely egy másik előfizetésben található.
3. Válassza ki vagy adja meg egy új erőforráscsoportot. Az erőforráscsoport a ASE használt azonosnak kell lennie, amely a virtuális hálózat szolgál. Egy már meglévő virtuális hálózatot választ ki, ha az erőforráscsoport kiválasztása a ASE a frissíti a virtuális hálózat tükrözi.
   
    ![][2]
4. Adja meg a virtuális hálózat és a hely beállításokat. Ha szeretné, hozzon létre új virtuális hálózatot, vagy válasszon ki egy már meglévő virtuális hálózatot. Ha egy új virtuális hálózat ezután megadhatja a nevét és helyét. Az új Vnetet fog rendelkezni, a cím tartomány 192.168.250.0/23 és nevű alhálózat **alapértelmezett** , amely 192.168.250.0/24 típusúként van definiálva. Egy korábban létező klasszikus és Resource Manager virtuális hálózat is egyszerűen válassza. A VIP választást határozza meg, ha a ASE közvetlenül elérhető az internetről (külső), vagy ha egy belső Load Balancer (ILB) használja. További információt, olvassa el őket [egy belső terheléselosztó használata az App Service-környezetek][ILBASE]. Ha egy külső VIP típusú majd kiválaszthatja a rendszer hozza létre IPSSL célokra hány külső IP-címeket. Ha belső majd szeretné adja meg az altartományt, amelyet a ASE fog használni. ASEs telepíthető a virtuális hálózatok, amelyek *vagy* nyilvános-címtartományokat, *vagy* RFC1918 címterek (azaz Magáncímeket). Ahhoz, hogy egy virtuális hálózatot egy nyilvános-címtartománnyal rendelkező, szüksége lesz a VNet időben létrehozásához. Amikor kiválaszt egy már meglévő virtuális hálózat akkor hozzon létre egy új alhálózatot ASE létrehozása során. **Nem használhat egy korábban létrehozott alhálózati a portálon. Létrehozhat egy ASE már meglévő alhálózattal, a resource manager-sablon használatával ASE létrehozásakor.** Egy ASE létrehozása egy sablon használatát az információkat, [egy App Service Environment-környezet létrehozása sablonból] [ ILBAseTemplate] és itt [ILB App Service-környezetek létrehozása sablonból][ASEfromTemplate].

### <a name="details"></a>Részletek
Egy ASE 2 első véget ér, és a 2 feldolgozónak hozza létre. Az első karakterlánccal végződik-e a HTTP/HTTPS-végpontként személyekről, és forgalmat küldeni a dolgozók, amelyek a szerepköröket, amelyek az alkalmazások tárolására. Módosítsa a mennyiséget ASE létrehozása után, és még akkor is létre lehet hozni automatikus skálázási szabályok ezeknek az erőforráskészleteknek. További részletekért manuális skálázás körül, kezelési és figyelési egy App Service Environment-környezet itt lépjen: [az App Service-környezetek konfigurálása][ASEConfig] 

Csak az egyik ASE létezhet az alhálózat, a ASE használják. Az alhálózat nem használható a ASE csakis

### <a name="after-app-service-environment-v1-creation"></a>App Service Environment-környezet v1 létrehozása után
ASE létrehozása után módosíthatja:

* Előtér mennyisége (minimális: 2)
* A munkavállalók mennyiség (minimális: 2)
* Az IP-SSL elérhető IP-címek mennyisége
* Számítási erőforrás mérete használják az előtér- vagy munkavállalók (előtér minimális mérete P2)

További részletek manuális skálázás, felügyeleti és App Service Environment-környezetek figyelése itt érhetők el: [az App Service-környezetek konfigurálása][ASEConfig] 

Információ az automatikus skálázás van egy útmutató itt: [egy App Service Environment-környezet automatikus skálázás konfigurálása][ASEAutoscale]

Nincsenek további függőségek, amelyek nem érhető el, például az adatbázis és a tárolási testreszabáshoz. Ezek az Azure-ban kezelt és a rendszer kapható. A rendszer storage legfeljebb 500 GB-ig támogatja a teljes App Service Environment-környezet és az adatbázis az Azure-ban módosul, a rendszer a skála igény szerint.

## <a name="getting-started"></a>Első lépések
App Service Environment-környezet v1 megkezdéséhez, lásd: [az App Service Environment-környezet v1 bemutatása][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
