---
title: További tudnivalók az Azure Service Fabric-alkalmazás biztonságának |} A Microsoft Docs
description: Biztonságos futtatásáról a mikroszolgáltatás-alapú alkalmazások a Service Fabric áttekintése. Útmutató a fiókok különböző biztonsági szolgáltatások és az indítási szkript futtatásához, hitelesítést és használatának engedélyezése felhasználók, titkos alkalmazáskulcsok kezelése, biztonságos kommunikáció a szolgáltatások között, egy API-átjáró és a biztonságos alkalmazásadatok használja aktívan.
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
ms.openlocfilehash: f83f7afa4173316f127c76f20967054bf13c9a6b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097906"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric-alkalmazás és szolgáltatás biztonsága
A mikroszolgáltatási architektúra is átviheti [számos előnnyel jár](service-fabric-overview-microservices.md). Viszont, mikroszolgáltatások, a biztonság kezelése, a kérdés, és eltér a hagyományos monolitikus alkalmazások biztonság kezelése. 

A mikroszolgáltatásokká az alkalmazás általában egy hálózaton belül egy vagy több kiszolgálón fut, és könnyebben azonosíthatja a elérhetővé tett port és az API-k és az IP-cím. Gyakran van egy szegélyhálózat-alapú vagy a határ és a egy adatbázis védelme érdekében. Ha a rendszer a biztonsági szabályzat megsértése vagy támadások miatt integritása sérül, valószínű, hogy minden, a rendszeren belül lesz elérhető a támadónak. A mikroszolgáltatásokat a rendszer összetettebb.  Szolgáltatások decentralizált, és számos gazdagépek elosztva és telepítse át a gazdagépre.  A megfelelő biztonsági beállíthatja a kérheti le a támadó jogosultságot és a egy egyetlen támadás elérhető adat mennyisége meghalad egy szolgáltatást.  Kommunikáció nem belső, de a hálózaton keresztül történik, és sok elérhetővé tett port és a szolgáltatások közötti interakciókat. E szolgáltatás kapcsolati vannak, és azokat mikor elengedhetetlen az alkalmazás biztonsági.

Ez a cikk nem mikroszolgáltatás-alapú biztonsági útmutatóját, számos forrásanyag létezik ilyen elérhető online, ismerteti a különböző biztonsági szempontból valósítható meg a Service Fabricben.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés
Gyakran szükség az erőforrások és a szolgáltatás által elérhetővé tett API csak bizonyos megbízható felhasználók vagy az ügyfelek számára. Hitelesítés az a folyamat megbízhatóan megismerjük a felhasználó identitását.  Engedély a folyamatot, amely lehetővé teszi az API-k vagy a szolgáltatások számára elérhető egyes hitelesített felhasználók, de nem más.

### <a name="authentication"></a>Hitelesítés
Az első lépés az API-szintű megbízhatóság döntéseket hitelesítésre. Hitelesítés az a folyamat megbízhatóan megismerjük a felhasználó identitását.  A mikroszolgáltatás-környezetben hitelesítési általában kezelése központilag. Ha egy API-átjárót használ, akkor [hitelesítési kiürítési](/azure/architecture/patterns/gateway-offloading) az átjáróhoz. Ha ezt a módszert használja, ügyeljen arra, hogy az egyes szolgáltatások nem érhető el közvetlenül (az API-átjáró) nélkül, kivéve, ha további biztonsági üzenetek hitelesítésére helyen e származnak az átjáró-e.

Szolgáltatások közvetlenül is elérhetők, ha olyan hitelesítési szolgáltatás, például az Azure Active Directory vagy egy dedikált hitelesítési mikroszolgáltatások és a egy biztonsági jogkivonat-szolgáltatás (STS) segítségével hitelesítheti a felhasználókat. Megbízható döntéseket hozhat a biztonsági jogkivonatok vagy cookie-k szolgáltatások között vannak megosztva. 

Az ASP.NET Core, az elsődleges mechanizmusa [felhasználók hitelesítéséhez](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/) az ASP.NET Core identitás tagsági rendszer. ASP.NET Core-identitását egy adattárba, a fejlesztő által konfigurált felhasználói információk (beleértve a bejelentkezési adatait, a szerepkörök és a jogcímek) tárolja. ASP.NET Core-identitás kéttényezős hitelesítést támogatja.  Külső hitelesítésszolgáltatók is támogatja, így a felhasználók bejelentkezhetnek meglévő hitelesítési folyamatokat, például a Microsoft, a Google, Facebook vagy a Twitter segítségével. 

### <a name="authorization"></a>Engedélyezés
A hitelesítés után szolgáltatásokat kell felhasználói hozzáférés engedélyezéséhez, vagy felfedheti, mely egy felhasználó tagja tudja megtenni. Ez a folyamat lehetővé teszi, hogy a szolgáltatás API-k néhány hitelesített felhasználók számára elérhető, de nem minden. Engedélyezési merőleges és független a hitelesítést, amely a folyamat a felhasználók, akik megismerjük. Hitelesítés az aktuális felhasználó egy vagy több identitást hozhat létre.

