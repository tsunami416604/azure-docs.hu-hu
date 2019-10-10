---
title: SSL-tanúsítvány feltöltése és kötése – Azure App Service | Microsoft Docs
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
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177041"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Oktatóanyag: SSL-tanúsítványok feltöltése és kötése Azure App Service

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan védheti meg App Service egy egyéni tartományát egy megbízható hitelesítésszolgáltatótól vásárolt tanúsítvánnyal. Azt is bemutatja, hogyan tölthet fel az alkalmazás által igényelt magán-és nyilvános tanúsítványokat. Ha elkészült, a saját DNS-tartományának HTTPS-végpontján férhet hozzá az alkalmazáshoz.

![Egyéni SSL-tanúsítványt használó webalkalmazás](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás tarifacsomagjának bővítése.
> * Egyéni tartomány biztonságossá tétele tanúsítvánnyal
> * Privát tanúsítvány feltöltése
> * Nyilvános tanúsítvány feltöltése
> * Tanúsítványok megújítása
> * HTTPS kényszerítése
> * A TLS 1.1/1.2 kényszerítése
> * A TLS kezelésének automatizálása szkriptekkel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Egyéni DNS-név hozzárendelése a app Service-alkalmazáshoz](app-service-web-tutorial-custom-domain.md) (ha egyéni tartományt biztosít)
- Tanúsítvány beolvasása megbízható hitelesítésszolgáltatótól
- Rendelkeznie kell a tanúsítványkérelem aláírásához használt titkos kulccsal (privát tanúsítványok esetén)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Privát tanúsítvány előkészítése

A tartomány biztonságossá tételéhez a tanúsítványnak meg kell felelnie az alábbi követelményeknek:

* Kiszolgáló-hitelesítésre konfigurálva
* A tanúsítványt megbízható hitelesítésszolgáltatónak kell aláírnia.
* Jelszóval védett PFX-fájlként kell exportálni.
* Legalább 2048 bit hosszúságú titkos kulcsot kell tartalmaznia.
* Tartalmaznia kell a tanúsítványláncban lévő összes köztes tanúsítványt.

> [!TIP]
> Ha egyéni SSL-tanúsítványt kell beszereznie, közvetlenül is beszerezhet egyet a Azure Portal, és importálhatja azt az alkalmazásba. Kövesse az [App Service-tanúsítványok szóló oktatóanyag](web-sites-purchase-ssl-web-site.md) utasításait.

> [!NOTE]
> **Az elliptikus görbéjű titkosítási (ECC-) tanúsítványok** együttműködnek az App Service-szel, ez a cikk azonban erre nem tér ki. A hitelesítésszolgáltatóval együttműködve dolgozzák ki az ECC-tanúsítványok létrehozására szolgáló lépéseket.

Miután beszerezte a tanúsítványt a hitelesítésszolgáltatótól, kövesse az ebben a szakaszban ismertetett lépéseket, hogy készen álljon a App Servicere.

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

Most már készen áll a tanúsítvány feltöltésére App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Egyéni tartomány biztonságossá tétele

> [!TIP]
> Ha egyéni SSL-tanúsítványt kell beszereznie, közvetlenül is beszerezhet egyet a Azure Portal, és az alkalmazáshoz kötheti. Kövesse az [App Service-tanúsítványok szóló oktatóanyag](web-sites-purchase-ssl-web-site.md) utasításait.

