---
title: Tanúsítványok és a App Service Environment – Azure
description: A tanúsítványokkal kapcsolatos számos témakör ismertetése
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a8b8e7270851c71869b1a67f0f0f0ba2187f0e87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470673"
---
# <a name="certificates-and-the-app-service-environment"></a>Tanúsítványok és a App Service Environment 

A App Service Environment (bevezetési) az Azure-Virtual Network (VNet) belül futó Azure App Service központi telepítése. Üzembe helyezhető egy internetről elérhető alkalmazás-végponttal vagy egy VNet található alkalmazás-végponttal. Ha a központilag elérhető Internet-végponttal telepíti a bevezetést, akkor az üzembe helyezést külső kiegészítő szolgáltatásnak nevezzük. Ha a VNet egy végponttal helyezi üzembe a bevezetést, akkor az üzembe helyezést ILB-előállító-nek nevezzük. További információt a ILB a ILB-ben való [létrehozásáról és használatáról](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) című dokumentumban olvashat.

A szolgáltató egyetlen bérlői rendszer. Mivel ez egyetlen bérlő, a több-bérlős App Serviceban nem elérhetővé vált egy olyan szolgáltatás, amely nem érhető el. 

## <a name="ilb-ase-certificates"></a>ILB-tanúsítvány 

Ha külső betekintő szolgáltatást használ, akkor az alkalmazások a következő időpontban érhetők el: [AppName]. [asename]. p. azurewebsites. net. Alapértelmezés szerint az összes ASE, akár a ILB ASE is, az adott formátumot követő tanúsítványokkal jönnek létre. Ha van egy ILB-alapú beadása, az alkalmazások a ILB-előkészítés létrehozásakor megadott tartománynév alapján érhetők el. Ahhoz, hogy az alkalmazások támogassák az SSL-t, fel kell töltenie a tanúsítványokat. Szerezzen be egy érvényes SSL-tanúsítványt belső hitelesítésszolgáltatók használatával, külső kiállítótól származó tanúsítvány megvásárlása vagy önaláírt tanúsítvány használatával. 

A tanúsítványok a ILB-vel való konfigurálásának két lehetősége van.  Beállíthat egy helyettesítő karaktert a ILB beadásához, vagy beállíthat tanúsítványokat a központhoz tartozó egyes webalkalmazásokhoz.  A választott lehetőségtől függetlenül a következő tanúsítvány-attribútumokat megfelelően kell konfigurálni:

- **Tárgy:** Ezt az attribútumot a * értékre kell állítani. [a-root-domain-itt] a helyettesítő karakteres ILB-tanúsítványhoz. Ha létrehozta az alkalmazáshoz tartozó tanúsítványt, akkor a következőnek kell lennie: [AppName]. [a-root-domain-itt]
- **Tulajdonos alternatív neve:** Ennek az attribútumnak a következőket kell tartalmaznia: *. [a-root-domain-itt] és a *. SCM. [a-root-domain-itt] a helyettesítő karakteres ILB-tanúsítványhoz. Ha létrehozta az alkalmazáshoz tartozó tanúsítványt, akkor a következőnek kell lennie: [AppName]. [a-root-domain-itt] és [AppName]. SCM. [a-root-domain-itt].

Harmadik változatként létrehozhat egy olyan ILB-alapú betekintő tanúsítványt, amely a tanúsítvány SAN-beli összes egyéni alkalmazásának nevét tartalmazza, nem helyettesítő karakteres hivatkozással. Ennek a módszernek a problémája az, hogy ismernie kell a bevezetésben üzembe helyezett alkalmazások nevét, vagy meg kell őriznie a ILB-beléptetési tanúsítvány frissítését.

### <a name="upload-certificate-to-ilb-ase"></a>Tanúsítvány feltöltése a ILB-be 

Miután létrehozta a ILB-előállítók létrehozását a portálon, a tanúsítványt be kell állítani a ILB beadásához. Amíg a tanúsítvány be nem fejeződik, a beadási pont azt a szalagcímet jeleníti meg, amelyet a tanúsítvány nem állított be.  

A feltöltött tanúsítványnak. pfx formátumúnak kell lennie. A tanúsítvány feltöltése után a beadási szolgáltatás a tanúsítvány beállításához méretezési műveletet hajt végre. 

