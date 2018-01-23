---
title: "Azure DNS integrálása az Azure-erőforrások |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure DNS mentén DNS biztosít az Azure-erőforrások."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/19/2018
ms.author: kumud
ms.openlocfilehash: cbc769cd7356b3057fd2aae295071b04d2e40d91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Adja meg az egyéni tartomány beállításait az Azure-szolgáltatások az Azure DNS használatával

Az Azure DNS biztosít a DNS az Azure-erőforrások valamelyike egyéni tartományt, támogatási egyéni tartományok, illetve hogy rendelkezik-e egy teljesen minősített tartománynevét (FQDN). Példa: egy Azure webes alkalmazás, és azt szeretné, hogy a felhasználók által eléréséhez contoso.com, vagy a www.contoso.com teljes tartománynév használatával. Ez a cikk végigvezeti az Azure service konfigurálása az Azure DNS-beli egyéni tartományok használatának.

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy Azure DNS-ben az egyéni tartomány, meg kell először tartomány delegálása az Azure DNS-ben. Látogasson el [az Azure DNS-tartomány delegálása az](./dns-delegate-domain-azure-dns.md) konfigurálása delegáláshoz a Névkiszolgálók útmutatást. Ha a tartomány delegált az Azure DNS-zónát, le is tudja konfigurálja a szükséges DNS-rekordokat.

