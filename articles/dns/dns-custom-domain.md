---
title: Az Azure DNS integrálása az Azure-erőforrások
description: Ismerje meg, hogyan mentén az Azure DNS használatával DNS adja meg az Azure-erőforrások.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/18/2019
ms.author: victorh
ms.openlocfilehash: b513e898e25397f54b8f7f7590a4466523a705ff
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401418"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Adja meg az egyéni tartomány beállításait az Azure-szolgáltatások az Azure DNS használatával

Az Azure DNS biztosít a DNS egy egyéni tartományt az Azure-erőforrások bármely, támogatási egyéni tartományok vagy, hogy rendelkezik-e egy teljesen minősített tartománynevét (FQDN). Rendelkezik egy Azure-webalkalmazást, és azt szeretné, hogy a felhasználók által eléréséhez lehet például a contoso.com vagy www.contoso.com az FQDN-ként. Ez a cikk végigvezeti az Azure DNS az Azure-szolgáltatás konfigurálása egyéni tartományok használatával.

## <a name="prerequisites"></a>Előfeltételek

Az egyéni tartomány Azure DNS használatához meg kell először tartomány delegálása az Azure DNS. Látogasson el [tartomány delegálása az Azure DNS](./dns-delegate-domain-azure-dns.md) vonatkozó utasításokat a névkiszolgálókat a delegálás konfigurálása. Ha az Azure DNS-zónabeli tartományához delegált, áll a szükséges DNS-rekordok konfigurálásával.

