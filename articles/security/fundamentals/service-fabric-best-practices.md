---
title: Gyakorlati tanácsok az Azure Service Fabric biztonságához
description: Ez a cikk az Azure Service Fabric biztonságával kapcsolatos gyakorlati tanácsok készletét tartalmazza.
author: unifycloud
ms.author: tomsh
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 4548bf77c01194802c2e6203bcbf9fbd240370a2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461650"
---
# <a name="azure-service-fabric-security-best-practices"></a>Az Azure Service Fabric ajánlott biztonsági eljárásai
Egy alkalmazás üzembe helyezése az Azure-ban gyors, egyszerű és költséghatékony. Mielőtt üzembe helyezné a felhőalapú alkalmazást éles környezetben, tekintse át a biztonságos fürtök alkalmazásában való megvalósításához szükséges alapvető és ajánlott gyakorlati tanácsok listáját.

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások csomagolását, üzembe helyezését és kezelését. A Service Fabric emellett választ ad a felhőalapú alkalmazások fejlesztésének és kezelésének jelentős kihívásaira. A fejlesztők és a rendszergazdák elkerülhetik az infrastruktúrával kapcsolatos összetett problémákat, és a kritikus fontosságú, nagy erőforrás-igényű, skálázható, megbízható és felügyelhető számítási feladatok megvalósítására koncentrálhatnak.

Minden egyes bevált gyakorlathoz elmagyarázzuk a következőket:

-   Mi a legjobb gyakorlat.
-   Miért kell végrehajtani a legjobb gyakorlatot.
-   Mi történhet, ha nem valósítja meg a legjobb gyakorlatot?
-   Hogyan lehet megtanulni, hogy hajtsák végre a legjobb gyakorlat.

Az Azure Service Fabric következő biztonsági gyakorlati tanácsait javasoljuk:

-   Az Azure Resource Manager-sablonok és a Service Fabric PowerShell modul használatával biztonságos fürtöket hozhat létre.
-   Használja az X.509 tanúsítványokat.
-   Konfigurálja a biztonsági házirendeket.
-   Valósítsa meg a Reliable Actors biztonsági konfiguráció.
-   Konfigurálja a TLS-t az Azure Service Fabric hez.
-   Használja a hálózati elkülönítést és biztonságot az Azure Service Fabric használatával.
-   Konfigurálja az Azure Key Vault ot a biztonság érdekében.
-   Felhasználók hozzárendelése szerepkörökhöz.


## <a name="best-practices-for-securing-your-clusters"></a>Gyakorlati tanácsok a fürtök védelméhez

Mindig használjon biztonságos fürtöt:
-   Fürtbiztonság megvalósítása tanúsítványok használatával.
-   Ügyfél-hozzáférés biztosítása (rendszergazdai és csak olvasható) az Azure Active Directory (Azure AD) használatával.

Automatikus központi telepítések használata:
-   Parancsfájlok használatával hozza létre, telepítse és forgassa át a titkos kulcsokat.
-   Tárolja a titkos kulcsokat az Azure Key Vaultban, és használja az Azure AD-t az összes többi ügyfélhozzáféréshez.
-   Hitelesítés szükséges a titkos kulcsokhoz való emberi hozzáféréshez.

Ezenkívül vegye figyelembe a következő konfigurációs lehetőségeket:
-   Az Azure Network Security Groups (NSG-k) használatával szegélyhálózatokat (más néven demilitarizált zónákat, DMZ-ket és szűrt alhálózatokat) hozhat létre.
-   Érje el a fürt virtuális gépeit (VM-eket), vagy kezelje a fürtöt a Távoli asztali kapcsolattal rendelkező ugrókiszolgálók használatával.

A fürtöket biztonságosnak kell tekinteni, hogy illetéktelen felhasználók ne csatlakozzanak, különösen akkor, ha egy fürt éles környezetben fut. Bár lehetőség van egy nem biztonságos fürt létrehozására, névtelen felhasználók csatlakozhatnak a fürthöz, ha a fürt felügyeleti végpontokat ad a nyilvános internetnek.

A fürtbiztonság különböző technológiák használatával történő megvalósítására három [forgatókönyv létezik:](../../service-fabric/service-fabric-cluster-security.md)

