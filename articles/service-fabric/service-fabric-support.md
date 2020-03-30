---
title: További információ az Azure Service Fabric támogatási lehetőségeiről
description: Az Azure Service Fabric fürtverziói támogatottak, és fájltámogatási jegyekre mutató hivatkozások
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 7494f0072f27f2c9b00db7070f19dfc05627eacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282093"
---
# <a name="azure-service-fabric-support-options"></a>Az Azure Service Fabric támogatási lehetőségei

A megfelelő támogatást a Service Fabric-fürtök, amelyek az alkalmazás munkabetölti az alkalmazás terhelése, már beállított különböző lehetőségeket az Ön számára. A szükséges támogatás szintjétől és a probléma súlyosságától függően kiválaszthatja a megfelelő lehetőségeket. 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Termelési problémák jelentése vagy fizetős támogatás kérése az Azure-hoz

Az Azure-ban üzembe helyezett Service Fabric-fürttel kapcsolatos problémák jelentéséhez nyisson meg egy támogatási jegyet [az Azure Portalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) vagy a [Microsoft támogatási portálján.](https://support.microsoft.com/oas/default.aspx?prid=16146)

További információk:
 
- [Támogatás a Microsoft for Azure-tól](https://azure.microsoft.com/support/plans/?b=16.44).
- [A Microsoft premier támogatása](https://support.microsoft.com/en-us/premier).

> [!Note]
> A bronz megbízhatósági szinten vagy egycsomópontos fürtön futó fürtök lehetővé teszik, hogy csak tesztszámítási feladatokat futtasson. Ha a bronzmegbízhatóságon vagy az egycsomópontos fürtön futó fürttel kapcsolatos problémákat tapasztal, a Microsoft támogatási csapata segítséget nyújt a probléma enyhítésében, de nem hajt végre kiváltó ok-elemzést. További részletekért olvassa el [a fürt megbízhatósági jellemzőit.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)
>
> A termelésre kész fürthöz szükséges információkról a [termelésre való alkalmassági ellenőrzőlistán](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)olvashat bővebben.

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Termelési problémák jelentése vagy fizetős támogatás kérése önálló Service Fabric-fürtökhöz

A helyszíni vagy más felhőkön üzembe helyezett Service Fabric-fürtön felmerülő problémák jelentéséhez nyisson meg egy jegyet a [Microsoft támogatási portálján.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

További információk:

- [Szakmai támogatás a Microsofttól a helyszíni használatra.](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [A Microsoft premier támogatása](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Az Azure Service Fabric-problémák jelentése

Létrehoztunk egy GitHub-repo-t a Service Fabric-problémák jelentéséhez.  Aktívan figyelemmel kísérjük a következő fórumokat is.

### <a name="github-repo"></a>GitHub-tárcsa 

Jelentse az Azure Service Fabric-problémákat a [Service-Fabric-problémák git tárhéten.](https://github.com/Azure/service-fabric-issues) Ez a tártár az Azure Service Fabric szolgáltatással kapcsolatos problémák jelentésére és nyomon követésére, valamint kis funkciókérések készítésére szolgál. **Ne használja ezt az élő webhelyekkel kapcsolatos problémák jelentésére.**

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow és MSDN fórumok

A [Service Fabric-címkén StackOverflow][stackoverflow] és a [Service Fabric fórum MSDN][msdn-forum] a legjobb, ha kérdéseket feltenni a platform működését, és hogyan lehet bizonyos feladatok elvégzéséhez vele.

### <a name="azure-feedback-forum"></a>Azure-visszajelzési fórum

Az [Azure Feedback Forum for Service Fabric][uservoice-forum] a legjobb hely a termékhez szükséges nagy funkcióötletek elküldéséhez, mivel a legnépszerűbb kérések áttekintése közép- és hosszú távú tervezésünk részét képezi. Javasoljuk, hogy a közösségen belül támogasson javaslatokat.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric preview verziók - nem támogatott éles használatra

Időről időre olyan verziókat adunk ki, amelyek olyan jelentős funkciókkal rendelkeznek, amelyekről visszajelzést szeretnénk kapni, amelyek előnézetként jelennek meg. Ezeket az előzetes verziókat csak tesztelési célokra szabad használni. Az éles fürt mindig egy támogatott, stabil Service Fabric-verziót kell futtatnia. Az előzetes verzió mindig 255-ös fő- és alverziószámmal kezdődik. Ha például egy Service Fabric 255.255.5703.949-es verziót lát, akkor a kiadási verzió csak tesztfürtökben használható, és előzetes verzióban érhető el. Ezek az előzetes kiadások is bejelentette a [Service Fabric csapat blog,](https://blogs.msdn.microsoft.com/azureservicefabric) és részleteket a funkciók at tartalmazza.
Ezekhez az előzetes verziókhoz nincs fizetős támogatási lehetőség. Használja az Azure Service [Fabric-problémák jelentésével](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues) kapcsolatos problémák egyikét, és kérjen visszajelzést.

## <a name="next-steps"></a>További lépések

[A Service Fabric támogatott verziói](service-fabric-versions.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: https://aka.ms/servicefabricdocs
[sample-repos]: https://aka.ms/servicefabricsamples
