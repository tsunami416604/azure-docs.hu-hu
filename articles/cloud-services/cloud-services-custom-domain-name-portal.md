---
title: "Állítson be egy egyéni tartománynevet a felhőalapú szolgáltatások |} Microsoft Docs"
description: "Megtudhatja, hogyan teszi közzé az Azure alkalmazás vagy adatokat az interneten egyéni tartomány DNS-beállítások konfigurálásával.  Ezekben a példákban az Azure-portálon."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: afda0bc79a26c8cb9736c754a681ebb602e41af1
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Egy egyéni tartománynevet, az Azure-felhőszolgáltatás konfigurálása
Amikor létrehoz egy felhőalapú szolgáltatás, Azure rendeli altartománya **cloudapp.net**. Például ha a Felhőszolgáltatás neve "contoso", a felhasználók tudnak az URL-cím http://contoso.cloudapp.net például az alkalmazás eléréséhez. Azure is hozzárendel egy virtuális IP-címet.

Azonban Ön is is elérhetővé teheti az alkalmazás a saját tartománynevét, például a **contoso.com**. Ez a cikk ismerteti, hogyan állítson be egy egyéni tartománynevet a felhőalapú szolgáltatás webes szerepkör, illetve a.

Tegye meg már megismeréséhez CNAME és A rekordok? [Ugrás a magyarázat túli](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Ebben a feladatban eljárások Azure Felhőszolgáltatások vonatkozik. Alkalmazásszolgáltatások, lásd: [ez](../app-service/app-service-web-tutorial-custom-domain.md). Storage-fiókok, lásd: [ez](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Gyorsabb--induláshoz használja az új Azure [forgatókönyv interaktív](http://support.microsoft.com/kb/2990804)!  Így a egy egyéni tartománynevet és biztonságossá tétele kommunikációhoz (SSL) társítása Azure Cloud Services vagy az Azure Websitesra egy beépülő.
> 
> 

## <a name="understand-cname-and-a-records"></a>CNAME és az A rekordok ismertetése
CNAME (vagy alias rekordokat) és a rekordok mindkét lehetővé teszi tartománynév társítani a kiszolgálóhoz (vagy szolgáltatás ebben az esetben) azonban ezek eltérően működik. Nincsenek is figyelembe kell venni néhány bizonyos Azure felhőszolgáltatások is dönt használandó előtt figyelembe kell vennie A rekordok használatával.

### <a name="cname-or-alias-record"></a>CNAME- vagy aliasnév rekord
A CNAME rekord rendeli hozzá a *adott* tartomány, például **contoso.com** vagy **www.contoso.com**, kanonikus tartománynévvel. A kanonikus tartománynév van ebben az esetben a **[myapp] .cloudapp .net** tartománynevét, az Azure futó alkalmazás. Létrehozása után a CNAME REKORDOT hoz létre egy aliast a **[myapp] .cloudapp .net**. A CNAME-bejegyzés feloldja az IP-címére a **[myapp] .cloudapp .net** szolgáltatás automatikusan, így ha megváltozik az IP-címet a felhőszolgáltatás, nincs teendője.

> [!NOTE]
> Néhány tartomány regisztráló szervezetek altartományok csatlakoztatható egy olyan CNAME rekordot, például www.contoso.com és a nem gyökér neve, például contoso.com használata esetén csak engedélyezése. További információ a CNAME-rekordot, a regisztráló által dokumentációjában talál [CNAME rekordot a Wikipedia bevitelének](http://en.wikipedia.org/wiki/CNAME_record), vagy a [IETF tartománynév - megvalósítása és meghatározása](http://tools.ietf.org/html/rfc1035) dokumentum.
> 
> 

### <a name="a-record"></a>Egy rekord
Egy *A* rekord rendeli hozzá a tartományhoz, például a **contoso.com** vagy **www.contoso.com**, *vagy helyettesítő tartomány* például  **\*. contoso.com**, egy IP-címre. Ha egy Azure Cloud Service, a virtuális IP-címe a szolgáltatást. Így a fő előnye, hogy az A rekord keresztül egy olyan CNAME rekordot, akkor használja, mint a helyettesítő karakter, egy vagy több bejegyzése \* **. contoso.com**, amely volna tanúsítványigénylések több altartományok például  **mail.contoso.com**, **login.contoso.com**, vagy **www.contso.com**.

> [!NOTE]
> Mivel az A rekord egy statikus IP-cím van leképezve, automatikusan nem képes feloldani módosítások a felhőalapú szolgáltatás IP-címre. A a felhőalapú szolgáltatás által használt IP-cím van lefoglalva az első alkalommal telepít egy üres helyre (éles vagy átmeneti.) Ha törli a tárhely központi telepítését, az IP-cím Azure által kiadott, és a jövőbeli telepítések a kivett új IP-cím is megadható.
> 
> Állandó kényelmesen, egy adott üzembe helyezési pontjának (üzemi és átmeneti) IP-címét a csere közötti átmeneti és üzemi környezetek vagy egy meglévő központi telepítési helyben történő frissítése során. Ezek a műveletek végrehajtásával további információkért lásd: [felhőszolgáltatások kezelése](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adjon hozzá egy CNAME rekordot, az egyéni tartományhoz
A CNAME rekordot kell létrehozni, hozzá kell adnia egy új bejegyzést a DNS-tábla az egyéni tartomány a regisztráló által biztosított eszközök segítségével. Minden tartományregisztráló egy CNAME rekordot a telepítésükhöz hasonló, csak metódust tartalmaz, de a koncepciók.

1. Ezen módszerek egyikét kereséséhez használja a **. cloudapp.net** tartománynevet a felhőalapú szolgáltatáshoz.
   
   * Jelentkezzen be a [Azure-portálon], válassza ki a felhőalapú szolgáltatás, tekintse meg a **Essentials** szakaszt, és keresse meg a **webhely URL-címe** bejegyzés.
     
       ![Megjeleníti a hely URL-cím gyors áttekintő szakasz][csurl]
     
       **VAGY**
   * Telepítse és konfigurálja [Azure Powershell](/powershell/azure/overview), és a következő parancsot:
     
       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```
     
     Mentse a tartomány nevét, szüksége lesz rá egy CNAME rekordot a létrehozásakor vagy metódus által visszaadott URL-címét használják.
2. Jelentkezzen be a DNS-regisztráló webhelyén, és a DNS kezelése lapon. Keressen hivatkozásokat vagy a hely címkézve területéhez **tartománynév**, **DNS**, vagy **neve kezelő**.
3. Most már található, ahol válassza ki vagy adja meg a CNAME meg. Előfordulhat, hogy a rekord típusát a legördülő menüből válassza le, vagy egy speciális beállítások lapot. A szavakat kell keresnie **CNAME**, **Alias**, vagy **altartományok**.
4. Meg kell adni a tartomány és altartomány alias a CNAME, például a **www** Ha létre szeretné **www.customdomain.com**. Ha azt szeretné, hozzon létre egy aliast a legfelső szintű tartomány, akkor elérhetőként a "**@**" szimbólumot lát, az a regisztráló DNS-eszközök.
5. Ezt követően meg kell adnia egy kanonikus nevet, amely az alkalmazás **cloudapp.net** tartomány ebben az esetben.

Például a következő CNAME-rekordot továbbítja származó összes forgalmat **www.contoso.com** való **contoso.cloudapp.net**, az egyéni tartománynév a központilag telepített alkalmazás:

| Alias és a gazdagép neve/altartomány | Canonical tartomány |
| --- | --- |
| www |contoso.cloudapp.NET |

> [!NOTE]
> A látogatói **www.contoso.com** nem érhető el a valódi állomás (contoso.cloudapp.net), így a továbbítási folyamatban a felhasználók nem látják.
> 
> A fenti példa csak vonatkozik-forgalom zárása a **www** altartomány. Helyettesítő karakterek nem használhatja a CNAME-rekordot, mert minden tartomány/altartomány egy CNAME REKORDOT kell létrehoznia. Ha szeretné-e a forgalom altartományt, például a közvetlen *. contoso.com, a cloudapp.net címet is konfigurálhat egy **átirányítási URL-cím** vagy **URL-cím előre** bejegyzést a DNS-beállításokat, vagy hozzon létre egy A rekordot.
> 
> 

## <a name="add-an-a-record-for-your-custom-domain"></a>Az egyéni tartomány az A rekord hozzáadása
Az A rekord létrehozásához először keresse meg a virtuális IP-cím a felhőalapú szolgáltatás. Majd adjon hozzá egy új bejegyzést a DNS-tábla az egyéni tartomány a regisztráló által biztosított eszközök segítségével. Minden tartományregisztráló egy A rekordot telepítésükhöz hasonló, csak metódust tartalmaz, de a koncepciók.

1. Az alábbi módszerek valamelyikével tud az IP-címet a felhőalapú szolgáltatás.
   
   * Jelentkezzen be a [Azure-portálon], válassza ki a felhőalapú szolgáltatás, tekintse meg a **Essentials** szakaszt, és keresse meg a **nyilvános IP-címek** bejegyzés.
     
       ![a VIP megjelenítő gyors áttekintő szakasz][vip]
     
       **VAGY**
   * Telepítse és konfigurálja [Azure Powershell](/powershell/azure/overview), és a következő parancsot:
     
       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```
     
     Az IP-cím mentése szüksége lesz rá egy A rekordot létrehozásakor.
2. Jelentkezzen be a DNS-regisztráló webhelyén, és a DNS kezelése lapon. Keressen hivatkozásokat vagy a hely címkézve területéhez **tartománynév**, **DNS**, vagy **neve kezelő**.
3. Most már található, ahol válasszon vagy adjon meg egy olyan rekordot. Előfordulhat, hogy a rekord típusát a legördülő menüből válassza le, vagy egy speciális beállítások lapot.
4. Válassza ki vagy írja be a tartomány vagy altartományt, amelyet ez A rekord fog használni. Válassza például **www** Ha létre szeretné **www.customdomain.com**. Ha szeretne létrehozni egy helyettesítő karakteres bejegyzést altartományokkal, adja meg az "x". Ez például lefedi altartományokkal **mail.customdomain.com**, **login.customdomain.com**, és **www.customdomain.com**.
   
    Ha szeretne létrehozni egy A rekordot, a legfelső szintű tartomány, akkor elérhetőként a "**@**" szimbólumot lát, az a regisztráló DNS-eszközök.
5. A megadott mezőben adja meg a felhőalapú szolgáltatás IP-címét. Ez társít a tartomány bejegyzést, az A rekord a cloud service-környezet az IP-cím szerepel.

Például a következő rekord származó összes forgalmat továbbítja **contoso.com** való **137.135.70.239**, a telepített alkalmazás IP-címe:

| Gazdagép neve/altartomány | IP-cím |
| --- | --- |
| @ |137.135.70.239 |

Ez a példa azt mutatja be, a legfelső szintű tartomány az A rekord létrehozása. Ha létre szeretne hozni egy helyettesítő karakteres bejegyzés fedik le az összes altartomány, "x", az altartomány.

> [!WARNING]
> IP-címek az Azure-ban alapértelmezés szerint dinamikus. Valószínűleg érdemes használni a [fenntartott IP-cím](../virtual-network/virtual-networks-reserved-public-ip.md) annak érdekében, hogy az IP-címe nem változik.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [A Cloud Services felügyelete](cloud-services-how-to-manage-portal.md)
* [CDN-tartalom leképezése egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md)
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure-portálon]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