-   Csomópont-csomópont biztonság: Ez a forgatókönyv biztosítja a virtuális gépek és a fürt számítógépei közötti kommunikációt. Ez a biztonsági forma biztosítja, hogy csak a fürthöz való csatlakozásra jogosult számítógépek üzemeltethessenek alkalmazásokat és szolgáltatásokat a fürtben.
Ebben a forgatókönyvben az Azure-on futó fürtök vagy a Windows rendszeren futó önálló fürtök használhatják a [tanúsítványok biztonságát](../../service-fabric/service-fabric-windows-cluster-x509-security.md) vagy a Windows Server-gépek Windows Server-alapú gépeken a Windows Server-alapú biztonsági [rendszert.](../../service-fabric/service-fabric-windows-cluster-windows-security.md)
-   Ügyfél-csomópont biztonság: Ez a forgatókönyv biztosítja a kommunikációt a Service Fabric-ügyfél és a fürt egyes csomópontjai között.
-   Szerepköralapú hozzáférés-vezérlés (RBAC): Ez a forgatókönyv külön identitásokat (tanúsítványok, Azure AD és így tovább) használ minden olyan rendszergazdai és felhasználói ügyfélszerepkörhöz, amely hozzáfér a fürthöz. A fürt létrehozásakor adja meg a szerepkör-identitásokat.

>[!NOTE]
>**Az Azure-fürtök biztonsági javaslata:** Az Azure AD-biztonság használatával hitelesítheti az ügyfeleket és a tanúsítványokat a csomópontok biztonsága érdekében.

Önálló Windows-fürt konfigurálásához olvassa [el az Önálló Windows-fürt beállításainak konfigurálása című témakört.](../../service-fabric/service-fabric-cluster-manifest.md)

Az Azure Resource Manager-sablonok és a Service Fabric PowerShell modul használatával hozzon létre egy biztonságos fürtöt.
A biztonságos Service Fabric-fürt Azure Resource Manager-sablonok használatával történő létrehozásáról a [Service Fabric-fürt létrehozása című](../../service-fabric/service-fabric-cluster-creation-via-arm.md)témakörben olvashat.

Az Azure Resource Manager sablon használata:
-   A fürt testreszabása a sablon használatával konfigurálja a virtuális gép virtuális merevlemezek (VHD-k) felügyelt tárolóját.
-   Az erőforráscsoport módosításait a sablon használatával az egyszerű konfigurációkezelés és naplózás segítségével hajthatja végre.

Kezelje a fürtkonfigurációt kódként:
-   Legyen alapos a központi telepítési konfigurációk ellenőrzésekor.
-   Ne használjon implicit parancsokat az erőforrások közvetlen módosításához.

A Service [Fabric alkalmazás életciklusának](../../service-fabric/service-fabric-application-lifecycle.md) számos szempontja automatizálható. A [Service Fabric PowerShell modul](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) automatizálja az Azure Service Fabric-alkalmazások üzembe helyezésével, frissítésével, eltávolításával és tesztelésével kapcsolatos gyakori feladatokat. Felügyelt API-k és HTTP API-k alkalmazáskezeléshez is rendelkezésre állnak.

## <a name="use-x509-certificates"></a>X.509-es tanúsítványok használata
A fürtöket mindig x.509-es tanúsítványokkal vagy a Windows biztonságával biztosítsa. A biztonság csak a fürt létrehozásakor van konfigurálva. A fürt létrehozása után nem lehet bekapcsolni a biztonságot.

[Fürttanúsítvány](../../service-fabric/service-fabric-windows-cluster-x509-security.md)megadásához állítsa a **ClusterCredentialType** tulajdonság értékét X509-re. Külső kapcsolatok kiszolgálói tanúsítványának megadásához állítsa a **ServerCredentialType** tulajdonságot X509-re.

Ezen túlmenően kövesse az alábbi gyakorlatokat:
-   Hozza létre az éles fürtök tanúsítványait egy megfelelően konfigurált Windows Server tanúsítványszolgáltatás használatával. A tanúsítványokat egy jóváhagyott hitelesítésszolgáltatótól is beszerezheti.
-   Soha ne használjon ideiglenes vagy teszttanúsítványt éles fürtökhöz, ha a tanúsítványt a MakeCert.exe vagy egy hasonló eszköz segítségével hozták létre.
-   Használjon önaláírt tanúsítványt tesztfürtökhöz, de éles fürtökhöz nem.

