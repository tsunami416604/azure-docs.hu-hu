---
title: Az Azure Service Fabric ajánlott biztonsági eljárások |} Microsoft Docs
description: Ez a cikk ajánlott eljárások az Azure Service Fabric biztonsági biztosít.
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
ms.date: 11/01/2017
ms.author: tomsh
ms.openlocfilehash: b908589903d243b2d284e2a23b6111785229c16f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894129"
---
# <a name="azure-service-fabric-security-best-practices"></a>Az Azure Service Fabric ajánlott biztonsági eljárások
Gyors, egyszerű és költséghatékony Azure-alkalmazás központi telepítése. Mielőtt éles környezetben a felhő alkalmazást telepít központilag, tekintse át az alapvető és ajánlott gyakorlati tanácsok a biztonságos fürtök megvalósítása az alkalmazásban listáját.

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak. 

A minden ajánlott eljárás az hogy ismertetik:

-   Mi az ajánlott eljárás az.
-   Miért kell alkalmazniuk az ajánlott eljárás szerint.
-   Mi történne, ha nem valósítja meg az ajánlott eljárás szerint.
-   Hogyan szerezhet az ajánlott eljárás végrehajtásához.

Ajánlott a következő Azure Service Fabric biztonsági gyakorlati tanácsokat:

-   Azure Resource Manager-sablonok és a Service Fabric PowerShell-modul segítségével biztonságos fürtök létrehozása.
-   X.509-tanúsítványokat használ.
-   Biztonsági szabályzatainak konfigurálását.
-   A Reliable Actors biztonsági beállítások megvalósításához.
-   Az SSL konfigurálása az Azure Service Fabric.
-   Azure Service Fabric hálózati elkülönítési és biztonsági használják.
-   Konfigurálja az Azure Key Vault biztonsági.
-   Felhasználók hozzárendelése szerepkörökhöz.


## <a name="best-practices-for-securing-your-clusters"></a>A fürtök biztonságossá tételének ajánlott eljárásai

Mindig használjon biztonságos fürtöt:
-   Valósítja meg a fürt biztonsági tanúsítványok használatával.
-   Adja meg az ügyfél-hozzáférési (felügyeleti, és csak olvasható) Azure Active Directory (Azure AD) segítségével.

Az automatikus központi telepítéseket használ:
-   Parancsfájlok segítségével létrehozni, telepíteni és a titkos kulcsok át.
-   A titkos kulcsok tárolása az Azure Key Vault és egyéb ügyfél-hozzáférési használja az Azure AD.
-   Hitelesítés szükséges a titkos kulcsok emberi elérésére.

Emellett vegye figyelembe az alábbi konfigurációs lehetőségeket:
-   Szegélyhálózat (más néven demilitarizált zóna DMZ-k és árnyékolt alhálózatok) Azure hálózati biztonsági csoportokkal (NSG-k) segítségével hozható létre.
-   Fürt virtuális gépek (VM) érheti el és kezelheti a fürt a távoli asztali kapcsolat jump kiszolgálók használatával.

A fürtök védetté kell tennie, hogy illetéktelen felhasználók nem tudnak csatlakozni, különösen ha a fürt fut éles környezetben. Bár lehetséges egy nem biztonságos fürtök létrehozásához, ha a fürt mutatja meg a nyilvános internethez felügyeleti végpontok névtelen felhasználók kapcsolódhatnak a fürthöz.

Nincsenek három [forgatókönyvek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) fürt biztonsági megvalósításához a különböző technológiák használatával:

-   Csomópont-csomópont biztonsági: Ebben a forgatókönyvben a virtuális gépek és a fürtben lévő számítógépek közötti kommunikáció biztonságossá tételére. Ezt az űrlapot a biztonsági biztosítja, hogy azokra a számítógépekre, amelyek jogosultak a fürthöz való csatlakoztatáshoz tárolhatja, alkalmazások és szolgáltatások a fürtben.
Ebben a forgatókönyvben, az Azure rendszeren futó, vagy fájlkiszolgálófürtökkel önálló Windows, rendszeren futó választhat [biztonsági tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) vagy [Windows biztonsági](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) a Windows Server-gépek.
-   Ügyfél-csomópont biztonsági: Ebben a forgatókönyvben a Service Fabric-ügyfél és a fürt egyes csomópontjain közötti kommunikáció biztonságossá tételére.
-   Szerepköralapú hozzáférés-vezérlés (RBAC): Ez a forgatókönyv használ külön identitások (tanúsítványok, az Azure ad-vel, és így tovább) minden rendszergazdai és felhasználói ügyfél szerepkörhöz, aki hozzáfér a fürthöz. A szerepkör-identitást a fürt létrehozásakor adja meg.

