---
title: Az App Service URL-címére történő átirányítás – problémamegoldás
titleSuffix: Azure Application Gateway
description: Ez a cikk arról nyújt tájékoztatást, hogyan háríthatók el az átirányítási probléma, amikor az Azure Application Gateway-t az Azure App Service szolgáltatással használják.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293542"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Alkalmazásszolgáltatással kapcsolatos problémák elhárítása az Application Gateway alkalmazásban

Ismerje meg, hogyan diagnosztizálhatja és oldhatja meg azokat a problémákat, amelyek akkor merülhetnek fel, amikor az Azure App Service háttércélként van használva az Azure Application Gateway szolgáltatással.

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan háríthatja el a következő problémákat:

> [!div class="checklist"]
> * Az alkalmazásszolgáltatás URL-címe a böngészőben jelenik meg, ha átirányítás történik.
> * Az alkalmazásszolgáltatás ARRAffinity cookie domain van beállítva, hogy az app service host neve, example.azurewebsites.net, hanem az eredeti állomás.

Amikor egy háttéralkalmazás átirányítási választ küld, érdemes lehet átirányítani az ügyfelet egy másik URL-címre, mint a háttéralkalmazás által megadott. Ezt akkor érdemes megtennie, ha egy alkalmazásszolgáltatás egy alkalmazásátjáró mögött található, és az ügyfélnek át kell haladnia a relatív elérési úthoz. Erre példa a contoso.azurewebsites.net/path1-contoso.azurewebsites.net/path2 átirányítás. 

Amikor az alkalmazásszolgáltatás átirányítási választ küld, ugyanazt az állomásnevet használja a válasz helyfejlécében, mint az alkalmazásátjárótól kapott kérelemben. Például az ügyfél a kérelmet közvetlenül contoso.azurewebsites.net/path2 ahelyett, hogy az alkalmazásátjáró contoso.com/path2. Nem szeretné megkerülni az alkalmazásátjárót.

A probléma a következő okok miatt fordulhat elő:

- Az átirányítás konfigurálva van az alkalmazásszolgáltatásban. Az átirányítás lehet olyan egyszerű, mint egy záró perjel hozzáadása a kérelemhez.
- Az Azure Active Directory-hitelesítéssel rendelkezik, ami az átirányítást okozza.

Ha alkalmazásátjáró mögött használja az alkalmazásszolgáltatásokat, az alkalmazásátjáróhoz társított tartománynév (example.com) eltér az alkalmazásszolgáltatás tartománynevétől (például example.azurewebsites.net). Az alkalmazásszolgáltatás által beállított ARRAffinity cookie tartományértéke a example.azurewebsites.net tartománynevet viseli, ami nem kívánatos. Az eredeti állomásnévnek (example.com) a cookie tartománynév értékének kell lennie.

## <a name="sample-configuration"></a>Minta konfigurációja

- HTTP-figyelő: Alapszintű vagy többhelyes
- Háttér-címkészlet: App Service
- HTTP-beállítások: **Az állomásnév kivétele a háttércím ben**
- Mintavétel: **Állomásnév választása a HTTP-beállítások** engedélyezve

## <a name="cause"></a>Ok

Az App Service egy több-bérlős szolgáltatás, így a kérelemben lévő gazdagépfejlécet használja a kérelem megfelelő végpontra való továbbításához. Az App Services alapértelmezett tartományneve,*.azurewebsites.net (mondjuk contoso.azurewebsites.net) eltér az alkalmazásátjáró tartománynevétől (például contoso.com). 

Az ügyfél eredeti kérése az alkalmazásátjáró tartományneve contoso.com, mint állomásnév. Konfigurálnia kell az alkalmazásátjárót az eredeti kérelemben lévő állomásnév módosításához az alkalmazásszolgáltatás állomásnevére, amikor a kérelmet az alkalmazásszolgáltatás háttérrendszerébe irányítja. Használja a **kapcsolót Pick Hostname from Backend Address** az alkalmazásátjáró HTTP-beállítás konfigurációját. Használja a **kapcsolót Pick Hostname a háttérrendszer HTTP-beállítások** az állapotminta konfigurációját.



