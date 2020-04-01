---
title: ASE létrehozása ARM-tal
description: Ismerje meg, hogyan hozhat létre egy külső vagy ILB App Service-környezet egy Azure Resource Manager sablon használatával.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e06fcdbac097e85c039e34274c61cb51ee06bcd6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478326"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>ASE létrehozása Azure Resource Manager-sablon használatával

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure App Service-környezetek (ASE-k) egy interneten elérhető végpontvagy egy belső cím egy Azure virtuális hálózat (VNet) egy végpont. Belső végponttal jön létre, hogy a végpont ot egy belső terheléselosztónak (ILB) nevezett Azure-összetevő biztosítja. A belső IP-cím ASE-t ILB ASE-nek nevezzük. A nyilvános végponttal rendelkező ASE-t külső ASE-nek nevezzük. 

Az ASE az Azure Portal vagy egy Azure Resource Manager-sablon használatával hozható létre. Ez a cikk bemutatja azokat a lépéseket és szintaxist, amelyekkel külső ASE-t vagy ILB ASE-t kell létrehoznia az Erőforrás-kezelő sablonokkal. Ha tudni szeretné, hogyan hozhat létre ASE-t az Azure Portalon, olvassa el a [Külső ASE létrehozása][MakeExternalASE] vagy [az ILB ASE létrehozása című témakört.][MakeILBASE]

Amikor létrehoz egy ASE-t az Azure Portalon, létrehozhatja a virtuális hálózatot egyszerre, vagy választhat egy már meglévő virtuális hálózatot, amelybe üzembe szeretne helyezni. Amikor létrehoz egy ASE-t egy sablonból, a következőkkel kell kezdenie: 

* Egy erőforrás-kezelő virtuális hálózat.
* A virtuális hálózat egyik alhálózata. Javasoljuk, hogy az ASE-alhálózat mérete 256 `/24` címa jövőbeli növekedési és skálázási igények kielégítésére. Az ASE létrehozása után nem módosíthatja a méretet.
* Az erőforrás-azonosító a virtuális hálózatból. Ezeket az információkat az Azure Portalon a virtuális hálózati tulajdonságok alatt kaphatja meg.
* Az az előfizetés, amelybe telepíteni szeretné a rendszert.
* Az a hely, ahhoz a helyhez, ahol telepíteni szeretné.

Az ASE létrehozásának automatizálása:

1. Hozza létre az ASE-t egy sablonból. Ha külső ASE-t hoz létre, a lépés után elkészült. Ha létrehoz egy ILB ASE-t, még néhány lehetőség van.

2. Az ILB ASE létrehozása után feltölt egy TLS/SSL tanúsítványt, amely megfelel az ILB ASE-tartománynak.

3. A feltöltött TLS/SSL tanúsítvány "alapértelmezett" TLS/SSL tanúsítványként van hozzárendelve az ILB ASE-hez.  Ez a tanúsítvány az ILB ASE-n lévő alkalmazások TLS/SSL-forgalmára szolgál, amikor az ASE-hez rendelt közös gyökértartományt használják `https://someapp.mycustomrootdomain.com`(például).


## <a name="create-the-ase"></a>Az ASE létrehozása
Egy Erőforrás-kezelő sablon, amely létrehoz egy ASE és a kapcsolódó paraméterek fájl érhető el [egy példában][quickstartasev2create] a GitHubon.

Ha ILB ASE-t szeretne készíteni, használja az alábbi Erőforrás-kezelő [sablonpéldákat.][quickstartilbasecreate] Ők gondoskodnak a használati esetről. Az *azuredeploy.parameters.json* fájl ban lévő paraméterek többsége közös az ILB ASEs és a külső ASE-k létrehozásában. Az alábbi lista az ILB ASE létrehozásakor egyedi vagy egyedi paramétereket hív meg:

