---
title: "Egy meglévő egyéni SSL-tanúsítvány kötését az Azure Web Apps |} Microsoft Docs"
description: "Ismerkedjen meg az egyéni SSL-tanúsítványt kötni a webalkalmazást, mobil-háttéralkalmazás vagy az Azure App Service API-alkalmazás."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c18ca8e81fefdee723714c6535160e75ef4d698d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Egy meglévő egyéni SSL-tanúsítvány kötését az Azure Web Apps

Az Azure Web Apps jól skálázható, önálló javítási a webhelyszolgáltató biztosít. Az oktatóanyag bemutatja, hogyan lehet kötést létrehozni egy egyéni SSL-tanúsítványt a megbízható hitelesítésszolgáltatótól származó megvásárolt [Azure Web Apps](app-service-web-overview.md). Amikor végzett, akkor képes lesz a webalkalmazás a HTTPS-végpont az egyéni DNS-tartomány eléréséhez.

![Egyéni SSL-tanúsítvánnyal webalkalmazás](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Frissítse az alkalmazás tarifacsomag kiválasztása
> * Az egyéni SSL-tanúsítvány kötését az App Service
> * Az alkalmazás HTTPS kényszerítése
> * SSL-tanúsítvány kötés parancsfájlokkal automatizálhatja

> [!NOTE]
> Ha egy egyéni SSL-tanúsítvány beszerzése van szüksége, közvetlenül lekérni egy Azure-portálon, és kösse a webes alkalmazást. Kövesse a [App Service-tanúsítványokkal oktatóanyag](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [Az App Service-alkalmazás létrehozása](/azure/app-service/)
- [Egy egyéni DNS-nevet a webalkalmazás hozzárendelését](app-service-web-tutorial-custom-domain.md)
- Szerezzen be egy megbízható hitelesítésszolgáltatótól származó SSL-tanúsítvány
- Az SSL-tanúsítvány kérésére aláírásához használt titkos kulcsa

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Az SSL-tanúsítványra vonatkozó követelményekről

Az App Service tanúsítványt használ, a tanúsítványt a következő követelményeknek kell megfelelniük:

* Megbízható hitelesítésszolgáltató által aláírt
* Jelszóval védett PFX-fájlba exportált
* Titkos kulcs legalább 2048 bit hosszúságú tartalmazza
* A tanúsítványlánc összes köztes tanúsítvány tartalmazza

> [!NOTE]
> **Elliptikus görbe alapú titkosítást (ECC) tanúsítványok** App Service képes együttműködni, de ez a cikk nem vonatkozik. A hitelesítésszolgáltató használni a pontos lépések az ECC-tanúsítványokkal létrehozásához.

## <a name="prepare-your-web-app"></a>A webes alkalmazás előkészítése

Egy egyéni SSL-tanúsítványt kötni a webalkalmazás a [App Service-csomag](https://azure.microsoft.com/pricing/details/app-service/) szerepelnie kell a **alapvető**, **szabványos**, vagy **prémium** réteg. Ezt a lépést akkor győződjön meg arról, hogy a webalkalmazás van a támogatott az IP-címek.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Nyissa meg az [Azure portált](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Keresse meg a webalkalmazás

Kattintson a bal oldali menü **alkalmazásszolgáltatások**, majd kattintson a webalkalmazás nevét.

![Webalkalmazás kiválasztása](./media/app-service-web-tutorial-custom-ssl/select-app.png)

A webalkalmazás kezelése lapon rendelkezik ki.  

### <a name="check-the-pricing-tier"></a>Ellenőrizze az árképzési szint

A bal oldali navigációs sáv a weblap app, görgessen a **beállítások** válassza ki azt **vertikális felskálázás (App Service-csomag)**.

![Méretezett menü](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Győződjön meg arról, hogy a webalkalmazás nem szerepel a **szabad** vagy **megosztott** réteg. A webalkalmazás jelenlegi rétegtől ki van jelölve, egy kékkel által.

![Ellenőrizze a tarifacsomag](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Egyéni SSL nem támogatja a **szabad** vagy **megosztott** réteg. Ha vertikális felskálázás van szüksége, kövesse a következő szakaszban. Ellenkező esetben zárja be a **válasszon tarifacsomagot** lapon, és folytassa a [töltse fel, és az SSL-tanúsítvány kötését](#upload).

### <a name="scale-up-your-app-service-plan"></a>Vertikális felskálázás App Service-csomag

Válasszon egyet a a **alapvető**, **szabványos**, vagy **prémium** rétegek.

Kattintson a **Kiválasztás** gombra.

![Tarifacsomag kiválasztása](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Amikor megjelenik a következő értesítés, a méretezési művelet befejeződött.

![Vertikális felskálázás értesítés](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Az SSL-tanúsítvány kötése

Készen áll az SSL-tanúsítvány feltöltése a webalkalmazáshoz.

### <a name="merge-intermediate-certificates"></a>Köztes tanúsítványok egyesítése

Ha a hitelesítésszolgáltató lehetővé teszi több tanúsítvány a tanúsítványlánc, kell egyesíteni, a tanúsítványok sorrendben. 

Ehhez nyissa meg a minden tanúsítvány kapott egy szövegszerkesztőben. 

Az egyesített tanúsítvány, nevű fájl létrehozása _mergedcertificate.crt_. Egy szövegszerkesztőben másolja az egyes tanúsítványok tartalmat a fájlba. A tanúsítványok sorrendjét a tanúsítványlánc verziótól kezdve a tanúsítványt, és a legfelső szintű tanúsítvány végződő sorrendben kell követnie. Az alábbihoz hasonlóan néz ki:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Tanúsítvány exportálása PFX

Az egyesített SSL-tanúsítvány exportálása a titkos kulccsal, a tanúsítvány kérelmezéséhez során létrejött.

Ha a tanúsítvány kérelmezéséhez OpenSSL használatával jön létre, majd hozott létre a titkos kulcs fájlja. Exportálja a tanúsítványt PFX, futtassa a következő parancsot. Cserélje le a helyőrzőket  _&lt;titkos kulcsfájl >_ és  _&lt;egyesített-tanúsítványfájl >_ és a titkos kulcs és a egyesített fájl elérési útvonalát.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Amikor a rendszer kéri, adja meg az exportálási jelszó. Ezt a jelszót fog használni, amikor az SSL-tanúsítvány feltöltése az App Service később.

Ha az IIS vagy _Certreq.exe_ létrehozni a tanúsítványkérelmet, telepítse a tanúsítványt a helyi számítógépen, majd [exportálja a tanúsítványt PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Az SSL-tanúsítvány feltöltése

Az SSL-tanúsítvány feltöltése, kattintson a **SSL-tanúsítványok** a bal oldali navigációs webalkalmazás.

Kattintson a **-tanúsítvány feltöltése**. 

A **PFX-tanúsítványfájlt**, válassza ki a PFX-fájlt. A **tanúsítványjelszavas**, írja be a PFX-fájl exportálása során létrehozott jelszót.

Kattintson a **Feltöltés** gombra.

![Tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

App Service befejezése után a tanúsítvány feltöltése megjelenik a **SSL-tanúsítványok** lap.

![A tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Az SSL-tanúsítvány kötése

Az a **SSL-kötések** kattintson **kötés hozzáadása**.

Az a **hozzá SSL-kötés** lapon, a legördülő lista, és válassza ki a tartomány nevét biztonságos, valamint a használni kívánt tanúsítványt.

> [!NOTE]
> Ha a tanúsítvány feltöltött, de nem jelenik meg a tartomány nevét a **állomásnév** legördülő menüből, próbálja meg frissíteni a böngésző lapot.
>
>

A **SSL típus**, válassza ki, hogy használja-e  **[kiszolgálónév jelzése (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**  vagy IP-alapú SSL.

- **Az SNI-alapú SSL** -több SNI-alapú SSL-kötések adhatók hozzá. Ez a beállítás lehetővé teszi, hogy a biztonságos több tartományt a azonos IP-címhez több SSL-tanúsítvány. (Beleértve az Internet Explorer, Chrome, Firefox és Opera) a legtöbb modern böngésző támogatja az SNI (átfogóbb böngésző információt, [kiszolgálónév jelzése](http://wikipedia.org/wiki/Server_Name_Indication)).
- **IP-alapú SSL** -csak egy IP-alapú SSL-kötés adhatók hozzá. Ez a beállítás lehetővé teszi, hogy a biztonságos egy dedikált nyilvános IP-cím csak egyetlen SSL-tanúsítvány. Több tartomány biztonságos, biztosítania kell őket az összes SSL-tanúsítvánnyal. Ez a lehetőség a hagyományos SSL-kötés.

Kattintson a **kötés hozzáadása**.

![SSL-tanúsítvány kötésének létrehozása](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

App Service befejezése után a tanúsítvány feltöltése megjelenik a **SSL-kötések** szakaszok.

![A webes alkalmazás kötött tanúsítvány](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Adja meg újból egy rekordot az IP-SSL

Ha IP-alapú SSL nem adja meg a web app alkalmazásban, folytassa a [teszt a HTTPS-t az egyéni tartomány](#test).

Alapértelmezés szerint a webalkalmazás egy megosztott nyilvános IP-címet használja. IP-alapú SSL tanúsítvány kötése, az App Service létrehoz egy új, dedikált IP-címet a webes alkalmazást.

Ha a webes alkalmazás egy A rekordot van leképezve, a tartomány rendszerleíró adatbázis frissítése az új, dedikált IP-címmel.

A webalkalmazás **egyéni tartomány** lap tartalmát az új, dedikált IP-címmel. [Az IP-cím másolása](app-service-web-tutorial-custom-domain.md#info), majd [megfelelteti az A rekord](app-service-web-tutorial-custom-domain.md#map-an-a-record) ezt új IP-címet.

<a name="test"></a>

## <a name="test-https"></a>Teszt HTTPS

Ehhez marad most csak a győződjön meg arról, hogy HTTPS működik-e az egyéni tartományhoz. A különböző böngészők, keresse meg a `https://<your.custom.domain>` megtekintéséhez, hogy kész a webalkalmazás működik.

![Az Azure alkalmazásba portálnavigációjával](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Ha a webes alkalmazást ad meg a tanúsítvány érvényesítési hibákat, valószínűleg egy önaláírt tanúsítványt használ.
>
> Ha nem, a helyzet, előfordulhat, hogy kilépett azokat a köztes tanúsítványokat amikor exportálta a tanúsítványt a PFX-fájlba.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>HTTPS kényszerítése

Alkalmazás szolgáltatásnak nincs *nem* kényszerítése a HTTPS-t, így bárki, aki továbbra is elérheti a HTTP-n keresztül webalkalmazás. A webalkalmazás HTTPS kényszerítéséhez a átdolgozás szabály megadása a _web.config_ fájl a webalkalmazás. App Service használja ezt a fájlt, a nyelvi keretrendszerébe webalkalmazás függetlenül.

> [!NOTE]
> Kérelmek átirányítása nyelvspecifikus van. ASP.NET MVC használhatja a [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) helyett a módosítsa úgy a szabály a szűrő _web.config_.

Ha a .NET-fejlesztők, ezzel a fájllal viszonylag tisztában kell lennie. A megoldás gyökérkönyvtárában van.

Azt is megteheti Ha a PHP, Node.js, Python vagy Java fejleszt, esély van jelenleg ezt a fájlt az Ön nevében létrehozott App Service-ben.

A webalkalmazás FTP végponthoz kapcsolódni a megadott utasítások szerint [telepítse az alkalmazást az Azure App Service segítségével FTP/S](app-service-deploy-ftp.md).

Ez a fájl megtalálható _/home/site/wwwroot_. Ha nem, hozzon létre egy _web.config_ ebben a mappában van a következő XML-fájlt:

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule ELEMENT FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule ELEMENT FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

Egy létező _web.config_ fájlt, másolja a teljes `<rule>` az elem a _web.config_tartozó `configuration/system.webServer/rewrite/rules` elemet. Ha nincsenek más `<rule>` elemeinek a _web.config_, helyezze a másolt `<rule>` elem előtt a másik `<rule>` elemek.

Ez a szabály egy HTTP 301 (Állandó átirányítás) és a HTTPS protokoll ad vissza, amikor a felhasználó egy HTTP-kérelem esetén a webalkalmazáshoz. Például az átirányítja a `http://contoso.com` való `https://contoso.com`.

Az IIS URL-újraíró modult további információkért tekintse meg a [URL-újraíró](http://www.iis.net/downloads/microsoft/url-rewrite) dokumentációját.

## <a name="enforce-https-for-web-apps-on-linux"></a>A Web Apps Linux HTTPS kényszerítése

Linux alkalmazás-szolgáltatásnak nincs *nem* kényszerítése a HTTPS-t, így bárki, aki továbbra is elérheti a HTTP-n keresztül webalkalmazás. A webalkalmazás HTTPS kényszerítéséhez a átdolgozás szabály megadása a _.htaccess_ fájl a webalkalmazás. 

A webalkalmazás FTP végponthoz kapcsolódni a megadott utasítások szerint [telepítse az alkalmazást az Azure App Service segítségével FTP/S](app-service-deploy-ftp.md).

A _/home/site/wwwroot_, hozzon létre egy _.htaccess_ fájl a következő kóddal:

```
RewriteEngine On
RewriteCond %{HTTP:X-ARR-SSL} ^$
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

Ez a szabály egy HTTP 301 (Állandó átirányítás) és a HTTPS protokoll ad vissza, amikor a felhasználó egy HTTP-kérelem esetén a webalkalmazáshoz. Például az átirányítja a `http://contoso.com` való `https://contoso.com`.

## <a name="automate-with-scripts"></a>Parancsfájlok automatizálásához

Automatizálható SSL-kötések a webalkalmazás, parancsfájlok, használja a [Azure CLI](/cli/azure/install-azure-cli) vagy [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

A következő parancsot az exportált PFX-fájlt feltölti, és az ujjlenyomat beolvasása.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Az alábbi parancs hozzáadja az SNI-alapú SSL-kötést, az előző parancs ujjlenyomatának használatával.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

A következő parancsot az exportált PFX-fájlt feltölti, és hozzáadja az SNI-alapú SSL-kötést.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Nyilvános tanúsítványokat (nem kötelező)
Feltöltheti a [nyilvános tanúsítványokat](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) a webalkalmazáshoz. A webalkalmazásokkal az App Service vagy az App Service-környezet (ASE) a nyilvános tanúsítványokat is használhat. Ha a tanúsítványt a LocalMachine tanúsítványtárolójában van szüksége, kell használni egy webalkalmazást az App Service-környezetben. További részletekért lásd: [konfigurálása a nyilvános tanúsítványokat a webalkalmazáshoz](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Nyilvános tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Frissítse az alkalmazás tarifacsomag kiválasztása
> * Az egyéni SSL-tanúsítvány kötését az App Service
> * Az alkalmazás HTTPS kényszerítése
> * SSL-tanúsítvány kötés parancsfájlokkal automatizálhatja

A következő oktatóanyag megtudhatja, hogyan használható az Azure Content Delivery Network továbblépés.

> [!div class="nextstepaction"]
> [A Content Delivery Network (CDN) hozzáadása az Azure App Service](app-service-web-tutorial-content-delivery-network.md)
