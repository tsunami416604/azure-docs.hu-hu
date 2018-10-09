---
title: Mi az Azure Cloud Servicesben |} A Microsoft Docs
description: 'További tudnivalók: Mi az Azure Cloud Servicesben.'
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 309c7275a1e775ed2bd689520ac39501e8bd6052
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857822"
---
# <a name="overview-of-azure-cloud-services"></a>Az Azure Cloud Services áttekintése
Az Azure Cloud Services egyik példája egy [szolgáltatásként nyújtott platformon](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Például [Azure App Service](../app-service/app-service-web-overview.md), ez a technológia úgy tervezték, hogy skálázható, megbízható és költséghatékony működéséhez alkalmazások támogatása. Ugyanolyan módon, hogy App Service-ben üzemeltetett virtuális gépeken (VM), tehát túl van az Azure Cloud Servicesben. Azonban hogy jobban szabályozhatja a virtuális gépeket. Azure Cloud Services szolgáltatást használó virtuális gépek is telepíthető a saját szoftvereit, és távolról elérheti azokat.

![Az Azure Cloud Services – diagram](./media/cloud-services-choose-me/diagram.png)

Nagyobb mértékű is azt jelenti, hogy kevesebb a könnyű használatra. Ha nincs szükség a további beállítások, általában gyorsabb és könnyebb a webalkalmazások használatának és a webalkalmazások futtatása az App Service szolgáltatás képest Azure Cloud servicesben.

Azure Cloud Services-szerepkörök két típusa van. Az egyetlen különbség a kettő között, a szerepkört virtuális gépeken üzemeltetett hogyan:

* **Webová role**: automatikusan üzembe helyezi és futtatja az alkalmazás IIS-en keresztül.

* **Feldolgozói szerepkör**: nem használja az IIS és az alkalmazás különálló futtatja.

Előfordulhat, hogy például, hogy egy egyszerű alkalmazást csak egyetlen webes szerepkör, a kiszolgáló a webhely használja. Olyan összetettebb alkalmazást előfordulhat, hogy egy webes szerepkör segítségével kezeli a felhasználók a bejövő kéréseket, és akkor továbbítja a feldolgozói szerepkör feldolgozási be ezeket a kérelmeket. (Ehhez a kommunikációhoz használhat [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) vagy [Azure Queue storage](../storage/common/storage-introduction.md).)

Mivel az előző ábrán javasol, egy alkalmazást az összes virtuális gép ugyanazon a felhőszolgáltatáson futnak. Az alkalmazás keretében egyetlen nyilvános IP-címet, a kérelmek automatikusan betölteni a felhasználói hozzáférés az alkalmazás virtuális gépek kiegyensúlyozását. A platform [méretezi és helyezi üzembe](cloud-services-how-to-scale-portal.md) a virtuális gépek az Azure Cloud Services alkalmazások úgy, hogy elkerüli a hardverhiba hibaérzékeny pont.

Annak ellenére, hogy az alkalmazások virtuális gépeken futnak, fontos tudni, hogy az Azure Cloud Services nyújt PaaS, nem infrastruktúra-szolgáltatás (IaaS). Íme, gondolja át, az egyik lehetőség. Az IaaS, például az Azure Virtual Machines akkor először létrehozhat és konfigurálhat a környezetben fusson az alkalmazás. Ezt követően üzembe az alkalmazást erre a környezetre. Most már a világ számos módon többek között az új javított verziói az operációs rendszer az egyes virtuális gépek üzembe helyezése kezeléséért. A PaaS ezzel szemben az legyen, ha a környezet már létezik. Ehhez csak az alkalmazás üzembe helyezéséhez. A platform az fut, beleértve az operációs rendszer új verzióinak telepítése felügyeleti történik meg.

## <a name="scaling-and-management"></a>Skálázás és felügyelet
Az Azure Cloud Serviceshez ne hozzon létre virtuális gépeket. Ehelyett adja meg a konfigurációs fájlt, amely jelzi az Azure egyes hány szeretne, például a "három webes szerepkör példányai" és "két feldolgozói szerepkörpéldányok." A platform majd őket az Ön hozza létre. Továbbra is választja [milyen méretű](cloud-services-sizes-specs.md) azokat a virtuális gépek biztonsági kell lennie, de nincs explicit módon létrehozhatja saját magának. Ha az alkalmazásnak egy nagyobb terhelés kezeléséhez, kérhet további virtuális gépeket, és az Azure létrehozza az ezekhez a példányokhoz. Ha a terhelés csökkenése, állítsa le az ezekhez a példányokhoz, és állítsa le azokat kellene fizetnie.

Az Azure Cloud Services-alkalmazás általában szeretné elérhetővé tenni a felhasználók számára egy kétlépéses folyamat keresztül. A fejlesztő első [feltölti az alkalmazást](cloud-services-how-to-create-deploy-portal.md) a platform előkészítési területéhez. Amikor készen áll a fejlesztő ellenőrizze az alkalmazás live, a Váltás az Azure portal használata az éles átmeneti. Ez [váltás átmeneti és éles környezet között](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) üzemkimaradás nélkül, amely lehetővé teszi, hogy a felhasználók megzavarása nélkül egy új verzióra szeretne frissíteni egy futó alkalmazás teheti meg.

## <a name="monitoring"></a>Figyelés
Az Azure Cloud Services is figyelését teszi lehetővé. Például virtuális gépeket azt észleli hibás fizikai kiszolgálóra, és újraindítja az új gép adott kiszolgálón futó virtuális gépet. De Azure Cloud Services is észleli a hibás virtuális gépek és alkalmazások, nem csak a hardveres hibák esetén. Ellentétben a virtuális gépek az ügynök belül minden webes és feldolgozói szerepkör rendelkezik, és így elindíthatja az új virtuális gépek és alkalmazáspéldányok, ha hiba történik.

Az Azure Cloud Services PaaS jellege egyéb következmények túl van. A legfontosabb egyik célja, hogy ez a technológia-alapú alkalmazásokhoz kell írni megfelelően futni, ha bármely webes vagy feldolgozói szerepkör-példány nem sikerült. Ennek érdekében az Azure Cloud Services-alkalmazás állapota a fájlrendszer, a saját virtuális gépek nem karbantartása. Virtuális gépek használatával létrehozott virtuális gépek, eltérően az Azure Cloud Services virtuális gépeire írási nem állandó. Nincs virtuálisgép-adatlemez nem hasonlít. Ehelyett az Azure Cloud Services-alkalmazás kell explicit módon állapotadatokat írni az összes Azure SQL Database, blobok, táblák vagy valamilyen egyéb külső tárhelyen. Alkalmazások létrehozását, így megkönnyíti a méretezési csoport és ellenállóbbá hiba, amely mindkét fontos célja a Azure Cloud Services.

## <a name="next-steps"></a>További lépések
* [Felhőszolgáltatás-alapú alkalmazás létrehozása a .NET-ben](cloud-services-dotnet-get-started.md) 
* [Felhőszolgáltatás-alapú alkalmazás létrehozása Node.js-ben](cloud-services-nodejs-develop-deploy-app.md) 
* [Felhőszolgáltatás-alapú alkalmazás létrehozása php](../cloud-services-php-create-web-role.md) 
* [Felhőszolgáltatás-alapú alkalmazás létrehozása pythonban](cloud-services-python-ptvs.md)



