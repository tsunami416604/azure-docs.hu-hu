---
title: Egyéni tartománynév konfigurálása a Cloud Services |} A Microsoft Docs
description: Ismerje meg, hogyan teszi közzé az Azure-alkalmazás vagy adatokat az interneten, az egyéni tartomány DNS-beállítások konfigurálásával.  Ezekben a példákban az Azure Portalon.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 814ae0e79ac24d5b645c4bf107287ab1b8326ff4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121621"
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Az Azure cloud Services számára egyéni tartománynév konfigurálása
Amikor létrehoz egy felhőalapú szolgáltatás, az Azure hozzárendeli egy résztartományán **cloudapp.net**. Például, ha a Felhőszolgáltatás neve "contoso", a felhasználók tudják elérhetik az alkalmazást egy URL-címet, például a http://contoso.cloudapp.net. Az Azure hozzárendeli a virtuális IP-címet is.

Azonban is közzéteheti az alkalmazást a saját tartománynevét, például **contoso.com**. Ez a cikk ismerteti egy egyéni tartománynév beállítása a felhőalapú szolgáltatás webes szerepkörök, illetve a.

Ehhez már ismernie CNAME és a egy a rekordot vannak? [A magyarázat korábbi Jump](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Ebben a feladatban eljárásokat alkalmazni az Azure Cloud servicesben. Az App Services, lásd: [meglévő egyéni DNS-név leképezése az Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md). Storage-fiókok esetében lásd: [az Azure Blob storage-végpont egyéni tartománynév beállítása](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Gyorsabb – kezdheti használni az új Azure [útmutató](https://support.microsoft.com/kb/2990804)!  Lehetővé teszi, egy egyéni tartománynevet és biztonságossá tétele kommunikációhoz (SSL) társítása az Azure Cloud Services vagy az Azure-webhelyek egy beépülő modult.
> 
> 

## <a name="understand-cname-and-a-records"></a>CNAME és A rekordok ismertetése
CNAME (vagy alias rekordok) és a rekordok mindkét lehetővé teszi egy tartománynevet társítása egy adott kiszolgáló (vagy szolgáltatást ebben az esetben) azonban ezek eltérően működik. Nincsenek is figyelembe kell venni néhány bizonyos az Azure Cloud serviceshez, amelyeket érdemes, mielőtt mellett dönt, hogy melyiket kívánja használni A rekordok használatakor.

### <a name="cname-or-alias-record"></a>CNAME or Alias record
Egy CNAME rekord leképezi egy *adott* tartomány, például **contoso.com** vagy **www\.contoso.com**, kanonikus tartománynévhez. Ebben az esetben a canonical tartománynév van-e a **[myapp] .cloudapp .net** tartománynév az Azure a futó alkalmazás. Létrehozása után a CNAME REKORDOT hoz létre egy alias a **[myapp] .cloudapp .net**. A CNAME bejegyzés feloldja az IP-címet a **[myapp] .cloudapp .net** szolgáltatás automatikusan, így ha a felhőszolgáltatás IP-címe megváltozik, akkor nem kell semmit sem kell.

> [!NOTE]
> Some domain registrars only allow you to map subdomains when using a CNAME record, such as www\.contoso.com, and not root names, such as contoso.com. A CNAME-rekordokat további információkért lásd: a dokumentáció a regisztráló térképein [a CNAME-rekordot a Wikipedia bejegyzés](https://en.wikipedia.org/wiki/CNAME_record), vagy a [IETF tartománynevek - megvalósítási és meghatározása](https://tools.ietf.org/html/rfc1035) dokumentumot.

### <a name="a-record"></a>A record
Egy *A* rekord leképezi egy tartományhoz, mint például **contoso.com** vagy **www\.contoso.com**, *vagy egy helyettesítő karaktert tartalmazó tartomány* például **\*. contoso.com**, IP-címhez. Esetén az Azure Cloud Service, a virtuális IP-címét a szolgáltatást. Így egy A rekordot egy CNAME-rekordot keresztül fő előnye, hogy rendelkezhet egy bejegyzést, mint például egy helyettesítő karaktert használó \* **. contoso.com**, több altartománnyal kérelmek amely kellene kezelni  **mail.contoso.com**, **login.contoso.com**, vagy **www\.contso.com**.

> [!NOTE]
> Mivel az A rekord le van képezve egy statikus IP-címet, automatikusan nem képes feloldani módosításokat a felhőszolgáltatás IP-címet. A felhőalapú szolgáltatás által használt IP-cím van lefoglalva az első alkalommal telepít egy üres helyre (éles vagy előkészítési.) Az üzembe helyezés az üzembe helyezési pont törlése esetén az IP-cím akkor szabadul fel, amelyeket az Azure és a jövőbeli telepítések a bővítőhelyre új IP-cím is megadható.
> 
> A megadott üzembe helyezési pont (éles vagy előkészítési) IP-címét kényelmesen, ha átmeneti és éles környezetekben üzemelő példányok vagy meglévő környezet a helyben történő frissítése között felcserélése rendszer megőrzi. Ezek a műveletek végrehajtása a további információkért lásd: [cloud services kezelése](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adjon hozzá egy CNAME rekordot, az egyéni tartományhoz
Hozzon létre egy CNAME rekordot, hogy hozzá kell adnia egy új bejegyzést a DNS-tábla az egyéni tartományhoz a regisztráló által biztosított eszközök segítségével. Minden tartományregisztráló egy hasonló, de eltérő módot a CNAME rekordot, de a koncepciók megegyeznek.

1. Kereséséhez használja az alábbi módszerek egyikét a **. cloudapp.net** tartománynevet a felhőalapú szolgáltatáshoz rendelt.

   * Jelentkezzen be a [Azure Portal], válassza ki a felhőszolgáltatást, tekintse meg a **Essentials** szakaszt, és keresse meg a **webhely URL-címe** bejegyzés.

       ![gyors áttekintés szakaszban a webhely URL-Címének megjelenítése][csurl]

       **VAGY**
   * Telepítse és konfigurálja [Azure PowerShell-lel](/powershell/azure/overview), és a következő parancsot:

       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```

     Mentse a tartománynév, az URL-címet, vagy a metódus által visszaadott, szüksége lesz rá egy CNAME rekord létrehozásakor használt.
2. Jelentkezzen be a DNS-regisztráló webhelyén, és nyissa meg a DNS kezelésére szolgáló lap. Keresse meg az hivatkozások és a hely a következő címkét: területeit **tartománynév**, **DNS**, vagy **névkiszolgáló kezelése**.
3. Most már található, ahol válassza ki vagy adja meg a CNAME. Előfordulhat, hogy a rekord típusa egy legördülő listából válassza le, vagy egy speciális beállítások lapot. Meg kell keresnie **CNAME**, **Alias**, vagy **altartományok**.
4. Meg kell adni a tartomány vagy altartomány alias a CNAME, mint például **www** Ha azt szeretné, hozzon létre egy aliast a **www\.customdomain.com**. Ha azt szeretné, a legfelső szintű tartomány alias létrehozása, ez elérhetőként a "**\@**" szimbólumot a regisztráló DNS-eszközök a.
5. Ezt követően meg kell adnia egy canonical állomás neve, amely az alkalmazás **cloudapp.net** ebben az esetben a tartományhoz.

Ha például a következő CNAME-rekord továbbítja a érkező minden **www\.contoso.com** való **contoso.cloudapp.net**, az egyéni tartománynév az üzembe helyezett alkalmazás:

| Alias/Host name/Subdomain | Canonical tartomány |
| --- | --- |
| www |contoso.cloudapp.net |

> [!NOTE]
> A látogatói **www\.contoso.com** soha nem látható a true (contoso.cloudapp.net), állomás, így a továbbítási folyamatban a felhasználó számára.
> 
> A fenti példa csak érvényes forgalmat, a **www** altartományt. A CNAME-rekordokat nem használhat helyettesítő karaktereket, mivel létre kell hoznia egy CNAME minden tartomány/altartomány. Szeretné-e számára a forgalom az altartományt, mint például *. contoso.com, a cloudapp.net címet is konfigurálhat egy **átirányítási URL-cím** vagy **előre URL-cím** bejegyzést a DNS-beállításokat, vagy hozzon létre egy A rekordot.

## <a name="add-an-a-record-for-your-custom-domain"></a>Egy A rekordot az egyéni tartomány hozzáadása
Hozzon létre egy rekordot, először keresse meg a felhőszolgáltatás virtuális IP-címét. Majd adjon hozzá egy új bejegyzést a DNS-tábla az egyéni tartományhoz a regisztráló által biztosított eszközök segítségével. Minden tartományregisztráló egy hasonló, de eltérő módot a egy A rekordot, de a koncepciók megegyeznek.

1. A felhőszolgáltatás IP-címének lekéréséhez használja az alábbi módszerek egyikét.

   * Jelentkezzen be a [Azure Portal], válassza ki a felhőszolgáltatást, tekintse meg a **Essentials** szakaszt, és keresse meg a **nyilvános IP-címek** bejegyzés.

       ![gyors áttekintés szakaszban a virtuális IP-cím megjelenítése][vip]

       **VAGY**
   * Telepítse és konfigurálja [Azure PowerShell-lel](/powershell/azure/overview), és a következő parancsot:

       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```

     Mentse az IP-címet, szüksége lesz rá a rekord létrehozásakor.
2. Jelentkezzen be a DNS-regisztráló webhelyén, és nyissa meg a DNS kezelésére szolgáló lap. Keresse meg az hivatkozások és a hely a következő címkét: területeit **tartománynév**, **DNS**, vagy **névkiszolgáló kezelése**.
3. Most már található, jelölje be és ír egy bejegyzést. Előfordulhat, hogy a rekord típusa egy legördülő listából válassza le, vagy egy speciális beállítások lapot.
4. Válassza ki vagy adja meg a tartomány vagy altartomány, amelyet ez A rekordot fog használni. Válassza ki például **www** Ha azt szeretné, hozzon létre egy aliast a **www\.customdomain.com**. Ha szeretne létrehozni egy helyettesítő karaktert tartalmazó bejegyzést valamennyi altartományt, adja meg az "x". Ez azzal foglalkozik, hogy az összes altartományok például **mail.customdomain.com**, **login.customdomain.com**, és **www\.customdomain.com**.

    Szeretne létrehozni egy A rekordot a legfelső szintű tartomány, ha ez elérhetőként a "**\@**" szimbólumot lát, az a regisztráló DNS-eszközök.
5. A megadott mezőben adja meg a felhőszolgáltatás IP-címét. Ez hozzárendeli a tartomány bejegyzés az A rekord a felhőszolgáltatás üzembe helyezésének IP-címét használja.

Például a következő bejegyzés továbbítja érkező minden **contoso.com** való **137.135.70.239**, az üzembe helyezett alkalmazás IP-címe:

| Host name/Subdomain | IP-cím |
| --- | --- |
| \@ |137.135.70.239 |

Ez a példa bemutatja egy A rekordot a gyökértartomány létrehozása. Ha szeretné, hogy biztosítsák a altartományokkal helyettesítő bejegyzés létrehozása, kell beírnia "x", az altartomány.

> [!WARNING]
> Az Azure-beli IP-címek dinamikusak alapértelmezés szerint. Valószínűleg érdemes használni egy [fenntartott IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md) annak érdekében, hogy az IP-címe nem változik.
> 
> 

## <a name="next-steps"></a>További lépések
* [A Cloud Services felügyelete](cloud-services-how-to-manage-portal.md)
* [CDN-tartalom leképezése egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md)
* [A felhőszolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Ismerje meg, hogyan [egy felhőalapú szolgáltatás üzembe helyezése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure Portal]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
