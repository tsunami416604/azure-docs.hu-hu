---
title: SSL-tanúsítvány vásárlása és konfigurálása az Azure-ban – App Service | Microsoft Docs
description: Megtudhatja, hogyan vásárolhat egy App Service tanúsítványt, és hogyan köthető a App Service alkalmazáshoz
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 7675a22b4b2d8b13524f06f45d6bb805c1e2fad1
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019137"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>SSL-tanúsítvány vásárlása és konfigurálása Azure App Servicehoz

Ebből az oktatóanyagból megtudhatja, hogyan védheti meg [](https://docs.microsoft.com/azure/azure-functions/) [app Service alkalmazásait](https://docs.microsoft.com/azure/app-service/) vagy funkcióit app Service-tanúsítvány létrehozásával (megvásárlásával) [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) , majd egy app Service alkalmazáshoz kötheti.

> [!TIP]
> App Service tanúsítványokat bármely Azure-beli vagy nem Azure-szolgáltatáshoz használhat, és nem korlátozható a App Services. Ehhez létre kell hoznia egy App Service tanúsítvány helyi PFX-példányát, amelyet bárhol igénybe vehet. További információ: [app Service-tanúsítvány helyi pfx-példányának létrehozása](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Előfeltételek

A következő útmutató követése:

- [Létre kell hoznia egy App Service-alkalmazást.](/azure/app-service/)
- [Tartománynév hozzárendelése az alkalmazáshoz](app-service-web-tutorial-custom-domain.md) , illetve [Az Azure-beli vásárlás és konfigurálás](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>A tanúsítvány megrendelésének indítása

A <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">app Service-tanúsítvány létrehozás lapon</a>indítson el egy app Service-tanúsítványt.

![Tanúsítvány létrehozása](./media/app-service-web-purchase-ssl-web-site/createssl.png)

A következő táblázat segítséget nyújt a tanúsítvány konfigurálásában. Ha végzett, kattintson a **Létrehozás** gombra.

| Beállítás | Leírás |
|-|-|
| Name (Név) | A App Service tanúsítvány rövid neve. |
| Csupasz tartománynevet tartalmazó állomásnév | Itt adhatja meg a legfelső szintű tartományt. A kiállított tanúsítvány a legfelső szintű tartományt és az `www` altartományt is védi. A kiállított tanúsítványban a köznapi név mező tartalmazza a gyökértartomány tartományát, a tulajdonos alternatív neve mező pedig a `www` tartományt tartalmazza. Csak az altartományok védelméhez adja meg az altartomány teljes tartománynevét (például `mysubdomain.contoso.com`:).|
| Subscription | Az adatközpont, ahol a webalkalmazást üzemeltetik. |
| Resource group | A tanúsítványt tartalmazó erőforráscsoport. Használhat új erőforráscsoportot, vagy kiválaszthatja ugyanazt az erőforráscsoportot, mint a App Service alkalmazás, például:. |
| Tanúsítványváltozat | Meghatározza a létrehozandó tanúsítvány típusát, legyen az egy standard tanúsítvány vagy egy [helyettesítő tanúsítvány](https://wikipedia.org/wiki/Wildcard_certificate). |
| Jogi feltételek | Ide kattintva erősítse meg, hogy elfogadja a jogi feltételeket. A tanúsítványok a GoDaddyből szerezhetők be. |

## <a name="store-in-azure-key-vault"></a>Tárolás Azure Key Vault

A tanúsítvány megvásárlásának befejeződése után még néhány lépést el kell végeznie a tanúsítvány használatának megkezdése előtt. 

Válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a **tanúsítvány-konfiguráció** > **1. lépés: Tároló**.

![az áruházba való használatra kész kép beszúrása KV-ban](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

A [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) egy Azure-szolgáltatás, amely segít megvédeni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. Ez a App Service-tanúsítványok számára választható tároló.

A **Key Vault állapota** lapon kattintson a **Key Vault adattár** elemre egy új tároló létrehozásához, vagy válasszon egy meglévő tárat. Ha úgy dönt, hogy létrehoz egy új tárat, a következő táblázat segítségével konfigurálja a tárolót, és kattintson a Létrehozás gombra. További információ: új Key Vault létrehozása ugyanazon az előfizetésen belül és az erőforráscsoporthoz.

| Beállítás | Leírás |
|-|-|
| Name (Név) | Egy egyedi név, amely alfanumerikus karaktereket és kötőjeleket tartalmaz. |
| Resource group | Javaslatként válassza ki ugyanazt az erőforráscsoportot, mint a App Service-tanúsítványt. |
| Location | Válassza ki ugyanazt a helyet, mint a App Service alkalmazás. |
| Tarifacsomag | További információ: [Azure Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/). |
| Hozzáférési szabályzatok| Meghatározza az alkalmazásokat és az engedélyezett hozzáférést a tároló erőforrásaihoz. Később is konfigurálhatja, ha a lépéseket követve [számos alkalmazás hozzáférést biztosít a kulcstartóhoz](../key-vault/key-vault-group-permissions-for-apps.md). |
| Virtuális hálózati hozzáférés | A tár bizonyos Azure-beli virtuális hálózatokhoz való hozzáférésének korlátozása. Később is konfigurálhatja, a [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](../key-vault/key-vault-network-security.md) című témakör lépéseit követve. |

Miután kiválasztotta a tárolót, zárjuk be a **Key Vault adattár** lapot. Az **áruházi** beállításnak a sikerhez zöld pipa jelet kell mutatnia. Tartsa nyitva a lapot a következő lépéshez.

## <a name="verify-domain-ownership"></a>Tartomány tulajdonjogának ellenőrzése

Az utolsó lépésben használt **tanúsítvány-konfiguráció** lapon kattintson **a 2. lépés: Ellenőrizze**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Válassza ki **app Service ellenőrzést**. Mivel már leképezte a tartományt a webalkalmazásra (lásd: [Előfeltételek](#prerequisites)), már ellenőrizve van. Ezt a lépést csak az **ellenőrzés** gombra kattintva fejezheti be. Kattintson a **refresh (frissítés** ) gombra, amíg az üzenet **tanúsítványa nem ellenőrzött** .

> [!NOTE]
> A tartomány-ellenőrzési módszerek négyféle típusa támogatott: 
> 
> - **App Service** – a legkényelmesebb lehetőség, ha a tartomány már le van képezve egy app Service alkalmazásra ugyanabban az előfizetésben. Kihasználja azt a tényt, hogy a App Service alkalmazás már ellenőrizte a tartomány tulajdonjogát.
> - **Tartomány** – az [Azure-ból vásárolt app Service tartomány](manage-custom-dns-buy-domain.md)ellenőrzése. Az Azure automatikusan hozzáadja az ellenőrző TXT-rekordot az Ön számára, és befejezi a folyamatot.
> - E-mail – ellenőrizze a tartományt úgy, hogy e-mailt küld a tartományi rendszergazdának. A beállítás kiválasztásakor a rendszer útmutatást ad.
> - **Manuális** – ellenőrizze a TARTOMÁNYT egy HTML-oldal használatával (csak**normál** tanúsítvány esetén) vagy egy DNS TXT-rekorddal. A beállítás kiválasztásakor a rendszer útmutatást ad.

## <a name="bind-certificate-to-app"></a>Tanúsítvány kötése az alkalmazáshoz

A **[Azure Portal](https://portal.azure.com/)** bal oldali menüjében válassza a **app Services** >  **\<your_ alkalmazás >** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza az **SSL** >  **-Beállítások privát tanúsítványok (. pfx)**  > **app Service-tanúsítvány importálás**lehetőséget.

![Importálási tanúsítvány képének beszúrása](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Válassza ki az imént megvásárolt tanúsítványt.

Most, hogy importálta a tanúsítványt, hozzá kell kötnie egy hozzárendelt tartománynévhez az alkalmazásban. Válassza > a kötések**SSL-kötés hozzáadása**elemet. 

![Importálási tanúsítvány képének beszúrása](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

A következő táblázat segítségével konfigurálhatja a kötést az SSL- **kötések** párbeszédpanelen, majd kattintson a **kötés hozzáadása**gombra.

| Beállítás | Leírás |
|-|-|
| Állomásnév | A tartománynév, amelyhez SSL-kötést kell hozzáadni. |
| Privát tanúsítvány ujjlenyomata | A kötni kívánt tanúsítvány. |
| SSL-típus | <ul><li>**SNI SSL** – a rendszer több SNI-alapú SSL-kötést is hozzáadhat. Ez a beállítás lehetővé teszi, hogy több SSL-tanúsítvány biztosítson védelmet több tartomány számára ugyanazon az IP-címen. A legtöbb modern böngésző (beleértve az Internet Explorert, a Chrome-ot, a Firefox-ot és az Operát) támogatja az SNI-t (átfogóbb böngészőtámogatási információkat a [Kiszolgálónév jelzése](https://wikipedia.org/wiki/Server_Name_Indication) című szakaszban talál).</li><li>**IP-based SSL** (IP-alapú SSL) – Csak egy IP-alapú SSL-kötés adható hozzá. Ez a beállítás csak egy SSL-tanúsítványnak engedélyezi egy dedikált nyilvános IP-cím védelmét. Miután konfigurálta a kötést, kövesse a következő témakörben ismertetett lépéseket: [IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>HTTPS-hozzáférés ellenőrzése

A tanúsítvány megfelelő konfigurálásának ellenőrzéséhez használja `HTTPS://<domain_name>` az alkalmazást a `HTTP://<domain_name>` helyett.

## <a name="rekey-certificate"></a>Tanúsítvány újrakulcsolása

Ha úgy gondolja, hogy a tanúsítvány titkos kulcsa sérült, visszaállíthatja a tanúsítványt. Válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd a bal oldali navigációs sávon válassza a **regenerálás és szinkronizálás** lehetőséget.

A folyamat elindításához kattintson a **generálás** gombra. Ez a folyamat 1-10 percet is igénybe vehet.

![az újragenerálási SSL-Kép beszúrása](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A tanúsítvány újraírásával a tanúsítvány egy új, a hitelesítésszolgáltatótól kiállított tanúsítvánnyal rendelkezik.

Az újragenerálási művelet befejezése után kattintson a **szinkronizálás**elemre. A szinkronizálási művelet automatikusan frissíti a tanúsítványhoz tartozó állomásnév-kötéseket App Service anélkül, hogy leállást okozna az alkalmazásai számára.

> [!NOTE]
> Ha nem kattint a **szinkronizálás**gombra, a app Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

## <a name="renew-certificate"></a>Tanúsítvány megújítása

Ha bármikor be szeretné kapcsolni a tanúsítvány automatikus megújítását, válassza ki a tanúsítványt a [app Service tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd a bal oldali navigációs menüben kattintson a **beállítások automatikus** megújítása elemre.

Válassza **a** be lehetőséget, majd kattintson a **Mentés**gombra. A tanúsítványok automatikusan megújítják a 60 nappal a lejárat előtt, ha az automatikus megújítás be van kapcsolva.

![tanúsítvány automatikus megújítása](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Ha ehelyett manuálisan szeretné megújítani a tanúsítványt, kattintson a **manuális megújítás**elemre. A tanúsítvány lejárata előtt manuálisan megújíthatja a tanúsítványt 60 nappal.

Miután befejeződött a megújítási művelet, kattintson a **szinkronizálás**elemre. A szinkronizálási művelet automatikusan frissíti a tanúsítványhoz tartozó állomásnév-kötéseket App Service anélkül, hogy leállást okozna az alkalmazásai számára.

> [!NOTE]
> Ha nem kattint a **szinkronizálás**gombra, a app Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>További források

* [HTTPS kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [A TLS 1.1/1.2 betartatása](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Használjon SSL-tanúsítványt az alkalmazás kódjában Azure App Service](app-service-web-ssl-cert-load.md)
* [– GYAKORI KÉRDÉSEK Tanúsítványok App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