* *internalLoadBalancingMode*: A legtöbb esetben 3-ra állítja ezt, ami azt jelenti, hogy a 80/443-as portokon a HTTP/HTTPS-forgalom és az ASE FTP-szolgáltatásáltal figyelt vezérlő/adatcsatorna portjai egy ILB által lefoglalt belső hálózati címhez lesznek kötve. Ha ez a tulajdonság 2-re van állítva, csak az FTP-szolgáltatással kapcsolatos portok (vezérlő- és adatcsatornák) lesznek ILB-címhez kötve. The HTTP/HTTPS traffic remains on the public VIP.
* *dnsSuffix*: Ez a paraméter határozza meg az ASE-hez rendelt alapértelmezett gyökértartományt. Az Azure App Service nyilvános változatában az összes webalkalmazás alapértelmezett gyökértartománya *azurewebsites.net.* Mivel az ILB ASE az ügyfél virtuális hálózatán belül van, nincs értelme a közszolgálat alapértelmezett gyökértartományát használni. Ehelyett az ILB ASE-nek rendelkeznie kell egy alapértelmezett gyökértartománnyal, amely a vállalat belső virtuális hálózatán belül használható. Például a Contoso Corporation használhatja a *internal-contoso.com* alapértelmezett gyökértartományát olyan alkalmazásokhoz, amelyek feloldhatók és csak a Contoso virtuális hálózatán belül érhetők el. 
* *ipSslAddressCount*: Ez a paraméter automatikusan 0-ra alapértelmezés szerint 0-ra van az *azuredeploy.json* fájlban, mert az ILB ASEs csak egyetlen ILB-címmel rendelkezik. Az ILB ASE-hez nincsenek explicit IP-SSL-címek. Ezért az ILB ASE IP-SSL címkészletét nullára kell állítani. Ellenkező esetben kiépítési hiba lép fel. 

Az *azuredeploy.parameters.json* fájl kitöltése után hozza létre az ASE-t a PowerShell-kódkódrészlet használatával. Módosítsa a fájl elérési útjait úgy, hogy azok megfeleljenek a számítógépen lévő Erőforrás-kezelő sablonfájl-fájl helyeinek. Ne felejtse el megadni a saját értékeit az Erőforrás-kezelő központi telepítésének nevéhez és az erőforráscsoport nevéhez:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Körülbelül egy órát vesz igénybe az ASE létrehozása. Ezután az ASE megjelenik a portálon az üzembe helyezést kiváltó előfizetés ase-k listájában.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Az "alapértelmezett" TLS/SSL tanúsítvány feltöltése és konfigurálása
A TLS/SSL-tanúsítványt az ASE-hez kell társítani az alkalmazásokhoz tls-kapcsolatok létrehozásához használt "alapértelmezett" TLS/SSL tanúsítványként. Ha az ASE alapértelmezett DNS-utótagja *internal-contoso.com*, `https://some-random-app.internal-contoso.com` a kapcsolathoz **.internal-contoso.com*. 

Szerezzen be érvényes TLS/SSL tanúsítványt belső hitelesítésszolgáltatók segítségével, külső kibocsátótól vásároljon tanúsítványt, vagy önaláírt tanúsítványt használjon. A TLS/SSL tanúsítvány forrásától függetlenül a következő tanúsítványattribútumokat kell megfelelően konfigurálni:

* **Tárgy**: Ezt az attribútumot **.your-root-domain-here.com*-re kell állítani.
* **Tulajdonos alternatív neve:** Ennek az attribútumnak tartalmaznia kell a **.your-root-domain-here.com* és a **.scm.your-root-domain-here.com*. Az egyes alkalmazásokhoz társított SCM/Kudu webhelyhez létesített TLS-kapcsolatok az űrlap *your-app-name.scm.your-root-domain-here.com*címét használják.

Az érvényes TLS/SSL tanúsítvánnyal két további előkészítő lépésre van szükség. Konvertálja/mentse a TLS/SSL tanúsítványt .pfx fájlként. Ne feledje, hogy a .pfx fájlnak tartalmaznia kell az összes köztes és főtanúsítványt. Jelszóval gondoskodjon a védelméről.

A .pfx fájlt base64 karakterláncc-é kell konvertálni, mert a TLS/SSL tanúsítvány feltöltése Erőforrás-kezelő sablon használatával történik. Mivel az Erőforrás-kezelő sablonok szöveges fájlok, a .pfx fájlt base64 karakterláncsá kell konvertálni. Így a sablon paramétereként is szerepelhet.

Használja a következő PowerShell-kódrészletet a következőműveletekhez:

* Önaláírt tanúsítvány létrehozása.
* Exportálja a tanúsítványt .pfx fájlként.
* Konvertálja a .pfx fájlt base64 kódolású karakterláncsá.
* Mentse a base64 kódolású karakterláncot egy külön fájlba. 

Ez a Base64 kódoláshoz való PowerShell-kód a [PowerShell-parancsfájlok blogjáról][examplebase64encoding]lett adaptálva:

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

