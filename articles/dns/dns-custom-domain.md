---
title: Az Azure DNS integrálása az Azure-erőforrásokkal – Azure DNS
description: Ebből a cikkből megtudhatja, hogyan használhatja az Azure DNS-t az Azure-erőforrások hoz való DNS-szolgáltatáshoz.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: d84a7a908bd3bb5cfb2958a617be437f3b6b154e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266233"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Az Azure DNS használata egyéni tartománybeállítások biztosításához egy Azure-szolgáltatáshoz

Az Azure DNS biztosítja a DNS-t egy egyéni tartomány hoz az egyéni tartományokat támogató vagy teljesen minősített tartománynévvel (FQDN) rendelkező Azure-erőforrásokhoz. Egy példa egy Azure-webalkalmazás, és azt szeretné, hogy a felhasználók\.hozzáférjenek a contoso.com, vagy a www contoso.com teljes tartománynévként. Ez a cikk végigvezeti az Azure-szolgáltatás azure-DNS-sel egyéni tartományok használatára történő konfigurálásán.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy az Azure DNS-t az egyéni tartományához használhassa, először delegálhatja a tartományt az Azure DNS-re. Látogasson el [egy tartomány delegálása az Azure DNS-re,](./dns-delegate-domain-azure-dns.md) és adja meg, hogyan konfigurálhatja a névkiszolgálók delegálásra. Miután a tartomány delegálta az Azure DNS-zónában, konfigurálhatja a szükséges DNS-rekordokat.

