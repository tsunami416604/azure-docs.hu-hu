---
title: Az Azure Service Fabric biztonsági áttekintése |} A Microsoft Docs
description: Ez a cikk az Azure Service Fabric biztonsága áttekintést nyújt.
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
ms.openlocfilehash: 629b6fba9ced5fa2ccf22f473fe25c87d1cc4818
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436810"
---
# <a name="azure-service-fabric-security-overview"></a>Az Azure Service Fabric biztonsági áttekintése
[Az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) egy elosztott rendszerplatform, amely megkönnyíti a csomagolását, üzembe helyezése és kezelése a skálázható és megbízható mikroszolgáltatások. A Service Fabric a felhőalapú alkalmazások fejlesztésének és kezelésének kihívásaira. A fejlesztők és rendszergazdák infrastruktúrával kapcsolatos összetett problémák elkerülése és összpontosíthat, amelyek a méretezhető és megbízható alapvető fontosságú, nagy erőforrás-igényű számítási feladatok megvalósítására.

Ez a cikk a Service Fabric üzembe helyezéséhez a biztonsági szempontok nyújt áttekintést.

## <a name="secure-your-cluster"></a>A fürt védelme
Az Azure Service Fabric hangolja össze a szolgáltatások Vezényel számítógépfürtökön. Fürtök kell biztosítani, hogy megakadályozza a jogosulatlan felhasználók csatlakozzanak, különösen akkor, ha az éles számítási feladatok futnak. Bár egy biztonságos fürt létrehozásához, ez lehetővé teheti a névtelen felhasználók számára, hogy csatlakozzon a fürthöz (Ha a nyilvános interneten végpontoknak érheti el).

Különálló szolgáltatásként futtató fürtök esetén, vagy az Azure-ban két forgatókönyvet kell figyelembe venni olyan csomópontok közötti biztonsági és az ügyfél a csomópont közötti biztonsághoz. Különböző technológiákkal, illetve a forgatókönyvek megvalósításához használható.

### <a name="node-to-node-security"></a>Csomópontok közötti biztonsági
Csomópontok közötti biztonsági vonatkozik a virtuális gépek vagy a fürtben található gépek közötti kommunikációt. Csomópontok közötti biztonsági csak a fürthöz való csatlakoztatáshoz jogosult számítógépek részt vehetnek futtató alkalmazások és szolgáltatások a fürtben.

