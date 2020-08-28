---
title: Rövid útmutató – a Piggy-mérőszámok példás alkalmazásának bemutatása – Azure Spring Cloud
description: Az Azure Spring Cloud üzembe helyezéséhez használt Piggy metrikai minta alkalmazás leírása.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046782"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>A Piggy mérőszámok minta alkalmazásának bemutatása

Ebben a rövid útmutatóban egy "Piggy Metrics" nevű személyes pénzügyi mintát használunk, amely bemutatja, hogyan helyezhet üzembe egy alkalmazást az Azure Spring Cloud Service-ben. A Piggy-metrikák a szolgáltatás architektúrájának mintáját mutatják be, és kiemelik a szolgáltatások részletezését. Megtudhatja, hogyan helyezi üzembe az Azure-ban hatékony Azure Spring Cloud-funkciókkal, beleértve a Service Discovery, a konfigurációs kiszolgáló, a naplók, a metrikák és az elosztott nyomkövetést.

Az Azure Spring Cloud telepítési példáinak követéséhez csak a forráskód helyét kell megadnia, amelyet igény szerint kell biztosítani.

## <a name="functional-services"></a>Funkcionális szolgáltatások
A Piggy-metrikák három alapvető szolgáltatásból állnak. Mindegyik az üzleti tartományok által rendezett, egymástól függetlenül telepíthető alkalmazás.

* **Szolgáltatásfiók (központilag telepítendő)**: általános felhasználói bemeneti logikát és érvényesítést tartalmaz: az adatbevitelek/kiadások elemei, megtakarítások és Fiókbeállítások.
* **Statisztikai szolgáltatás (ebben a rövid útmutatóban nem használható)**: számításokat végez a főbb statisztikai paramétereken, és rögzíti az idősorozatokat az egyes fiókokhoz. A Datapoint az alappénznemre és az időszakra normalizált értékeket tartalmaz. Ezek az adatforgalom a pénzforgalmi dinamika a fiók élettartamában való nyomon követésére szolgálnak.
* **Értesítési szolgáltatás (ebben a rövid útmutatóban nem használható): a**felhasználók kapcsolattartási adatait és értesítési beállításait, például az emlékeztetőket és a biztonsági mentés gyakoriságát tárolja. Az ütemezett feldolgozó más szolgáltatásokból gyűjti a szükséges információkat, és e-mail-üzeneteket küld az előfizetett ügyfeleknek.

## <a name="infrastructure-services"></a>Infrastruktúra-szolgáltatások
Az elosztott rendszerekben számos gyakori minta van, amelyek segítenek az alapvető szolgáltatások működésében. Az Azure Spring Cloud hatékony eszközöket biztosít a Spring boot-alkalmazások működésének növeléséhez a minták megvalósításához: 

* **Konfigurációs szolgáltatás (az Azure Spring Cloud által üzemeltetett)**: az Azure Spring Cloud config egy horizontálisan méretezhető központi konfigurációs szolgáltatás az elosztott rendszerek számára. Egy olyan csatlakoztatható tárházat használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót.
* **Service Discovery (az Azure Spring Cloud által üzemeltetett)**: lehetővé teszi a hálózati telephelyek automatikus észlelését a szolgáltatási példányok számára, ami dinamikusan hozzárendelt címeket tartalmazhat az automatikus skálázás, a hibák és a frissítések miatt.
* **Hitelesítési szolgáltatás (telepítendő)** Az engedélyezési felelősségeket a rendszer teljesen kibontja egy különálló kiszolgálóra, amely OAuth2 jogkivonatokat biztosít a háttérbeli erőforrás-szolgáltatásokhoz. Az Auth-kiszolgáló a felhasználói hitelesítést és a számítógép és a gép közötti kommunikációt is biztosítja a peremhálózaton belül.
* **API Gateway (telepítendő)**: a három alapvető szolgáltatás külső API-t tesz elérhetővé az ügyfél számára. A valós rendszerekben a függvények száma nagyon gyorsan, a rendszer bonyolultságával növelhető. Egy összetett weblap megjelenítéséhez több száz szolgáltatás is kapcsolódhat. Az API-átjáró a rendszer egyetlen belépési pontja, amely a kérelmek kezelésére szolgál, és továbbítja azokat a megfelelő háttér-szolgáltatáshoz, vagy több háttér-szolgáltatást hív meg, így összesítve az eredményeket. 

## <a name="sample-usage-of-piggy-metrics"></a>A Piggy-metrikák használati mintája
A teljes körű megvalósítás részleteiért lásd: a [Piggy-metrikák](https://github.com/Azure-Samples/piggymetrics). A minták szükség szerint hivatkoznak a forráskódra.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Azure Spring Cloud-példány kiépítése](spring-cloud-quickstart-provision-service-instance.md)
