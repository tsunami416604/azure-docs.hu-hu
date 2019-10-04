---
title: Távoli asztali átjáró használata az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet konfigurálása |} A Microsoft Docs
description: Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet konfigurálása a távoli asztali átjáró nélkül tegye elérhetővé az RDP-portra a labor virtuális gépeken való biztonságos hozzáférés biztosítása érdekében az útmutató.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 430734878c01d10a4e7dd385dc75d8d502a2d82c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079001"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Az Azure DevTest Labs szolgáltatásban egy távoli asztali átjáró használatára a tesztkörnyezet konfigurálása
Az Azure DevTest Labs szolgáltatásban konfigurálhatja a tesztkörnyezethez a labor virtuális gépeken (VM) biztonságos elérésének biztosításához egy távoli asztali átjáró nélkül tegye elérhetővé az RDP-portra. A labor létrehozása a labor felhasználók megtekintése, és ők is hozzáférhetnek az összes virtuális gép kapcsolódni egy központi helyet biztosít. A **Connect** gombot a **virtuális gép** lap létrehoz egy számítógép-specifikus RDP-fájlt, amely megnyithat, és kapcsolódjon a géphez. További testreszabására és biztonságossá tétele az RDP-kapcsolat a labor csatlakozik egy távoli asztali átjáró. 

Ez a módszer azért biztonságosabb, mert a lab-felhasználó hitelesíti a közvetlenül az átjárót tartalmazó számítógépen, vagy használhat gépeikhez csatlakozni a tartományhoz csatlakoztatott átjárót tartalmazó számítógépen céges hitelesítő adataikat. A lab használatát is támogatja az átjárót tartalmazó számítógépen, amely lehetővé teszi a felhasználók csatlakozzanak a labor virtuális gépek anélkül, hogy az RDP-port közvetlenül az internetre csatlakozó eszközjogkivonattal történő hitelesítés. Ez a cikk végigvezeti egy példa való laborgépen csatlakozni az eszközjogkivonattal történő hitelesítés használó tesztkörnyezet beállítása.

## <a name="architecture-of-the-solution"></a>A megoldás architektúrája

