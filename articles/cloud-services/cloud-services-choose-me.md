---
title: "Az Azure számítási lehetőségek - Azure Felhőszolgáltatások |} Microsoft Docs"
description: "További tudnivalók az Azure compute beállítások és a Leírás: az App Service, Azure Felhőszolgáltatások és virtuális gépek"
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
ms.openlocfilehash: 2871a8c02db0ffc6d9033724e7c9f4a454afef8e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="should-i-choose-azure-cloud-services-or-something-else"></a>Azure Cloud Services szolgáltatásokból vagy valami más kell választanom?
Az Azure Felhőszolgáltatások a választott meg? Azure biztosít a különböző üzemeltetési modell-alkalmazások futtatására. Mindegyik számos különböző szolgáltatások. Azt, amelyiket úgy dönt, attól függ, pontosan milyen próbál tegye.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-azure-cloud-services"></a>További részletek az Azure Cloud Services csomag
Azure Cloud Services egyik példája egy [platformszolgáltatást](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Például [Azure App Service](../app-service/app-service-web-overview.md), ez a technológia célja, méretezhető, megbízható és alacsony költségű működéséhez alkalmazások támogatásához. Azonos módon, hogy az App Service tárolt virtuális gépek (VM), így túl van az Azure Cloud Services. Azonban hogy a virtuális gépek teljesebb körű vezérlése. Azure Cloud Services használó virtuális gépeken telepítheti a saját szoftvereit, és távolról elérheti őket.

![Azure Cloud Services diagramja](./media/cloud-services-choose-me/diagram.png)

Több vezérlő is jelenti, hogy az kisebb könnyű használat. Ha nem kell a további beállításokat, általában hatékonyabb és könnyebben webalkalmazás használhatja és az App Service szolgáltatás összehasonlítja a webalkalmazásokban futó Azure Cloud Services.

Azure Cloud Services szerepkörök két típusa van. Az egyetlen különbség a kettő között, a szerepkör milyen üzemelteti a virtuális gépek:

* **Webes szerepkör**: automatikusan telepíti és futtatja az alkalmazás IIS-en keresztül.

* **Feldolgozói szerepkör**: nem használják az IIS szolgáltatást, és az alkalmazás önálló futtatja.

Például egy egyszerű alkalmazást használhatja csak egyetlen webes szerepkör, egy webhely szolgál. Olyan összetettebb alkalmazást előfordulhat, hogy a webes szerepkör segítségével a felhasználók a bejövő kérelmeket kezelnek, és akkor továbbítja a feldolgozói szerepkör feldolgozásra be ezeket a kérelmeket. (Ez a kommunikáció használhatja [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) vagy [Azure Queue storage](../storage/common/storage-introduction.md).)

Mivel az előző ábrán javasol, egyetlen alkalmazás összes virtuális ugyanazt a felhőszolgáltatásban található futtassa. Az alkalmazás a egy egyetlen nyilvános IP-cím, kérések automatikus terheléselosztása a felhasználók hozzáférést kiegyensúlyozott az alkalmazás virtuális gépek között. A platform [méretezi, és telepíti a](cloud-services-how-to-scale-portal.md) a virtuális gépeket az Azure Cloud Services alkalmazásban úgy, hogy ezzel elkerülheti a hardver hibaérzékeny pontot.

Annak ellenére, hogy az alkalmazások virtuális gépek futnak, fontos megérteni, hogy az Azure Cloud Services nyújt PaaS, nem infrastruktúrák (IaaS). Íme, gondolja át az egyik módja. Az infrastruktúra-szolgáltatási, például az Azure virtuális gépek akkor először létrehozhat és konfigurálhat a az alkalmazás fut, a környezet. Ezután telepítheti az alkalmazást ebben a környezetben. Ön a felelős kezelése a világ számos módon műveleteket, mint az operációs rendszer, az egyes virtuális gépek új javított verzió telepítése. PaaS ezzel szemben, hogy a rendszer, ha a környezet már létezik. Ehhez csak az alkalmazás központi telepítése. A platform az fut, beleértve az új verziói az operációs rendszer telepítése kezelik a kulcsokat meg.

## <a name="scaling-and-management"></a>Méretezés és kezelése
Az Azure Cloud Services ne hozzon létre virtuális gépeket. Ehelyett adja meg a konfigurációs fájl, amely közli Azure hány kíváncsi, például a "három webes szerepkör instances" és "két szerepkör feldolgozópéldányok." A platform majd létrehozza azt. Úgy is dönt [milyen méretű](cloud-services-sizes-specs.md) azokat a virtuális gépek biztonsági kell lennie, de nincs explicit módon létrehozásuk magát. Ha az alkalmazásnak egy nagyobb terhelés kezelésére, további virtuális gépek esetén kérje meg, és azokat a példányokat az Azure létrehoz. Ha a terhelés csökkenése, állítsa le ezeken a példányokon, és állítsa le a számukra fizet.

Azure Cloud Services alkalmazás általában legyen elérhető a felhasználók számára egy kétlépéses folyamat keresztül. Egy fejlesztő első [az alkalmazás feltöltését](cloud-services-how-to-create-deploy-portal.md) a platform átmeneti területre. Amikor készen áll a fejlesztői ellenőrizze az alkalmazás live, az Azure portál használata felcserélni a üzemi és átmeneti. Ez [átmeneti és üzemi közötti váltás](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) teheti állásidő nélkül, amely lehetővé teszi, hogy a futó alkalmazások a felhasználók megzavarása nélkül egy új verzióra frissíteni.

## <a name="monitoring"></a>Figyelés
Azure Cloud Services figyelést is biztosít. Virtuális gépek, például azt észleli hibás fizikai kiszolgálóra, és újraindítja az adott kiszolgálón egy új gépen futó virtuális gépek. De Azure Felhőszolgáltatások is észleli a sikertelen virtuális gépeket és alkalmazásokat, nem csak a hardver meghibásodása. Ellentétben a virtuális gépek az ügynök minden webes és feldolgozói szerepkör belül van, és úgy tudja majd elindítani az új virtuális gépek és alkalmazáspéldányok hibák bekövetkezésekor.

Azure Cloud Services PaaS jellege más hatással, túl van. A legfontosabb egyike, hogy ez a technológia épülő alkalmazások kell írni működéséhez, ha az összes webes vagy feldolgozói szerepkör példánya nem sikerül. Ennek érdekében egy Azure Cloud Services alkalmazás állapotban nem szabad a saját virtuális gépek a fájlrendszerben. Ellentétben a virtuális gépek virtuális gépeket létrehozni az Azure Cloud Services virtuális gépen végzett írási műveletek nem állandó. Nem hasonlít a virtuális gépek adatlemez van. Ehelyett egy Azure Cloud Services alkalmazás kell explicit módon írható minden állapotának Azure SQL Database, blobok, táblák vagy valamilyen egyéb külső tárhelyen. Alkalmazások létrehozásához, így megkönnyíti a méretezési és ellenállóbbá hiba esetén, amelyek mindkét fontos célja, Azure Cloud Services.

## <a name="next-steps"></a>További lépések
* [Hozzon létre egy cloud service-alkalmazást a .NET](cloud-services-dotnet-get-started.md) 
* [Cloud service-alkalmazás létrehozása a node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Cloud service-alkalmazás létrehozása a PHP](../cloud-services-php-create-web-role.md) 
* [Cloud service-alkalmazás létrehozása a Python](cloud-services-python-ptvs.md)



