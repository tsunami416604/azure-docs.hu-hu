---
title: Egyéni DNS biztonságossá tétele SSL-kötéssel
description: Biztonságos HTTPS-hozzáférés az egyéni tartományhoz egy TLS/SSL-kötés használatával egy tanúsítvánnyal. Javítsa webhelye biztonságát a HTTPS vagy a TLS 1.2 kényszerítésével.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239706"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Egyéni DNS-név védelme SSL-kötéssel az Azure App Service-ben

Ez a cikk bemutatja, hogyan biztosíthatja az [egyéni tartományt](app-service-web-tutorial-custom-domain.md) az [App Service-alkalmazásban](https://docs.microsoft.com/azure/app-service/) vagy [a függvényalkalmazásban](https://docs.microsoft.com/azure/azure-functions/) egy tanúsítványkötés létrehozásával. Ha elkészült, elérheti az App Service-alkalmazást `https://` a végponton az egyéni DNS-nevéhez (például). `https://www.contoso.com` 

![Egyéni SSL-tanúsítványt használó webalkalmazás](./media/configure-ssl-bindings/app-with-custom-ssl.png)

[Egyéni tartomány](app-service-web-tutorial-custom-domain.md) tanúsítványlal történő védelme két lépésből áll:

- [Adjon hozzá egy privát tanúsítványt az App Service szolgáltatáshoz,](configure-ssl-certificate.md) amely megfelel az [SSL-kötések összes követelményének.](configure-ssl-certificate.md#private-certificate-requirements)
-  Hozzon létre egy SSL-kötést a megfelelő egyéni tartományhoz. Ez a második lépés a cikk hatálya alá tartozik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás tarifacsomagjának bővítése.
> * Egyéni tartomány védelme tanúsítvánnyal
> * HTTPS kényszerítése
> * A TLS 1.1/1.2 kényszerítése
> * A TLS kezelésének automatizálása szkriptekkel

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követése:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Tartománynév hozzárendelése az alkalmazáshoz,](app-service-web-tutorial-custom-domain.md) vagy [vásárlás és konfigurálás az Azure-ban](manage-custom-dns-buy-domain.md)
- [Privát tanúsítvány hozzáadása az alkalmazáshoz](configure-ssl-certificate.md)

> [!NOTE]
> A magántanúsítványok hozzáadásának legegyszerűbb módja egy ingyenes App Service Felügyelt tanúsítvány (előzetes verzió) [létrehozása.](configure-ssl-certificate.md#create-a-free-certificate-preview)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Egyéni tartomány védelme

Tegye a következő lépéseket:

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján indítsa el a **TLS/SSL kötés** párbeszédpanelt:

- **Egyéni tartományok** > **hozzáadása kötés hozzáadása**
- **TLS/SSL-beállítások** > **hozzáadása TLS/SSL kötés**

![Kötés hozzáadása a tartományhoz](./media/configure-ssl-bindings/secure-domain-launch.png)

Az **Egyéni tartomány ban**jelölje ki azt az egyéni tartományt, amelyhez kötést szeretne hozzáadni.

Ha az alkalmazás már rendelkezik tanúsítvánnyal a kijelölt egyéni tartományhoz, nyissa meg a [Kötés létrehozása](#create-binding) közvetlenül című lehetőséget. Ellenkező esetben folytasd.

### <a name="add-a-certificate-for-custom-domain"></a>Tanúsítvány hozzáadása egyéni tartományhoz

Ha az alkalmazás nem rendelkezik tanúsítvánnyal a kijelölt egyéni tartományhoz, akkor két lehetősége van:

- **PFX-tanúsítvány feltöltése** – Kövesse a munkafolyamatot [a Privát tanúsítvány feltöltése](configure-ssl-certificate.md#upload-a-private-certificate)területen, majd itt válassza ezt a lehetőséget.
- **App Service-tanúsítvány importálása** – Kövesse a munkafolyamatot az [App Service-tanúsítvány importálása](configure-ssl-certificate.md#import-an-app-service-certificate)című helyen, majd itt válassza ezt a lehetőséget.

> [!NOTE]
> [Létrehozhat ingyenes tanúsítványt](configure-ssl-certificate.md#create-a-free-certificate-preview) (előzetes verziót) vagy [importálhat egy Key Vault-tanúsítványt,](configure-ssl-certificate.md#import-a-certificate-from-key-vault)de ezt külön kell megtennie, majd vissza kell térnie a **TLS/SSL kötés** párbeszédpanelre.

### <a name="create-binding"></a>Kötés létrehozása

Az alábbi táblázat segítségével konfigurálhatja az SSL-kötést a **TLS/SSL kötés** párbeszédpanelen, majd kattintson a **Kötés hozzáadása**gombra.

| Beállítás | Leírás |
|-|-|
| Egyéni tartomány | Az a tartománynév, amelyhez hozzá adja az SSL-kötést. |
| Magántanúsítvány ujjlenyomata | A kötésre kötelezett tanúsítvány. |
| TLS/SSL típus | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - Több SNI SSL kötés adható hozzá. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefoxot és az Operát) támogatja az SNI-t (további információ: [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** – Csak egy IP SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. A kötés konfigurálása után kövesse az [IP SSL rekord újraleképezése](#remap-a-record-for-ip-ssl)című elem lépéseit.<br/>Az IP SSL csak éles vagy elszigetelt szinteken támogatott. </li></ul> |

A művelet befejezése után az egyéni tartomány SSL-állapota **Biztonságos**állapotra változik.

![Az SSL-kötés sikeres](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Az **egyéni tartományok** **biztonságos** állapota azt jelenti, hogy tanúsítványlal van biztosítva, de az App Service nem ellenőrzi, hogy a tanúsítvány önaláírt vagy lejárt-e, például, ami a böngészők nek hibát vagy figyelmeztetést is okozhat.

## <a name="remap-a-record-for-ip-ssl"></a>Az A rekord újbóli leképezése az IP SSL-re

Ha nem használja az IP SSL-t az alkalmazásban, ugorjon [az egyéni tartomány HTTPS tesztelése lehetőségre.](#test-https)

Alapértelmezés szerint az alkalmazás megosztott nyilvános IP-címet használ. Ha IP SSL-lel köt egy tanúsítványt, az App Service új, dedikált IP-címet hoz létre az alkalmazáshoz.

Ha a tartomány beállításjegyzékét az alkalmazáshoz dedikálta, frissítse a tartomány beállításjegyzékét ezzel az új, dedikált IP-címmel.

Az alkalmazás **egyéni tartománylapja** az új, dedikált IP-címmel frissül. [Másolja ezt az IP-címet](app-service-web-tutorial-custom-domain.md#info), majd [képezze le újra az A rekordot](app-service-web-tutorial-custom-domain.md#map-an-a-record) erre az új IP-címre.

## <a name="test-https"></a>HTTPS tesztelése

A különböző böngészőkben `https://<your.custom.domain>` keresse meg annak ellenőrzését, hogy az alkalmazást szolgálja-e.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Az alkalmazáskód az "x-appservice-proto" fejlécen keresztül vizsgálhatja meg a protokollt. A fejléc értéke `http` vagy `https`a. 

> [!NOTE]
> Ha az alkalmazás tanúsítvány-ellenőrzési hibákat ad, valószínűleg önaláírt tanúsítványt használ.
>
> Ha nem így van, előfordulhat, hogy kihagyott néhány köztes tanúsítványt, amikor a tanúsítványt a PFX-fájlba exportálta.

## <a name="prevent-ip-changes"></a>Ip-módosítások megakadályozása

A bejövő IP-cím akkor is változhat, ha töröl egy kötést, még akkor is, ha az IP SSL. Ez különösen akkor fontos, ha olyan tanúsítványt újít meg, amely már IP SSL-kötésben van. Annak érdekében, hogy az alkalmazás IP-címe ne változzon, kövesse sorrendben az alábbi lépéseket:

1. Töltse fel az új tanúsítványt.
2. Kösse az új tanúsítványt a kívánt egyéni tartományhoz anélkül, hogy törölné a régit. Ez a művelet lecserélni a kötést ahelyett, hogy eltávolítaná a régit.
3. Törölje a régi tanúsítványt. 

## <a name="enforce-https"></a>HTTPS kényszerítése

Alapértelmezés szerint bárki elérheti az alkalmazást HTTP használatával. A HTTP-kéréseket átirányíthatja a HTTPS-portra.

Az alkalmazáslapon a bal oldali navigációs sávon válassza az **SSL-beállítások lehetőséget.** Ezután a **HTTPS Only** (Csak HTTPS) területen válassza az **On** (Be) elemet.

![HTTPS kényszerítése](./media/configure-ssl-bindings/enforce-https.png)

Ha a művelet befejeződött, nyissa meg az alkalmazásra mutató HTTP URL-címek valamelyikét. Példa:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS-verziók kényszerítése

Az alkalmazása alapértelmezés szerint a [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2-t engedélyezi, amely az iparági szabványok, például a [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) szerint ajánlott TLS-szint. A TLS más verzióinak kényszerítéséhez kövesse az alábbi lépéseket:

Az alkalmazáslapon a bal oldali navigációs sávon válassza az **SSL-beállítások lehetőséget.** Ezután a **TLS version** (TLS-verzió) szakaszban válassza ki a kívánt TLS minimális verzióját. Ez a beállítás csak a bejövő hívásokat szabályozza. 

![A TLS 1.1 vagy 1.2 kényszerítése](./media/configure-ssl-bindings/enforce-tls1-2.png)

A művelet befejezése után az alkalmazás elutasítja a korábbi TLS-verziójú kapcsolatokat.

## <a name="handle-ssl-termination"></a>SSL-végződés leírója

Az App Service-ben [az SSL-végződtetés](https://wikipedia.org/wiki/TLS_termination_proxy) a hálózati terheléselosztóknál történik, így minden HTTPS-kérelem titkosítatlan HTTP-kérelemként éri el az alkalmazást. Ha az alkalmazás logikájának ellenőriznie kell, hogy `X-Forwarded-Proto` a felhasználói kérelmek titkosítva vannak-e vagy sem, ellenőrizze a fejlécet.

Nyelvspecifikus konfigurációs útmutatók, például a [Linux Node.js konfigurációs](containers/configure-language-nodejs.md#detect-https-session) útmutató, bemutatja, hogyan észlelheti a HTTPS-munkamenetet az alkalmazáskódban.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>További erőforrások

* [SSL-tanúsítvány használata az alkalmazáskódban](configure-ssl-certificate-in-code.md)
* [GYAKORI KÉRDÉSEK : App Service-tanúsítványok](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
