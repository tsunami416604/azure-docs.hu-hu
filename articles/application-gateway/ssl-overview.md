---
title: A végpontok közötti SSL engedélyezése az Azure Application Gateway
description: Ez a cikk áttekintést nyújt a Application Gateway végpontok közötti SSL-támogatásról.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: e52a32c1897a7add939880fbe27d6b4b7fbee0bd
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883586"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Az SSL-lezárás és a végpontok közötti SSL áttekintése Application Gateway

A SSL (SSL) a szabványos biztonsági technológia, amely egy webkiszolgáló és egy böngésző közötti titkosított kapcsolat létesítésére szolgál. Ez a hivatkozás biztosítja, hogy a webkiszolgáló és a böngészők között átadott összes adatforgalom magán-és titkosítatlan maradjon. Az Application Gateway egyszerre támogatja az SSL-lezárást az átjárón, valamint a végpontok közötti SSL-titkosítást is.

## <a name="ssl-termination"></a>SSL leállítása

Az Application Gateway támogatja az SSL-lezárást az átjárónál, ami után a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz. Az alkalmazás-átjárón számos előnnyel jár az SSL-lezárás:

- **Jobb teljesítmény** – a legnagyobb teljesítmény az SSL-visszafejtés során a kezdeti kézfogás. A teljesítmény javítása érdekében a visszafejtést végző kiszolgáló gyorsítótárazza az SSL-munkamenetek azonosítóit, és felügyeli a TLS-munkamenetek jegyeit. Ha ezt az Application Gateway végzi el, az azonos ügyféltől érkező összes kérelem használhatja a gyorsítótárazott értékeket. Ha a háttér-kiszolgálókon történik, akkor minden alkalommal, amikor az ügyfél kérései egy másik kiszolgálóra kerülnek, az ügyfélnek újra kell hitelesítenie magát. A TLS-jegyek használata segít enyhíteni ezt a problémát, de az összes ügyfél nem támogatja, és bonyolult lehet a konfigurálás és a felügyelet.
- **A háttér-kiszolgálók jobb kihasználtsága – az** SSL/TLS-feldolgozás rendkívül nagy CPU-igényű, és egyre intenzívebben növekszik a kulcsok mérete. Ha eltávolítja ezt a munkát a háttér-kiszolgálókról, az lehetővé teszi számukra, hogy a lehető leghatékonyabban lássák a tartalmat.
- **Intelligens útválasztás** – a forgalom visszafejtésével az Application Gateway hozzáfér a kérelem tartalmához, például a fejlécekhez, az URI-hoz és így tovább, és ezeket az adatokat a kérelmek továbbítására használhatja.
- **Tanúsítványkezelő** – a tanúsítványokat csak az Application gatewayben kell megvásárolni és telepíteni, nem minden háttér-kiszolgálóra. Ez időt és pénzt takarít meg.

Az SSL-lezárás konfigurálásához egy SSL-tanúsítványt kell hozzáadni a figyelőhöz, hogy az Application Gateway az SSL protokoll specifikációja alapján egy szimmetrikus kulcsot származtatjon. A szimmetrikus kulcs ezután az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. Az SSL-tanúsítványnak személyes információcsere (PFX) formátumúnak kell lennie. Ez a fájlformátum lehetővé teszi az Application Gateway által igényelt titkos kulcs exportálását a forgalom titkosításának és visszafejtésének elvégzéséhez.

> [!NOTE] 
>
> Az Application Gateway nem képes új tanúsítvány létrehozására vagy tanúsítványkérelem küldésére a hitelesítésszolgáltatónak.

Az SSL-kapcsolat működéséhez biztosítania kell, hogy az SSL-tanúsítvány megfeleljen a következő feltételeknek:

- Az aktuális dátum és idő a tanúsítvány érvényességi ideje és érvényességi tartománya között van.
- A tanúsítvány „köznapi neve” (Common Name, CN) megegyezik a kérésben szereplő gazdafejlécével. Ha például az ügyfél böngészője a(z) `https://www.contoso.com/` címre küldi a kérést, a köznapi névnek is a(z) `www.contoso.com` címnek kell lennie.

### <a name="certificates-supported-for-ssl-termination"></a>SSL-lezáráshoz támogatott tanúsítványok

Az Application Gateway a következő típusú tanúsítványokat támogatja:

- HITELESÍTÉSSZOLGÁLTATÓI (hitelesítésszolgáltató) tanúsítvány: A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány egy hitelesítésszolgáltató (CA) által kiadott digitális tanúsítvány
- EV (bővített ellenőrző) tanúsítvány: Az EV-tanúsítvány az iparági szabványoknak megfelelő tanúsítványokra vonatkozó irányelvek. Ezzel bekapcsolja a böngésző lokátorának zöldjét, és közzéteszi a cég nevét is.
- Helyettesítő tanúsítvány: Ez a tanúsítvány a *. site.com alapján tetszőleges számú altartományt támogat, ahol az altartománya lecseréli a *-t. Ez azonban nem támogatja a site.com, így ha a felhasználók a "www" beírása nélkül férnek hozzá a webhelyhez, a helyettesítő karakteres tanúsítvány nem fedi le azt.
- Önaláírt tanúsítványok: Az ügyféloldali böngészők nem bíznak a tanúsítványokban, és figyelmeztetik a felhasználót, hogy a virtuális szolgáltatás tanúsítványa nem része egy megbízhatósági láncnak. Az önaláírt tanúsítványok olyan tesztelési és környezeti környezetekben használhatók, ahol a rendszergazdák vezérelhetik az ügyfeleket, és biztonságosan kihagyhatják a böngésző biztonsági riasztásait. Az éles munkaterhelések soha nem használhatnak önaláírt tanúsítványokat.

