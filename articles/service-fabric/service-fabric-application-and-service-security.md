---
title: Tudnivalók az Azure Service Fabric Application Security szolgáltatásról | Microsoft Docs
description: Annak áttekintése, hogy miként futtathatók biztonságosan a Service-alkalmazások a Service Fabricon. Megtudhatja, hogyan futtathat szolgáltatásokat és indítási parancsfájlokat különböző biztonsági fiókok alatt, hogyan hitelesítheti és engedélyezheti a felhasználókat, kezelheti az alkalmazások titkait, gondoskodhat a biztonságos szolgáltatásokkal való kommunikációról, API-átjárót használhat, és biztonságossá teheti az alkalmazásokat
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: atsenthi
ms.openlocfilehash: 75a82a0915414d24ab9c58ea15d3fdc9c1922c63
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600071"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric alkalmazás és szolgáltatás biztonsága
A Service-architektúra [számos előnnyel](service-fabric-overview-microservices.md)járhat. A szolgáltatások biztonságának kezelése azonban kihívást jelent, és eltér a hagyományos monolit alkalmazások biztonságának kezelésével. 

A monolit esetében az alkalmazás általában egy vagy több kiszolgálón fut egy hálózaton belül, és könnyebben azonosítható a közzétett portok és API-k és IP-címek. Gyakran egy szegély vagy határ és egy adatbázis véd. Ha a rendszer biztonsági rések vagy támadás miatt sérül, akkor valószínű, hogy a rendszeren belül minden a támadó számára elérhető lesz. A szolgáltatásokkal a rendszer összetettebb.  A szolgáltatások decentralizáltak és különböző gazdagépekre oszlanak át, és a gazdagépről a gazdagépre migrálnak.  A megfelelő biztonsággal korlátozza a támadók által lekérhető és az egyetlen támadásban elérhető adatmennyiséget egy szolgáltatás megsértve.  A kommunikáció nem belső, hanem egy hálózaton keresztül történik, és számos elérhető port és interakció van a szolgáltatások között. Annak ismerete, hogy a szolgáltatás hogyan kommunikál, és ha igen, elengedhetetlenek az alkalmazás biztonsága szempontjából.

Ez a cikk nem a szolgáltatásokkal kapcsolatos biztonságra vonatkozó útmutató, ezért számos ilyen erőforrás online érhető el, de leírja, hogy a biztonság különböző szempontjait hogyan lehet elérni Service Fabric.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Gyakran szükséges, hogy a szolgáltatás által elérhető erőforrások és API-k bizonyos megbízható felhasználókra vagy ügyfelekre legyenek korlátozva. A hitelesítés a felhasználó identitásának megbízható megállapításának folyamata.  Az engedélyezés az a folyamat, amely API-kat vagy szolgáltatásokat tesz elérhetővé egyes hitelesített felhasználók számára, de másokat nem.

### <a name="authentication"></a>Authentication
Az API-szintű megbízhatósági döntések meghozatalának első lépése a hitelesítés. A hitelesítés a felhasználó identitásának megbízható megállapításának folyamata.  A szolgáltatással kapcsolatos forgatókönyvekben a hitelesítés általában központilag van kezelve. Ha API-átjárót használ, elvégezheti a [hitelesítés](/azure/architecture/patterns/gateway-offloading) kiszervezését az átjárón. Ha ezt a módszert használja, győződjön meg arról, hogy az egyes szolgáltatások nem érhetők el közvetlenül (az API-átjáró nélkül), kivéve, ha további biztonságra van szükség az üzenetek hitelesítéséhez, függetlenül attól, hogy az átjáróról származnak-e.

Ha a szolgáltatások közvetlenül érhetők el, egy olyan hitelesítési szolgáltatás, mint a Azure Active Directory, vagy egy biztonsági jogkivonat-szolgáltatásként (STS) működő dedikált hitelesítési szolgáltatás használható a felhasználók hitelesítéséhez. A megbízhatósági döntéseket a szolgáltatások a biztonsági jogkivonatokkal vagy cookie-kkal osztják meg. 

ASP.NET Core esetében a [felhasználók hitelesítésének](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) elsődleges mechanizmusa a ASP.net Core identitás-tagsági rendszer. ASP.NET Core identitás a fejlesztő által konfigurált adattárba menti a felhasználói adatokat (beleértve a bejelentkezési információkat, a szerepköröket és a jogcímeket). ASP.NET Core identitás támogatja a kétfaktoros hitelesítést.  A külső hitelesítő szolgáltatók is támogatottak, így a felhasználók a Microsoft, a Google, a Facebook vagy a Twitter szolgáltatásokból származó meglévő hitelesítési folyamatokat is bejelentkezhetnek.

### <a name="authorization"></a>Authorization
A hitelesítés után a szolgáltatásoknak engedélyeznie kell a felhasználói hozzáférést, vagy meg kell határozniuk a felhasználó által elvégezhető műveleteket. Ez a folyamat lehetővé teszi, hogy a szolgáltatás az API-kat néhány hitelesített felhasználó számára elérhetővé tegye, de nem mindegyikre. Az engedélyezés merőleges és független a hitelesítéstől, ami azt a folyamatot vizsgálja, hogy ki a felhasználó. Előfordulhat, hogy a hitelesítés egy vagy több identitást hoz létre az aktuális felhasználó számára.

