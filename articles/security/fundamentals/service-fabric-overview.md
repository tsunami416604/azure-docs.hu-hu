---
title: Az Azure Service Fabric biztonsági áttekintése | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Service Fabric biztonságról.
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
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 5329323122d8470b19055040b6f00b6db7c0e75f
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998952"
---
# <a name="azure-service-fabric-security-overview"></a>Az Azure Service Fabric biztonsági áttekintése
Az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) egy elosztott rendszerplatform, amely megkönnyíti a méretezhető és megbízható szolgáltatások csomagolását, üzembe helyezését és felügyeletét. Service Fabric a felhőalapú alkalmazások fejlesztésének és kezelésének kihívásaival foglalkozik. A fejlesztők és a rendszergazdák elkerülhetik a komplex infrastrukturális problémákat, és a nagy teljesítményű, méretezhető és megbízható számítási feladatok megvalósítására összpontosítanak.

Ez a cikk áttekintést nyújt a Service Fabric központi telepítésének biztonsági szempontjairól.

## <a name="secure-your-cluster"></a>A fürt védelme
Az Azure Service Fabric a különböző gépeken üzemelő szolgáltatásokat hangolja össze. A fürtöket védeni kell annak megakadályozása érdekében, hogy jogosulatlan felhasználók csatlakozzanak hozzájuk, különösen akkor, ha éles munkaterheléseket futtatnak. Bár lehetséges, hogy nem biztonságos fürtöt hoz létre, ez lehetővé teheti a névtelen felhasználók számára a fürthöz való csatlakozást (ha a felügyeleti végpontokat a nyilvános internethez teszi elérhetővé).

Az önálló vagy az Azure-t futtató fürtök esetében két forgatókönyvnek kell megfontolnia a csomópontok közötti biztonságot és az ügyfél – csomópont szintű biztonságot. Ezen forgatókönyvek megvalósításához különböző technológiákat használhat.

### <a name="node-to-node-security"></a>Csomópontok közötti biztonság
A csomópontok közötti biztonság a fürtben lévő virtuális gépek vagy számítógépek közötti kommunikációra vonatkozik. A csomópontok közötti biztonság esetében csak a fürthöz való csatlakozásra jogosult számítógépek vehetnek részt a fürtben lévő alkalmazásokban és szolgáltatásokban.

