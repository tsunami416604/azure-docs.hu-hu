---
title: Mi az Azure Cloud Services | Microsoft Docs
description: Ismerje meg, mi az Azure Cloud Services.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386850"
---
# <a name="overview-of-azure-cloud-services"></a>Az Azure Cloud Services áttekintése
Az Azure Cloud Services a [szolgáltatásként szolgáló platform](https://azure.microsoft.com/overview/what-is-paas/) (Péter) példája. A [Azure app Servicehoz](../app-service/overview.md)hasonlóan ez a technológia a méretezhető, megbízható és olcsó működést támogató alkalmazások támogatására szolgál. Ugyanúgy, ahogy a App Service a virtuális gépeken (VM) üzemel, így az Azure Cloud Services is. Azonban nagyobb mértékben szabályozhatja a virtuális gépeket. Telepítheti saját szoftvereit az Azure Cloud Servicest használó virtuális gépeken, és távolról is elérheti őket.

![Azure Cloud Services diagram](./media/cloud-services-choose-me/diagram.png)

A további szabályozás a kevésbé egyszerű használatot is jelenti. Ha a további vezérlési lehetőségekre van szüksége, általában gyorsabb és egyszerűbb a webalkalmazások beszerzése a App Service Web Apps szolgáltatásában az Azure Cloud Services-hoz képest.

Az Azure Cloud Services szerepköreinek két típusa van. Az egyetlen különbség a kettő között az, hogy a szerepkör a virtuális gépeken van tárolva:

* **Webes szerepkör**: az alkalmazás automatikus üzembe helyezése és üzemeltetése az IIS-en keresztül.

* **Feldolgozói szerepkör**: nem használja az IIS-t, és önálló alkalmazást futtat.

Egy egyszerű alkalmazás például csak egyetlen webes szerepkört használhat, amely egy webhelyet fog kiszolgálni. Egy összetettebb alkalmazás webes szerepkörrel kezelheti a felhasználóktól érkező kéréseket, majd átadhatja ezeket a kérelmeket a feldolgozásra feldolgozói szerepkörnek. (Ez a kommunikáció [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) vagy az [Azure üzenetsor-tárolást](../storage/common/storage-introduction.md)is használhatja.)

Ahogy az előző ábrán is látható, egy adott alkalmazás összes virtuális gépe ugyanabban a felhőalapú szolgáltatásban fut. A felhasználók egyetlen nyilvános IP-címen keresztül férhetnek hozzá az alkalmazáshoz, és a kérések automatikusan terheléselosztást végeznek az alkalmazás virtuális gépei között. A platform a virtuális gépeket egy Azure Cloud Services alkalmazásban [méretezi és helyezi üzembe](cloud-services-how-to-scale-portal.md) úgy, hogy elkerülje a hardver meghibásodását.

Annak ellenére, hogy az alkalmazások virtuális gépeken futnak, fontos tisztában lenni azzal, hogy az Azure Cloud Services a nem infrastruktúra-szolgáltatásként (IaaS) biztosítja a Pásti szolgáltatást. Itt az egyik lehetőség, hogy meggondoljuk. A IaaS-mel (például Azure Virtual Machines) először létre kell hoznia és konfigurálnia kell azt a környezetet, amelyben az alkalmazás fut. Ezután üzembe helyezi az alkalmazást ebbe a környezetbe. Ennek a világnak az Ön feladata, hogy olyan dolgokat hajtson végre, mint például az operációs rendszer új, javított verzióinak üzembe helyezése az egyes virtuális gépeken. A Pástiban, ezzel szemben, mintha a környezet már létezik. Mindössze annyit kell tennie, hogy üzembe helyezi az alkalmazást. Az Ön által futtatott platform kezelése, beleértve az operációs rendszer új verzióinak telepítését is, kezeljük Önnek.

## <a name="scaling-and-management"></a>Skálázás és felügyelet
Az Azure Cloud Services nem hoz létre virtuális gépeket. Ehelyett meg kell adnia egy konfigurációs fájlt, amely megadja, hogy az Azure hányat szeretne, például a "három webes szerepkör példányai" és a "két feldolgozói szerepkör példányai". A platform ezután létrehozza azokat. Továbbra is kiválaszthatja, hogy a virtuális gépek [milyen méretűek](cloud-services-sizes-specs.md) legyenek, de Ön kifejezetten nem hozza létre őket. Ha az alkalmazásnak nagyobb terhelést kell kezelnie, több virtuális gépet is kérhet, és az Azure létrehozza ezeket a példányokat. Ha csökkenti a terhelést, leállíthatja ezeket a példányokat, és leállíthatja azok fizetését.

Az Azure Cloud Services-alkalmazások általában két lépésből álló folyamaton keresztül érhetők el a felhasználók számára. A fejlesztő először [feltölti az alkalmazást](cloud-services-how-to-create-deploy-portal.md) a platform előkészítési területeire. Ha a fejlesztő készen áll az alkalmazás élő állapotba helyezésére, a Azure Portal az éles környezetben történő előkészítést használják. Az [előkészítés és a gyártás közötti váltás](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) leállás nélkül is elvégezhető, ami lehetővé teszi, hogy egy futó alkalmazás frissítve legyen egy új verzióra anélkül, hogy zavarja a felhasználóit.

## <a name="monitoring"></a>Figyelés
Az Azure Cloud Services monitorozást is biztosít. A Virtual Machineshoz hasonlóan a rendszer hibás fizikai kiszolgálót észlel, és újraindítja az adott kiszolgálón futó virtuális gépeket egy új gépen. Az Azure Cloud Services azonban a sikertelen virtuális gépeket és alkalmazásokat is észleli, nem csak a hardver meghibásodását. A Virtual Machinestól eltérően a szolgáltatás minden webes és feldolgozói szerepkörben rendelkezik ügynökkel, így a hibák bekövetkezésekor új virtuális gépeket és alkalmazás-példányokat indíthat el.

Az Azure Cloud Services Pásti természete más következményekkel is jár. Az egyik legfontosabb az, hogy az erre a technológiára épülő alkalmazások megfelelően fussanak, ha a webes vagy feldolgozói szerepkör-példány meghibásodik. Ennek eléréséhez az Azure Cloud Services-alkalmazásoknak nem kell megtartaniuk a saját virtuális gépei fájlrendszerében lévő állapotot. A Virtual Machines-vel létrehozott virtuális gépekkel ellentétben az Azure Cloud Services virtuális gépekre tett írások nem állandóak. Nincs semmi, mint egy Virtual Machines adatlemez. Ehelyett egy Azure Cloud Services alkalmazásnak explicit módon írnia kell az összes állapotot Azure SQL Database, blobok, táblák vagy más külső tárolóba. Az alkalmazások ily módon történő kiépítése megkönnyíti a meghibásodások méretezését és nagyobb ellenálló képességét, amely az Azure Cloud Services fontos célja.

## <a name="next-steps"></a>További lépések
* [Cloud Service-alkalmazás létrehozása a .NET-ben](cloud-services-dotnet-get-started.md) 
* [Cloud Service-alkalmazás létrehozása a Node. js-ben](cloud-services-nodejs-develop-deploy-app.md) 
* [Cloud Service-alkalmazás létrehozása PHP-ben](../cloud-services-php-create-web-role.md) 
* [Cloud Service-alkalmazás létrehozása Pythonban](cloud-services-python-ptvs.md)






