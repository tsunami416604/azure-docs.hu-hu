---
title: Tanúsítványkötések
description: Ismertesse az App Service-környezet tanúsítványaival kapcsolatos számos témakört. Ismerje meg, hogyan működik a tanúsítványkötések az ase-i egybérlős alkalmazásokon.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba1d06ce83d50b6f0db84d1e423e66eae98f665d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477497"
---
# <a name="certificates-and-the-app-service-environment"></a>Tanúsítványok és az App Service-környezet 

Az App Service Environment(ASE) az Azure-alkalmazásszolgáltatás, amely az Azure virtuális hálózaton (VNet) fut. Telepíthető egy internetes elérhető alkalmazás-végpontvagy a virtuális hálózatban található alkalmazásvégpont. Ha telepíti az ASE egy internetes elérhető végpont, hogy a központi telepítés neve külső ASE. Ha üzembe helyezi az ASE egy végpontot a virtuális hálózatban, hogy a központi telepítés neve iLB ASE. Az ILB ASE-ről az [ILB ASE-dokumentum létrehozása és használata](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) című dokumentumból tudhat meg többet.

Az ASE egyetlen bérlői rendszer. Mivel egy bérlő, vannak olyan funkciók, amelyek csak egy ASE, amelyek nem érhetők el a több-bérlős App Service. 

## <a name="ilb-ase-certificates"></a>ILB ASE-tanúsítványok 

Ha külső ASE-t használ, akkor az alkalmazások elérése az [appname] helyen található. [asename].p.azurewebsites.net. Alapértelmezés szerint minden ASEs, még az ILB ASEs, jönnek létre a tanúsítványokat, amelyek követik ezt a formátumot. Ha rendelkezik egy ILB ASE, az alkalmazások elérése alapján a tartomány nevét, amelyet az ILB ASE létrehozásakor megadott. Ahhoz, hogy az alkalmazások támogassák a TLS-t, fel kell töltenie a tanúsítványokat. Szerezzen be érvényes TLS/SSL tanúsítványt belső hitelesítésszolgáltatók segítségével, külső kibocsátótól vásároljon tanúsítványt, vagy önaláírt tanúsítványt használjon. 

Az ILB ASE-vel kétféleképpen konfigurálhatja a tanúsítványokat.  Beállíthat egy helyettesítő karakter alapértelmezett tanúsítványt az ILB ASE-hez, vagy tanúsítványokat állíthat be az ASE egyes webalkalmazásaihoz.  A választott tól függetlenül a következő tanúsítványattribútumokat kell megfelelően konfigurálni:

- **Tárgy:** Ezt az attribútumot *-ra kell állítani. [a-root-domain-here] egy helyettesítő ILB ASE tanúsítványhoz. Ha létrehozza a tanúsítványt az alkalmazáshoz, akkor az legyen az [appname]. [a-root-domain-itt]
- **Tárgy Alternatív neve:** Ennek az attribútumnak mindkét *-ot tartalmaznia kell. [a-root-domain-here] és a *.scm. [a-root-domain-here] a helyettesítő ILB ASE tanúsítványhoz. Ha létrehozza a tanúsítványt az alkalmazáshoz, akkor az legyen az [appname]. [your-root-domain-here] és [appname].scm. [a-root-domain-here].

Harmadik változatként létrehozhat egy ILB ASE-tanúsítványt, amely helyettesítő karakter helyett az egyes alkalmazásneveket tartalmazza a tanúsítvány san-jában. A probléma ezzel a módszerrel az, hogy meg kell tudni, hogy elöl a nevét, hogy az alkalmazások, hogy az ASE, vagy meg kell tartani az ILB ASE tanúsítvány frissítése.

### <a name="upload-certificate-to-ilb-ase"></a>Tanúsítvány feltöltése az ILB ASE-be 

Miután létrejött egy ILB ASE a portálon, a tanúsítványt be kell állítani az ILB ASE-hez. Atanúsítvány beállításáig az ASE egy szalagcímet jelenít meg, amelyszerint a tanúsítvány nincs beállítva.  

A feltöltött tanúsítványnak .pfx fájlnak kell lennie. A tanúsítvány feltöltése után az ASE egy méretezési műveletet hajt végre a tanúsítvány beállításához. 

