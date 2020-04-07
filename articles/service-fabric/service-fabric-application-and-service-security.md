---
title: További információ az Azure Service Fabric alkalmazásbiztonságáról
description: A mikroszolgáltatások alkalmazásainak biztonságos futtatásának áttekintése a Service Fabricen. Megtudhatja, hogy miként futtathat szolgáltatásokat és indítási parancsfájlokat különböző biztonsági fiókok alatt, hogyan hitelesítheti és engedélyezheti a felhasználókat, kezelheti az alkalmazástitkokat, nem biztonságos a szolgáltatáskommunikáció, hogyan használhat API-átjárót, és hogyan biztosíthatja az inaktív alkalmazásadatokat.
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: e9b4a1209838bdd5eee401b0defb01839b5cf684
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756232"
---
# <a name="service-fabric-application-and-service-security"></a>A Service Fabric alkalmazás- és szolgáltatásbiztonsága
A mikroszolgáltatások architektúrája [számos előnnyel járhat.](service-fabric-overview-microservices.md) A mikroszolgáltatások biztonságának kezelése azonban kihívást jelent, és különbözik a hagyományos monolitikus alkalmazások biztonságának kezelésétől. 

Monolit esetén az alkalmazás általában egy vagy több hálózaton fut, és könnyebb azonosítani a kitett portokat és API-kat és IP-címet. Gyakran van egy kerület vagy határ, és egy adatbázis védelme. Ha a rendszer biztonsága megsértése vagy támadás miatt sérül, akkor valószínű, hogy a rendszeren belül minden elérhető lesz a támadó számára. A mikroszolgáltatások, a rendszer összetettebb.  A szolgáltatások decentralizáltak és sok állomás között oszlanak meg, és gazdagépről állomásra vándorolnak.  A megfelelő biztonság gal, korlátozza a támadók által kapható jogosultságokat és az egyetlen támadás során rendelkezésre álló adatok mennyiségét egy szolgáltatás megsértésével.  A kommunikáció nem belső, hanem hálózaton keresztül történik, és sok a kitett port és a szolgáltatások közötti interakció. Az alkalmazás biztonsága szempontjából döntő fontosságú, hogy tudja, mik ezek a szolgáltatásinterakciók, és mikor történnek.

Ez a cikk nem egy útmutató a mikroszolgáltatások biztonsága, sok ilyen erőforrás érhető el az interneten, de ismerteti, hogyan különböző biztonsági szempontokkal lehet elérni a Service Fabric.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Gyakran szükséges, hogy a szolgáltatás által elérhetővé tett erőforrások és API-k bizonyos megbízható felhasználókra vagy ügyfelekre korlátozódjanak. A hitelesítés a felhasználó identitásának megbízható megállapítása.  Az engedélyezés az a folyamat, amely elérhetővé teszi az API-kat vagy szolgáltatásokat egyes hitelesített felhasználók számára, mások számára azonban nem.

### <a name="authentication"></a>Hitelesítés
Az API-szintű megbízhatósági döntések meghozatalának első lépése a hitelesítés. A hitelesítés a felhasználó identitásának megbízható megállapítása.  Mikroszolgáltatási forgatókönyvekben a hitelesítés általában központilag történik. Ha EGY API-átjárót használ, [kiszervezheti](/azure/architecture/patterns/gateway-offloading) a hitelesítést az átjáróba. Ha ezt a módszert használja, győződjön meg arról, hogy az egyes szolgáltatások nem érhető el közvetlenül (az API-átjáró nélkül), kivéve, ha további biztonsági van érvényben az üzenetek hitelesítéséhez, függetlenül attól, hogy az átjáróról érkeznek-e vagy sem.

Ha a szolgáltatások közvetlenül elérhetők, egy hitelesítési szolgáltatás, például az Azure Active Directory vagy egy dedikált hitelesítési mikroszolgáltatás biztonsági jogkivonat-szolgáltatásként (STS) használható a felhasználók hitelesítéséhez. A megbízhatósági döntéseket a szolgáltatások biztonsági jogkivonatok vagy cookie-k osztják meg. 

