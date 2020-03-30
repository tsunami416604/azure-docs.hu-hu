---
title: ASE 1-es eszköz létrehozása
description: Létrehozási folyamat leírása egy alkalmazásszolgáltatási környezet v1. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 752334e3d594b1f95786aecaca134b74c4e264d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688698"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>App Service-környezet létrehozása 1-es v1-ben 

> [!NOTE]
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól. Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).
> 

### <a name="overview"></a>Áttekintés
Az App Service Environment (ASE) az Azure App Service prémium szintű szolgáltatása, amely olyan továbbfejlesztett konfigurációs képességet biztosít, amely nem érhető el a több-bérlős bélyegzőkben. Az ASE szolgáltatás lényegében telepíti az Azure App Service-t az ügyfél virtuális hálózatába. Az App Service-környezetek által kínált képességek jobb megértéséhez olvassa el a [Mi az App Service-környezet][WhatisASE] dokumentációját.

### <a name="before-you-create-your-ase"></a>Az ASE létrehozása előtt
Fontos, hogy tudatában legyünk azoknak a dolgoknak, amelyeken nem lehet változtatni. Azok a szempontok, amelyeket nem lehet módosítani az ASE létrehozása után a következők:

* Hely
* Előfizetés
* Erőforráscsoport
* Használt virtuális hálózat
* Használt alhálózat 
* Alhálózat mérete

Virtuális hálózat kiválasztásakor és alhálózat megadásakor győződjön meg arról, hogy elég nagy ahhoz, hogy a jövőbeli növekedés hez. 

### <a name="creating-an-app-service-environment-v1"></a>App Service-környezet létrehozása 1-es v1-es
App Service-környezet 1-es eszközének létrehozásához keressen az Azure Marketplace for ***App Service Environment v1 alkalmazásban,*** vagy lépjen be az **Erőforrás** -> létrehozása**web + mobilalkalmazás-szolgáltatás** -> **környezetében.** ASEv1 létrehozása:

1. Adja meg az ASE nevét. Az ASE-hez megadott nevet a lesz használva az ASE-ben létrehozott alkalmazásokhoz. Ha az ASE neve appsvcenvdemo, az altartomány neve a következő lenne: *appsvcenvdemo.p.azurewebsites.net*. Ha így létrehozott egy app nevű *mytestapp*, lenne címezhető *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Az ASE nevében nem használhat szóközt. Ha a névben nagybetűket használ, a tartománynév lesz a név teljes kisbetűs változata. Ha ILB-t használ, az ASE-név nem az altartományban használatos, hanem explicit módon szerepel az ASE létrehozása során.
   
    ![][1]
2. Válassza ki előfizetését. Az ASE-hez használt előfizetés az adott ASE-ben létrehozott összes alkalmazásra is vonatkozik. Az ASE nem helyezhető el egy másik előfizetésben lévő virtuális hálózatban.
3. Új erőforráscsoport kijelölése vagy megadása. Az ASE-hez használt erőforráscsoportnak meg kell egyeznie a virtuális hálózathoz használt erőforráscsoporttal. Ha egy már meglévő virtuális hálózatot választ, az ASE erőforráscsoport-kiválasztása frissül, hogy tükrözze a virtuális hálózat.
   
    ![][2]
4. Adja meg a virtuális hálózat és a hely kiválasztása. Választhat, hogy hozzon létre egy új virtuális hálózatot, vagy válasszon ki egy már meglévő virtuális hálózatot. Ha új virtuális hálózatot választ, megadhatja a nevet és a helyet. Az új virtuális hálózat címtartománya 192.168.250.0/23, és egy **alapértelmezett** nevű alhálózat, amely 192.168.250.0/24. Egyszerűen kiválaszthat egy már meglévő klasszikus vagy erőforrás-kezelő virtuális hálózatot is. A VIP-típus kiválasztása határozza meg, hogy az ASE közvetlenül elérhető-e az internetről (külső), vagy belső terheléselosztót (ILB) használ. Ha többet szeretne megtudni róluk, olvassa el [A belső terheléselosztó használata App Service-környezettel][ILBASE]című részt. Ha kiválaszt egy VIP típusú külső, akkor kiválaszthatja, hogy hány külső IP-címek a rendszer jön létre az IPSSL célokra. Ha a Belső lehetőséget választja, akkor meg kell adnia az ASE által használni kívánt altartományt. Az ASE-k olyan virtuális hálózatokba is telepíthetők, amelyek *nyilvános* címtartományokat *vagy* RFC1918 címtereket (azaz magáncímeket) használnak. Ahhoz, hogy egy nyilvános címtartományú virtuális hálózatot használhasson, előre létre kell hoznia a virtuális hálózatot. Ha kiválaszt egy már meglévő virtuális hálózatot, létre kell hoznia egy új alhálózatot az ASE létrehozása során. **A portálon nem használható előre létrehozott alhálózat. Hozzon létre egy ASE egy már meglévő alhálózat, ha az ASE egy erőforrás-kezelő sablon használatával hoz létre.** AsE sablonból való létrehozásához használja az itt található információkat, [az App Service-környezet létrehozása sablonból][ILBAseTemplate] és itt [az ILB App Service-környezet létrehozása sablonból][ASEfromTemplate].

### <a name="details"></a>Részletek
Az ASE 2 előtér-végződéssel és 2 dolgozóval jön létre. Az előtér-végpontok HTTP/HTTPS-végpontként működnek, és forgalmat küldenek a dolgozóknak, amelyek az alkalmazásokat üzemeltető szerepkörök. Módosíthatja a mennyiséget az ASE létrehozása után, és még automatikus skálázási szabályokat is beállíthat ezeken az erőforráskészleteken. Az App Service-környezet manuális skálázásával, kezelésével és figyelésével kapcsolatos további részletekért látogasson el ide: [Az App Service-környezet konfigurálása][ASEConfig] 

Csak az egy ASE létezhet az ASE által használt alhálózatban. Az alhálózat nem használható másra, mint az ASE

### <a name="after-app-service-environment-v1-creation"></a>Az App Service-környezet v1 létrehozása után
Az ASE létrehozása után módosíthatja:

* Az elülső végek mennyisége (minimum: 2)
* A munkavállalók mennyisége (minimum: 2)
* Az IP SSL-hez rendelkezésre álló IP-címek mennyisége
* Az előtér-végződések vagy a dolgozók által használt erőforrásméretek számítása (az előtér minimális mérete P2)

További részletek a manuális skálázás, az App Service-környezetek kezelése és figyelése körül itt: [Az App Service-környezet konfigurálása][ASEConfig] 

Az automatikus skálázásról itt talál egy útmutatót: [Az automatikus skálázás konfigurálása egy App Service-környezetben][ASEAutoscale]

Vannak további függőségek, amelyek nem érhetők el a testreszabáshoz, például az adatbázis és a tároló. Ezeket az Azure kezeli, és a rendszerhez tartoznak. A rendszertároló legfeljebb 500 GB-ot támogat a teljes App Service-környezetben, és az adatbázist az Azure szükség szerint módosítja a rendszer mérete miatt.

## <a name="getting-started"></a>Első lépések
Az App Service-környezet v1-es alkalmazásával való ismerkedés: [Bevezetés az App Service-környezet v1-be című témakörben][WhatisASE]

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
