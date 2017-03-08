---
title: "Az SSL-szabályzat és a teljes körű SSL engedélyezése Application Gateway-en | Microsoft Docs"
description: "Ez az oldal az Application Gateway teljes körű SSL-támogatásról nyújt áttekintést."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 5009b13cec57e6974f71610c84fdaad837085df0
ms.openlocfilehash: 5f81d8146f8000e73a2eb578ff2371a62c8875e9
ms.lasthandoff: 02/28/2017


---
# <a name="overview-of-end-to-end-ssl-and-ssl-policy-on-application-gateway"></a>Az SSL-szabályzat és a teljes körű SSL áttekintése az Application Gatewayen

Az Application Gateway támogatja az SSL-lezárást az átjárónál, ami után a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási/visszafejtési üzemeltetési költségektől. Akadnak azonban ügyfelek, akiknek a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció nem elfogadható megoldás. Ennek a titkosítatlan kommunikációnak okai lehetnek biztonsági/megfelelőségi előírások, vagy az, hogy az alkalmazás kizárólag biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az alkalmazásátjáró immár támogatja a teljes körű SSL-titkosítást.

## <a name="overview"></a>Áttekintés

A teljes körű, végponttól végpontig érvényes SSL-kapcsolat segítségével titkosítva küldheti a bizalmas adatokat a háttérkiszolgálók felé, miközben kiaknázhatja az Application Gateway által biztosított 7. rétegbeli terheléselosztási funkciókat. Ilyen például a cookie-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Amikor végpontok közötti SSL-kommunikációs móddal van konfigurálva, az alkalmazásátjáró leállítja a felhasználó SSL-munkamenetét az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az alkalmazásátjáró ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóhoz, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. A teljes körű SSL akkor engedélyezett, ha a BackendHTTPSetting tulajdonságban a protokoll beállítása a Https értékre van állítva, amit a rendszer ilyenkor a háttérkészletre is alkalmaz. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

![Teljes körű ssl eset][1]

Jelen példa esetén a TLS1.2-t használó kérések a Pool1-ben lévő háttérkiszolgálókra vannak átirányítva teljes körű SSL segítségével.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Teljes körű SSL és a tanúsítványok engedélyezése

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványokat használat előtt a fenti lépések szerint szintén engedélyeztetni kell az alkalmazásátjáróval.

## <a name="application-gateway-ssl-policy"></a>Az Application Gateway SSL-szabályzatai

Az Application Gateway támogatja a felhasználó által konfigurálható SSL-egyeztetési szabályzatokat, amelyekkel a felhasználók szorosabb ellenőrzést gyakorolhatnak a SSL-kapcsolatok felett az alkalmazásátjárón.

1. Az SSL 2.0 és 3.0 alapértelmezés szerint minden alkalmazásátjárón le van tiltva. és egyáltalán nem konfigurálható.
2. Az SSL-szabályzat definíciója lehetőséget nyújt a következő három protokoll bármelyikének letiltására: **TLSv1\_0**, **TLSv1\_1**, **TLSv1\_2**.
3. Ha nincs SSL-szabályzat megadva, mindhárom protokoll (TLSv1\_0, TLSv1\_1, TLSv1_2) engedélyezett.

## <a name="next-steps"></a>Következő lépések

Miután megismerkedett a teljes körű SSL-lel és az SSL-szabályzatokkal, látogasson el az [Enable end to end SSL on application gateway](application-gateway-end-to-end-ssl-powershell.md) (Teljes körű SSL engedélyezése az alkalmazásátjárón) weboldalra, ahonnan megtudhatja, hogyan hozhat létre átjáróalkalmazást teljes körű SSL segítségével.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

