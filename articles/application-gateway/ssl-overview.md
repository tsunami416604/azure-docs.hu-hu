---
title: Az Azure Application Gateway teljes körű SSL engedélyezése
description: Ez a cikk a az Application Gateway teljes körű SSL-támogatásról nyújt áttekintést.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: amsriva
ms.openlocfilehash: 4575bed18697a5661d58dc350c24a9497f7c46ff
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578813"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Az Application Gatewayen engedélyezett teljes körű SSL áttekintése

Az Application Gateway támogatja az SSL-lezárást az átjárónál, mely a forgalom rendszerint titkosítatlanul áramlik a háttérkiszolgálókhoz után. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. Akadnak azonban ügyfelek, akiknek a háttérkiszolgálók felé irányuló titkosítatlan kommunikáció nem elfogadható megoldás. A titkosítatlan kommunikáció okai lehetnek biztonsági követelmények, megfelelőségi előírások, vagy az, hogy az alkalmazás csak biztonságos kapcsolatot fogad el. Az ilyen alkalmazásokhoz az Application Gateway támogatja a teljes körű SSL-titkosítást.

A teljes körű, végponttól végpontig érvényes SSL-kapcsolat segítségével titkosítva küldheti a bizalmas adatokat a háttérkiszolgálók felé, miközben kiaknázhatja az Application Gateway által biztosított 7. rétegbeli terheléselosztási funkciókat. Ilyen például a cookie-alapú munkamenet-affinitás, az URL-cím-alapú útválasztás, a helyalapú útválasztás támogatása vagy az XForwarded-* fejlécek beszúrása.

Ha teljes körű SSL-kommunikációs üzemmóddal van konfigurálva, az Application Gateway leállítja az SSL-munkamenetet az átjárónál, és visszafejti a felhasználói forgalmat. Ezután alkalmazza a konfigurált szabályokat, hogy kiválassza a megfelelő háttérkészletpéldányt, ahová irányítható a forgalom. Az Application Gateway ekkor új SSL-kapcsolatot kezdeményez a háttérkiszolgálóval, és mielőtt a kérést továbbítaná, a háttérkiszolgáló nyilvánoskulcs-tanúsítványával újratitkosítja az adatokat. Teljes körű SSL akkor engedélyezett, protokoll beállítása **BackendHTTPSetting** , HTTPS, amely majd érvényben van a háttérkészlethez. A biztonságos kommunikációhoz tanúsítványt kell konfigurálni a teljes körű SSL-lel engedélyezett háttérkészlet minden egyes háttérkiszolgálójához.

![Teljes körű ssl eset][1]

Jelen példa esetén a TLS1.2-t használó kérések a Pool1-ben lévő háttérkiszolgálókra vannak átirányítva teljes körű SSL segítségével.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Teljes körű SSL és a tanúsítványok engedélyezése

Az alkalmazásátjáró csak ismert háttérpéldányokkal kommunikál, amelyek tanúsítványa az alkalmazásátjáró engedélyezési listájában szerepel. A tanúsítványok engedélyezéséhez fel kell tölteni a háttérkiszolgáló-tanúsítvány nyilvános kulcsát az alkalmazásátjáróra (főtanúsítványt nem). Ezután kizárólag az ismert és engedélyezett háttérkiszolgálókkal való kapcsolódás engedélyezett. A fennmaradó háttérkiszolgálók átjáróhibához vezetnek. Az önaláírt tanúsítványok csupán tesztelési célokat szolgálnak, és nem ajánlottak éles számítási feladatokra. Az ilyen tanúsítványokat engedélyeztetni kell az application gateway használhatók legyenek az előző lépésben ismertetett módon.

> [!NOTE]
> Hitelesítési tanúsítvány beállítása, nem szükséges, például az Azure Web Apps megbízható Azure-szolgáltatásokhoz.

## <a name="next-steps"></a>További lépések

Miután megismerkedett a teljes körű SSL-lel, tekintse át az [Alkalmazásátjáró konfigurálása az SSL leállításával az Azure Portalon](create-ssl-portal.md) témakört, amelyből megtudhatja, hogyan hozhat létre átjáróalkalmazást teljes körű SSL segítségével.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