Egy harmadik féltől származó tanúsítvánnyal rendelkező [egyéni tartomány](app-service-web-tutorial-custom-domain.md) biztonságossá tételéhez töltse fel az [előkészített privát tanúsítványt](#prepare-a-private-certificate) , majd kösse azt az egyéni tartományhoz, de app Service leegyszerűsíti a folyamatot. Hajtsa végre a következő lépéseket:

Kattintson az **Egyéni tartományok** lehetőségre az alkalmazás bal oldali navigációs sávján, majd kattintson a **kötés hozzáadása** elemre a védeni kívánt tartományhoz. Ha nem látja a **kötés hozzáadása** egy tartományhoz, akkor az már biztonságos, és **biztonságos** SSL-állapottal kell rendelkeznie.

![Kötés hozzáadása a tartományhoz](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Kattintson az **Upload Certificate** (Tanúsítvány feltöltése) parancsra.

A **PFX Certificate File** (PFX-tanúsítványfájl) mezőben válassza ki a PFX-fájlt. A **Certificate password** (Tanúsítvány jelszava) területen írja be a PFX-fájl exportálásakor létrehozott jelszót.

Kattintson a **Feltöltés** gombra.

![Tartományhoz tartozó tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Várjon, amíg az Azure feltölti a tanúsítványt, és elindítja az SSL-kötések párbeszédpanelt.

Az SSL-kötések párbeszédpanelen válassza ki a feltöltött tanúsítványt és az SSL-típust, majd kattintson a **kötés hozzáadása**elemre.

> [!NOTE]
> A következő SSL-típusok támogatottak:
>
> - **[SNI-alapú SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** – több SNI-alapú SSL-kötés is felvehető. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).
> - **IP-based SSL** (IP-alapú SSL) – Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. Több tartomány védelméhez mindegyik tartományt ugyanazzal az SSL-tanúsítvánnyal kell védelemmel ellátni. Ez az SSL-kötések hagyományos beállítása.

![SSL kötése tartományhoz](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

A tartomány SSL-állapotát mostantól **biztonságos**értékre kell módosítani.

![Biztonságos tartomány](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Az **Egyéni tartományokban** található **biztonságos** állapot azt jelenti, hogy a tanúsítvány védett, de app Service nem vizsgálja, hogy a tanúsítvány önaláírt vagy lejárt, például hogy a böngészők hibát vagy figyelmeztetést jelenítenek-e meg.

## <a name="remap-a-record-for-ip-ssl"></a>Az A rekord újbóli leképezése az IP SSL-re

Ha nem használ IP-alapú SSL-t az alkalmazásban, ugorjon a [https tesztelése az egyéni tartományhoz](#test)lehetőségre.

Alapértelmezés szerint az alkalmazás egy megosztott nyilvános IP-címet használ. Ha IP-alapú SSL-sel köt egy tanúsítványt, App Service létrehoz egy új, dedikált IP-címet az alkalmazáshoz.

Ha hozzárendelt egy rekordot az alkalmazáshoz, frissítse a tartomány beállításjegyzékét ezzel az új, dedikált IP-címmel.

Az alkalmazás **egyéni tartomány** lapja az új, dedikált IP-címmel frissül. [Másolja ezt az IP-címet](app-service-web-tutorial-custom-domain.md#info), majd [képezze le újra az A rekordot](app-service-web-tutorial-custom-domain.md#map-an-a-record) erre az új IP-címre.

<a name="test"></a>

## <a name="test-https"></a>HTTPS tesztelése

Már csak annak ellenőrzése van hátra, hogy a HTTPS működik-e az egyéni tartomány esetén. A különböző böngészőkben keresse meg a `https://<your.custom.domain>` lehetőséget, hogy láthassa, hogy az alkalmazást szolgálja-e ki.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Ha az alkalmazás tanúsítvány-ellenőrzési hibákat ad, valószínűleg önaláírt tanúsítványt használ.
>
> Ha nem így van, előfordulhat, hogy kihagyott néhány köztes tanúsítványt, amikor a tanúsítványt a PFX-fájlba exportálta.

## <a name="renew-certificates"></a>Tanúsítványok megújítása

A bejövő IP-cím kötések törlésekor változhat, akkor is, ha a kötés IP-alapú. Ez akkor kifejezetten fontos, ha egy olyan tanúsítványt újít meg, amely már egy IP-alapú kötésben található. Annak érdekében, hogy az alkalmazás IP-címe ne változzon, kövesse sorrendben az alábbi lépéseket:

1. Töltse fel az új tanúsítványt.
2. Kösse az új tanúsítványt a kívánt egyéni tartományhoz anélkül, hogy törölné a régit. Ez a művelet lecserélni a kötést ahelyett, hogy eltávolítaná a régit.
3. Törölje a régi tanúsítványt. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>HTTPS kényszerítése

Alapértelmezés szerint bárki elérheti az alkalmazást HTTP használatával. A HTTP-kéréseket átirányíthatja a HTTPS-portra.

Az alkalmazás lapjának bal oldali navigációs sávján válassza az **SSL-beállítások**elemet. Ezután a **HTTPS Only** (Csak HTTPS) területen válassza az **On** (Be) elemet.

![HTTPS kényszerítése](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Ha a művelet befejeződött, nyissa meg az alkalmazásra mutató HTTP URL-címek valamelyikét. Példa:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-verziók kényszerítése

Az alkalmazása alapértelmezés szerint a [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2-t engedélyezi, amely az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) szerint ajánlott TLS-szint. A TLS más verzióinak kényszerítéséhez kövesse az alábbi lépéseket:

Az alkalmazás lapjának bal oldali navigációs sávján válassza az **SSL-beállítások**elemet. Ezután a **TLS version** (TLS-verzió) szakaszban válassza ki a kívánt TLS minimális verzióját. Ez a beállítás csak a bejövő hívásokat szabályozza. 

![A TLS 1.1 vagy 1.2 kényszerítése](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

A művelet befejezése után az alkalmazás elutasítja a korábbi TLS-verziójú kapcsolatokat.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

Parancsfájlokkal automatizálhatja az alkalmazás SSL-kötéseit az [Azure CLI](/cli/azure/install-azure-cli) vagy a [Azure PowerShell](/powershell/azure/overview)használatával.

### <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az alábbi parancs feltölt egy exportált PFX-fájlt, és lekéri az ujjlenyomatot.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

A következő parancs SNI-alapú SSL-kötést ad hozzá az előző parancsból származó ujjlenyomat használatával.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

A következő parancs kényszeríti az alkalmazást a https használatára.

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

A következő parancs a TLS legalább 1.2-es verzióját kényszeríti ki.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az alábbi parancs feltölt egy exportált PFX-fájlt, és SNI-alapú SSL-kötést ad hozzá.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Tanúsítványok használata a kódban

Ha az alkalmazásnak távoli erőforrásokhoz kell csatlakoznia, és a távoli erőforrásnak tanúsítványalapú hitelesítést kell használnia, nyilvános vagy privát tanúsítványokat tölthet fel az alkalmazásba. Ezeket a tanúsítványokat nem kell minden egyéni tartományhoz kötnie az alkalmazásban. További információ: [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben).

## <a name="next-steps"></a>Következő lépések

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
