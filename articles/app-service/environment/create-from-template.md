---
title: A Resource Manager-sablon – az Azure App Service Environment-környezet létrehozása
description: Azt ismerteti, hogyan hozhat létre a külső vagy ILB Azure App Service environment egy Resource Manager-sablon használatával
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
ms.custom: seodec18
ms.openlocfilehash: bdf722ffa7a7c499ff256392886e0f229f27c7a5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109894"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Az ASE létrehozása Azure Resource Manager-sablon használatával

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Egy internethez csatlakozó végponttal vagy a végpont egy belső címet egy Azure virtuális hálózaton (VNet) az Azure App Service Environment (ASE) hozható létre. Egy belső végpont létrehozásakor, hogy a végpont kerül a Azure által az összetevő a belső terheléselosztó (ILB) nevű. A belső IP-cím az ASE ILB ASE nevezzük. Az ASE egy nyilvános végponttal rendelkező külső ASE nevezzük. 

Az ASE az Azure Portalon vagy Azure Resource Manager-sablon segítségével hozható létre. Ez a cikk ismerteti a lépéseket és a egy külső ASE vagy ILB ASE Resource Manager-sablonokkal való létrehozásához szükséges szintaxist. Az ASE létrehozása az Azure Portalon kapcsolatban lásd: [győződjön meg arról, a külső ASE] [ MakeExternalASE] vagy [győződjön meg arról, az ILB ASE][MakeILBASE].

Az ASE az Azure Portalon hoz létre, amikor a virtuális hálózat létrehozásához egy időben, vagy válasszon egy már létező virtuális hálózathoz való üzembe helyezéséhez. Ha az ASE létrehozása sablonból, a kezdéshez: 

* Resource Manager virtuális hálózathoz.
* Egy alhálózatot az adott virtuális hálózatban. Azt javasoljuk, hogy az ASE alhálózat méretét `/24` 256 címekkel, a későbbi növekedéshez és skálázási igényeihez. Az ASE létrehozását követően a mérete nem módosítható.
* Az erőforrás-azonosítója a virtuális hálózatról. Ezt az információt az Azure Portalról alatt a virtuális hálózati tulajdonságok beolvasása.
* Az üzembe helyezése a kívánt előfizetést.
* A való üzembe helyezése a kívánt helyre.

Automatizálhatja az ASE létrehozása:

1. Az ASE létrehozása sablonból. Külső ASE létrehozása, ha elkészült a lépés után. ILB ASE létrehozása, ha nincsenek ehhez néhány dolgot.

2. Az ILB ASE létrehozása után, amely megfelel az ILB ASE tartomány SSL-tanúsítvány feltöltése.

3. A feltöltött SSL-tanúsítvány hozzá van rendelve az ILB ASE az "alapértelmezett" SSL-tanúsítványt.  Ez a tanúsítvány használható SSL az ILB ASE alkalmazások felé irányuló forgalom a közös gyökértartomány, amely van rendelve az ASE használata esetén (például https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Az ASE létrehozása
A Resource Manager-sablon, amely létrehoz egy ASE Környezethez és a kapcsolódó paraméterfájl érhető el [egy példa a] [ quickstartasev2create] a Githubon.

Ha azt szeretné, hogy az ILB ASE környezetben, használja a Resource Manager-sablon [példák][quickstartilbasecreate]. Ezek méretformátumok figyelembe vétele, amely a kis-és nagybetűhasználattal. A paraméterek a legtöbb a *azuredeploy.parameters.json* fájl közösek az ILB ASE és a külső ASE létrehozását. Az alábbi lista felhívja paraméterek különösen fontos, vagy az egyedi, az ILB ASE létrehozásakor:

* *internalLoadBalancingMode*: A legtöbb esetben az FTP-szolgáltatás, az ASE által figyelt ezt a 3, ami azt jelenti, hogy a HTTP/HTTPS-forgalmat a 80-as/443-as portokon, és a vezérlő/adatok portok beállítása társítani kívánt belső ILB-lefoglalt virtuális hálózati címhez. Ha ez a tulajdonság értéke 2, csak az FTP szolgáltatással kapcsolatos portok (egyaránt vezérlési és csatornák) ILB-címmel vannak kötve. A HTTP/HTTPS-forgalmat a nyilvános virtuális IP-cím nem marad.
* *dnsSuffix*: Ez a paraméter határozza meg az alapértelmezett legfelső szintű tartományt, amely az ASE van rendelve. Az Azure App Service-ben nyilvános változata, az alapértelmezett gyökértartomány esetében az összes webes alkalmazások van *azurewebsites.net*. Mivel az ILB ASE környezetben egy ügyfél virtuális hálózatán belüli, a nyilvános service alapértelmezett legfelső szintű tartományt használja, hogy nincs értelme. Ehelyett az ILB ASE rendelkeznie kell egy alapértelmezett legfelső szintű tartományt, amely logikus a használatra a vállalat belső virtuális hálózaton belül. Contoso Corporation például használhatja az alapértelmezett gyökértartomány *belső contoso.com* az alkalmazásokhoz, melyek nem oldható fel és csak a Contoso virtuális hálózaton belül elérhető. 
* *ipSslAddressCount*: Ez a paraméter alapértelmezett értéke automatikusan a 0 értéket a *azuredeploy.json* fájlhoz, mert az ILB ASE egy ILB-címmel rendelkezik. Nincsenek explicit IP-SSL címek ILB ASE esetében. Ezért az ILB ASE IP SSL-címkészletet kell beállítani a nulla. Ellenkező esetben egy üzembe helyezési hiba történik. 

Miután a *azuredeploy.parameters.json* fájl ki van töltve, az ASE létrehozása a PowerShell-kódrészlet használatával. A Resource Manager-sablonfájl helyek a gépen megfelelően a elérési útjának módosítása. Ne felejtse adja meg a saját a Resource Manager üzembe helyezési nevét és az erőforráscsoport neve:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Az ASE létrehozását egy órát vesz igénybe. Ezután az ASE megjelenik-e a listában, az ASE az előfizetés, amely kiváltotta az üzembe helyezés a portálon.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Töltse fel, és az "alapértelmezett" SSL-tanúsítvány konfigurálása
SSL-tanúsítvány az ASE társítva kell lennie az "alapértelmezett" SSL-tanúsítvány, amely alkalmazások SSL-kapcsolat létesítésére szolgál. Ha az ASE alapértelmezett DNS-utótag *belső contoso.com*, kapcsolatot https://some-random-app.internal-contoso.com érvényes SSL-tanúsítvány szükséges **.internal-contoso.com*. 

Szerezze be egy érvényes SSL-tanúsítvány használatával a belső tanúsítványszolgáltatót, vásárol egy tanúsítványt külső kiállítótól vagy egy önaláírt tanúsítvány használatával. Az SSL-tanúsítvány forrásától függetlenül a következő tanúsítvány attribútumok megfelelően kell konfigurálni:

* **Tulajdonos**: Ezt az attribútumot állítsa **.az-gyökér-domain-here.com*.
* **Tulajdonos alternatív neve**: Ennek az attribútumnak tartalmaznia kell mindkét **.az-gyökér-domain-here.com* és **.az-gyökér-domain-here.com*. Az SCM/Kudu helyhez társított SSL-kapcsolatok használata a képernyő címének *your-app-name.scm.your-root-domain-here.com*.

Az érvényes SSL-tanúsítványt az aktuális két további előkészítő lépések szükségesek. Konvertálja/mentse az SSL-tanúsítványt .pfx fájlként. Ne feledje, hogy a .pfx-fájlt kell összes köztes és főtanúsítványok. Jelszóval gondoskodjon a védelméről.

A .pfx-fájlt kell base64-karakterlánc konvertálva, mert az SSL-tanúsítvány feltöltése egy Resource Manager-sablon használatával. Mivel a Resource Manager-sablonok szöveges fájlok, a .pfx-fájlt Base64 kódolású karakterláncnak kell konvertálni. Ezzel a módszerrel azt is meg lehet adni a sablon paraméterként.

Az alábbi PowerShell kódrészletet használja:

* Létrehozhat egy önaláírt tanúsítványt.
* Exportálja a tanúsítványt egy .pfx fájlba.
* A .pfx-fájl átalakítása base64-kódolású karakterlánc.
* Mentse a base64-kódolású karakterlánc egy különálló fájlban. 

A PowerShell-kódot a base64 kódolás volt a igazítani a [PowerShell-szkriptek blog][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Miután az SSL-tanúsítvány sikeresen létrehozott és base64-kódolású karakterlánccá alakítja, a példában a Resource Manager-sablon [az alapértelmezett SSL-tanúsítvány konfigurálása] [ quickstartconfiguressl] a Githubon. 

A paramétereket a *azuredeploy.parameters.json* fájl itt találhatók:

* *appServiceEnvironmentName*: Az ILB ASE konfigurált neve.
* *existingAseLocation*: Az Azure-régióban, az ILB ASE környezetben telepített tartalmazó szöveges karakterlánc.  Példa: "USA déli középső RÉGIÓJA".
* *pfxBlobString*: A .pfx-fájlt based64-kódolású karakterlánc-ábrázolása. A korábban bemutatott kódrészletet használja, és másolja a "exportedcert.pfx.b64" foglalt karakterlánc. Illessze be az értéket a *pfxBlobString* attribútum.
* *Jelszó*: A .pfx fájl védelméhez használt jelszó.
* *certificateThumbprint*: A tanúsítvány ujjlenyomata. Ha ez az érték lekérése PowerShell (például *$certificate. Ujjlenyomat* a korábbi kódrészlet), az értéket, használhatja. Ha a Windows-tanúsítvány párbeszédpanelről másolja az értéket, ne feledje, távolítsa el a felesleges szóközöket. A *certificateThumbprint* AF3143EB61D43F6727842115BB7F17BBCECAECAE hasonlóan kell kinéznie.
* *certificateName*: Identitás a tanúsítvány használja saját kiválasztása egy rövid karakterlánc-azonosító. A név szolgál az erőforrás-kezelő egyedi azonosítóját részeként a *Microsoft.Web/certificates* entitás, amely az SSL-tanúsítvány jelöli. A név *kell* a következő utótaggal végződik: \_yourASENameHere_InternalLoadBalancingASE. Az Azure Portalon ezt az utótagot használja azt, hogy a tanúsítvány egy ILB-kompatibilis az ASE védelme használható.

Rövidített például *azuredeploy.parameters.json* itt látható:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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
```

Miután a *azuredeploy.parameters.json* fájl ki van töltve, az alapértelmezett SSL-tanúsítvány konfigurálása a PowerShell-kódrészlet használatával. Hol található a gépen a Resource Manager-sablonfájlokat megfelelően a elérési útjának módosítása. Ne felejtse adja meg a saját a Resource Manager üzembe helyezési nevét és az erőforráscsoport neve:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A módosítás alkalmazása ASE előtér / körülbelül 40 percet vesz igénybe. Például két előtérrendszerek használó alapértelmezett méretű ASE a sablon körülbelül egy óra és 20 percet vesz igénybe végrehajtásához. A sablon futása közben az ASE nem skálázhatja.  

A sablon befejezését követően az ILB ASE alkalmazások HTTPS-kapcsolaton keresztül érhető el. A kapcsolatok biztonságosak, az alapértelmezett SSL-tanúsítvány használatával. Az alapértelmezett SSL-tanúsítványt használt az ILB ASE alkalmazásokat az alkalmazás nevét, valamint az alapértelmezett állomásnév együttes használatával foglalkozik. Ha például https://mycustomapp.internal-contoso.com használja az alapértelmezett SSL-tanúsítványt **.internal-contoso.com*.

Csakúgy, mint a nyilvános több-bérlős szolgáltatás rendszeren futtatott alkalmazások, azonban a fejlesztők konfigurálhatja az egyes alkalmazások egyéni állomásnevek. Egyedi SNI SSL-tanúsítványok kötései az egyes alkalmazások is konfigurálhatja.

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##
App Service Environment-környezet két verziója van: Az ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat.

Az asev1-ben kezelheti az összes olyan erőforrást manuálisan. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. Ki lehet terjeszteni az App Service-csomag, mielőtt ki kell terjeszteni a az üzemeltetni kívánt feldolgozókészlet.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Ez tartalmazza a vcpu-k által használt és a feldolgozókhoz, amelyek nem üzemeltetnek számítási feladatokat. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. Az ASEv1 verzióról további információt az [App Service Environment v1 bemutatása][ASEv1Intro] témakörben találhat.

Az ASEv1 létrehozása egy Resource Manager-sablon használatával: [egy ILB ASE v1 létrehozása Resource Manager-sablonnal][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
