<properties
   pageTitle="Az SSL-szabályzat és a teljes körű SSL engedélyezése Application Gateway-en | Microsoft Azure"
   description="Ez az oldal az Application Gateway teljes körű SSL-támogatásról nyújt áttekintést."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="amsriva"/>


# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Az SSL-szabályzat és a teljes körű SSL engedélyezése Application Gateway-en

## <a name="overview"></a>Áttekintés

Az Application Gateway támogatja az SSL-lezárást az átjárónál, ami után a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz. Így a webkiszolgálók megszabadulhatnak a magas titkosítási/visszafejtési üzemeltetési költségektől. Akadnak azonban ügyfelek, akiknek a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció nem elfogadható megoldás. Ennek okai lehetnek biztonsági/megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az alkalmazásátjáró immár támogatja a teljes körű SSL-titkosítást.

A teljes körű SSL segítségével titkosítva küldheti a bizalmas adatokat a háttérkiszolgálók felé, miközben kiaknázhatja az alkalmazásátjáró által biztosított 7. rétegbeli terheléselosztási funkciókat, például a cookie-affinitást, az URL-cím alapú útválasztást, a webhelyalapú útválasztás támogatását vagy az XForwarded-* fejlécek beszúrását.

Amikor végpontok közötti SSL-kommunikációs móddal van konfigurálva, az alkalmazásátjáró leállítja a felhasználó SSL-munkamenetét az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az alkalmazásátjáró ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóhoz, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. A teljes körű SSL akkor engedélyezett, ha a BackendHTTPSetting tulajdonságban a protokoll beállítása a Https értékre van állítva, amit a rendszer ilyenkor a háttérkészletre is alkalmaz. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Ebben a példában a rendszer teljes körű SSL-lel a https://contoso.com felé irányuló kérelmeket HTTP-n keresztül a ContosoServerPoolhoz, a https://fabrikam.com felé irányuló kérelmeket pedig HTTPS-en keresztül a FabrikamServerPoolhoz irányítja.

## <a name="end-to-end-ssl-and-white-listing-of-certificates"></a>Teljes körű SSL és a tanúsítványok engedélyezése

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjárónál engedélyezett. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra. Kizárólag az ismert és engedélyezett háttérkiszolgálók felé irányuló kapcsolatok engedélyezettek, a többi átjáróhibát eredményez. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványokat használat előtt szintúgy a fentiekben leírt módon engedélyeztetni kell az alkalmazásátjáróval.

## <a name="application-gateway-ssl-policy"></a>Az Application Gateway SSL-szabályzatai

Az Application Gateway támogatja a felhasználó által konfigurálható SSL-egyeztetési szabályzatokat, amelyekkel a felhasználók szorosabb felügyeletet gyakorolhatnak a SSL-kapcsolatok felett az alkalmazásátjárón.

1. Az SSL 2.0 és 3.0 le van tiltva minden alkalmazásátjáró számára, és egyáltalán nem konfigurálható.
2. Az SSL-szabályzat definíciója lehetőséget nyújt, hogy az alábbi három protokoll bármelyikét letiltsa: TLSv1_0, TLSv1_1, TLSv1_2.
3. Ha nincs SSL-szabályzat megadva, mindhárom (TLSv1_0, TLSv1_1, TLSv1_2) engedélyezett.

## <a name="next-steps"></a>Következő lépések

Miután megismerte a teljes körű SSL-t és az SSL-szabályzatokat, látogasson el az [Enable end to end SSL on application gateway](application-gateway-end-to-end-ssl-powershell.md) (Teljes körű SSL engedélyezése az alkalmazásátjárón) weboldalra, ahonnan megtudhatja, hogyan hozhat létre olyan átjáróalkalmazást, amely képes titkosítottan küldeni a forgalmat a háttérrendszerbe.



<!--HONumber=Oct16_HO3-->


