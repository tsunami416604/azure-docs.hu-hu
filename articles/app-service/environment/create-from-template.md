---
title: Beadási ág létrehozása ARM-mel
description: Megtudhatja, hogyan hozhat létre külső vagy ILB App Service környezetet Azure Resource Manager sablon használatával.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0dccefa47789b4658a7bca828b5a820db0d448e5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688657"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>BeAzure Resource Manageri sablon létrehozása

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service környezetek (ASE-EK) internetről elérhető végponttal vagy végponttal hozhatók létre egy Azure-beli virtuális hálózatban (VNet) található belső címen. Belső végponttal való létrehozáskor a végpontot egy belső terheléselosztó (ILB) nevű Azure-összetevő kapja meg. A belső IP-címekhez tartozó beadást ILB-beadásnak nevezzük. A nyilvános végponttal rendelkező közforrást külső szakszolgáltatásnak nevezik. 

A beAzure Portal vagy egy Azure Resource Manager sablonnal hozhatók létre a kiegészítő szolgáltatás. Ez a cikk végigvezeti a következő lépéseken és szintaxison, amelyekben létre kell hoznia egy külső bevezetőt vagy ILB a Resource Manager-sablonokkal. Ha meg szeretné tudni, hogyan hozhat létre egy központot a Azure Portalban, tekintse meg a [külső][MakeExternalASE] betanító vagy a [ILB][MakeILBASE]beszerzése című részt.

Amikor létrehoz egy bevezetést a Azure Portalban, egyszerre létrehozhatja a VNet, vagy kiválaszthat egy már meglévő VNet a üzembe helyezéséhez. Ha sablonból hoz létre egy beadást, a következővel kell kezdődnie: 

* Egy Resource Manager-VNet.
* Egy alhálózat az adott VNet. A jövőbeli növekedési és méretezési igények kielégítése érdekében javasoljuk, hogy a `/24` a 256-címmel rendelkező, a beadási alhálózati méretet. A kisegítő lehetőség létrehozása után a méret nem módosítható.
* A VNet erőforrás-azonosítója. Ezt az információt a virtuális hálózat tulajdonságai között található Azure Portalból szerezheti be.
* Az előfizetés, amelyet központilag telepíteni szeretne.
* Az a hely, amelyet üzembe szeretne helyezni.

A kiegészítő környezet létrehozásának automatizálása:

1. Hozzon létre egy sablont egy sablonból. Ha külső beléptetést hoz létre, akkor a lépés után már elkészült. Ha létrehoz egy ILB-kiegészítőt, még néhány dolgot kell tennie.

2. A ILB-előkészítés létrehozása után a rendszer feltölt egy SSL-tanúsítványt, amely megfelel az ILB-előtagjai tartománynak.

