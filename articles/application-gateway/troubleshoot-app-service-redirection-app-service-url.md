---
title: Az Azure Application Gateway hibaelhárítása App Service – átirányítás App Service URL-címére
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani az átirányítási problémát az Azure Application Gateway Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347887"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>A Application Gateway App Service problémáinak elhárítása

Megtudhatja, hogyan diagnosztizálhatja és megoldhatja a problémákat, amikor az App Server háttérbeli célként van használatban Application Gateway

## <a name="overview"></a>Áttekintés

Ebből a cikkből megtudhatja, hogyan végezheti el az alábbi problémák megoldását:

> [!div class="checklist"]
> * App Service URL-címe, amely elérhető a böngészőben, ha van átirányítás
> * App Service ARRAffinity cookie-tartománya App Service hostname (example.azurewebsites.net) értékre van beállítva az eredeti gazdagép helyett

Ha egy háttérbeli alkalmazás átirányítási választ küld, előfordulhat, hogy át szeretné irányítani az ügyfelet egy másik URL-címre, mint amelyet a háttérbeli alkalmazás adott meg. Tegyük fel például, hogy ezt akkor érdemes megtenni, ha egy app Service-t egy Application Gateway mögött futtat, és azt igényli, hogy az ügyfél átirányítsa a relatív elérési útra. (Például egy átirányítás a contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2-be.) Amikor az App Service átirányítási választ küld, ugyanazt a gazdagépet használja a válaszának Location fejlécében, mint az Application gatewaytől kapott kérelemben szereplőnek. Így az ügyfél közvetlenül a contoso.azurewebsites.net/path2 küldi a kérést az Application Gateway (contoso.com/path2) helyett. Az Application Gateway megkerülése nem kívánatos.

A probléma a következő fő okok miatt fordulhat elő:

- Az átirányítás konfigurálva van a App Serviceon. Az átirányítás olyan egyszerű lehet, mint egy záró perjel hozzáadása a kéréshez.
- Rendelkezik Azure AD-hitelesítéssel, amely az átirányítás okát okozza.

Továbbá, ha Application Gateway mögötti App Services használ, a Application Gatewayhoz (example.com) társított tartománynév eltér a App Service tartománynevétől (azaz example.azurewebsites.net), mert ez a következő lesz: a App Service által beállított ARRAffinity-cookie tartományi értéke a "example.azurewebsites.net" tartománynevet fogja látni, ami nem kívánatos. Az eredeti hostname (example.com) a tartománynév értékének kell lennie a cookie-ban.

## <a name="sample-configuration"></a>Minta konfigurációja

- HTTP-figyelő: Alapszintű vagy többhelyes
- Háttérbeli címkészlet: App Service
- HTTP-beállítások: "Az állomásnév kiválasztása a háttér-címről" beállítás engedélyezve
- Mintavételi "Az állomásnév kiválasztása a HTTP-beállításokból" engedélyezve

## <a name="cause"></a>Ok

Mivel a App Service egy több-bérlős szolgáltatás, a kérelemben szereplő állomásfejléc használatával irányítja át a kérést a megfelelő végpontra. Az App Services, a *. azurewebsites.net (Say contoso.azurewebsites.net) alapértelmezett tartományneve azonban eltér az Application Gateway tartománynevétől (azaz contoso.com). Mivel az ügyfél eredeti kérelme az Application Gateway tartománynevét (contoso.com) adja meg állomásnévként, konfigurálnia kell az Application Gateway-t, hogy az eredeti kérelemben szereplő állomásnevet módosítsa az App Services állomásnévre, amikor a kérést átirányítja a következőre: az App Service-háttérrendszer.  Ezt úgy érheti el, ha a "válassza az állomásnév a háttérbeli címről" lehetőséget a Application Gateway HTTP-beállítási konfigurációjában, valamint az "állomásnév kiválasztása a háttérbeli HTTP-beállításokból" kapcsolót az állapot mintavételi konfigurációjában.



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Ennek következtében, amikor a App Service átirányítást végez, a "contoso.azurewebsites.net" felülbírálható állomásnévt használja a Location fejlécben az eredeti "contoso.com" állomásnév helyett, kivéve, ha másként van konfigurálva. Az alábbi példákban megtekintheti a kérelmek és válaszok fejléceit.
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
A fenti példában láthatja, hogy a válasz fejlécének 301-as állapotkódot kell lennie az átirányításhoz, és a Location fejlécben a App Service állomásneve szerepel az eredeti "www.contoso.com" állomásnév helyett.

## <a name="solution-rewrite-the-location-header"></a>Megoldás A Location fejléc újraírása

Be kell állítania a hostname nevet a Location (hely) fejlécben az Application Gateway tartománynevére. Ehhez hozzon létre egy Újraírási [szabályt](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) egy feltétellel, amely kiértékeli, hogy a válaszban található azurewebsites.net tartalmaz-e, és végrehajt egy műveletet, amellyel újraírhatja a Location fejlécet, hogy az Application Gateway állomásneve legyen.  [A hely fejlécének](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)újraírására vonatkozó utasításokat itt találhatja meg.

> [!NOTE]
> A HTTP-fejléc újraírásának támogatása csak a Application Gateway [Standard_V2 és WAF_V2 SKU-](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) ban érhető el. Ha v1 SKU-t használ, javasoljuk, hogy [telepítse át a v1-ről v2-re](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) , hogy a v2 SKU-ban elérhető újraírást és egyéb [speciális funkciókat](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) is használni tudja.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Alternatív megoldás: Az App Service egyéni tartományának használata az alapértelmezett tartománynév helyett

Ha v1 SKU-t használ, nem tudja újraírni a Location fejlécet, mivel ez a funkció csak a v2 SKU-hoz érhető el. Ezért az átirányítási probléma megoldásához ugyanazt az állomásnevet kell megadnia, amelyet Application Gateway fogad a App Servicehoz, és nem a gazdagép felülbírálását.

Ha ezt elvégezte, a App Service végrehajtja az átirányítást (ha van ilyen) ugyanarra az eredeti állomásfejléc-fejlécre, amely Application Gatewayra és nem a sajátra mutat.

Ennek eléréséhez rendelkeznie kell egy egyéni tartománnyal, és követnie kell az alább leírt eljárást.

- Regisztrálja a tartományt a App Service egyéni tartomány listájában. Ebben az esetben a App Service teljes tartománynevére mutató CNAME-t kell megadnia az egyéni tartományban. További információ: [meglévő egyéni DNS-név leképezése Azure app Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice – 2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Ha elkészült, a App Service készen áll a "www.contoso.com" állomásnév elfogadására. Most módosítsa a CNAME-bejegyzést a DNS-ben úgy, hogy visszamutasson Application Gateway teljes tartománynevére. Például: "appgw.eastus.cloudapp.azure.com".

- Győződjön meg arról, hogy a "www.contoso.com" tartomány a DNS-lekérdezés végrehajtásakor feloldja a Application Gateway teljes tartománynevét.

- Állítsa be az egyéni mintavételt az "állomásnév kiválasztása a háttérbeli HTTP-beállításokból" letiltásához. Ez a portálról a mintavételi beállítások és a PowerShell jelölőnégyzetének törlésével végezhető el, ha nem a-PickHostNameFromBackendHttpSettings kapcsolót használja a set-AzApplicationGatewayProbeConfig parancsban. A mintavétel állomásnév mezőjébe írja be a App Service FQDN "example.azurewebsites.net" értéket, mivel a Application Gateway által küldött mintavételi kérelmek ezt a gazdagép fejlécében fogják végrehajtani.

  > [!NOTE]
  > A következő lépés végrehajtásakor győződjön meg arról, hogy az egyéni mintavétel nincs társítva a háttérbeli HTTP-beállításokhoz, mert a HTTP-beállítások továbbra is a "válasszon állomásnév a háttérbeli címről" kapcsolót.

- Állítsa be a Application Gateway HTTP-beállításait az "állomásnév kiválasztása a háttérbeli címről" beállítás letiltásához. Ezt a portálról a jelölőnégyzet és a PowerShell jelölőnégyzetének törlésével teheti meg, ha nem a set-AzApplicationGatewayBackendHttpSettings parancsban a-PickHostNameFromBackendAddress kapcsolót használja.

- Rendelje vissza az egyéni mintavételt a háttérbeli HTTP-beállításokhoz, és ellenőrizze a háttér állapotát, ha az kifogástalan állapotú.

- Ha ezt elvégezte, Application Gateway továbbítania kell ugyanazt a hostname "www.contoso.com"-t a App Servicera, és az átirányítás ugyanazon a gazdagépen fog történni. Az alábbi példákban megtekintheti a kérelmek és válaszok fejléceit.

A fent említett lépések végrehajtásához a PowerShell használatával egy meglévő telepítéshez kövesse az alábbi PowerShell-szkriptet. Figyelje meg, hogy nem használtuk a-PickHostname kapcsolókat a mintavétel és a HTTP-beállítások konfigurációjában.

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

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).
