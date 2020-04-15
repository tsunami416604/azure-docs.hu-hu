---
title: A TLS befejezésének engedélyezése az Azure Application Gateway-en
description: Ez a cikk az Application Gateway végpontok között a TLS-támogatás áttekintése.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311853"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>A TLS-végződtetés és a végpontok végéig szóló TLS áttekintése az Application Gateway alkalmazásátjáróval

A Transport Layer Security (TLS), korábbi nevén Secure Sockets Layer (SSL) a webkiszolgáló és a böngésző közötti titkosított kapcsolat létrehozásának szabványos biztonsági technológiája. Ez a hivatkozás biztosítja, hogy a webkiszolgáló és a böngészők között átadott összes adat privát és titkosított maradjon. Az alkalmazásátjáró támogatja a TLS-végződtetést az átjárón, valamint a végpontok közötti TLS-titkosítást.

## <a name="tls-termination"></a>A TLS megszüntetése

Az Application Gateway támogatja a TLS-végződtetést az átjárón, amely után a forgalom általában titkosítatlanul áramlik a háttérkiszolgálókra. A TLS-végződtetés nek számos előnye van az alkalmazásátjárón:

- **Jobb teljesítmény** – A TLS-visszafejtés során elért legnagyobb teljesítmény a kezdeti kézfogás. A teljesítmény javítása érdekében a visszafejtést lefolytató kiszolgáló gyorsítótárazza a TLS-munkamenet-azonosítókat, és kezeli a TLS-munkamenetjegyeket. Ha ez az alkalmazásátjárón történik, az ugyanazon ügyféltől származó összes kérelem használhatja a gyorsítótárazott értékeket. Ha ez történik a háttér-kiszolgálókon, majd minden alkalommal, amikor az ügyfél kérései megy egy másik kiszolgálóra az ügyfél nek újra hitelesítenie kell. A TLS-jegyek használata segíthet enyhíteni ezt a problémát, de nem minden ügyfél támogatja őket, és nehéz lehet konfigurálni és kezelni.
- **A háttérkiszolgálók jobb kihasználtsága** – az SSL/TLS feldolgozás nagyon nagy cpu-igényes, és a kulcsméretek növekedésével egyre intenzívebbé válik. Ha eltávolítja ezt a munkát a háttérkiszolgálókról, akkor arra összpontosíthatnak, amiben a leghatékonyabbak, és tartalmat szolgáltatnak.
- **Intelligens útválasztás** – A forgalom visszafejtésével az alkalmazásátjáró hozzáfér a kérelem tartalmához, például a fejlécekhez, uri-khoz és így tovább, és ezeket az adatokat a kérelmek továbbítására használhatja.
- **Tanúsítványkezelés** – A tanúsítványokat csak az alkalmazásátjáróra kell megvásárolni és telepíteni, és nem az összes háttérkiszolgálót. Ez időt és pénzt takarít meg.

A TLS-végződés konfigurálásához tls/SSL-tanúsítványt kell hozzáadni a figyelőhöz ahhoz, hogy az alkalmazásátjáró a TLS/SSL protokoll specifikációja szerint szimmetrikus kulcsot hozhasson le. A szimmetrikus kulcs ezután az átjárónak küldött forgalom titkosítására és visszafejtésére szolgál. A TLS/SSL tanúsítványnak személyes adatcsere (PFX) formátumúnak kell lennie. Ez a fájlformátum lehetővé teszi az alkalmazásátjáró által a forgalom titkosításához és visszafejtéséhez szükséges személyes kulcs exportálását.

> [!NOTE] 
>
> Az alkalmazásátjáró nem biztosít új tanúsítvány létrehozására vagy tanúsítványkérelem hitelesítésszolgáltatónak való elküldésére való képességet.

Ahhoz, hogy a TLS-kapcsolat működjön, meg kell győződnie arról, hogy a TLS/SSL tanúsítvány megfelel az alábbi feltételeknek:

- Az aktuális dátum és idő a tanúsítvány "Érvényesség kezdete" és "Érvényesség kezdete" dátumtartományán belül van.
- A tanúsítvány „köznapi neve” (Common Name, CN) megegyezik a kérésben szereplő gazdafejlécével. Ha például az ügyfél böngészője a(z) `https://www.contoso.com/` címre küldi a kérést, a köznapi névnek is a(z) `www.contoso.com` címnek kell lennie.

### <a name="certificates-supported-for-tls-termination"></a>A TLS-megszüntetéshez támogatott tanúsítványok

Az alkalmazásátjáró a következő típusú tanúsítványokat támogatja:

- Hitelesítésszolgáltatói (hitelesítésszolgáltatói) tanúsítvány: A hitelesítésszolgáltatói tanúsítvány egy hitelesítésszolgáltató (CA) által kibocsátott digitális tanúsítvány
- EV (Kiterjesztett érvényesítési) tanúsítvány: Az EV-tanúsítvány olyan tanúsítvány, amely megfelel az iparági szabványnak megfelelő tanúsítványra vonatkozó irányelveknek. Ez viszont a böngésző lokátor bar zöld és közzéteszi a cég nevét is.
- Helyettesítő tanúsítvány: Ez a tanúsítvány tetszőleges számú altartományt támogat a *.site.com alapján, ahol az altartomány lecserélné a *-ot. Ez azonban nem támogatja a site.com, így abban az esetben, ha a felhasználók a vezető "www" beírása nélkül férnek hozzá a webhelyéhez, a helyettesítő tanúsítvány nem terjed ki erre.
- Önaláírt tanúsítványok: Az ügyfélböngészők nem bíznak ezekben a tanúsítványokban, és figyelmeztetik a felhasználót, hogy a virtuális szolgáltatás tanúsítványa nem része megbízhatósági láncnak. Az önaláírt tanúsítványok tesztelésre vagy olyan környezetekbe is alkalmasak, ahol a rendszergazdák irányítják az ügyfeleket, és biztonságosan megkerülhetik a böngésző biztonsági figyelmeztetéseit. Az éles számítási feladatok soha nem használhatnak önaláírt tanúsítványokat.