Konfigurálhat egy személyes vagy egyéni tartomány [Azure Function Apps](#azure-function-app), [nyilvános IP-címek](#public-ip-address), [App Service (webalkalmazások)](#app-service-web-apps), [a Blob storage](#blob-storage), és [az Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure Function App

Azure function appsszel egyéni tartomány konfigurálása, egy CNAME rekordot a függvényalkalmazás magát a konfigurációs és jön létre.
 
Navigáljon a **Függvényalkalmazás** , és válassza ki a függvényalkalmazást. Kattintson a **platformfunkciók** és **hálózatkezelés** kattintson **egyéni tartományok**.

![függvény panelére](./media/dns-custom-domain/functionapp.png)

Vegye figyelembe az aktuális URL-címet a a **egyéni tartományok** panelen ezt a címet a létrehozott DNS-rekordok szolgál az alias.

![Egyéni tartomány panel](./media/dns-custom-domain/functionshostname.png)

Keresse meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Adja meg az alábbi adatokat a a **rekordhalmaz hozzáadása** panelre, és kattintson **OK** létrehozásához.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | myfunctionapp        | Ezt az értéket a tartománynév címkéjének együtt az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Alias használata egy CNAME-rekordot használ.        |
|TTL     | 1        | 1 1 órán keresztül van használatban        |
|Élettartam mértékegysége     | Óra        | Óra van megadva, az idő mérése         |
|Alias     | adatumfunction.azurewebsites.net        | A DNS-név hoz létre alias, ebben a példában a adatumfunction.azurewebsites.net alapértelmezés szerint a függvényalkalmazás által biztosított DNS-név legyen.        |

Lépjen vissza a függvényalkalmazást, kattintson a **platformfunkciók**, majd a **hálózatkezelés** kattintson **egyéni tartományok**, majd **egyéniállomásnevek** kattintson **+ gazdagépnév hozzáadása**.

Az a **gazdagépnév hozzáadása** panelen adja meg a CNAME rekordot a **állomásnév** szövegmezőbe, és kattintson **ellenőrzése**. Ha a rekord található, a **gazdagépnév hozzáadása** gomb jelenik meg. Kattintson a **gazdagépnév hozzáadása** alias hozzáadása.

![a függvényalkalmazások hozzáadása a gazdagép neve panel](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Nyilvános IP-cím

Egy nyilvános IP-címet használó szolgáltatások cím erőforrás, például az Application Gateway, a Load Balancer, a Cloud Service, a Resource Manager virtuális gépek, és klasszikus virtuális gépeket, egy CNAME rekord jegyezze fel az egyéni tartomány konfigurálásához használt.

Navigáljon a **hálózatkezelés** > **nyilvános IP-cím**, válassza ki a nyilvános IP-cím-erőforrást, és kattintson a **konfigurációs**. Az IP-cím látható notate.

![nyilvános IP-cím panel](./media/dns-custom-domain/publicip.png)

Keresse meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Adja meg az alábbi adatokat a a **rekordhalmaz hozzáadása** panelre, és kattintson **OK** létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | mywebserver        | Ezt az értéket a tartománynév címkéjének együtt az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | A        | Egy A rekordot, hogy az erőforrás IP-cím használata        |
|TTL     | 1        | 1 1 órán keresztül van használatban        |
|Élettartam mértékegysége     | Óra        | Óra van megadva, az idő mérése         |
|IP-cím     | <your ip address>       | A nyilvános IP-cím.|

![a rekord létrehozása](./media/dns-custom-domain/arecord.png)

Az A rekord létrehozása után futtassa `nslookup` érvényesíteni a rekordot szándékot oldja fel.

![nyilvános IP-cím DNS-címkeresés](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Az alábbi lépéseket vezeti végig egy app service web appsban az egyéni tartománynév beállítása.

Navigáljon a **App Service-ben** , és válassza ki az erőforrást konfigurál egy egyéni tartománynevet, és kattintson a **egyéni tartományok**.

Vegye figyelembe az aktuális URL-címet a a **egyéni tartományok** panelen ezt a címet a létrehozott DNS-rekordok szolgál az alias.

![egyéni tartományok paneljén](./media/dns-custom-domain/url.png)

Keresse meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Adja meg az alábbi adatokat a a **rekordhalmaz hozzáadása** panelre, és kattintson **OK** létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | mywebserver        | Ezt az értéket a tartománynév címkéjének együtt az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Alias használata egy CNAME-rekordot használ. Ha az erőforrás IP-címet használ, rekord használni kívánt.        |
|TTL     | 1        | 1 1 órán keresztül van használatban        |
|Élettartam mértékegysége     | Óra        | Óra van megadva, az idő mérése         |
|Alias     | webserver.azurewebsites.net        | A DNS-név hoz létre alias, ebben a példában a webalkalmazás alapértelmezés szerint a megadott webserver.azurewebsites.net DNS-név legyen.        |


![create a CNAME record](./media/dns-custom-domain/createcnamerecord.png)

Lépjen vissza az app service, amely az egyéni tartománynév van konfigurálva. Kattintson a **egyéni tartományok**, majd kattintson a **állomásnevek**. A létrehozott CNAME-rekord hozzáadásához kattintson **+ gazdagépnév hozzáadása**.

![1. ábra](./media/dns-custom-domain/figure1.png)

A folyamat befejezése után futtassa **nslookup** ellenőrzése a névfeloldás működik.

![1. ábra](./media/dns-custom-domain/finalnslookup.png)

Az App Service egyéni tartományok leképezését kapcsolatos további információkért látogasson el [meglévő egyéni DNS-név leképezése az Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Ha egy egyéni tartományt van szüksége, látogasson el a [Azure Web Apps egy egyéni tartománynév vásárlása](../app-service/manage-custom-dns-buy-domain.md) további információ az App Service-tartományok.

## <a name="blob-storage"></a>Blob Storage

Az alábbi lépéseket vezeti végig egy CNAME rekordot a asverify metódussal a blob storage-fiók konfigurálása. Ez a módszer biztosítja, hogy nem jár.

Navigáljon a **tárolási** > **Tárfiókok**, válassza ki a tárfiókját, és kattintson a **egyéni tartomány**. A teljes tartománynév, a 2. lépés notate, ezt az értéket használja az első CNAME-rekord létrehozása

![a BLOB storage – egyéni tartomány](./media/dns-custom-domain/blobcustomdomain.png)

Keresse meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Adja meg az alábbi adatokat a a **rekordhalmaz hozzáadása** panelre, és kattintson **OK** létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | asverify.mystorageaccount        | Ezt az értéket a tartománynév címkéjének együtt az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Alias használata egy CNAME-rekordot használ.        |
|TTL     | 1        | 1 1 órán keresztül van használatban        |
|Élettartam mértékegysége     | Óra        | Óra van megadva, az idő mérése         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | A DNS-név hoz létre alias, ebben a példában a tárfiók alapértelmezés szerint a megadott asverify.adatumfunctiona9ed.blob.core.windows.net DNS-név legyen.        |

Lépjen vissza a tárfiók kattintva **tárolási** > **Tárfiókok**, válassza ki a tárfiókját, és kattintson a **egyéni tartomány**. Írja be az aliast, a szövegmezőbe, ellenőrizze a asverify előtag nélkül létrehozott ** CNAME rekord közvetett ellenőrzésének használata, majd kattintson az **mentése**. Ez a lépés végrehajtása után térjen vissza a DNS-zónát, és hozzon létre egy CNAME rekordot a asverify előtag nélkül.  Később, a cdnverify előtagot a CNAME rekord törléséhez biztonságban.

![a BLOB storage – egyéni tartomány](./media/dns-custom-domain/indirectvalidate.png)

A futó DNS-feloldás ellenőrzése `nslookup`

További információt talál további információt az egyéni tartomány leképezése egy blob storage-végponthoz [egy egyéni tartománynév beállítása a Blob storage-végponthoz](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Az alábbi lépéseket vezeti végig egy CNAME rekordot a CDN-végpont a cdnverify metódussal konfigurálása. Ez a módszer biztosítja, hogy nem jár.

Navigáljon a **hálózatkezelés** > **CDN-profilok**, válassza ki a CDN-profilra.

Válassza ki a végpont dolgozik, és kattintson a **+ egyéni tartomány**. Megjegyzés: a **végpont gazdaneve** mivel ezt az értéket a rekordot, amely a CNAME-rekordot mutat.

![Egyéni CDN-tartomány](./media/dns-custom-domain/endpointcustomdomain.png)

Keresse meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Adja meg az alábbi adatokat a a **rekordhalmaz hozzáadása** panelre, és kattintson **OK** létrehozásához.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | cdnverify.mycdnendpoint        | Ezt az értéket a tartománynév címkéjének együtt az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Alias használata egy CNAME-rekordot használ.        |
|TTL     | 1        | 1 1 órán keresztül van használatban        |
|Élettartam mértékegysége     | Óra        | Óra van megadva, az idő mérése         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | A DNS-név hoz létre alias, ebben a példában a tárfiók alapértelmezés szerint a megadott cdnverify.adatumcdnendpoint.azureedge.net DNS-név legyen.        |

Lépjen vissza a CDN-végpontra kattintva **hálózatkezelés** > **CDN-profilok**, és válassza ki a CDN-profilra. Kattintson a **+ egyéni tartomány** és adja meg a CNAME-rekord alias nélkül a cdnverify előtagot, majd kattintson az **Hozzáadás**.

Ez a lépés végrehajtása után térjen vissza a DNS-zónát, és hozzon létre egy CNAME rekordot a cdnverify előtagot nélkül.  Később, a cdnverify előtagot a CNAME rekord törléséhez biztonságban. További információk a CDN és a köztes lépésben nélkül egyéni tartomány konfigurálása [térkép az Azure CDN-tartalom egy egyéni tartományt](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fordított DNS az Azure-ban üzemeltetett szolgáltatások konfigurálása](dns-reverse-dns-for-azure-services.md).