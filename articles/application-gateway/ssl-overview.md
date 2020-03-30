---
title: Az SSL befejezésének engedélyezése az Azure Application Gateway-en
description: Ez a cikk az Application Gateway végpontok között SSL-támogatás áttekintése.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 9c4e6124acdbb35233f8e829f43d2665fd4a5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284806"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Az SSL-végződtetés és az SSL végpontok között történő leségének áttekintése az Application Gateway alkalmazásátjáróval

A Secure Sockets Layer (SSL) a webkiszolgáló és a böngésző közötti titkosított kapcsolat létrehozásának szabványos biztonsági technológiája. Ez a hivatkozás biztosítja, hogy a webkiszolgáló és a böngészők között átadott összes adat privát és titkosított maradjon. Az alkalmazásátjáró támogatja az SSL-végződtetést az átjárón, valamint a végpontok közötti SSL-titkosítást.

## <a name="ssl-termination"></a>SSL leállítása

Az Application Gateway támogatja az SSL-lezárást az átjárónál, ami után a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz. Az SSL-végződtetés nek számos előnye van az alkalmazásátjárón:

- **Jobb teljesítmény** – Az SSL-visszafejtés során elért legnagyobb teljesítmény a kezdeti kézfogás. A teljesítmény javítása érdekében a visszafejtést lefolytató kiszolgáló gyorsítótárazza az SSL-munkamenet-azonosítókat, és kezeli a TLS-munkamenetjegyeket. Ha ez az alkalmazásátjárón történik, az ugyanazon ügyféltől származó összes kérelem használhatja a gyorsítótárazott értékeket. Ha ez történik a háttér-kiszolgálókon, majd minden alkalommal, amikor az ügyfél kérései megy egy másik kiszolgálóra az ügyfél nek újra hitelesítenie kell. A TLS-jegyek használata segíthet enyhíteni ezt a problémát, de nem minden ügyfél támogatja őket, és nehéz lehet konfigurálni és kezelni.
- **A háttérkiszolgálók jobb kihasználtsága** – az SSL/TLS feldolgozás nagyon nagy cpu-igényes, és a kulcsméretek növekedésével egyre intenzívebbé válik. Ha eltávolítja ezt a munkát a háttérkiszolgálókról, akkor arra összpontosíthatnak, amiben a leghatékonyabbak, és tartalmat szolgáltatnak.
- **Intelligens útválasztás** – A forgalom visszafejtésével az alkalmazásátjáró hozzáfér a kérelem tartalmához, például a fejlécekhez, uri-khoz és így tovább, és ezeket az adatokat a kérelmek továbbítására használhatja.
- **Tanúsítványkezelés** – A tanúsítványokat csak az alkalmazásátjáróra kell megvásárolni és telepíteni, és nem az összes háttérkiszolgálót. Ez időt és pénzt takarít meg.

Az SSL-végződés konfigurálásához ssl-tanúsítványt kell hozzáadni a figyelőhöz ahhoz, hogy az alkalmazásátjáró az SSL-protokoll specifikációjának megfelelő szimmetrikus kulcsot lehessen levezetni. A szimmetrikus kulcs ezután az átjárónak küldött forgalom titkosítására és visszafejtésére szolgál. Az SSL-tanúsítványnak személyes adatcsere (PFX) formátumban kell lennie. Ez a fájlformátum lehetővé teszi az alkalmazásátjáró által a forgalom titkosításához és visszafejtéséhez szükséges személyes kulcs exportálását.

> [!NOTE] 
>
> Az alkalmazásátjáró nem biztosít új tanúsítvány létrehozására vagy tanúsítványkérelem hitelesítésszolgáltatónak való elküldésére való képességet.

Az SSL-kapcsolat működéséhez meg kell győződnie arról, hogy az SSL-tanúsítvány megfelel az alábbi feltételeknek:

- Az aktuális dátum és idő a tanúsítvány "Érvényesség kezdete" és "Érvényesség kezdete" dátumtartományán belül van.
- A tanúsítvány „köznapi neve” (Common Name, CN) megegyezik a kérésben szereplő gazdafejlécével. Ha például az ügyfél böngészője a(z) `https://www.contoso.com/` címre küldi a kérést, a köznapi névnek is a(z) `www.contoso.com` címnek kell lennie.

### <a name="certificates-supported-for-ssl-termination"></a>Az SSL-megszüntetéshez támogatott tanúsítványok

Az alkalmazásátjáró a következő típusú tanúsítványokat támogatja:

- Hitelesítésszolgáltatói (hitelesítésszolgáltatói) tanúsítvány: A hitelesítésszolgáltatói tanúsítvány egy hitelesítésszolgáltató (CA) által kibocsátott digitális tanúsítvány
- EV (Kiterjesztett érvényesítési) tanúsítvány: Az EV-tanúsítvány olyan tanúsítvány, amely megfelel az iparági szabványnak megfelelő tanúsítványra vonatkozó irányelveknek. Ez viszont a böngésző lokátor bar zöld és közzéteszi a cég nevét is.
- Helyettesítő tanúsítvány: Ez a tanúsítvány tetszőleges számú altartományt támogat a *.site.com alapján, ahol az altartomány lecserélné a *-ot. Ez azonban nem támogatja a site.com, így abban az esetben, ha a felhasználók a vezető "www" beírása nélkül férnek hozzá a webhelyéhez, a helyettesítő tanúsítvány nem terjed ki erre.
- Önaláírt tanúsítványok: Az ügyfélböngészők nem bíznak ezekben a tanúsítványokban, és figyelmeztetik a felhasználót, hogy a virtuális szolgáltatás tanúsítványa nem része megbízhatósági láncnak. Az önaláírt tanúsítványok tesztelésre vagy olyan környezetekbe is alkalmasak, ahol a rendszergazdák irányítják az ügyfeleket, és biztonságosan megkerülhetik a böngésző biztonsági figyelmeztetéseit. Az éles számítási feladatok soha nem használhatnak önaláírt tanúsítványokat.

