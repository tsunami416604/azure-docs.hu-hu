---
title: További tudnivalók az Azure Service Fabric támogatási lehetőségek |} A Microsoft Docs
description: Az Azure Service Fabric-fürt podporované és fájl mutató hivatkozások a támogatási jegy megjelenítése
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/13/2018
ms.author: pkc
ms.openlocfilehash: 596e71be75453874492aac15d91cb6153c2076f5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112890"
---
# <a name="azure-service-fabric-support-options"></a>Az Azure Service Fabric támogatási lehetőségei

Hogy megfelelő támogatást biztosít a Service Fabric-fürtök, amelyek az alkalmazás vállalatokra futtat az, hogy beállította különböző lehetőségei. A szükséges támogatás szintjét és a probléma súlyosságától függően juthat el a válassza ki a megfelelő beállításokat. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Éles problémák jelentése és az Azure-ban fizetős támogatási kérelem

A Service Fabric-fürtben üzembe helyezett Azure-beli problémák jelentési, nyisson meg egy támogatási jegyet [az Azure Portalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) vagy [a Microsoft támogatási portálján](http://support.microsoft.com/oas/default.aspx?prid=16146).

További információk:
 
- [Az Azure-hoz a Microsoft támogatási](https://azure.microsoft.com/support/plans/?b=16.44).
- [A Microsoft Premier szintű támogatás](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Éles problémák jelentése és az önálló Service Fabric-fürtök fizetős támogatási kérelem

Jelentéskészítési problémákat a Service Fabric-fürtben üzembe helyezett helyszíni vagy más felhőkben, nyisson meg egy professzionális támogatási jegyet a [a Microsoft támogatási portálján](http://support.microsoft.com/oas/default.aspx?prid=16146).

További információk:

- [A helyszíni Microsoft professzionális támogatási](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [A Microsoft Premier szintű támogatás](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>A jelentés az Azure Service Fabric-problémák 
Nem kell beállítani egy GitHub-adattárat a reporting Service Fabric-problémák.  Azt is aktívan figyeli a következő fórumokban.

### <a name="github-repo"></a>GitHub-adattár 
Azure Service Fabric hibák jelentést [Service-Fabric-hibák git-tárház](https://github.com/Azure/service-fabric-issues). Ebben a tárházban a jelentéskészítési és nyomkövetési problémák az Azure Service Fabric és a kis funkciókérések végzett szól. **Ne használja ezt a webhelyek működés közbeni problémákat**.

### <a name="stackoverflow-and-msdn-forums"></a>Stackoverflow-n és az MSDN-fórumok
A [Service Fabric-címke a StackOverflow-n] [ stackoverflow] és a [Service Fabric-fórum az MSDN] [ msdn-forum] használt kérdések feltevéséhez a kapcsolatos legjobb Platform működik, és hogyan, előfordulhat, hogy vele bizonyos feladatok elvégzését.

### <a name="azure-feedback-forum"></a>Azure Visszajelzési fórum
A [Azure Visszajelzési fórum a Service fabric] [ uservoice-forum] a legjobb hely rendelkezik a termék ellenőrizzük, hogy a legnépszerűbb kérelmek nagy ötletek részei a közepes, hosszú távú tervezési és elküldéséhez. Javasoljuk, hogy rally javaslatait a Közösségben támogatása.


## <a name="supported-service-fabric-versions"></a>A Service Fabric verziója támogatott.

Győződjön meg arról, hogy a fürt minden esetben támogatott a Service Fabric verziót futtat. És hogy jelentjük, hogy a Service Fabric egy új verziójának, az előző verzió után 60 napon, hogy legalább támogatásuk van megjelölve. Az új kiadásokkal már bejelentettünk [a Service Fabric blogján](https://blogs.msdn.microsoft.com/azureservicefabric/).

Tekintse meg a következő dokumentumokban talál útmutatást, hogy a fürt Service Fabric támogatott verziót futtat.

- [Az Azure-fürtön a Service Fabric-verzió frissítése ](service-fabric-cluster-upgrade.md)
- [A Service Fabric verziója egy önálló windows server-fürt frissítése ](service-fabric-cluster-upgrade-windows-server.md)
 
Íme a Service Fabric-verziók támogatott listája és azok támogatás záró dátumát.

| **A fürt Service Fabric-futtatókörnyezet** | **Közvetlenül a fürt verziója frissíthető** |**Kompatibilis SDK / NuGet-csomag verziója** | **Támogatásának vége** |
| --- | --- |--- | --- |
| Fürt összes 5.3.121 korábbi verziók | 5.1.158* |Kisebb vagy egyenlő 2.3-as verzió |2017. január 20. |
| 5.3.* | 5.1.158.* |Kisebb vagy egyenlő 2.3-as verzió |2017. február 24. |
| 5.4.* | 5.1.158.* |Kisebb vagy egyenlő 2.4-es verzió |Előfordulhat, hogy 10,2017       |
| 5.5.* | 5.4.164.* |Kisebb vagy egyenlő 2.5-ös verzió |Augusztus 10,2017    |
| 5.6.* | 5.4.164.* |Kisebb vagy egyenlő 2.6-os verzió |Október 13,2017   |
| 5.7.* | 5.4.164.* |Kisebb vagy egyenlő 2.7-es verzió |December 15,2017  |
| 6.0.* | 5.6.205.* |Kisebb vagy egyenlő 2.8-as verziója |30,2018. március     | 
| 6.1.* | 5.7.221.* |Kisebb vagy egyenlő, mint a 3.0-s verzió |Július 15,2018      |
| 6.2. * | 6.0.232.* |Kisebb vagy egyenlő, mint 3.1-es verzióját |Szeptember 15,2018 |
| 6.3. * | 6.1.480.* |Kisebb vagy egyenlő 3.2-es verziója |Aktuális verzióját, és ezért nincs befejezési dátum |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric előzetes verzió – üzemi használatra nem támogatott.
Időről időre kiadunk szeretnénk visszajelzését, jelentős funkciókat, amelyeket mintaként-verziókban. Ezen előzetes verziói csak tesztelési célokra használandó. Az éles fürt mindig futnia kell egy támogatott, stabil, Service Fabric-verzió. Előzetes verzió mindig a fő- és alverzió verziószáma 255 kezdődik. Például ha egy Service Fabric 255.255.5703.949 verzió, a végleges verziót, hogy csak tesztelési fürtökhöz használható és előzetes verzióban érhető el. Ezen előzetes kiadások is bejelentjük a a [Service Fabric-csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric) és definíciókhoz az elérhető funkciókat használhatja.

Nincs az előzetes verziók fizetős támogatási lehetőség. A felsorolt lehetőségek egyikével [jelentés az Azure Service Fabric problémák](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) felteheti kérdéseit, és visszajelzést.

## <a name="next-steps"></a>További lépések

- [Frissítés a service fabric verziója az Azure-fürtön ](service-fabric-cluster-upgrade.md)
- [A Service Fabric verziója egy önálló windows server-fürt frissítése ](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