![A megoldás architektúrája](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. A [első RDP-fájl tartalmának](/rest/api/dtl/virtualmachines/getrdpfilecontents) művelet nevezzük, amikor kiválasztja a **Connect** button.1. 
1. Az RDP első fájl tartalma művelet meghívása `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` kérése egy hitelesítési tokent.
    1. `{gateway-hostname}` az átjáró állomásneve megadva a **labor beállítások** oldal az Azure Portalon a tesztkörnyezethez. 
    1. `{lab-machine-name}` az a név, a gép, amely kapcsolódni próbál.
    1. `{port-number}` Ez a port, amelyen a kapcsolatot kell létesíteni. Általában ez a port a 3389-es. Ha a labor virtuális gép által használt a [megosztott IP](devtest-lab-shared-ip.md) funkció a DevTest Labs szolgáltatásban, a port eltérő lesz.
1. A távoli asztali átjáró késleltet a hívás a `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` , egy Azure-függvényt a hitelesítési jogkivonat létrehozásához. A DevTest Labs szolgáltatás a függvény kulcsot automatikusan tartalmazza a kérés fejlécében. A függvény kulcs, hogy a labor létrehozása a key vault mentve. Jelennek meg a titkos kód nevét **átjáró jogkivonat titkos kulcs** a a **labor beállítások** lap a tesztkörnyezethez.
1. Az Azure-függvény várhatóan az átjárót tartalmazó számítógépen token hitelesítésére tanúsítványalapú jogkivonat.  
1. Az első RDP-fájl tartalmát a művelet, majd a teljes RDP-fájlt, beleértve a hitelesítési adatokat adja vissza.
1. Megnyitja az RDP-fájlt az előnyben részesített RDP-kapcsolat program segítségével. Ne feledje, hogy nem minden RDP-kapcsolat program támogatja-e a jogkivonat-hitelesítés. A hitelesítési jogkivonat lejárati dátummal rendelkeznek, és állítsa a függvényalkalmazáshoz. A jogkivonat lejárata előtt, győződjön meg a kapcsolat a tesztkörnyezeti virtuális Gépet.
1. A távoli asztali átjárót tartalmazó számítógépen hitelesíti a jogkivonatot az RDP-fájlban, miután a kapcsolat a labor gép lesznek továbbítva.

### <a name="solution-requirements"></a>A megoldás követelményei
A DevTest Labs-tokent használó hitelesítés biztosítását funkció dolgozni, nincsenek az átjáró gépek, a tartománynév-szolgáltatásokat (DNS) és a functions néhány konfigurációs követelményei.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>A távoli asztali átjáró gépekre vonatkozó követelmények
- SSL-tanúsítványt kell telepíteni az átjárót tartalmazó számítógépen HTTPS-forgalom kezelésére. A tanúsítványt meg kell egyeznie a teljesen minősített tartománynevét (FQDN) a terheléselosztó az átjáró farm vagy teljes Tartománynevét a gépet, ha csak egy számítógépen. Helyettesítő SSL-tanúsítványok nem működnek.  
- Átjáró gép(ek) telepíteni egy aláíró tanúsítványt. Hozzon létre egy aláíró tanúsítványt használatával [létrehozás-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) parancsfájlt.
- Telepítse a [Pluggable Authentication](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) modul, amely támogatja az eszközjogkivonattal történő hitelesítés a távoli asztali átjáró. Egy példa az ilyen modulok `RDGatewayFedAuth.msi` , amely együttműködik a [System Center Virtual Machine Manager (VMM) lemezképek](/system-center/vmm/install-console?view=sc-vmm-1807). További információ a System Center: [a System Center dokumentációja](https://docs.microsoft.com/system-center/) és [díjszabás](https://www.microsoft.com/cloud-platform/system-center-pricing).  
- Az átjárókiszolgáló képes kezelni az irányuló kérelmek biztonságosak `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`.

    Az átjáró-állomásnév akkor a load balancer az átjáró-farm teljes Tartománynevét vagy üzemelő gépen teljes Tartománynevét, ha csak egy gép. A `{lab-machine-name}` próbál csatlakozni, labor gép neve és a `{port-number}` port, amelyen a kapcsolat hozható létre.  Alapértelmezés szerint ez a port a 3389-es.  Azonban ha a virtuális gép használja a [megosztott IP](devtest-lab-shared-ip.md) funkció a DevTest Labs szolgáltatásban, a port eltérő lesz.
- A [alkalmazáskérelem útválasztási](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) Internet Information Server (IIS) modul segítségével átirányítási `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` az azure-függvény, amely kezeli a hitelesítési jogkivonat beszerzéséhez a kérelem, kéréseket.


## <a name="requirements-for-azure-function"></a>Azure-függvény követelményei
Azure-függvény leírók kérelem formátuma `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` és a hitelesítési jogkivonat alapján azonos értéket ad vissza az aláíró tanúsítvány az átjáró-gépek telepítve. A `{function-app-uri}` az URI-t a funkció eléréséhez használt. A függvény kulcs automatikusan van átadva a kérelem fejlécében. Lásd: a sample függvény [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs). 


## <a name="requirements-for-network"></a>Hálózati követelményei

- Az SSL-tanúsítvány az átjáró-gépeken telepített társított teljes DNS közvetlen kell az átjárót tartalmazó számítógépen, vagy az átjáró-gép farm a terheléselosztó forgalmat.
- Ha a labor gépet használ a magánhálózati IP-címek, hálózati elérési utat az átjárót tartalmazó számítógépen a labor gép megosztása ugyanazon a virtuális hálózaton vagy virtuális társhálózatok használatával keresztül kell.

## <a name="configure-the-lab-to-use-token-authentication"></a>Jogkivonat-hitelesítés használatára a tesztkörnyezet konfigurálása 
Ez a szakasz ismerteti, hogyan konfigurálható a tesztkörnyezet egy távoli asztali átjáró a gép, amely támogatja az eszközjogkivonattal történő hitelesítés használata. Ez a szakasz nem fedi le egy távoli asztali átjáró farm maga beállítása. Ez az információ, tekintse meg a [mintát hozhat létre egy távoli asztali átjáró](#sample-to-create-a-remote-desktop-gateway) szakaszban Ez a cikk végén található. 

A tesztkörnyezet beállításainak frissítéséhez, tárolja a kulcsot, a függvény által visszaadott egy hitelesítési tokent a key vaultban a tesztlabor sikeres végrehajtásához szükséges. A függvény kulcsérték kezdheti a **kezelés** oldalon, a függvény az Azure Portalon. A titkos kulcs mentése a key vault további információkért lásd: [titkos kód hozzáadása a Key Vault](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault). Mentse a titkos kód nevét későbbi használatra.

A key vault a labor Azonosítójának megkereséséhez futtassa a következő Azure CLI-parancsot: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Adja meg a labor létrehozása a jogkivonat-hitelesítés használatához az alábbi módon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában válassza ki a **labor**.
1. A labor oldalán válassza **Konfigurace a zásady**.
1. A bal oldali menüben az a **beállítások** szakaszban jelölje be **labor beállítások**.
1. Az a **a távoli asztal** szakaszban írja be a teljesen minősített tartománynevét (FQDN) vagy IP-címét a távoli asztali átjárót tartalmazó számítógépen, vagy a farm a **átjáró állomásnév** mező. Ezt az értéket meg kell egyeznie az átjáró gépeken használt SSL-tanúsítvány teljes Tartománynevét.

    ![Távoli asztal beállításainak a labor-beállítások](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. Az a **a távoli asztal** szakaszban a **az átjáró jogkivonata** titkos kulcsot, adja meg a korábban létrehozott titkos kód nevét. Ez az érték nem magát a függvény kulcsot, de a titkos kulcsot, a függvény kulcsot tartalmazó kulcstartó a labor nevét.

    ![A lab beállítások átjáró jogkivonat titkos kulcsa](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Mentés** módosításokat.

    > [!NOTE] 
    > Kattintva **mentése**, el kell fogadnia [távoli asztali átjáró licencfeltételek](https://www.microsoft.com/licensing/product-licensing/products). Távoli átjáró kapcsolatos további információkért lásd: [üdvözli a távoli asztali szolgáltatások](https://aka.ms/rds) és [a távoli asztali környezet üzembe helyezése](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).


Ha a labor keresztül automation konfigurálásával részesíti előnyben, tekintse meg [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) beállítani egy minta PowerShell-parancsfájlt a **átjáró állomásnév** és **átjáró jogkivonat titkos kulcs**beállításait. A [Azure DevTest Labs GitHub-adattár](https://github.com/Azure/azure-devtestlab) is biztosít az Azure Resource Manager-sablon, amely létrehozza vagy frissíti a tesztkörnyezet a **átjáró állomásnév** és **átjáró jogkivonat titkos kulcs**beállításait.

## <a name="configure-network-security-group"></a>Hálózati biztonsági csoport konfigurálása
A labor további védelméhez, a virtuális hálózat a labor virtuális gépek által használt hálózati biztonsági csoport (NSG) is hozzáadhatók. Útmutatás hogyan állítsa be az NSG-KET, [létrehozása, módosítása vagy törlése a hálózati biztonsági csoport](../virtual-network/manage-network-security-group.md).

Íme egy példa NSG-t, amely csak engedélyezi a forgalmat, amely először halad végig laborgépen elérni az átjárót. Ez a szabály a forrása az egyetlen átjárót tartalmazó számítógép IP-címét, vagy az átjáróként működő gépek előtti terheléselosztó IP-címét.

![Hálózati biztonsági csoport – szabályok](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>A minta egy távoli asztali átjáró létrehozása

> [!NOTE] 
> A mintasablonokat használatával elfogadja [távoli asztali átjáró licencfeltételek](https://www.microsoft.com/licensing/product-licensing/products). Távoli átjáró kapcsolatos további információkért lásd: [üdvözli a távoli asztali szolgáltatások](https://aka.ms/rds) és [a távoli asztali környezet üzembe helyezése](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

A [Azure DevTest Labs GitHub-adattár](https://github.com/Azure/azure-devtestlab) biztosít néhány különböző minták segítik a telepítő a jogkivonat-hitelesítés és a távoli asztali átjáró használatára a DevTest Labs szükséges erőforrásokat. Ezek a minták átjáró gépek, a labor beállításokat és a függvényalkalmazás Azure Resource Manager-sablonokat tartalmaznak.

Kövesse az alábbi lépéseket egy minta megoldás a távoli asztali átjáró farm beállításához.

1. Hozzon létre egy aláíró tanúsítványt.  Futtatás [létrehozása SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1). Mentse az ujjlenyomatot, jelszó és a létrehozott tanúsítvány Base64 kódolást.
2. SSL-tanúsítvány beszerzése. Az SSL-tanúsítvány társított FQDN szabályozhatja a tartományban kell lennie. Mentse az ujjlenyomatot, jelszó és a tanúsítvány Base64 kódolást. PowerShell-lel ujjlenyomat lekéréséhez használja a következő parancsokat.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    A PowerShell-lel Base64 kódolást lekéréséhez használja az alábbi parancsot.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. A fájlokat letöltjük [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway).

    A sablon néhány más Resource Manager-sablonokkal és az azonos alap URI-n található kapcsolódó erőforrásokhoz való hozzáférés szükséges. Másolja ki az összes fájl [ https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) és RDGatewayFedAuth.msi storage-fiókban található blob-tárolóba.  
4. Üzembe helyezése **azuredeploy.json** a [ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway). A sablon a következő paramétereket fogadja:
    - adminUsername – kötelező.  Az átjáróként működő gépek rendszergazdai felhasználóneve.
    - adminPassword – kötelező. Az átjáróként működő gépek rendszergazdai fiókjának jelszava.
    - instanceCount – hozhat létre átjárót gépek száma.  
    - alwaysOn – azt jelzi, hogy a létrehozott Azure Functions-alkalmazás online állapotban tartani, vagy sem. Az Azure Functions az alkalmazás elkerüli a késések, amikor a felhasználó először próbál kapcsolódni a tesztkörnyezeti virtuális Gépet, de ez kihatással költség.  
    - tokenLifetime – mennyi ideig érvényes lesz a létrehozott tokent. Formátum ÓÓ:.
    - sslCertificate – a Base64 kódolás az SSL-tanúsítványt az átjárót tartalmazó számítógépen.
    - sslCertificatePassword – az SSL-tanúsítványt az átjárót tartalmazó számítógépen, a jelszót.
    - sslCertificateThumbprint - azonosító az SSL-tanúsítványt a helyi tanúsítványtárolóban található a tanúsítvány ujjlenyomatát.
    - signCertificate – a Base64 kódolás az átjárót tartalmazó számítógépen a tanúsítvány aláírására.
    - signCertificatePassword – aláíró az átjárót tartalmazó számítógépen a tanúsítvány jelszavát.
    - signCertificateThumbprint – a tanúsítvány ujjlenyomatát a helyi tanúsítványtárolóban, az aláíró tanúsítvány azonosításához.
    - _artifactsLocation – URI elérési útját összes támogató erőforrást is. Ez az érték egy teljesen minősített UIR, nem relatív elérési útnak kell lennie.
    - _artifactsLocationSasToken – a hely egy Azure storage-fiókot a támogató erőforrások eléréséhez használt a közös hozzáférésű Jogosultságkód (SAS) tokent.

    A sablon az Azure CLI használatával a következő paranccsal telepíthető:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation=”{storage-account-endpoint}/{container-name}” -–parameters _artifactsLocationSasToken = “?{sas-token}”
    ```

    Az alábbiakban a paraméterek leírásait:

    - A {storage-fiók-végponthoz} futtatásával szerezhető `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`.  A {tároló-acct-} név a feltöltött fájlokat tartalmazó tárfiók nevére.  
    - A {tároló neve} a {tárolási acct-neve} a tárolóba feltöltött fájlokat tartalmazó neve.  
    - A {sas-token} futtatásával szerezhető `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`. 
        - A {tároló-acct-} név a feltöltött fájlokat tartalmazó tárfiók nevére.  
        - A {tároló neve} a {tárolási acct-neve} a tárolóba feltöltött fájlokat tartalmazó neve.  
        - A {utc-lejárati – dátum} az a dátum (UTC), amelyen a SAS-jogkivonat lejár, és a SAS-jogkivonat már nem használható a storage-fiók eléréséhez.

    Jegyezze fel a gatewayFQDN és a központi telepítési sablon kimenetében gatewayIP értéke. Is kell menteni az újonnan létrehozott függvény, amely megtalálható a függvény kulcs értékét a [Alkalmazásbeállítások függvény](../azure-functions/functions-how-to-use-azure-function-app-settings.md) fülre.
5. Konfigurálja a DNS-, így az előző lépésben gatewayIP IP-címére irányítja a teljes Tartománynevet az SSL-tanúsítványt.

    Miután a távoli asztali átjáró farm létrehozása, és a megfelelő DNS-frissítések jönnek létre, készen áll DevTest Labs szolgáltatásban létrehozott tesztkörnyezet által használható. A **átjáró állomásnév** és **átjáró jogkivonat titkos kulcs** beállításokat kell megadni a telepített átjáró gép(ek) használatára. 

    > [!NOTE]
    > Ha a labor gépet használ a magánhálózati IP-címek, hálózati elérési utat az átjárót tartalmazó számítógépen a labor gép megosztása ugyanazon a virtuális hálózaton, vagy egy társított virtuális hálózat használatával keresztül kell.

    Átjáró és a tesztkörnyezet konfigurálása után a kapcsolat fájl létrehozásakor a lab-felhasználó kattint a **Connect** automatikus módon kiegészül eszközjogkivonattal történő hitelesítés használata csatlakoztatásához szükséges információkat.     

## <a name="next-steps"></a>További lépések
További információ a távoli asztali szolgáltatások a következő cikkben talál: [Távoli asztali szolgáltatások dokumentációja](/windows-server/remote/remote-desktop-services/Welcome-to-rds)