[ASP.NET Core engedélyezési](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications) felhasználói szerepkörök alapján történik, vagy egyéni szabályzatot, amely tartalmazhat jogcímeket vagy más heurisztikus vizsgálata alapján.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>Korlátozza és a biztonságos hozzáférés az API-átjáró használatával
A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. Egy [API-átjáró](/azure/architecture/microservices/gateway) ügyfelek és a szolgáltatások között helyezkedik el, és a belépési pont az összes olyan szolgáltatás, amely az alkalmazás biztosít. Fordított proxy, útválasztási szolgáltatások ügyfelektől érkező kérelmek funkcionál. Emellett végrehajthatnak, például a hitelesítési és engedélyezési, SSL-lezárást és a sebességkorlátozás különböző általános feladatok. Ha nem telepít egy átjárót, az ügyfelek kell küldenie kérések közvetlenül előtér-szolgáltatásokat.

A Service Fabricben átjáró lehet bármely állapotmentes szolgáltatás például egy [ASP.NET Core-alkalmazás](service-fabric-reliable-services-communication-aspnetcore.md), vagy egy másik szolgáltatás, például a bejövő forgalomra tervezett [Traefik](https://docs.traefik.io/), [az Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Az IoT Hub](https://docs.microsoft.com/azure/iot-hub/), vagy [az Azure API Management](https://docs.microsoft.com/azure/api-management).

Az API Management közvetlenül integrálható a Service Fabric lehetővé teszi, hogy tegye közzé az API-k az útválasztási szabályokat a háttér-Service Fabric-szolgáltatások széles skáláját.  Biztonságos hozzáférés a háttérszolgáltatásokat, szabályozás használatával Szolgáltatásmegtagadási támadások megelőzése, vagy ellenőrizze API kulcsokat, JWT jogkivonatokat, tanúsítványokat és más hitelesítő adatokat. További tudnivalókért olvassa el a [Service Fabric és Azure API Management áttekintése](service-fabric-api-management-overview.md).

## <a name="manage-application-secrets"></a>Titkos alkalmazáskulcsok kezelése
Titkos kódok lehet a bizalmas adatokat, például a storage kapcsolati karakterláncok, jelszavak és egyéb értékek, amelyek nem szövegként kezelje. Ez a cikk az Azure Key Vault használja a kulcsok és titkos kulcsok kezeléséhez. Azonban *használatával* egy alkalmazás titkos kulcsainak van cloud platform-agnosztikus üzemeltetett fürt központilag telepített alkalmazások.

Az ajánlott módszer a szolgáltatás konfigurációs beállítások kezeléséhez van keresztül [szolgáltatás konfigurációs csomagokat][config-package]. Konfigurációs csomagokat is a rendszerverzióval ellátott és frissíthető kurzoron keresztül az állapot-ellenőrzési és automatikus visszaállítási felügyelt működés közbeni frissítése. Ez a globális konfiguráció előnyben részesített, mivel ez csökkenti az esélyét, a globális szolgáltatás-kimaradás. A titkosított titkos kulcsok sem jelentenek kivételt. A Service Fabric beépített funkciók titkosítása és visszafejtése az értékeket a tanúsítvány titkosítással konfigurációs csomag Settings.xml fájlban van.

A következő ábra szemlélteti a titkos kódok kezelése a Service Fabric-alkalmazások általános folyamata:

![titkos kódok kezelése – áttekintés][overview]

Ez a folyamat a négy fő lépésből áll:

1. Szerezzen be egy adatok titkosítási tanúsítványt.
2. Telepítse a tanúsítványt a fürtben.
3. Titkosítani a titkos értékkel, a tanúsítvány az alkalmazás üzembe helyezésekor, majd helyezze azokat egy szolgáltatás Settings.xml konfigurációs fájlt.
4. Olvassa el a titkosított értékeket Settings.xml visszafejti a ugyanazt a titkosítási tanúsítványt. 

[Az Azure Key Vault] [ key-vault-get-started] itt történik, a tanúsítványok biztonságos tárolási helyként, valamint arra, hogy a Service Fabric-fürtök az Azure-ban telepített tanúsítványok beolvasása. Ha nem telepíti az Azure-ba, nem kell a titkos kulcsokat a Service Fabric-alkalmazások kezelése a Key Vault használatával.

Egy vonatkozó példáért lásd: [titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md).

## <a name="secure-the-hosting-environment"></a>Az üzemeltetési környezet biztonságossá tétele
Az Azure Service Fabric használatával gondoskodhat a különböző felhasználói fiókok a fürtben futó alkalmazásokat. A Service Fabric emellett segít az erőforrások által használt alkalmazások időpontjában üzemelő példányt a felhasználói fiókok – például, fájlok, könyvtárak és tanúsítványok védelmét. Ez lehetővé teszi futó alkalmazások még megosztott környezetben üzemeltetett, egy biztonságosabb.

Az alkalmazásjegyzék deklarálja, hogy a rendszerbiztonsági tagok (felhasználók és csoportok) szükséges futtatni a szolgáltatás(ok) és biztonságos erőforrásokhoz.  Ezen rendszerbiztonsági tagok hivatkozott házirendek, például a Futtatás mint végpontkötési, biztonsági a csomag megosztási vagy a biztonsági hozzáférési szabályzatok.  Ezután szabályzatokkal a szolgáltatási erőforrások a **ServiceManifestImport** az alkalmazásjegyzékben szakaszában.

Rendszerbiztonsági tagok deklarálásakor is definiálása és felhasználói csoportok létrehozásához, hogy az egyes csoportok felügyelete együtt, egy vagy több felhasználó is hozzáadhatók. Ez akkor hasznos, ha több felhasználó számára a különböző belépési pontok, és szükségük van bizonyos közös jogosultságok, amely a csoport szintjén érhető el.

Alapértelmezés szerint Service Fabric-alkalmazásokat, amely a Fabric.exe folyamatban fut, a fiók alatt fut. A Service Fabric emellett lehetővé teszi, hogy egy helyi felhasználói fiók vagy helyi rendszer fiók, amely belül az alkalmazásjegyzékben megadott alkalmazások futtatására. További információkért lásd: [szolgáltatás futtatása egy helyi felhasználói fiók vagy helyi rendszerfiók](service-fabric-application-runas-security.md).  Emellett [szolgáltatásindítási szkript futtatása helyi felhasználói vagy rendszer fiókként](service-fabric-run-script-at-service-startup.md).

A Service Fabric Windows önálló fürtön futtatja, amikor egy szolgáltatást futtathatja [Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoportosan felügyelt szolgáltatásfiókok](service-fabric-run-service-as-gmsa.md).

## <a name="secure-containers"></a>Biztonságos tárolók
A Service Fabric lehetővé teszi a szolgáltatás hozzáférjen a tanúsítványhoz, amely a csomópontok egy Windows vagy Linux rendszerű fürt (5.7-es vagy újabb verzió) telepítve van a tárolóban. A PFX-tanúsítvány használható az alkalmazás vagy szolgáltatás, vagy más szolgáltatásokkal biztonságos kommunikáció hitelesítéséhez. További információkért lásd: [tanúsítvány importálása egy tárolóba](service-fabric-securing-containers.md).

Emellett a Service Fabric is támogatja a csoportosan felügyelt szolgáltatásfiókok (csoportosan felügyelt szolgáltatásfiókok) Windows-tárolókhoz. További információkért lásd: [Windows-tárolók csoportosan felügyelt szolgáltatásfiók beállítása](service-fabric-setup-gmsa-for-windows-containers.md).

## <a name="secure-service-communication"></a>A szolgáltatások közötti kommunikáció biztonságossá tétele
A Service Fabric-szolgáltatás fut valahol a Service Fabric-fürt, általában elosztva a több virtuális gép. A szolgáltatás kommunikáció biztonságossá tételéhez számos lehetőséget kínál a Service Fabric.

Engedélyezheti a HTTPS-végpontnak a [ASP.NET Core vagy a Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) webes szolgáltatásokhoz.

A fordított proxy és a szolgáltatások közötti biztonságos kapcsolatot tesz lehetővé egy végpontok közötti biztonságos csatorna létesíthet. Csatlakozás biztonságos szolgáltatások támogatott csak akkor, ha a fordított proxy HTTPS figyelésére van konfigurálva. A fordított proxy konfigurálásával kapcsolatos további információkért olvassa el [fordított proxy az Azure Service Fabric](service-fabric-reverseproxy.md).  [Csatlakozás biztonságos szolgáltatáshoz](service-fabric-reverseproxy-configure-secure-communication.md) ismerteti, hogyan lehet a fordított proxy és a szolgáltatások közötti biztonságos kapcsolatot létesíteni.

A Reliable Services alkalmazási keretrendszer tartalmaz néhány előre létrehozott kommunikációs implementációt, eszközök, amelyek a biztonság növelése érdekében használhatja. Ismerje meg, hogyan szolgáltatás távelérésének lehetővé tétele használatakor a biztonság növelése érdekében (a [ C# ](service-fabric-reliable-services-secure-communication.md) vagy [Java](service-fabric-reliable-services-secure-communication-java.md)) vagy [WCF](service-fabric-reliable-services-secure-communication-wcf.md).

## <a name="encrypt-application-data-at-rest"></a>Alkalmazás az inaktív adatok titkosítása
Minden egyes [csomóponttípus](service-fabric-cluster-nodetypes.md) Service Fabric-fürtön az Azure-ban futó alapját egy [virtuálisgép-méretezési csoport](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Egy Azure Resource Manager-sablonnal adatlemezeket csatlakoztathat a Service Fabric-fürtöt alkotó méretezési csoport(ok)hoz.  Ha a szolgáltatások adatok mentése egy csatolt adatlemezre, [titkosítja azokat az adatlemezeket](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md) az alkalmazásadatok védelme érdekében.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
* [Futtassa a beállítási szkriptet a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md)
* [Erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)
* [Fürtbiztonság ismertetése](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png