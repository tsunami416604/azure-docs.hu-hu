---
title: Meglévő egyéni SSL-tanúsítvány kötése – az Azure App Service |} A Microsoft Docs
description: Megtudhatja, hogyan köthet egyéni SSL-tanúsítványt a webalkalmazáshoz, egy mobilalkalmazás hátérrendszeréhez vagy egy API-alkalmazáshoz az Azure App Service-ben.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d627158744ae89e3c614d10a64886cee6879a3fb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116049"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>Oktatóanyag: Meglévő egyéni SSL-tanúsítvány kötése az Azure App Service-ben

Az Azure App Service egy rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt. Az oktatóanyag bemutatja, hogyan köthet, egy megbízható hitelesítésszolgáltatótól vásárolt egyéni SSL-tanúsítvány [Azure App Service](overview.md). Ha elkészült, fogja tudni elérni az alkalmazást az egyéni DNS-tartomány HTTPS-végpontján.

![Egyéni SSL-tanúsítványt használó webalkalmazás](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás tarifacsomagjának bővítése.
> * Az egyéni tanúsítvány App Service-hez kötése
> * Tanúsítványok megújítása
> * HTTPS kényszerítése
> * A TLS 1.1/1.2 kényszerítése
> * A TLS kezelésének automatizálása szkriptekkel

> [!NOTE]
> Egy egyéni SSL-tanúsítvány van szüksége, ha közvetlenül be egyet, az Azure Portalon, és kösse az alkalmazást. Kövesse az [App Service-tanúsítványok szóló oktatóanyag](web-sites-purchase-ssl-web-site.md) utasításait.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Egy egyéni DNS-név leképezése az App Service-alkalmazás](app-service-web-tutorial-custom-domain.md)
- Be kell szereznie egy SSL-tanúsítványt egy megbízható hitelesítésszolgáltatótól.
- Rendelkeznie kell az SSL-tanúsítványkérés aláírásához használt titkos kulccsal.

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Az SSL-tanúsítvány követelményei

A tanúsítvány App Service-ben történő használatához a tanúsítványnak meg kell felelnie az alábbi követelmények mindegyikének:

* A tanúsítványt megbízható hitelesítésszolgáltatónak kell aláírnia.
* Jelszóval védett PFX-fájlként kell exportálni.
* Legalább 2048 bit hosszúságú titkos kulcsot kell tartalmaznia.
* Tartalmaznia kell a tanúsítványláncban lévő összes köztes tanúsítványt.

> [!NOTE]
> **Az elliptikus görbéjű titkosítási (ECC-) tanúsítványok** együttműködnek az App Service-szel, ez a cikk azonban erre nem tér ki. A hitelesítésszolgáltatóval együttműködve dolgozzák ki az ECC-tanúsítványok létrehozására szolgáló lépéseket.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Az SSL-tanúsítvány kötése

Az SSL-tanúsítvány feltöltéséhez az alkalmazás készen áll.

### <a name="merge-intermediate-certificates"></a>Köztes tanúsítványok egyesítése

Ha a hitelesítésszolgáltató több tanúsítványt biztosít a tanúsítványláncban, sorrendben kell egyesítenie a tanúsítványokat. 

Ehhez nyissa meg a kapott tanúsítványokat egy szövegszerkesztőben. 

Hozzon létre egy _mergedcertificate.crt_ nevű fájlt az egyesített tanúsítvány számára. Egy szövegszerkesztőben másolja ebbe a fájlba az egyes tanúsítványok tartalmát. A tanúsítványok sorrendjének egyeznie kell a tanúsítványláncban lévő sorrenddel, a saját tanúsítvánnyal kezdve és a főtanúsítvánnyal végződve. Az alábbi példához hasonlóan néz ki:

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

### <a name="export-certificate-to-pfx"></a>Tanúsítvány exportálása PFX-fájlba

Exportálja az egyesített SSL-tanúsítványt a tanúsítványkérés létrehozásához használt titkos kulccsal.

Ha OpenSSL használatával hozta létre a tanúsítványkérést, akkor létrehozott egy titkoskulcsfájlt. A tanúsítvány PFX-fájlba exportáláshoz futtassa az alábbi parancsot. Cserélje le a _&lt;private-key-file>_ és _&lt;merged-certificate-file>_ helyőrzőt a titkos kulcs és az egyesített tanúsítványfájl elérési útjára.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Amikor a rendszer megkéri, adjon meg egy exportálási jelszót. Ezt a jelszót fogja használni, amikor később feltölti az SSL-tanúsítványt az App Service-be.