>[!NOTE]
>**Biztonsági ajánlás Azure fürtök:** használja az Azure AD biztonsági ügyfelek és -csomópontok biztonsági tanúsítványok hitelesítéséhez.

Egy különálló Windows-fürt konfigurálásához lásd: [önálló Windows fürt beállításainak konfigurálása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Használja az Azure Resource Manager-sablonok és a Service Fabric PowerShell-modul a biztonságos fürtök létrehozásához.
Biztonságos Service Fabric-fürt létrehozása az Azure Resource Manager-sablonok használatával lépésenkénti útmutatót lásd: [a Service Fabric-fürt létrehozása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Az Azure Resource Manager sablon használata:
-   Testre szabhatja a fürt a virtuális gép virtuális merevlemezek (VHD) felügyelt tárolás konfigurálása a sablon használatával.
-   Az erőforráscsoport a sablon használatával könnyen Konfigurációkezelő, és a naplózás meghajtó módosításait.

A fürt konfigurációját, akkor kezelje kódot:
-   Kell alapos a központi telepítési beállítások ellenőrzése során.
-   Ne közvetlenül módosítja az erőforrások implicit parancsok segítségével.

Sok aspektusainak a [Service Fabric-alkalmazás életciklusa](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) automatizálható. A [Service Fabric PowerShell-modul](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatizálja a szokásos feladatokat telepítése, frissítése, eltávolítása és tesztelés az Azure Service Fabric-alkalmazások. Felügyelt API-k és az Alkalmazáskezelés HTTP API-kat is elérhetők.

## <a name="use-x509-certificates"></a>X.509-tanúsítványokat használ
Mindig biztonságos a fürtök X.509-tanúsítványokat vagy a Windows biztonsági használatával. A biztonsági csak beállítások konfigurálása a fürt létrehozása során. Nincs lehetőség a fürt létrehozása után a biztonsági bekapcsolása.

Adja meg, hogy egy [fürt tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), állítsa a **ClusterCredentialType** X509 tulajdonságot. Adjon meg egy külső kapcsolatok kiszolgálói tanúsítványának, állítsa be a **ServerCredentialType** X509 tulajdonságot.

Emellett az alábbi lépésekkel:
-   A tanúsítványok termelési fürtök létrehozása a megfelelően konfigurált Windows kiszolgálói tanúsítvány szolgáltatások használatával. A tanúsítványok is egy jóváhagyott hitelesítésszolgáltatótól (CA) szerezhet be.
-   Soha ne használja az ideiglenes, vagy a tanúsítványt üzemi fürtök tesztelése, ha a tanúsítványt a MakeCert.exe vagy hasonló eszköz használatával lett létrehozva.
-   Egy önaláírt tanúsítványt használja, a tesztfürtökön, de nem éles fürtök.

Ha a fürt nem biztonságos, bárki névtelenül csatlakozzon a fürthöz, és felügyeleti műveleteket. Emiatt mindig biztonságos éles fürtök X.509-tanúsítványokat vagy a Windows biztonsági használatával.

X.509-tanúsítványok használatával kapcsolatos további tudnivalókért lásd: [tanúsítványok hozzáadása vagy eltávolítása a Service Fabric-fürt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Biztonsági szabályzatok konfigurálása
A Service Fabric is használt alkalmazások által használt erőforrások biztonságossá tételére. Erőforrások – például fájlok, könyvtárak, és a tanúsítványokat tárolja a felhasználói fiókok alapján, az alkalmazás központi telepítésekor. A szolgáltatás révén futó alkalmazások biztonságosabb egymástól, még akkor is, a megosztott üzemeltetési környezetben.

-   Egy Active Directory tartományi csoport vagy felhasználó használja: a szolgáltatás futtatására a hitelesítő adatokat az Active Directory-felhasználó vagy csoport fiókkal. Győződjön meg arról, az Active Directory helyszíni belül a tartomány és a nem Azure Active Directory használatára. A tartomány egyéb erőforrásainak, amely engedéllyel rendelkezik egy tartományi felhasználó vagy csoport használatával érhető el. Például erőforrások, például fájlmegosztásokat.

-   Rendelje hozzá a biztonsági házirend HTTP és HTTPS-végpont: Adja meg a **SecurityAccessPolicy** alkalmazandó tulajdonság egy **RunAs** házirend számára, egy szolgáltatás, akkor a szolgáltatás jegyzékfájl deklarál végpont erőforrások a HTTP. A HTTP-végpontokról rendelt portjait a futtató felhasználói fiók, amely a szolgáltatás fut, a megfelelő hozzáférés-vezérelt listája. Ha a házirend nincs beállítva, a http.sys nem fér hozzá a szolgáltatás, és sikertelen hívások lekérheti az ügyfél.

A Service Fabric-fürt biztonsági házirendek használatával kapcsolatban lásd: [konfigurálhat biztonsági házirendeket az alkalmazás](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>A Reliable Actors biztonsági konfiguráció megvalósítása
Service Fabric Reliable Actors szereplő kialakítási minta megvalósítása. A szoftver a kialakítási mintában a alkalmazással használja egy adott mintával való alapul e szoftver probléma megfelel a mintának.

Általában segítségével szereplő kialakítási minta a következő szoftverfrissítési problémák és biztonsági forgatókönyvekhez modell megoldások:
-   A probléma tárhely magában foglalja a sok (több ezer vagy több) kis, független és elkülönített állapot és a logikai egységek.
-   Dolgozunk a egyszálas objektumok, amelyek nem igényelnek fontos kapcsolata külső összetevők, beleértve az állapot lekérdezése szereplője csoportja között.
-   Az aktor példányok nincs blokkolás hívók előre nem látható késlelteti az i/o-műveletek kiállításával.

A Service Fabric szereplője a Reliable Actors alkalmazás-keretrendszer valósíthatók meg. Ezt a keretrendszert a szereplő szabály alapján, és a beépített [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Minden megbízható szereplő szolgáltatás írást egy olyan particionált állapot-nyilvántartó megbízható szolgáltatás.

Minden aktor egy szereplő típusú, a .NET-objektum egy .NET-típus egy példánya nem módosul azonos típusúként van definiálva. Például egy **szereplő típus** , hogy megvalósítja a Számológép funkcióinak rendelkezhet sok szereplője az adott típusú fürt különböző csomópontokon elosztott. Az elosztott szereplője mindegyikének egyedi jellemzőek szereplő azonosítóval.

[A replikáló biztonsági beállításokkal](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) szolgálnak a replikáció során használt kommunikációs csatornát. Ez a konfiguráció megakadályozza, hogy a szolgáltatások egymás replikációs forgalom lássák, és biztosítja, hogy biztonságos legyen-e magas rendelkezésre állású adatokat. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.
Replikációs konfigurációk konfigurálása a replikátor, amely feladata, hogy az Aktor Állapotszolgáltató állapot nagymértékben megbízható.

## <a name="configure-ssl-for-azure-service-fabric"></a>Az SSL konfigurálása az Azure Service Fabric
A kiszolgáló hitelesítési folyamat [hitelesíti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) a fürt felügyeleti végpontok felügyeleti ügyfél számára. A felügyeleti ügyfél majd felismeri, ha a valós fürthöz. Ez a tanúsítvány is biztosít egy [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül.
Egyéni tartománynevet kell beszereznie a fürt számára. Ha tanúsítványt kérhet egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynév, amely a fürt használhatja.

SSL konfigurálása az alkalmazáshoz, először a hitelesítésszolgáltató által aláírt SSL-tanúsítvány beszerzése. A hitelesítésszolgáltató által kiállított tanúsítványok SSL biztonsági okokból megbízható harmadik fél. Ha még nem rendelkezik SSL-tanúsítvány, akkor beszerzése utólag egy vállalat, amely SSL-tanúsítványok értékesít.

A tanúsítványt az SSL-tanúsítványok az Azure-ban az alábbi követelményeknek kell megfelelniük:
-   A tanúsítványnak tartalmaznia kell egy titkos kulccsal.

-   A tanúsítvány a kulcscseréhez használt kell létrehozni, és egy személyes információcsere (.pfx) fájl a exportálhatónak lennie.

-   A tanúsítvány tulajdonosának nevét meg kell egyeznie a tartománynév, a felhőalapú szolgáltatás eléréséhez használt.

    - Szerezzen be egy egyéni tartománynevet a felhőalapú szolgáltatás eléréséhez használandó.
    - A hitelesítésszolgáltató, amely megfelel a szolgáltatás egyéni tartománynevet tulajdonosnévvel rendelkező tanúsítványt kérhet. Például, ha az egyéni tartománynevet __contoso__**.com**, a tanúsítványt a hitelesítésszolgáltatóról a tulajdonos nevének kell **. contoso.com** vagy __www__ **. contoso.com**.

    >[!NOTE]
    >Nem szerezzen be egy SSL-tanúsítványt egy hitelesítésszolgáltatóból a __cloudapp__**.net** tartomány.
    
-   A tanúsítvány legalább 2048 bites titkosítást kell használnia.

A HTTP protokoll nem biztonságos, és a lehallgatási támadások célpontjává. HTTP Protokollon keresztül továbbított adatokat küldi, egyszerű szövegként a webböngésző számára a webkiszolgálóhoz, vagy más végpontok közötti. A támadók intercept, és via HTTP, például a hitelkártya adatait és a fiók bejelentkezések küldött bizalmas adatok megtekintéséhez. Amikor adatokat küldött, vagy HTTPS használatával böngészőn keresztül közzétett, SSL biztosítja, hogy bizalmas adatokat titkosított és biztonságos hozzáférés a.

SSL-tanúsítványok használatával kapcsolatos további információkért lásd: [SSL konfigurálása az Azure-alkalmazások](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Hálózati elkülönítési és biztonsági használata Azure Service Fabric
A 3 nodetype biztonságos fürt beállítása a [Azure Resource Manager sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) egy mintát. A sablon és a hálózati biztonsági csoportok használatával szabályozhatja a bejövő és kimenő hálózati forgalmat.

A sablon az NSG tartozik az egyes virtuálisgép-méretezési csoportok, és a forgalom mindkét a készlet használatával. A szabályok a rendszerszolgáltatások és az alkalmazás portok a sablonban megadott szükséges összes forgalmat engedélyezi alapértelmezés szerint vannak konfigurálva. Tekintse át ezeket a szabályokat, és szükség szerint, beleértve az új szabályokat, az alkalmazások módosításokat.

További információkért lásd: [Azure Service Fabric általános hálózati forgatókönyvek](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>Állítsa be az Azure Key Vault biztonság
A Service Fabric tanúsítványokat használ a hitelesítési és titkosítási biztonságossá tételéhez a fürt és az alkalmazásokhoz.

A Service Fabric X.509-tanúsítványokat használ, védelméhez a fürt és az alkalmazás biztonsági funkciók biztosítására. Használhatja az Azure Key Vault [tanúsítványok kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) Service Fabric-fürtök az Azure-ban. Az Azure erőforrás-szolgáltató, amely a fürtök hoz kéri le a tanúsítványokat a kulcstároló. A szolgáltató a fürt telepítésekor Azure majd telepíti a tanúsítványokat a virtuális gépeken.

Létezik egy tanúsítvány kapcsolat között [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), a Service Fabric-fürt, és az erőforrás-szolgáltató, amely a tanúsítványokat használ. Amikor létrehozza a fürtöt, a tanúsítvány-kapcsolat információi kulcstároló tárolódik.

Kulcstároló beállítása két alapvető lépésből áll:
1. Hozzon létre egy erőforráscsoportot kifejezetten a kulcstartót.

    Azt javasoljuk, hogy a key vault be a saját erőforráscsoportot. Ezt a műveletet a kulcsok és titkos az adatvesztés elkerülése érdekében, ha egyéb erőforráscsoportok törlődik, például a tárolási, számítási vagy a csoport, amely tartalmazza a fürt segítségével. Az erőforráscsoport, amely tartalmazza a kulcstartót a fürt által használt ugyanabban a régióban kell lennie.

2. Hozzon létre egy kulcstartót új erőforráscsoportban.

    A key vault engedélyeznie kell a központi telepítéshez. A számítási erőforrás-szolgáltató a tanúsítványok beolvasása a tárolóból, majd telepítse őket a Virtuálisgép-példányok.

Kulcstároló beállításával kapcsolatos további információkért lásd: [Ismerkedés az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz
Az alkalmazásokat a határoz meg a fürt létrehozása után a felhasználók a szerepkörökhöz rendeljen hozzá a Service Fabric által támogatott: olvasási és a rendszergazda segítségét. Ezeket a szerepköröket rendelhet az Azure portál használatával.

>[!NOTE]
> A Service Fabric szerepkörök használatával kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Azure Service Fabric hozzáférés-vezérlési kétféle támogatja az ügyfelek csatlakoznak a [Service Fabric-fürt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): rendszergazdai és felhasználói. A fürt rendszergazdai hozzáférés-vezérlés használatával korlátozhatja az egyes különböző felhasználói csoportokhoz a fürt működését. Hozzáférés-vezérlés lehetővé teszi a fürt biztonságosabb.

## <a name="next-steps"></a>További lépések
- Állítsa be a Service Fabric [fejlesztőkörnyezet](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- További tudnivalók [Service Fabric támogatási lehetőségek](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
