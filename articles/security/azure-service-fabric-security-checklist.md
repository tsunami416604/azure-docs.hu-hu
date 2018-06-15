---
title: Az Azure service fabric biztonsági ellenőrzőlista |} Microsoft Docs
description: Ez a cikk ellenőrzőlista biztosít az Azure-hálót biztonsági biztonsági.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ef404b106d600f5cb25a46319d75c8978148b466
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895873"
---
# <a name="azure-service-fabric-security-checklist"></a>Az Azure Service Fabric biztonsági ellenőrzőlista
Ez a cikk egy könnyen használható ellenőrzőlistát, amely segít az Azure Service Fabric-környezet biztonságának tartalmazza.

## <a name="introduction"></a>Bevezetés
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

## <a name="checklist"></a>Feladatlista
A következő ellenőrzőlista segítségével győződjön meg arról, hogy kihagyott-e még nem minden fontos problémák a kezelését és biztonságos Azure Service Fabric-megoldás konfigurálását.


|Feladatlista kategória| Leírás |
| ------------ | -------- |
|[A szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így a fürt biztonságosabb bizonyos fürtműveletekben való hozzáférés korlátozásához.</li><li>Rendszergazdák (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. </li><li> Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét.</li></ul>|
|[X.509-tanúsítványokat és a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Tanúsítványok](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) fürtön futó termelési számítási feladatokhoz legyen a megfelelően konfigurált Windows kiszolgálói tanúsítvány szolgáltatások segítségével létrehozott vagy beszerzett egy jóváhagyott használt [tanúsítvány hitelesítésszolgáltatói (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Soha ne használja a [ideiglenes vagy tanúsítványok tesztelése](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) használatával létrehozott eszközök például éles környezetben [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Használhatja a [önaláírt tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) azonban csak akkor tegye ezt tesztfürtökön és nem éles környezetben.</li></ul>|
|[Fürtbiztonság](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>A fürt biztonsági forgatókönyvek például a csomópontok biztonsági, ügyfél-csomópont biztonsági [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Fürt hitelesítés](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Hitelesíti [csomópontok kommunikáció](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) fürt összevonási. </li></ul>|
|[Kiszolgálóhitelesítés](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Hitelesíti a [fürt felügyeleti végpontok](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) felügyeleti ügyfél számára.</li></ul>|
|[Alkalmazások biztonsága](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Titkosítás és visszafejtés az alkalmazáskonfigurációs értékeket.</li><li>   Replikáció során az adatok csomópontok közötti titkosítása.</li></ul>|
|[Fürt tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére.</li><li>    Állítsa be az elsődleges tanúsítvány ujjlenyomatát a ujjlenyomat szakaszban, valamint a másodlagos ThumbprintSecondary változók.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Ezt a tanúsítványt az ügyfél áll rendelkezésre, ha csatlakozik a fürthöz. Frissítés két különböző kiszolgálói tanúsítványok, egy elsődleges és másodlagos használható.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Ez olyan tanúsítványokat, amelyek a hitelesített ügyfelek telepíteni szeretné. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Az első ügyfél tanúsítvány köznapi nevének beállítása a CertificateCommonName. A CertificateIssuerThumbprint Ez a tanúsítvány kiállítójának ujjlenyomata. </li></ul>|
|ReverseProxyCertificate| <ul><li>Ez az egy nem kötelező tanúsítvány, amely meg, hogy szeretné-e biztonságos a [fordított Proxy](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Az Azure Service Fabric-fürtök tanúsítványai kezelésére használható.  </li></ul>|


## <a name="next-steps"></a>További lépések
- [Service Fabric-fürt verziófrissítés folyamatáról és az Ön elvárásainak](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [A Service Fabric-alkalmazások, a Visual Studio kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Service Fabric állapotfigyelő modell bemutatása](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).