Ha az IIS vagy a _Certreq.exe_ használatával hozta létre a tanúsítványkérést, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX-fájlba](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Az SSL-tanúsítvány feltöltése

Az SSL-tanúsítvány feltöltéséhez kattintson **SSL-beállítások** az alkalmazás a bal oldali navigációs.

Kattintson az **Upload Certificate** (Tanúsítvány feltöltése) parancsra. 

A **PFX Certificate File** (PFX-tanúsítványfájl) mezőben válassza ki a PFX-fájlt. A **Certificate password** (Tanúsítvány jelszava) területen írja be a PFX-fájl exportálásakor létrehozott jelszót.

Kattintson a **Feltöltés** gombra.

![Tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Amikor az App Service befejezi a tanúsítvány feltöltését, a tanúsítvány megjelenik az **SSL settings** (SSL-beállítások) lapon.

![Feltöltött tanúsítvány](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Az SSL-tanúsítvány kötése

Az **SSL bindings** (SSL-kötések) területen kattintson az **Add binding** (Kötés hozzáadása) elemre.

Az **Add SSL Binding** (SSL-kötés hozzáadása) lapon a legördülő listákkal válassza ki a védeni kívánt tartománynevet és a használni kívánt tanúsítványt.

> [!NOTE]
> Ha feltöltötte a tanúsítványt, de nem látja a tartományneve(ke)t a **Hostname** (Gazdanév) legördülő listában, próbálkozzon a böngészőoldal frissítésével.
>
>

Az **SSL Type** (SSL típusa) területen válassza ki, hogy a **[kiszolgálónév jelzésén (SNI)](https://en.wikipedia.org/wiki/Server_Name_Indication)** alapuló vagy IP-alapú SSL-t kíván-e használni.

- **SNI-based SSL** (SNI-alapú SSL) – Több SNI-alapú SSL-kötés adható hozzá. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).
- **IP-based SSL** (IP-alapú SSL) – Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. Több tartomány védelméhez mindegyik tartományt ugyanazzal az SSL-tanúsítvánnyal kell védelemmel ellátni. Ez az SSL-kötések hagyományos beállítása.

Kattintson az **Add Binding** (Kötés hozzáadása) lehetőségre.

![SSL-tanúsítvány kötése](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Amikor az App Service befejezi a tanúsítvány feltöltését, a tanúsítvány megjelenik az **SSL bindings** (SSL-kötések) szakaszban.

![Webalkalmazáshoz kötött tanúsítvány](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Az A rekord újbóli leképezése az IP SSL-re

Ha az alkalmazás IP-alapú SSL nem használja, ugorjon a [az egyéni tartomány HTTPS teszt](#test).

Alapértelmezés szerint az alkalmazás megosztott nyilvános IP-címet használ. IP-alapú SSL-tanúsítvány kötése, amikor az App Service-ben az alkalmazás egy új, dedikált IP-címet hoz létre.

Ha egy A rekordot képezett le az alkalmazáshoz, frissítse a tartomány beállításjegyzékét az új, dedikált IP-címet.

Az alkalmazás **egyéni tartomány** lap frissül az új, dedikált IP-címet. [Másolja ezt az IP-címet](app-service-web-tutorial-custom-domain.md#info), majd [képezze le újra az A rekordot](app-service-web-tutorial-custom-domain.md#map-an-a-record) erre az új IP-címre.

<a name="test"></a>

## <a name="test-https"></a>HTTPS tesztelése

Már csak annak ellenőrzése van hátra, hogy a HTTPS működik-e az egyéni tartomány esetén. A különböző böngészők, keresse meg a `https://<your.custom.domain>` megtekintéséhez, hogy azt kiszolgálja az alkalmazást.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Ha az alkalmazás ad tanúsítvány-ellenőrzési hibákat, valószínűleg önaláírt tanúsítványt használ.
>
> Ha nem így van, előfordulhat, hogy kihagyott néhány köztes tanúsítványt, amikor a tanúsítványt a PFX-fájlba exportálta.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Tanúsítványok megújítása

A bejövő IP-cím kötések törlésekor változhat, akkor is, ha a kötés IP-alapú. Ez akkor kifejezetten fontos, ha egy olyan tanúsítványt újít meg, amely már egy IP-alapú kötésben található. Annak érdekében, hogy az alkalmazás IP-címe ne változzon, kövesse sorrendben az alábbi lépéseket:

1. Töltse fel az új tanúsítványt.
2. Kösse az új tanúsítványt a kívánt egyéni tartományhoz anélkül, hogy törölné a régit. Ez a művelet lecserélni a kötést ahelyett, hogy eltávolítaná a régit.
3. Törölje a régi tanúsítványt. 

## <a name="enforce-https"></a>HTTPS kényszerítése

Alapértelmezés szerint bárki elérheti az alkalmazás HTTP-n keresztül. A HTTP-kéréseket átirányíthatja a HTTPS-portra.

Az alkalmazás oldalán a bal oldali navigációs sávján válassza **SSL-beállítások**. Ezután a **HTTPS Only** (Csak HTTPS) területen válassza az **On** (Be) elemet.

![HTTPS kényszerítése](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Ha a művelet befejeződött, nyissa meg az alkalmazásra mutató HTTP URL-címek valamelyikét. Példa:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-verziók kényszerítése

Az alkalmazása alapértelmezés szerint a [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2-t engedélyezi, amely az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) szerint ajánlott TLS-szint. A TLS más verzióinak kényszerítéséhez kövesse az alábbi lépéseket:

Az alkalmazás oldalán a bal oldali navigációs sávján válassza **SSL-beállítások**. Ezután a **TLS version** (TLS-verzió) szakaszban válassza ki a kívánt TLS minimális verzióját. Ez a beállítás csak a bejövő hívásokat szabályozza. 

![A TLS 1.1 vagy 1.2 kényszerítése](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

A művelet befejezése után az alkalmazás elutasítja a korábbi TLS-verziójú kapcsolatokat.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Automatizálhatja SSL-kötések szkriptekkel, az alkalmazás használatával az [Azure CLI-vel](/cli/azure/install-azure-cli) vagy [Azure PowerShell-lel](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Az alábbi parancs feltölt egy exportált PFX-fájlt, és lekéri az ujjlenyomatot.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

A következő parancs SNI-alapú SSL-kötést ad hozzá az előző parancsból származó ujjlenyomat használatával.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

A következő parancs a TLS legalább 1.2-es verzióját kényszeríti ki.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancs feltölt egy exportált PFX-fájlt, és SNI-alapú SSL-kötést ad hozzá.

```PowerShell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Nyilvános tanúsítványok (nem kötelező)
Az alkalmazás távoli erőforrásokhoz fér hozzá a ügyfélként kell, és a távoli erőforrás Tanúsítványalapú hitelesítés szükséges, feltölthet [nyilvános tanúsítványokat](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) az alkalmazáshoz. A nyilvános tanúsítványok nem szükségesek az alkalmazás SSL-kötések.

A nyilvános tanúsítványok az alkalmazásokba való feltöltésével és használatával kapcsolatos további információkért lásd az [SSL-tanúsítvány az alkalmazáskódban az Azure App Service-ben való használatát](app-service-web-ssl-cert-load.md) ismertető cikket. Használhatja a nyilvános tanúsítványok alkalmazásokkal az App Service Environment-környezetek túl. Tárolja a tanúsítványt a LocalMachine tanúsítványtárolóban kell, ha szeretné használni egy alkalmazást az App Service Environment-környezet. További információkért lásd: [az App Service-alkalmazást a nyilvános tanúsítványok konfigurálása](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Nyilvános tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazás tarifacsomagjának bővítése.
> * Az egyéni tanúsítvány App Service-hez kötése
> * Tanúsítványok megújítása
> * HTTPS kényszerítése
> * A TLS 1.1/1.2 kényszerítése
> * A TLS kezelésének automatizálása szkriptekkel

A következő oktatóanyag azt mutatja be, hogy hogyan használhatja az Azure Content Delivery Networköt.

> [!div class="nextstepaction"]
> [Content Delivery Network (CDN) hozzáadása Azure App Service platformon](../cdn/cdn-add-to-web-app.md)

További információ: [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben).
