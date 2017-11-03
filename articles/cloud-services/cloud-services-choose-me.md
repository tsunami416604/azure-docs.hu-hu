---
title: "Az Azure számítási lehetőségek - Felhőszolgáltatások |} Microsoft Docs"
description: "További tudnivalók az Azure compute beállítások és a Leírás: az App Service, a Cloud Services és a virtuális gépek"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: e8053b74e0e4d721523f49bcbb9e33b08bb7a1dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="should-i-choose-cloud-services-or-something-else"></a>Cloud Services szolgáltatásokból vagy más kell választanom?
Az Azure Felhőszolgáltatások a választott meg? Azure biztosít a különböző üzemeltetési modell-alkalmazások futtatására. Mindegyik számos különböző szolgáltatások, ezért úgy dönt, melyiket, attól függ, pontosan milyen próbál tegye.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>További részletek a cloud services csomag
Cloud Services csomag példája [Platform,--szolgáltatás](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Például [App Service](../app-service/app-service-web-overview.md), ez a technológia célja, méretezhető, megbízható és működtetéséről olcsó ítélt alkalmazások támogatásához. Ahogy az App Service található virtuális gépek, így túl a Felhőszolgáltatások, amelyek azonban rendelkezik-e a virtuális gépek teljesebb körű vezérlése. A Cloud Service virtuális gépeken telepítheti a saját szoftvereit, és a távoli is be őket.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

Több vezérlő is jelenti, hogy az kisebb könnyű használat. Kivéve, ha a további beállításokat kell, általában hatékonyabb és könnyebben webalkalmazás használhatja és Felhőszolgáltatások az App Service Web Apps futó képest.

A felhőalapú szolgáltatás szerepkörök két típusa van. Az egyetlen különbség a kettő között, a szerepkör milyen üzemelteti a virtuális gép.

* **Webes szerepkör**  
Automatikusan telepíti és futtatja az alkalmazás IIS-en keresztül.

* **Feldolgozói szerepkör**  
Nem használják az IIS szolgáltatást, és az alkalmazás önálló futtatja.

Például egy egyszerű alkalmazást használhatja csak egyetlen webes szerepkör, egy webhely szolgál. Olyan összetettebb alkalmazást egy webes szerepkör segítségével a felhasználók a bejövő kérelmeket kezelnek, majd továbbítja ezeket a kérelmeket, a feldolgozói szerepkör feldolgozásra be. (Ez a kommunikáció használhatja [Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) vagy [Azure várólisták](../storage/common/storage-introduction.md).)

Mivel az előző ábrán javasol, egyetlen alkalmazás összes virtuális ugyanazt a felhőszolgáltatásban található futtassa. Az alkalmazás a egy egyetlen nyilvános IP-cím, kérések automatikus terheléselosztása a felhasználók hozzáférést kiegyensúlyozott az alkalmazás virtuális gépek között. A platform [méretezi, és telepíti a](cloud-services-how-to-scale.md) a virtuális gépek által egy Felhőszolgáltatások alkalmazást úgy, hogy ezzel elkerülheti a hardver hibaérzékeny pontot.

Annak ellenére, hogy az alkalmazások virtuális gépek futnak, fontos megérteni, hogy a Cloud Services nyújt PaaS, nem IaaS. Gondolja át az egyik módja: az infrastruktúra-szolgáltatási, például az Azure virtuális gépek, akkor először hozzon létre a az alkalmazás fut, a környezet konfigurálása, majd erre a környezetre az alkalmazás központi telepítése. Ön a világ számos kezeléséért műveleteket, mint az új javított verziói az operációs rendszer, az egyes virtuális gépek telepítése során. PaaS ezzel szemben, hogy a rendszer, ha a környezet már létezik. Ehhez csak az alkalmazás központi telepítése. A platform az fut, beleértve az új verziói az operációs rendszer telepítése kezelik a kulcsokat meg.

## <a name="scaling-and-management"></a>Méretezés és kezelése
A Cloud Serviceshez ne hozzon létre virtuális gépeket. Ehelyett adja meg, amely közli Azure hány kíváncsi, például a konfigurációs fájl **három webalkalmazás-szerepkörpéldányokat** és **két szerepkör feldolgozópéldányok**, és a platform létrehozza azt.  Úgy is dönt [milyen méretű](cloud-services-sizes-specs.md) azokat a virtuális gépek biztonsági kell lennie, de nincs explicit módon létrehozásuk magát. Ha az alkalmazásnak egy nagyobb terhelés kezelésére, további virtuális gépek esetén kérje meg, és azokat a példányokat az Azure létrehoz. Ha a terhelés csökkenése, állítsa le ezeken a példányokon, és állítsa le a számukra fizet.

A Cloud Services alkalmazás általában legyen elérhető a felhasználók számára egy kétlépéses folyamat keresztül. Egy fejlesztő első [az alkalmazás feltöltését](cloud-services-how-to-create-deploy.md) a platform átmeneti területre. Amikor készen áll a fejlesztői ellenőrizze az alkalmazás live, az Azure portál használata felcserélni a üzemi és átmeneti. Ez [átmeneti és üzemi közötti váltás](cloud-services-nodejs-stage-application.md) teheti állásidő nélkül, amely lehetővé teszi, hogy a futó alkalmazások a felhasználók megzavarása nélkül egy új verzióra frissíteni.

## <a name="monitoring"></a>Figyelés
Cloud Services csomag figyelést is biztosít. Például Azure virtuális gépek, azt észleli a hibás fizikai kiszolgálóra, és újraindul az új számítógép az adott kiszolgálón futó virtuális gépek. De a Felhőszolgáltatások is észleli a sikertelen virtuális gépeket és alkalmazásokat, nem csak a hardver meghibásodása. Ellentétben a virtuális gépek az ügynök minden webes és feldolgozói szerepkör belül van, és úgy tudja majd elindítani az új virtuális gépek és alkalmazáspéldányok hibák bekövetkezésekor.

Cloud Services PaaS jellege más hatással, túl van. A legfontosabb egyike, hogy ez a technológia épülő alkalmazások kell írni működéséhez, ha az összes webes vagy feldolgozói szerepkör példánya nem sikerül. Ennek érdekében a Cloud Services alkalmazás állapotban nem szabad a saját virtuális gépek a fájlrendszerben. A virtuális gépek létrehozott Azure virtuális gépekkel, eltérően felhőalapú szolgáltatások virtuális gépeken végrehajtott írási műveletek nem állandó; nem hasonlít a virtuális gépek adatlemez van. Ehelyett a Felhőszolgáltatások alkalmazások kell explicit módon írható minden állapotának SQL-adatbázis, blobok, táblák vagy valamilyen egyéb külső tárhelyen. Alkalmazások létrehozásához, így azokat könnyebben méretezési és teszi ellenállóbbá hibákhoz, mindkét fontos célja a Felhőszolgáltatások.

## <a name="next-steps"></a>Következő lépések
[Hozzon létre egy cloud service-alkalmazást a .NET](cloud-services-dotnet-get-started.md)  
[Cloud service-alkalmazás létrehozása a node.js](cloud-services-nodejs-develop-deploy-app.md)  
[Cloud service-alkalmazás létrehozása a PHP](../cloud-services-php-create-web-role.md)  
[Cloud service-alkalmazás létrehozása a Python](cloud-services-python-ptvs.md)

