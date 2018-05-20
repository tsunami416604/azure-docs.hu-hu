---
title: További tudnivalók az Azure Service Fabric-alkalmazás biztonságának |} Microsoft Docs
description: Mikroszolgáltatások alkalmazások biztonságos futtatásáról a Service Fabric áttekintése. Útmutató a különböző biztonsági fiókok szolgáltatások és az indítási parancsfájl futtatása, hitelesítéséhez és a felhasználóknak engedélyezik, alkalmazás titkos kulcsok kezelése, biztonságos szolgáltatáskommunikációs, API-átjáró, és biztonságos alkalmazásadatok használatához aktívan.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: ryanwi
ms.openlocfilehash: fa6d46186ad833b68e60c24f742d210b7845759a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-alkalmazás és szolgáltatás biztonsága
Egy mikroszolgáltatások architektúra átvihetők [számos előnyt](service-fabric-overview-microservices.md). Biztonsága érdekében mikroszolgáltatások létrehozására, kezelésére azonban a kérdés és különböznek a hagyományos egységes alkalmazások biztonságának kezelése. 

Az egy monolit az alkalmazás általában a hálózatban lévő egy vagy több kiszolgálón fut, és könnyebben azonosíthatja a kitett portok és API-k és az IP-cím. Nincs gyakran egy peremhálózati vagy határ, és egy adatbázis védelmét. Ha, hogy a rendszer sérült a biztonság megsértésére vagy támadás miatt, valószínű, hogy minden, a rendszerben elérhető lesz a támadónak. A mikroszolgáltatások létrehozására a rendszer összetett.  Szolgáltatások decentralizált, és sok állomások pontjain telepítse át a gazdagépre.  A megfelelő biztonsági beállíthatja a beszerezheti a támadó jogosultságot, és egyetlen támadás érhetők el adatok mennyisége egy szolgáltatás megsértése.  Kommunikáció nincs belső, de egy hálózaton keresztül történik, és sok kitett portok és szolgáltatások közötti kapcsolat. Ezek interakciókat vannak és amikor akkor fordulhat elő, hogy tudnák elengedhetetlen az alkalmazás biztonsági.

Ez a cikk nincs mikroszolgáltatások biztonsági útmutatóját, érhetők el sok erőforrást online, ismerteti, hogyan különböző vonatkozásai, de a Service Fabric is elvégezhető.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Gyakran szükség az erőforrások és a szolgáltatás által elérhetővé tett API egyes megbízható felhasználók vagy az ügyfelek kell korlátozni. A hitelesítés az a folyamat megbízható megismerjük a felhasználó identitását.  Engedélyezési a folyamat, amely lehetővé teszi az API-k vagy szolgáltatások számára elérhető egyes hitelesített felhasználók másokat nem.

### <a name="authentication"></a>Hitelesítés
Az első lépés az API-szintű megbízhatóság döntések hitelesítésre. A hitelesítés az a folyamat megbízható megismerjük a felhasználó identitását.  Mikroszolgáltatási esetekben hitelesítési általában kezelése központilag. Ha egy API-átjárót használ, akkor [hitelesítési kiszervezése](/azure/architecture/patterns/gateway-offloading) átjáróhoz. Ha ezt a módszert használja, győződjön meg arról, hogy az egyes szolgáltatások nem érhető el közvetlenül (az API-átjáró) nélkül kivéve, ha további biztonsági üzeneteket hitelesítő helyen e lépnek az átjáró-e.

Szolgáltatások közvetlenül is elérhetők, ha olyan hitelesítési szolgáltatás, például Azure Active Directory vagy egy dedikált hitelesítési mikroszolgáltatási nevében jár el a biztonsági jogkivonat-szolgáltatás (STS) segítségével hitelesíti a felhasználókat. Megbízhatósági döntések azokat a szolgáltatásokat, a biztonsági jogkivonatok vagy a cookie-k között vannak megosztva. 

Az ASP.NET Core, az elsődleges mechanizmus [felhasználók hitelesítéséhez](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) ASP.NET Core identitás tagsági rendszert. Az ASP.NET Core identitás állította be a fejlesztői adattárat felhasználói adatait (beleértve a bejelentkezési adatait, a szerepkörök és a jogcímek) tárolja. Az ASP.NET Core identitás kéttényezős hitelesítést támogatja.  Külső hitelesítésszolgáltatók is támogatja, így a már meglévő hitelesítési folyamatokat, például a Microsoft, a Google, a Facebook-on vagy a Twitteren szolgáltatóktól segítségével a felhasználók bejelentkezhetnek. 

