---
title: További tudnivalók az Azure Service Fabric támogatási lehetőségeiről
description: Az Azure Service Fabric-fürt támogatott verziói és a fájlokra vonatkozó támogatási jegyekre mutató hivatkozások
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: ae49a59c2629d9f9461d298ada555d314c0c9f22
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256968"
---
# <a name="azure-service-fabric-support-options"></a>Az Azure Service Fabric támogatási lehetőségei

Annak érdekében, hogy a megfelelő támogatást nyújtson a Service Fabric-fürtökhöz, amelyeken az alkalmazás munkaterhelése betöltődik, különböző beállításokat állítottunk be. Attól függően, hogy milyen szintű támogatásra van szükség, és a probléma súlyosságát, válassza ki a megfelelő beállításokat. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Termelési problémák jelentése vagy az Azure-ra vonatkozó fizetett támogatás kérése

Az Azure-ban üzembe helyezett Service Fabric-fürtre vonatkozó jelentéskészítési problémákért nyissa meg a [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) vagy a [Microsoft támogatási portálon](https://support.microsoft.com/oas/default.aspx?prid=16146)a támogatási jegyet.

További információk:
 
- A [Microsoft Azure-támogatásának támogatása](https://azure.microsoft.com/support/plans/?b=16.44).
- A [Microsoft Premier szintű támogatása](https://support.microsoft.com/en-us/premier).

> [!Note]
> A bronz megbízhatósági szinten vagy egyetlen csomóponttal rendelkező fürtön futó fürtök csak a tesztelési feladatok futtatását teszik lehetővé. Ha a bronz vagy egy csomópontos fürtön futó fürttel kapcsolatos problémákat tapasztal, a Microsoft támogatási csapata segítséget nyújt a probléma megoldásában, de nem hajt végre kiváltó okokat. További részletekért tekintse meg a [fürt megbízhatósági jellemzőit](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) .
>
> Ha további információt szeretne arról, hogy mi szükséges az éles használatra kész fürthöz, tekintse meg az [üzemi készültségi ellenőrzőlistát](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Termelési problémák jelentése vagy fizetett támogatás kérése önálló Service Fabric-fürtökhöz

A helyszíni vagy más felhőkben üzembe helyezett Service Fabric-fürtről történő jelentéskészítéssel kapcsolatos problémákért nyisson meg egy jegyet a [Microsoft támogatási portálján](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)a professzionális támogatáshoz.

További információk:

- [A Microsoft szakmai támogatása a helyszínen](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- A [Microsoft Premier szintű támogatása](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric-problémák jelentése

Egy GitHub-tárházat állítottunk be a Service Fabric problémák jelentéséhez.  A következő fórumokon is aktívan figyelik.

### <a name="github-repo"></a>GitHub-adattár 

Azure Service Fabric-problémák jelentése a [Service-Fabric-problémák git-](https://github.com/Azure/service-fabric-issues)tárházban. Ez a tárház az Azure-Service Fabricokkal kapcsolatos problémák jelentéskészítésére és nyomon követésére, valamint a kisméretű szolgáltatásokra vonatkozó kérelmek készítésére szolgál. Ne **használja az élő helyekkel kapcsolatos problémák jelentését**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow és MSDN-fórumok

A [StackOverflow Service Fabric címkéje][stackoverflow] és az [MSDN-Service Fabric fórum][msdn-forum] a legjobb megoldás a platform működésével és bizonyos feladatok végrehajtásával kapcsolatos kérdések feltevésére.

### <a name="azure-feedback-forum"></a>Azure visszajelzési fórum

A [Service Fabric Azure visszajelzési fóruma][uservoice-forum] a legjobb hely a nagy funkciós ötletek elküldéséhez a termékhez, ahogy a legnépszerűbb kérések áttekintése a közép-és hosszú távú tervezés részét képezi. Javasoljuk, hogy a Közösségen belüli javaslataihoz támogassa a támogatását.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>A Service Fabric előzetes verziójának verziója – nem támogatott éles használatra

Időről időre olyan verziókat szabadítunk fel, amelyek olyan jelentős funkciókkal rendelkeznek, melyeket előzetes verzióként adnak meg. Ezek az előzetes verziók csak tesztelési célokra használhatók. Az üzemi fürtnek mindig támogatott, stabil Service Fabric verziót kell futtatnia. Az előzetes verzió verziója mindig a fő és alverziószám 255-as számú. Ha például megjelenik egy Service Fabric 255.255.5703.949-verzió, akkor a kiadási verzió csak tesztelési fürtökben használható, és előzetes verzióban érhető el. Ezek az előzetes verziók a [Service Fabric csapat blogjában](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) is bejelentve lesznek, és a szolgáltatással kapcsolatos részleteket is tartalmaznak.
Ezekhez az előzetes verzióhoz nem biztosítunk fizetős támogatási lehetőséget. A kérdésekkel kapcsolatos kérdések és visszajelzések megadásához használja az [Azure-Service Fabrici problémák című](#report-azure-service-fabric-issues) részben felsorolt lehetőségek egyikét.

## <a name="next-steps"></a>Következő lépések

[Támogatott Service Fabric verziók](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform
