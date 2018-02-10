---
title: "Az Azure App Service-környezetek létrehozása egy Resource Manager-sablon használatával"
description: "Ismerteti, hogyan egy külső vagy ILB Azure App Service-környezet létrehozása egy Resource Manager-sablon használatával"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 015bf031aea6b79fcca0a416253e9aa47bb245b6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Hozzon létre egy ASE Azure Resource Manager-sablon használatával

## <a name="overview"></a>Áttekintés
Az Azure App Service-környezetek (ASEs) az internetről elérhető végpontok vagy egy Azure virtuális hálózatot (VNet) belső cím végpontja hozhatók létre. A belső végpont létrehozásakor adott végpontra kerül az Azure által az összetevő egy belső terheléselosztón (ILB) nevű. A belső IP-cím ASE egy ILB ASE nevezik. Egy nyilvános végponttal ASE egy külső ASE nevezik. 

Egy ASE is létrehozható az Azure-portálon vagy az Azure Resource Manager-sablon használatával. Ez a cikk végigvezeti a lépéseken, és egy külső ASE vagy ILB ASE Resource Manager-sablonok létrehozásához szükséges szintaxist. Egy ASE létrehozása az Azure-portálon a további tudnivalókért lásd: [egy külső ASE ellenőrizze] [ MakeExternalASE] vagy [egy ILB ASE ellenőrizze][MakeILBASE].

Amikor létrehoz egy ASE az Azure portálon, a virtuális hálózat létrehozása egy időben, vagy válasszon egy már meglévő VNet és üzembe helyezés. Amikor egy ASE sablon alapján hoz létre, meg kell kezdődnie: 

* Egy erőforrás-kezelő virtuális hálózat.
* A virtuális alhálózat. Azt javasoljuk, hogy egy ASE alhálózat mérete `/25` biztosít a későbbi növekedéshez való 128 címekkel. A ASE létrehozása után nem módosíthatja a mérete.
* Az erőforrás-azonosítója a vnet. Ezt az információt lekérheti az Azure-portálon, a virtuális hálózati tulajdonságok alapján.
* Az üzembe helyezés kívánt előfizetést.
* Üzembe helyezés kívánt helyét.

A ASE létrehozásának automatizálása:

1. A ASE létrehozása sablonból. Ha létrehoz egy külső ASE, végzett elvégezte ezt a lépést. Ha létrehoz egy ILB ASE, nincsenek ehhez néhány dolgot.

2. A ILB ASE létrehozása után, amely megfelel a ILB ASE tartomány SSL-tanúsítvány feltöltése.