További információ: [az SSL-lezárás konfigurálása az Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>A tanúsítvány mérete
Az SSL-tanúsítvány maximális méretének megismeréséhez olvassa el a [Application Gateway határértékek](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) szakaszt.

## <a name="end-to-end-ssl-encryption"></a>Végpontok közötti SSL-titkosítás

Előfordulhat, hogy egyes ügyfelek nem kívánnak titkosítatlan kommunikációt a háttér-kiszolgálókon. Ennek okai lehetnek biztonsági és megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az Application Gateway támogatja a teljes körű SSL-titkosítást.

A teljes körű SSL-kapcsolat segítségével titkosítva küldheti a bizalmas adatokat a háttérkiszolgálók felé, miközben kiaknázhatja az Application Gateway által biztosított 7. rétegbeli terheléselosztási funkciókat. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Ha teljes körű SSL-kommunikációs üzemmóddal van konfigurálva, az Application Gateway leállítja az SSL-munkamenetet az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az Application Gateway ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóval, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz. A végpontok közötti SSL engedélyezve van, ha a [háttérbeli http-beállítás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) protokoll BEÁLLÍTÁSát HTTPS értékre állítja, amelyet a rendszer a háttérbeli készletre alkalmaz.

Az SSL-szabályzat a felületi és a háttérbeli adatforgalomra is érvényes. Az előtérben Application Gateway kiszolgálóként működik, és érvényesíti a szabályzatot. A háttérben Application Gateway ügyfélként viselkedik, és az SSL-kézfogás során a protokoll/titkosítási adatokat küldi el.

Az Application Gateway csak azokkal a háttér-példányokkal kommunikál, amelyeken engedélyezve van a tanúsítvány vagy az Application Gateway, vagy amelyek tanúsítványait jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok írták alá, ahol a tanúsítvány a HTTP-ben megfelel a gazdagép neveként háttér-beállítások. Ezek közé tartoznak a megbízható Azure-szolgáltatások, például az Azure app Service Web Apps és az Azure API Management.

Ha a háttérbeli készlet tagjainak tanúsítványait nem a jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok írták alá, akkor a háttérrendszer összes példányát, a végpontok közötti SSL-t engedélyezve kell lennie egy tanúsítvánnyal, hogy engedélyezze a biztonságos kommunikációt. A tanúsítvány hozzáadásával biztosítható, hogy az Application Gateway csak az ismert háttérbeli példányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

> [!NOTE] 
>
> A hitelesítési tanúsítvány beállítása nem szükséges olyan megbízható Azure-szolgáltatásokhoz, mint például az Azure app Service Web Apps és az Azure API Management.

> [!NOTE] 
>
> A háttérrendszer-kiszolgálók hitelesítéséhez a háttérbeli **http** -beállításhoz hozzáadott tanúsítvány megegyező lehet, mint a **figyelőhöz** az Application gatewayben az SSL-lezáráshoz hozzáadott tanúsítvány, illetve a fokozott biztonság érdekében.

![Teljes körű ssl eset][1]

Jelen példa esetén a TLS1.2-t használó kérések a Pool1-ben lévő háttérkiszolgálókra vannak átirányítva teljes körű SSL segítségével.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Teljes körű SSL és a tanúsítványok engedélyezése

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Ezeket a tanúsítványokat az Application Gatewaynek kell megadnia az előző lépésekben leírtak szerint, mielőtt használni tudnák őket.

> [!NOTE]
> A hitelesítési tanúsítvány beállítása nem szükséges a megbízható Azure-szolgáltatásokhoz, például a Azure App Servicehoz.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Végpontok közötti SSL a v2 SKU-val

A hitelesítési tanúsítványok elavultak, és a megbízható főtanúsítványok a Application Gateway v2 SKU-ban lettek lecserélve. A hitelesítési tanúsítványokhoz hasonlóan működnek, néhány fő különbséggel:

- Azok a tanúsítványok, amelyeket jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok aláírtak, és amelyek a HTTP-háttér-beállításokban szereplő állomásnévnek felelnek meg, nincs szükség további lépésre a végpontok közötti SSL működéséhez. 

   Ha például a háttérbeli tanúsítványokat egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, és a contoso.com rendelkezik, és a háttérrendszer http-beállításának gazdagép mezője is contoso.com értékre van állítva, akkor nincs szükség további lépésekre. A háttérbeli http-beállítás protokollját HTTPS-re állíthatja, és az állapot-mintavétel és az adatelérési út is engedélyezve lesz az SSL-ben. Ha Azure App Service vagy más Azure-webszolgáltatást használ a háttérként, akkor ezek implicit módon megbízhatóak, és a végpontok közötti SSL eléréséhez nincs szükség további lépésekre.
   
> [!NOTE] 
>
> Ahhoz, hogy egy SSL-tanúsítvány megbízható legyen, a háttér-kiszolgáló tanúsítványát egy olyan HITELESÍTÉSSZOLGÁLTATÓ állította ki, amely szerepel a Application Gateway megbízható tárolójában. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki, akkor a Application Gateway megkeresi annak ellenőrzéséhez, hogy a kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát megbízható HITELESÍTÉSSZOLGÁLTATÓ adta-e ki, és hogy nem található-e megbízható hitelesítésszolgáltató (ekkor a megbízható, biztonságos kapcsolatok létrejönnek), vagy nem található megbízható HITELESÍTÉSSZOLGÁLTATÓ (ekkor a Application Gateway meg fogja jelölni a háttérrendszer nem megfelelő állapotú). Ezért javasoljuk, hogy a háttér-kiszolgáló tanúsítványa tartalmazza a legfelső szintű és a intermidiate CAs-t is.

- Ha a tanúsítvány önaláírt vagy ismeretlen közvetítők által van aláírva, akkor a végpontok közötti SSL engedélyezéséhez a v2 SKU-ban meg kell adni egy megbízható főtanúsítványt. Application Gateway csak olyan háttérrel kommunikál, amelynek a kiszolgálói tanúsítvány főtanúsítványa megegyezik a készlethez társított háttérbeli http-beállításban található megbízható legfelső szintű tanúsítványok egyikével.

> [!NOTE] 
>
> Az önaláírt tanúsítványnak egy tanúsítványlánc részét kell képeznie. A v2 SKU-ban nem támogatott egyetlen önaláírt tanúsítvány, amelynek nincs lánca.

- A főtanúsítványok egyeztetése mellett a Application Gateway azt is ellenőrzi, hogy a háttérbeli http-beállításban megadott gazdagép-beállítás megegyezik-e a háttér-kiszolgáló SSL-tanúsítványa által megjelenített köznapi névvel (CN). Amikor SSL-kapcsolatot próbál létesíteni a háttérrel, Application Gateway beállítja a Kiszolgálónév jelzése (SNI) bővítményt a háttérbeli http-beállításban megadott gazdagépre.
- Ha a háttérbeli http-beállításban a gazdagép mező helyett a kiválasztó **állomásnév elemet** választja, akkor a SNI fejléc mindig a háttérbeli készlet teljes tartománynevére van beállítva, és a háttér-kiszolgáló SSL-tanúsítványának CN elemének meg kell egyeznie a teljes tartománynévvel. Ebben a forgatókönyvben nem támogatottak a háttérbeli készlet tagjai az IP-címekkel.
- A főtanúsítvány a háttér-kiszolgálói tanúsítványok Base64 kódolású főtanúsítványa.

## <a name="next-steps"></a>További lépések

A végpontok közötti SSL megismerése után nyissa meg a végpontok közötti [SSL konfigurálását az Application Gateway és a PowerShell használatával](application-gateway-end-to-end-ssl-powershell.md) , és hozzon létre egy Application Gateway-t a végpontok közötti SSL használatával.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