Miután a TLS/SSL tanúsítvány sikeresen létrejött és base64 kódolású karakterlánctá konvertált, használja a példa Erőforrás-kezelő [sablont Az alapértelmezett SSL-tanúsítvány konfigurálása a][quickstartconfiguressl] GitHubon. 

Az *azuredeploy.parameters.json* fájl paraméterei itt találhatók:

* *appServiceEnvironmentName*: A konfigurált ILB ASE neve.
* *existingAseLocation*: Az ILB ASE üzembe helyezésének helyét tartalmazó Szöveges karakterlánc.  Például: "Dél-Közép-USA".
* *pfxBlobString*: A .pfx fájl based64 kódolású karakterlánc-ábrázolása. Használja a korábban bemutatott kódrészletet, és másolja az "exportedcert.pfx.b64" mezőben található karakterláncot. Illessze be a *pfxBlobString* attribútum értékeként.
* *jelszó*: A .pfx fájl védelméhez használt jelszó.
* *tanúsítványThumbprint*: A tanúsítvány ujjlenyomata. Ha ezt az értéket a PowerShellből olvassa be (például *$certificate. Ujjlenyomat* a korábbi kódrészletből), használhatja az értéket a hogy van. Ha az értéket a Windows tanúsítvány párbeszédpaneléről másolja, ne felejtse el kitépni a felesleges szóközöket. A *tanúsítványThumbprint* kell kinéznie AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: A tanúsítvány identitásához használt, saját maga által választott rövid karakterlánc-azonosító. A név a TLS/SSL tanúsítványt képviselő *Microsoft.Web/certificates* entitás egyedi Erőforrás-kezelő-azonosítójának részeként használatos. A *névnek* a következő utótaggal kell végződnie: \_yourASENameHere_InternalLoadBalancingASE. Az Azure Portal ezt az utótagot használja annak jelzésére, hogy a tanúsítvány az ILB-kompatibilis ASE védelmére szolgál.

Az *azuredeploy.parameters.json* rövidített példája itt látható:

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

Az *azuredeploy.parameters.json* fájl kitöltése után konfigurálja az alapértelmezett TLS/SSL tanúsítványt a PowerShell-kódkódrészlet használatával. Módosítsa a fájl elérési útjait úgy, hogy azok megegyeznek az Erőforrás-kezelő sablonfájljainak helyével a számítógépen. Ne felejtse el megadni a saját értékeit az Erőforrás-kezelő központi telepítésének nevéhez és az erőforráscsoport nevéhez:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A módosítás alkalmazása ase előlaponként nagyjából 40 percet vesz igénybe. Például egy alapértelmezett méretű ASE, amely két előtér-t használ, a sablon körülbelül egy óra és 20 perc alatt befejeződik. A sablon futása közben az ASE nem skálázható.  

A sablon befejezése után az ILB ASE-n lévő alkalmazások HTTPS-en keresztül érhetők el. A kapcsolatok az alapértelmezett TLS/SSL tanúsítvánnyal vannak biztosítva. Az alapértelmezett TLS/SSL-tanúsítvány akkor használatos, ha az ILB ASE-n lévő alkalmazásokat az alkalmazásnév és az alapértelmezett állomásnév kombinációjával kezelik. Az alapértelmezett `https://mycustomapp.internal-contoso.com` TLS/SSL-tanúsítványt például a **.internal-contoso.com.*

A nyilvános több-bérlős szolgáltatáson futó alkalmazásokhoz hasonlóan azonban a fejlesztők egyéni gazdagépneveket is konfigurálhatnak az egyes alkalmazásokhoz. Egyedi SNI TLS/SSL tanúsítványkötéseket is konfigurálhatnak az egyes alkalmazásokhoz.

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##
Kétféle verzió érhető el az App Service Environment szolgáltatáshoz: ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat.

Az ASEv1-ben az összes erőforrást manuálisan kezelheti. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. Az App Service-csomag horizontális felskálázása előtt ki kell skáláznia a kívánt munkavégző készletet.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Ez magában foglalja az előtér-rendszerekhez használt vCPU-kat vagy a számítási feladatokat nem tartalmazó dolgozókat. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. Az ASEv1 verzióról további információt az [App Service Environment v1 bemutatása][ASEv1Intro] témakörben találhat.

Ha Erőforrás-kezelő sablon használatával szeretne létrehozni egy ASEv1-et, olvassa el az [ILB ASE v1 létrehozása Erőforrás-kezelő sablonnal című témakört.][ILBASEv1Template]


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
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