3. A feltöltött SSL-tanúsítványt az "alapértelmezett" SSL-tanúsítványként rendeli hozzá a ILB-alapú adatforráshoz.  Ezt a tanúsítványt használja a rendszer az ILB-ben lévő alkalmazásokhoz való SSL-forgalomhoz, amikor a központhoz rendelt közös gyökértartomány (például https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>A bekészítés létrehozása
Egy olyan Resource Manager-sablon, amely létrehoz egy bevezetőt és a hozzá tartozó paramétereket tartalmazó fájlt, a GitHubon [egy példán][quickstartasev2create] keresztül érhető el.

Ha ILB-bevezetőt szeretne készíteni, használja a következő Resource Manager-sablonok [példáit][quickstartilbasecreate]. Ez a használati esetet is kielégíti. A *azuredeploy. Parameters. JSON* fájlban található paraméterek többsége közös a ILB ASE és a külső ASE létrehozásához. Az alábbi lista a speciális megjegyzések paramétereit hívja meg, vagy amelyek egyediek, amikor létrehoz egy ILB-bevezetést:

* *internalLoadBalancingMode*: a legtöbb esetben ez a beállítás 3 értékre van állítva, ami azt jelenti, hogy a 80/443-as porton a HTTP/HTTPS-forgalom, valamint a szolgáltatón futó FTP-szolgáltatás által figyelt vezérlési/adatcsatorna-portok a ILB lefoglalt virtuális hálózat belső címeihez lesznek kötve. Ha ez a tulajdonság 2 értékre van állítva, akkor csak az FTP szolgáltatáshoz kapcsolódó portok (vezérlő-és adatcsatornák) vannak kötve egy ILB-címnek. A HTTP/HTTPS-forgalom a nyilvános VIP-en marad.
* *dnsSuffix*: Ez a paraméter határozza meg a közirányhoz rendelt alapértelmezett gyökértartomány-tartományt. A Azure App Service nyilvános változatában az összes webalkalmazás alapértelmezett legfelső szintű tartománya a *azurewebsites.net*. Mivel a ILB-alapú adatközpont egy ügyfél virtuális hálózatán belül van, nem érdemes a nyilvános szolgáltatás alapértelmezett gyökértartomány-tartományát használni. Ehelyett egy ILB-szolgáltatónak rendelkeznie kell egy alapértelmezett gyökértartomány-tartománnyal, amely logikus a vállalat belső virtuális hálózatán belüli használatra. Előfordulhat például, hogy a contoso Corporation a *Internal-contoso.com* alapértelmezett gyökértartomány-tartományát használja olyan alkalmazások esetében, amelyek csak a contoso virtuális hálózatán belül lesznek feloldhatók és elérhetők. 
* *ipSslAddressCount*: Ez a paraméter automatikusan alapértelmezett értéke a 0 érték a *azuredeploy. JSON* FÁJLBAN, mert a ILB ASE csak egyetlen ILB-címnek van. Nincsenek explicit IP-SSL-címek egy ILB-alapú beszabályozáshoz. Ezért a ILB-beadáshoz tartozó IP-SSL-címkészlet értéke csak nulla lehet. Ellenkező esetben kiépítési hiba történik. 

A *azuredeploy. Parameters. JSON* fájl kitöltése után hozza létre a beadást a PowerShell-kódrészlet használatával. Módosítsa a fájl elérési útját úgy, hogy az megfeleljen a számítógép Resource Manager-sablonjának helyeinek. Ne feledje, hogy a Resource Manager-alapú központi telepítési név és az erőforráscsoport neve számára adja meg a saját értékeit:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Egy órát vesz igénybe a bevezetés létrehozásakor. Ezután a bevezető megjelenik a portálon a ASE az üzembe helyezést kiváltó előfizetéshez tartozó listájában.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Az "alapértelmezett" SSL-tanúsítvány feltöltése és konfigurálása
SSL-tanúsítványt kell társítani a kiegészítő csomaghoz az alkalmazásokhoz való SSL-kapcsolatok létrehozásához használt alapértelmezett SSL-tanúsítványként. Ha a *Internal-contoso.com*alapértelmezett DNS-utótagja a, a https://some-random-app.internal-contoso.com hoz való kapcsolódáshoz olyan SSL-tanúsítványra van szükség, amely a * *. internal-contoso.com*esetében érvényes. 

Szerezzen be egy érvényes SSL-tanúsítványt belső hitelesítésszolgáltatók használatával, külső kiállítótól származó tanúsítvány megvásárlása vagy önaláírt tanúsítvány használatával. Az SSL-tanúsítvány forrástól függetlenül a következő tanúsítvány-attribútumokat megfelelően kell konfigurálni:

* **Subject**: ezt az attribútumot a * *. your-root-domain-here.com*értékre kell állítani.
* **Tulajdonos alternatív neve**: ebben az attribútumban a * *. your-root-domain-here.com* és a * *. SCM.your-root-domain-here.com*is szerepelnie kell. Az egyes alkalmazásokhoz társított SCM-/kudu-helyhez tartozó SSL-kapcsolatok az űrlap *Your-app-Name.SCM.your-root-domain-here.com*-címeit használják.

Egy érvényes SSL-tanúsítvánnyal együtt két további előkészítési lépésre van szükség. Konvertálja/mentse az SSL-tanúsítványt .pfx fájlként. Ne feledje, hogy a. pfx-fájlnak tartalmaznia kell az összes köztes és főtanúsítványt. Jelszóval gondoskodjon a védelméről.

A. pfx fájlt át kell alakítani Base64 karakterlánccá, mert az SSL-tanúsítvány egy Resource Manager-sablonnal lett feltöltve. Mivel a Resource Manager-sablonok szövegfájlok, a. pfx-fájlt Base64 karakterlánccá kell konvertálni. Így a sablon paraméterként is szerepelhet.

Használja az alábbi PowerShell-kódrészletet a következőhöz:

* Önaláírt tanúsítvány létrehozása.
* Exportálja a tanúsítványt. pfx-fájlként.
* Alakítsa át a. pfx-fájlt Base64 kódolású karakterlánccá.
* Mentse a Base64 kódolású karakterláncot egy külön fájlba. 

Ez a Base64 kódolású PowerShell-kód a [PowerShell-parancsfájlok blogjában][examplebase64encoding]lett kialakítva:

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

Ha az SSL-tanúsítvány létrehozása és konvertálása Base64 kódolású karakterlánccá történt, a példa Resource Manager-sablonnal [konfigurálja az alapértelmezett SSL-tanúsítványt][quickstartconfiguressl] a githubon. 

A *azuredeploy. Parameters. JSON* fájlban található paraméterek itt láthatók:

* *appServiceEnvironmentName*: a konfigurált ILB-előállítók neve.
* *existingAseLocation*: az a szöveges karakterlánc, amely azt az Azure-régiót tartalmazza, ahol a ILB-központot telepítették.  Például: "az USA déli középső régiója".
* *pfxBlobString*: a. pfx fájl bementi-kódolású karakterláncának ábrázolása. Használja a korábban bemutatott kódrészletet, és másolja a "exportedcert. pfx. b64" fájlban található karakterláncot. Illessze be a értéket a *pfxBlobString* attribútum értékeként.
* *Password (jelszó*): a. pfx fájl védelméhez használt jelszó.
* *certificateThumbprint*: a Tanúsítvány ujjlenyomata. Ha ezt az értéket a PowerShellből kéri le (például *$Certificate.* A korábbi kódrészletből származó ujjlenyomatot), az értéket használhatja. Ha az értéket a Windows-tanúsítvány párbeszédpanelen másolja, ne felejtse el kihúzni a felesleges szóközöket. A *certificateThumbprint* hasonlóan kell kinéznie, mint a AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: a tanúsítvány azonosítására szolgáló, saját maga által választott, felhasználóbarát karakterlánc-azonosító. A név az SSL-tanúsítványt képviselő *Microsoft. Web/Certificates* entitás egyedi Resource Manager-azonosítójának részeként használatos. A névnek a következő utótaggal *kell* végződnie: \_yourASENameHere_InternalLoadBalancingASE. Az Azure Portal ezt az utótagot használja jelzőként, hogy a tanúsítvány egy ILB-kompatibilis bemutatók biztonságossá tételére szolgál.

A *azuredeploy. Parameters. JSON* rövidített példája itt látható:

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

Az *azuredeploy. Parameters. JSON* fájl kitöltése után konfigurálja az alapértelmezett SSL-tanúsítványt a PowerShell-kódrészlet használatával. Módosítsa a fájlelérési utakat úgy, hogy az megfeleljen a számítógépen található Resource Manager-Sablonfájlok helyének. Ne feledje, hogy a Resource Manager-alapú központi telepítési név és az erőforráscsoport neve számára adja meg a saját értékeit:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

A módosítás alkalmazása körülbelül 40 percet vesz igénybe. Ha például egy alapértelmezett méretű, két előtéri végpontot használó, a sablon körülbelül egy óra, és 20 percet vesz igénybe. Amíg a sablon fut, a kiszolgáló nem méretezhető.  

A sablon befejeződése után a ILB-beadási csomag alkalmazásai HTTPS-kapcsolaton keresztül érhetők el. A kapcsolatok az alapértelmezett SSL-tanúsítvány használatával biztonságosak. Az alapértelmezett SSL-tanúsítvány akkor használatos, ha az ILB-szolgáltatón futó alkalmazások az alkalmazás neve és az alapértelmezett állomásnév együttes használatával vannak kezelve. A https://mycustomapp.internal-contoso.com például a * *. internal-contoso.com*alapértelmezett SSL-tanúsítványát használja.

Akárcsak a nyilvános több-bérlős szolgáltatáson futó alkalmazások esetében, a fejlesztők egyéni állomásnévket állíthatnak be az egyes alkalmazásokhoz. Emellett egyedi SNI SSL tanúsítvány-kötéseket is beállíthat az egyes alkalmazásokhoz.

## <a name="app-service-environment-v1"></a>App Service-környezet v1 ##
Kétféle verzió érhető el az App Service Environment szolgáltatáshoz: ASEv1 és ASEv2. A fenti információ az ASEv2 verzión alapul. Ebben a szakaszban az ASEv1 és ASEv2 különbségeiről olvashat.

A ASEv1-ben manuálisan kezelheti az összes erőforrást. Ebbe beletartoznak az előtérrendszerek, a feldolgozók, valamint IP-alapú SSL esetén az IP-címek is. A App Service tervének méretezése előtt ki kell bővíteni a használni kívánt munkavégző készletet.

Az ASEv1 díjszabása eltér az ASEv2-étől. Az ASEv1 esetében minden lefoglalt vCPU után fizet. Ebbe beletartozik a vCPU, amelyek az előfizetéseket és a munkaterheléseket nem működtető feladatokhoz használatosak. Az ASEv1 esetében egy ASE alapértelmezett maximális skálázhatósága összesen 55 gazdagép. Ez tartalmazza a feldolgozókat és az előtérrendszereket is. Az ASEv1 egyik előnye az, hogy klasszikus, illetve Resource Manager virtuális hálózatokban is üzembe helyezhető. További információ a ASEv1: [app Service Environment v1 – bevezetés][ASEv1Intro].

Ha Resource Manager-sablonnal szeretne ASEv1 létrehozni, tekintse meg a [ILB bevezető v1 Resource Manager-sablonnal történő létrehozását][ILBASEv1Template]ismertető témakört.


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
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
