---
title: Mi az Azure Cloud Services | Microsoft dokumentumok
description: Ismerje meg, hogy mi az Azure Cloud Services.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386850"
---
# <a name="overview-of-azure-cloud-services"></a>Az Azure felhőszolgáltatások áttekintése
Az Azure Cloud Services egy példa a [platform szolgáltatásként](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Az [Azure App Service szolgáltatáshoz](../app-service/overview.md)hasonlóan ez a technológia is méretezhető, megbízható és olcsó alkalmazásokat támogat. Ugyanúgy, ahogy az App Service virtuális gépeken (VM-eken) található, az Azure Cloud Services is. Azonban több ellenőrzést a virtuális gépek felett. Telepítheti saját szoftverét az Azure Cloud Servicest használó virtuális gépekre, és távolról is elérheti őket.

![Az Azure Cloud Services diagramja](./media/cloud-services-choose-me/diagram.png)

A nagyobb kontroll azt is jelenti, hogy kevésbé könnyű a használat. Ha csak a további vezérlési beállításokra van szüksége, általában gyorsabb és egyszerűbb egy webalkalmazást beszerezni és futtatni az App Services Web Apps szolgáltatásában az Azure Cloud Serviceshez képest.

Az Azure Cloud Services-szerepköröknek két típusa van. Az egyetlen különbség a kettő között az, hogy a szerepkör hogyan van elrendezve a virtuális gépeken:

* **Webes szerepkör:** Automatikusan telepíti és üzemelteti az alkalmazást az IIS-en keresztül.

* **Feldolgozói szerepkör:** Nem használja az IIS-t, és önállóan futtatja az alkalmazást.

Előfordulhat például, hogy egy egyszerű alkalmazás csak egyetlen webes szerepkört használ, amely egy webhelyet szolgál ki. Egy összetettebb alkalmazás webes szerepkört használhat a felhasználók tól érkező kérelmek kezelésére, majd továbbítja ezeket a kérelmeket egy feldolgozói szerepkörnek feldolgozásra. (Ez a kommunikáció az [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) vagy az Azure Queue storage használatát is [használhatja.)](../storage/common/storage-introduction.md)

Ahogy az előző ábrán is sugallja, az összes virtuális gép egyetlen alkalmazásban fut ugyanabban a felhőszolgáltatásban. A felhasználók egyetlen nyilvános IP-címen keresztül érhetik el az alkalmazást, és a kérelmek automatikusan elvannak osztva az alkalmazás virtuális gépein. A platform [úgy méretezi és telepíti](cloud-services-how-to-scale-portal.md) a virtuális gépeket egy Azure Cloud Services-alkalmazásban, hogy egyetlen hardverhiba elkerülhető legyen.

Annak ellenére, hogy az alkalmazások virtuális gépeken futnak, fontos megérteni, hogy az Azure Cloud Services PaaS-t biztosít, nem infrastruktúrát szolgáltatásként (IaaS). Itt van egy módja annak, hogy gondolja át. Az IaaS, például az Azure virtuális gépek, először hozza létre és konfigurálja a környezetet az alkalmazás fut. Ezután üzembe helyezi az alkalmazást ebben a környezetben. Ön felelős a világ nagy részének kezeléséért, például az operációs rendszer új javított verzióinak üzembe helyezésével az egyes virtuális gépeken. A PaaS,ezzel szemben, ez olyan, mintha a környezet már létezik. Mindössze annyit kell tennie, hogy telepíti az alkalmazást. A platform kezelése, amelyen fut, beleértve az operációs rendszer új verzióinak telepítését is, az Ön feladata.

## <a name="scaling-and-management"></a>Méretezés és felügyelet
Az Azure Cloud Services használatával nem hozhat létre virtuális gépeket. Ehelyett egy konfigurációs fájlt biztosít, amely megmondja az Azure-nak, hogy hány at szeretne, például "három webes szerepkör-példány" és "két feldolgozói szerepkör példánya". A platform ezután létrehozza őket az Ön számára. Továbbra is kiválaszthatja, [hogy milyen méretűlegyenek](cloud-services-sizes-specs.md) a biztonsági mentési virtuális gépek, de nem hozza létre őket kifejezetten saját maga. Ha az alkalmazásnak nagyobb terhelést kell kezelnie, kérhet további virtuális gépeket, és az Azure létrehozza ezeket a példányokat. Ha a terhelés csökken, leállíthatja ezeket a példányokat, és leállíthatja a fizetésüket.

Az Azure Cloud Services-alkalmazások általában kétlépésben érhetők el a felhasználók számára. A fejlesztő először [feltölti az alkalmazást](cloud-services-how-to-create-deploy-portal.md) a platform átmeneti területre. Amikor a fejlesztő készen áll arra, hogy az alkalmazást élőben hozza létre, az Azure Portal használatával cserélik az előkészítést az éles környezettel. Ez [az átmeneti és éles környezet közötti váltás](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) leállás nélkül is elvégezhető, ami lehetővé teszi, hogy egy futó alkalmazás új verzióra frissítve legyen anélkül, hogy zavarná a felhasználókat.

## <a name="monitoring"></a>Figyelés
Az Azure Cloud Services figyelést is biztosít. A virtuális gépekhez hasonlóan észleli a meghibásodott fizikai kiszolgálót, és újraindítja az adott kiszolgálón egy új gépen futó virtuális gépeket. Az Azure Cloud Services azonban nem csak a hardverhibákat, hanem a meghibásodott virtuális gépeket és alkalmazásokat is észleli. A virtuális gépekkel ellentétben minden webes és feldolgozói szerepkörben rendelkezik egy ügynökkel, így képes új virtuális gépeket és alkalmazáspéldányokat elindítani, ha hibák fordulnak elő.

Az Azure Cloud Services PaaS-jellege más következményekkel is jár. Az egyik legfontosabb az, hogy az erre a technológiára épülő alkalmazásokat helyesen kell írni, ha bármely webes vagy feldolgozói szerepkörpéldány meghibásodik. Ennek elérése érdekében egy Azure Cloud Services-alkalmazás nem tarthatja fenn a saját virtuális gépeinek fájlrendszerében az állapotot. A virtuális gépekkel létrehozott virtuális gépekkel ellentétben az Azure Cloud Services virtuális gépeinek készített írási műveletek nem állandóak. Nincs semmi, mint egy virtuális gépek adatlemez. Ehelyett egy Azure Cloud Services-alkalmazás explicit módon írja az összes állapotot az Azure SQL Database, blobok, táblák vagy más külső tároló. Az ilyen módon készült alkalmazások egyszerűbben méretezhetők és ellenállóbbak a hibákkal szemben, amelyek az Azure Cloud Services fontos céljai.

## <a name="next-steps"></a>További lépések
* [Felhőalapú szolgáltatásalkalmazás létrehozása a .NET szolgáltatásban](cloud-services-dotnet-get-started.md) 
* [Felhőalapú szolgáltatásalkalmazás létrehozása a Node.js-ben](cloud-services-nodejs-develop-deploy-app.md) 
* [Felhőalapú alkalmazás létrehozása PHP-ben](../cloud-services-php-create-web-role.md) 
* [Felhőalapú szolgáltatásalkalmazás létrehozása a Pythonban](cloud-services-python-ptvs.md)






