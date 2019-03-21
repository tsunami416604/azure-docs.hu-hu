---
title: Az Azure Application Gateway teljes körű SSL engedélyezése
description: Ez a cikk a az Application Gateway teljes körű SSL-támogatásról nyújt áttekintést.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 92799019d13de71d911767d8e400598513587667
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258395"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>SSL-lezárások és teljes körű SSL-Titkosítást az Application Gateway – áttekintés

Secure Sockets Layer (SSL) a szokásos biztonsági technológia lehessen létesíteni egy titkosított hivatkozásra a webalkalmazás-kiszolgáló és a böngésző között. Ez a hivatkozás biztosítja, hogy az összes adat a webkiszolgáló és a böngészők között átadott továbbra is a privát, titkosított csatornákon történik. Az Application gateway támogatja mindkét SSL-lezárást az átjáróhoz, valamint a végpontok közötti SSL-titkosítást.

## <a name="ssl-termination"></a>SSL leállítása

Az Application Gateway támogatja az SSL-lezárást az átjárónál, mely a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz után. Nincsenek számos előnnyel jár SSL-lezárást az application gatewayben állapotát:

- **Továbbfejlesztett teljesítmény** – a legnagyobb teljesítményt SSL visszafejtési esetén a kezdeti kézfogás. A teljesítmény javítása érdekében a kiszolgáló ezzel a visszafejtési SSL munkamenet-azonosítók gyorsítótárazza, és TLS-munkamenet jegyeket. Ha ez történik, az application gatewayben, az ugyanazon ügyféltől érkező kérések a gyorsítótárazott értékeket is használhat. Ha kész van a háttérkiszolgálókon lévő minden alkalommal, amikor az ügyfélkérelmek egy másik kiszolgálóra, nyissa meg az ügyfél az re‑authenticate fel. A TLS-jegyek használata segíthet a probléma megoldásához, de nem támogatottak az összes ügyfél és a nehéz lehet, konfigurálhatja és kezelheti.
- **A háttérkiszolgálók kihasználtsági jobb** – SSL/TLS-feldolgozási nagyon CPU-igényes, és egyre több nagy számításigényű, ahogy kulcsméretek növelése. Ezt a munkát eltávolítását a háttérkiszolgálók lehetővé teszi, hogy Mik ezek leghatékonyabb összpontosítani: tartalom jusson el.
- **Intelligens útválasztás** – visszafejti a forgalmat, az application gateway férhet hozzá a kérelem tartalma, például fejlécek, URI és így tovább, és a kérelmeket a tárolt adatok.
- **Tanúsítványkezelés az** – csak vásárolható meg, és az application gateway és a nem az összes háttér-kiszolgálókon telepítve kell tanúsítványokat. Ezzel takaríthat meg időt és pénzt takarít.

SSL-tanúsítvány konfigurálása SSL-lezárást, a figyelőt, hogy engedélyezze az application gateway, hogy a szimmetrikus kulcs az SSL protokoll-meghatározása megfelelően hozzáadandó szükséges. A szimmetrikus kulcs titkosításához és visszafejtéséhez az átjáró küldött forgalmat majd szolgál. Az SSL-tanúsítványnak kell lennie a személyes információcsere (PFX) formátumban. Ez a fájlformátum exportálja a titkos kulcsot, a titkosítási és visszafejtési forgalmat az application gateway által igényelt teszi lehetővé.

> [!NOTE] 
>
> Az Application gateway nem biztosít minden képességét, hogy hozzon létre egy új tanúsítványt, vagy egy hitelesítésszolgáltató-tanúsítvány kérés küldése.

Az SSL-kapcsolathoz működik győződjön meg arról, hogy az SSL-tanúsítvány megfelel-e a következő feltételek kell:

- A "Érvényesség kezdete" és "a" dátum közötti a tanúsítványon belül, amely az aktuális dátum és idő van.
- Hogy a tanúsítvány "Common Name" (CN) megegyezik a kérelemben szereplő állomás fejlécével. Például, ha az ügyfelet, hogy így kérést `https://www.contoso.com/`, akkor a CN kell `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>SSL-lezárást támogatott tanúsítványok

Az Application gateway támogatja a következő típusú tanúsítványt:

- (Hitelesítésszolgáltató) hitelesítésszolgáltató tanúsítványa: A Hitelesítésszolgáltatói tanúsítvány, amely hitelesítésszolgáltató (CA) által kiadott digitális tanúsítványt
- Bővített (bővített ellenőrzés)-tanúsítvány: Egy Bővített tanúsítvány, amely az iparági szabványos tanúsítvány irányelveket. Ezzel kíséretében zöld színre vált a böngésző kereső sávon és közzé, valamint a vállalat neve.
- A helyettesítő tanúsítvány: Ez a tanúsítvány támogatja altartományok alapján tetszőleges számú *. site.com, ahol az altartomány lecserélné a *. Azt azonban nem, támogatja a site.com, abban az esetben, ha a felhasználó a "www" vezető beírása nélkül éri el a webhelyet, a helyettesítő tanúsítvány nem vonatkozik, amelyek így.
- Önaláírt tanúsítványok: Ügyfél böngészők nem bízik meg ezeket a tanúsítványokat, és figyelmezteti a felhasználót, hogy a virtuális service-tanúsítvány nem szerepel egy megbízhatósági láncában. Önaláírt tanúsítványok jók tesztelési vagy környezetekben, ahol a rendszergazdák az ügyfelek vezérlőelemet, és biztonságosan elkerülheti a böngésző biztonsági riasztásokat. Éles számítási feladatok soha ne használjon önaláírt tanúsítványokat.

További információkért lásd: [az application gateway konfigurálása SSL-lezárást](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

## <a name="end-to-end-ssl-encryption"></a>A teljes körű SSL-titkosítás

Egyes ügyfeleink előfordulhat, hogy nem igényel a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció. Ennek okai lehetnek biztonsági és megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az Application Gateway támogatja a teljes körű SSL-titkosítást.

Teljes körű SSL lehetővé teszi, hogy küldheti a bizalmas adatokat a háttérkiszolgálók felé, titkosított, miközben továbbra is kihasználhatja a 7. rétegbeli terheléselosztási funkciókat előnyeit kiaknázhatja az Alkalmazásátjáró biztosít. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Ha teljes körű SSL-kommunikációs üzemmóddal van konfigurálva, az Application Gateway leállítja az SSL-munkamenetet az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az Application Gateway ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóval, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. A webkiszolgáló esetleges válasza ugyanilyen módon jut el a végfelhasználóhoz. Teljes körű SSL akkor engedélyezett, protokoll beállítása [háttérbeli HTTP-beállítás](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) , HTTPS, amely majd érvényben van a háttérkészlethez.

Előtér- és háttérportokat forgalmat az SSL-szabályzat vonatkozik. Az előtérben az Application Gateway a kiszolgálóként működik, és érvénybe lépteti a szabályzatot. A háttérben az Application Gateway az ügyfél funkcionál, és a protokoll/titkosítási adatokat küld a beállítást az SSL-kézfogás során.

Az Application gateway csak kommunikál mindkét alkalmazásátjárónál háttérrendszer logikailemez tanúsítványa az Alkalmazásátjáró, vagy amelyek tanúsítványa alá a jól ismert hitelesítésszolgáltató hatóságok, ahol a tanúsítvány CN-neve egyezik-e az állomásnév a http háttér beállításokat. Ezek közé tartozik például az Azure App service web apps szolgáltatásban és az Azure API Management a megbízható Azure-szolgáltatásokat.

Ha a háttérkiszolgáló-készlet azon tagjait, a tanúsítványok nem írta alá a jól ismert hitelesítésszolgáltató hatóságok, majd a háttérkészletben teljes körű SSL engedélyezve van az egyes példányok kell konfigurálni a biztonságos kommunikációhoz tanúsítványt. A tanúsítvány hozzáadása biztosítja, hogy az application gateway csak kommunikál a háttér-ismert példányok. Ez további védi a végpontok közötti kommunikációt.

> [!NOTE] 
>
> Hitelesítési tanúsítvány beállítása, nem szükséges, például az Azure App service web apps szolgáltatásban és az Azure API Management megbízható Azure-szolgáltatásokhoz.

> [!NOTE] 
>
> A tanúsítvány hozzá **háttérbeli HTTP-beállítás** hitelesítéséhez a háttérbeli kiszolgálók lehet ugyanaz, mint a tanúsítvány hozzá a **figyelő** az SSL-lezárások application gatewayben vagy más-más fokozott biztonságot.

![Teljes körű ssl eset][1]

Jelen példa esetén a TLS1.2-t használó kérések a Pool1-ben lévő háttérkiszolgálókra vannak átirányítva teljes körű SSL segítségével.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Teljes körű SSL és a tanúsítványok engedélyezése

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványokat engedélyeztetni kell az application gateway használhatók legyenek az előző lépésben ismertetett módon.

> [!NOTE]
> Hitelesítési tanúsítvány beállítása, nem szükséges az Azure App Service-ben például a megbízható Azure-szolgáltatásokhoz.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Teljes körű SSL a v2 termékváltozatú

A hitelesítési tanúsítványokat elavult, és az Application Gateway v2 szintű Termékváltozatot a megbízható legfelső szintű tanúsítványok cserélve. Azok hasonlóan működnek a hitelesítési tanúsítványokat néhány kulcsfontosságú eltéréssel:

- Jól ismert, amelynek CN megfelel a HTTP-háttérbeállítások található gazdanevet hitelesítésszolgáltató hatóságok által aláírt tanúsítványokat nem igényel teljes körű SSL működéséhez minden további lépést. 

   Például, ha a háttérrendszer tanúsítványokat a jól ismert hitelesítésszolgáltató által kiállított, és van egy contoso.com CN, és a háttérbeli http-beállítás a gazdagép mező értéke is a contoso.com, majd nincsenek további lépésekre szükség. A háttérbeli http protokoll beállítása HTTPS-re állíthatja, és az egészségügyi mintavétel és adatok elérési útvonalat SSL engedélyezve lenne. Az Azure App Service-ben vagy más Azure-alapú webes szolgáltatások háttérrendszerrel használja, ha ezek implicit módon megbízhatónak minősülnek, valamint, és nincs további lépések szükségesek, teljes körű SSL-hez.
- Ha a tanúsítvány önaláírt, vagy ismeretlen közvetítők írja alá, majd a megbízható főtanúsítvány v2 szintű Termékváltozatot teljes körű SSL engedélyezése definiálni kell. Az Application Gateway csak háttérrendszerek, amelynek kiszolgálói tanúsítvány legfelső szintű tanúsítvány megfelel a háttérbeli http-beállítás a készlethez társított megbízható legfelső szintű tanúsítványok listáját egyikének kommunikál.
- Mellett a legfelső szintű tanúsítvány egyezik az Application Gateway azt is ellenőrzi, ha a gazdagép-beállítást megadott a háttérbeli http-beállítás megegyezik a háttérkiszolgáló SSL-tanúsítvány által bemutatott köznapi név (CN). Próbálja a háttérrendszer SSL-kapcsolatot létesíteni, amikor az Application Gateway a kiszolgálónév jelzése (SNI) bővítmény a gazdagép, a háttérbeli http-beállításban megadott állítja be.
- Ha **válasszon címet gazdanévre** helyett a gazdagép mezőt a háttérbeli http-beállítás van kiválasztva, majd az SNI-fejléc értéke mindig a háttérkészlethez teljes tartománynév és a CN a háttérkiszolgálón SSL tanúsítványt meg kell egyeznie a teljes Tartományneve. Háttérkészlet-tagokra az IP-címek nem támogatottak ebben a forgatókönyvben.
- A legfelső szintű tanúsítvány base64 kódolású származó főtanúsítványt a háttérkiszolgáló-tanúsítvány.

## <a name="next-steps"></a>További lépések

Miután megismerkedett a teljes körű SSL, látogasson el [végpontok közötti SSL konfigurálása az Application Gateway a PowerShell használatával](application-gateway-end-to-end-ssl-powershell.md) hozhat létre átjáróalkalmazást teljes körű SSL segítségével.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