Ha a fürt nem biztonságos, bárki névtelenül csatlakozhat a fürthöz, és felügyeleti műveleteket hajthat végre. Ezért mindig biztosítsa az éles fürtöket x.509-es tanúsítványok vagy Windows-biztonság használatával.

Az X.509-tanúsítványok használatáról a [Service Fabric-fürt tanúsítványainak hozzáadása és eltávolítása.](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md)

## <a name="configure-security-policies"></a>Biztonsági házirendek konfigurálása
A Service Fabric is biztosítja az alkalmazások által használt erőforrásokat. Az olyan erőforrások, mint a fájlok, könyvtárak és tanúsítványok az alkalmazás telepítésekor a felhasználói fiókok alatt tárolódnak. Ez a szolgáltatás biztonságosabbá teszi az alkalmazások futtatását egymástól, még egy megosztott üzemeltetett környezetben is.

-   Active Directory tartományi csoport vagy felhasználó használata: Futtassa a szolgáltatást egy Active Directory-felhasználó vagy -csoport fiók hitelesítő adatai alatt. Ügyeljen arra, hogy az Active Directoryt a tartományon belül használja, és ne az Azure Active Directoryt. A tartomány más, engedéllyel rendelkező erőforrásainak elérése tartományi felhasználó vagy csoport használatával. Például erőforrások, például fájlmegosztások.

-   Biztonsági hozzáférési házirend hozzárendelése HTTP- és HTTPS-végpontokhoz: Adja meg a **SecurityAccessPolicy** tulajdonságot, ha **RunAs-házirendet** szeretne alkalmazni egy szolgáltatásra, amikor a szolgáltatásjegyzék deklarálja a végponterőforrásait HTTP-vel. A HTTP-végpontokhoz rendelt portok megfelelően hozzáférés-vezérelt listák a szolgáltatás alatt futó RunAs felhasználói fiókhoz. Ha a házirend nincs beállítva, a http.sys nem fér hozzá a szolgáltatáshoz, és az ügyfél hívásaival hibákat kaphat.

A biztonsági házirendek Service Fabric-fürtben való használatát az [Alkalmazás biztonsági házirendjeinek konfigurálása](../../service-fabric/service-fabric-application-runas-security.md)című témakörben olvashat.

## <a name="implement-the-reliable-actors-security-configuration"></a>A Reliable Actors biztonsági konfigurációjának megvalósítása
A Service Fabric megbízható szereplői az aktor tervezési minta megvalósítása. Mint minden szoftver tervezési minta, a döntés, hogy egy adott minta alapul, hogy egy szoftver probléma illeszkedik a minta.

Általában használja az aktor tervezési minta segítségével modell megoldások a következő szoftverproblémák vagy biztonsági forgatókönyvek:
-   A problémás terület nagy számú (több ezer vagy több) kis, független és elszigetelt állapot- és logikai egységet foglal magában.
-   Egyszálas objektumokkal dolgozik, amelyek nem igényelnek jelentős interakciót a külső összetevőktől, beleértve az állapot lekérdezését az egy szereplőkészlet között.
-   Az aktorpéldányok nem tiltják le a hívókat előre nem látható késésekkel I/O-műveletek kibocsátásával.

A Service Fabric, a szereplők a Reliable Actors alkalmazáskeretrendszerben vannak megvalósítva. Ez a keretrendszer az aktor mintán alapul, és a [Service Fabric megbízható szolgáltatások](../../service-fabric/service-fabric-reliable-services-introduction.md)ra épül. Minden megbízható aktor szolgáltatás, amely írása egy particionált állapotalapú megbízható szolgáltatás.

Minden aktor egy aktortípus példányaként van definiálva, amely megegyezik azzal, ahogyan a .NET objektum egy .NET típusú példány. Például egy **aktortípus,** amely megvalósítja a kalkulátor funkcióit, számos ilyen típusú szereplővel rendelkezhet, amelyek a fürt különböző csomópontjain vannak elosztva. Az elosztott szereplők mindegyike egyedileg jellemzi az aktor azonosító.