![Az alkalmazásátjáró megváltoztatja az állomásnevét](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Amikor az alkalmazásszolgáltatás átirányítást végez, a felülírt állomásnevet használja, contoso.azurewebsites.net a helyfejlécben az eredeti állomásnév helyett contoso.com, hacsak másként van konfigurálva. Ellenőrizze a következő példakérelem- és válaszfejléceket.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Az előző példában figyelje meg, hogy a válaszfejléc 301-es állapotkóddal rendelkezik az átirányításhoz. A helyfejléc az alkalmazásszolgáltatás állomásneve az eredeti `www.contoso.com`állomásnév helyett.

## <a name="solution-rewrite-the-location-header"></a>Megoldás: A helyfejléc újraírása

Állítsa be az állomásnevet a helyfejlécben az alkalmazásátjáró tartománynevére. Ehhez hozzon létre egy [újraírási szabályt](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) egy feltétellel, amely kiértékeli, hogy a válasz helyfejléce tartalmaz-e azurewebsites.net. Az alkalmazásátjáró állomásnevének átírásához végre kell hajtania egy műveletet a helyfejléc újraírásához. További információt [a helyfejléc átírására](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)vonatkozó utasításokban talál.

> [!NOTE]
> A HTTP-fejléc újraírási támogatása csak az Application Gateway [Standard_v2 és WAF_v2 termékváltozatához](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) érhető el. Ha v1 Termékváltozatot használ, azt javasoljuk, hogy [telepítse át a v1-ről a v2-re.](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) Újraírási és más [speciális képességeket](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) szeretne használni, amelyek a v2 Termékváltozatban érhetők el.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatív megoldás: Egyéni tartománynév használata

Ha v1 termékváltozatot használ, nem tudja újraírni a helyfejlécet. Ez a funkció csak a v2 Termékváltozat esetén érhető el. Az átirányítási probléma megoldásához adja át ugyanazt az állomásnevet, amelyet az alkalmazásátjáró kap az alkalmazásszolgáltatásnak is, ahelyett, hogy egy állomás felülbírálását végezne.

Az alkalmazásszolgáltatás most már nem az átirányítás (ha van ilyen) ugyanazon az eredeti gazdagép fejlécén, amely az alkalmazásátjáróra mutat, és nem a sajátjára.

Egyéni tartományt kell birtokolnia, és ezt a folyamatot kell követnie:

- Regisztrálja a tartományt az alkalmazásszolgáltatás egyéni tartománylistájára. Rendelkeznie kell egy CNAME az egyéni tartományban, amely az alkalmazásszolgáltatás teljes tartománynevére mutat. További információt a [Meglévő egyéni DNS-név hozzárendelése az Azure App Service szolgáltatáshoz](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)című témakörben talál.

    ![Alkalmazásszolgáltatás egyéni tartománylistája](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Az alkalmazásszolgáltatás készen áll az `www.contoso.com`állomásnév elfogadására. Módosítsa a CNAME bejegyzést a DNS-ben úgy, hogy az mutasson `appgw.eastus.cloudapp.azure.com`vissza az alkalmazásátjáró teljes tartománynevére, például .

- Győződjön meg `www.contoso.com` arról, hogy a tartomány dns-lekérdezés esetén feloldódik az alkalmazásátjáró teljes tartománynévvel.

- Állítsa be az egyéni mintavételt, hogy letiltsa a **Pick Hostname-t a háttérrendszer HTTP-beállításai közül.** Az Azure Portalon törölje a jelet a mintavételi beállítások jelölőnégyzetből. A PowerShellben ne használja a **-PickHostNameFromBackendHttpSettings** kapcsolót a **Set-AzApplicationApplicationProbeConfconfig** parancsban. A mintavétel állomásnév mezőjébe írja be az alkalmazásszolgáltatás teljes tartománynevét, example.azurewebsites.net. Az alkalmazásátjáróból küldött mintavételi kérelmek ezt a teljes tartománynnt az állomásfejlécben hordozzák.

  > [!NOTE]
  > A következő lépésben győződjön meg arról, hogy az egyéni mintavétel nincs társítva a háttér-HTTP-beállításokat. A HTTP-beállításokon a **Háttérrendszercím kapcsolóból való kivételezési állomásnév** továbbra is engedélyezett.

- Az alkalmazásátjáró HTTP-beállításainak beállítása az **Állomásnév választása a háttércímből letiltásához.** Az Azure Portalon törölje a jelet a jelölőnégyzetből. A PowerShellben ne használja a **-PickHostNameFromBackendAddress** kapcsolót a **Set-AzApplicationGatewayBackendHttpSettings** parancsban.

- Társítsa vissza az egyéni mintavételt a háttér-HTTP-beállításokhoz, és ellenőrizze, hogy a háttérrendszer kifogástalan-e.

- Az alkalmazásátjárónak most már ugyanazt az állomásnevet `www.contoso.com`kell továbbítania az alkalmazásszolgáltatásnak. Az átirányítás ugyanazon az állomásnéven történik. Ellenőrizze a következő példakérelem- és válaszfejléceket.

Az előző lépések megvalósításához a PowerShell egy meglévő telepítő, használja a minta PowerShell-parancsfájl, amely a következő. Vegye figyelembe, hogy nem használtuk a **-PickHostname** kapcsolókat a mintavételben és a HTTP beállítások konfigurációjában.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>További lépések

Ha az előző lépések nem oldották meg a problémát, nyisson meg egy [támogatási jegyet.](https://azure.microsoft.com/support/options/)