Az Azure-on vagy a Windows rendszeren futó önálló fürtökön futó fürtök a Windows Server rendszerű számítógépek [tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) -vagy [Windows-biztonsági](https://msdn.microsoft.com/library/ff649396.aspx) szolgáltatásait használhatják.

#### <a name="node-to-node-certificate-security"></a>Csomópontok közötti tanúsítvány biztonsága

A Service Fabric a fürt létrehozásakor megadott X. 509 kiszolgálói tanúsítványokat használja. A tanúsítványok és azok beszerzésének és létrehozásának gyors áttekintését a [tanúsítványok használata](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)című témakörben tekintheti meg.

A tanúsítványok biztonsága akkor állítható be, amikor létrehozza a fürtöt a Azure Portalon, Azure Resource Manager sablonokon vagy egy önálló JSON-sablonon keresztül. Megadhat egy elsődleges tanúsítványt és egy opcionális másodlagos tanúsítványt, amelyet a tanúsítvány-átváltáshoz használ. Az elsődleges és a másodlagos tanúsítványnak meg kell egyeznie az ügyfél és a [csomópont közötti biztonsághoz](../../service-fabric/service-fabric-cluster-security.md)megadott rendszergazdai ügyféltől és csak olvasható ügyféltanúsítvány-tanúsítvánnyal.

### <a name="client-to-node-security"></a>Az ügyfél és a csomópont közötti biztonság
Az ügyfél és a csomópont közötti biztonságot az ügyfél-identitások használatával konfigurálhatja. Az ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozásához konfigurálnia kell a fürtöt, hogy megtudja, melyik ügyfél-identitások bízhatnak meg.

Service Fabric két hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz csatlakozó ügyfelekhez:

-   **Rendszergazda**: Teljes hozzáférés a felügyeleti képességekhez, beleértve az írási/olvasási képességeket.
-   **Felhasználó**: Csak olvasási hozzáférés a felügyeleti képességekhez (például a lekérdezési képességekhez), valamint az alkalmazások és szolgáltatások feloldásának lehetősége.

A hozzáférés-vezérlés használatával a fürt rendszergazdái korlátozni tudják a hozzáférést bizonyos típusú fürt-műveletekhez. Ez biztonságosabbá teszi a fürtöt.

#### <a name="client-to-node-certificate-security"></a>Az ügyfél és a csomópont közötti tanúsítvány biztonsága

Ha a Azure Portal, Resource Manager-sablonok vagy önálló JSON-sablon segítségével hoz létre fürtöt, konfigurálja az ügyfél és a csomópont közötti biztonságot. Meg kell adnia egy rendszergazdai ügyféltanúsítványt és/vagy egy felhasználói ügyféltanúsítványt. Győződjön meg arról, hogy ezek a tanúsítványok eltérnek a csomópontok közötti biztonsághoz megadott elsődleges és másodlagos tanúsítványtól.

A felügyeleti tanúsítvány használatával a fürthöz csatlakozó ügyfelek teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez. A fürthöz a csak olvasási jogosultsággal rendelkező felhasználói ügyféltanúsítvány használatával csatlakozó ügyfelek csak olvasási hozzáféréssel rendelkeznek a felügyeleti képességekhez. Más szóval ezeket a tanúsítványokat a szerepköralapú hozzáférés-vezérléshez (RBAC) használja a rendszer.

Ha szeretné megtudni, hogyan konfigurálhatja a tanúsítványok biztonságát egy fürtben, tekintse meg [a fürt beállítása Azure Resource Manager sablonnal](../../service-fabric/service-fabric-cluster-creation-via-arm.md)című témakört.

#### <a name="client-to-node-azure-active-directory-security"></a>Az ügyfél és a csomópont közötti Azure Active Directory biztonság

Az Azure-on futó fürtök a Azure Active Directory (Azure AD) használatával is biztonságossá tehetik a felügyeleti végpontokhoz való hozzáférést. További információ a szükséges Azure Active Directory összetevők létrehozásáról, a fürt létrehozása során történő feltöltéséről, valamint a fürtökhöz való csatlakozásról: [fürt beállítása Azure Resource Manager sablonnal](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlők) az alkalmazásokhoz való felhasználói hozzáférést kezeljék. Vannak olyan alkalmazások, amelyek webalapú bejelentkezési felhasználói felülettel és natív ügyfél-tapasztalattal rendelkező alkalmazásokkal rendelkeznek.

A Service Fabric-fürtök több belépési pontot biztosítanak a felügyeleti funkcióihoz, beleértve a webalapú Service Fabric Explorer és a Visual studiót is. Ennek eredményeképpen két Azure AD-alkalmazást hoz létre a fürt elérésének vezérléséhez: egy webalkalmazást és egy natív alkalmazást.

Az Azure-fürtök esetében javasoljuk, hogy az Azure AD biztonsági szolgáltatásával hitelesítse az ügyfeleket és a tanúsítványokat a csomópontok közötti biztonság érdekében.

A Windows Server 2012 R2 és Active Directory operációs rendszert futtató, különálló Windows Server-fürtök esetében javasoljuk, hogy a Windows-biztonságot a csoportosan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiókokat) használatával használja. Ellenkező esetben használja a Windows-biztonságot Windows-fiókokkal.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>A monitorozás és a diagnosztika megismerése Service Fabric
A [monitorozás és a diagnosztika](../../service-fabric/service-fabric-diagnostics-overview.md) kritikus fontosságú az alkalmazások és szolgáltatások bármilyen környezetben történő fejlesztéséhez, teszteléséhez és üzembe helyezéséhez. A Service Fabric megoldások a monitorozás és a diagnosztika megvalósításakor működnek, így biztosítva, hogy az alkalmazások és szolgáltatások a várt módon működjenek a helyi fejlesztési környezetekben vagy éles környezetben.

Biztonsági szempontból a monitorozás és a diagnosztika fő célja a következő:

-   A biztonsági események által okozott hardver-és infrastrukturális problémák észlelése és diagnosztizálása.
-   A szoftveres és az alkalmazással kapcsolatos olyan problémák észlelése, amelyek veszélyeztethetik a feltörtség (NOB) jeleit.
-   Az erőforrás-felhasználás megismerése a szolgáltatás véletlen megtagadásának megelőzése érdekében.

A monitorozási és diagnosztikai munkafolyamat három lépésből áll:

1.  **Esemény létrehozása**: Az esemény-létrehozás az infrastruktúra (fürt) szintjén és az alkalmazás/szolgáltatás szintjén is tartalmaz eseményeket (naplókat, nyomkövetéseket és egyéni eseményeket). Tudjon meg többet az [infrastruktúra-szintű eseményekről](../../service-fabric/service-fabric-diagnostics-event-generation-infra.md) és az [alkalmazás szintű eseményekről](../../service-fabric/service-fabric-diagnostics-event-generation-app.md) , amelyekkel megismerheti, hogy milyen információkat biztosít, és hogyan adhat hozzá további rendszerállapotot.

2.  **Esemény összesítése**: A generált eseményeket a megjelenítésük előtt össze kell gyűjteni és összesíteni kell. Általában azt javasoljuk, hogy használjon [Azure Diagnostics](../../service-fabric/service-fabric-diagnostics-event-aggregation-wad.md) (az ügynök-alapú naplókhoz hasonlóan) vagy [EventFlow segítségével](../../service-fabric/service-fabric-diagnostics-event-aggregation-eventflow.md) (a folyamaton belüli naplózási gyűjteményben).

3.  **Elemzés**: Az eseményeket az elemzés és a megjelenítés érdekében bizonyos formátumban kell megjeleníteni és elérhetővé tenni. A monitorozási és diagnosztikai adatai több platformon is elérhetők. Javasoljuk, hogy [Azure monitor naplókat](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) és az [Azure Application Insightst](../../service-fabric/service-fabric-diagnostics-event-analysis-appinsights.md) , mivel azok jól integrálva vannak a Service Fabricokkal.

[Azure monitor](../../azure-monitor/overview.md) segítségével számos olyan Azure-erőforrást is megfigyelheti, amelyeken Service Fabric-fürt épül fel.

A watchdog egy különálló szolgáltatás, amely a szolgáltatások állapotának és terhelésének figyelésére, valamint a Health Model-hierarchia minden adatának jelentésére szolgál. A watchdog használatával megakadályozható, hogy az egyetlen szolgáltatás nézete alapján észlelt hibák ne legyenek észlelhetők. 

A watchdog is jó hely a kód futtatására, amely felhasználói beavatkozás nélkül végez javító műveleteket. Egy példa arra, hogy bizonyos időközönként megtisztítsa a naplófájlokat a tárolóban. Az [Azure Service Fabric watchdog-minta](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)keretében megtalálhatja a watchdog szolgáltatás megvalósítását.

## <a name="secure-communication-by-using-certificates"></a>Biztonságos kommunikáció tanúsítványok használatával
A tanúsítványok segítenek az önálló Windows-fürt különböző csomópontjai közötti kommunikáció biztonságossá tételében. X. 509 tanúsítványok használatával a fürthöz csatlakozó ügyfeleket is hitelesíteni lehet. Ez biztosítja, hogy csak a jogosult felhasználók férhessenek hozzá a fürthöz. Azt javasoljuk, hogy a létrehozásakor engedélyezzen egy tanúsítványt a fürtön.

Az X. 509 digitális tanúsítványokat általában az ügyfelek és a kiszolgálók hitelesítésére használják. Emellett az üzenetek titkosítására és digitális aláírására is használhatók.

A következő táblázat felsorolja a fürt beállításához szükséges tanúsítványokat:

|Tanúsítvány-információ beállítása |Leírás|
|-------------------------------|-----------|
|ClusterCertificate|    Ez a tanúsítvány szükséges a fürt csomópontjai közötti kommunikáció biztonságossá tételéhez. Használhat két fürtös tanúsítványt: egy elsődleges tanúsítványt és egy másodlagos frissítést.|
|ServerCertificate| Ez a tanúsítvány az ügyfél számára jelenik meg, amikor megpróbál csatlakozni a fürthöz. Két kiszolgálói tanúsítványt is használhat: egy elsődleges tanúsítványt és egy másodlagost a frissítéshez.|
|ClientCertificateThumbprints|  Ez a hitelesített ügyfelekre telepítendő tanúsítványok halmaza.|
|ClientCertificateCommonNames|  Ez a CertificateCommonName első ügyféltanúsítvány-tanúsítványának köznapi neve. A CertificateIssuerThumbprint a tanúsítvány kiállítójának ujjlenyomata.|
|ReverseProxyCertificate|   Ez egy nem kötelezően megadandó tanúsítvány, amelyet megadhat a [fordított proxy](../../service-fabric/service-fabric-reverseproxy.md)biztonságossá tételéhez.|

További információ a tanúsítványok biztonságossá tételéről: [önálló fürt biztonságossá tétele Windows rendszeren X. 509 tanúsítványok használatával](../../service-fabric/service-fabric-windows-cluster-x509-security.md).

## <a name="understand-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés ismertetése
A fürt létrehozásakor a rendszergazda és a felhasználói szerepköröket külön azonosítók (beleértve a tanúsítványok) megadásával határozhatja meg. További információ az alapértelmezett hozzáférés-vezérlési beállításokról és az alapértelmezett beállítások módosításáról: [Service Fabric ügyfelek szerepköralapú hozzáférés-vezérlése](../../service-fabric/service-fabric-cluster-security-roles.md).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Önálló fürtök védelme a Windows biztonsági szolgáltatásával
A Service Fabric fürthöz való jogosulatlan hozzáférés megakadályozása érdekében védenie kell a fürtöt. A biztonság különösen akkor fontos, ha a fürt éles számítási feladatokat futtat. A ClusterConfig. JSON fájlban a Windows biztonsági szolgáltatással konfigurálhatja a csomópontok közötti és az ügyfél – csomópont szintű biztonságot.

Ha Service Fabric a gMSA alatt kell futnia, a [ClustergMSAIdentity](../../service-fabric/service-fabric-windows-cluster-windows-security.md)beállításával konfigurálhatja a csomópontok közötti biztonságot. A csomópontok közötti megbízhatósági kapcsolatok létrehozásához ismernie kell egymással.

Ha Active Directory tartományon belüli számítógépcsoportot kíván használni, a csomópontok közötti biztonságot a ClusterIdentity beállításával konfigurálhatja. További információ: [Machine Group létrehozása a Active Directoryban](https://msdn.microsoft.com/library/aa545347).

Az ügyfél és a csomópont közötti biztonságot az ClientIdentities használatával konfigurálhatja. A fürtöt úgy kell konfigurálni, hogy felismerje, mely ügyfél-identitások bízhatnak meg. Két módon hozhat létre megbízhatósági kapcsolatot:

-   Itt adhatja meg azokat a tartományi csoportokat, akik csatlakozhatnak.
-   Válassza ki azokat a tartományi csomópont-felhasználókat, akik csatlakozhatnak.

## <a name="configure-application-security-in-service-fabric"></a>Az alkalmazás biztonságának konfigurálása Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>A titkok kezelése Service Fabric alkalmazásokban
A titkok lehetnek bármilyen bizalmas információk, például a tárolási kapcsolatok karakterláncai, jelszavai vagy más olyan értékek, amelyeket nem szabad egyszerű szövegben kezelni.

A kulcsok és a titkos kódok kezeléséhez [Azure Key Vault](../../key-vault/key-vault-overview.md) is használhatja. Egy alkalmazásban azonban nem használhatók a titkos kódok egy adott felhőalapú platformon. Alkalmazásokat telepíthet egy bárhol üzemeltetett fürtre. A folyamat négy fő lépésből áll:

1.  Titkosítási-tanúsítvány beszerzése.
2.  Telepítse a tanúsítványt a fürtön.
3.  Titkosítsa a titkos értékeket, amikor egy alkalmazást telepít a tanúsítvánnyal, és beilleszti őket a szolgáltatás Settings. xml konfigurációs fájljába.
4.  A Settings. xml fájlban lévő titkosított értékek elolvasása ugyanazzal a titkosítási-tanúsítvánnyal való visszafejtéssel.

További információ: [titkok kezelése Service Fabric alkalmazásokban](../../service-fabric/service-fabric-application-secret-management.md).

### <a name="configure-security-policies-for-an-application"></a>Biztonsági szabályzatok konfigurálása alkalmazáshoz
Az Azure Service Fabric Security használatával biztonságosabbá teheti a fürtben futó alkalmazásokat a különböző felhasználói fiókokban. A Service Fabric biztonság emellett a felhasználói fiókok (például fájlok, könyvtárak és tanúsítványok) alatt az alkalmazások által használt erőforrások védelmét is segíti. Így még a megosztottan üzemeltetett környezetekben is biztonságosabbá válik az alkalmazások futtatása.

A biztonsági házirendek konfigurálásának feladatai a következők:

-   Egy szolgáltatás telepítési pontjához tartozó házirend konfigurálása
-   PowerShell-parancsok indítása telepítési belépési pontról
-   Konzol-átirányítás használata helyi hibakereséshez
-   Szabályzat konfigurálása a szolgáltatási kód csomagjaihoz
-   Biztonsági hozzáférési szabályzat kiosztása HTTP-és HTTPS-végpontokhoz

## <a name="secure-communication-for-services"></a>Biztonságos kommunikáció a szolgáltatásokkal
A biztonság a kommunikáció egyik legfontosabb aspektusa. A Reliable Services alkalmazás-keretrendszer néhány előre elkészített kommunikációs veremet és eszközt biztosít, amelyek segítségével javíthatja a biztonságot. További információ: [biztonságos szolgáltatás távelérési kommunikációja szolgáltatáshoz](../../service-fabric/service-fabric-reliable-services-secure-communication.md).

## <a name="next-steps"></a>További lépések
- A fürt biztonságával kapcsolatos elméleti információk: [Service Fabric-fürt létrehozása Azure Resource Manager használatával](../../service-fabric/service-fabric-cluster-creation-via-arm.md) és [Service Fabric-fürt létrehozása a Azure Portal használatával](../../service-fabric/service-fabric-cluster-creation-via-portal.md).
- Ha többet szeretne megtudni a Service Fabric a fürt biztonságáról, tekintse meg a [Service Fabric-fürtök biztonsági forgatókönyveit](../../service-fabric/service-fabric-cluster-security.md).
