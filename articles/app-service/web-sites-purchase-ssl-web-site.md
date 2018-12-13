---
title: Az Azure - App Service egy SSL-tanúsítvány vásárlása és konfigurálása |} A Microsoft Docs
description: App Service-tanúsítvány vásárlása és kösse az App Service-alkalmazás
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: ff2fd2c9b66cc9c80087ab5009ee65c0ba73714b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268716"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Vásárlása és konfigurálása az Azure App Service SSL-tanúsítvány

Ez az oktatóanyag bemutatja, hogyan teheti biztonságossá webalkalmazását (beszerzés) létrehozásával az App Service-tanúsítvány [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) , és kösse az App Service-alkalmazás.

> [!TIP]
> App Service-tanúsítványok bármilyen Azure és az Azure Services használható, és nem korlátozódik az App Services. Ehhez szüksége egy App Service-tanúsítvány, hogy ezzel bárhol felhasználható helyi PFX másolatának létrehozásához. További információkért lásd: [létrehozása egy App Service-tanúsítvány PFX helyi példányának](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató követéséhez:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [A webalkalmazás egy tartománynév hozzárendelése](app-service-web-tutorial-custom-domain.md) vagy [vásárlása és konfigurálása, az Azure-ban](custom-dns-web-site-buydomains-web-app.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>A tanúsítványrendelések indítása

Egy App Service-tanúsítvány sorrendben indítsa el a <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service-tanúsítvány létrehozása oldal</a>.

![Tanúsítvány létrehozása](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Használja az alábbi táblázat segítségével konfigurálja a tanúsítványt. Ha végzett, kattintson a **Létrehozás** gombra.

| Beállítás | Leírás |
|-|-|
| Name (Név) | Az App Service-tanúsítvány rövid nevét. |
| Csupasz tartománynevet tartalmazó állomásnév | Ebben a lépésben az egyik legfontosabb részeit a vásárlási folyamat. A legfelső szintű tartománynév az alkalmazáshoz rendelt használja. Tegye _nem_ illesztenie a tartománynevet `www`. |
| Előfizetés | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Erőforráscsoport | Az erőforráscsoport, amely tartalmazza a tanúsítványt. Egy új erőforráscsoport használata, vagy válassza ki ugyanazt az erőforráscsoportot, az App Service-alkalmazást. |
| Tanúsítványváltozat | Határozza meg a tanúsítványt szeretne létrehozni, hogy egy normál tanúsítványt, vagy [helyettesítő tanúsítvány](https://wikipedia.org/wiki/Wildcard_certificate). |
| Jogi feltételek | Ide kattintva erősítse meg, hogy elfogadja a jogi feltételeket. |

## <a name="store-in-azure-key-vault"></a>Az Azure Key Vaultban Store

A vásárlási folyamat befejezése után a nincsenek néhány további lépést kell megtennie, hogy ez a tanúsítvány használatának megkezdéséhez szüksége. 

Válassza ki a tanúsítványt a [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a **Tanúsítványkonfiguráció** > **1. lépés: Store**.

![készen áll a KV tárolása ábrázoló kép beszúrása](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[A Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) Azure-szolgáltatás, amely segít a felhőalapú alkalmazások és szolgáltatások által használt kriptográfiai kulcsok és titkos. Az App Service-tanúsítványok számára választott storage.

Az a **Key Vault-állapot** kattintson **Key Vault-tárház** hozzon létre egy új tárolót, vagy válasszon egy meglévő tároló. Ha létrehoz egy új tárolót, használja az alábbi táblázat segítségével a tároló konfigurálása, majd kattintson a Létrehozás gombra. Tekintse meg az ugyanazon előfizetésben és erőforráscsoportban csoporton belül az új kulcstartó létrehozása.

| Beállítás | Leírás |
|-|-|
| Name (Név) | Egy egyedi nevet, amely alfanumerikus karaktereket és kötőjeleket tartalmazhat. |
| Erőforráscsoport | Egy javaslat, válassza ki ugyanazt az erőforráscsoportot, az App Service-tanúsítvány. |
| Hely | Válassza ki ugyanazt a helyet, az App Service-alkalmazást. |
| Tarifacsomag | További információ: [Azure Key Vault díjszabását](https://azure.microsoft.com/pricing/details/key-vault/). |
| Hozzáférési szabályzatok| Határozza meg az alkalmazások és az engedélyezett hozzáférést a tároló-erőforrások. Beállíthatja, később ismertető [több alkalmazások hozzáférést key vault](../key-vault/key-vault-group-permissions-for-apps.md). |
| Virtuális hálózati hozzáférés | Tároló hozzáférés korlátozása az egyes Azure virtuális hálózatokhoz. Beállíthatja, később ismertető [konfigurálása az Azure Key Vault tűzfalak és virtuális hálózatok](../key-vault/key-vault-network-security.md) |

Miután kiválasztotta a tárolót, zárja be a **Key Vault-tárház** lapot. A **Store** beállítás sikeres végrehajtás esetén egy zöld pipának kell megjelennie. Ne zárja be az oldal a következő lépéshez.

## <a name="verify-domain-ownership"></a>Tartomány tulajdonjogának ellenőrzése

Az azonos **Tanúsítványkonfiguráció** kattintson az oldal az előző lépésben használt **2. lépés: Győződjön meg arról**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Válassza ki **App Service-ellenőrzés**. Mivel a webalkalmazás már leképezve a tartomány (lásd: [Előfeltételek](#prerequisites)), már ellenőrizve van. Ehhez egyszerűen kattintson **ellenőrizze** Ez a lépés befejezéséhez. Kattintson a **frissítése** gombra, amíg az üzenet **tanúsítványt a tartomány már ellenőrizve** jelenik meg.

> [!NOTE]
> Tartomány-ellenőrzési módszerek négy típusa támogatott: 
> 
> - **App Service-ben** – a legalkalmasabb lehetőség, amikor a tartomány már hozzá van rendelve egy App Service-alkalmazás ugyanabban az előfizetésben. Azt a tényt, hogy az App Service-alkalmazás már ellenőrizte a tartomány tulajdonjogának kihasználja.
> - **Tartomány** -ellenőrzése egy [vásárolt az Azure App Service-tartomány](custom-dns-web-site-buydomains-web-app.md). Az Azure automatikusan hozzáadja az ellenőrzési txt típusú rekord, és elvégzi a folyamatot.
> - **Mail** – a tartományi rendszergazda e-mailt küld a tartomány ellenőrzéséhez. Útmutatás: Ha a beállítást választja.
> - **Manuális** – ellenőrizze a tartományt vagy a HTML-lapok használatával (**Standard** tanúsítvány csak) vagy egy DNS txt típusú rekordot. Útmutatás: Ha a beállítást választja.

## <a name="bind-certificate-to-app"></a>Tanúsítvány kötése az alkalmazáshoz

Az a  **[az Azure portal](https://portal.azure.com/)**, a bal oldali menüben válassza ki a **App Services** > **\<your_ alkalmazás >**.

Az alkalmazás a bal oldali navigációs sávján válassza **SSL-beállítások** > **privát tanúsítványokat (.pfx)** > **App Service-tanúsítvány importálása**.

![Helyezze be a tanúsítvány importálása képe](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Válassza ki a most megvásárolt tanúsítványt.

Most, hogy a tanúsítvány importálása, kell kötni egy leképezett tartománynevet az alkalmazásban. Válassza ki **kötések** > **SSL-kötés hozzáadása**. 

![Helyezze be a tanúsítvány importálása képe](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Használja az alábbi táblázat segítségével konfigurálja a kötés a **SSL-kötések** párbeszédpanelen, majd kattintson a **kötésének hozzáadása**.

| Beállítás | Leírás |
|-|-|
| Gazdanév | Az SSL-kötés hozzáadása a tartomány neve. |
| Privát tanúsítvány ujjlenyomata | Kösse a tanúsítványt. |
| SSL-típus | <ul><li>**SNI SSL** – több SNI-alapú SSL-kötés adható hozzá. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).</li><li>**IP-based SSL** (IP-alapú SSL) – Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. A kötés után konfigurálni, kövesse a [a rekord újbóli leképezése az IP SSL-hez](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>HTTPS-hozzáférés ellenőrzése

Látogasson el az alkalmazás használatával `HTTPS://<domain_name>` helyett `HTTP://<domain_name>` , győződjön meg arról, hogy a tanúsítvány megfelelően van konfigurálva.

## <a name="rekey-and-sync-certificate"></a>Újrakulcsolás és szinkronizálás tanúsítvány

Ha átállítására lenne szükség a tanúsítvány újrakulcsolása, válassza ki a tanúsítványt a [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd válassza a **Újrakulcsolás és szinkronizálás** a bal oldali navigációs sávon.

Kattintson a **Újrakulcsolása** gombra kattintva elindíthatja a folyamatot. Ez a folyamat 1 – 10 percet is igénybe vehet.

![az SSL kulcsismétlési kép beszúrása](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A tanúsítvány újrakulcsolása összesíti a tanúsítványt, és a egy új tanúsítvány kibocsátása a hitelesítésszolgáltatótól.

## <a name="renew-certificate"></a>Tanúsítvány megújítása

Kapcsolja be a tanúsítvány automatikus megújítását, bármikor, jelölje be a tanúsítványt a [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a **az automatikus megújítás beállításai** a bal oldali navigációs. 

Válassza ki **a** kattintson **mentése**. Tanúsítványok automatikus megújítása 60 nappal a lejárat előtt, ha engedélyezve van az automatikus megújítását megkezdéséhez.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Ehelyett manuálisan újítsa meg a tanúsítványt, kattintson a **manuális megújítás**. Manuálisan megújítani a tanúsítványt a lejárta előtt 60 nappal kérheti.

> [!NOTE]
> A megújított tanúsítvány nem automatikusan kötött alkalmazását, akár manuálisan megújítani automatikusan megújul. Kösse az alkalmazáshoz, tekintse meg [tanúsítványok megújítása](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>További erőforrások

* [HTTPS kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben](app-service-web-ssl-cert-load.md)
* [GYAKORI KÉRDÉSEK: App Service-tanúsítványok](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
