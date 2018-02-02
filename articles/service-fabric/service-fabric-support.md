---
title: "Azure Service Fabric támogatási lehetőségek megismerése |} Microsoft Docs"
description: "Azure Service Fabric-fürt verziók támogatott és a fájl mutató hivatkozásokat tartalmaz támogatási jegy megjelenítése"
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/26/2018
ms.author: pkc
ms.openlocfilehash: 970f1ff59560beaf898f22eb4e701088a6d5902a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-service-fabric-support-options"></a>Az Azure Service Fabric támogatási lehetőségek

A Service Fabric-fürtök az alkalmazás a munkahelyi betölti a futó megfelelő támogatása, hogy rendelkezik beállítjuk különböző lehetőségeket. Attól függően, a szükséges támogatást és a probléma súlyossága, get válassza ki a megfelelő beállításokat. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Éles problémák jelentése, vagy kérje meg az Azure-fizetős támogatási

A telepített Azure Service Fabric-fürt a problémák jelentési, nyissa meg a támogatási jegy [Azure-portál](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) vagy [a Microsoft támogatási portal](http://support.microsoft.com/oas/default.aspx?prid=16146).

További információ:
 
- [A Microsoft Azure támogatási](https://azure.microsoft.com/en-us/support/plans/?b=16.44).
- [Microsoft Premier szintű támogatás](https://support.microsoft.com/en-us/premier).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Éles problémák jelentése, vagy kérje meg a Service Fabric-fürtök különálló fizetős támogatási

A Service Fabric-fürt kapcsolatos problémákat a helyi telepített jelentéskészítésre, illetve a többi felhőből, nyissa meg a jegyet a professzionális támogatás a [a Microsoft támogatási portal](http://support.microsoft.com/oas/default.aspx?prid=16146).

További információ:

- [A helyszíni Microsoft professzionális támogatás](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier szintű támogatás](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>A jelentés Azure Service Fabric-problémák 
Jelenleg a Service Fabric problémák jelentési egy GitHub-tárház beállítása.  Azt is aktívan figyeli a következő fórumokban.

### <a name="github-repo"></a>GitHub-tárház 
Azure Service Fabric problémák jelentése a [szolgáltatás-háló-problémák git-tárház](https://github.com/Azure/service-fabric-issues). A tárház jelentéskészítési és nyomkövetési problémákat, az Azure Service Fabric, adja meg a megfelelő kis funkciókra vonatkozó kérések számára készült. **Ne használja a jelentés élő hely problémák**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow és MSDN-fórumok
A [StackOverflow a Service Fabric címke] [ stackoverflow] és a [MSDN fórum Service Fabric] [ msdn-forum] is ajánlott az kapcsolatos kérdéseket feltenni használt Platform működik, és hogyan lehet vele bizonyos feladatok elvégzéséhez.

### <a name="azure-feedback-forum"></a>Az Azure visszajelzési fórumon
A [Azure visszajelzési fórumon a Service Fabric] [ uservoice-forum] a legjobb hely küldésére, a termék, a legnépszerűbb kérelmek tanulmányozzák rendelkezik nagy szolgáltatás ötleteket a közepes és hosszú távú tervezési részét képezik. Javasoljuk, hogy rally Közösségen belüli javaslatait támogatása.


## <a name="supported-service-fabric-versions"></a>A Service Fabric-verziója támogatott.

Győződjön meg arról, hogy a fürt mindig fut a Service Fabric támogatott verziója. Amennyiben, és amikor azt a kiadás a Service Fabric új verziójának bejelentése, az előző verzió legalább attól az időponttól 60 nap után végéhez van megjelölve. Az új kiadásokat történik bejelentés [a Service Fabric csapat blogjában](https://blogs.msdn.microsoft.com/azureservicefabric/).

Tekintse meg a következő dokumentumok részletes információt a Service Fabric támogatott verzióját futtató fürtre tartani.

- [Egy Azure fürt frissítési Service Fabric-verzió](service-fabric-cluster-upgrade.md)
- [Service Fabric verziója a különálló windows server-fürt frissítése](service-fabric-cluster-upgrade-windows-server.md)
 
Az alábbiakban a Service Fabric által támogatott verziók listáját és azok támogatás záró dátumát.

| **A fürt Service Fabric-futtatókörnyezet** | **Közvetlenül a fürt verzióra frissíthető** |**Kompatibilis SDK / NuGet csomag verziója** | **Támogatási dátum vége** |
| --- | --- |--- | --- |
| Fürt összes 5.3.121 korábbi verziók | 5.1.158* |Kisebb vagy egyenlő, mint 2.3 verziója |2017. január 20. |
| 5.3.* | 5.1.158.* |Kisebb vagy egyenlő, mint 2.3 verziója |2017. február 24. |
| 5.4.* | 5.1.158.* |Kisebb vagy egyenlő, mint 2.4 verziója |Előfordulhat, hogy 10,2017       |
| 5.5.* | 5.4.164.* |Kisebb vagy egyenlő, mint 2.5-ös verzió |Augusztus 10,2017    |
| 5.6.* | 5.4.164.* |Kisebb vagy egyenlő, mint 2.6-os verziója |Október 13,2017   |
| 5.7.* | 5.4.164.* |Kisebb vagy egyenlő, mint 2.7-es verzió |December 15,2017  |
| 6.0.* | 5.6.205.* |Kisebb vagy egyenlő, mint 2.8-as verziója |30,2018. március     | 
| 6.1.* | 5.7.221.* |Kisebb vagy egyenlő, mint a 3.0-s verzió |Aktuális verzióra, és ezért nincs befejezési dátum |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric előzetes verzió – üzemi használatra nem támogatott.
Időről időre azt verzióira, amelyek azt szeretnénk, ha visszajelzést, amelyeket mintaként jelentős funkciót. Ezen előzetes verziói csak tesztelési célra használható. Az éles fürt mindig futnia kell egy támogatott, stabil, a Service Fabric-verzió. Előzetes verzióval mindig a fő- és alverzió verziószámát 255 kezdődik. Például a Service Fabric 255.255.5703.949 verzió jelenik meg, ha a verzió csak a tesztfürtökön lévő használni kívánt tartalmaz, és jelenleg előzetes verzióban érhető. Ezen előzetes kiadások is nem jelentik be a [Service Fabric csapat blogja](https://blogs.msdn.microsoft.com/azureservicefabric) és részletek rendelkezzen a szolgáltatásai.

Nincs a preview kiadásokban fizetős támogatási lehetőség. A felsorolt lehetőségek valamelyikével [jelentés Azure Service Fabric problémák](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) felteheti kérdéseit és visszajelzés küldése.

## <a name="next-steps"></a>További lépések

- [Service fabric-verzió egy Azure fürt frissítése](service-fabric-cluster-upgrade.md)
- [Service Fabric verziója a különálló windows server-fürt frissítése](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
