---
title: Az Azure Resource Manager-sablonok használatával ILB ASE létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy belső load balancer ASE Azure Resource Manager-sablonok használatával.
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
ms.openlocfilehash: a136234c6645e7f88fc16a5f7a5d84580906c0f7
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964849"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>ILB ASE létrehozása Azure Resource Manager-sablonokkal

> [!NOTE] 
> Ez a cikk az App Service Environment-környezet v1 szól. Nincs az App Service-környezet, amely egyszerűbb és nagyobb teljesítményű infrastruktúra fut egy újabb verziója. További információ az új verzió elindítása a [az App Service Environment bemutatása](intro.md).
>

## <a name="overview"></a>Áttekintés
App Service Environment-környezetek lehet létrehozni egy virtuális hálózat belső cím, egy nyilvános virtuális IP-cím helyett.  A belső cím egy Azure-összetevő a belső terheléselosztó (ILB) nevű biztosítják.  ILB ASE környezetben az Azure portal használatával hozható létre.  Azt is létrehozhatók automation, de az Azure Resource Manager-sablonok használatával.  Ez a cikk ismerteti a lépéseket, és az ILB ASE létrehozása Azure Resource Manager-sablonok a szükséges szintaxist.

Három lépésből áll részt vevő automatizálása az ILB ASE létrehozása:

1. Először az alap ASE virtuális hálózatot egy nyilvános virtuális IP-cím helyett egy belső terheléselosztó címmel használatával jön létre.  Ez a lépés részeként a legfelső szintű tartománynév az ILB ASE van hozzárendelve.
2. Az ILB ASE létrehozása után egy SSL-tanúsítvány feltöltése.  
3. A feltöltött SSL-tanúsítvány van explicit módon rendelve az ILB ASE környezetben az "alapértelmezett" SSL-tanúsítványt.  Az SSL-tanúsítvány lesz használható az SSL-forgalmat az ILB ASE környezetben található alkalmazások, ha az alkalmazások foglalkozik a közös gyökértartomány (pl. rendelve az ASE használatával https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Az alap ILB ASE létrehozása
Egy példa az Azure Resource Manager-sablon és a kapcsolódó paramétereket tartalmazó fájlt, a Githubon érhető el [Itt][quickstartilbasecreate].

A paraméterek a legtöbb a *azuredeploy.parameters.json* fájl akkor is ILB ASE, valamint egy nyilvános virtuális IP-CÍMEK kötött ASE létrehozása.  Hívások különösen fontos paraméterek meg az alábbi listán, vagy, amelyek egyediek, az ILB ASE létrehozása során:

* *interalLoadBalancingMode*: A legtöbb esetben az FTP-szolgáltatás, az ASE által figyelt ezt a 3, ami azt jelenti, hogy a HTTP/HTTPS-forgalmat a 80-as/443-as portokon, és a vezérlő/adatok portok beállítása társítani kívánt lefoglalt virtuális hálózat belső ILB cím.  Ha ehelyett Ez a tulajdonság értéke 2, csak az FTP-szolgáltatás kapcsolatos, portok (egyaránt vezérlési és csatornák) társítani kívánt ILB-címmel, amíg változatlan marad a HTTP/HTTPS-forgalmat a nyilvános virtuális IP-cím.
* *DNS-utótagja*: Ez a paraméter határozza meg az alapértelmezett legfelső szintű tartományt, amely hozzá lesz rendelve az ASE-t.  Az Azure App Service-ben nyilvános változata, az alapértelmezett gyökértartomány esetében az összes webes alkalmazások van *azurewebsites.net*.  Azonban mivel az ILB ASE egy ügyfél virtuális hálózatán belüli, akkor nem értelme használata a nyilvános service alapértelmezett legfelső szintű tartományt.  Ehelyett az ILB ASE rendelkeznie kell egy alapértelmezett legfelső szintű tartományt, amely logikus a használatra a vállalat belső virtuális hálózaton belül.  Egy kitalált, Contoso Corporation például használhatja az alapértelmezett gyökértartomány *belső contoso.com* feloldható és elérhető-e a Contoso virtuális hálózaton belül csak nem igénylő alkalmazások számára. 
* *ipSslAddressCount*: Ez a paraméter automatikus alapértelmezés szerint a 0 értéket, a *azuredeploy.json* fájlhoz, mert az ILB ASE egy ILB-címmel rendelkezik.  Nincsenek explicit IP-SSL címek az ILB ASE környezetben, és ezért az IP SSL-címkészletet az ILB ASE nulla értékre kell állítani, ellenkező esetben egy üzembe helyezési hiba történik. 

Miután a *azuredeploy.parameters.json* fájl ki van töltve az ILB ASE környezetben, az ILB ASE majd hozható létre a következő Powershell-kódrészletet.  A fájl elérési utak, hol található a gépen az Azure Resource Manager-sablonfájlokat megfelelően módosítsa.  Ne feledje a adja meg a saját Azure Resource Manager üzemelő példány neve és erőforráscsoport-nevet.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Az Azure Resource Manager után a sablon elküldésekor fog létrehozni az ILB ASE néhány órát is igénybe.  A létrehozás befejezése után az ILB ASE jelennek meg a portálon UX az App Service Environment-környezetek listája az előfizetés, amely a központi telepítés elindítva.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Fel- és az "Alapértelmezett" SSL-tanúsítvány konfigurálása
Az ILB ASE létrehozása után egy SSL-tanúsítványt kell társítva az ASE-t az "alapértelmezett" SSL-tanúsítvány használata az alkalmazások SSL-kapcsolat.  Van folytatása a elméleti Contoso Corporation például, ha az ASE alapértelmezett DNS-utótag *belső contoso.com*, majd a kapcsolat *https://some-random-app.internal-contoso.com* igényel, amely SSL-tanúsítvány érvényes **.internal-contoso.com*. 

Nincsenek többféle módon, beleértve a belső hitelesítésszolgáltatók, a vásárol egy tanúsítványt külső kiállítótól és a egy önaláírt tanúsítvány használatával érvényes SSL-tanúsítvány beszerzése.  Az SSL-tanúsítvány forrásától függetlenül az alábbi tanúsítványattribútumokat megfelelően kell konfigurálni:

* *Tulajdonos*: ezt az attribútumot állítsa **.az-gyökér-domain-here.com*
* *Tulajdonos alternatív neve*: ennek az attribútumnak tartalmaznia kell mindkét **.az-gyökér-domain-here.com*, és a **.az-gyökér-domain-here.com*.  A második bejegyzés oka, hogy SSL-kapcsolatok az egyes alkalmazásokhoz hozzárendelt SCM/Kudu helyhez lesz-e az űrlap egy címmel *your-app-name.scm.your-root-domain-here.com*.

Az érvényes SSL-tanúsítványt az aktuális két további előkészítő lépések szükségesek.  Az SSL-tanúsítványt kell lennie egy .pfx-fájlba konvertálja/elmentve.  Ne feledje, hogy a .pfx-fájl összes köztes és főtanúsítványok van szükség, és jelszóval kell védeni kell is.

Ezután a létrejövő .pfx-fájlt kell base64-karakterlánc konvertálva, mert az SSL-tanúsítványt fel lesz töltve egy Azure Resource Manager-sablon használatával.  Mivel az Azure Resource Manager-sablonok szöveges fájlok, a .pfx-fájlt kell base64-karakterlánc konvertálva, így azt is meg lehet adni a sablon paraméterként.

Az alábbi Powershell-kódrészlet mutat példát egy önaláírt tanúsítvány létrehozása, a tanúsítvány exportálása egy .pfx-fájlként történő konvertálásakor a .pfx-fájlt egy base64 kódolású karakterlánc, karakterlánc külön fájlt, majd mentse a base64 kódolású.  A Powershell-kóddal a base64 kódolás igazítani a volt a [Powershell parancsfájlok Blog][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Az SSL-tanúsítvány sikeresen létrehozott és alakítani egy base64-kódolású karakterlánc, a Githubon, például az Azure Resource Manager-sablonja [az alapértelmezett SSL-tanúsítvány konfigurálása] [ configuringDefaultSSLCertificate] is használható.

A paramétereket a *azuredeploy.parameters.json* fájlt az alábbiak:

* *appServiceEnvironmentName*: az ILB ASE konfigurált nevét.
* *existingAseLocation*: az Azure-régióban, az ILB ASE környezetben telepített tartalmazó szöveges karakterlánc.  Például: "USA déli középső Régiója".
* *pfxBlobString*: A based64 kódolású karakterláncos leképezését a .pfx-fájlt.  Használja a korábban bemutatott kódrészletet, kívánja másolni a "exportedcert.pfx.b64" foglalt karakterlánc, majd illessze be az értéket a *pfxBlobString* attribútum.
* *jelszó*: A jelszót a .pfx-fájlt védi.
* *certificateThumbprint*: A tanúsítvány ujjlenyomata.  Ha ez az érték lekérése Powershell (pl. *$certificate. Ujjlenyomat* a korábbi kódrészlet), használhatja a értékével megegyező-van.  A Windows-tanúsítvány párbeszédpanelről másolja az értéket, ha azonban ne felejtse el a felesleges szóközöket ki.  A *certificateThumbprint* hasonlóan kell kinéznie: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: a tanúsítvány identitás használt saját egy rövid karakterlánc-azonosító.  A név részeként az Azure Resource Manager egyedi azonosítóját használja a *Microsoft.Web/certificates* az SSL-tanúsítvány képviselő entitás.  A név **kell** a következő utótaggal végződik: \_yourASENameHere_InternalLoadBalancingASE.  Ennek az utótagnak azt jelzi, hogy a tanúsítvány egy ILB-kompatibilis ASE biztonságossá tételéhez van használva, a portál által használt.

Rövidített például *azuredeploy.parameters.json* az alábbiakban látható:

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

Miután a *azuredeploy.parameters.json* fájl ki van töltve, az alapértelmezett SSL-tanúsítványt a következő Powershell-kódrészlet használatával konfigurálható.  A fájl elérési utak, hol található a gépen az Azure Resource Manager-sablonfájlokat megfelelően módosítsa.  Ne feledje a adja meg a saját Azure Resource Manager üzemelő példány neve és erőforráscsoport-nevet.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Az Azure Resource Manager után a sablon elküldésekor a módosítás alkalmazása előtér ASE / nagyjából negyven percet vesz igénybe.  Például egy alapértelmezett méretű ASE használatával két az előtér-kiszolgálókon, az a sablon percet vesz igénybe körülbelül egy óra és húsz végrehajtásához.  A sablon futása közben az ASE nem lesz képes van ellátva.  

Miután befejeződött a sablont, az ILB ASE alkalmazások elérhetők HTTPS-kapcsolaton keresztül, és a kapcsolat biztonságát az alapértelmezett SSL-tanúsítvány használatával.  Az alapértelmezett SSL-tanúsítvány használható, ha az ILB ASE alkalmazásokat az alkalmazás nevét és az alapértelmezett állomásnév együttes használatával foglalkozik.  Például *https://mycustomapp.internal-contoso.com* az alapértelmezett SSL-tanúsítványt használna **.internal-contoso.com*.

Azonban a több-bérlős nyilvános szolgáltatásban futó alkalmazások, mint a fejlesztők is is konfigurálhatja az egyes alkalmazások egyéni állomásnevek, és konfigurálja egyedi SNI SSL-tanúsítványok kötései az egyes alkalmazások.  

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [App Service Environment bemutatása](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

