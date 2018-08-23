---
title: Vásárlása és konfigurálása az Azure App Service SSL-tanúsítvány |} A Microsoft Docs
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
ms.date: 12/01/2017
ms.author: apurvajo;cephalin
ms.openlocfilehash: 21dde5b0199345e9e8faffc6feac4854a3aa7350
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054299"
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>SSL-tanúsítvány vásárlása és konfigurálása saját Azure App Service szolgáltatások számára

Ez az oktatóanyag bemutatja, hogyan teheti biztonságossá webalkalmazását egy SSL-tanúsítványt vásárol az  **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, biztonságosan tárolja a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), és az egyéni tartomány társítása azt.

## <a name="step-1---sign-in-to-azure"></a>1. lépés – bejelentkezés az Azure-bA

Jelentkezzen be az Azure Portalra http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>2. lépés – SSL-tanúsítvány rendelés

Hozzon létre egy új SSL-tanúsítvány rendelés elhelyezhet [App Service-tanúsítvány](https://portal.azure.com/#create/Microsoft.SSL) a a **az Azure portal**.

![Tanúsítvány létrehozása](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Adjon meg egy rövid **neve** az SSL-tanúsítvány, és adja meg a **tartománynév**

> [!NOTE]
> Ebben a lépésben az egyik legfontosabb részeit a vásárlási folyamat. Ellenőrizze, hogy adja meg a helyes állomásnevet (egyéni tartomány), amely ezt a tanúsítványt a védeni kívánt. **NE** illesztenie a WWW-állomás neve. 
>

Válassza ki a **előfizetés**, **erőforráscsoport**, és **tanúsítvány-Termékváltozat**

> [!TIP]
> App Service-tanúsítványok bármilyen Azure és az Azure Services használható, és nem korlátozódik az App Services. Ehhez szüksége egy App Service-tanúsítvány, hogy ezzel bárhol felhasználható helyi PFX másolatának létrehozásához. További információkért olvassa el [létrehozása egy App Service-tanúsítvány PFX helyi példányának](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>3. lépés – a tanúsítvány Store az Azure Key Vaultban

> [!NOTE]
> [A Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) Azure-szolgáltatás, amely segít a felhőalapú alkalmazások és szolgáltatások által használt kriptográfiai kulcsok és titkos.
>

Az SSL-tanúsítvány megvásárlása után meg kell nyitnia a [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapot.

![készen áll a KV tárolása ábrázoló kép beszúrása](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

A tanúsítvány állapota **"Kibocsátás függőben"** , néhány további lépést kell megtennie, hogy ez a tanúsítvány használatának megkezdéséhez szüksége.

Kattintson a **Tanúsítványkonfiguráció** belül a tanúsítvány tulajdonságai lapon, majd kattintson a **1. lépés: Store** tárolja ezt a tanúsítványt az Azure Key Vaultban.

Az a **Key Vault-állapot** kattintson **Key Vault-tárház** választhat egy meglévő ezt a tanúsítványt tároló kulcstartót **vagy új kulcstartó létrehozása** új kulcstartó létrehozása azonos előfizetésben és erőforráscsoportban csoporton belül.

> [!NOTE]
> Az Azure Key Vault rendelkezik a minimális díjat a tanúsítvány tárolásához.
> További információkért lásd:  **[Azure Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Miután kiválasztotta a Key Vault-tárházat tárolja ezt a tanúsítványt, a **Store** beállítást meg kell jelennie a sikeres.

![KV siker store kép beszúrása](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>4. lépés – a tartomány tulajdonjogának ellenőrzése

Az azonos **Tanúsítványkonfiguráció** kattintson a lap a 3. lépésben használt **2. lépés: Győződjön meg arról**.

Válassza ki a kívánt tartomány-ellenőrzési módszert. 

App Service-tanúsítványok által támogatott tartomány-ellenőrzés négy típusa van: App Service-ben, a tartomány és a manuális ellenőrzést. Ezek a hitelesítési típusok mutatjuk be a további részleteket a [szakasz speciális](#advanced).

> [!NOTE]
> **App Service-ellenőrzés** a legalkalmasabb lehetőség, ha szeretné ellenőrizni a tartományt már hozzá van rendelve egy App Service-alkalmazás ugyanabban az előfizetésben. Azt a tényt, hogy az App Service-alkalmazás már ellenőrizte a tartomány tulajdonjogának kihasználja.
>

Kattintson a **ellenőrizze** gombra kattintva fejezze be ezt a lépést.

![Helyezze be a tartomány-ellenőrzés képe](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Kattintás után **győződjön meg arról**, használja a **frissítése** gombra, amíg a **ellenőrizze** beállítást meg kell jelennie a sikeres.

![INSERT képe a KV sikerességének ellenőrzése](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>5. lépés – tanúsítvány App Service-alkalmazás hozzárendelése

> [!NOTE]
> Ebben a szakaszban ismertetett lépések végrehajtása, mielőtt kell társítva van egy egyéni tartománynevet az alkalmazás. További információkért lásd:  **[webes alkalmazás egy egyéni tartománynév beállítása.](app-service-web-tutorial-custom-domain.md)**
>

Az a  **[az Azure portal](https://portal.azure.com/)**, kattintson a **App Service-ben** lehetőséget a bal oldalon az oldal.

Kattintson annak az alkalmazásnak a nevére, amelyhez hozzá szeretné rendelni a tanúsítványt.

Az a **beállítások**, kattintson a **SSL-beállítások**.

Kattintson a **App Service-tanúsítvány importálása** , és válassza ki a most megvásárolt tanúsítványt.

![Helyezze be a tanúsítvány importálása képe](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Az a **ssl-kötések** szakaszban kattintson a **adhat hozzá kötéseket**, és a legördülő menükben válassza ki a tartomány nevét az SSL és a tanúsítvány használatára. Is kiválaszthat a hogy használható-e **[kiszolgálónév jelzése (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** vagy IP-alapú SSL.

![az SSL-kötések kép beszúrása](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Kattintson a **kötésének hozzáadása** mentse a módosításokat, és az SSL engedélyezéséhez.

> [!NOTE]
> Ha a kiválasztott **IP-alapú SSL** és az egyéni tartomány úgy van konfigurálva, az A rekord, akkor az alábbi kiegészítő lépéseket kell végrehajtania. Ezekről további információt a további részleteket a [szakasz speciális](#Advanced).

Ezen a ponton kell látnia, látogasson el az alkalmazás használatával `HTTPS://` helyett `HTTP://` , győződjön meg arról, hogy a tanúsítvány megfelelően van konfigurálva.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>6. lépés – felügyeleti feladatok

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Extra szintű

### <a name="verifying-domain-ownership"></a>Tartomány tulajdonjogának ellenőrzése

Más App service-tanúsítványok által támogatott tartomány-ellenőrzési két típusa van: tartomány-ellenőrzés és a manuális ellenőrzést.

#### <a name="domain-verification"></a>Tartomány ellenőrzése

Ezt a lehetőséget csak a [vásárolt az Azure App Service-tartományok.](custom-dns-web-site-buydomains-web-app.md). Az Azure automatikusan hozzáadja az ellenőrzési txt típusú rekord, és elvégzi a folyamatot.

#### <a name="manual-verification"></a>Manuális ellenőrzés

> [!IMPORTANT]
> HTML-weblap ellenőrzése (csak a tanúsítványt a Standard Termékváltozat esetében működik)
>

1. Hozzon létre egy HTML-fájlt **"starfield.html"**

1. Tartalom ennek a fájlnak kell lennie a pontos nevét a tartomány-ellenőrzési tokennel. (Másolhatja a jogkivonatot a tartomány-ellenőrzési állapot oldal)

1. Ezt a tartományt üzemeltető webkiszolgáló gyökérmappájában-fájl feltöltése `/.well-known/pki-validation/starfield.html`

1. Kattintson a **frissítése** ellenőrzés befejezése után a tanúsítvány állapotának frissítéséhez. Az ellenőrzés néhány percbe is telhet.

> [!TIP]
> Ellenőrizze a terminál használatával `curl -G http://<domain>/.well-known/pki-validation/starfield.html` a válasznak tartalmaznia kell a `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>DNS txt típusú rekord ellenőrzése

1. A DNS-kezelő használatával hozzon létre egy txt típusú rekordot a a `@` altartomány egyenlő a tartomány-ellenőrzési Token értékét.
1. Kattintson a **"Frissítés"** ellenőrzés befejezése után a tanúsítvány állapotának frissítéséhez.

> [!TIP]
> Hozzon létre egy txt típusú rekordot a kell `@.<domain>` értékkel `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>Tanúsítvány hozzárendelése az App Service-alkalmazás

Ha a kiválasztott **IP-alapú SSL** és az egyéni tartomány úgy van konfigurálva, az A rekord, akkor az alábbi kiegészítő lépéseket kell végrehajtania:

Miután konfigurálta egy IP-cím alapú SSL-kötés, az alkalmazás hozzá van rendelve egy dedikált IP-címet. Annak az IP-címet a a **egyéni tartomány** oldalon az alkalmazás beállításaiban jobbra fent a **állomásnevek** szakaszban. Másiké pedig **külső IP-cím**

![az IP SSL kép beszúrása](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Az IP-cím eltér a virtuális IP-cím, az A rekord a tartomány konfigurálásához használt korábban. Ha használatára vannak konfigurálva az SNI-alapú SSL, vagy nincsenek konfigurálva SSL használatára, nincs cím szerepel a listán a bejegyzéshez tartozó.

A tartománynév regisztrálójánál az által biztosított eszközökkel, módosítsa az egyéni tartománynevet az IP-cím átirányítása az előző lépésben az A rekordhoz.

## <a name="rekey-and-sync-the-certificate"></a>Újrakulcsolás és szinkronizálás a tanúsítvány

Ha átállítására lenne szükség a tanúsítvány újrakulcsolása, válassza ki a **Újrakulcsolás és szinkronizálás** parancsát a **tanúsítvány tulajdonságai** lapot.

Kattintson a **Újrakulcsolása** gombra kattintva elindíthatja a folyamatot. Ez a folyamat 1 – 10 percet is igénybe vehet.

![az SSL kulcsismétlési kép beszúrása](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A tanúsítvány újrakulcsolása összesíti a tanúsítványt, és a egy új tanúsítvány kibocsátása a hitelesítésszolgáltatótól.

## <a name="renew-the-certificate"></a>A tanúsítvány megújítása

A tanúsítvány automatikus megújítás bármikor bekapcsolásához kattintson **az automatikus megújítás beállításai** a tanúsítvány-kezelési oldalán. Válassza ki **a** kattintson **mentése**. Tanúsítványok automatikus megújítása 90 nappal a lejárat előtt, ha engedélyezve van az automatikus megújítását megkezdéséhez.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Ehelyett manuálisan újítsa meg a tanúsítványt, kattintson a **manuális megújítás** helyette. Manuálisan megújítani a tanúsítványt a lejárta előtt 60 nappal kérheti.

> [!NOTE]
> A megújított tanúsítvány nem automatikusan kötött alkalmazását, akár manuálisan megújítani automatikusan megújul. Kösse az alkalmazáshoz, tekintse meg [tanúsítványok megújítása](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

<a name="notrenewed"></a>
## <a name="why-is-my-certificate-not-auto-renewed"></a>Miért érdemes a tanúsítvány nem automatikus – újítja meg?

Ha az SSL-tanúsítvány automatikus megújítása van konfigurálva, de nem automatikusan megújítani, előfordulhat, hogy rendelkezik egy függőben lévő tartomány-ellenőrzés. Vegye figyelembe: 

- GoDaddy, az App Service-tanúsítványokat hoz létre, amely a tartomány-ellenőrzés két évente van szükség. A tartományi rendszergazda három évente egyszer kap egy e-mailt a tartomány ellenőrzéséhez. Ellenőrizze az e-mailben, vagy ellenőrizze a tartomány megakadályozza, hogy az App Service-tanúsítvány automatikus megújítása. 
- A GoDaddy házirend módosítása miatt a 2017. március 1. előtt kiadott összes App Service-tanúsítványok reverification tartomány tovább a megújítás időpontjában megkövetelése a (még akkor is, ha az automatikus megújítás engedélyezve van a tanúsítvány). Tekintse meg az e-mail, és fejezze be az automatikus megújítás az App Service-tanúsítvány továbbra is a egyszeri tartomány-ellenőrzésére. 

## <a name="more-resources"></a>További erőforrások

* [HTTPS kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](app-service-web-tutorial-custom-ssl.md#enforce-tls-1112)
* [SSL-tanúsítvány használata az alkalmazáskódban az Azure App Service-ben](app-service-web-ssl-cert-load.md)
* [– Gyakori kérdések: App Service-tanúsítványok](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
