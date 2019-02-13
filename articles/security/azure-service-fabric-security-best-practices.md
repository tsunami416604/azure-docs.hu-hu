---
title: Az Azure Service Fabric ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ez a cikk egy Azure Service Fabric biztonsági védelmének bevált gyakorlata biztosít.
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
ms.openlocfilehash: 8bafc4a95ca9af4567ed70c190a72f3b351da47c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114705"
---
# <a name="azure-service-fabric-security-best-practices"></a>Az Azure Service Fabric ajánlott biztonsági eljárások
Az Azure-alkalmazás üzembe helyezése a gyors, egyszerű és költséghatékony. Mielőtt telepítené a felhőbeli alkalmazások éles környezetben, szükséges és ajánlott megvalósításához ajánlott eljárások biztonságos fürtökhöz az alkalmazásban listájának áttekintése.

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

A minden egyes ajánlott eljárás az hogy ismertetik:

-   Mi az ajánlott eljárás az.
-   Miért is meg kell valósítania az ajánlott eljárás.
-   Mi történne, ha nem alkalmazza az ajánlott eljárás.
-   Hogyan tudhat meg az ajánlott eljárás végrehajtására.

Javasoljuk, hogy a következő Azure Service Fabric-biztonság ajánlott eljárásokat:

-   Az Azure Resource Manager-sablonok és a Service Fabric PowerShell-modul segítségével biztonságos fürtöket létrehozni.
-   X.509-tanúsítványokat használ.
-   Biztonsági szabályzatok konfigurálása.
-   A Reliable Actors biztonsági konfiguráció megvalósításához.
-   Az SSL konfigurálása az Azure Service Fabric.
-   Hálózati elkülönítési és biztonsági használja az Azure Service Fabric.
-   Állítsa be az Azure Key Vault a biztonsági.
-   Felhasználók hozzárendelése szerepköröket.


## <a name="best-practices-for-securing-your-clusters"></a>A fürtök biztonságossá tételének ajánlott eljárásait

Mindig használjon egy biztonságos fürt:
-   Fürtbiztonság tanúsítványok segítségével megvalósítható.
-   Adja meg az ügyfél-hozzáférési (felügyeleti, és csak olvasható) Azure Active Directory (Azure AD) használatával.

Az automatikus központi telepítéseket használ:
-   Parancsprogramok használatával hozzon létre, telepíthet és vihetők át a titkos kulcsok.
-   Store a titkos kulcsok Azure Key vaultban, és használja az Azure AD minden más ügyfél-hozzáférési.
-   A titkos kódokhoz való emberi hozzáféréshez hitelesítés szükséges.

Továbbá vegye figyelembe az alábbi beállítások közül:
-   Hozzon létre a szegélyhálózatokon (más néven vagy demilitarizált zóna, DMZ-ket és átesett alhálózatok) az Azure hálózati biztonsági csoportok (NSG-k) használatával.
-   Fürt virtuális gépeken (VM) eléréséhez, vagy a fürt kezeléséhez helyettesítő kiszolgálókkal a távoli asztali kapcsolat segítségével.

A fürtök kell biztosítani, hogy megakadályozza a jogosulatlan felhasználók csatlakozzanak, különösen ha a fürt fut éles környezetben. Bár egy biztonságos fürt létrehozásához, névtelen felhasználók csatlakozhat a fürthöz, ha a fürt felügyeleti végpontok, a nyilvános interneten teszi elérhetővé.

Három [forgatókönyvek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) Fürtbiztonság megvalósításához a különböző technológiák használatával:

-   Csomópontok közötti biztonsági: Ebben a forgatókönyvben védi a virtuális gépek és a fürtben lévő számítógépek közötti kommunikációt. Az ilyen típusú biztonsági biztosítja, hogy csak olyan számítógépeken, amelyeken a fürthöz való csatlakoztatáshoz jogosult üzemeltethet, alkalmazások és szolgáltatások a fürtben.
A jelen forgatókönyv, a fürtök, amelyek az Azure-ban vagy az önálló fürtök futó Windows, használhatja [biztonsági tanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) vagy [Windows biztonsági](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) Windows Server-alapú gépek esetében.
-   Ügyfél-csomópont biztonsági: Ebben a forgatókönyvben egy Service Fabric-ügyfél és az egyes csomópontokhoz a fürtben közötti kommunikáció biztonságossá teszi.
-   Role-Based Access Control (RBAC): Ebben a forgatókönyvben különálló identitásokat használ (tanúsítványok, az Azure Active Directory, és így tovább) minden rendszergazdai és felhasználói ügyfél szerepkör, amely hozzáfér a fürthöz. A szerepkör-identitásokat a fürt létrehozásakor adja meg.

