---
title: Végpontok közötti TLS engedélyezése az Azure Application Gateway
description: Ez a cikk áttekintést nyújt a Application Gateway végpontok közötti TLS-támogatásról.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 286c9329be38055808571d8d32c724d27a61cbf3
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855881"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>A TLS-lezárás és a végpontok közötti TLS áttekintése Application Gateway

Transport Layer Security (TLS), korábbi nevén SSL (SSL), a szabványos biztonsági technológia a webkiszolgáló és a böngésző közötti titkosított kapcsolat létesítéséhez. Ez a hivatkozás biztosítja, hogy a webkiszolgáló és a böngészők között átadott összes adatforgalom magán-és titkosítatlan maradjon. Az Application Gateway a TLS-lezárást is támogatja az átjárón, valamint a végpontok közötti TLS-titkosítást.

## <a name="tls-termination"></a>TLS-lezárás

Application Gateway támogatja a TLS-lezárást az átjárón, amely után a forgalom általában titkosítatlan folyamatokat végez a háttér-kiszolgálókon. Az Application Gateway TLS-megszakítása számos előnnyel jár:

- **Jobb teljesítmény** – a legnagyobb teljesítmény, ha a TLS-visszafejtés a kezdeti kézfogás. A teljesítmény javítása érdekében a visszafejtést végző kiszolgáló gyorsítótárazza a TLS munkamenet-azonosítókat, és felügyeli a TLS-munkamenetek jegyeit. Ha ezt az Application Gateway végzi el, az azonos ügyféltől érkező összes kérelem használhatja a gyorsítótárazott értékeket. Ha a háttér-kiszolgálókon történik, akkor minden alkalommal, amikor az ügyfél kérései egy másik kiszolgálóra kerülnek, az ügyfélnek újra kell hitelesítenie magát. A TLS-jegyek használata segít enyhíteni ezt a problémát, de az összes ügyfél nem támogatja, és bonyolult lehet a konfigurálás és a felügyelet.
- **A háttér-kiszolgálók jobb kihasználtsága – az** SSL/TLS-feldolgozás rendkívül nagy CPU-igényű, és egyre intenzívebben növekszik a kulcsok mérete. Ha eltávolítja ezt a munkát a háttér-kiszolgálókról, az lehetővé teszi számukra, hogy a lehető leghatékonyabban lássák a tartalmat.
- **Intelligens útválasztás** – a forgalom visszafejtésével az Application Gateway hozzáfér a kérelem tartalmához, például a fejlécekhez, az URI-hoz és így tovább, és ezeket az adatokat a kérelmek továbbítására használhatja.
- **Tanúsítványkezelő** – a tanúsítványokat csak az Application gatewayben kell megvásárolni és telepíteni, nem minden háttér-kiszolgálóra. Ez időt és pénzt takarít meg.

A TLS-megszakítás konfigurálásához TLS/SSL-tanúsítványt kell hozzáadni a figyelőhöz, hogy az Application Gateway a TLS/SSL protokoll specifikációja szerint szimmetrikus kulcsot származtatjon. A szimmetrikus kulcs ezután az átjárónak továbbított forgalom titkosítására és visszafejtésére szolgál. A TLS/SSL-tanúsítványnak személyes információcsere (PFX) formátumúnak kell lennie. Ez a fájlformátum lehetővé teszi az Application Gateway által igényelt titkos kulcs exportálását a forgalom titkosításának és visszafejtésének elvégzéséhez.

> [!IMPORTANT] 
> Vegye figyelembe, hogy a figyelő tanúsítványa a teljes tanúsítványlánc feltöltését igényli. 


> [!NOTE] 
>
> Az Application Gateway nem képes új tanúsítvány létrehozására vagy tanúsítványkérelem küldésére a hitelesítésszolgáltatónak.

Ahhoz, hogy a TLS-kapcsolat működjön, gondoskodnia kell arról, hogy a TLS/SSL-tanúsítvány megfeleljen a következő feltételeknek:

- Az aktuális dátum és idő a tanúsítvány érvényességi ideje és érvényességi tartománya között van.
- A tanúsítvány „köznapi neve” (Common Name, CN) megegyezik a kérésben szereplő gazdafejlécével. Ha például az ügyfél böngészője a(z) `https://www.contoso.com/` címre küldi a kérést, a köznapi névnek is a(z) `www.contoso.com` címnek kell lennie.

### <a name="certificates-supported-for-tls-termination"></a>A TLS-lezáráshoz támogatott tanúsítványok

Az Application Gateway a következő típusú tanúsítványokat támogatja:

- CA (hitelesítésszolgáltató) tanúsítvány: A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány egy hitelesítésszolgáltató (CA) által kiadott digitális tanúsítvány.
- EV (bővített érvényesítés) tanúsítvány: az EV-tanúsítvány egy olyan tanúsítvány, amely megfelel az iparági szabványnak megfelelő tanúsítványokra vonatkozó irányelveknek. Ezzel bekapcsolja a böngésző-kereső sáv zöldjét, és közzéteszi a vállalat nevét is.
- Helyettesítő tanúsítvány: Ez a tanúsítvány a *. site.com alapján tetszőleges számú altartományt támogat, ahol az altartománya lecseréli a *-t. Ez azonban nem támogatja a site.com, így ha a felhasználók a "www" beírása nélkül férnek hozzá a webhelyhez, a helyettesítő karakteres tanúsítvány nem fedi le azt.
- Önaláírt tanúsítványok: az ügyfél böngészője nem bízik meg ezekkel a tanúsítványokkal, és figyelmezteti a felhasználót, hogy a virtuális szolgáltatás tanúsítványa nem része egy megbízhatósági láncnak. Az önaláírt tanúsítványok olyan tesztelési és környezeti környezetekben használhatók, ahol a rendszergazdák vezérelhetik az ügyfeleket, és biztonságosan kihagyhatják a böngésző biztonsági riasztásait. Az éles munkaterhelések soha nem használhatnak önaláírt tanúsítványokat.