[A Replikátor biztonsági konfigurációi](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) a replikáció során használt kommunikációs csatorna védelmére szolgálnak. Ez a konfiguráció megakadályozza, hogy a szolgáltatások lássák egymás replikációs forgalmát, és biztosítja, hogy a magas rendelkezésre állású adatok biztonságosak legyenek. Alapértelmezés szerint egy üres biztonsági konfigurációs szakasz megakadályozza a replikáció biztonságát.
A Replikátor-konfigurációk konfigurálják a replikátort, amely felelős az aktor állapotszolgáltató állapotának rendkívül megbízhatóvá tételéért.

## <a name="configure-tls-for-azure-service-fabric"></a>TLS konfigurálása az Azure Service Fabric hez
A kiszolgálóhitelesítési folyamat [hitelesíti](../../service-fabric/service-fabric-cluster-creation-via-arm.md) a fürtkezelési végpontokat egy felügyeleti ügyfél számára. A felügyeleti ügyfél ezután felismeri, hogy a valódi fürthöz beszél. Ez a tanúsítvány [tls-t](../../service-fabric/service-fabric-cluster-creation-via-arm.md) is biztosít a HTTPS-felügyeleti API-hoz és a Service Fabric Explorer HTTPS-en keresztüli kezeléséhez.
Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt kér egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell egyeznie a fürthöz használt egyéni tartománynévvel.

Egy alkalmazás TLS-ének konfigurálásához először be kell szereznie egy hitelesítésszolgáltató által aláírt SSL/TLS-tanúsítványt. A hitelesítésszolgáltató egy megbízható harmadik fél, amely tls biztonsági célokra tanúsítványokat állít ki. Ha még nem rendelkezik SSL/TLS tanúsítvánnyal, be kell szereznie egyet egy SSL/TLS tanúsítványokat értékesítő vállalattól.

A tanúsítványnak meg kell felelnie az alábbi követelményeknek az SSL/TLS tanúsítványokhoz az Azure-ban:
-   A tanúsítványnak titkos kulcsot kell tartalmaznia.

-   A tanúsítványt kulcscseréhez kell létrehozni, és személyes adatcserefájlba (.pfx) kell exportálni.

-   A tanúsítvány tulajdonosnevének meg kell egyeznie a felhőszolgáltatás eléréséhez használt tartománynévvel.

    - Szerezzen be egy egyéni tartománynevet a felhőszolgáltatás eléréséhez.
    - Kérjen tanúsítványt egy olyan tulajdonosnévvel rendelkező hitelesítésszolgáltatótól, amely megfelel a szolgáltatás egyéni tartománynevének. Ha például az egyéni tartománynév __contoso__**.com,** a hitelesítésszolgáltató tanúsítványának **tulajdonosnevének .contoso.com** vagy __www__**.contoso.com**.

    >[!NOTE]
    >A __cloudapp__**.net** tartomány hitelesítésszolgáltatójától nem szerezhet be SSL/TLS tanúsítványt.

-   A tanúsítványnak legalább 2048 bites titkosítást kell használnia.

A HTTP protokoll nem biztonságos, és lehallgatási támadásoknak van kitéve. A HTTP-n keresztül továbbított adatokat a webböngésző egyszerű szövegként küldi el a webkiszolgálónak vagy más végpontok között. A támadók elkaphatják és megtekinthetik a HTTP-n keresztül küldött bizalmas adatokat, például a hitelkártya adatait és a fiókbejelentkezéseket. Amikor az adatokat a böngészőn keresztül küldik vagy teszik közzé HTTPS-en keresztül, az SSL biztosítja, hogy a bizalmas adatok titkosítva legyenek és biztonságosak legyenek a lehallgatástól.

Az SSL/TLS-tanúsítványok használatáról a [TLS konfigurálása az Azure-ban](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md)című témakörben olvashat bővebben.

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Hálózati elkülönítés és biztonság használata az Azure Service Fabric használatával
Állítson be egy 3 csomópontos biztonságos fürtöt az [Azure Resource Manager sablon](../../azure-resource-manager/templates/template-syntax.md) mintaként való használatával. A sablon és a hálózati biztonsági csoportok segítségével szabályozhatja a bejövő és kimenő hálózati forgalmat.