Az ASE nem hozható létre, és a tanúsítvány feltöltése egyetlen műveletként a portálon, vagy akár egy sablonban. Külön műveletként feltöltheti a tanúsítványt egy sablon használatával, ahogy az a [sablondokumentumból ase létrehozása](./create-from-template.md) című dokumentumban leírtak szerint szerepel.  

Ha gyorsan szeretne önaláírt tanúsítványt létrehozni a teszteléshez, a PowerShell következő bitjét használhatja:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Saját aláírású tanúsítvány létrehozásakor meg kell győződnie arról, hogy a tulajdonos neve CN={ASE_NAME_HERE}_InternalLoadBalancingASE formátumú.

## <a name="application-certificates"></a>Alkalmazási tanúsítványok 

Az ASE-ben üzemeltetett alkalmazások használhatják a több-bérlős App Service-ben elérhető alkalmazásközpontú tanúsítványfunkciókat. Ezek a funkciók a következők:  

- SNI-tanúsítványok 
- IP-alapú SSL, amely csak egy külső ASE támogatja.  Az ILB ASE nem támogatja az IP-alapú SSL-t.
- A KeyVault tárolt tanúsítványai 

A tanúsítványok feltöltésére és kezelésére vonatkozó utasítások az [Azure App Service TLS/SSL-tanúsítvány ának hozzáadása](../configure-ssl-certificate.md)című dokumentumban érhetők el.  Ha egyszerűen konfigurálja a tanúsítványokat, hogy megfeleljenek a webalkalmazáshoz rendelt egyéni tartománynévnek, akkor ezek az utasítások elegendőek. Ha az alapértelmezett tartománynévvel rendelkező ILB ASE webalkalmazás tanúsítványát tölti fel, akkor adja meg az scm-helyet a tanúsítvány san-ban, ahogy azt korábban említettük. 

## <a name="tls-settings"></a>TLS-beállítások 

A TLS-beállítást alkalmazásszinten konfigurálhatja.  

## <a name="private-client-certificate"></a>Privát ügyféltanúsítvány 

Gyakori használati eset az, hogy az alkalmazást ügyfél-kiszolgáló modellben ügyfélként konfigurálja. Ha a kiszolgálót saját hitelesítésszolgáltatói tanúsítvánnyal biztosítja, fel kell töltenie az ügyféltanúsítványt az alkalmazásba.  A következő utasítások betöltik a tanúsítványokat az on-t futtató dolgozók megbízhatósági tárába. Ha betölti a tanúsítványt egy alkalmazásba, használhatja azt a többi alkalmazással ugyanabban az App Service-csomagban anélkül, hogy újra feltöltenék a tanúsítványt.

A tanúsítvány feltöltése az alkalmazásba az ASE-ben:

1. Hozzon létre egy *.cer* fájlt a tanúsítványhoz. 
2. Nyissa meg azt az alkalmazást, amelynek szüksége van a tanúsítványra az Azure Portalon
3. Nyissa meg az SSL-beállításokat az alkalmazásban. Kattintson az Upload Certificate (Tanúsítvány feltöltése) parancsra. Válassza a Nyilvános lehetőséget. Válassza a Helyi számítógép lehetőséget. Adjon meg egy nevet. Tallózás és a *.cer* fájl kiválasztása. Válassza a feltöltés lehetőséget. 
4. Másolja az ujjlenyomatot.
5. Nyissa meg az Alkalmazás beállításai lehetőséget. Hozzon létre egy alkalmazásbeállítási WEBSITE_LOAD_ROOT_CERTIFICATES az ujjlenyomatértékkel. Ha több tanúsítvánnyal rendelkezik, akkor azokat ugyanebben a környezetben, vesszővel elválasztva helyezheti el, és nincs szóköz 

    84EC242A4EC7957817B8E48913E5095552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

A tanúsítvány elérhető lesz az összes alkalmazás ugyanabban az alkalmazásszolgáltatási csomag, mint az alkalmazás, amely konfigurálta ezt a beállítást. Ha szüksége van arra, hogy elérhető legyen egy másik App Service-csomagban lévő alkalmazásokhoz, meg kell ismételnie az alkalmazásbeállítási műveletet az adott App Service-csomagban. A tanúsítvány beállításának ellenőrzéséhez lépjen a Kudu konzolra, és adja ki a következő parancsot a PowerShell hibakeresési konzolján:

    dir cert:\localmachine\root

A teszteléshez létrehozhat egy önaláírt tanúsítványt, és létrehozhat egy *.cer* fájlt a következő PowerShell használatával: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

