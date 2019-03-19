---
title: Az Azure Application Gateway teljes körű SSL engedélyezése
description: Ez a cikk a az Application Gateway teljes körű SSL-támogatásról nyújt áttekintést.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/12/2019
ms.author: victorh
ms.openlocfilehash: 16ba6b73dd0c64298f319d4b18750d753f166987
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849380"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Az Application Gatewayen engedélyezett teljes körű SSL áttekintése

Az Application Gateway támogatja az SSL-lezárást az átjárónál, mely a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz után. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. Akadnak azonban ügyfelek, akiknek a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció nem elfogadható megoldás. A titkosítatlan kommunikáció okai lehetnek biztonsági követelmények, megfelelőségi előírások, vagy az, hogy az alkalmazás csak biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az Application Gateway támogatja a teljes körű SSL-titkosítást.

Teljes körű SSL lehetővé teszi, hogy küldheti a bizalmas adatokat a háttérkiszolgálók felé, titkosított, miközben továbbra is kihasználhatja a 7. rétegbeli terheléselosztási funkciókat előnyeit kiaknázhatja az Alkalmazásátjáró biztosít. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Ha teljes körű SSL-kommunikációs üzemmóddal van konfigurálva, az Application Gateway leállítja az SSL-munkamenetet az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az Application Gateway ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóval, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. Teljes körű SSL akkor engedélyezett, a protokoll beállítása **BackendHTTPSetting** , HTTPS, amely majd érvényben van a háttérkészlethez. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

Előtér- és háttérportokat forgalmat az SSL-szabályzat vonatkozik. Az előtérben az Application Gateway a kiszolgálóként működik, és érvénybe lépteti a szabályzatot. A háttérben az Application Gateway az ügyfél funkcionál, és a protokoll/titkosítási adatokat küld a beállítást az SSL-kézfogás során.

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