### <a name="authorization"></a>Engedélyezés
A hitelesítés után szolgáltatások kell felhasználói hozzáférés engedélyezésére, vagy felfedheti, milyen a felhasználó személyazonosságának tudja megtenni. Ez a folyamat egy szolgáltatást, hogy a API-k lehetővé teszi a hitelesített felhasználók számára elérhető, de nem az összes. Az engedélyezés merőleges és független a hitelesítést, amely az a folyamat megismerjük a felhasználó, aki történik. Hitelesítés az aktuális felhasználó egy vagy több identitást hozhat létre.

[Az ASP.NET Core engedélyezési](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) a felhasználói szerepkörök alapján történik, vagy egyéni házirend, beleértve például jogcímeket vagy más heurisztikus vizsgálata alapján.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Korlátozza és a biztonságos hozzáférés az API-átjáró használatával
A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. Egy [API átjáró](/azure/architecture/microservices/gateway) ügyfelek és a szolgáltatások között helyezkedik el, és a belépési pont az összes szolgáltatáshoz, az alkalmazás biztosítja. Úgy működik, mint egy fordított proxy services ügyfelek útválasztási kérelmeit. Több területet különböző feladatokat, mint a hitelesítési és engedélyezési, SSL-lezárást és sebességkorlátozást azt is végrehajthatja. Ha nem telepít egy átjárót, az ügyfelek kell kérelmet küldeni közvetlenül előtér-szolgáltatásokat.

