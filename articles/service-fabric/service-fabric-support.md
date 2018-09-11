---
title: További tudnivalók az Azure Service Fabric támogatási lehetőségek |} A Microsoft Docs
description: Az Azure Service Fabric-fürt podporované és fájl mutató hivatkozások a támogatási jegy megjelenítése
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: e22a2431ecae68278276b069bc199cfea60cd413
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347358"
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

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric előzetes verziókat – üzemi használatra nem támogatott

Időről időre kiadunk szeretnénk visszajelzését, jelentős funkciókat, amelyeket mintaként-verziókban. Ezen előzetes verziói csak tesztelési célokra használandó. Az éles fürt mindig futnia kell egy támogatott, stabil, Service Fabric-verzió. Előzetes verzió mindig a fő- és alverzió verziószáma 255 kezdődik. Például ha egy Service Fabric 255.255.5703.949 verzió, a végleges verziót, hogy csak tesztelési fürtökhöz használható és előzetes verzióban érhető el. Ezen előzetes kiadások is bejelentjük a a [Service Fabric-csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric) és definíciókhoz az elérhető funkciókat használhatja.
Nincs az előzetes verziók fizetős támogatási lehetőség. A felsorolt lehetőségek egyikével [jelentés az Azure Service Fabric problémák](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) felteheti kérdéseit, és visszajelzést.

## <a name="next-steps"></a>További lépések

[A Service Fabric támogatott verziói](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples