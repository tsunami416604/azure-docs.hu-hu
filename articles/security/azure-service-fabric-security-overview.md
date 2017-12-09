---
title: "Az Azure service fabric biztonsági áttekintése |} Microsoft Docs"
description: "Ez a cikk áttekintést nyújt az Azure Service Fabric biztonsági."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: 64717da922701aabd27e15a67e8da1b0acb30b77
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="azure-service-fabric-security-overview"></a>Az Azure Service Fabric biztonsági áttekintése
[Az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) egy elosztott rendszerek platform, amely megkönnyíti a csomag, telepítése és kezelése, méretezhető és megbízható mikroszolgáltatások létrehozására. A Service Fabric fejlesztése és a felhőalapú alkalmazások kezelésének jelentős kihívásaira megoldást. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

Azure Service Fabric biztonsági áttekintő cikkben elsősorban az alábbi területeken:

-   A fürt védelme
-   Understanding figyelési és diagnosztika
-   Tanúsítványok használatával biztonságosabb környezetek létrehozása
-   Szerepköralapú hozzáférés-vezérlést (RBAC) használata
-   Fürtök védelme a Windows biztonsági használatával
-   A Service Fabric alkalmazás biztonsági beállításainak megadása
-   Azure Service Fabric-szolgáltatások kommunikáció biztonságához 

## <a name="secure-your-cluster"></a>A fürt védelme
Az Azure Service Fabric szolgáltatás koordinálja a gépet fürtön belül. Fürtök védetté kell tennie, hogy jogosulatlan felhasználók csatlakozzon, különösen akkor, ha a termelési számítási feladatokhoz futnak. Bár lehetséges egy nem biztonságos fürtök létrehozásához, ez lehetővé teheti a névtelen felhasználók a csatlakozáshoz (Ha a nyilvános internethez felügyeleti végpontok érheti el).

Ez a szakasz áttekintést a biztonsági forgatókönyveket vagy önálló rendszert futtató fürtöket, vagy az Azure-on. Azt is bemutatja a különböző technológiák azokra végrehajtásához használt. A fürt biztonsági forgatókönyvek a következők:

-   Csomópontok biztonsági
-   Ügyfél-csomópont biztonsági

### <a name="node-to-node-security"></a>Csomópontok biztonsági
Csomópontok biztonság a virtuális gépek vagy a fürt gépek közötti kommunikáció biztonságossá tételére. A csomópontok biztonságot csak azok a számítógépek, amelyek jogosultak a fürthöz való csatlakoztatáshoz részt vehetnek-alkalmazások és szolgáltatások a fürt üzemeltetéséhez.

Windows rendszeren futó Azure vagy az önálló fürtök a futó fürtök választhat [biztonsági tanúsítvány](https://msdn.microsoft.com/library/ff649801.aspx) vagy [Windows biztonsági](https://msdn.microsoft.com/library/ff649396.aspx) a Windows Server-gépek.

**Csomópontok tanúsítvány biztonsági ismertetése**

A Service Fabric használ X.509 kiszolgálói tanúsítványok meg kell adnia a fürt létrehozásakor. Mik azok a tanúsítványok, és hogyan szerezni, vagy hozza létre a címzetteket gyors áttekintést, lásd: [tanúsítványok használata](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

Tanúsítvány biztonsági konfigurálnia a fürt, vagy az Azure-portálon, az Azure Resource Manager-sablonok vagy egy önálló JSON-sablon létrehozásakor. Megadhat egy elsődleges tanúsítványnak és egy nem kötelező másodlagos tanúsítvány előgörgetések használt tanúsítványt. A megadott elsődleges és másodlagos tanúsítványokat nem lehet ugyanaz, mint a felügyeleti ügyfél és a csak olvasható ügyféltanúsítványok beállításhoz [ügyfél-csomópont biztonsági](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Ügyfél-csomópont biztonsági
Ügyfél-csomópont biztonsági ügyfél identitások segítségével konfigurálhatja. Ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozása, konfigurálnia kell a fürt tudni, hogy melyik ügyfél akkor is megbízhat identitásokat. Ehhez két különböző módon:

-   Adja meg a tartományi felhasználók csoporthoz, amely képes kapcsolódni. 
-   Adja meg a csomópont Tartományfelhasználók csatlakoztatható.

Service Fabric két különböző hozzáférési vezérlő típusokat támogatja az ügyfelek csatlakoznak a Service Fabric-fürt:

-   Rendszergazda
-   Felhasználó

Hozzáférés-vezérlés használatával fürt rendszergazdák korlátozhatják, hogy bizonyos típusú fürtműveletekben elérésére. Így a fürt biztonságosabb.

 Rendszergazdák (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét.

**Ügyfél-csomópont tanúsítvány biztonsági ismertetése**

Ügyfél-csomópont tanúsítvány biztonsági konfigurál egy fürt legyen az Azure-portálon, a Resource Manager-sablonok vagy egy önálló JSON-sablon létrehozásakor. Meg kell adnia egy rendszergazdai ügyféltanúsítvány és/vagy felhasználói ügyféltanúsítványt. 

Felügyeleti ügyfél és a felhasználói ügyféltanúsítványok megadott nem lehet ugyanaz, mint az elsődleges és másodlagos tanúsítványok, amennyit megadott a csomópontok biztonsági.

A fürt felügyeleti tanúsítvány használatával csatlakozó ügyfelek felügyeleti képességek teljes hozzáféréssel rendelkeznek. A fürt a felhasználó csak olvasható ügyféltanúsítvány használatával csatlakozó ügyfelek felügyeleti képességek csak olvasási hozzáféréssel rendelkeznek. Más szóval ezek a tanúsítványok érvényesek RBAC.

Tanúsítvány biztonság konfigurálása a fürt további tudnivalókért lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

**Ügyfél-csomópont Azure Active Directory biztonsági Azure ismertetése**

Az Azure-on futó fürtök is biztosíthassa a felügyeleti végpontok elérését Azure Active Directory (Azure AD) segítségével. Létrehozása az Azure Active Directory szükséges összetevőket, hogyan tölthetők fel a fürt létrehozása során és kapcsolódás ezeket a fürtök kapcsolatos információkért lásd: [fürt beállítása az Azure Resource Manager-sablon használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Az Azure AD lehetővé teszi, hogy a szervezetek (más néven bérlői) alkalmazásokhoz való felhasználói hozzáférések kezelése. Nincsenek a webalapú bejelentkezési felhasználói felület az alkalmazásokat és natív ügyfél nyújthassunk.

A Service Fabric-fürt felügyeleti szolgáltatásai, beleértve a webalapú Service Fabric Explorer és a Visual Studio számos belépési pontot nyújt. Ennek eredményeképpen történő hozzáférés szabályozása érdekében a fürt két Azure AD-alkalmazások létrehozása: egy webalkalmazást, és egy natív alkalmazás.

Az Azure-fürtök esetén azt javasoljuk, hogy használja-e az Azure AD biztonsági ügyfelek és -csomópontok biztonsági tanúsítványok hitelesítéséhez.

Különálló Windows Server-fürtök a Windows Server 2012 R2 és az Active Directory Azt javasoljuk, hogy használja-e Windows biztonsági a csoportosan felügyelt fiókokkal.  Ellenkező esetben használja a Windows biztonsági Windows fiókokhoz.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Megfigyelés és az Azure Service Fabric diagnosztika
[Megfigyelési és diagnosztikai](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) kritikus fejleszt, tesztelése és telepítése az alkalmazások és szolgáltatások minden környezetben. A Service Fabric-megoldások a legmegfelelőbb figyelése és diagnosztikai alkalmazások és szolgáltatások működésének biztosításához a helyi környezet, vagy éles környezetben bevezetésekor.

Biztonsági szempontból a fő figyelése és diagnosztikai céljai:

-   Észleli és eseményadatokat hardver- és infrastruktúra, amely biztonsági esemény okozhatja.
-   Észlelés, szoftver- és alkalmazás-problémák, amely azt jelzi, hogy a biztonsági sérülés (IoC) lehet.
-   Ismerje meg, erőforrás-felhasználás véletlen szolgáltatásmegtagadást megelőzése érdekében.

Az általános munkafolyamat figyelési és diagnosztika három lépésből áll:

-   **Az eseménygenerálás:** eseménygenerálás azokat az eseményeket tartalmazza (naplókat, nyomkövetéseket, egyéni események) az infrastruktúra (fürt) és az alkalmazás/kiszolgáló szinten egyaránt. Tudjon meg többet az [infrastruktúra szintű események](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) és [alkalmazásszintű események](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) megtudhatja, hogy milyen áll rendelkezésre, és további instrumentation hozzáadása.

-   **Esemény összesítési:** létrehozott események összegyűjtésének és azok megjelenítése előtt összesítve kell. Általában javasoljuk [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (ügynök-alapú naplógyűjtést hasonlóan) vagy [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (a folyamat naplógyűjtést).

-   **Elemzési:** események kell lenniük a feladatkonfigurációkat és néhány formátumban, elemzés és megjelenítési érhető el. Nincsenek elemzésére és figyelési és diagnosztikai adatok a képi megjelenítés számos platformra. Javasolt a két rendszer [Operations Management Suite](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) és [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) miatt a Service Fabric jó integrációját.

Is [Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) számos, amelyen a Service Fabric-fürt épül Azure-erőforrások figyelése.

Egy figyelő figyelemmel követheti az állapotfigyelő szolgáltatásban betölteni, és a jelentés semmit, a health modell hierarchia rendszerállapot különálló szolgáltatásként. Egy figyelő segítségével megakadályozhatja a nézet egy egyetlen szolgáltatás alapján, amely nem észlelhető hiba. 

Watchdogs egyaránt remek állomás kódot, amely végrehajtja a javító műveleteket (például bizonyos időközönként törölje a naplófájlokat tároló) felhasználói beavatkozás nélkül. Egy minta figyelő megvalósított, megtalálhatja [Azure Service Fabric-figyelő mintavételi](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Megtudhatja, hogyan tanúsítványok használatával biztonságos kommunikáció
Tanúsítványok segítségével a különálló Windows fürt különböző csomópontok közötti kommunikáció védelméhez. X.509-tanúsítványokat használ, a fürt csatlakozó ügyfelek is hitelesítéséhez. Ez biztosítja, hogy csak a jogosult felhasználók férhetnek hozzá a fürt. Azt javasoljuk, hogy engedélyeznie a tanúsítványt a fürt létrehozásakor.

### <a name="x509-certificates-and-service-fabric"></a>X.509-tanúsítványokat és a Service Fabric
X.509 digitális tanúsítványok segítségével hitelesíti az ügyfelek és kiszolgálók általában használhatók. Akkor is titkosítására és digitális aláírására használhatók.

A következő táblázat, amelyekre szüksége van a fürt beállítása a tanúsítványokat sorolja fel:

|Tanúsítvány-információkat beállítás |Leírás|
|-------------------------------|-----------|
|ClusterCertificate|    Ez a tanúsítvány szükséges a fürt a csomópontok közötti kommunikáció biztonságossá tételére. Két különböző tanúsítványokat is használhat: egy elsődleges tanúsítványt, és egy másodlagos frissítésre.|
|ServerCertificate| Ezt a tanúsítványt az ügyfél áll rendelkezésre, ha csatlakozik a fürthöz. Két különböző kiszolgálói tanúsítványokat is használhat: egy elsődleges tanúsítványt, és egy másodlagos frissítésre.|
|ClientCertificateThumbprints|  Ez az a hitelesített ügyfelek telepítendő tanúsítványok készlete.|
|ClientCertificateCommonNames|  Ez az CertificateCommonName az első ügyféltanúsítvány köznapi neveként. CertificateIssuerThumbprint Ez a tanúsítvány kiállítójának ujjlenyomata.|
|ReverseProxyCertificate|   Ez az egy nem kötelező tanúsítvány biztonságos adható meg a [fordított proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

További információ a tanúsítványok biztonságossá tétele: [önálló fürtben, X.509-tanúsítványokat használ a Windows biztonságos](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés ismertetése
Hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző csoportok számára, így nagyobb biztonságot nyújt a fürt egyes fürtműveletekben való hozzáférés korlátozásához. Fürthöz csatlakozó ügyfelek két különböző hozzáférési ellenőrzési típusok támogatottak: 

- Rendszergazdai szerepkör
- felhasználói szerepkör

Rendszergazdák (beleértve az olvasási/írási képességek) felügyeleti képességek teljes hozzáféréssel rendelkeznek. Felhasználók, alapértelmezés szerint rendelkezik csak olvasási hozzáférés kezelési képességek (például lekérdezési lehetőségek), és oldja meg az alkalmazások és szolgáltatások képességét.

Megadhatja a rendszergazdai és felhasználói szerepkörök ügyfél a fürt létrehozása idején (beleértve a tanúsítványok) külön identitások megadásával az egyes. Az alapértelmezett hozzáférés-vezérlési beállításokkal és az alapértelmezett beállítások módosításával kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>A Windows biztonsági biztonságos önálló fürthöz
A jogosulatlan hozzáférés elkerülése érdekében a Service Fabric-fürt, biztosítania kell a fürthöz. Biztonsági különösen fontos, amikor a fürt fut termelési számítási feladatokhoz. Ismerteti, hogyan állíthat be csomópontok és ügyfél-csomópont biztonsági Windows biztonsági a művelet fájlban.

**Csoportosan felügyelt szolgáltatásfiókot használó Windows rendszerbiztonság konfigurálása**

Ha a Service Fabric csoportosan felügyelt szolgáltatásfiók alatt fut, úgy, hogy konfigurálja-csomópontok biztonsági [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Csomópontok közötti megbízhatósági kapcsolat létrehozására is el minden más kompatibilis.

Ügyfél-csomópont biztonsági ClientIdentities segítségével konfigurálhatja. Ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozása, konfigurálnia kell a fürtöt, mely akkor is megbízhat ügyfél identitások ismeri fel.

**A számítógép csoport használatával Windows biztonságának konfigurálása**

Ha egy gép csoportot az Active Directory-tartományban használni kívánt, csomópontok biztonságának konfigurálása ClusterIdentity beállításával. További információkért lásd: [létrehoz egy gép csoportot az Active Directory](https://msdn.microsoft.com/library/aa545347).

Ügyfél-csomópont biztonsági ClientIdentities segítségével konfigurálhatja. Ügyfél és a fürt közötti megbízhatósági kapcsolat létrehozása, konfigurálnia kell a fürt a fürt megbízhatónak az ügyfél identitások ismeri fel. Megbízhatósági kapcsolat két különböző módon is létrehozása:

-   Adja meg a tartományi felhasználók csoporthoz, amely képes kapcsolódni.
-   Adja meg a csomópont Tartományfelhasználók csatlakoztatható.

## <a name="configure-application-security-in-service-fabric"></a>A Service Fabric-alkalmazások biztonságának konfigurálása
### <a name="manage-secrets-in-service-fabric-applications"></a>A Service Fabric-alkalmazások titkos kulcsok kezelése
Ez a módszer segítségével, a Service Fabric-alkalmazás a titkos kulcsok kezelése. Titkos kulcsok lehet bármely bizalmas adatokat, például a tárolási kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem egyszerű szöveges kezelje.

Ezt a módszert használja [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) kulcsok és titkos kulcsok kezeléséhez. Egy alkalmazás titkos kulcsok használata azonban felhő platform-független. Ez azt jelenti, hogy a fürt, amely bárhol alkalmazások telepíthetők. Ez a folyamat négy fő lépésből áll:

-   Adatok rejtjelezése tanúsítványának beszerzése.
-   A tanúsítvány telepítése a fürt.
-   Titkosítani a titkos értékek, a tanúsítvány az alkalmazás telepítésekor, és azokat behelyezése egy szolgáltatás Settings.xml konfigurációs fájlt.
-   Olvassa el a titkosított értékeket abból Settings.xml rejtjelezése ugyanazzal a tanúsítvánnyal visszafejti azokat.

>[!NOTE]
>További információ [Service Fabric-alkalmazások a titkos kulcsok kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Biztonsági házirendek beállítása az alkalmazáshoz
Azure Service Fabric biztonsági használatával segíthet a biztonságos alkalmazásokat, hogy a fürt a különböző felhasználói fiókok. Service Fabric biztonsági is lehetővé teszi az alkalmazások által használt felhasználói fiók – központi telepítés alkalmával például erőforrásokat, a fájlokat, a könyvtárak és a tanúsítványok biztonságos. Így futó alkalmazást, még akkor is, a megosztott üzemeltetési környezetben, nagyobb biztonságot nyújt.

A lépések az alábbiak:

-   A házirend egy szolgáltatás telepítője a belépési pont konfigurálása.
-   PowerShell-parancsok kezdve egy telepítő belépési ponthoz.
-   Konzol átirányítással helyi hibakereséshez.
-   A szolgáltatás kód csomagok házirend beállítása.
-   A HTTP és HTTPS-végpont biztonsági hozzáférési házirend hozzárendelése.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Azure Service Fabric biztonsági szolgáltatások biztonságos kommunikáció
A biztonság az egyik legfontosabb szempontja a kommunikáció. A Reliable Services alkalmazás-keretrendszer tartalmaz néhány előre elkészített kommunikációs verem és eszközöket, amelyek a biztonság növelése érdekében használható.

-   [Számítógépek biztonságossá tétele a szolgáltatás használatakor a távelérési szolgáltatás](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Számítógépek biztonságossá tétele a szolgáltatás egy WCF-alapú kommunikációs verem használatakor](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Következő lépések
- Fürt biztonsággal kapcsolatos általános információkért lásd: [a Service Fabric-fürt létrehozása az Azure Resource Manager használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) és [Azure-portálon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- A Service Fabric fürt biztonsággal kapcsolatos további tudnivalókért lásd: [Service Fabric-fürt biztonsági](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