A ASP.NET Core számára a [felhasználók hitelesítésének](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) elsődleges mechanizmusa a ASP.NET Core Identity tagsági rendszer. ASP.NET Core Identity a felhasználói adatokat (beleértve a bejelentkezési adatokat, szerepköröket és jogcímeket) a fejlesztő által konfigurált adattárban tárolja. ASP.NET Core Identity támogatja a kétfaktoros hitelesítést.  A külső hitelesítésszolgáltatók is támogatottak, így a felhasználók a Microsoft, a Google, a Facebook vagy a Twitter szolgáltatótól származó meglévő hitelesítési folyamatok használatával jelentkezhetnek be.

### <a name="authorization"></a>Engedélyezés
A hitelesítés után a szolgáltatásoknak engedélyezniük kell a felhasználói hozzáférést, vagy meg kell határozniuk, hogy a felhasználó mire képes. Ez a folyamat lehetővé teszi, hogy egy szolgáltatás elérhetővé tegye az API-kat néhány hitelesített felhasználó számára, de nem mindenki számára. Az engedélyezés ortogonális és független a hitelesítéstől, ami a felhasználó megállapításának folyamata. A hitelesítés egy vagy több identitást hozhat létre az aktuális felhasználó számára.

[ASP.NET Az alapengedélyezés](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) a felhasználói szerepkörök vagy az egyéni házirend alapján végezhető el, amely magában foglalhatja a jogcímek vagy más heurisztika vizsgálatát.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Hozzáférés korlátozása és biztonságossá tétele API-átjáró használatával
A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. Az [API-átjáró](/azure/architecture/microservices/gateway) az ügyfelek és a szolgáltatások között helyezkedik el, és az alkalmazás által nyújtott összes szolgáltatás belépési pontja. Fordított proxyként működik, és az ügyfelektől a szolgáltatásokhoz irányítja a kérelmeket. Emellett különböző, több területet érintő feladatokat is elvégezhet, például hitelesítést és engedélyezést, TLS-végződtetést és sebességkorlátozást. Ha nem telepít iszlamistát, az ügyfeleknek közvetlenül az előtér-szolgáltatásoknak kell kéréseket küldeniük.