A bekészítés nem hozható létre, és nem tölthető fel a tanúsítvány egyetlen műveletként a portálon, vagy akár egy sablonban is. Külön műveletként feltöltheti a tanúsítványt egy sablon használatával, a következő témakörben leírtak szerint: a bekészítés a [sablon](./create-from-template.md) alapján dokumentum.  

Ha gyorsan szeretne létrehozni egy önaláírt tanúsítványt a teszteléshez, használhatja a következő PowerShell-t:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Önaláírt tanúsítvány létrehozásakor meg kell győződnie arról, hogy a tulajdonos nevének a CN = {ASE_NAME_HERE} _InternalLoadBalancingASE formátumúnak kell lennie.

## <a name="application-certificates"></a>Alkalmazás-tanúsítványok 

A szolgáltatói környezetben üzemeltetett alkalmazások használhatják a több-bérlős App Serviceban elérhető, app-centrikus tanúsítvány-szolgáltatásokat. Ezek a szolgáltatások a következők:  

- SNI-tanúsítványok 
- IP-alapú SSL, amely csak külső kiegészítő szolgáltatással támogatott.  Az ILB-alapú kiegészítő szolgáltatás nem támogatja az IP-alapú SSL-t.
- Kulcstartó által üzemeltetett tanúsítványok 

A tanúsítványok feltöltésével és kezelésével kapcsolatos utasítások az [SSL-tanúsítvány hozzáadása Azure app Serviceban](../configure-ssl-certificate.md)érhetők el.  Ha egyszerűen konfigurálja a tanúsítványokat úgy, hogy azok megfeleljenek a webalkalmazáshoz rendelt egyéni tartománynévnek, akkor ezek az utasítások elegendőek lesznek. Ha az alapértelmezett tartománynévvel tölti fel a tanúsítványt egy ILB-beadási webalkalmazáshoz, akkor az SCM-helyet a tanúsítvány TÁROLÓHÁLÓZATI helyén kell megadnia a korábban feljegyzett módon. 

## <a name="tls-settings"></a>TLS-beállítások 

A TLS-beállítást beállíthatja az alkalmazás szintjén.  

## <a name="private-client-certificate"></a>Magánhálózati ügyféltanúsítvány 

Gyakori felhasználási eset az, ha az alkalmazást ügyfélként konfigurálja ügyfél-kiszolgáló modellben. Ha a kiszolgálót privát HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal védi, fel kell töltenie az ügyféltanúsítványt az alkalmazásba.  A következő utasítások betöltik a tanúsítványokat annak a feldolgozónak a truststore, amelyen az alkalmazás fut. Ha egy alkalmazásba tölti be a tanúsítványt, azt a többi alkalmazással is használhatja ugyanazon a App Servicei csomagban anélkül, hogy újra kellene feltöltenie a tanúsítványt.

A tanúsítvány feltöltése az alkalmazásba a szolgáltatásban:

1. Állítson elő egy *. cer* fájlt a tanúsítványhoz. 
2. Nyissa meg azt az alkalmazást, amelynek szüksége van a tanúsítványra a Azure Portal
3. Nyissa meg az SSL-beállításokat az alkalmazásban. Kattintson a tanúsítvány feltöltése elemre. Válassza a nyilvános lehetőséget. Válassza a helyi gép lehetőséget. Adjon meg egy nevet. Tallózással keresse meg és válassza ki a *. cer* fájlt. Válassza a feltöltés lehetőséget. 
4. Másolja az ujjlenyomatot.
5. Válassza az Alkalmazásbeállítások lehetőséget. Hozzon létre egy WEBSITE_LOAD_ROOT_CERTIFICATES, amely az ujjlenyomatot értékként állítja be. Ha több tanúsítványa is van, akkor a beállításokat vesszővel elválasztva is elhelyezheti, és nem tartalmazhat szóközt (például 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

A tanúsítvány minden olyan alkalmazás számára elérhető lesz, amely ugyanabban az App Service-csomagban van, mint az adott beállítás. Ha azt szeretné, hogy a különböző App Service-csomagban lévő alkalmazásokhoz is elérhető legyen, akkor meg kell ismételnie az alkalmazás-beállítási műveletet egy alkalmazásban az adott App Service tervben. A tanúsítvány beállításának megadásához nyissa meg a kudu-konzolt, és adja ki a következő parancsot a PowerShell hibakeresési konzolján:

    dir cert:\localmachine\root

A tesztelés végrehajtásához létrehozhat egy önaláírt tanúsítványt, és létrehozhatja a *. cer* fájlt a következő PowerShell-lel: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

