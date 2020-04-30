---
title: Oktatóanyag – meglévő egyéni tartomány leképezése az Azure Spring Cloud-ra
description: Meglévő egyéni Distributed Name Service-(DNS-) név leképezése az Azure Spring Cloud-ra
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176993"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Meglévő egyéni tartomány leképezése az Azure Spring Cloud-ra
Az elosztott név szolgáltatás (DNS) a hálózati csomópontok nevének hálózaton belüli tárolására szolgáló módszer. Ez az oktatóanyag egy tartományt, például a www.contoso.com-t egy CNAME-rekord használatával képezi le. Az egyéni tartományt egy tanúsítvánnyal védi, és bemutatja, hogyan lehet kényszeríteni a Transport Layer Security (TLS), más néven SSL (SSL) használatát. 

A tanúsítványok titkosítják a webes forgalmat. Ezek a TLS/SSL-tanúsítványok a Azure Key Vault tárolhatók. 

## <a name="prerequisites"></a>Előfeltételek
* Azure Spring Cloud üzembe helyezett alkalmazás (lásd: gyors útmutató [: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart-launch-app-portal.md), vagy meglévő alkalmazás használata).
* Egy tartománynév, amely hozzáféréssel rendelkezik a tartományi szolgáltató DNS-beállításjegyzékéhez, például a GoDaddyhez.
* Külső szolgáltatótól származó privát tanúsítvány. A tanúsítványnak meg kell egyeznie a tartománnyal.
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) üzembe helyezett példánya

## <a name="import-certificate"></a>Tanúsítvány importálása 
A tanúsítvány importálására vonatkozó eljáráshoz a PEM-vagy PFX-kódolású fájlnak lemezen kell lennie, és rendelkeznie kell a titkos kulccsal. 

A tanúsítvány feltöltése a Key vaultba:
1. Nyissa meg a Key Vault-példányt.
1. A bal oldali navigációs ablaktáblán kattintson a **tanúsítványok**elemre.
1. A felső menüben kattintson a **készítés/importálás**elemre.
1. A **tanúsítvány létrehozása** párbeszédpanel **tanúsítvány létrehozása módjában**válassza a lehetőséget `Import`.
1. A **tanúsítványfájl feltöltése**területen navigáljon a tanúsítvány helyére, és jelölje ki.
1. A **jelszó**területen adja meg a tanúsítvány titkos kulcsát.
1. Kattintson a **Létrehozás**gombra.

![1. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-a.png)

Tanúsítvány importálása az Azure Spring Cloud-ba:
1. Lépjen a szolgáltatási példányra. 
1. Az alkalmazás bal oldali navigációs paneljén válassza a **TLS/SSL-beállítások**lehetőséget.
1. Ezután kattintson **Key Vault tanúsítvány importálása**elemre.

![Tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate.png)

Miután sikeresen importálta a tanúsítványt, megjelenik a **titkos kulcsokra vonatkozó tanúsítványok**listáján.

![Titkos kulcs tanúsítványa](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
> Ha egy egyéni tartományt ezzel a tanúsítvánnyal kíván biztonságossá tenni, akkor továbbra is egy adott tartományhoz kell kötnie a tanúsítványt. Kövesse a jelen dokumentumban ismertetett lépéseket az **SSL-kötés hozzáadása**című fejezetben.

## <a name="add-custom-domain"></a>Egyéni tartomány hozzáadása
CNAME rekord használatával képezhető le egyéni DNS-nevet az Azure Spring Cloud-ra. 

> [!NOTE] 
> Az A rekord nem támogatott. 

### <a name="create-the-cname-record"></a>A CNAME rekord létrehozása
Nyissa meg a DNS-szolgáltatót, és adjon hozzá egy CNAME-rekordot a tartománynak a <service_name>. azuremicroservices.io való leképezéséhez. Itt <service_name> az Azure Spring Cloud-példány neve. A helyettesítő karakteres tartományt és altartományt is támogatjuk. A CNAME hozzáadása után a DNS-rekordok lap a következő példához hasonló lesz: 

![DNS-rekordok oldala](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Egyéni tartomány leképezése az Azure Spring Cloud-alkalmazásra
Ha nem rendelkezik alkalmazással az Azure Spring Cloud-ban, kövesse a rövid útmutató [: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master)című témakör utasításait.

Ugrás az alkalmazás oldalra.

1. Válassza az **egyéni tartomány**lehetőséget.
2. Ezután **adja hozzá az egyéni tartományt**. 

![Egyéni tartomány](./media/custom-dns-tutorial/custom-domain.png)

3. Írja be a teljes tartománynevet, amelyhez hozzáadott egy CNAME rekordot (például www.contoso.com). Győződjön meg arról, hogy az állomásnév bejegyzéstípusa CNAME értékre van beállítva (<service_name>. azuremicroservices.io)
4. Az **Érvényesítés** gombra kattintva engedélyezheti a **Hozzáadás** gombot.
5. Kattintson a **Hozzáadás** parancsra.

![Egyéni tartomány hozzáadása](./media/custom-dns-tutorial/add-custom-domain.png)

Egy alkalmazás több tartománnyal is rendelkezhet, de egy tartomány csak egyetlen alkalmazáshoz képezhető le. Ha sikeresen leképezte az egyéni tartományt az alkalmazáshoz, az egyéni tartomány táblán fog megjelenni.

![Egyéni tartomány tábla](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba érkező HTTPS-kérések hibaüzenetet vagy figyelmeztetést kapnak.

## <a name="add-ssl-binding"></a>SSL-kötés hozzáadása
Az egyéni tartomány táblában válassza az **SSL-kötés hozzáadása** lehetőséget az előző ábrán látható módon.  
1. Válassza ki a **tanúsítványt** , vagy importálja.
1. Kattintson a **Save** (Mentés) gombra.

![SSL-kötés hozzáadása](./media/custom-dns-tutorial/add-ssl-binding.png)

Az SSL-kötés sikeres hozzáadása után a tartomány állapota biztonságos lesz: **kifogástalan**. 

![SSL-kötés hozzáadása](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS kényszerítése
Alapértelmezés szerint bárki megtekintheti az alkalmazást HTTP-n keresztül, de az összes HTTP-kérelmet átirányíthatja a HTTPS-portra.

Az alkalmazás lapjának bal oldali navigációs sávján válassza az **egyéni tartomány**lehetőséget. Ezt követően **csak a https**-t állítsa *igaz*értékre.

![SSL-kötés hozzáadása](./media/custom-dns-tutorial/enforce-http.png)

Ha a művelet befejeződött, navigáljon az alkalmazására mutató HTTPS URL-címek bármelyikéhez. Vegye figyelembe, hogy a HTTP-URL-címek nem működnek.

## <a name="see-also"></a>Lásd még
* [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Tanúsítvány importálása](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [A Spring Cloud-alkalmazás elindítása az Azure CLI használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

