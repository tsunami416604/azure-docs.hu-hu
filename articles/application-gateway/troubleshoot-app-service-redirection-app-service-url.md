---
title: Az App Service – az App Service URL-átirányítás használatával az Azure Application Gateway hibáinak elhárítása
description: Ez a cikk információt nyújt az Azure Application Gateway használata az Azure App Service átirányítás hiba elhárításához
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 9874ff7fde049c4dba4efb77ff541c80e462671a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808668"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Az App Service – az App Service URL-átirányítás használatával az Application Gateway hibaelhárítása

 Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a problémákat is az Application Gateway, ahol az App Service URL-cím első elérhetővé.

## <a name="overview"></a>Áttekintés

Amikor konfigurál egy App Service az Application Gateway háttérkészlet rendelkező nyilvános, és ha az alkalmazás kódjában konfigurált átirányítás, előfordulhat, hogy látható az Application Gateway használatakor, amelyeket a böngésző közvetlenül az alkalmazás a rendszer átirányítja Szolgáltatás URL-CÍMÉT.

A probléma a következő fő okok miatt fordulhat elő:

- Az App Service-ben konfigurált átirányítás rendelkezik. Lehet, hogy átirányítás szúrni egy záró perjellel a kérelemre.
- Azure AD-hitelesítés, amely azt eredményezi, az átirányítás rendelkezik.
- Engedélyezte, hogy az Application Gateway a HTTP-beállítások "Válasszon gazdagépnév a háttér-cím" kapcsolóhoz.
- Nem kell az egyéni tartomány az App Service regisztrálva.

## <a name="sample-configuration"></a>Példa konfigurációja

- HTTP-figyelő: Alap- vagy többhelyes
- Háttér-címkészlet: App Service
- HTTP-beállítások: "Választhatok állomásnév címet" engedélyezve
- Mintavétel: "Választhatok állomásnév HTTP-beállítások" engedélyezve

## <a name="cause"></a>Ok

Egy App Service-ben csak elérhetők, a konfigurált gazdanév található az egyéni tartomány beállításokat, az alapértelmezett, "example.azurewebsites.net", és hogy szeretné-e hozzáférni az App Service vagy az App Service-ben nem regisztrált állomásnévvel rendelkező Application Gateway használatával Az Application Gateway teljes Tartományneve, kell bírálni az állomásnév az eredeti kérés az App Service gazdagépnevére.

Ennek az Application Gatewayen érdekében, hogy használata a kapcsoló "Válasszon állomásnév a címet" a HTTP-beállítások és a mintavétel, "Válasszon állomásnév a HTTP-háttérbeállítások" használjuk a mintavételi konfigurációban.

![appservice-1](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-1.png)

Miatt, ez az alkalmazás szolgáltatásnak nincs a mappaátirányítás, használja az állomásnevet a Location fejlécet "example.azurewebsites.net" helyett az eredeti hostname, kivéve, ha másként van konfigurálva. A példa-kérelmek és válaszfejlécek alább megtekinthető.

Az Application Gateway kérelemfejlécek:

Kérelem URL-címe: http://www.contoso.com/path

Kérelmi metódus: GET

Gazdagép: www.contoso.com

Válaszfejlécek:

Állapotkód: 301 véglegesen áthelyezése

Hely: http://example.azurewebsites.net/path/

Kiszolgáló: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET

A fenti példában is láthatja, hogy a válasz fejléce átirányítási 301 állapotkódot pedig a location fejlécet az App Service-állomásnév helyett az eredeti hostname "www.contoso.com".

## <a name="solution"></a>Megoldás

A probléma nem rendelkezik az alkalmazás oldalán, azonban egy átirányítási által feloldható, ha ez nem lehetséges, az App Service, valamint helyett ezzel a gazdagép-felülbírálás, amely fogad Alkalmazásátjáró azonos állomás fejlécével kell átadott.

Nézzük meg, miután az App Service elvégzi az átirányítás (ha vannak) az azonos eredeti állomásfejlécet az Application Gateway mutat, amely a, és nem a saját.

Ennek érdekében kell egy egyéni tartománnyal, és kövesse az alábbi folyamatot.

- Regisztrálja a tartomány az App Service egyéni tartománnyal listájához. Ehhez egy olyan CNAME REKORDOT kell rendelkeznie az App Service teljes Tartományneve mutató egyéni tartomány. További információkért lásd: [meglévő egyéni DNS-név leképezése az Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).![ az App Service-2](.\media\troubleshoot-app-service-redirection-app-service-url\appservice-2.png)

- Miután ez megtörtént, fogadja el a "www.contoso.com" állomásnév készen áll az App Service. Módosítsa a CNAME bejegyzést, hogy az Application Gateway FQDN vissza a DNS-ben. Például "appgw.eastus.cloudapp.azure.com."

- Győződjön meg arról, hogy a tartományához "www.contoso.com" az Application Gateway FQDN feloldása, amikor ezt teszi, hogy a DNS-lekérdezés.

- Állítsa be az egyéni mintavétel "Válasszon állomásnév a HTTP-háttérbeállítások" letiltása. Ez a mintavételi beállítások jelölőnégyzet jelölésének törlése végezhető a portálról, és a PowerShell - PickHostNameFromBackendHttpSettings nem használatával válthat.

  > [!NOTE]
  > Ehhez a lépéshez során győződjön meg arról, hogy az egyéni mintavétel a háttérbeli HTTP-beállítások nem tartozik.

- Állítsa be az Application Gateway HTTP-beállítások "Válasszon állomásnév a címet" letiltása. Ez a jelölőnégyzet jelölésének törlése végezhető a portálról, és a PowerShell - PickHostNameFromBackendAddress nem használatával válthat.

- Társítsa az egyéni mintavétel a háttérbeli HTTP-beállításokra, és ellenőrizze a háttérrendszer állapota, ha kifogástalan.

- Miután ez megtörtént, az Application Gateway az azonos állomásnévvel "www.contoso.com" most továbbítani kell az App Service-ben, és az átirányítás úgy történik, az azonos állomásnévvel a. A példa-kérelmek és válaszfejlécek alább megtekinthető.

  Az Application Gateway kérelemfejlécek:

  Kérelem URL-címe: http://www.contoso.com/path

  Kérelmi metódus: GET

  Gazdagép: [www.contoso.com](http://www.contoso.com)

  Válaszfejlécek:

  Állapotkód: 301 véglegesen áthelyezése

  Hely: http://www.contoso.com/path/

  Kiszolgáló: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET

## <a name="next-steps"></a>További lépések

Ha a fenti lépések nem a probléma megoldásához nyissa meg a [támogatási jegyet](https://azure.microsoft.com/support/options/).