Konfigurálhat egy személyes vagy tartozó egyéni tartomány [Azure függvény alkalmazások](#azure-function-app), [nyilvános IP-címek](#public-ip-address), [App Service (webalkalmazások)](#app-service-web-apps), [Blob-tároló](#blob-storage), és [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Az Azure-függvény alkalmazás

Azure függvény alkalmazások esetében az egyéni tartománynév konfigurálása, egy olyan CNAME rekordot a függvény alkalmazás maga a konfigurációs és jön létre.
 
Navigáljon a **más** > **függvény App** válassza ki a függvény alkalmazást. Kattintson a **Platform funkciói** és a **hálózati** kattintson **egyéni tartományok**.

![függvény alkalmazás paneljén](./media/dns-custom-domain/functionapp.png)

Jegyezze fel az aktuális URL-címet a a **egyéni tartományok** panelen, ezt a címet a DNS-rekord létrehozása az alias használt.

![az egyéni tartomány panel](./media/dns-custom-domain/functionshostname.png)

Nyissa meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Töltse ki a következő információkat a a **adja hozzá a rekordhalmaz** panel megnyitásához, és kattintson **OK** létrehozásához.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | myfunctionapp        | Ez az érték és a tartománynév-címke az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Használjon egy olyan CNAME rekordot alias használ.        |
|TTL     | 1        | 1 használt 1 óra        |
|Élettartam egység     | Óra        | Az idő mérése használatosak üzemideje (óra)         |
|Alias     | adatumfunction.azurewebsites.net        | A DNS-név hoz létre az aliast, ebben a példában a, a függvény alkalmazás alapértelmezés szerint adatumfunction.azurewebsites.net DNS-név.        |

Lépjen vissza a függvény app, kattintson a **Platform funkciói**, majd a **hálózati** kattintson **egyéni tartományok**, majd a **Hostnames**kattintson **+ Hozzáadás állomásnév**.

Az a **állomásnév hozzáadása** panelen adja meg a CNAME rekordot a **állomásnév** szövegmezőt, és kattintson **ellenőrzése**. Ha a rekord tudta található, a **állomásnév hozzáadása** gomb akkor jelenik meg. Kattintson a **állomásnév hozzáadása** hozzáadása az aliast.

![függvény alkalmazások hozzáadása a gazdagép neve panel](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Nyilvános IP-cím

Az egyéni tartománynév konfigurálása egy nyilvános IP-címet használó szolgáltatások például Application Gateway, a terheléselosztó, a felhőalapú szolgáltatás, a Resource Manager virtuális gépek erőforrás címet, majd jegyezze fel klasszikus virtuális gépeket, egy CNAME használt.

Navigáljon a **hálózati** > **nyilvános IP-cím**, válassza ki a nyilvános IP-cím erőforrás, és kattintson a **konfigurációs**. Az IP-cím látható notate.

![nyilvános IP-cím panel](./media/dns-custom-domain/publicip.png)

Nyissa meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Töltse ki a következő információkat a a **adja hozzá a rekordhalmaz** panel megnyitásához, és kattintson **OK** létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | mywebserver        | Ez az érték és a tartománynév-címke az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | A        | Az A rekord használatára, mert az erőforrás IP-címet.        |
|TTL     | 1        | 1 használt 1 óra        |
|Élettartam egység     | Óra        | Az idő mérése használatosak üzemideje (óra)         |
|IP-cím     | <your ip address>       | A nyilvános IP-cím.|

![az A rekord létrehozása](./media/dns-custom-domain/arecord.png)

Az A rekord létrehozása után futtassa `nslookup` a rekord oldja fel a rendszer ellenőrzése.

![nyilvános IP-cím DNS-címkeresés](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

A következő lépések egy app service webalkalmazás az egyéni tartománynév beállítása.

Navigáljon a **webes és mobil** > **App Service** , és válassza ki az erőforrást, konfigurál egy egyéni tartománynevet, majd kattintson az **egyéni tartományok**.

Jegyezze fel az aktuális URL-címet a a **egyéni tartományok** panelen, ezt a címet a DNS-rekord létrehozása az alias használt.

![egyéni tartományok panel](./media/dns-custom-domain/url.png)

Nyissa meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Töltse ki a következő információkat a a **adja hozzá a rekordhalmaz** panel megnyitásához, és kattintson **OK** létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | mywebserver        | Ez az érték és a tartománynév-címke az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Használjon egy olyan CNAME rekordot alias használ. Ha az erőforrás IP-címet használ, egy A rekordot használni.        |
|TTL     | 1        | 1 használt 1 óra        |
|Élettartam egység     | Óra        | Az idő mérése használatosak üzemideje (óra)         |
|Alias     | webserver.azurewebsites.net        | A DNS-név hoz létre az aliast, ebben a példában a webes alkalmazás alapértelmezés szerint webserver.azurewebsites.net DNS nevével.        |


![Hozzon létre egy CNAME rekordot](./media/dns-custom-domain/createcnamerecord.png)

Lépjen vissza az app service szolgáltatásban az egyéni tartománynév beállítása. Kattintson a **egyéni tartományok**, majd kattintson a **Hostnames**. A létrehozott CNAME rekord hozzáadásához kattintson **+ Hozzáadás állomásnév**.

![1. ábra](./media/dns-custom-domain/figure1.png)

A folyamat befejezése után futtassa **nslookup** érvényesítése névfeloldás működik-e.

![1. ábra](./media/dns-custom-domain/finalnslookup.png)

Az App Service egy egyéni tartomány leképezése kapcsolatos további információkért látogasson el a [egy meglévő egyéni DNS-névvel hozzárendelése az Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Ha egy egyéni tartomány vásárlásához van szüksége, látogasson el [vásároljon egy egyéni tartománynevet, az Azure Web Apps](../app-service/custom-dns-web-site-buydomains-web-app.md) további információt az App Service-tartományokat.

## <a name="blob-storage"></a>Blob Storage

A következő lépések egy CNAME rekordot a asverify metódussal a blob storage-fiók konfigurálása. Ez a módszer biztosítja, hogy nincs állásidő nélkül.

Navigáljon a **tárolási** > **Tárfiókok**, válassza ki a tárfiók, és kattintson a **egyéni tartomány**. A teljes tartománynév, a 2. lépés notate, ezt az értéket az első CNAME rekord létrehozására szolgál

![a BLOB storage egyéni tartományt](./media/dns-custom-domain/blobcustomdomain.png)

Nyissa meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Töltse ki a következő információkat a a **adja hozzá a rekordhalmaz** panel megnyitásához, és kattintson **OK** létrehozásához.


|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | asverify.mystorageaccount        | Ez az érték és a tartománynév-címke az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Használjon egy olyan CNAME rekordot alias használ.        |
|TTL     | 1        | 1 használt 1 óra        |
|Élettartam egység     | Óra        | Az idő mérése használatosak üzemideje (óra)         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | A DNS-név hoz létre az aliast, ebben a példában a tárolási fiók alapértelmezés szerint asverify.adatumfunctiona9ed.blob.core.windows.net DNS nevével.        |

Lépjen vissza a tárfiók kattintva **tárolási** > **Tárfiókok**, válassza ki a tárfiók, és kattintson a **egyéni tartomány**. Írja be az aliast, a szövegmezőben, ellenőrizze az asverify előtag nélkül ** CNAME rekord közvetett ellenőrzésének használata, és kattintson a **mentése**. Ez a lépés végrehajtása után térjen vissza a DNS-zónát, és hozzon létre egy CNAME rekordot a asverify előtag nélkül.  Később áll biztonságosan törölje a CNAME rekordot a cdnverify előtaggal.

![a BLOB storage egyéni tartományt](./media/dns-custom-domain/indirectvalidate.png)

Futtassa a DNS-feloldás eredményének ellenőrzése`nslookup`

Egyéni tartomány leképezése egy blob storage endpoint olvashat további [állítson be egy egyéni tartománynevet a Blob storage-végponthoz](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

A következő lépések használatával egy olyan CNAME rekordot a cdnverify metódussal a CDN-végponthoz konfigurálása. Ez a módszer biztosítja, hogy nincs állásidő nélkül.

Navigáljon a **hálózati** > **CDN-profil**, válassza ki a CDN-profilt, és kattintson a **végpontok** alatt **általános**.

Válassza ki a végpont dolgozik, és kattintson a **+ egyéni tartomány**. Megjegyzés: a **végpont állomásnevéhez** mivel ezt az értéket a bejegyzést, amely a CNAME rekord mutat.

![Egyéni CDN-tartomány](./media/dns-custom-domain/endpointcustomdomain.png)

Nyissa meg a DNS-zónát, és kattintson a **+ rekordhalmaz**. Töltse ki a következő információkat a a **adja hozzá a rekordhalmaz** panel megnyitásához, és kattintson **OK** létrehozásához.

|Tulajdonság  |Érték  |Leírás  |
|---------|---------|---------|
|Name (Név)     | cdnverify.mycdnendpoint        | Ez az érték és a tartománynév-címke az egyéni tartománynév teljes Tartománynevét.        |
|Típus     | CNAME        | Használjon egy olyan CNAME rekordot alias használ.        |
|TTL     | 1        | 1 használt 1 óra        |
|Élettartam egység     | Óra        | Az idő mérése használatosak üzemideje (óra)         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | A DNS-név hoz létre az aliast, ebben a példában a tárolási fiók alapértelmezés szerint cdnverify.adatumcdnendpoint.azureedge.net DNS nevével.        |

Lépjen vissza a CDN-végpontot kattintva **hálózati** > **CDN-profilra**, és válassza ki a CDN-profilt. Kattintson a **+ egyéni tartomány** , és adja meg a CNAME rekord alias a cdnverify előtag nélkül, és kattintson **Hozzáadás**.

Ez a lépés végrehajtása után térjen vissza a DNS-zónát, és hozzon létre egy CNAME rekordot a cdnverify előtag nélkül.  Később áll biztonságosan törölje a CNAME rekordot a cdnverify előtaggal. További információ a CDN és a köztes regisztrációs lépésében nélkül az egyéni tartománynév beállítása a Microsoft [térkép Azure CDN-tartalom egyéni tartományhoz](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [névkeresési DNS az Azure-ban tárolt szolgáltatások konfigurálása](dns-reverse-dns-for-azure-services.md).