További információ: [a TLS-lezárás konfigurálása az Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>A tanúsítvány mérete
A [Application Gateway korlátozások](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) szakaszban találja a maximálisan támogatott TLS/SSL-tanúsítvány méretét.

## <a name="end-to-end-tls-encryption"></a>Végpontok közötti TLS-titkosítás

Előfordulhat, hogy egyes ügyfelek nem kívánnak titkosítatlan kommunikációt a háttér-kiszolgálókon. Ennek okai lehetnek biztonsági és megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Ilyen alkalmazások esetén az Application Gateway támogatja a végpontok közötti TLS-titkosítást.

A végpontok közötti TLS lehetővé teszi, hogy biztonságosan továbbítsa a bizalmas adatokat a háttérbe, miközben továbbra is kihasználja az Application Gateway által biztosított 7. rétegbeli terheléselosztási funkciók előnyeit. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

A végpontok közötti TLS kommunikációs mód beállítása esetén az Application Gateway leállítja a TLS-munkameneteket az átjárón, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az Application Gateway ezután kezdeményez egy új TLS-kapcsolódást a háttér-kiszolgálóval, majd újratitkosítja az adataikat a háttér-kiszolgáló nyilvánoskulcs-tanúsítványával, mielőtt továbbítja a kérést a háttérbe. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz. A végpontok közötti TLS engedélyezése a [háttérbeli http-beállítás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) protokoll-beállításának HTTPS-re való beállításával történik, amelyet a rendszer a háttérbeli készletre alkalmaz.

A TLS-házirend a felületi és a háttérbeli adatforgalomra is érvényes. Az előtérben Application Gateway kiszolgálóként működik, és érvényesíti a szabályzatot. A háttérben Application Gateway ügyfélként viselkedik, és a TLS-kézfogás során a protokoll/titkosítási adatokat küldi el.

Az Application Gateway csak azokkal a háttérbeli példányokkal kommunikál, amelyeken engedélyezve van a tanúsítvány vagy az Application Gateway, vagy amelyek tanúsítványait jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok írták alá, ahol a tanúsítvány a HTTP-háttér beállításaiban szereplő állomásnévnek felel meg. Ezek közé tartoznak a megbízható Azure-szolgáltatások, például az Azure app Service Web Apps és az Azure API Management.

Ha a háttérbeli készlet tagjainak tanúsítványait nem a jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok írták alá, akkor a háttérrendszer összes példányát, a végpontok közötti TLS-t engedélyezve kell lennie egy tanúsítvánnyal, hogy engedélyezze a biztonságos kommunikációt. A tanúsítvány hozzáadásával biztosítható, hogy az Application Gateway csak az ismert háttérbeli példányokkal kommunikáljon. Ez tovább biztosítja a végpontok közötti kommunikációt.

> [!NOTE] 
>
> A hitelesítési tanúsítvány beállítása nem szükséges olyan megbízható Azure-szolgáltatásokhoz, mint például az Azure app Service Web Apps és az Azure API Management.

> [!NOTE] 
>
> A háttérrendszer-kiszolgálók hitelesítéséhez a háttérbeli **http-beállításhoz** hozzáadott tanúsítvány megegyező lehet, mint az Application Gateway- **ben a TLS** -megszakításhoz hozzáadott tanúsítvány, vagy a fokozott biztonság érdekében.

![végpontok közötti TLS-forgatókönyv][1]

Ebben a példában a TLS 1.2-t használó kérelmeket a rendszer a Pool1 háttér-kiszolgálókra irányítja a végpontok közötti TLS protokoll használatával.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Végpontok közötti TLS és a tanúsítványok engedélyezési listája

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Ezeket a tanúsítványokat az Application Gatewaynek kell megadnia az előző lépésekben leírtak szerint, mielőtt használni tudnák őket.

> [!NOTE]
> A hitelesítési tanúsítvány beállítása nem szükséges a megbízható Azure-szolgáltatásokhoz, például a Azure App Servicehoz.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Végpontok közötti TLS a v2 SKU-val

A hitelesítési tanúsítványok elavultak, és a megbízható főtanúsítványok a Application Gateway v2 SKU-ban lettek lecserélve. A hitelesítési tanúsítványokhoz hasonlóan működnek, néhány fő különbséggel:

- Azok a tanúsítványok, amelyeket jól ismert HITELESÍTÉSSZOLGÁLTATÓI hatóságok aláírtak, és amelyek a HTTP-háttér-beállításokban szereplő állomásnévnek felelnek meg, nincs szükség további lépésre a TLS működésének befejezéséhez. 

   Ha például a háttérbeli tanúsítványokat egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ állítja ki, és a contoso.com rendelkezik, és a háttérrendszer http-beállításának gazdagép mezője is contoso.com értékre van állítva, akkor nincs szükség további lépésekre. A háttérbeli http-beállítás protokollját HTTPS-re állíthatja, és az állapot-mintavétel és az adatelérési út is TLS-kompatibilis lesz. Ha Azure App Service vagy más Azure-webszolgáltatást használ a háttérként, akkor ezek implicit módon megbízhatóak, és a végpontok közötti TLS-hez nincs szükség további lépésekre.
   
> [!NOTE] 
>
> Ahhoz, hogy a TLS/SSL-tanúsítvány megbízható legyen, a háttér-kiszolgáló tanúsítványát olyan HITELESÍTÉSSZOLGÁLTATÓ bocsátotta ki, amely szerepel a Application Gateway megbízható tárolójában. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki, akkor a Application Gateway ekkor megvizsgálhatja, hogy a kiállító HITELESÍTÉSSZOLGÁLTATÓ tanúsítványát megbízható HITELESÍTÉSSZOLGÁLTATÓ adta-e ki, és hogy amíg nem található megbízható HITELESÍTÉSSZOLGÁLTATÓ (amelyen a megbízható, biztonságos kapcsolatok létrejönnek), vagy nem található megbízható HITELESÍTÉSSZOLGÁLTATÓ (ekkor a Application Gateway a háttér nem megfelelőként lesz megjelölve). Ezért javasoljuk, hogy a háttér-kiszolgáló tanúsítványa tartalmazza a legfelső szintű és a közbenső hitelesítésszolgáltatót is.

- Ha a tanúsítvány önaláírt vagy ismeretlen közvetítők által van aláírva, akkor a végpontok közötti TLS engedélyezése a v2 SKU-ban megbízható főtanúsítványt kell definiálni. Application Gateway csak olyan háttérrel kommunikál, amelynek a kiszolgálói tanúsítvány főtanúsítványa megegyezik a készlethez társított háttérbeli http-beállításban található megbízható legfelső szintű tanúsítványok egyikével.

> [!NOTE] 
>
> Az önaláírt tanúsítványnak egy tanúsítványlánc részét kell képeznie. A v2 SKU-ban nem támogatott egyetlen önaláírt tanúsítvány, amelynek nincs lánca.

- A főtanúsítványok egyeztetése mellett a Application Gateway azt is ellenőrzi, hogy a háttérbeli http-beállításban megadott gazdagép-beállítás megegyezik-e a háttér-kiszolgáló TLS/SSL-tanúsítványa által megjelenített köznapi névvel (CN). Amikor TLS-kapcsolatot próbál létesíteni a háttérrel, Application Gateway beállítja a Kiszolgálónév jelzése (SNI) bővítményt a háttérbeli http-beállításban megadott gazdagépre.
- Ha a háttérbeli http-beállításban a gazdagép mező helyett a **kiválasztó állomásnév elemet** választja, akkor a SNI fejléc mindig a háttérrendszer-készlet teljes tartománynevére van beállítva, és a háttér-kiszolgáló TLS/SSL-tanúsítványához tartozó CN-nek meg kell egyeznie a teljes tartománynevével. Ebben a forgatókönyvben nem támogatottak a háttérbeli készlet tagjai az IP-címekkel.
- A főtanúsítvány a háttér-kiszolgálói tanúsítványok Base64 kódolású főtanúsítványa.

## <a name="next-steps"></a>További lépések

A végpontok közötti TLS-vel kapcsolatos ismeretek megismerése után nyissa meg a végpontok közötti TLS-t a [Application Gateway és a PowerShell használatával](application-gateway-end-to-end-ssl-powershell.md) , és hozzon létre egy Application Gatewayt a végpontok közötti TLS használatával.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