A Service Fabric-ben az átjáró bármilyen állapotnélküli szolgáltatás lehet, például egy [ASP.NET Core alkalmazás,](service-fabric-reliable-services-communication-aspnetcore.md)vagy egy másik, forgalomforgalom-forgalomhoz tervezett szolgáltatás, például [a Traefik,](https://docs.traefik.io/) [az Event Hubs,](https://docs.microsoft.com/azure/event-hubs/)az [IoT Hub](https://docs.microsoft.com/azure/iot-hub/)vagy az Azure API [Management.](https://docs.microsoft.com/azure/api-management)

Az API Management közvetlenül integrálható a Service Fabric, amely lehetővé teszi, hogy api-k at közzé tesz a háttér-Service Fabric-szolgáltatások számos útválasztási szabályok at.  Biztonságos hozzáférést biztosíthat a háttérszolgáltatásokhoz, megakadályozhatja a DOS-támadásokat a szabályozás használatával, vagy ellenőrizheti az API-kulcsokat, jwt-jogkivonatokat, tanúsítványokat és egyéb hitelesítő adatokat. További információért olvassa el a [Service Fabric és az Azure API Management áttekintéscímű témakört.](service-fabric-api-management-overview.md)

## <a name="manage-application-secrets"></a>Titkos alkalmazáskulcsok kezelése
A titkos kulcsok lehetnek bizalmas információk, például tárolási kapcsolati karakterláncok, jelszavak vagy más értékek, amelyeket nem szabad egyszerű szövegként kezelni. Ez a cikk az Azure Key Vault kulcs- és titkos kulcsok kezelésére használja. Azonban egy alkalmazás titkos titkainak *használata* felhőplatform-független, amely lehetővé teszi az alkalmazások üzembe helyezését egy fürtre bárhol üzemeltetett.

A szolgáltatáskonfigurációs beállítások kezelésének ajánlott módja a [szolgáltatáskonfigurációs csomagok.][config-package] A konfigurációs csomagok verziószámmal és frissíthetők a felügyelt működés közbeni frissítésekkel, állapot-ellenőrzéssel és automatikus visszaállítással. Ez előnyös a globális konfiguráció, mivel csökkenti a globális szolgáltatáskimaradás esélyét. A titkosított titkok sem kivételek. A Service Fabric beépített szolgáltatásokkal rendelkezik a Settings.xml fájl ban lévő értékek titkosításához és visszafejtéséhez tanúsítványtitkosítással.

Az alábbi ábra a titkos kezelés alapfolyamatát mutatja be egy Service Fabric-alkalmazásban:

![titkos kezelés – áttekintés][overview]

Ebben a folyamatban négy fő lépés van:

1. Adattitkosítási tanúsítvány beszerzése.
2. Telepítse a tanúsítványt a fürtbe.
3. Titkos értékek titkosítása, amikor egy alkalmazást a tanúsítvánnyal telepít, és behelyezi őket a szolgáltatás Settings.xml konfigurációs fájljába.
4. Olvassa be a titkosított értékeket a Settings.xml fájlból ugyanazzal a titkosítási tanúsítvánnyal történő visszafejtéssel. 

[Az Azure Key Vault][key-vault-get-started] itt a tanúsítványok biztonságos tárolóhelyeként és az Azure-beli Service Fabric-fürtökre telepített tanúsítványok leszállításának egyik módjaként használatos. Ha nem telepíti az Azure-ba, nem kell használnia a Key Vault a Service Fabric-alkalmazások titkos kulcsok kezeléséhez.

Például az [Alkalmazástitok kezelése című témakörben.](service-fabric-application-secret-management.md)

## <a name="secure-the-hosting-environment"></a>Az üzemeltetési környezet biztosítása
Az Azure Service Fabric használatával biztonságossá teheti a fürtben különböző felhasználói fiókok alatt futó alkalmazásokat. A Service Fabric is segít az alkalmazások által a felhasználói fiókok – például fájlok, könyvtárak és tanúsítványok – központi telepítésekor használt erőforrások biztonságossá tétele. Ez még egy megosztott üzemeltetett környezetben is biztonságosabbá teszi az alkalmazások futtatását egymástól.

Az alkalmazásjegyzék deklarálja a szükséges rendszerbiztonsági tagokat (felhasználókat és csoportokat) a szolgáltatás(ok) futtatásához és a biztonságos erőforrásokhoz.  Ezekre a rendszerbiztonsági tagokra a házirendek hivatkoznak, például a futtatási, végpontkötési, csomagmegosztási vagy biztonsági hozzáférési házirendekben.  A házirendek ezután az alkalmazásjegyzék **ServiceManifestImport** szakaszában lévő szolgáltatás-erőforrásokra lesznek alkalmazva.

A rendszerbiztonsági tagok deklarálásakor felhasználói csoportokat is definiálhat és hozhat létre, hogy minden csoporthoz egy vagy több felhasználó takarható együtt. Ez akkor hasznos, ha több felhasználó van különböző szolgáltatásbelépési pontokhoz, és rendelkezniük kell bizonyos közös jogosultságokkal, amelyek a csoport szintjén érhetők el.

Alapértelmezés szerint a Service Fabric-alkalmazások a Fabric.exe folyamat alatt futó fiók alatt futnak. A Service Fabric azt is lehetővé teszi, hogy alkalmazásokat futtasson egy helyi felhasználói fiók vagy helyi rendszerfiók, amely az alkalmazásjegyzékben van megadva. További információt a [Szolgáltatás futtatása helyi felhasználói fiókként vagy helyi rendszerfiókként című témakörben talál.](service-fabric-application-runas-security.md)  [A szolgáltatás indítási parancsfájlját helyi felhasználóként vagy rendszerfiókként](service-fabric-run-script-at-service-startup.md)is futtathatja.

Ha a Service Fabric szolgáltatást önálló Windows-fürtön futtatja, egy szolgáltatást [az Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoportfelügyelt szolgáltatásfiókok](service-fabric-run-service-as-gmsa.md)alatt futtathat.

## <a name="secure-containers"></a>Biztonságos tárolók
A Service Fabric egy olyan mechanizmust biztosít a tárolón belüli szolgáltatások számára, amelyek egy Windows vagy Linux-fürt (5.7-es vagy újabb verzió) csomópontjaira telepített tanúsítványok eléréséhez érhetők el. Ez a PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás hitelesítésére vagy más szolgáltatásokkal való biztonságos kommunikációra. További információt a [Tanúsítvány importálása tárolóba című témakörben talál.](service-fabric-securing-containers.md)

Emellett a Service Fabric is támogatja a gMSA (csoport felügyelt szolgáltatásfiókok) a Windows-tárolók. További információt a [GMSA beállítása Windows-tárolókhoz című témakörben talál.](service-fabric-setup-gmsa-for-windows-containers.md)

## <a name="secure-service-communication"></a>Biztonságos szolgáltatáskommunikáció
A Service Fabric egy szolgáltatás fut valahol egy Service Fabric-fürt, általában több virtuális gép között elosztva. A Service Fabric számos lehetőséget biztosít a szolgáltatáskommunikáció védelmére.

Https-végpontokat engedélyezhet a [ASP.NET Core vagy a Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webszolgáltatásokban.

Biztonságos kapcsolatot hozhat létre a fordított proxy és a szolgáltatások között, így lehetővé téve a végpontok közötti biztonságos csatornát. A biztonságos szolgáltatásokhoz való csatlakozás csak akkor támogatott, ha a fordított proxy https-kapcsolaton történő figyelésre van beállítva. A fordított proxy konfigurálásáról az [Azure Service Fabric Fordított proxy című témakörében](service-fabric-reverseproxy.md)olvashat.  [A biztonságos szolgáltatáshoz való csatlakozás](service-fabric-reverseproxy-configure-secure-communication.md) azt ismerteti, hogyan lehet biztonságos kapcsolatot létesíteni a fordított proxy és a szolgáltatások között.

A Megbízható szolgáltatások alkalmazáskeretrendszer néhány előre összeállított kommunikációs veremeket és eszközöket biztosít, amelyek a biztonság növelése érdekében használhatók. Megtudhatja, hogy miként növelheti a biztonságot a szolgáltatás átirányító használata [(C#](service-fabric-reliable-services-secure-communication.md) vagy [Java](service-fabric-reliable-services-secure-communication-java.md)nyelven) vagy [a WCF használatakor.](service-fabric-reliable-services-secure-communication-wcf.md)

## <a name="encrypt-application-data-at-rest"></a>Az inaktív alkalmazásadatok titkosítása
Az Azure-ban futó Service Fabric-fürt minden [csomóponttípusát](service-fabric-cluster-nodetypes.md) egy [virtuálisgép-méretezési csoport támogatja.](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) Egy Azure Resource Manager-sablonnal adatlemezeket csatlakoztathat a Service Fabric-fürtöt alkotó méretezési csoport(ok)hoz.  Ha a szolgáltatások adatokat mentenek egy csatolt adatlemezre, az alkalmazásadatok védelme érdekében [titkosíthatja ezeket az adatlemezeket.](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md)

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Telepítési parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md)
* [Erőforrások megadása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [További információ a fürtbiztonságról](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png