A Service Fabric átjáró lehet bármely állapotmentes szolgáltatások, mint egy [ASP.NET Core alkalmazás](service-fabric-reliable-services-communication-aspnetcore.md), vagy egy másik szolgáltatás, amely a forgalom érkező, például a [Træfik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT-központ](https://docs.microsoft.com/azure/iot-hub/), vagy [az Azure API Management](https://docs.microsoft.com/azure/api-management).

Az API Management közvetlenül integrálható a Service Fabric, lehetővé téve az API-k közzététele az útválasztási szabályokat a Service Fabric háttérszolgáltatások széles skáláját.  Háttér-szolgáltatásokhoz való hozzáférés biztonságos, sávszélesség-szabályozás használatával Szolgáltatásmegtagadási támadások megelőzése érdekében vagy API kulcsok, JWT jogkivonatokat, tanúsítványok és egyéb hitelesítő adatok ellenőrzése. További tudnivalókért olvassa el a [Service Fabric Azure API Management áttekintése](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Titkos alkalmazáskulcsok kezelése
Titkos kulcsok lehet bármely bizalmas adatokat, például a tárolási kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem egyszerű szöveges kezelje. Ez a cikk az Azure Key Vault használatával kulcsokat és titkos kódokat. Azonban *használatával* egy alkalmazás titkos kulcsainak van cloud platform-független bárhol üzemeltetett fürt központilag telepített alkalmazások.

Az ajánlott módszer a szolgáltatás konfigurációs beállítások kezeléséhez van keresztül [szolgáltatás konfigurációs csomagok][config-package]. Konfigurációs csomagok a következők: rendszerverzióval ellátott és a rendszerállapot-érvényesítési és automatikus visszaállítási felügyelt közbeni keresztül frissíthető. Ez az előnyben részesített a globális konfigurációs, mivel csökkenti a veszélyét annak, hogy a globális szolgáltatáskimaradás. Titkosított titkokat sem kivétel. A Service Fabric titkosítására és visszafejtésére értékek a tanúsítvány titkosítással konfigurációs csomag Settings.xml fájlban beépített lehetőséggel rendelkezik.

A következő ábra szemlélteti a titkos kezelése a Service Fabric-alkalmazás általános folyamata:

![titkos – áttekintés][overview]

Ez a folyamat négy fő lépésből áll:

1. Adatok rejtjelezése tanúsítványának beszerzése.
2. Telepítse a tanúsítványt a fürtön.
3. Titkosítani a titkos értékek, a tanúsítvány az alkalmazás telepítésekor, és azokat behelyezése egy szolgáltatás Settings.xml konfigurációs fájlt.
4. Olvassa el a titkosított értékeket abból Settings.xml visszafejti rejtjelezése ugyanazzal a tanúsítvánnyal. 

[Az Azure Key Vault] [ key-vault-get-started] itt történik, a tanúsítványok biztonságos tárolási helyeként, valamint az Azure Service Fabric-fürtök telepített tanúsítványok lekérése is. Ha nem telepíti az Azure-ba, nem kell Key Vault segítségével kezelheti a Service Fabric-alkalmazások a titkos kulcsok.

Egy vonatkozó példáért lásd: [alkalmazás titkos kulcsok kezelése](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Az üzemeltetési környezetben biztonságos
Azure Service Fabric használatával biztonságossá teheti a különböző felhasználói fiókok a fürtben futó alkalmazás számára. A Service Fabric is lehetővé teszi az alkalmazások által használt felhasználói fiók – központi telepítés alkalmával például erőforrásokat, a fájlokat, a könyvtárak és a tanúsítványok biztonságos. Így futó alkalmazást, még akkor is, a megosztott üzemeltetési környezetben, nagyobb biztonságot nyújt, egymástól.

Az alkalmazás jegyzékében kijelenti, hogy a rendszerbiztonsági tagok (felhasználókat és csoportokat) szükséges szolgáltatásokat és biztonságos erőforrások futtatásához.  Ezen rendszerbiztonsági tagok hivatkozott házirendek, például a futtató,-végpont kötése, biztonsági csomag megosztása, vagy a biztonsági hozzáférési házirendek.  Házirendek vonatkoznak szolgáltatások erőforrásait a **ServiceManifestImport** szakasza az alkalmazás jegyzékében.

Rendszerbiztonsági tagok deklaráló, amikor is határozza meg, és hozzon létre felhasználói csoportokat, hogy egy vagy több felhasználó is adható hozzá mindegyik csoport felügyelete együtt. Ez akkor hasznos, ha több felhasználó különböző belépési pontot, és bizonyos közös jogosultságok, amelyek elérhetők a csoportok szintjén van szükségük.

Alapértelmezés szerint a Service Fabric alkalmazások futnak, a fiók, amely alatt futó a Fabric.exe folyamatban. A Service Fabric is lehetővé teszi a helyi felhasználói fiók vagy helyi rendszerfiók, az alkalmazás jegyzékében belül megadott alkalmazások futtatásához. További információkért lásd: [futtatni a szolgáltatást egy helyi felhasználói fiók vagy helyi rendszer fiókként](service-fabric-application-runas-security.md).  Emellett [szolgáltatás indítási parancsfájlt futtató helyi felhasználói vagy rendszer fiók](service-fabric-run-script-at-service-startup.md).

Jelenik meg a Service Fabric Windows önálló fürtön, a szolgáltatás futtatható [Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoport felügyelt szolgáltatásfiókok](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Biztonságos tárolók
A Service Fabric lehetővé teszi a szolgáltatások eléréséhez a Windows vagy Linux rendszerű fürt (5.7-es vagy újabb verzió) csomópontján telepített tanúsítvány tárolója belül. A PFX-tanúsítvány az alkalmazás vagy szolgáltatás vagy egyéb szolgáltatások biztonságos kommunikációt hitelesítésére is használható. További információkért lásd: [tanúsítvány importálása egy tárolóba](service-fabric-securing-containers.md).

Emellett a Service Fabric is támogatja csoportosan felügyelt szolgáltatásfiók (csoportosan felügyelt szolgáltatásfiókok) Windows tárolók. További információkért lásd: [Windows tárolók csoportosan felügyelt szolgáltatásfiók beállítása](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Szolgáltatások közötti kommunikáció biztonságos
A Service Fabric szolgáltatás fut. valahol a Service Fabric-fürt, általában pontjain több virtuális géphez A Service Fabric a szolgáltatás kommunikáció biztosításához számos lehetőséget biztosít.

Engedélyezheti a HTTPS-végpontnak a [ASP.NET Core vagy a Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webszolgáltatásokat.

A fordított proxy és a szolgáltatások közötti biztonságos kapcsolatot létesíthetnek, így egy végpontok közötti biztonságos csatorna. Biztonságos szolgáltatásokhoz való kapcsolódás esetén támogatott csak fordított proxy a figyelést a HTTPS van konfigurálva. A fordított proxy beállításáról, olvassa el a [fordított proxy az Azure Service Fabric](service-fabric-reverseproxy.md).  [Csatlakozzon egy biztonságos szolgáltatáshoz](service-fabric-reverseproxy-configure-secure-communication.md) ismerteti, hogyan lehet a fordított proxy és a szolgáltatások közötti biztonságos kapcsolatot létrehozni.

A Reliable Services alkalmazás-keretrendszer tartalmaz néhány előre elkészített kommunikációs verem és eszközöket, amelyek a biztonság növelése érdekében használhatja. Útmutató: biztonsági javítják a szolgáltatás távoli eljáráshívás használata (a [C#](service-fabric-reliable-services-secure-communication.md) vagy [Java](service-fabric-reliable-services-secure-communication-java.md)) vagy [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Alkalmazás inaktív adatok titkosítása
Minden egyes [csomóponttípus](service-fabric-cluster-nodetypes.md) a Service Fabric-fürt az Azure-beli biztonsági egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Egy Azure Resource Manager-sablonnal adatlemezeket csatlakoztathat a Service Fabric-fürtöt alkotó méretezési csoport(ok)hoz.  Ha a szolgáltatások adatok mentése a mellékelt adatok lemezre, akkor [adatok lemezek titkosítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) az alkalmazásadatok védelme érdekében.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [A telepítő parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md)
* [Adja meg a szolgáltatás jegyzék erőforrások](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Fürt biztonság megismerése](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png