[ASP.net Core hitelesítés](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) a felhasználói szerepkörök alapján vagy egyéni házirend alapján végezhető el, ami magában foglalhatja a jogcímek vagy más heurisztikus vizsgálatát is.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Hozzáférés korlátozása és biztonságossá tétele API-átjáró használatával
A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. Az [API-átjáró](/azure/architecture/microservices/gateway) az ügyfelek és a szolgáltatások között helyezkedik el, és a belépési pont az alkalmazás által biztosított összes szolgáltatáshoz. Fordított proxyként működik, az ügyfelektől a szolgáltatások felé irányuló útválasztási kérelmeket. Emellett különböző, több területet érintő feladatokat is végrehajthat, például a hitelesítést és az engedélyezést, az SSL-lezárást és a díjszabási korlátozást. Ha nem telepít átjárót, az ügyfeleknek közvetlenül kell elküldeni a kérelmeket az előtér-szolgáltatásoknak.

Service Fabric az átjáró bármilyen állapot nélküli szolgáltatás lehet, például egy [ASP.net Core alkalmazás](service-fabric-reliable-services-communication-aspnetcore.md)vagy egy másik, a bejövő forgalomra tervezett szolgáltatás, például [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT hub](https://docs.microsoft.com/azure/iot-hub/)vagy [Azure API Management](https://docs.microsoft.com/azure/api-management).

A API Management közvetlenül integrálható Service Fabricekkel, így lehetővé teszi az API-k széles körű útválasztási szabályokkal történő közzétételét a háttérbeli Service Fabric-szolgáltatásokhoz.  Biztonságos hozzáférést biztosíthat a háttér-szolgáltatásokhoz, megakadályozhatja a DOS-támadások szabályozását, vagy ellenőrizheti az API-kulcsokat, a JWT-jogkivonatokat, a tanúsítványokat és az egyéb hitelesítő adatokat. További információért olvassa el [Service Fabric az Azure API Management áttekintése](service-fabric-api-management-overview.md)című témakört.

## <a name="manage-application-secrets"></a>Titkos alkalmazáskulcsok kezelése
A titkok lehetnek bármilyen bizalmas információk, például a tárolási kapcsolatok karakterláncai, jelszavai vagy más olyan értékek, amelyeket nem szabad egyszerű szövegben kezelni. Ez a cikk a kulcsok és titkok kezeléséhez Azure Key Vault használ. Azonban az alkalmazásokban a titkos kódok *használatával* a felhőalapú platform-agnosztikus lehetővé teszi, hogy az alkalmazások a bárhol üzemeltetett fürtön legyenek telepítve.

A szolgáltatás konfigurációs beállításainak a [szolgáltatás konfigurációs csomagjain][config-package]keresztül történő kezelésének ajánlott módja. A konfigurációs csomagok verziója és frissítése a felügyelt működés közbeni frissítésekkel, az állapot-ellenőrzés és az automatikus visszaállítás használatával történik. Ez a globális konfigurációhoz javasolt, mivel ez csökkenti a globális szolgáltatás kimaradásának esélyét. A titkosított titkok nem kivételek. A Service Fabric beépített funkciókkal rendelkezik az értékek titkosításához és visszafejtéséhez a konfigurációs csomag beállításainak. XML fájljában a tanúsítvány titkosítása segítségével.

Az alábbi ábrán egy Service Fabric alkalmazás titkos felügyeletének alapszintű folyamata látható:

![Secret Management – áttekintés][overview]

A folyamat négy fő lépésből áll:

1. Szerezze be az titkosítási-tanúsítványt.
2. Telepítse a tanúsítványt a fürtben.
3. Titkosítsa a titkos értékeket, amikor egy alkalmazást telepít a tanúsítvánnyal, és beilleszti őket a szolgáltatás Settings. xml konfigurációs fájljába.
4. A titkosított értékeket a Settings. XML fájlból olvashatja, ha ugyanazzal a titkosítási-tanúsítvánnyal végez visszafejtést. 

A [Azure Key Vault][key-vault-get-started] a tanúsítványok biztonságos tárolási helyeként, valamint az Azure-beli Service Fabric-fürtökön telepített tanúsítványok beszerzésének módjaként használatos. Ha nem telepíti az Azure-t, nincs szükség a Key Vault használatára a titkok kezeléséhez Service Fabric alkalmazásokban.

Példaként tekintse meg az [alkalmazás-titkok kezelése](service-fabric-application-secret-management.md)című témakört.

## <a name="secure-the-hosting-environment"></a>Az üzemeltetési környezet biztonságossá tétele
Az Azure Service Fabric használatával a fürtben futó alkalmazások különböző felhasználói fiókokban is biztonságossá tehetők. A Service Fabric az alkalmazások által használt erőforrásokat is biztonságossá teszi a felhasználói fiókok – például a fájlok, a címtárak és a tanúsítványok – telepítésének időpontjában. Így még a megosztottan üzemeltetett környezetekben is elérhetővé válik az alkalmazások egymástól való biztonságosabbá tétele.

Az alkalmazás jegyzékfájlja kijelenti, hogy a szolgáltatás (ok) és a biztonságos erőforrások futtatásához szükséges rendszerbiztonsági tag (felhasználók és csoportok).  Ezek a rendszerbiztonsági tag a szabályzatokban hivatkozik, például a Futtatás, a végponti kötés, a csomag megosztása vagy a biztonsági hozzáférési házirendek.  Ezután a rendszer alkalmazza a házirendeket a szolgáltatási erőforrásokra az alkalmazás jegyzékfájljának **ServiceManifestImport** szakaszában.

A rendszerbiztonsági tag megadásakor létrehozhat és létrehozhat felhasználói csoportokat is, hogy az egyes csoportokhoz hozzá lehessen adni egy vagy több felhasználót, hogy azok együtt felügyelhetők legyenek. Ez akkor hasznos, ha több felhasználó van a különböző szolgáltatási belépési pontokhoz, és szükségük van bizonyos, a csoport szintjén elérhető általános jogosultságokra.

Alapértelmezés szerint Service Fabric alkalmazások a Fabric. exe folyamat alatt futó fiók alatt futnak. A Service Fabric lehetővé teszi az alkalmazások futtatását helyi felhasználói fiókkal vagy helyi rendszerfiókkal, amely az alkalmazás jegyzékfájljában van megadva. További információ: [szolgáltatás futtatása helyi felhasználói fiók vagy helyi rendszerfiók](service-fabric-application-runas-security.md).  [A szolgáltatás indítási parancsfájlját helyi felhasználóként vagy](service-fabric-run-script-at-service-startup.md)rendszerfiókként is futtathatja.

Ha Service Fabric futtat egy önálló Windows-fürtön, akkor [Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy csoportosan felügyelt szolgáltatásfiókok alatt [](service-fabric-run-service-as-gmsa.md)futtathat egy szolgáltatást.

## <a name="secure-containers"></a>Biztonságos tárolók
A Service Fabric a tárolón belüli szolgáltatások számára biztosít olyan tanúsítványt, amely a Windows vagy Linux rendszerű fürt csomópontjain telepített tanúsítványokhoz fér hozzá (5,7-es vagy újabb verzió). Ez a PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás hitelesítésére vagy más szolgáltatásokkal való biztonságos kommunikációra. További információ: [tanúsítvány importálása tárolóba](service-fabric-securing-containers.md).

Emellett a Service Fabric támogatja a Windows-tárolók gMSA (csoportosan felügyelt szolgáltatásfiókok) is. További információ: [set up gMSA for Windows containers](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>Biztonságos szolgáltatás kommunikációja
Service Fabric a szolgáltatás egy Service Fabric fürtön fut valahol, általában több virtuális gépen elosztva. Service Fabric számos lehetőséget biztosít a szolgáltatással kapcsolatos kommunikáció biztonságossá tételéhez.

Engedélyezheti a HTTPS-végpontokat a [ASP.net Core vagy a Java-](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webszolgáltatásokban.

Biztonságos kapcsolatot létesíthet a fordított proxy és a szolgáltatások között, így lehetővé válik a végpontok közötti biztonságos csatorna engedélyezése is. A biztonságos szolgáltatásokhoz való csatlakozás csak akkor támogatott, ha a fordított proxy a HTTPS-figyelésre van konfigurálva. A fordított proxy konfigurálásával kapcsolatos információkért olvassa el a [fordított proxy az Azure Service Fabricban](service-fabric-reverseproxy.md)című témakört.  A [biztonságos szolgáltatáshoz való csatlakozás](service-fabric-reverseproxy-configure-secure-communication.md) azt ismerteti, hogyan hozható létre biztonságos kapcsolat a fordított proxy és a szolgáltatások között.

A Reliable Services alkalmazás-keretrendszer néhány előre elkészített kommunikációs veremet és eszközt biztosít, amelyek segítségével javíthatja a biztonságot. Ismerje meg, hogyan javíthatja a biztonságot, ha a szolgáltatás távelérését ( [C#](service-fabric-reliable-services-secure-communication.md) vagy [javát](service-fabric-reliable-services-secure-communication-java.md)) vagy a [WCF](service-fabric-reliable-services-secure-communication-wcf.md)-t használja.

## <a name="encrypt-application-data-at-rest"></a>Alkalmazás-adatok titkosítása a nyugalmi állapotban
Az Azure-ban futó Service Fabric fürtök mindegyik [csomópont-típusát](service-fabric-cluster-nodetypes.md) egy virtuálisgép- [méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)támogatja. Egy Azure Resource Manager-sablonnal adatlemezeket csatlakoztathat a Service Fabric-fürtöt alkotó méretezési csoport(ok)hoz.  Ha a szolgáltatások egy csatolt adatlemezre mentik az adataikat [](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) , akkor az adatlemezeket titkosíthatja az alkalmazásadatok védelme érdekében.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Telepítési parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md)
* [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Tudnivalók a fürt biztonságáról](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png