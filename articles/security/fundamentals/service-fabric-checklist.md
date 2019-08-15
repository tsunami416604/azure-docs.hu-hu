---
title: Az Azure Service Fabric biztonsági ellenőrzőlistája | Microsoft Docs
description: Ez a cikk az Azure Fabric biztonsági biztonságára vonatkozó ellenőrzőlista-készletet tartalmaz.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927724"
---
# <a name="azure-service-fabric-security-checklist"></a>Azure Service Fabric biztonsági ellenőrzőlista
Ez a cikk egy könnyen használható feladatlistát tartalmaz, amely segít az Azure Service Fabric-környezet biztonságossá tételében.

## <a name="introduction"></a>Bevezetés
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

## <a name="checklist"></a>Ellenőrzőlista
A következő ellenőrzőlista segít meggyőződni arról, hogy a biztonságos Azure Service Fabric megoldás kezelésében és konfigurálásában nem merült fel semmilyen fontos probléma.


|Ellenőrzőlista kategóriája| Leírás |
| ------------ | -------- |
|[Szerepköralapú hozzáférés-vezérlés (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>A hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző felhasználói csoportok esetében korlátozza a hozzáférést bizonyos fürt műveleteihez, így a fürt biztonságosabbá válik.</li><li>A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az írási/olvasási képességeket is). </li><li> A felhasználók alapértelmezés szerint csak olvasási jogosultságot biztosítanak a felügyeleti képességekhez (például a lekérdezési képességekhez), valamint az alkalmazások és szolgáltatások feloldásának lehetőségét.</li></ul>|
|[X. 509 tanúsítványok és Service Fabric](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>[](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) Az éles munkaterheléseket futtató fürtökben használt tanúsítványokat megfelelően konfigurált Windows Server Certificate Service használatával kell létrehozni, vagy egy jóváhagyott HITELESÍTÉSSZOLGÁLTATÓTÓL [(CA)](https://en.wikipedia.org/wiki/Certificate_authority)kell beszerezni.</li><li>Soha ne használjon olyan [ideiglenes vagy tesztelési tanúsítványt](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) az éles környezetben, amely olyan eszközökkel lett létrehozva, mint például a [MakeCert. exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Használhat [önaláírt tanúsítványt](../../service-fabric/service-fabric-windows-cluster-x509-security.md) is, de csak a tesztelési fürtök esetében, és nem éles környezetben.</li></ul>|
|[Fürt biztonsága](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>A fürt biztonsági forgatókönyvei közé tartozik a csomópontok közötti biztonság, az ügyfél és a csomópont közötti biztonság, a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Fürt hitelesítése](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>A fürt [-összevonás csomópont-csomópont](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) típusú kommunikációjának hitelesítése. </li></ul>|
|[Kiszolgáló hitelesítése](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>A [fürt felügyeleti végpontjának](../../service-fabric/service-fabric-cluster-creation-via-portal.md) hitelesítése egy felügyeleti ügyfélen.</li></ul>|
|[Alkalmazások biztonsága](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Az alkalmazás konfigurációs értékeinek titkosítása és visszafejtése.</li><li>   Adattitkosítás a csomópontok között a replikálás során.</li></ul>|
|[Fürt tanúsítványa](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Ez a tanúsítvány szükséges a fürt csomópontjai közötti kommunikáció biztonságossá tételéhez.</li><li>    Állítsa be az elsődleges tanúsítvány ujjlenyomatát az ujjlenyomat szakaszban, a másodlagos értéket pedig a ThumbprintSecondary változóban.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Ez a tanúsítvány az ügyfél számára jelenik meg, amikor megpróbál csatlakozni a fürthöz. A frissítéshez két különböző kiszolgálói tanúsítvány, egy elsődleges és egy másodlagos kiszolgáló is használható.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Ez az a tanúsítvány, amelyet telepíteni kíván a hitelesített ügyfeleken. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Állítsa be az első ügyféltanúsítvány köznapi nevét a CertificateCommonName. A CertificateIssuerThumbprint a tanúsítvány kiállítójának ujjlenyomata. </li></ul>|
|ReverseProxyCertificate| <ul><li>Ez egy opcionális tanúsítvány, amely akkor adható meg, ha biztosítani szeretné a [fordított proxy](../../service-fabric/service-fabric-reverseproxy.md)védelmét. </li></ul>|
|Key Vault| <ul><li>Az Azure-beli Service Fabric-fürtök tanúsítványainak kezelésére szolgál.  </li></ul>|


## <a name="next-steps"></a>További lépések

- [Service Fabric ajánlott biztonsági eljárások](service-fabric-best-practices.md)
- [Service Fabric fürt frissítési folyamata és elvárásai](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Service Fabric-alkalmazások kezelése a Visual Studióban](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Service Fabric Health Model](../../service-fabric/service-fabric-health-introduction.md)bevezetése.