Az [Azure Function Apps,](#azure-function-app) [a Nyilvános IP-címek,](#public-ip-address) [az App Service (Web Apps),](#app-service-web-apps) [a Blob storage](#blob-storage)és az Azure CDN hiúságát és egyéni [tartományát](#azure-cdn)konfigurálhatja.

## <a name="azure-function-app"></a>Azure függvényalkalmazás

Egyéni tartomány konfigurálásához az Azure függvényalkalmazásokhoz egy CNAME rekord jön létre, valamint a függvényalkalmazás konfigurációja.
 
Nyissa meg a **Function app alkalmazást,** és válassza ki a függvényalkalmazást. Kattintson **a Platform szolgáltatásai ra,** majd a **Hálózat csoportban** kattintson **az Egyéni tartományok**elemre.

![funkció alkalmazás panel](./media/dns-custom-domain/functionapp.png)

Vegye figyelembe az **egyéni tartományok** panel aktuális URL-címét, ez a cím a létrehozott DNS-rekord aliasaként használatos.

![egyéni tartomány panel](./media/dns-custom-domain/functionshostname.png)

Nyissa meg a DNS-zónát, és kattintson **a + Rekordkészlet gombra.** Töltse ki az alábbi információkat a **Rekordkészlet hozzáadása** panelen, és a létrehozáshoz kattintson az **OK** gombra.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | myfunctionapp        | Ez az érték a tartománynév címkével együtt az egyéni tartománynév teljes tartományneve.        |
|Típus     | CNAME        | A CNAME rekord aliast használ.        |
|Élettartam     | 1        | 1 használt 1 óra        |
|TTL mértékegysége     | Óra        | Az órák időmérésként használatosak         |
|Alias     | adatumfunction.azurewebsites.net        | Ebben a példában az a DNS-név, amelyhez az aliast létrehozza, ez az a adatumfunction.azurewebsites.net DNS-név, amelyet alapértelmezés szerint a függvényalkalmazáshoz ad meg.        |

Lépjen vissza a függvényalkalmazásba, kattintson a **Platform szolgáltatásai**elemre, és a **Hálózat** csoportban kattintson az **Egyéni tartományok**elemre, majd az **Egyéni állomásnevek** csoportban kattintson a + **Hostname hozzáadása gombra.**

Az **Állomásnév hozzáadása** panelen írja be a CNAME rekordot az **állomásnév** szövegmezőbe, és kattintson az **Érvényesítés gombra.** Ha a rekord megtalálható, megjelenik az **Állomásnév hozzáadása** gomb. Az alias hozzáadásához kattintson az **Állomásnév hozzáadása gombra.**

![függvényalkalmazások állomásnév-panelhozzáadása](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Nyilvános IP-cím

Egyéni tartomány konfigurálásához olyan szolgáltatásokhoz, amelyek nyilvános IP-címerőforrást használnak, például az Application Gateway, a Load Balancer, a Cloud Service, az Erőforrás-kezelő virtuális gépek és a klasszikus virtuális gépek, egy A rekordot használnak.

Nyissa meg **a Nyilvános IP-hálózat** > **címét,** jelölje ki a Nyilvános IP-erőforrást, és kattintson a **Konfiguráció gombra.** A megjelenített IP-cím notate.

![nyilvános ip penge](./media/dns-custom-domain/publicip.png)

Nyissa meg a DNS-zónát, és kattintson **a + Rekordkészlet gombra.** Töltse ki az alábbi információkat a **Rekordkészlet hozzáadása** panelen, és a létrehozáshoz kattintson az **OK** gombra.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | webkiszolgáló        | Ez az érték a tartománynév címkével együtt az egyéni tartománynév teljes tartományneve.        |
|Típus     | A        | Használjon A rekordot, mivel az erőforrás IP-cím.        |
|Élettartam     | 1        | 1 használt 1 óra        |
|TTL mértékegysége     | Óra        | Az órák időmérésként használatosak         |
|IP-cím     | `<your ip address>`       | A nyilvános IP-cím.|

![A rekord létrehozása](./media/dns-custom-domain/arecord.png)

Az A rekord létrehozása `nslookup` után futtassa a rekord feloldásának ellenőrzéséhez.

![nyilvános ip dns keresési](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (webalkalmazások)

A következő lépések az alkalmazásszolgáltatás webalkalmazásához egyéni tartomány konfigurálásán keresztül hajtják végre.

Nyissa meg az **App Service-t,** és jelölje ki az egyéni tartománynevet konfiguráló erőforrást, majd kattintson az **Egyéni tartományok gombra.**

Vegye figyelembe az **egyéni tartományok** panel aktuális URL-címét, ez a cím a létrehozott DNS-rekord aliasaként használatos.

![egyéni tartományok panel](./media/dns-custom-domain/url.png)

Nyissa meg a DNS-zónát, és kattintson **a + Rekordkészlet gombra.** Töltse ki az alábbi információkat a **Rekordkészlet hozzáadása** panelen, és a létrehozáshoz kattintson az **OK** gombra.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | webkiszolgáló        | Ez az érték a tartománynév címkével együtt az egyéni tartománynév teljes tartományneve.        |
|Típus     | CNAME        | A CNAME rekord aliast használ. Ha az erőforrás IP-címet használt, akkor a program A rekordot használ.        |
|Élettartam     | 1        | 1 használt 1 óra        |
|TTL mértékegysége     | Óra        | Az órák időmérésként használatosak         |
|Alias     | webserver.azurewebsites.net        | Ebben a példában az a DNS-név, amelyhez az aliast létrehozza, ez az a webserver.azurewebsites.net DNS-név, amelyet alapértelmezés szerint a webalkalmazáshoz ad meg.        |


![CNAME rekord létrehozása](./media/dns-custom-domain/createcnamerecord.png)

Lépjen vissza az egyéni tartománynévhez konfigurált alkalmazásszolgáltatáshoz. Kattintson **az Egyéni tartományok**, majd az **Állomásnevek**elemre. A létrehozott CNAME rekord hozzáadásához kattintson a **+ Hostname hozzáadása gombra**.

![1. ábra](./media/dns-custom-domain/figure1.png)

A folyamat befejezése után futtassa az **nslookup-ot** a névfeloldás ellenőrzéséhez.

![1. ábra](./media/dns-custom-domain/finalnslookup.png)

Ha többet szeretne tudni arról, hogy miként lehet egy egyéni tartományt az App Service szolgáltatáshoz hozzárendelve, olvassa el [a Meglévő egyéni DNS-név hozzárendelése az Azure Web Apps alkalmazáshoz](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)című területet.

Az aktív DNS-név áttelepítése című témakörben [olvashat: Aktív DNS-név áttelepítése az Azure App Service szolgáltatásba című témakörben.](../app-service/manage-custom-dns-migrate-domain.md)

Ha egyéni tartományt kell vásárolnia, az App Service-tartományokról további információért látogasson el [az Azure Web Apps egyéni tartománynév vásárlása](../app-service/manage-custom-dns-buy-domain.md) című lapra.

## <a name="blob-storage"></a>Blob Storage

A következő lépések segítségével konfigurálása egy CNAME rekord egy blob storage-fiók az asverify metódus használatával. Ez a módszer biztosítja, hogy nincs állásidő.

Nyissa meg a **Storage** > **Storage-fiókok at,** jelölje ki a tárfiókot, és kattintson **az Egyéni tartomány**gombra. A 2.

![blob storage egyéni tartomány](./media/dns-custom-domain/blobcustomdomain.png)

Nyissa meg a DNS-zónát, és kattintson **a + Rekordkészlet gombra.** Töltse ki az alábbi információkat a **Rekordkészlet hozzáadása** panelen, és a létrehozáshoz kattintson az **OK** gombra.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | asverify.mystorageaccount        | Ez az érték a tartománynév címkével együtt az egyéni tartománynév teljes tartományneve.        |
|Típus     | CNAME        | A CNAME rekord aliast használ.        |
|Élettartam     | 1        | 1 használt 1 óra        |
|TTL mértékegysége     | Óra        | Az órák időmérésként használatosak         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Az a DNS-név, amelyhez az aliast létrehozza, ebben a példában ez a asverify.adatumfunctiona9ed.blob.core.windows.net DNS-név, amelyet alapértelmezés szerint a tárfiókhoz ad meg.        |

Navigáljon vissza a tárfiókhoz a > **Tárfiókok**elemre kattintva, jelölje ki a tárfiókot, és kattintson az **Egyéni tartomány**elemre. **Storage** Írja be a szövegmezőbe a hitelesítés előtagja nélkül létrehozott aliast, jelölje be a **Közvetett CNAME-érvényesítés használata, majd kattintson a **Mentés gombra.** Ha ez a lépés befejeződött, térjen vissza a DNS-zónába, és hozzon létre egy CNAME rekordot a hitelesítéselőtag nélkül.  Ezt követően a CNAME rekord a cdnverify előtaggal törölhető.

![blob storage egyéni tartomány](./media/dns-custom-domain/indirectvalidate.png)

DNS-feloldás ellenőrzése futással`nslookup`

Ha többet szeretne megtudni arról, hogy miként szeretne egyéni tartományt blob-tárolóvégponthoz hozzáhozzá, látogasson el [a Blob-tároló végpontegyéni tartománynevének konfigurálása](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

A következő lépésekkel a CDN-végpont CNAME rekordjának konfigurálása a cdnverify metódus használatával. Ez a módszer biztosítja, hogy nincs állásidő.

Nyissa meg **a Hálózati** > **CDN-profilok at,** és jelölje ki a CDN-profilt.

Jelölje ki azt a végpontot, amelyen dolgozik, és kattintson **a + Egyéni tartomány**gombra. Vegye figyelembe az **Endpoint állomásnevét,** mivel ez az érték az a rekord, amelyre a CNAME rekord mutat.

![CDN egyéni tartomány](./media/dns-custom-domain/endpointcustomdomain.png)

Nyissa meg a DNS-zónát, és kattintson **a + Rekordkészlet gombra.** Töltse ki az alábbi információkat a **Rekordkészlet hozzáadása** panelen, és a létrehozáshoz kattintson az **OK** gombra.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Név     | cdnverify.mycdnendpoint        | Ez az érték a tartománynév címkével együtt az egyéni tartománynév teljes tartományneve.        |
|Típus     | CNAME        | A CNAME rekord aliast használ.        |
|Élettartam     | 1        | 1 használt 1 óra        |
|TTL mértékegysége     | Óra        | Az órák időmérésként használatosak         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Ebben a példában az a DNS-név, amelyhez az aliast létrehozza, ez az a cdnverify.adatumcdnendpoint.azureedge.net DNS-név, amelyet alapértelmezés szerint a tárfiókhoz ad meg.        |

A **Hálózati** > **CDN-profilok**gombra kattintva lépjen vissza a CDN-végponthoz, és válassza ki a CDN-profilt. Kattintson **a + Egyéni tartomány** elemre, és írja be a CNAME rekordaliasát a cdnverify előtag nélkül, majd kattintson a **Hozzáadás gombra.**

Ha ez a lépés befejeződött, térjen vissza a DNS-zónába, és hozzon létre egy CNAME rekordot a cdnverify előtag nélkül.  Ezt követően a CNAME rekord a cdnverify előtaggal törölhető. A CDN-ről és arról, hogy miként konfigurálhat egyéni tartományt a köztes regisztrációs lépés nélkül, látogasson el a [Map Azure CDN-tartalom egyéni tartományba.](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy [miként konfigurálhatja a fordított DNS-t az Azure-ban üzemeltetett szolgáltatásokhoz.](dns-reverse-dns-for-azure-services.md)
