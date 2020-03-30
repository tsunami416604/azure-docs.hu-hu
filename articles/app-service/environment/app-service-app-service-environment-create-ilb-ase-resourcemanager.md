---
title: ILB ASE v1 létrehozása
description: Hozzon létre egy App Service-környezetet egy belső terheléselosztóval (ILB ASE). Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 1a0ec9465be3b714e90bfca6a15b60423d6065a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295579"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>ILB ASE létrehozása Azure Resource Manager-sablonokkal

> [!NOTE] 
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól. Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Az App Service-környezetek nyilvános virtuális IP-cím helyett virtuális hálózati belső címmel hozhatók létre.  Ezt a belső címet egy Azure-összetevő, a belső terheléselosztó (ILB) biztosítja.  Az Azure Portal használatával ilb ASE hozható létre.  Az Azure Resource Manager-sablonok automatizálási használatával is létrehozható.  Ez a cikk bemutatja az ILB ASE-nek az Azure Resource Manager-sablonokkal való létrehozásához szükséges lépéseket és szintaxist.

Az ILB ASE létrehozásának automatizálása három lépésből áll:

1. Először az alap ASE jön létre egy virtuális hálózat ban egy belső terheléselosztó címet használ, nem pedig egy nyilvános VIP.  Ennek a lépésnek a részeként egy gyökértartomány név van rendelve az ILB ASE-hez.
2. Az ILB ASE létrehozása után a rendszer ssl-tanúsítványt tölt fel.  
3. A feltöltött SSL-tanúsítvány "alapértelmezett" SSL-tanúsítványként kifejezetten az ILB ASE-hez van rendelve.  Ez az SSL-tanúsítvány az ILB ASE-n lévő alkalmazások SSL-forgalmára lesz használva, amikor az `https://someapp.mycustomrootcomain.com`alkalmazásokat az ASE-hez rendelt közös gyökértartomány (pl. ) használatával címezik.

## <a name="creating-the-base-ilb-ase"></a>Az alap ILB ASE létrehozása
Egy példa az Azure Resource Manager-sablonra és a hozzá [here][quickstartilbasecreate]tartozó paraméterek fájljára, itt érhetők el a GitHubon.

Az *azuredeploy.parameters.json* fájl ban lévő paraméterek többsége közös az ILB ASEs és a nyilvános VIRTUÁLIS IP-címhez kötött ASEs létrehozásában.  Az alábbi lista az ILB ASE létrehozásakor a speciális vagy egyedi paramétereket hívja meg:

* *internalLoadBalancingMode*: A legtöbb esetben ez 3, ami azt jelenti, mind a HTTP/HTTPS forgalmat portok 80/443, és a vezérlő /adatcsatorna portok által hallgatott FTP szolgáltatás az ASE, lesz kötve egy ILB lefoglalt virtuális hálózati belső címet.  Ha ez a tulajdonság ehelyett 2, akkor csak az FTP-szolgáltatással kapcsolatos portok (vezérlő- és adatcsatornák) lesznek ilb-címhez kötve, míg a HTTP/HTTPS-forgalom a nyilvános VIP-n marad.
* *dnsSuffix*: Ez a paraméter határozza meg az ASE-hez hozzárendelt alapértelmezett gyökértartományt.  Az Azure App Service nyilvános változatában az összes webalkalmazás alapértelmezett gyökértartománya *azurewebsites.net.*  Mivel azonban az ILB ASE az ügyfél virtuális hálózatán belül van, nincs értelme a közszolgálat alapértelmezett gyökértartományát használni.  Ehelyett az ILB ASE-nek rendelkeznie kell egy alapértelmezett gyökértartománnyal, amely a vállalat belső virtuális hálózatán belül használható.  Egy hipotetikus Contoso Corporation például *a internal-contoso.com* alapértelmezett gyökértartományát használhatja olyan alkalmazásokhoz, amelyek csak feloldhatók és a Contoso virtuális hálózatán belül érhetők el. 
* *ipSslAddressCount*: Ez a paraméter automatikusan alapértelmezés szerint 0 az *azuredeploy.json* fájlban, mert az ILB ASEs csak egyetlen ILB-címmel rendelkezik.  Az ILB ASE-hez nincsenek explicit IP-SSL-címek, ezért az ILB ASE IP-SSL címkészletét nullára kell állítani, ellenkező esetben kiépítési hiba lép fel. 

