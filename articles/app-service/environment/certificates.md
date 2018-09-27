---
title: Tanúsítványok és az Azure App Service-környezet
description: Az ASE-tanúsítványokkal kapcsolatos számos témakörök ismertetik.
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.openlocfilehash: 3d417d560d8a88100f31def27c7db5f9b2493062
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47167983"
---
# <a name="certificates-and-the-app-service-environment"></a>Tanúsítványok és az App Service-környezet 

Az App Service Environment az üzemelő példánya az Azure App Service, amely az Azure virtuális hálózat (vnet) belül. Internetes alkalmazás elérhető-e a végpont vagy egy alkalmazás-végpontot, amely a virtuális hálózatban található is telepíthető. Ha az ASE az internetről elérhető végpont telepít, a telepítés külső ASE neve. Ha az ASE-végponttal rendelkező központi telepítését a virtuális hálózat, a telepítés az ILB ASE neve. További információ az ILB ASE a a [létrehozása és használata az ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) dokumentumot.

Az ASE egy egybérlős rendszer. Mivel egyetlen új bérlő, érhetők el egyes funkciói csak a, amelyek nem érhetők el a több-bérlős App Service-ben az ASE. 

## <a name="ilb-ase-certificates"></a>ILB ASE tanúsítványok 

Ha külső ASE környezetben használja, majd az alkalmazások elérésekor: [alkalmazásnév]. [asename]. p.azurewebsites.net. Alapértelmezés szerint minden ASE, még akkor is, az ILB ASE, jönnek létre, amely követi, hogy tanúsítványokkal. Ha az ILB ASE rendelkezik, az alkalmazások elérésekor az ILB ASE létrehozásakor megadott tartománynév alapján. Ahhoz, hogy az alkalmazásokat az SSL támogatására, a szükséges tanúsítványok feltöltése. Szerezze be egy érvényes SSL-tanúsítvány használatával a belső tanúsítványszolgáltatót, vásárol egy tanúsítványt külső kiállítótól vagy egy önaláírt tanúsítvány használatával. 

Tanúsítványok konfigurálása az ILB ASE esetében két lehetőség van.  Alapértelmezett helyettesítő tanúsítvány beállítása az ILB ASE környezetnek, vagy állítsa be a tanúsítványok az egyes webalkalmazások az ASE-t.  A választott, függetlenül a következő tanúsítvány attribútumok megfelelően kell konfigurálni:

- **Tulajdonos:** ezt az attribútumot állítsa *. [ a gyökér--Ön-gyökértartománya] ILB ASE környezetben helyettesítő tanúsítványt. Ha az alkalmazáshoz tartozó tanúsítványt létrehozni, azt [alkalmazásnév] kell lennie. [a gyökér--Ön-gyökértartománya]
- **Tulajdonos alternatív neve:** ennek az attribútumnak tartalmaznia kell mindkét *. [ a gyökér--Ön-gyökértartománya] és a *.scm. [a gyökér--Ön-gyökértartománya] esetében az ILB ASE környezetben helyettesítő tanúsítványt. Ha az alkalmazáshoz tartozó tanúsítványt létrehozni, azt [alkalmazásnév] kell lennie. [a gyökér--Ön-gyökértartománya] és [alkalmazásnév] .scm. [a gyökér--Ön-gyökértartománya].

Egy harmadik változó, amely tartalmazza az összes az egyedi alkalmazásnevek a tulajdonos alternatív nevének helyettesítő hivatkozás használata helyett a tanúsítvány egy ILB ASE tanúsítvány is létrehozhat. A probléma merült fel ez a módszer, hogy az alkalmazásokat, akkor az ASE helyeznek nevei meghozni ismernie kell, vagy az ILB ASE tanúsítvány folyamatosan frissíteni kell.

### <a name="upload-certificate-to-ilb-ase"></a>Az ILB ASE-tanúsítvány feltöltése 

A portálon az ILB ASE létrehozása után a tanúsítványt az ILB ASE kell beállítani. Mindaddig, amíg a tanúsítvány van beállítva, az ASE megjelenik egy szalagcím, hogy a tanúsítvány nem lett-e beállítva.  