>[!NOTE]
>**Az Azure-fürtök biztonsági ajánlás:** Az Azure AD biztonsági használatával az ügyfelek és a csomópontok közötti biztonsági tanúsítványok hitelesítéséhez.

Egy különálló Windows-fürt beállítása: [egy különálló Windows-fürt beállításainak konfigurálása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Egy biztonságos fürt létrehozása Azure Resource Manager-sablonok és a Service Fabric PowerShell-modult használja.
Biztonságos Service Fabric-fürt létrehozása Azure Resource Manager-sablonok használatával részletes utasításokért lásd: [Service Fabric-fürt létrehozása](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Az Azure Resource Manager-sablon használata:
-   A fürt testreszabása a sablon konfigurálása a felügyelt tárfiókok, virtuális gép virtuális merevlemezek (VHD) használatával.
-   Meghajtó a sablonnal könnyedén konfigurációkezelés és naplózásával módosítja az erőforráscsoportban.

A fürt konfigurációt kezelje kódként:
-   Lehet alapos, a központi telepítési beállítások ellenőrzése során.
-   Kerülje a implicit parancsokkal történő közvetlen módosítása az erőforrások.

Számos szempontból a [Service Fabric-alkalmazás-életciklus](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) automatizálható. A [Service Fabric PowerShell-modul](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatizálja a központi telepítése, frissítése, eltávolítása és az Azure Service Fabric-alkalmazásokat kíván tesztelni, kapcsolódó általános feladatok. Felügyelt API-k és az Alkalmazáskezelés HTTP API-k is elérhetők.

## <a name="use-x509-certificates"></a>X.509-tanúsítványok használata
Mindig biztonságos a fürtök X.509-tanúsítványokat vagy a Windows biztonsági használatával. Biztonsági csak a fürt létrehozásakor van konfigurálva. Nem alkalmas biztonsági kapcsolja be a fürt létrehozása után.

Adja meg, hogy egy [fürttanúsítvány](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), az értékét állítsa be a **ClusterCredentialType** X509 tulajdonságot. Adjon meg egy külső kapcsolatok kiszolgálói tanúsítványának, állítsa be a **ServerCredentialType** X509 tulajdonságot.

Emellett kövesse ezeket az eljárásokat:
-   Éles fürtökben tanúsítványainak hozzon létre egy megfelelően konfigurált Windows Server tanúsítványszolgáltatást. A tanúsítványok is egy elismert hitelesítésszolgáltató (CA) szerezhet.
-   Soha ne használja az ideiglenes vagy tesztelése éles fürtök esetén a tanúsítványt, ha a tanúsítvány a MakeCert.exe vagy egy hasonló eszköz használatával lett létrehozva.
-   Önaláírt tanúsítvány használata a tesztfürtök esetében, de éles fürtök esetén nem.

Ha a fürt nem biztonságos, bárki név nélkül csatlakozhat a fürthöz, és felügyeleti műveletek elvégzéséhez. Ezért mindig biztonságos éles fürtökben X.509-tanúsítványokat vagy a Windows biztonsági használatával.

X.509-tanúsítványok használatával kapcsolatos további tudnivalókért lásd: [Service Fabric-fürt hozzáadása vagy eltávolítása tanúsítványai](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Biztonsági szabályzatok beállítása
A Service Fabric is biztosítja az alkalmazások által használt erőforrásokat. Erőforrások, például a könyvtárak, fájlok és a tanúsítványokat tárolja a felhasználói fiókok alapján az alkalmazás központi telepítésekor. A szolgáltatás révén futó alkalmazások biztonságát egymástól, még akkor is, a közös üzemeltetési környezetben.

-   Az Active Directory tartományi csoport vagy felhasználó használata: Egy Active Directory-felhasználó vagy csoport-fiókja alatt a hitelesítő adatokat a szolgáltatás fut. Győződjön meg arról, az Active Directory a helyszíni belül a tartomány és a nem az Azure Active Directory használatára. A tartomány egyéb erőforrásainak, amely engedélyekkel rendelkezik egy tartományi felhasználó vagy csoport használatával eléréséhez. Ha például erőforrásokat, például fájlmegosztásokat.

-   A HTTP vagy HTTPS-végpontokat biztonsági hozzáférési szabályzat hozzárendelése: Adja meg a **SecurityAccessPolicy** tulajdonságát a alkalmazni egy **RunAs** egy szolgáltatás, akkor a szolgáltatásjegyzék deklarálja a HTTP-végpont erőforrások szabályzatot. HTTP-végpontokat lefoglalt portjait a futtató felhasználói fiók, amely a szolgáltatás fut, a megfelelő hozzáférés-vezérelt listák. A szabályzat nincs beállítva, amikor a http.sys nem rendelkezik a szolgáltatáshoz való hozzáférést, és sikertelen hívások megtekintheti az ügyféltől.

Biztonsági házirendek használata a Service Fabric-fürt kapcsolatban lásd: [konfigurálhat biztonsági házirendeket az alkalmazás](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="implement-the-reliable-actors-security-configuration"></a>Reliable Actors biztonsági beállítások
Service Fabric Reliable Actors az aktor tervezési minta egy megvalósítását. Csakúgy, mint bármely szoftver a kialakítási mintában a egy adott minta használata mellett alapul e szoftver probléma megfelel-e a minta.

Általában az aktor tervezési minta használatával modellmegoldásokkal, a következő szoftverfrissítési problémák vagy a biztonsági forgatókönyvek segítségével:
-   A probléma tárhely magában foglalja a nagy számú (több ezer vagy több) kisebb, független és elkülönített állapotot és a logikai egységek.
-   Egyszálas objektumokat, amelyeknek nincs szükségük külső összetevők, beleértve az állapot lekérdezése actors több jelentős felhasználói beavatkozás dolgozik.
-   Az aktorpéldányokat i/o-műveletek kiállításával nem blokkolja a hívó az késleltetések előre nem látható.

A Service Fabric actors a Reliable Actors-alkalmazás keretrendszer vannak megvalósítva. Ezt a keretrendszert az aktor minta alapján, és a beépített [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Minden egyes reliable actors-szolgáltatás, amely ír egy particionált állapotalapú reliable Services.

Minden egyes színész számít, ha az aktor típusát, a .NET-objektumokat kell egy .NET-típus egy példányát módja megegyezik egy példányát. Ha például egy **szereplőtípus** , valósít meg egy Számológép funkciójának rendelkezhet, amely különböző csomópontokon vannak elosztva egy fürtöt az adott típusú számos actors. Az elosztott aktorokat mindegyike egyedileg jellemző szereplő azonosítóval.

[A replikáló biztonsági konfigurációk](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) szolgálnak a replikáció során használt kommunikációs csatornát. Ez a konfiguráció megakadályozza, hogy a szolgáltatások jelennek meg a többi összes replikációs forgalmat, és biztosítható, hogy biztonságos legyen-e magas rendelkezésre állású. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza, hogy a replikációs biztonságot.
A replikáló konfigurációk a replikátor, amely feladata, hogy az Aktor Riasztásiállapot-szolgáltató állapota magas megbízhatóságú konfigurálása.

## <a name="configure-ssl-for-azure-service-fabric"></a>Az SSL konfigurálása az Azure Service Fabric
A kiszolgáló-hitelesítési folyamat [hitelesíti](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) a fürtkezelési végpontoknak egy kezelési ügyfél. A felügyeleti ügyfél majd felismeri, hogy a valós fürt beszél. Ezt a tanúsítványt is biztosít egy [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) a HTTPS-felügyeleti API-hoz és a HTTPS-kapcsolaton keresztül a Service Fabric Explorer számára.
Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének egyeznie kell a fürthöz használt egyéni tartománynév.

Az SSL konfigurálása az alkalmazáshoz, először egy hitelesítésszolgáltató által aláírt SSL-tanúsítvány beszerzése. A hitelesítésszolgáltató által kiállított tanúsítványok az SSL biztonsági okokból harmadik fél megbízható. Ha még nem rendelkezik egy SSL-tanúsítványt, szerezze be olyan cég, amely képeit SSL-tanúsítványok közül szeretné.

A tanúsítványt az SSL-tanúsítványok az Azure-ban az alábbi követelményeknek kell megfelelnie:
-   A tanúsítványnak tartalmaznia kell egy titkos kulcsot.

-   A tanúsítvány a kulcscseréhez használt kell létrehozni, és lehet exportálni, a személyes információcsere (.pfx) fájlt.

-   A tanúsítvány tulajdonosnevének egyeznie kell a tartomány nevét, a felhőalapú szolgáltatás eléréséhez használt.

    - A cloud Services eléréséhez használandó egyéni tartománynév beszerezni.
    - Egy hitelesítésszolgáltató, amely megfelel a szolgáltatást egyéni tartománynév tulajdonosnévvel rendelkező tanúsítványt kérhet. Például, ha az egyéni tartománynév __contoso__**.com**, a tanúsítványt a hitelesítésszolgáltatótól kell rendelkeznie a tulajdonos neve **. contoso.com** vagy __www__ **. contoso.com**.

    >[!NOTE]
    >Nem szerezheti be egy SSL-tanúsítványt egy hitelesítésszolgáltatóból számára a __cloudapp__**.net** tartományhoz.

-   A tanúsítvány legalább 2048 bites titkosítást kell használnia.

A HTTP-protokoll nem biztonságos, és a lehallgatási támadások áldozatai. A webkiszolgálóhoz, vagy más végpontok közötti webböngészőből küldött HTTP-n keresztül továbbított adatok az egyszerű szövegként. A támadók intercept is, és érzékeny adatokat, például a hitelkártya adatait és a fiók bejelentkezések küldött HTTP-n keresztül. Küldött vagy HTTPS-kapcsolaton keresztül böngészőn keresztül közzétett, SSL biztosítja, hogy bizalmas adatok titkosítva és biztonságos hozzáférés a.

SSL-tanúsítványok használatával kapcsolatos további információkért lásd: [SSL konfigurálása az Azure-alkalmazásokhoz](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Az Azure Service Fabric hálózati elkülönítési és biztonsági használata
A 3 nodetype biztonságos fürt beállítása a [Azure Resource Manager-sablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) egy mintát. A kimenő és bejövő hálózati forgalmának vezérlése a sablon és a hálózati biztonsági csoportok használatával.

A sablon rendelkezik NSG-vel az egyes virtuálisgép-méretezési csoportok, és a set-ból a forgalom szabályozására szolgál. A szabályok úgy vannak konfigurálva, az összes forgalom engedélyezéséhez szükséges, hogy a helyrendszeri szolgáltatások és az alkalmazás portok a sablonban megadott alapértelmezés szerint. Tekintse át ezeket a szabályokat, és végezze el a módosításokat a saját igényei szerint, beleértve az új szabályokat, az alkalmazások számára.

További információkért lásd: [Azure Service Fabric általános hálózati forgatókönyvek](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-azure-key-vault-for-security"></a>A biztonság az Azure Key Vault beállítása
A Service Fabric tanúsítványokat használ a hitelesítéshez és titkosításhoz a fürtök és a rajtuk található alkalmazások biztonságossá tételéhez.

A Service Fabric X.509-tanúsítványokat használ, a fürt biztonságossá tétele és alkalmazás biztonsági funkciók biztosítására. Az Azure Key Vault használata [tanúsítványok kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) a Service Fabric-fürtök az Azure-ban. Az Azure erőforrás-szolgáltató, amely létrehozza a fürtöket lekéri a tanúsítványokat a key vault. A fürtöt helyezünk üzembe az Azure-ban a szolgáltató ezután telepíti a tanúsítványokat a virtuális gépeken.

Létezik egy tanúsítvány kapcsolat között [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), a Service Fabric-fürt és az erőforrás-szolgáltató által használt tanúsítványok. Ha a fürt létrejött, a tanúsítvány-kapcsolat információi key vault tárolódik.

A key vault létrehozása két alapvető lépésből áll:
1. Hozzon létre egy erőforráscsoportot a kimondottan a key vaultban.

    Azt javasoljuk, hogy a key vault helyezi a saját erőforráscsoport. Ez a művelet segít meggátolni a kulcsok és titkok elvesztését, ha egyéb erőforráscsoportok el lesznek távolítva, például tárolási, számítási vagy a csoport, amely tartalmazza a fürt. Az erőforráscsoport, amely tartalmazza a kulcstartó a fürt által használt ugyanabban a régióban kell lennie.

2. Kulcstartó létrehozása az új erőforráscsoportban.

    A key vault engedélyezni kell a központi telepítés. A számítási erőforrás-szolgáltató ezután a tanúsítványok lekérése a tárolóból, és telepítse őket a Virtuálisgép-példányokon.

Hogyan állítható be a key vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-to-roles"></a>Felhasználók szerepkörökhöz rendelése
Az alkalmazások, amelyek a fürt létrehozása után a felhasználók hozzárendelése a Service Fabric által támogatott szerepkörök: csak olvasható és a rendszergazdával. Ezeket a szerepköröket rendelhet az Azure portal használatával.

>[!NOTE]
> A Service Fabric szerepkörök használatával kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés a Service Fabric-ügyfelek](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

Az Azure Service Fabric támogatja a hozzáférés-vezérlési kétféle csatlakozó ügyfelek egy [Service Fabric-fürt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): rendszergazdai és felhasználói. A fürt rendszergazdai hozzáférés-vezérlés használatával férjenek hozzá az egyes fürtműveletek a különböző felhasználói csoportokhoz. Hozzáférés-vezérlés biztonságosabbá teszi a fürt.

## <a name="next-steps"></a>További lépések

- [A Service Fabric biztonsági ellenőrzőlista](azure-service-fabric-security-checklist.md)
- A Service Fabric beállítása [fejlesztési környezet](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Ismerje meg [Service Fabric támogatási lehetőségeinek](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
