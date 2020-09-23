---
title: Oktatóanyag – meglévő egyéni tartomány leképezése az Azure Spring Cloud-ra
description: Meglévő egyéni Distributed Name Service-(DNS-) név leképezése az Azure Spring Cloud-ra
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cd10421ddcf752625b8040e1afa4e7b15f142ce2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885690"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Meglévő egyéni tartomány leképezése az Azure Spring Cloud-ra

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

A tartománynév-szolgáltatás (DNS) a hálózati csomópontok nevének hálózaton belüli tárolására szolgáló módszer. Ez az oktatóanyag egy tartományt, például a www.contoso.com-t egy CNAME-rekord használatával képezi le. Az egyéni tartományt egy tanúsítvánnyal védi, és bemutatja, hogyan lehet kényszeríteni a Transport Layer Security (TLS), más néven SSL (SSL) használatát. 

A tanúsítványok titkosítják a webes forgalmat. Ezek a TLS/SSL-tanúsítványok a Azure Key Vault tárolhatók. 

## <a name="prerequisites"></a>Előfeltételek
* Azure Spring Cloud üzembe helyezett alkalmazás (lásd: gyors útmutató [: meglévő Azure Spring Cloud-alkalmazás elindítása a Azure Portal használatával](spring-cloud-quickstart.md), vagy meglévő alkalmazás használata).
* Egy tartománynév, amely hozzáféréssel rendelkezik a tartományi szolgáltató DNS-beállításjegyzékéhez, például a GoDaddyhez.
* Privát tanúsítvány (azaz önaláírt tanúsítvány) egy harmadik féltől származó szolgáltatótól. A tanúsítványnak meg kell egyeznie a tartománnyal.
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) üzembe helyezett példánya

## <a name="import-certificate"></a>Tanúsítvány importálása 
A tanúsítvány importálására vonatkozó eljáráshoz a PEM-vagy PFX-kódolású fájlnak lemezen kell lennie, és rendelkeznie kell a titkos kulccsal. 

A tanúsítvány feltöltése a Key vaultba:
1. Nyissa meg a Key Vault-példányt.
1. A bal oldali navigációs ablaktáblán kattintson a **tanúsítványok**elemre.
1. A felső menüben kattintson a **készítés/importálás**elemre.
1. A **tanúsítvány létrehozása** párbeszédpanel **tanúsítvány létrehozása módjában**válassza a lehetőséget `Import` .
1. A **tanúsítványfájl feltöltése**területen navigáljon a tanúsítvány helyére, és jelölje ki.
1. A **jelszó**területen adja meg a tanúsítvány titkos kulcsát.
1. Kattintson a **Létrehozás** lehetőségre.

    ![1. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-a.png)

Azure Spring Cloud-hozzáférés biztosítása a Key vaulthoz a tanúsítvány importálása előtt:
1. Nyissa meg a Key Vault-példányt.
1. A bal oldali navigációs panelen kattintson a **hozzáférési rendőr**elemre.
1. A felső menüben kattintson a **hozzáférési szabályzat hozzáadása**elemre.
1. Töltse ki az adatokat, és kattintson a **Hozzáadás** gombra, majd **mentse** a hozzáférési rendőrséget.

| Titkos engedély | Tanúsítvány engedélye | Rendszerbiztonsági tag kiválasztása |
|--|--|--|
| Beolvasás, Listázás | Beolvasás, Listázás | Azure Spring Cloud domain – felügyelet |

![2. tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate-b.png)

Használhatja az Azure CLI-t is a Key vaulthoz való Azure Spring Cloud-hozzáférés biztosításához.

Az objektumazonosító beszerzése a következő parancs használatával.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

Adja meg az Azure Spring Cloud olvasási hozzáférését a Key vaulthoz, és cserélje le az objektumazonosító értékét a következő parancsra.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Tanúsítvány importálása az Azure Spring Cloud-ba:
1. Lépjen a szolgáltatási példányra. 
1. Az alkalmazás bal oldali navigációs paneljén válassza a **TLS/SSL-beállítások**lehetőséget.
1. Ezután kattintson **Key Vault tanúsítvány importálása**elemre.

    ![Tanúsítvány importálása](./media/custom-dns-tutorial/import-certificate.png)

A tanúsítvány importálásához használhatja az Azure CLI-t is:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Az előző importálási tanúsítvány parancs végrehajtása előtt győződjön meg arról, hogy Azure Spring Cloud-hozzáférést biztosít a Key vaulthoz. Ha még nem tette meg, a következő parancs futtatásával adhatja meg a hozzáférési jogosultságokat.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Miután sikeresen importálta a tanúsítványt, megjelenik a **titkos kulcsokra vonatkozó tanúsítványok**listájában.

![Titkos kulcs tanúsítványa](./media/custom-dns-tutorial/key-certificates.png)

Vagy használhatja az Azure CLI-t is a tanúsítványok listájának megjelenítéséhez:

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
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

Vagy használhatja az Azure CLI-t egyéni tartomány hozzáadására is:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

Egy alkalmazás több tartománnyal is rendelkezhet, de egy tartomány csak egyetlen alkalmazáshoz képezhető le. Ha sikeresen leképezte az egyéni tartományt az alkalmazáshoz, az egyéni tartomány táblán fog megjelenni.

![Egyéni tartomány tábla](./media/custom-dns-tutorial/custom-domain-table.png)

Használhatja az Azure CLI-t is az egyéni tartományok listájának megjelenítéséhez:
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Az egyéni tartomány **nem biztonságos** címkéje azt jelenti, hogy még nincs SSL-tanúsítványhoz kötve. A böngészőből az egyéni tartományba érkező HTTPS-kérések hibaüzenetet vagy figyelmeztetést kapnak.

## <a name="add-ssl-binding"></a>SSL-kötés hozzáadása
Az egyéni tartomány táblában válassza az **SSL-kötés hozzáadása** lehetőséget az előző ábrán látható módon.  
1. Válassza ki a **tanúsítványt** , vagy importálja.
1. Kattintson a **Mentés** gombra.

    ![SSL-kötés hozzáadása 1](./media/custom-dns-tutorial/add-ssl-binding.png)

Vagy az Azure CLI használatával **SSL-kötést is hozzáadhat**:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Az SSL-kötés sikeres hozzáadása után a tartomány állapota biztonságos lesz: **kifogástalan**. 

![SSL-kötés hozzáadása 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>HTTPS kényszerítése
Alapértelmezés szerint bárki megtekintheti az alkalmazást HTTP-n keresztül, de az összes HTTP-kérelmet átirányíthatja a HTTPS-portra.

Az alkalmazás lapjának bal oldali navigációs sávján válassza az **egyéni tartomány**lehetőséget. Ezt követően **csak a https**-t állítsa *igaz*értékre.

![3. SSL-kötés hozzáadása](./media/custom-dns-tutorial/enforce-http.png)

Vagy az Azure CLI-vel kényszerítheti a HTTPS használatát:
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

Ha a művelet befejeződött, navigáljon az alkalmazására mutató HTTPS URL-címek bármelyikéhez. Vegye figyelembe, hogy a HTTP-URL-címek nem működnek.

## <a name="see-also"></a>Lásd még
* [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Tanúsítvány importálása](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [A Spring Cloud-alkalmazás elindítása az Azure CLI használatával](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