A feltöltött tanúsítvány .pfx fájlnak kell lennie. Miután feltöltötte a tanúsítványt, az ASE végrehajtja a tanúsítványt egy skálázási művelet. 

Nem lehet az ASE létrehozása, és töltse fel a tanúsítványt a portálon, vagy akár egy sablon egy művelet. Külön műveletként, töltse fel a sablon használatával, a tanúsítványt a [ASE létrehozása sablonból](./create-from-template.md) dokumentumot.  

Ha szeretne egy gyors teszteléshez önaláírt tanúsítvány létrehozása, a következő bit PowerShell használhatja:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     


## <a name="application-certificates"></a>Alkalmazás-tanúsítványok 

Az ASE környezetben futó alkalmazások az alkalmazás-központú tanúsítvány funkciók, a több-bérlős App Service-ben elérhető is használhatók. Ezek a funkciók a következők:  

- SNI-tanúsítványok 
- IP-alapú SSL-t, amely csak a külső ASE támogatott.  Az ILB ASE nem támogatja az IP-alapú SSL.
- Futó KeyVault-tanúsítványok 

A következő útmutatót: feltöltése és tanúsítványok kezelése érhetők el az App Service SSL-oktatóanyag https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl.  Ha egyszerűen konfigurál egy egyéni tartománynevet, amely a webalkalmazás rendelt megfelelő tanúsítványokat, majd ezeket az utasításokat adnak. Ha tölt fel a tanúsítvány egy ILB ASE-webalkalmazást az alapértelmezett tartomány nevét, majd adja meg az az scm helyet a SAN tanúsítvány korábban feljegyzett. 

## <a name="tls-settings"></a>A TLS-beállítások 

A TLS-beállítás konfigurálása az alkalmazás szintjén.  

## <a name="private-client-certificate"></a>Privát ügyféltanúsítvány 

Egy gyakori alkalmazási helyzet, hogy az alkalmazás konfigurálása az ügyfél és kiszolgáló modellben-ügyfélként. Ha a kiszolgáló és az egy privát Hitelesítésszolgáltatói tanúsítvány védi, szüksége lesz az ügyfél-tanúsítvány feltöltése az alkalmazáshoz.  Az alábbi utasítások alapján tölti be a truststore a munkavállalók, amelyen az alkalmazás fut, a tanúsítványok. Ha a tanúsítvány egy alkalmazás betölt, használhatja a más alkalmazásokkal az App Service-csomag nélkül újból feltölteni a tanúsítványt.

A tanúsítvány feltöltése az alkalmazáshoz, az ASE környezetben:

1. Hozzon létre egy *.cer* fájlt a tanúsítványhoz. 
2. Nyissa meg az alkalmazást, amelyet a tanúsítvány az Azure Portalon
3. Nyissa meg az SSL-beállítások az alkalmazásban. Kattintson a tanúsítvány feltöltése. A nyilvános. Válassza ki a helyi gépen. Adjon meg egy nevet. Keresse meg és válassza a *.cer* fájlt. Feltöltés kiválasztása. 
4. Másolja az ujjlenyomatot.
5. Nyissa meg az alkalmazás beállításait. Hozzon létre egy alkalmazás beállítás WEBSITE_LOAD_ROOT_CERTIFICATES az ujjlenyomat értéket. Ha több tanúsítvány van, akkor lehet helyezni őket elválasztva vesszővel válassza el egymástól, és nincs szóköz, például ugyanazon beállítás 

    84EC242A4EC7957817B8E48913E50953552DAFA6, 6A5C65DC9247F762FE17BF8D4906E04FE6B31819

A tanúsítvány azonos app service-csomag az alkalmazás, amelynek a beállítást, az összes alkalmazás számára elérhető lesz. Ha kell, hogy egy másik App Service-csomag alkalmazásokhoz érhető el, szüksége lesz a ismételje meg az Alkalmazásbeállítás művelet az alkalmazás az App Service-csomag. Annak ellenőrzéséhez, hogy van-e beállítva a tanúsítványt, nyissa meg a Kudu konzol, és adja ki a parancsot a dir parancs cert: \localmachine\root hibakeresési PowerShell-konzolján. 

Tesztek elvégzéséhez egy önaláírt tanúsítvány létrehozása és hozzon létre egy *.cer* fájl az alábbi PowerShell-lel: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