További információt a [TLS-végződtetés konfigurálása alkalmazásátjáróval című témakörben talál.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="size-of-the-certificate"></a>A tanúsítvány mérete
Ellenőrizze az [Application Gateway korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) szakasz tudni a maximális TLS/SSL tanúsítvány mérete támogatott.

## <a name="end-to-end-tls-encryption"></a>Végpontok közötti TLS-titkosítás

Előfordulhat, hogy egyes ügyfelek nem kívánnak titkosítatlan kommunikációt a háttérkiszolgálókkal. Ennek okai lehetnek biztonsági és megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Az ilyen alkalmazások esetében az alkalmazásátjáró támogatja a végpontok közötti TLS-titkosítást.

A végpontok közötti TLS lehetővé teszi, hogy biztonságosan továbbítsa a bizalmas adatokat a titkosított háttérrendszernek, miközben továbbra is kihasználja a Layer 7 terheléselosztási funkcióinak előnyeit, amelyeket az alkalmazásátjáró nyújt. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Ha végpontok közötti TLS kommunikációs móddal van konfigurálva, az alkalmazásátjáró leállítja a TLS-munkameneteket az átjárón, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az alkalmazásátjáró ezután új TLS-kapcsolatot kezdeményez a háttérkiszolgálóval, és újra titkosítja az adatokat a háttérkiszolgáló nyilvános kulcsú tanúsítványával, mielőtt a kérelmet a háttérrendszernek továbbítaná. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz. A végpontok között a TLS a [háttér-HTTP-beállítás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) https-re beállításával engedélyezhető, amelyet ezután egy háttérkészletre alkalmaz.

A TLS-szabályzat az előtér- és a háttérforgalomra egyaránt vonatkozik. Az előtér-rendszer, az Application Gateway működik, mint a kiszolgáló, és kényszeríti a házirendet. A háttérrendszeren az Application Gateway ügyfélként működik, és a TLS-kézfogás során a protokoll/titkosítási adatokat küldi el preferenciaként.

Az alkalmazásátjáró csak azokkal a háttérpéldányokkal kommunikál, amelyek vagy az alkalmazásátjáróval engedélyezési listán szerepeltek, vagy amelyek tanúsítványait jól ismert hitelesítésszolgáltatói hatóságok írták alá, ahol a tanúsítvány KN-je megegyezik az állomásnévvel a HTTP-háttérbeállításokban. Ezek közé tartoznak a megbízható Azure-szolgáltatások, például az Azure App-szolgáltatás webalkalmazásai és az Azure API Management.

Ha a háttérkészlet tagjainak tanúsítványait nem írták alá jól ismert hitelesítésszolgáltatói hatóságok, akkor a háttérkészlet minden olyan példányát, amelyen a végpontok TLS engedélyezve van, egy tanúsítvánnyal kell konfigurálni a biztonságos kommunikáció engedélyezéséhez. A tanúsítvány hozzáadása biztosítja, hogy az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

> [!NOTE] 
>
> A hitelesítési tanúsítványok beállítása nem szükséges a megbízható Azure-szolgáltatásokhoz, például az Azure App-szolgáltatás webalkalmazásaihoz és az Azure API Managementhez.

> [!NOTE] 
>
> A **háttérrendszer HTTP-beállításához** a háttérkiszolgálók hitelesítéséhez hozzáadott tanúsítvány megegyezhet a TLS-végződés **figyelőjéhez** az alkalmazásátjárón hozzáadott tanúsítvánnyal, vagy a fokozott biztonság érdekében.

![végponttól a végéig tls forgatókönyv][1]

Ebben a példában a TLS1.2-t használó kérelmek a pool1 háttérkiszolgálóira irányítják a végpontok között a TLS használatával.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Teljes körű TLS és a tanúsítványok engedélyezési listája

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványokat az alkalmazásátjáróval engedélyezési listán kell szerepelni az előző lépésekben leírtak szerint, mielőtt használni lehetne őket.

> [!NOTE]
> A hitelesítési tanúsítványok beállítása nem szükséges a megbízható Azure-szolgáltatásokhoz, például az Azure App Service-hez.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Teljes körű TLS a v2 termékváltozattal

A hitelesítési tanúsítványok elavultak, és az Application Gateway v2 termékváltozatában lévő megbízható főtanúsítványokkal váltak. A hitelesítési tanúsítványokhoz hasonlóan működnek, néhány fő eltéréssel:

- Azok a tanúsítványok, amelyeket olyan jól ismert hitelesítésszolgáltatói hatóságok írtak alá, amelyek cn-je megegyezik a HTTP-háttérbeállításokban lévő állomásnévvel, nem igényelnek további lépést a végpontok közötti TLS működéséhez. 

   Ha például a háttértanúsítványokat egy jól ismert hitelesítésszolgáltató állítja ki, és contoso.com kn-je van, és a háttérhttp-beállítás gazdagépmezője is contoso.com, akkor nincs szükség további lépésekre. Beállíthatja a háttérrendszer http beállítási protokoll HTTPS és mind az állapotminta és az adatok elérési útja lenne TLS engedélyezve. Ha az Azure App Service vagy más Azure-webszolgáltatások háttérként, majd ezek implicit módon megbízható is, és nincs szükség további lépésekre a végpontok tls.
   
> [!NOTE] 
>
> Ahhoz, hogy a TLS/SSL-tanúsítványok megbízhatóak legyenek, a háttérkiszolgáló tanúsítványát olyan hitelesítésszolgáltatónak kell kiállítania, amely szerepel az Application Gateway megbízható tárolójában.Ha a tanúsítványt nem megbízható hitelesítésszolgáltató bocsátotta ki, Az Alkalmazásátjáró ezután ellenőrzi, hogy a kiállító hitelesítésszolgáltató tanúsítványát megbízható hitelesítésszolgáltató adta-e ki, és így tovább, amíg nem talál megbízható hitelesítésszolgáltatót (ekkor megbízható, biztonságos kapcsolat jön létre), vagy nem található megbízható hitelesítésszolgáltató (ekkor az Application Gateway nem megfelelő állapotúként jelöli meg a háttérkiszolgálót). Ezért ajánlott, hogy a háttérkiszolgálói tanúsítvány a legfelső szintű és a köztes hitelesítésszolgáltatót is tartalmazza.

- Ha a tanúsítvány önaláírt, vagy ismeretlen közvetítők által aláírt, akkor a tls befejezésének engedélyezéséhez a v2 Termékváltozatban megbízható főtanúsítványt kell definiálni. Az Application Gateway csak olyan háttérrendszerekkel kommunikál, amelyek kiszolgálói tanúsítványának főtanúsítványa megegyezik a készlethez társított háttérhttp-beállításmegbízható főtanúsítványainak egyik éneklistájával.

> [!NOTE] 
>
> Az önaláírt tanúsítványnak egy tanúsítványlánc részét kell, hogy legyen. A V2 Termékváltozat nem támogatja a lánc nélküli egyetlen önaláírt tanúsítványt.

- A főtanúsítvány-egyezés mellett az Application Gateway azt is ellenőrzi, hogy a háttérrendszer http-beállításában megadott állomásbeállítás megegyezik-e a háttérkiszolgáló TLS/SSL-tanúsítványa által bemutatott common name (CN) beállításával. Amikor TLS-kapcsolatot próbál létrehozni a háttérrendszerhez, az Application Gateway beállítja a kiszolgálónév-jelzési (SNI) bővítményt a háttérrendszer http-beállításában megadott állomásra.
- Ha a háttérrendszer http-beállításában a gazdagépmező helyett a **gazdagépnév** kiválasztása, akkor az SNI-fejléc mindig a háttérkészlet teljes tartományneve lesz, és a háttérkiszolgálóN TLS/SSL-tanúsítványának meg kell egyeznie a teljes tartománynévvel. Háttérkészlet-tagok IP-k nem támogatottebben a forgatókönyvben.
- A főtanúsítvány egy base64 kódolású legfelső szintű tanúsítvány a háttérkiszolgálói tanúsítványokból.

## <a name="next-steps"></a>További lépések

Miután tudomást szerzett a végpontok között TLS, nyissa [meg a konfigurálása end to end TLS alkalmazáshasználatával A PowerShell](application-gateway-end-to-end-ssl-powershell.md) egy alkalmazásátjáró segítségével end to end TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
