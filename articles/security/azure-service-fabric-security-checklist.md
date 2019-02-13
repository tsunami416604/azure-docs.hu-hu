---
title: Az Azure service fabric biztonsági ellenőrzőlista |} A Microsoft Docs
description: Ez a cikk biztosít az Azure fabric biztonsági biztonsági ellenőrzőlista készletét.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 06a1903e5e27d748310c1b7846105b8069b73437
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111374"
---
# <a name="azure-service-fabric-security-checklist"></a>Az Azure Service Fabric biztonsági ellenőrzőlista
Ez a cikk ismerteti egy könnyen használható ellenőrzőlista, amely segít megvédeni Azure Service Fabric-környezetét.

## <a name="introduction"></a>Bevezetés
Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

## <a name="checklist"></a>Ellenőrzőlista
A következő ellenőrzőlista segítségével győződjön meg arról, hogy kihagyott-e még nem minden fontos problémák a kezelését és konfigurálását egy biztonságos Azure Service Fabric megoldást.


|Ellenőrzőlista kategória| Leírás |
| ------------ | -------- |
|[Szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így több biztonságos a fürt egyes fürtműveletek való hozzáférés korlátozásához.</li><li>A rendszergazdák (beleértve az olvasási/írási képességeket) felügyeleti képességek teljes hozzáféréssel rendelkezik. </li><li> Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés a felügyeleti funkciók (például a lekérdezési képességek), valamint az alkalmazások és szolgáltatások megoldásához.</li></ul>|
|[X.509-tanúsítványokat és a Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Tanúsítványok](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) legyen egy megfelelően konfigurált Windows Server tanúsítvánnyal szolgáltatással létrehozott vagy beszerzett egy jóváhagyott éles számítási feladatok futtatása fürtökön használt [hitelesítésszolgáltató (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Soha ne használja bármely [ideiglenes vagy tanúsítványok teszteléséhez](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) eszközökkel például a létrehozott éles környezetben [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Használhat egy [önaláírt tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) azonban csak akkor tegye ezt tesztfürtök esetében, és nem éles környezetben.</li></ul>|
|[Fürtbiztonság](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) | <ul><li>A fürtök biztonsági forgatókönyveit tartalmazza a csomópont a csomópont közötti biztonsághoz, ügyfél-csomópont biztonsági, [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Fürt-hitelesítés](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Hitelesíti a [csomópontok közötti kommunikáció](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) fürt az összevonáshoz. </li></ul>|
|[Kiszolgálói hitelesítés](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Hitelesíti a [a fürt felügyeleti végpontok](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal) egy kezelési ügyfél.</li></ul>|
|[Alkalmazások biztonsága](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>Titkosítás és visszafejtés alkalmazáskonfigurációs értékeket.</li><li>   Az adatok titkosítása az csomópont közötti replikálás során.</li></ul>|
|[Fürt tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére.</li><li>    Az ujjlenyomat szakaszban, és hogy a másodlagos ThumbprintSecondary változók be az elsődleges tanúsítvány ujjlenyomatát.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Ezt a tanúsítványt az ügyfél számára való csatlakozás a fürthöz. Két különböző kiszolgálói tanúsítványok, az elsődleges és a egy másodlagos frissítéshez használható.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Ez a tanúsítványokat, amelyek a hitelesített ügyfelek telepíteni szeretné egy készletét. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Az első ügyfél-tanúsítvány köznapi nevével a CertificateCommonName be. A CertificateIssuerThumbprint ezt a tanúsítványt kibocsátó ujjlenyomatát. </li></ul>|
|ReverseProxyCertificate| <ul><li>Ez egy nem kötelező tanúsítványt, amely a megadott, ha szeretné biztonságossá tenni a [fordított Proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Key Vault| <ul><li>Az Azure Service Fabric-fürtök tanúsítványai kezelésére szolgál.  </li></ul>|


## <a name="next-steps"></a>További lépések

- [A Service Fabric ajánlott biztonsági eljárások](azure-service-fabric-security-best-practices.md)
- [Service Fabric-fürt frissítési folyamat és az Ön elvárásainak](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade)
- [A Visual Studióban a Service Fabric-alkalmazások kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Bevezetés a Service Fabric Health modell](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction).