A sablon rendelkezik egy NSG minden a virtuális gép méretezési csoportok, és a készleten kívüli forgalom szabályozására szolgál. A szabályok alapértelmezés szerint úgy vannak beállítva, hogy a sablonban megadott rendszerszolgáltatásokhoz és alkalmazásportokhoz szükséges összes forgalmat engedélyezze. Tekintse át ezeket a szabályokat, és hajtsa végre az igényeinek megfelelő módosításokat, beleértve az alkalmazásokhoz új szabályok hozzáadását is.

További információ: [Common networking scenarios for Azure Service Fabric.](../../service-fabric/service-fabric-patterns-networking.md)

## <a name="set-up-azure-key-vault-for-security"></a>Az Azure Key Vault beállítása a biztonság érdekében
A Service Fabric tanúsítványokat használ a fürt és az alkalmazások védelméhez hitelesítés és titkosítás biztosítására.

A Service Fabric X.509-es tanúsítványokat használ a fürt védelméhez és az alkalmazás biztonsági szolgáltatásainak biztosításához. Az Azure Key Vault használatával kezelheti a Service Fabric-fürtök [tanúsítványait](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) az Azure-ban. A fürtöket létrehozó Azure-erőforrás-szolgáltató lekéri a tanúsítványokat egy key vaultból. A szolgáltató ezután telepíti a tanúsítványokat a virtuális gépeken, amikor a fürt telepítve van az Azure-ban.

Tanúsítványkapcsolat van az [Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), a Service Fabric-fürt és a tanúsítványokat használó erőforrás-szolgáltató között. A fürt létrehozásakor a tanúsítványkapcsolattal kapcsolatos információk egy key vaultban tárolódnak.

A kulcstartó beállításának két alapvető lépése van:
1. Hozzon létre egy erőforráscsoportot kifejezetten a key vaulthoz.

    Azt javasoljuk, hogy helyezze a key vault saját erőforráscsoportba. Ez a művelet segít megakadályozni a kulcsok és a titkos kulcsok elvesztését, ha más erőforráscsoportokat távolít el, például a tárolást, a számítást vagy a fürtöt tartalmazó csoportot. A key vaultot tartalmazó erőforráscsoportnak ugyanabban a régióban kell lennie, mint az azt használó fürtnek.

2. Hozzon létre egy key vault az új erőforráscsoportban.

    A key vault engedélyezni kell a központi telepítéshez. A számítási erőforrás-szolgáltató ezután lejuthat a tanúsítványokat a tárolóból, és telepítheti őket a virtuálisgép-példányokon.

Ha többet szeretne tudni a key vault beállításáról, olvassa el [a Mi az Azure Key Vault?](../../key-vault/general/overview.md)

## <a name="assign-users-to-roles"></a>Felhasználók hozzárendelése szerepkörökhöz
Miután létrehozta a fürtet képviselő alkalmazásokat, rendelje hozzá a felhasználókat a Service Fabric által támogatott szerepkörökhöz: csak olvasható és rendszergazda. Ezeket a szerepköröket az Azure Portal használatával rendelheti hozzá.

>[!NOTE]
> A szerepkörök Service Fabric-ben való használatáról további információt a [Service Fabric-ügyfelek szerepköralapú hozzáférés-vezérlése című témakörben talál.](../../service-fabric/service-fabric-cluster-security-roles.md)

Az Azure Service Fabric két hozzáférés-vezérlési típust támogat a [Service Fabric-fürthöz](../../service-fabric/service-fabric-cluster-creation-via-arm.md)kapcsolódó ügyfelek számára: a rendszergazdát és a felhasználót. A fürtrendszergazdája a hozzáférés-vezérlés segítségével korlátozhatja a hozzáférést bizonyos fürtműveletekhez a felhasználók különböző csoportjai számára. A hozzáférés-vezérlés biztonságosabbá teszi a fürtöt.

## <a name="next-steps"></a>További lépések

- [A Service Fabric biztonsági ellenőrzőlistája](service-fabric-checklist.md)
- Állítsa be a Service [Fabric-fejlesztői környezetet.](../../service-fabric/service-fabric-get-started.md)
- További információ a [Service Fabric támogatási lehetőségeiről.](../../service-fabric/service-fabric-support.md)
