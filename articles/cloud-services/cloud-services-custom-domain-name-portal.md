---
title: Egyéni tartománynév konfigurálása a Felhőszolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogyan teheti elérhetővé az Azure-alkalmazást vagy az adatokat az interneten egy egyéni tartományban a DNS-beállítások konfigurálásával.  Ezek a példák az Azure Portalt használják.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: e764e6a474b9843d43f9e8af9cf3b6a8ddf37189
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811654"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Egyéni tartománynév konfigurálása Azure felhőszolgáltatáshoz
Amikor létrehoz egy felhőszolgáltatást, az Azure hozzárendeli azt **a cloudapp.net**altartományához. Ha például a felhőszolgáltatás neve "contoso", a felhasználók egy URL-címen `http://contoso.cloudapp.net`férhetnek hozzá az alkalmazáshoz. Az Azure egy virtuális IP-címet is hozzárendel.

Azonban azt is ki az alkalmazás a saját domain nevet, például **contoso.com**. Ez a cikk bemutatja, hogyan foglalhat le vagy konfigurálhat egyéni tartománynevet a Cloud Service webes szerepkörökhöz.

Már érti, hogy mik azok a CNAME és A rekordok? [Ugrás múlt a magyarázat](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> A feladatban szereplő eljárások az Azure Cloud Servicesre vonatkoznak. Az App Services ről a [Meglévő egyéni DNS-név hozzárendelése az Azure Web Apps alkalmazáshoz](../app-service/app-service-web-tutorial-custom-domain.md)című témakörben talál. A tárfiókokról az [Egyéni tartománynév konfigurálása az Azure Blob storage-végponthoz](../storage/blobs/storage-custom-domain-name.md)című témakörben található.
> 
> 

<p/>

> [!TIP]
> Gyorsabban haladhat – használja az ÚJ Azure [interaktív útmutatót!](https://support.microsoft.com/kb/2990804)  Ez teszi társítja az egyéni tartománynevet és a kommunikáció (TLS) az Azure Cloud Services vagy az Azure websites egy pillanat alatt.
> 
> 

## <a name="understand-cname-and-a-records"></a>A CNAME és Az rekordok ismertetése
A CNAME (vagy alias rekordok) és az A rekordok egyaránt lehetővé teszik a tartománynév társítását egy adott kiszolgálóhoz (vagy ebben az esetben a szolgáltatáshoz), azonban eltérően működnek. Az A rekordok Azure Cloud-szolgáltatásokkal való használata során is vannak bizonyos szempontok, amelyeket figyelembe kell vennie, mielőtt eldöntené, hogy melyiket használja.

### <a name="cname-or-alias-record"></a>CNAME vagy Alias rekord
A CNAME rekord egy *adott* tartományt , például **contoso.com** vagy **www\.contoso.com**egy gyűjtőtartománynévhez rendel. Ebben az esetben a kanonikus tartománynév az Azure üzemeltetett alkalmazás **[myapp].cloudapp.net** tartományneve. Létrehozása után a CNAME létrehoz egy aliast a **[myapp].cloudapp.net**. A CNAME bejegyzés automatikusan feloldódik a **[myapp].cloudapp.net** szolgáltatás IP-címére, így ha a felhőszolgáltatás IP-címe megváltozik, nem kell semmilyen műveletet végrehajtania.

> [!NOTE]
> Egyes tartományregisztrálók csak CNAME rekord (például www\.contoso.com) használata esetén engedélyezik az altartományok leképezését, a gyökérnevek (például contoso.com) használata esetén. A CNAME rekordokról további információt a regisztrátor, [a CNAME rekord Wikipédia-bejegyzésében](https://en.wikipedia.org/wiki/CNAME_record)vagy az [IETF Domain Names - Implementation and Specification](https://tools.ietf.org/html/rfc1035) dokumentumban talál.

### <a name="a-record"></a>Rekord
*Az A* rekord egy tartományt , például **contoso.com** vagy **www\.contoso.com** *vagy egy helyettesítő tartományt* (például ** \*.contoso.com)** IP-címre rendel. Egy Azure Cloud-szolgáltatás esetén a szolgáltatás virtuális IP-címét. Tehát az A rekord fő előnye a CNAME rekordokkal szemben az, hogy lehet \*egy helyettesítő karaktert használó bejegyzés , például **a .contoso.com**, amely több altartományra , például **mail.contoso.com**, **login.contoso.com**vagy **www\.contso.com**vonatkozó kéréseket kezel.

> [!NOTE]
> Mivel egy A rekord statikus IP-címhez van rendelve, nem tudja automatikusan feloldani a felhőszolgáltatás IP-címének módosításait. A felhőszolgáltatás által használt IP-cím az első alkalommal van lefoglalva egy üres tárolóhelyre (éles vagy átmeneti). Ha törli a központi telepítést a tárolóhely, az IP-cím az Azure-ban, és a jövőbeli központi telepítések a tárolóhelyen kaphat egy új IP-címet.
> 
> Kényelmesen, egy adott központi telepítési hely (éles vagy átmeneti) IP-címét megmarad, ha az átmeneti és az éles környezetek közötti csere, vagy egy meglévő központi telepítés helyben történő frissítése végrehajtása. A műveletek végrehajtásáról a [Felhőszolgáltatások kezelése című](cloud-services-how-to-manage-portal.md)témakörben talál további információt.
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>CNAME rekord hozzáadása az egyéni tartományhoz
CNAME rekord létrehozásához a regisztrátor által biztosított eszközökkel új bejegyzést kell hozzáadnia az egyéni tartomány DNS-táblájához. Minden regisztrátor nak van egy hasonló, de kissé eltérő módszere a CNAME rekord megadására, de a fogalmak azonosak.

1. Az alábbi módszerek egyikével megkeresheti a felhőszolgáltatáshoz rendelt **.cloudapp.net** tartománynevet.

   * Jelentkezzen be az [Azure Portalon,]válassza ki a felhőszolgáltatást, tekintse meg az **Áttekintés szakaszt,** majd keresse meg a **webhely URL-bejegyzését.**

       ![gyors pillantás szakasz, amely a webhely URL-címét mutatja][csurl]

       **Vagy**
   * Telepítse és konfigurálja [az Azure Powershellt,](/powershell/azure/overview)majd használja a következő parancsot:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Mentse a tartománynevet, amelyet bármelyik módszer visszaadott URL-címben használ, mivel a CNAME rekord létrehozásakor szüksége lesz rá.
2. Jelentkezzen be a DNS-regisztráló webhelyére, és lépjen a DNS kezelésére szolgáló lapra. Keresse meg a webhely **tartománynév,** **DNS**vagy **névkiszolgáló-kezelés**címkével ellátott hivatkozásait vagy területeit.
3. Most keresse meg, hol választhatja ki vagy adhatja meg a CNAME's-t. Előfordulhat, hogy ki kell választania a bejegyzés típusát egy legördülő menüből, vagy egy speciális beállítási lapra kell lépnie. A **CNAME**, **Alias**vagy **Aldomains szavakat kell keresnie.**
4. Ha aliast szeretne létrehozni a **www** **www\.customdomain.com**. Ha aliast szeretne létrehozni a gyökértartományhoz, akkor a**\@** regisztráló DNS-eszközeiben a ' ' szimbólumként szerepelhet.
5. Ezután meg kell adnia egy gyűjtőállomásnevet, amely ebben az esetben az alkalmazás **cloudapp.net** tartománya.

A következő CNAME rekord például továbbítja az összes forgalmat **a www\.contoso.com-ról** **contoso.cloudapp.net,** az üzembe helyezett alkalmazás egyéni tartománynevére:

| Alias/állomásnév/altartomány | Kanonikus domain |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> A látogató a **www\.contoso.com** soha nem fogja látni az igazi fogadó (contoso.cloudapp.net), így a továbbítási folyamat láthatatlan a végfelhasználó számára.
> 
> A fenti példa csak a **www** aldomain forgalmára vonatkozik. Mivel a CNAME rekordokhoz nem használható helyettesítő karakterek, minden tartományhoz/altartományhoz létre kell hozniegy CNAME-t. Ha az altartományokból (például a *.contoso.com) érkező forgalmat a cloudapp.net-címre szeretné irányítani, konfigurálhat **URL-átirányítást** vagy **URL-továbbítási** bejegyzést a DNS-beállításokban, vagy létrehozhat egy A rekordot.

## <a name="add-an-a-record-for-your-custom-domain"></a>A rekord hozzáadása az egyéni tartományhoz
A rekord létrehozásához először meg kell találnia a felhőszolgáltatás virtuális IP-címét. Ezután a regisztrátor által biztosított eszközökkel vegyen fel egy új bejegyzést az egyéni tartomány DNS-táblájába. Minden regisztrátor nak van egy hasonló, de kissé eltérő módszere az A rekord megadására, de a fogalmak azonosak.

1. Az alábbi módszerek egyikével lejuthat a felhőszolgáltatás IP-címével.

   * Jelentkezzen be az [Azure Portalon,]válassza ki a felhőszolgáltatást, tekintse meg az **Áttekintés szakaszt,** majd keresse meg a **nyilvános IP-címek** bejegyzést.

       ![gyors pillantás szakasz mutatja a VIP][vip]

       **Vagy**
   * Telepítse és konfigurálja [az Azure Powershellt,](/powershell/azure/overview)majd használja a következő parancsot:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Mentse az IP-címet, ahogy szüksége lesz rá az A rekord létrehozásakor.
2. Jelentkezzen be a DNS-regisztráló webhelyére, és lépjen a DNS kezelésére szolgáló lapra. Keresse meg a webhely **tartománynév,** **DNS**vagy **névkiszolgáló-kezelés**címkével ellátott hivatkozásait vagy területeit.
3. Most keresse meg, hogy hol választhatja ki az A rekordot, illetve hol adhatja meg az Okat. Előfordulhat, hogy ki kell választania a bejegyzés típusát egy legördülő menüből, vagy egy speciális beállítási lapra kell lépnie.
4. Jelölje ki vagy írja be azt a tartományt vagy altartományt, amely ezt az A rekordot fogja használni. Ha például **www** aliast szeretne létrehozni a **\.www customdomain.com.** Ha minden altartományhoz helyettesítő bejegyzést szeretne létrehozni, írja be a "*****" értéket. Ez minden aldomainre kiterjed , például **a mail.customdomain.com**, **a login.customdomain.com**és a **www\.customdomain.com**.

    Ha A rekordot szeretne létrehozni a gyökértartományhoz, akkor a**\@** regisztráló DNS-eszközeiben a ' ' szimbólumként szerepelhet.
5. Adja meg a felhőszolgáltatás IP-címét a megadott mezőben. Ez társítja az A rekordban használt tartománybejegyzést a felhőszolgáltatás üzembe helyezésének IP-címével.

A következő A rekord például **továbbítja** az összes forgalmat contoso.com-ről **137.135.70.239-re,** az üzembe helyezett alkalmazás IP-címére:

| Állomásnév/altartomány | IP-cím |
| --- | --- |
| \@ |137.135.70.239 |

Ez a példa bemutatja a gyökértartomány A rekordjának létrehozását. Ha az összes aldomaint lefedő helyettesítő bejegyzést szeretne létrehozni, akkor altartományként a "*****" értéket kell megadnia.

> [!WARNING]
> Az Azure-beli IP-címek alapértelmezés szerint dinamikusak. Valószínűleg [fenntartott IP-címet](../virtual-network/virtual-networks-reserved-public-ip.md) szeretne használni annak biztosítására, hogy az IP-cím ne változzon.
> 
> 

## <a name="next-steps"></a>További lépések
* [A Cloud Services felügyelete](cloud-services-how-to-manage-portal.md)
* [CDN-tartalom leképezése egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md)
* [A felhőszolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* További információ a [felhőszolgáltatások üzembe helyezéséről.](cloud-services-how-to-create-deploy-portal.md)
* [TLS/SSL-tanúsítványok konfigurálása](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png