A Windows rendszert futtató Azure-ban vagy az önálló fürtök a futó fürtök bármelyiket használhatja [biztonsági tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) vagy [Windows biztonsági](https://msdn.microsoft.com/library/ff649396.aspx) Windows Server-alapú gépek esetében.

#### <a name="node-to-node-certificate-security"></a>Biztonsági csomópontok tanúsítvány

Service Fabric-fürt létrehozásakor megadott X.509 kiszolgálótanúsítványok használ. Mik azok a tanúsítványok, és hogyan beszerezni, vagy hozza létre őket rövid áttekintéséért lásd: [-tanúsítványok használata](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Tanúsítvány biztonsági konfigurálja a fürt az Azure Portalon, az Azure Resource Manager-sablonok vagy egy különálló JSON-sablon létrehozásakor. Megadhat egy elsődleges tanúsítvány és a egy nem kötelező másodlagos tanúsítvány tanúsítvány előgörgetések szolgálja ki. Az elsődleges és másodlagos tanúsítványát, Ön által megadott különböznie kell a rendszergazdai ügyfél és a csak olvasható ügyfél tanúsítványokat, amelyek a megadott [ügyfél a csomópont közötti biztonsághoz](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Ügyfél-csomópont biztonsági
Ügyfél identitások használatával állítja be az ügyfél a csomópont közötti biztonsághoz. Ügyfél és a egy fürt közötti megbízhatósági kapcsolatot hoz létre, konfigurálnia kell a fürt tudni, hogy melyik ügyfél megbízható, identitásokat.

A Service Fabric a Service Fabric-fürt csatlakozó ügyfelek access control kétféle támogatja:

-   **Rendszergazdai**: teljes hozzáférés az lehetőségekről, köztük az olvasási/írási képességeket.
-   **Felhasználói**: csak olvasási hozzáférés a felügyeleti funkciók (például a lekérdezési képességek), valamint az alkalmazások és szolgáltatások megoldásához.

Hozzáférés-vezérlés használatával a fürt a rendszergazdák bizonyos típusú, a fürt működését hozzáférést korlátozhatja. Ez lehetővé teszi a fürt biztonságosabb.

#### <a name="client-to-node-certificate-security"></a>Ügyfél-csomópont tanúsítványalapú biztonsággal

Ügyfél-csomópont tanúsítvány biztonsági konfigurál egy fürtöt az Azure Portalon, a Resource Manager-sablonok vagy egy különálló JSON-sablon létrehozásakor. Meg kell adnia egy rendszergazdai ügyfél tanúsítványa és/vagy felhasználói ügyféltanúsítványt. Győződjön meg arról, hogy ezek a tanúsítványok eltérnek az elsődleges és másodlagos tanúsítványát, Ön által megadott csomópont és csomópont közötti biztonsághoz.

A felügyeleti tanúsítvány használatával a fürthöz csatlakozó ügyfelek és a felügyeleti képességek teljes hozzáféréssel rendelkezik. Az írásvédett felhasználó ügyféltanúsítvány használatával a fürthöz csatlakozó ügyfelek csak olvasási hozzáférés-felügyeleti képességekkel rendelkezik. Más szóval ezek a tanúsítványok szolgálnak szerepköralapú hozzáférés-vezérlés (RBAC).

Biztonsági tanúsítvány konfigurálása egy fürtben kapcsolatban lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Azure Active Directory biztonsági ügyfél-csomópont

Az Azure-ban futó fürtökhöz is biztosíthat hozzáférést a felügyeleti végpontok Azure Active Directory (Azure AD) használatával. Hogyan hozhat létre a szükséges Azure Active Directory-összetevők, hogyan tölthetők fel a fürt létrehozásakor és e-fürtökhöz csatlakozhat, hogyan kapcsolatos információkért lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure ad-ben alkalmazásokhoz való felhasználói hozzáférés kezelése lehetővé teszi a szervezetek (más néven bérlők). Nincsenek alkalmazások és a egy webalapú bejelentkezési felhasználói felület és alkalmazások egy natív ügyfél felhasználói élményét.

Service Fabric-fürt kínál a felügyeleti funkciókat, beleértve a webalapú Service Fabric Explorer és a Visual Studio számos elérésére. Ennek eredményeképpen hoz létre, ki férhet hozzá a fürt két Azure AD-alkalmazások: egy webalkalmazás és a egy natív alkalmazást.

Azure fürtök esetén azt javasoljuk, hogy használja-e az Azure AD biztonsági ügyfelek és a csomópontok közötti biztonsági tanúsítványok hitelesítéséhez.

Önálló Windows Server-fürtök a Windows Server 2012 R2 és az Active Directory javasoljuk, hogy a csoportosan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiókokat) használjon Windows biztonsági. Ellenkező esetben használja a Windows biztonsági Windows-fiókokkal.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Megfigyelés és a Service Fabric-diagnosztika
[A monitoring and diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) különösen fontos fejlesztése, tesztelése és üzembe helyezése az alkalmazások és szolgáltatások bármilyen környezetben. A Service Fabric-megoldások akkor működnek a legjobban, monitorozási és diagnosztikai funkciókkal, győződjön meg arról, hogy alkalmazásokat és szolgáltatásokat a várt módon működik a helyi fejlesztési környezetet, akár éles környezetről megvalósításakor.

Biztonsági szempontból a monitorozási és diagnosztikai fő céljai a következők:

-   Észlelheti és diagnosztizálhatja a hardver- és infrastruktúra problémákat, amelyek biztonsági eseményt okozhatja.
-   Szoftver- és alkalmazás-hibák, amely azt jelzi, hogy a biztonsági sérülés (IoC) lehet észlelése.
-   Ismerje meg, erőforrás-használat nem szándékos szolgáltatásmegtagadás megelőzése érdekében.

A monitorozási és diagnosztikai munkafolyamat három lépésből áll:

1.  **Az eseménygenerálás**: az eseménygenerálás események (naplókat, nyomkövetéseket, egyéni események) tartalmazza a (fürt) infrastruktúra-szintű és az alkalmazások vagy szolgáltatások szintjén egyaránt. Tudjon meg többet [infrastruktúra-szintű eseményeit](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) és [alkalmazásszintű események](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) megtudhatja, hogy milyen áll rendelkezésre, és hogyan adhat hozzá további kialakítási.

2.  **Események összesítése**: létrehozott események kell gyűjteni és összesített előtt is megjeleníthetők. Általában javasoljuk [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (az ügynök-alapú naplógyűjtés hasonló) vagy [eventflow segítségével](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (folyamaton belüli naplógyűjtés).

3.  **Elemzés**: események kell lennie a teszi és néhány formátumban, elemzési és megjelenítési elérhető. Nincsenek a elemzési és vizualizációs monitorozási és diagnosztikai adatok számos platformra. Javasoljuk, hogy [Azure Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) és [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) mert azok a Service Fabric integrálása.

Is [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) figyelése számos, az Azure-erőforrások, amelyen a Service Fabric-fürt épül.

Egy figyelő egy külön szolgáltatás, amely állapotának megtekintése és az egészségügyi modell hierarchiában lévő összes adat lehet a szolgáltatások és a jelentés állapotának betöltése. Egy figyelő használatával megakadályozhatja a hibákat, nem lennének megtalálhatóak egy egyetlen szolgáltatást nézete alapján. 

Watchdogs egyúttal nagyszerű hely az állomás kódot, amely végrehajtja a javító műveleteket, felhasználói beavatkozás nélkül. Például naplófájlok tárolási a tisztítás bizonyos időszakonkénti cseréjét. A minta megvalósítása figyelő címen található [Azure Service Fabric-figyelő minta](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-communication-by-using-certificates"></a>Tanúsítványok használatával biztonságos kommunikáció
Tanúsítványok segítségével a különálló Windows-fürt a különféle csomópontok közötti kommunikáció védelméhez. X.509-tanúsítványok használatával is hitelesítheti az ügyfelek, amelyek ehhez a fürthöz csatlakozik. Ez biztosítja, hogy csak a jogosult felhasználók hozzáférhet a fürthöz. Azt javasoljuk, hogy engedélyezze a fürtben található tanúsítvány létrehozásakor.

X.509 digitális tanúsítványok gyakran segítségével az ügyfelek és kiszolgálók hitelesítéséhez. Emellett titkosítására és digitális aláírására használhatók.

Az alábbi táblázat a tanúsítványok, a fürt beállítása a van szüksége:

|Tanúsítvány eszközinformációs beállítás |Leírás|
|-------------------------------|-----------|
|ClusterCertificate|    Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére. Két fürt tanúsítványt is használhat: egy elsődleges tanúsítványt, és a egy másodlagos frissítéshez.|
|ServerCertificate| Ezt a tanúsítványt az ügyfél számára való csatlakozás a fürthöz. Két kiszolgálói tanúsítványokat is használhat: egy elsődleges tanúsítványt, és a egy másodlagos frissítéshez.|
|ClientCertificateThumbprints|  Ez az a hitelesített ügyfelek telepítendő tanúsítványok készletét.|
|ClientCertificateCommonNames|  Ez a CertificateCommonName az első ügyfél-tanúsítvány köznapi neveként. CertificateIssuerThumbprint ezt a tanúsítványt kibocsátó ujjlenyomatát.|
|ReverseProxyCertificate|   Ez az egy nem kötelező tanúsítvány biztonságos megadható a [fordított proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Tanúsítványok védelmével kapcsolatos további információkért lásd: [Windows különálló fürt védelme X.509-tanúsítványok használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés megismerése
Megadhatja a rendszergazdai és felhasználói szerepkörök ügyfél, a fürt létrehozásakor (például a tanúsítványok) külön identitások azáltal, hogy az egyes. Az alapértelmezett hozzáférés-vezérlési beállításokkal és az alapértelmezett beállítások módosításával kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Önálló fürtök biztonságos Windows-rendszerbiztonság használatával
Service Fabric-fürtön való illetéktelen hozzáférés megakadályozása, biztosítania kell a fürtöt. Biztonsági különösen fontos, amikor a fürt futtatása termelési számítási feladatokhoz. Csomópont-csomópont és az ügyfél-csomópont biztonsági konfigurálását a Windows biztonsági a ClusterConfig.JSON fájl használatával.

Amikor a Service Fabric van szüksége a csoportosan felügyelt szolgáltatásfiók alatt szeretné futtatni, beállításával csomópontok közötti biztonságának konfigurálása [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Csomópontok közötti bizalmi kapcsolatokat hozhat létre, meg kell győződnie, őket figyelembe egymással.

Ha azt szeretné, a gép csoportot használja az Active Directory-tartományban lévő csomópontok közötti biztonsági konfigurálnia ClusterIdentity beállításával. További információkért lásd: [hozzon létre egy gép csoportot az Active Directory](https://msdn.microsoft.com/library/aa545347).

Ügyfél-csomópont biztonsági ClientIdentities segítségével konfigurálhatja. Konfigurálnia kell a fürt azon ügyfél identitások, amelyek megbízható, felismerni. Megbízhatósági kapcsolat két módon is létrehozhat:

-   Adja meg a tartományi felhasználók csoporthoz, amely csatlakozni tud.
-   Adja meg a csomópont Tartományfelhasználók kapcsolódhatnak.

## <a name="configure-application-security-in-service-fabric"></a>A Service Fabric-alkalmazások biztonságának konfigurálása
### <a name="manage-secrets-in-service-fabric-applications"></a>Service Fabric-alkalmazásokat a titkos kulcsok kezelése
Titkos kódok lehet a bizalmas adatokat, például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem szövegként kezelje.

Használhat [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) kulcsok és titkos kulcsok kezeléséhez. Azonban az alkalmazások titkos kulcsok használatát egy adott felhőalapú platformon nem függ. A fürt, bárhol üzemeltetett alkalmazásokat helyezhet üzembe. Ez a folyamat a négy fő lépésből áll:

1.  Adatok titkosítási tanúsítvány beszerzéséhez.
2.  Telepítse a tanúsítványt a fürtön.
3.  Titkosítani a titkos értékkel, a tanúsítvány az alkalmazás üzembe helyezésekor, majd helyezze azokat egy szolgáltatás Settings.xml konfigurációs fájlt.
4.  Olvassa el a titkosított értékeket Settings.xml visszafejti őket a ugyanazt a titkosítási tanúsítványt.

További információkért lásd: [kezelése a Service Fabric-alkalmazások titkos kulcsainak](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Az alkalmazás biztonsági szabályzatok konfigurálása
Az Azure Service Fabric biztonsága, segíthet a különböző felhasználói fiókok a fürtben futó biztonságos alkalmazásokat. A Service Fabric biztonsága is biztonságossá tehető a erőforrásokat használó alkalmazások időpontjában üzemelő példányt a felhasználói fiókok – például, fájlok, könyvtárak és tanúsítványokat. Biztonságosabb így futó alkalmazások, még akkor is, a közös üzemeltetési környezetben.

Feladatok biztonsági szabályzatainak konfigurálásához a következők:

-   A szabályzat a szolgáltatásbeállítás belépési pontjának konfigurálása
-   PowerShell-parancsok kezdve a szolgáltatásbeállítás belépési pontjának
-   A konzolátirányítás használatával a helyi hibakereséshez
-   A kód szolgáltatáscsomagok házirend beállítása
-   A HTTP vagy HTTPS-végpontokat biztonsági hozzáférési szabályzat hozzárendelése

## <a name="secure-communication-for-services"></a>Biztonságos kommunikáció a szolgáltatások
Biztonsági az egyik legfontosabb szempontja kommunikáció. A Reliable Services alkalmazási keretrendszer tartalmaz néhány előre létrehozott kommunikációs implementációt, eszközök, amelyek a biztonság növelése érdekében használhatja. További információkért lásd: [szolgáltatás távelérésének lehetővé tétele szolgáltatáskommunikációs biztonságos](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>További lépések
- Fürtbiztonság kapcsolatos általános információkért lásd: [Service Fabric-fürt létrehozása az Azure Resource Managerrel](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) és [Service Fabric-fürt létrehozása az Azure portal használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- A Service Fabric fürtök védelmével kapcsolatos további információkért lásd: [Service Fabric-fürtök biztonsági forgatókönyveit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
