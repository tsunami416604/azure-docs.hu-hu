---
title: Az Azure Resource Manager-sablonok használatával ILB ASE létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy belső terheléselosztó ASE Azure Resource Manager-sablonok használatával.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.openlocfilehash: ea9407208f1bf555cf1a6d166825896dec89ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836853"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>ILB ASE létrehozása Azure Resource Manager-sablonokkal

> [!NOTE] 
> Ez a cikk az App Service Environment-környezet v1 tárgya. Az App Service-környezet, amely könnyebben használható, és nagyobb teljesítményű infrastruktúra fut egy újabb verziója van telepítve. További információt az új verzió útmutató a [az App Service Environment bemutatása](intro.md).
>

## <a name="overview"></a>Áttekintés
App Service-környezetek nyilvános virtuális IP-címhez helyett egy virtuális hálózat belső cím hozhatók létre.  Ez a belső cím egy Azure összetevő neve, a belső terheléselosztó (ILB) biztosítja.  Egy ILB ASE az Azure portál használatával hozhatók létre.  Azt is létrehozhatók automation vállalja Azure Resource Manager-sablonok használatával.  Ez a cikk végigvezeti a lépéseket és az Azure Resource Manager-sablonok egy ILB ASE létrehozásához szükséges.

Három lépésben történik egy ILB ASE létrehozásának automatizálása részt:

1. A kiinduló ASE először egy nyilvános VIP helyett egy belső terheléselosztói címet a virtuális hálózat jön létre.  Ez a lépés részeként egy legfelső szintű tartomány nevét a ILB ASE van hozzárendelve.
2. A ILB ASE létrehozása után egy SSL-tanúsítvány feltöltése.  
3. A feltöltött SSL-tanúsítvány van explicit módon hozzárendelt a ILB ASE az "alapértelmezett" SSL-tanúsítványt.  Az SSL-tanúsítvány lesz használható az SSL-forgalmat a ILB ASE található alkalmazások, ha az alkalmazások kiiktatása használatával a közös gyökértartomány a ASE (pl. https://someapp.mycustomrootcomain.com) rendelt

## <a name="creating-the-base-ilb-ase"></a>A kiinduló ILB ASE létrehozása
Egy példa Azure Resource Manager-sablon és a társított paraméterek fájlt a Githubon érhetők [Itt][quickstartilbasecreate].

A paraméterek a legtöbb a *azuredeploy.parameters.json* fájl megegyeznek az mind ILB ASEs, valamint nyilvános virtuális IP-címhez kötött ASEs létrehozása.  A hívások Megjegyzés paraméterei ki az alábbi listából, vagy ha az egyediek, egy ILB ASE létrehozásakor:

* *interalLoadBalancingMode*: egy ILB lefoglalt virtuális hálózat belső címet társítani a ASE meg az FTP-szolgáltatás által figyelt ez 3, ami azt jelenti, hogy a 80/443-as port HTTP/HTTPS-forgalmat, és a/adatok portok beállítása a legtöbb esetben.  Ez a tulajdonság értéke ehelyett 2, csak az FTP-szolgáltatás kapcsolatos, portok (vezérlő és az adatokat egyaránt csatornák) társítani ILB címre, amíg változatlan marad a HTTP/HTTPS-forgalmat a nyilvános VIP-e.
* *dnsSuffix*: Ez a paraméter határozza meg az alapértelmezett gyökértartomány, amely a ASE lesz hozzárendelve.  Az Azure App Service nyilvános változata, az alapértelmezett gyökértartomány minden webes alkalmazások nem *azurewebsites.net*.  Azonban mivel egy ILB ASE belső, az ügyfél virtuális hálózathoz, azt nem célszerű a használata a nyilvános service alapértelmezett legfelső szintű tartomány.  Ehelyett egy ILB ASE kell egy alapértelmezett gyökértartomány legjobb a vállalat belső virtuális hálózaton belüli használatra.  Például egy kitalált, Contoso Corporation használhatja az alapértelmezett legfelső szintű tartomány *belső contoso.com* célja, hogy csak akkor lehet feloldható Contoso virtuális hálózaton belülről érhetők el alkalmazások. 
* *ipSslAddressCount*: Ez a paraméter értékét 0-ra a automatikusan alapértelmezés szerint a *azuredeploy.json* fájlhoz, mert ILB ASEs csak egyetlen ILB címmel rendelkezik.  Nincsenek a egy ILB ASE explicit IP-SSL címek, ezért az IP-SSL-címkészletet egy ILB ASE nulla értékre kell állítani, ellenkező esetben a létesítési hiba történik. 

Egyszer a *azuredeploy.parameters.json* fájl ki van töltve egy ILB ASE a, a ILB ASE majd segítségével hozhatók létre a következő Powershell-kódrészletet.  Módosítsa a fájl elérési útjait felel meg, hol található az Azure Resource Manager sablon fájlokat a számítógépre.  Továbbá ne feledje a saját Azure Resource Manager deployment nevét és az erőforráscsoport neve értéket ad meg.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Az Azure Resource Manager után a sablon nyújtják a létrehozandó ILB ASE néhány órát fog igénybe venni.  A létrehozása után a ILB ASE jelennek meg a portálon UX az előfizetés, a központi telepítés kiváltó App Service Environment-környezetek listájában.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Feltöltését és az "Alapértelmezett" SSL-tanúsítvány konfigurálása
A ILB ASE létrehozása után egy SSL-tanúsítványt kell venniük a mértékéig alkalmazások SSL-kapcsolat létrehozásához használja az "alapértelmezett" SSL-tanúsítvány.  Van folytatása a elméleti Contoso Corporation például, ha a ASE alapértelmezett DNS-utótag *belső contoso.com*, majd kapcsolódni a *https://some-random-app.internal-contoso.com* érvényes SSL-tanúsítvány szükséges **.internal-contoso.com*. 

Nincsenek többféle módon, beleértve a belső hitelesítésszolgáltatók, egy tanúsítvány beszerzése egy külső kiállítótól érkező és egy önaláírt tanúsítványt használ érvényes SSL-tanúsítványt kell beszerezniük.  Az SSL-tanúsítvány forrását, függetlenül is megfelelően konfigurálni kell a következő tanúsítvány attribútumok:

* *Tulajdonos*: meg kell ezt az attribútumot **.your-gyökér-tartományi-here.com*
* *Tulajdonos alternatív neve*: ennek az attribútumnak kell tartalmaznia a **.your-gyökér-tartományi-here.com*, és **.scm.your-gyökér-tartományi-here.com*.  A második bejegyzés oka, hogy minden alkalmazáshoz társított SCM/Kudu webhely SSL-kapcsolat lesz-e az űrlap címmel történő *your-app-name.scm.your-root-domain-here.com*.

Egy érvényes SSL-tanúsítvánnyal aktuális két további előkészítő lépések szükségesek.  Az SSL-tanúsítványt kell lennie a alakítja/egy .pfx fájlba menti.  Ne feledje, hogy a .pfx fájl tartalmazza az összes köztes, és a legfelső szintű tanúsítványokat kell-e, és is jelszóval védeni kell.

Majd az eredményül kapott .pfx-fájlt kell Base64 kódolású karakterlánc alakítja át, mert az SSL-tanúsítvány lesz feltöltve, Azure Resource Manager-sablonnal.  Mivel az Azure Resource Manager-sablonok szövegfájlok, a .pfx-fájlt kell konvertálni kell egy Base64 kódolású karakterlánc, azt is meg lehet adni a sablon paraméterként.

A Powershell kódrészletben látható példa önaláírt tanúsítvány létrehozása, a tanúsítvány exportálása .pfx-fájlként, a .pfx fájl átalakítása a base64 kódolású karakterlánc, karakterlánc külön fájlt, majd mentse a base64 kódolású.  A Powershell-kódot az alkalmazás base64 kódolást volt módosítani a a [Powershell parancsfájlok Blog][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Amennyiben az SSL-tanúsítvány sikeresen generált és alakítja át a base64 kódolású karakterlánc, a példában Azure Resource Manager-sablon a Githubon [az alapértelmezett SSL-tanúsítvány konfigurálása] [ configuringDefaultSSLCertificate] is használható.

A paraméterek a *azuredeploy.parameters.json* fájlt az alábbiak:

* *appServiceEnvironmentName*: a konfigurálni kívánt ILB ASE nevét.
* *existingAseLocation*: az Azure-régió, ahol a ILB ASE állított tartalmazó szöveges karakterlánc.  Például: "Déli középső Régiójában".
* *pfxBlobString*: A based64 kódolású .pfx fájlt karakterláncos ábrázolása.  A korábban bemutatott kódrészletet használja, akkor volna másolja át a "exportedcert.pfx.b64" foglalt karakterlánc és illessze be az értékeként a *pfxBlobString* attribútum.
* *jelszó*: A jelszót a .pfx-fájlt védi.
* *certificateThumbprint*: A tanúsítvány ujjlenyomata.  Ha ez az érték lekérése Powershell (pl. *$certificate. Ujjlenyomat* a korábbi kódrészletet a), használhatja a értékével megegyező-van.  Értékének másolása a Windows tanúsítvány párbeszédpanel, ha azonban ne felejtse el a felesleges szóközöket ki.  A *certificateThumbprint* hasonlóan kell kinéznie: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: identitás a tanúsítvány segítségével A saját rövid karakterlánc-azonosító.  A név használatban van az Azure Resource Manager egyedi azonosítóját részeként a *Microsoft.Web/certificates* az SSL-tanúsítvány képviselő entitás.  A név **kell** végződhet a következő előtagot: \_yourASENameHere_InternalLoadBalancingASE.  Ez utótag lesz a portál által azt jelzi, hogy ezzel a tanúsítvánnyal egy ILB-kompatibilis ASE biztonságossá tételéhez.

Rövidített például *azuredeploy.parameters.json* alább találja:

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

Egyszer a *azuredeploy.parameters.json* fájl ki van töltve, az alapértelmezett SSL-tanúsítvány a következő Powershell-kódrészletet használatával konfigurálható.  Módosítsa a fájl elérési útjait felel meg, hol található az Azure Resource Manager sablon fájlokat a számítógépre.  Továbbá ne feledje a saját Azure Resource Manager deployment nevét és az erőforráscsoport neve értéket ad meg.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Miután az Azure Resource Manager sablon beküldött eltarthat nagyjából előtér-ASE negyven perc percig a módosítás alkalmazására.  Például egy alapértelmezett méretű mértékéig két elülső-végpontok használatával, a sablon lépnek körülbelül egy óra és 20 perc befejezéséhez.  A sablon futása közben a ASE nem lehet méretezni.  

Miután befejeződött a sablon, a ILB ASE alkalmazások HTTPS-KAPCSOLATON keresztül is elérhetők, és a kapcsolatok megfelelően történik az alapértelmezett SSL-tanúsítvány.  Az alapértelmezett SSL-tanúsítvány fog használni, amikor a ILB ASE alkalmazások használata az alkalmazás nevét és az alapértelmezett állomásnév kombinációja tárgyalja.  Például *https://mycustomapp.internal-contoso.com* szeretné használni az alapértelmezett SSL-tanúsítványt **.internal-contoso.com*.

Azonban hasonlóan a nyilvános több-bérlős szolgáltatást futó alkalmazások, a fejlesztők is konfigurálja a egyéni állomásnevek egyes alkalmazások, és majd konfigurálja az egyedi SNI SSL-tanúsítványok kötései egyes alkalmazásokhoz.  

## <a name="getting-started"></a>Bevezetés
App Service Environment-környezetek megkezdéséhez, lásd: [App Service Environment bemutatása](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

