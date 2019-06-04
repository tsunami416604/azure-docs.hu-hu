---
title: Feltölteni és hozzákötni SSL-tanúsítvány – az Azure App Service |} A Microsoft Docs
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
ms.openlocfilehash: e0ee1e7c652ddf4126fc9658bf17d3e919d7a5c8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475365"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Oktatóanyag: Töltse fel, és az Azure App Service SSL-tanúsítványok kötése

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan védheti meg az App Service egyéni tartománnyal, amelyen egy tanúsítványt egy megbízható hitelesítésszolgáltatótól vásárolt. Azt is bemutatja, hogyan tölthetők fel minden olyan privát és nyilvános tanúsítványokat az alkalmazás igényeinek megfelelően. Ha elkészült, fogja tudni elérni az alkalmazást az egyéni DNS-tartomány HTTPS-végpontján.

![Egyéni SSL-tanúsítványt használó webalkalmazás](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás tarifacsomagjának bővítése.
> * Egyéni tartomány tanúsítvánnyal biztonságos
> * Privát tanúsítvány feltöltése
> * Nyilvános tanúsítvány feltöltése
> * Tanúsítványok megújítása
> * HTTPS kényszerítése
> * A TLS 1.1/1.2 kényszerítése
> * A TLS kezelésének automatizálása szkriptekkel

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Egy egyéni DNS-név leképezése az App Service-alkalmazást](app-service-web-tutorial-custom-domain.md) (ha az egyéni tartomány védelme)
- Szerezzen be egy megbízható hitelesítésszolgáltatótól származó tanúsítványt
- A (a személyes tanúsítványok) a tanúsítvány kérés aláírásához használt titkos kulccsal rendelkezik

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Privát tanúsítvány előkészítése

Biztonságos egy tartományhoz, a tanúsítványt a következő követelményeknek kell megfelelnie:

* Kiszolgáló-hitelesítési konfigurálva
* A tanúsítványt megbízható hitelesítésszolgáltatónak kell aláírnia.
* Jelszóval védett PFX-fájlként kell exportálni.
* Legalább 2048 bit hosszúságú titkos kulcsot kell tartalmaznia.
* Tartalmaznia kell a tanúsítványláncban lévő összes köztes tanúsítványt.

> [!TIP]
> Egy egyéni SSL-tanúsítvány van szüksége, ha közvetlenül be egyet, az Azure Portalon, és importálja azt az alkalmazást. Kövesse az [App Service-tanúsítványok szóló oktatóanyag](web-sites-purchase-ssl-web-site.md) utasításait.

> [!NOTE]
> **Az elliptikus görbéjű titkosítási (ECC-) tanúsítványok** együttműködnek az App Service-szel, ez a cikk azonban erre nem tér ki. A hitelesítésszolgáltatóval együttműködve dolgozzák ki az ECC-tanúsítványok létrehozására szolgáló lépéseket.

Amint beszerzett egy tanúsítványt a tanúsítvány szolgáltatói, kövesse az ebben a szakaszban, hogy készen áll az App Service.

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

Most már most már készen áll a tanúsítvány feltöltése App Service-ben.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Egyéni tartomány védelme

> [!TIP]
> Egy egyéni SSL-tanúsítvány van szüksége, ha közvetlenül be egyet, az Azure Portalon, és kösse az alkalmazást. Kövesse az [App Service-tanúsítványok szóló oktatóanyag](web-sites-purchase-ssl-web-site.md) utasításait.

Biztonságos egy [egyéni tartomány](app-service-web-tutorial-custom-domain.md) egy külső tanúsítvány feltöltése a [személyes tanúsítvány előkészített](#prepare-a-private-certificate) , és kösse az egyéni tartomány, de az App Service leegyszerűsíti a folyamatot az Ön számára. Kövesse az alábbi lépéseket:

Kattintson a **egyéni tartományok** az alkalmazás bal oldali navigációs sávján kattintson a **kötés hozzáadása** szeretné biztonságossá tenni a tartományhoz. Ha nem lát **kötés hozzáadása** tartományhoz, majd már biztonságos, és rendelkeznie kell egy **biztonságos** SSL-állapot.

![Kötés hozzáadása a tartományhoz](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Kattintson az **Upload Certificate** (Tanúsítvány feltöltése) parancsra.

A **PFX Certificate File** (PFX-tanúsítványfájl) mezőben válassza ki a PFX-fájlt. A **Certificate password** (Tanúsítvány jelszava) területen írja be a PFX-fájl exportálásakor létrehozott jelszót.

Kattintson a **Feltöltés** gombra.

![Tartomány-tanúsítvány feltöltése](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Várjon, amíg az Azure-ban töltse fel a tanúsítványt, és indítsa el az SSL-kötései párbeszédpanelen.

Az SSL kötései párbeszédpanelen válassza ki a feltöltött tanúsítvány és az SSL-típus, és kattintson **kötésének hozzáadása**.

> [!NOTE]
> A következő SSL-típusokat támogatja:
>
> - **[SNI-alapú SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)**  – több SNI-alapú SSL-kötés adható hozzá. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).
> - **IP-based SSL** (IP-alapú SSL) – Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. Több tartomány védelméhez mindegyik tartományt ugyanazzal az SSL-tanúsítvánnyal kell védelemmel ellátni. Ez az SSL-kötések hagyományos beállítása.

![Az SSL kötést létrehozni tartományhoz](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

A tartomány SSL-állapot most kell módosítani, **biztonságos**.

![Védett tartomány](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> A **biztonságos** az állapot a **egyéni tartományok** azt jelenti, hogy, egy tanúsítvány védi, de az App Service-ben nem jelentkezik be, ha a tanúsítvány önaláírt vagy lejárt, például amelyek is okozhatnak a böngészők a hibák vagy figyelmeztetések megjelenítése.

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

## <a name="renew-certificates"></a>Tanúsítványok megújítása

A bejövő IP-cím kötések törlésekor változhat, akkor is, ha a kötés IP-alapú. Ez akkor kifejezetten fontos, ha egy olyan tanúsítványt újít meg, amely már egy IP-alapú kötésben található. Annak érdekében, hogy az alkalmazás IP-címe ne változzon, kövesse sorrendben az alábbi lépéseket:

1. Töltse fel az új tanúsítványt.
2. Kösse az új tanúsítványt a kívánt egyéni tartományhoz anélkül, hogy törölné a régit. Ez a művelet lecserélni a kötést ahelyett, hogy eltávolítaná a régit.
3. Törölje a régi tanúsítványt. 

<a name="bkmk_enforce"></a>

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
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

A következő parancs a TLS legalább 1.2-es verzióját kényszeríti ki.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
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

Ha az alkalmazásnak szüksége van a távoli erőforrásokhoz történő csatlakozáshoz, és a távoli erőforrás Tanúsítványalapú hitelesítés szükséges, nyilvános vagy privát tanúsítványok tölthet fel az alkalmazáshoz. Nem kell kötni ezeket a tanúsítványokat az alkalmazás bármely egyéni tartomány. További információ: [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben).

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
