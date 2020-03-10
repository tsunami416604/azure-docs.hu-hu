---
title: További tudnivalók az Azure Service Fabric támogatási lehetőségeiről
description: Az Azure Service Fabric-fürt támogatott verziói és a fájlokra vonatkozó támogatási jegyekre mutató hivatkozások
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389850"
---
# <a name="azure-service-fabric-support-options"></a>Az Azure Service Fabric támogatási lehetőségei

Annak érdekében, hogy a megfelelő támogatást nyújtson a Service Fabric-fürtökhöz, amelyeken az alkalmazás munkaterhelése betöltődik, különböző beállításokat állítottunk be. Attól függően, hogy milyen szintű támogatásra van szükség, és a probléma súlyosságát, válassza ki a megfelelő beállításokat. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Termelési problémák jelentése vagy az Azure-ra vonatkozó fizetett támogatás kérése

Az Azure-ban üzembe helyezett Service Fabric-fürtre vonatkozó jelentéskészítési problémákért nyissa meg a [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) vagy a [Microsoft támogatási portálon](https://support.microsoft.com/oas/default.aspx?prid=16146)a támogatási jegyet.

További információk:
 
- A [Microsoft Azure-támogatásának támogatása](https://azure.microsoft.com/support/plans/?b=16.44).
- A [Microsoft Premier szintű támogatása](https://support.microsoft.com/en-us/premier).

> [!Note]
> A bronz megbízhatósági szinten vagy egyetlen csomóponttal rendelkező fürtön futó fürtök csak a tesztelési feladatok futtatását teszik lehetővé. Ha a bronz vagy egy csomópontos fürtön futó fürttel kapcsolatos problémákat tapasztal, a Microsoft támogatási csapata segítséget nyújt a probléma megoldásában, de nem hajt végre kiváltó okokat. További részletekért tekintse meg a [fürt megbízhatósági jellemzőit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) .
>
> Ha további információt szeretne arról, hogy mi szükséges az éles használatra kész fürthöz, tekintse meg az [üzemi készültségi ellenőrzőlistát](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Termelési problémák jelentése vagy fizetett támogatás kérése önálló Service Fabric-fürtökhöz

A helyszíni vagy más felhőkben üzembe helyezett Service Fabric-fürtről történő jelentéskészítéssel kapcsolatos problémákért nyisson meg egy jegyet a [Microsoft támogatási portálján](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)a professzionális támogatáshoz.

További információk:

- [A Microsoft szakmai támogatása a helyszínen](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- A [Microsoft Premier szintű támogatása](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric-problémák jelentése

Egy GitHub-tárházat állítottunk be a Service Fabric problémák jelentéséhez.  A következő fórumokon is aktívan figyelik.

### <a name="github-repo"></a>GitHub-tárház 

Azure Service Fabric-problémák jelentése a [Service-Fabric-problémák git-](https://github.com/Azure/service-fabric-issues)tárházban. Ez a tárház az Azure-Service Fabricokkal kapcsolatos problémák jelentéskészítésére és nyomon követésére, valamint a kisméretű szolgáltatásokra vonatkozó kérelmek készítésére szolgál. Ne **használja az élő helyekkel kapcsolatos problémák jelentését**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow és MSDN-fórumok

A [StackOverflow Service Fabric címkéje][stackoverflow] és az [MSDN-Service Fabric fórum][msdn-forum] a legjobb megoldás a platform működésével és bizonyos feladatok végrehajtásával kapcsolatos kérdések feltevésére.

### <a name="azure-feedback-forum"></a>Azure visszajelzési fórum

A [Service Fabric Azure visszajelzési fóruma][uservoice-forum] a legjobb hely a nagy funkciós ötletek elküldéséhez a termékhez, ahogy a legnépszerűbb kérések áttekintése a közép-és hosszú távú tervezés részét képezi. Javasoljuk, hogy a Közösségen belüli javaslataihoz támogassa a támogatását.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>A Service Fabric előzetes verziójának verziója – nem támogatott éles használatra

Időről időre olyan verziókat szabadítunk fel, amelyek olyan jelentős funkciókkal rendelkeznek, melyeket előzetes verzióként adnak meg. Ezek az előzetes verziók csak tesztelési célokra használhatók. Az üzemi fürtnek mindig támogatott, stabil Service Fabric verziót kell futtatnia. Az előzetes verzió verziója mindig a fő és alverziószám 255-as számú. Ha például megjelenik egy Service Fabric 255.255.5703.949-verzió, akkor a kiadási verzió csak tesztelési fürtökben használható, és előzetes verzióban érhető el. Ezek az előzetes verziók a [Service Fabric csapat blogjában](https://blogs.msdn.microsoft.com/azureservicefabric) is bejelentve lesznek, és a szolgáltatással kapcsolatos részleteket is tartalmaznak.
Ezekhez az előzetes verzióhoz nem biztosítunk fizetős támogatási lehetőséget. A kérdésekkel kapcsolatos kérdések és visszajelzések megadásához használja az [Azure-Service Fabrici problémák című](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) részben felsorolt lehetőségek egyikét.

## <a name="next-steps"></a>Következő lépések

[Támogatott Service Fabric verziók](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