További információt az [SSL-végződtetés konfigurálása alkalmazásátjáróval című témakörben talál.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="size-of-the-certificate"></a>A tanúsítvány mérete
Ellenőrizze az [Application Gateway korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) szakasz tudni a maximális SSL tanúsítvány mérete támogatott.

## <a name="end-to-end-ssl-encryption"></a>Végponttól végpontig ssl titkosítás

Előfordulhat, hogy egyes ügyfelek nem kívánnak titkosítatlan kommunikációt a háttérkiszolgálókkal. Ennek okai lehetnek biztonsági és megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az Application Gateway támogatja a teljes körű SSL-titkosítást.

A teljes körű SSL-kapcsolat segítségével titkosítva küldheti a bizalmas adatokat a háttérkiszolgálók felé, miközben kiaknázhatja az Application Gateway által biztosított 7. rétegbeli terheléselosztási funkciókat. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Ha teljes körű SSL-kommunikációs üzemmóddal van konfigurálva, az Application Gateway leállítja az SSL-munkamenetet az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az Application Gateway ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóval, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz. A végpontok között az SSL protokollbeállítás beállításával engedélyezve van a [háttérrendszer HTTP-beállításában](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) HTTPS-re, amelyet ezután egy háttérkészletre alkalmaz.

Az SSL-szabályzat az előtér- és a háttérforgalomra egyaránt vonatkozik. Az előtér-rendszer, az Application Gateway működik, mint a kiszolgáló, és kényszeríti a házirendet. A háttérrendszeren az Application Gateway ügyfélként működik, és az SSL-kézfogás során a protokoll/titkosítási adatokat küldi el preferenciaként.

Az alkalmazásátjáró csak azokkal a háttérrendszer-példányokkal kommunikál, amelyek a tanúsítványukat az alkalmazásátjáróval engedélyezési listán tartják, vagy amelyek tanúsítványait jól ismert hitelesítésszolgáltatói hatóságok írják alá, ahol a tanúsítvány KN-je megegyezik a HTTP-ben szereplő állomásnévvel háttérbeállításokat. Ezek közé tartoznak a megbízható Azure-szolgáltatások, például az Azure App-szolgáltatás webalkalmazásai és az Azure API Management.

Ha a háttérkészlet tagjainak tanúsítványait nem írták alá jól ismert hitelesítésszolgáltatói hatóságok, akkor a háttérkészlet minden olyan példányát, amelyen a végpontok közötti SSL engedélyezve van, egy tanúsítvánnyal kell konfigurálni a biztonságos kommunikáció engedélyezéséhez. A tanúsítvány hozzáadása biztosítja, hogy az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

> [!NOTE] 
>
> A hitelesítési tanúsítványok beállítása nem szükséges a megbízható Azure-szolgáltatásokhoz, például az Azure App-szolgáltatás webalkalmazásaihoz és az Azure API Managementhez.

> [!NOTE] 
>
> A **háttérrendszer HTTP-beállításához** a háttérkiszolgálók hitelesítéséhez hozzáadott tanúsítvány megegyezhet az SSL-végződés **figyelőjéhez** az alkalmazásátjárón hozzáadott tanúsítvánnyal, vagy a fokozott biztonság érdekében.

![Teljes körű ssl eset][1]

Jelen példa esetén a TLS1.2-t használó kérések a Pool1-ben lévő háttérkiszolgálókra vannak átirányítva teljes körű SSL segítségével.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Teljes körű SSL és a tanúsítványok engedélyezése

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványokat az alkalmazásátjáróval engedélyezési listán kell szerepelni az előző lépésekben leírtak szerint, mielőtt használni lehetne őket.

> [!NOTE]
> A hitelesítési tanúsítványok beállítása nem szükséges a megbízható Azure-szolgáltatásokhoz, például az Azure App Service-hez.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Teljes körű SSL a v2 termékváltozattal

A hitelesítési tanúsítványok elavultak, és az Application Gateway v2 termékváltozatában lévő megbízható főtanúsítványokkal váltak. A hitelesítési tanúsítványokhoz hasonlóan működnek, néhány fő eltéréssel:

- A http-háttérbeállításokban az állomásnévnek megfelelő hitelesítésszolgáltatói hatóságok által aláírt tanúsítványok nem igényelnek további lépést a végpontok közötti SSL működéséhez. 

   Ha például a háttértanúsítványokat egy jól ismert hitelesítésszolgáltató állítja ki, és contoso.com kn-je van, és a háttérhttp-beállítás gazdagépmezője is contoso.com, akkor nincs szükség további lépésekre. Beállíthatja a háttérhttp beállítás protokoll HTTPS és mind az állapotminta és az adatok elérési útja lenne SSL engedélyezve. Ha az Azure App Service vagy más Azure-webszolgáltatások háttérként, majd ezek implicit módon megbízható is, és nincs szükség további lépésekre a végpontok közötti SSL.
   
> [!NOTE] 
>
> Az SSL-tanúsítványok megbízhatóságához a háttérkiszolgáló tanúsítványát az Application Gateway megbízható tárolójában található hitelesítésszolgáltatónak kell kiállítania. annak megállapítása, hogy a kiállító hitelesítésszolgáltató tanúsítványát megbízható hitelesítésszolgáltató adta-e ki, és így tovább, amíg nem talál megbízható hitelesítésszolgáltatót (ekkor megbízható, biztonságos kapcsolat jön létre), vagy nem található megbízható hitelesítésszolgáltató (ekkor az Alkalmazásátjáró megjelöli a háttérkiszolgálót egészségtelen). Ezért ajánlott, hogy a háttérkiszolgálói tanúsítvány a legfelső szintű és a köztes hitelesítésszolgáltatót is tartalmazza.

- Ha a tanúsítvány önaláírt, vagy ismeretlen közvetítők által aláírt, akkor a 2 termékváltozatban az SSL végpontig történő engedélyezéséhez megbízható főtanúsítványt kell definiálni. Az Application Gateway csak olyan háttérrendszerekkel kommunikál, amelyek kiszolgálói tanúsítványának főtanúsítványa megegyezik a készlethez társított háttérhttp-beállításmegbízható főtanúsítványainak egyik éneklistájával.

> [!NOTE] 
>
> Az önaláírt tanúsítványnak egy tanúsítványlánc részét kell, hogy legyen. A V2 Termékváltozat nem támogatja a lánc nélküli egyetlen önaláírt tanúsítványt.

- A főtanúsítvány-egyezés mellett az Application Gateway azt is ellenőrzi, hogy a háttérrendszer http-beállításában megadott állomásbeállítás megegyezik-e a háttérkiszolgáló SSL-tanúsítványa által bemutatott common name (CN) beállításával. Amikor SSL-kapcsolatot próbál létrehozni a háttérrendszerhez, az Application Gateway beállítja a kiszolgálónév-jelzési (SNI) bővítményt a háttérrendszer http-beállításában megadott állomásra.
- Ha a háttérrendszer http-beállításában a gazdagépmező helyett a **gazdagépnév** kiválasztása, akkor az SNI-fejléc mindig a háttérkészlet teljes tartományneve lesz, és a háttérkiszolgáló SSL-tanúsítványának meg kell egyeznie a teljes tartománynévvel. Háttérkészlet-tagok IP-k nem támogatottebben a forgatókönyvben.
- A főtanúsítvány egy base64 kódolású legfelső szintű tanúsítvány a háttérkiszolgálói tanúsítványokból.

## <a name="next-steps"></a>További lépések

Miután tudomást szerzett a végpontok között SSL, nyissa [meg a végpontok között SSL konfigurálása az Application Gateway a PowerShell használatával](application-gateway-end-to-end-ssl-powershell.md) hozzon létre egy alkalmazásátjáró tend to end SSL használatával.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