Miután az *azuredeploy.parameters.json* fájl ki lett töltve egy ILB ASE-hez, az ILB ASE ezután a következő Powershell-kódkódrészlet használatával hozható létre.  Módosítsa a fájl PATH-ok, hogy megfeleljen, ahol az Azure Resource Manager sablonfájlok találhatók a gépen.  Ne felejtse el megadni a saját értékeit az Azure Resource Manager központi telepítési nevéhez és az erőforráscsoport nevéhez.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Az Azure Resource Manager sablon elküldése után néhány órát vesz igénybe az ILB ASE létrehozása.  A létrehozás befejezése után az ILB ASE megjelenik a portál felhasználói felületén az alkalmazásszolgáltatás-környezetek listájában az üzembe helyezést kiváltó előfizetéshez.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>A „Default” (Alapértelmezett) SSL-tanúsítvány feltöltése és konfigurálása
Az ILB ASE létrehozása után az ASE-hez ssl-tanúsítványt kell társítani az alkalmazásokhoz való SSL-kapcsolatok létrehozásához használt "alapértelmezett" SSL-tanúsítványként.  Ha az ASE alapértelmezett DNS-utótagja *internal-contoso.com,* a feltételezett Contoso Corporation példával folytatva, akkor a *https://some-random-app.internal-contoso.com* kapcsolathoz **.internal-contoso.com.* 

Az érvényes SSL-tanúsítványok beszerzésének számos módja van, beleértve a belső hitelesítésszolgáltatót, a külső kibocsátótól származó tanúsítvány megvásárlását és az önaláírt tanúsítvány használatát.  Az SSL-tanúsítvány forrásától függetlenül az alábbi tanúsítványattribútumokat megfelelően kell konfigurálni:

* *Tárgy*: Ezt az attribútumot **.your-root-domain-here.com*
* *Tulajdonos alternatív neve:* Ennek az attribútumnak tartalmaznia kell a **.your-root-domain-here.com*és a **.scm.your-root-domain-here.com*.  A második bejegyzés oka az, hogy az egyes alkalmazásokhoz társított SSL-kapcsolat az egyes alkalmazásokhoz társított SSL-kapcsolat *a your-app-name.scm.your-root-domain-here.com*űrlap címével történik.

Az érvényes SSL-tanúsítvánnyal két további előkészítő lépésre van szükség.  Az SSL-tanúsítványt .pfx fájlként kell konvertálni/menteni.  Ne feledje, hogy a .pfx fájlnak tartalmaznia kell az összes köztes és főtanúsítványt, és jelszóval is biztosítani kell.

Ezután a keletkező .pfx fájlt base64 karakterláncddddddákká kell konvertálni, mert az SSL-tanúsítvány feltöltése egy Azure Resource Manager-sablon használatával történik.  Mivel az Azure Resource Manager-sablonok szöveges fájlok, a .pfx fájlt base64 karakterláncddddddákká kell konvertálni, hogy a sablon paramétereként szerepelhessen.

Az alábbi Powershell-kódrészlet egy példát mutat be egy önaláírt tanúsítvány létrehozására, a tanúsítvány .pfx fájlként való exportálására, a .pfx fájl base64 kódolású karakterláncra történő konvertálására, majd a base64 kódolású karakterlánc külön fájlba mentésére.  A Powershell-kódot a base64 kódoláshoz a [Powershell Scripts Blogból][examplebase64encoding]adaptálták.

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Miután az SSL-tanúsítvány sikeresen létrejött, és egy base64 kódolású karakterláncra konvertált, a példa Azure Resource Manager sablon a GitHubon [az alapértelmezett SSL-tanúsítvány konfigurálásához][configuringDefaultSSLCertificate] használható.

Az *azuredeploy.parameters.json* fájl paraméterei az alábbiak:

* *appServiceEnvironmentName*: A konfigurált ILB ASE neve.
* *existingAseLocation*: Az ILB ASE üzembe helyezésének helyét tartalmazó Szöveges karakterlánc.  Például: "Dél-Közép-USA".
* *pfxBlobString*: A .pfx fájl based64 kódolású karakterlánc-ábrázolása.  A korábban bemutatott kódrészlet használatával másolja az "exportedcert.pfx.b64" karakterláncot, és illessze be a *pfxBlobString* attribútum értékeként.
* *jelszó*: A .pfx fájl védelméhez használt jelszó.
* *tanúsítványThumbprint*: A tanúsítvány ujjlenyomata.  Ha ezt az értéket a Powershellből (pl. *$certificate. Ujjlenyomat* a korábbi kódrészletből), használhatja az értéket a hogy-van.  Ha azonban az értéket a Windows tanúsítvány párbeszédpaneléről másolja, ne felejtse el kitépni a felesleges szóközöket.  A *tanúsítványThumbprint* kell kinéznie: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: A tanúsítvány identitásához használt, saját maga által választott rövid karakterlánc-azonosító.  A név az SSL-tanúsítványt képviselő *Microsoft.Web/certificates* entitás egyedi Azure Resource Manager-azonosítójának részeként használatos.  A **névnek** a következő utótaggal kell végződnie: \_yourASENameHere_InternalLoadBalancingASE.  Ezt az utótagot a portál annak jelzésére használja, hogy a tanúsítványt az ILB-kompatibilis ASE védelmére használják.

Az *azuredeploy.parameters.json* rövidített példája az alábbiakban látható:

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

Miután az *azuredeploy.parameters.json* fájl kilett töltve, az alapértelmezett SSL-tanúsítvány konfigurálható a következő Powershell kódrészlet használatával.  Módosítsa a fájl PATH-ok, hogy megfeleljen, ahol az Azure Resource Manager sablonfájlok találhatók a gépen.  Ne felejtse el megadni a saját értékeit az Azure Resource Manager központi telepítési nevéhez és az erőforráscsoport nevéhez.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Az Azure Resource Manager-sablon elküldése után az ASE előtér-alapú körülbelül negyven percet vesz igénybe a módosítás alkalmazása.  Ha például egy alapértelmezett méretű ASE két előtér-végződést használ, a sablon körülbelül egy óra és húsz perc alatt fejeződik be.  Asablon futtatása közben az ASE nem lesz képes méretezve.  

A sablon befejezése után az ILB ASE-n lévő alkalmazások HTTPS-kapcsolaton keresztül érhetők el, és a kapcsolatok az alapértelmezett SSL-tanúsítvánnyal lesznek biztosítva.  Az alapértelmezett SSL-tanúsítvány akkor lesz használva, ha az ILB ASE-n lévő alkalmazásokat az alkalmazásnév és az alapértelmezett állomásnév kombinációjával kezelia.  Például *https://mycustomapp.internal-contoso.com* az alapértelmezett SSL tanúsítványt használja a **.internal-contoso.com*.

A nyilvános több-bérlős szolgáltatáson futó alkalmazásokhoz hasonlóan azonban a fejlesztők egyéni állomásneveket is konfigurálhatnak az egyes alkalmazásokhoz, majd egyedi SNI SSL tanúsítványkötéseket konfigurálhatnak az egyes alkalmazásokhoz.  

## <a name="getting-started"></a>Első lépések
Az App Service-környezetek ismerkedése az [App Service-környezet bemutatása című témakörben](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

