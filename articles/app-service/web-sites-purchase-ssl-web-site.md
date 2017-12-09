---
title: "Vásároljon és SSL-tanúsítvány konfigurálása az Azure App Service |} Microsoft Docs"
description: "Megtudhatja, hogyan vásárolhat egy tanúsítványt az alkalmazás szolgáltatáshoz, majd kösse App Service-alkalmazás"
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
ms.openlocfilehash: 6c0125bf0bd22912a21372b5a7da6846e924e6cd
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>SSL-tanúsítvány vásárlása és konfigurálása saját Azure App Service szolgáltatások számára

Ez az oktatóanyag bemutatja, hogyan tegye biztonságossá webalkalmazását által az SSL-tanúsítvány beszerzése a  **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**, biztonságos helyen tárolja [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis), és társítás egyéni tartományt.

## <a name="step-1---log-in-to-azure"></a>1. lépés – Azure való bejelentkezéshez

Jelentkezzen be az Azure portálon, a http://portal.azure.com

## <a name="step-2---place-an-ssl-certificate-order"></a>2. lépés - az SSL-tanúsítvány megrendelés

Hozzon létre egy új SSL-tanúsítvány rendelés elhelyezhet [App szolgáltatástanúsítvány](https://portal.azure.com/#create/Microsoft.SSL) a a **Azure-portálon**.

![Tanúsítvány létrehozása](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Adjon meg egy rövid **neve** SSL-tanúsítványok a tanúsítványok, és írja be a **tartománynév**

> [!NOTE]
> Ez a lépés nem egy kritikus fontosságú része a beszerzési folyamatának. Győződjön meg arról, hogy ezt a tanúsítványt a védeni kívánt megfelelő gazdagép nevét (egyéni tartomány). **NE** az állomásnév WWW rendelkező hozzáfűzése. 
>

Válassza ki a **előfizetés**, **erőforráscsoport**, és **tanúsítvány-SKU**

> [!WARNING]
> App Service-tanúsítványok csak egyazon előfizetésen belül más alkalmazásszolgáltatások használható.  
>

## <a name="step-3---store-the-certificate-in-azure-key-vault"></a>3. lépés - a tanúsítványt az Azure Key Vault

> [!NOTE]
> [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) Azure szolgáltatás, amely segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos védelme.
>

Az SSL-tanúsítvány a vásárlás befejezése után meg kell nyitnia a [App Service-tanúsítványokkal](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lap.

![Helyezze be a készen áll a KV tárolása képe](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

A tanúsítvány állapota **"Függőben lévő kiállítási"** , néhány további lépést kell végrehajtani, ezzel a tanúsítvánnyal megkezdése előtt.

Kattintson a **Tanúsítványkonfiguráció** belül a tanúsítvány tulajdonságai lapon, majd kattintson a **1. lépés: tárolására** az Azure Key Vault a tanúsítványt tárolni szeretné.

A a **Key Vault állapot** kattintson **Key Vault tárház** választania ezt a tanúsítványt tárolni egy meglévő kulcstároló **vagy hozzon létre új Key Vault** kulcstároló létrehozása azonos előfizetésbe és erőforráscsoportba csoporton belül.

> [!NOTE]
> Az Azure Key Vault tárolja a tanúsítványt a minimális díja van.
> További információkért lásd:  **[Azure Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/)**.
>

Miután kiválasztotta a kulcsot tároló tárház tárolja ezt a tanúsítványt, a **tárolására** beállítást meg kell jelennie a sikeres.

![Helyezze be a tároló sikeres KV képe](./media/app-service-web-purchase-ssl-web-site/KVStoreSuccess.png)

## <a name="step-4---verify-the-domain-ownership"></a>4. lépés - ellenőrizze a tartomány tulajdonosa

Az azonos **Tanúsítványkonfiguráció** oldalra, hogy a 3. lépésben, kattintson a **2. lépés: Ellenőrizze**.

Adja meg az elsődleges tartomány ellenőrzés módját. 

App Service-tanúsítványokkal által támogatott tartományok ellenőrzésének négy típusa van: az App Service, a tartományt, a levelek és a manuális ellenőrzése. További részletek a következő ellenőrzési típusok magyarázata a [szakasz speciális](#advanced).

> [!NOTE]
> **App Service-ellenőrzési** van a legmegfelelőbb beállítást, ha ellenőrizni szeretné a tartomány már le van képezve egy App Service-alkalmazást ugyanahhoz az előfizetéshez. Kihasználja a tényt, hogy az App Service alkalmazás már ellenőrizte a tartomány tulajdonosa.
>

Kattintson a **ellenőrizze** gombra kattintva fejezze be ezt a lépést.

![Helyezze be a tartomány ellenőrzése képe](./media/app-service-web-purchase-ssl-web-site/DomainVerificationRequired.png)

Kattintás után **ellenőrizze**, használja a **frissítése** gombra kattint, amíg a **győződjön meg arról** beállítást meg kell jelennie a sikeres.

![Kép beszúrása a KV sikerességének ellenőrzése](./media/app-service-web-purchase-ssl-web-site/KVVerifySuccess.png)

## <a name="step-5---assign-certificate-to-app-service-app"></a>App Service alkalmazás hozzárendelése tanúsítványt, 5 -. lépés

> [!NOTE]
> Ebben a szakaszban a lépések elvégzése előtt kell társítva van egy egyéni tartománynevet az alkalmazást. További információkért lásd:  **[egy webalkalmazást az egyéni tartománynév beállítása.](app-service-web-tutorial-custom-domain.md)**
>

Az a  **[Azure-portálon](https://portal.azure.com/)**, kattintson a **App Service** lehetőséget a bal oldali a lap.

Kattintson annak az alkalmazásnak a nevére, amelyhez hozzá szeretné rendelni a tanúsítványt.

Az a **beállítások**, kattintson a **SSL-tanúsítványok**.

Kattintson a **App Service-tanúsítvány importálása** , és válassza ki az imént beszerzett tanúsítványt.

![Helyezze be a tanúsítvány importálása képe](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Az a **ssl-kötések** szakaszban kattintson a **felvenni kötéseket**, és a legördülő lista segítségével válassza ki a biztonságos SSL és a tanúsítvány használatára a tartomány nevét. Is kiválaszthat a használ-e  **[kiszolgálónév jelzése (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**  vagy IP-alapú SSL.

![az SSL-kötések kép beszúrása](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

Kattintson a **kötésének hozzáadása** menti a módosításokat, és az SSL engedélyezéséhez.

> [!NOTE]
> Ha a kiválasztott **IP-alapú SSL** és az egyéni tartomány úgy van konfigurálva, az A rekord, a következő további lépéseket kell végrehajtani. Ezek a további részleteket magyarázata a [szakasz speciális](#Advanced).

Ezen a ponton kell tudni látogasson el az alkalmazás használatával `HTTPS://` helyett `HTTP://` ellenőrzése, hogy a tanúsítvány helyesen van konfigurálva.

<!--![insert image of https](./media/app-service-web-purchase-ssl-web-site/Https.png)-->

## <a name="step-6---management-tasks"></a>6 - felügyeleti feladatokat. lépés

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="advanced"></a>Extra szintű

### <a name="verifying-domain-ownership"></a>Tartomány-tulajdonjogának ellenőrzéséről

App service-tanúsítványokkal által támogatott tartományok ellenőrzésének további két típusa van: Mail és a kézi ellenőrzése.

#### <a name="mail-verification"></a>E-mailes ellenőrzés

Megerősítési e-mailt már az E-mail címeket, az egyéni tartomány társított el lett küldve.
Végezze el az e-mailek ellenőrzési lépés, nyissa meg az e-mailt, és kattintson a megerősítési hivatkozást.

![Helyezze be az e-mail ellenőrzése képe](./media/app-service-web-purchase-ssl-web-site/KVVerifyEmailSuccess.png)

Ha újra kell küldenie a megerősítési e-mailt, kattintson a **E-mail újraküldése** gombra.

#### <a name="domain-verification"></a>Tartomány ellenőrzése

Válassza ezt a beállítást csak a [egy App Service-tartomány, az Azure-ból vásárolta.](custom-dns-web-site-buydomains-web-app.md). Azure automatikusan hozzáadja az ellenőrzés TXT-rekord, és befejezi a folyamatot.

#### <a name="manual-verification"></a>Manuális ellenőrzés

> [!IMPORTANT]
> HTML-weblap ellenőrzése (csak a Standard tanúsítvány Termékváltozat működik)
>

1. Hozzon létre egy HTML fájlt **"starfield.html"**

1. Tartalom ennek a fájlnak kell lennie a tartomány ellenőrző jogkivonat pontos nevét. (Átmásolhatja a jogkivonatot a tartomány ellenőrzése lapon)

1. Ez a fájl a webkiszolgálón, a tartomány gyökerében feltöltése`/.well-known/pki-validation/starfield.html`

1. Kattintson a **frissítése** ellenőrzés után a tanúsítvány állapotának frissítése. Az ellenőrzés befejezéséhez néhány percig is eltarthat.

> [!TIP]
> Ellenőrizze a terminál használatával `curl -G http://<domain>/.well-known/pki-validation/starfield.html` a válasz tartalmaznia kell a `<verification-token>`.

#### <a name="dns-txt-record-verification"></a>DNS TXT rekord ellenőrzése

1. A DNS-kezelő használatával hozzon létre egy TXT rekordot a a `@` altartomány értékkel egyenlőnek a tartomány ellenőrző jogkivonat.
1. Kattintson a **"Frissítés"** ellenőrzés után a tanúsítvány állapotának frissítése.

> [!TIP]
> A TXT-rekord létrehozásához szükséges `@.<domain>` értékű `<verification-token>`.

### <a name="assign-certificate-to-app-service-app"></a>App Service alkalmazás-tanúsítványt hozzárendeli

Ha a kiválasztott **IP-alapú SSL** és az egyéni tartomány úgy van konfigurálva, az A rekord, a következő további lépéseket kell végrehajtani:

Beállítása után egy IP-alapú SSL-kötést, egy dedikált IP-cím hozzá van rendelve az alkalmazáshoz. Az IP-cím található a **egyéni tartomány** jobbra fent a az alkalmazás beállításai lapon a **Hostnames** szakasz. Szerepel **külső IP-cím**

![Helyezze be az IP-SSL képe](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

Az IP-cím nem egyezik a virtuális IP-cím a tartomány az A rekord konfigurálása korábban használt. Ha használatára van konfigurálva SNI SSL-alapú vagy nem SSL használatára vannak konfigurálva, a bejegyzés nincs cím szerepel.

A regisztrációs által biztosított eszközöket használja, módosítsa az egyéni tartománynevet az IP-címre az előző lépésben rekordjában.

## <a name="rekey-and-sync-the-certificate"></a>Kulcsismétlés és a tanúsítványának szinkronizálása

Ha bármikor át kívánja a tanúsítvány kulcsismétlés, válassza ki a **kulcs újbóli létrehozása és a szinkronizálás** parancsát a **tanúsítvány tulajdonságai** lap.

Kattintson a **kulcsismétlés** gombra kattintva indítani a folyamatot. Ez az eljárás 1 – 10 percet is igénybe vehet.

![Helyezze be a kulcsismétlés SSL képe](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A tanúsítvány újbóli visszaállítja a tanúsítványt a hitelesítésszolgáltatótól származó kibocsátott új tanúsítványt.

<a name="notrenewed"></a>
## <a name="why-is-my-ssl-certificate-not-auto-renewed"></a>Miért érdemes az SSL-tanúsítvány nem automatikus-megújult?

Ha az SSL-tanúsítvány automatikus megújítási van konfigurálva, de nem automatikusan megújítani, előfordulhat, hogy a függőben lévő tartományok ellenőrzésének. Vegye figyelembe a következőket: 

- GoDaddy, az App Service-tanúsítványokat hoz létre, amely a tartományok ellenőrzésének három évente egyszer igényel. A tartományi rendszergazda három évente egyszer kap egy e-mailt a tartomány ellenőrzése. Nem sikerült ellenőrizni az e-mailben, vagy ellenőrizze a tartományt a megakadályozza, hogy az App Service-tanúsítvány megújítása automatikusan folyamatban. 
- Március 31 2017 előtt kiadott összes App Service-tanúsítványt igényel reverification tartomány a következő megújítási időpontjában, (még akkor is, ha az automatikus tartománymegújítási engedélyezve van a tanúsítvány). Ez a GoDaddy házirend változás eredménye. Nézze meg leveleit, és a tartomány egyszeri igazolásához automatikus az App Service-tanúsítvány megújítási a folytatáshoz. 

## <a name="more-resources"></a>További erőforrások

* [Egy SSL-tanúsítvány használható az alkalmazás kódjában, az Azure App Service-ben](app-service-web-ssl-cert-load.md)
* [– Gyakori kérdések: App Service-tanúsítványok](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)