3. A feltöltött SSL-tanúsítvány hozzá van rendelve a ILB ASE az "alapértelmezett" SSL-tanúsítványt.  Ez a tanúsítvány használható SSL-forgalom a ILB ASE található alkalmazások használata a közös gyökértartomány, amely hozzá van rendelve a ASE (például https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>A ASE létrehozása
A Resource Manager-sablont, amely létrehoz egy ASE és a kapcsolódó paraméterfájl érhető [egy példa a] [ quickstartasev2create] a Githubon.

Ha engedélyezni szeretné egy ILB ASE, használja a Resource Manager-sablon [példák][quickstartilbasecreate]. Ezek elemeket, amelyek használati eset. A paraméterek a legtöbb a *azuredeploy.parameters.json* fájl megegyeznek az ILB ASEs és külső ASEs létrehozását. Az alábbi lista meghívja a kimenő paraméterek különleges megjegyzés, vagy az egyedi, amikor létrehoz egy ILB ASE:

* *internalLoadBalancingMode*: A legtöbb esetben a ASE meg az FTP-szolgáltatás által figyelt 3, ami azt jelenti, hogy a 80/443-as port HTTP/HTTPS-forgalmat, és a/adatok portok a készlet egy ILB lefoglalt virtuális hálózat belső társítani cím. Ha ez a tulajdonság értéke 2, csak az FTP szolgáltatással kapcsolatos portok (vezérlő és az adatokat egyaránt csatornák) ILB címnek vannak kötve. A HTTP/HTTPS-forgalmat a nyilvános VIP marad.
* *dnsSuffix*: Ez a paraméter határozza meg az alapértelmezett gyökértartomány, amely hozzá van rendelve a ASE. Az Azure App Service nyilvános változata, az alapértelmezett gyökértartomány minden webes alkalmazások nem *azurewebsites.net*. Mivel egy ILB ASE belső, az ügyfél virtuális hálózathoz, azt nem célszerű a használata a nyilvános service alapértelmezett legfelső szintű tartomány. Ehelyett egy ILB ASE kell egy alapértelmezett gyökértartomány legjobb a vállalat belső virtuális hálózaton belüli használatra. Például a Contoso Corporation használhatja az alapértelmezett legfelső szintű tartomány *belső contoso.com* célja, hogy feloldható, és csak a Contoso virtuális hálózaton belülről érhetők el alkalmazások. 
* *ipSslAddressCount*: Ez a paraméter alapértelmezett értéke automatikusan a 0 értéket a *azuredeploy.json* fájlhoz, mert ILB ASEs csak egyetlen ILB címmel rendelkezik. Nincsenek a egy ILB ASE explicit IP-SSL címek. Emiatt az IP-SSL-címkészletet egy ILB ASE a nulla értékre kell állítani. Ellenkező esetben a létesítési hiba történik. 

Miután a *azuredeploy.parameters.json* fájl ki van töltve, a ASE létrehozása a PowerShell kódrészletet használatával. Módosítsa az elérési utat kell egyeznie a Resource Manager sablon-fájlhelyek a számítógépen. Ne felejtse el az erőforrás-kezelő központi telepítés nevét és az erőforráscsoport neve a saját értéket ad meg:

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

A létrehozandó ASE egy órát vesz igénybe. Ezután a ASE megjelennek a portálon az előfizetés, a központi telepítés kiváltó ASEs listájában.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Töltse fel, és az "alapértelmezett" SSL-tanúsítvány konfigurálása
Az SSL-tanúsítvány alkalmazások SSL-kapcsolat létrehozásához használt "alapértelmezett" SSL-tanúsítványt kell rendelni, a mértékéig. Ha a ASE alapértelmezett DNS-utótagja *belső contoso.com*, https://some-random-app.internal-contoso.com kapcsolatot igényel, amely érvényes SSL-tanúsítvány **.internal-contoso.com*. 

Szerezzen be egy érvényes SSL-tanúsítvány belső hitelesítésszolgáltatók használatával, egy tanúsítvány beszerzése egy külső kiállítótól érkező, vagy önaláírt tanúsítványt használ. Az SSL-tanúsítvány forrását, függetlenül a következő tanúsítvány attribútumok megfelelően kell konfigurálni:

* **Tulajdonos**: meg kell ezt az attribútumot **.your-gyökér-tartományi-here.com*.
* **Tulajdonos alternatív neve**: ennek az attribútumnak kell tartalmaznia a **.your-gyökér-tartományi-here.com* és **.scm.your-gyökér-tartományi-here.com*. Minden alkalmazáshoz társított SCM/Kudu webhely SSL-kapcsolatot az űrlap cím használata *your-app-name.scm.your-root-domain-here.com*.

Egy érvényes SSL-tanúsítvánnyal aktuális két további előkészítő lépések szükségesek. Konvertálja/mentse az SSL-tanúsítványt .pfx fájlként. Ne feledje, hogy a .pfx fájl tartalmazza az összes köztes kell, és legfelső szintű tanúsítványok. Jelszóval gondoskodjon a védelméről.

A .pfx-fájlt kell a Base64 kódolású karakterlánc alakítja át, mivel az SSL-tanúsítvány feltöltése a Resource Manager-sablon használatával. Mivel Resource Manager-sablonok szövegfájlok, a .pfx fájl konvertálni kell egy Base64 kódolású karakterlánc. Ezzel a módszerrel azt is meg lehet adni a sablon paraméterként.

A következő PowerShell-kódrészletet a használja:

* Önaláírt tanúsítvány jön létre.
* Exportálja a tanúsítványt egy .pfx fájlba.
* A .pfx fájl átalakítása base64 kódolású karakterlánc.
* A base64 kódolású karakterlánc külön fájlt mentse. 

A PowerShell-kódot az alkalmazás base64 kódolást volt módosítani a a [PowerShell parancsfájlok blog][examplebase64encoding]:

        $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

        $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
        $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

        $fileName = "exportedcert.pfx"
        Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

        $fileContentBytes = get-content -encoding byte $fileName
        $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
        $fileContentEncoded | set-content ($fileName + ".b64")

Miután sikeresen generált és base64-kódolású karakterlánccá alakítja át az SSL-tanúsítvány, a példa a Resource Manager-sablon [az alapértelmezett SSL-tanúsítvány konfigurálása] [ quickstartconfiguressl] a Githubon. 

A paraméterek a *azuredeploy.parameters.json* fájl itt találhatók:

* *appServiceEnvironmentName*: a konfigurálni kívánt ILB ASE nevét.
* *existingAseLocation*: az Azure-régió, ahol a ILB ASE állított tartalmazó szöveges karakterlánc.  Például: "Déli középső Régiójában".
* *pfxBlobString*: a .pfx fájl a based64 kódolású karakterlánc-ábrázolása. A korábban bemutatott kódrészletet használja, és másolja át a "exportedcert.pfx.b64" foglalt karakterlánc. Illessze be az értékeként a *pfxBlobString* attribútum.
* *jelszó*: A jelszót a .pfx-fájlt védi.
* *certificateThumbprint*: A tanúsítvány ujjlenyomata. Ha ez az érték lekérése PowerShell (például *$certificate. Ujjlenyomat* a korábbi kódrészletet a), használhatja az érték, mert a. Értékének másolása a Windows tanúsítvány párbeszédpanel, ha fontos, hogy a fölösleges szóközök ki. A *certificateThumbprint* AF3143EB61D43F6727842115BB7F17BBCECAECAE hasonlóan kell kinéznie.
* *certificateName*: identitás a tanúsítvány segítségével A saját rövid karakterlánc-azonosító. A nevét használja egyedi erőforrás-kezelő azonosítója részeként a *Microsoft.Web/certificates* entitás, amely jelöli az SSL-tanúsítvány. A név *kell* végződhet a következő előtagot: \_yourASENameHere_InternalLoadBalancingASE. Az Azure-portálon a utótagot használja, mint azt jelzi, hogy a tanúsítvány használatával biztonságos egy ILB-kompatibilis ASE.

Rövidített például *azuredeploy.parameters.json* itt jelenik meg:

    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Miután a *azuredeploy.parameters.json* fájl ki van töltve, az alapértelmezett SSL-tanúsítvány konfigurálása a PowerShell kódrészletet használatával. Módosítsa a fájlelérési út egyezőre, hol található a Resource Manager sablon fájlokat a számítógépre. Ne felejtse el az erőforrás-kezelő központi telepítés nevét és az erőforráscsoport neve a saját értéket ad meg:

     $templatePath="PATH\azuredeploy.json"
     $parameterPath="PATH\azuredeploy.parameters.json"

     New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

A módosítás alkalmazására ASE előtér / nagyjából 40 percet vesz igénybe. Például az alapértelmezett méretű ASE, két előtér-webkiszolgálóinak használó, a sablon befejezéséhez körülbelül egy óra és 20 perc. A sablon futása közben a ASE nem lehet méretezni.  

A sablon befejezése után a ILB ASE alkalmazások HTTPS-KAPCSOLATON keresztül érhető el. A kapcsolatok biztonságáról az alapértelmezett SSL-tanúsítvány. Az alapértelmezett SSL-tanúsítvány akkor használatos, ha a az alkalmazás nevét, valamint az alapértelmezett állomásnév együttes használatával a ILB ASE alkalmazások tárgyalja. Például https://mycustomapp.internal-contoso.com használja az alapértelmezett SSL-tanúsítványt **.internal-contoso.com*.

Csakúgy, mint a nyilvános több-bérlős szolgáltatást a futó alkalmazások, azonban fejlesztők konfigurálható egyéni állomásnevek egyes alkalmazásokhoz. Egyedi SNI SSL-tanúsítványok kötései egyes alkalmazások esetében is konfigurálhatja.

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##
Kétféle verzió érhető el az App Service Environment szolgáltatáshoz: ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat.

Az ASEv1 kezelhetők a összes erőforrást manuálisan. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. Ki lehet terjeszteni a App Service-csomagot, mielőtt a feldolgozókészleten üzemeltetnie kívánt kell kiterjesztése.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Az előtér-webkiszolgálóinak vagy bármilyen számítási feladatot nem futtató munkavállalók használt Vcpu, amely tartalmazza. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. Az ASEv1 verzióról további információt az [App Service Environment v1 bemutatása][ASEv1Intro] témakörben találhat.

Egy ASEv1 létrehozása egy Resource Manager-sablon használatával, lásd: [hozzon létre egy ILB ASE v1 Resource Manager-sablon][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
