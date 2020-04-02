---
title: A Távoli asztali átjáró azure DevTest Labs-ben való használatára konfigurált labor
description: Ismerje meg, hogyan konfigurálhat egy tesztkörnyezetet az Azure DevTest Labs egy távoli asztali átjáróval, hogy biztonságos hozzáférést biztosítson a labor virtuális gépekhez anélkül, hogy elérhetővé kellene tennie az RDP-portot.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2cdafa9a36a5f906151ca6946e18ef82bc7f1e01
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529421"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>Konfigurálja a tesztkörnyezetet az Azure DevTest Labs ben egy távoli asztali átjáró használatára
Az Azure DevTest Labs konfigurálhatja a tesztkörnyezet egy távoli asztali átjáró a labor biztonságos hozzáférést biztosít a labor virtuális gépek (VM-ek) anélkül, hogy az RDP-port. A labor központi helyet biztosít a tesztkörnyezet felhasználói számára, hogy megtekinthessék és csatlakozzanak az összes olyan virtuális géphez, amelyhez hozzáféréssel rendelkeznek. A **Virtuálisgép** lap **Csatlakozás** gombja létrehoz egy gépspecifikus RDP-fájlt, amelyet megnyithat a számítógéphez való csatlakozáshoz. Az RDP-kapcsolat további testreszabásához és védelméhez csatlakoztassa a tesztkörnyezetegy távoli asztali átjáró. 

Ez a megközelítés biztonságosabb, mert a tesztkörnyezet-felhasználó hitelesíti magát közvetlenül az átjárógép, vagy használhatja a vállalati hitelesítő adatok at a tartományhoz csatlakozott átjárógép csatlakozhat a gépekhez. A labor is támogatja a jogkivonat-hitelesítést az átjárógép, amely lehetővé teszi a felhasználók számára, hogy csatlakozzanak a labor virtuális gépek anélkül, hogy az RDP-port elérhető az interneten. Ez a cikk bemutatja, hogyan állíthat be egy tesztkörnyezet, amely jogkivonat-hitelesítést használ a tesztkörnyezet-gépekhez való csatlakozáshoz.

## <a name="architecture-of-the-solution"></a>A megoldás architektúrája

![A megoldás architektúrája](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. Az [RDP-fájl tartalmának beszereznie](/rest/api/dtl/virtualmachines/getrdpfilecontents) művelet a **Csatlakozás** gomb kiválasztásakor hívható meg.1. 
1. Az RDP-fájl tartalmának `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` bekérése művelet hitelesítési jogkivonat kéréséhez hív meg.
    1. `{gateway-hostname}`az azure portalon a **labor laborkonfigurációjának laborbeállítások** lapján megadott átjáróállomásnév. 
    1. `{lab-machine-name}`annak a gépnek a neve, amelyet csatlakoztatni próbál.
    1. `{port-number}`az a port, amelyen a kapcsolatot létre kell hozni. Általában ez a port 3389. Ha a labor virtuális gép a [megosztott IP-szolgáltatás](devtest-lab-shared-ip.md) DevTest Labs, a port más lesz.
1. A távoli asztali átjáró elhalasztja `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` a hívást egy Azure-függvénya hitelesítési jogkivonat létrehozásához. A DevTest Labs szolgáltatás automatikusan tartalmazza a függvénykulcsot a kérelem fejlécében. A funkciókulcsot a labor kulcstartójába kell menteni. A titkos kulcs neve, amely **átjárójogkivonat titkos** kulcsként jelenik meg a labor **beállítások** lapján.
1. Az Azure-függvény várhatóan egy jogkivonatot a tanúsítvány-alapú jogkivonat-hitelesítés az átjárógép.  
1. Az RDP-fájl tartalmának beszereznie művelet ezután a teljes RDP-fájlt adja vissza, beleértve a hitelesítési adatokat is.
1. Az RDP-fájlt a kívánt RDP-kapcsolati programmal nyitja meg. Ne feledje, hogy nem minden RDP-kapcsolati program támogatja a tokenhitelesítést. A hitelesítési jogkivonat nak van egy lejárati dátuma, amelyet a függvényalkalmazás állít be. A kapcsolat a labor virtuális gép, mielőtt a jogkivonat lejár.
1. Miután a távoli asztali átjárógép hitelesíti a jogkivonatot az RDP-fájlban, a kapcsolat továbbítása a laborgépre.

### <a name="solution-requirements"></a>A megoldás követelményei
A DevTest Labs token hitelesítési szolgáltatás, néhány konfigurációs követelmények az átjáró gépek, tartománynév-szolgáltatások (DNS) és a függvények.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>A távoli asztali átjárógépekre vonatkozó követelmények
- A TLS/SSL-tanúsítványt telepíteni kell az átjárógépen a HTTPS-forgalom kezeléséhez. A tanúsítványnak meg kell egyeznie az átjárófarm terheléselosztójának teljes tartománynevével (FQDN) vagy magának a gépnek a teljes tartománynncével, ha csak egy gép van. A helyettesítő karakteres TLS/SSL tanúsítványok nem működnek.  
- Az átjárórendszer(ek)re telepített aláíró tanúsítvány. Aláírási tanúsítvány létrehozása [a Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) parancsfájl használatával.
- Telepítse a [pluggable hitelesítés](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) imodult, amely támogatja a távoli asztali átjáró tokenhitelesítését. Egy példa egy ilyen `RDGatewayFedAuth.msi` modul, hogy jön a [System Center Virtual Machine Manager (VMM) képek](/system-center/vmm/install-console?view=sc-vmm-1807). A System Center szolgáltatásról a [System Center dokumentációjában](https://docs.microsoft.com/system-center/) és [díjszabásával kapcsolatos részletekben olvashat bővebben.](https://www.microsoft.com/cloud-platform/system-center-pricing)  
- Az átjárókiszolgáló képes kezelni `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`a rendszernek a.

    Az átjáró-állomásnév az átjárófarm terheléselosztójának teljes tartományszáma vagy a gép teljes tartományneve, ha csak egy gép van. A `{lab-machine-name}` a csatlakoztatni kívánt laborgép neve, és az `{port-number}` a port, amelyen a kapcsolat létre fog menni.  Alapértelmezés szerint ez a port 3389.  Ha azonban a virtuális gép a DevTest Labs [megosztott IP-szolgáltatását](devtest-lab-shared-ip.md) használja, a port más lesz.
- Az Internet Information Server (IIS) [alkalmazás-útválasztási](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kérelem `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` modul segítségével átirányíthatja a kérelmeket az azure függvénybe, amely kezeli a hitelesítési jogkivonat lekérésére irányuló kérelmet.


## <a name="requirements-for-azure-function"></a>Az Azure-függvény követelményei
Az Azure függvény a `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` kérést az átjárógépekre telepített azonos aláíró tanúsítvány alapján adja vissza. A `{function-app-uri}` a funkció eléréséhez használt uri. A függvénykulcs automatikusan átkerül a kérelem fejlécébe. A mintafüggvényt [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)lásd: . 


## <a name="requirements-for-network"></a>A hálózatra vonatkozó követelmények

- Az átjárógépekre telepített TLS/SSL-tanúsítványhoz társított teljes qdn-nek a forgalmat az átjárógépre vagy az átjárógép-farm terheléselosztójára kell irányítania.
- Ha a laborgép privát IP-eket használ, az átjárógéptől a laborgépig kell egy hálózati elérési utat, akár ugyanazon virtuális hálózat megosztásán keresztül, akár társviszony-létesített virtuális hálózatok használatával.

## <a name="configure-the-lab-to-use-token-authentication"></a>A tesztkörnyezet konfigurálása jogkivonat-hitelesítés használatára 
Ez a szakasz bemutatja, hogyan konfigurálhat egy tesztkörnyezet et egy távoli asztali átjárógép használatára, amely támogatja a token hitelesítést. Ez a szakasz nem terjed ki a távoli asztali átjárófarm okának beállítására. Ehhez a cikkhez tekintse meg a [Minta című témakört, ha távoli asztali átjárót szeretne létrehozni](#sample-to-create-a-remote-desktop-gateway) a cikk végén. 

A labor beállítások frissítése előtt tárolja a szükséges kulcsot a függvény sikeres végrehajtásához, hogy egy hitelesítési jogkivonatot ad vissza a tesztkörnyezet key vaultjában. A függvénykulcs-értéket az Azure Portalon a függvény **kezelése** lapján szerezheti be. A titkos kulcs kulcstartóba mentését a [Titkos kulcs hozzáadása a Key Vaulthoz](../key-vault/quick-create-portal.md#add-a-secret-to-key-vault)című témakörben talál. Mentse a titkos név későbbi használatra.

A tesztkörnyezet kulcstartójának azonosítójának megkereséséhez futtassa a következő Azure CLI-parancsot: 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

Konfigurálja a tesztkörnyezetet a tokenhitelesítés használatára az alábbi lépésekkel:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki a **labort.**
1. A tesztkörnyezet lapján válassza a **Konfiguráció és házirendek**lehetőséget.
1. A bal oldali menü **Beállítások** szakaszában válassza a **Lab beállítások lehetőséget.**
1. A **Távoli asztal** szakaszban adja meg a távoli asztali szolgáltatások átjárógépének vagy farmjának teljesen minősített tartománynevét (FQDN) vagy IP-címét a **Gateway állomásnév** mezőhöz. Ennek az értéknek meg kell egyeznie az átjárógépeken használt TLS/SSL tanúsítvány teljes tartománynevével.

    ![Távoli asztal beállításai a tesztkörnyezet beállításaiban](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. A **Távoli asztal** szakaszátjáró-jogkivonat titkos kulcsa, adja meg a korábban létrehozott titkos kulcs nevét. **Gateway token** Ez az érték nem maga a függvénykulcs, hanem a titkos kulcs neve a labor kulcstartójában, amely a függvénykulcsot tartalmazza.

    ![Átjárótoken titkos kulcsa a laborbeállításokban](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. **Mentés** Változások.

    > [!NOTE] 
    > A **Mentés**gombra kattintva elfogadja a [Távoli asztali átjáró licencfeltételeit.](https://www.microsoft.com/licensing/product-licensing/products) A távoli átjáróról további információt az [Üdvözli a Távoli asztali szolgáltatások](https://aka.ms/rds) és a Távoli asztali környezet telepítése című [témakörben.](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)


Ha a labor automatizálási keresztül konfigurálása előnyös, olvassa el [a Set-DevLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1) című témakört a PowerShell-parancsfájl mintabeállításához **az átjáró állomásnév** és **az átjárótoken titkos** beállításait. Az [Azure DevTest Labs GitHub-tárház](https://github.com/Azure/azure-devtestlab) egy Azure Resource Manager-sablont is biztosít, amely létrehoz vagy frissít egy tesztkörnyezetet az **átjáró állomásnév** és **átjárótoken titkos** beállításaival.

## <a name="configure-network-security-group"></a>Hálózati biztonsági csoport konfigurálása
A labor további biztonságossá tétele érdekében egy hálózati biztonsági csoport (NSG) adható hozzá a tesztkörnyezet virtuális gépei által használt virtuális hálózathoz. Az NSG beállításáról a [Hálózati biztonsági csoport létrehozása, módosítása és törlése](../virtual-network/manage-network-security-group.md)című témakörben talál.

Íme egy példa Az NSG, amely csak lehetővé teszi a forgalmat, amely először megy át az átjárón, hogy elérje a laborgépek. A szabály forrása az egyetlen átjárógép IP-címe, vagy az átjárógépek előtti terheléselosztó IP-címe.

![Hálózati biztonsági csoport - szabályok](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>Példa távoli asztali átjáró létrehozására

> [!NOTE] 
> A mintasablonok használatával elfogadja a [Távoli asztali átjáró licencfeltételeit.](https://www.microsoft.com/licensing/product-licensing/products) A távoli átjáróról további információt az [Üdvözli a Távoli asztali szolgáltatások](https://aka.ms/rds) és a Távoli asztali környezet telepítése című [témakörben.](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

Az [Azure DevTest Labs GitHub-tárház](https://github.com/Azure/azure-devtestlab) néhány mintát biztosít a tokenhitelesítés és a távoli asztali átjáró DevTest Labs használatával való használatához szükséges erőforrások beállításához. Ezek a minták tartalmazzák az Azure Resource Manager-sablonokat az átjárógépekhez, a laborbeállításokhoz és a függvényalkalmazáshoz.

Az alábbi lépésekkel mintamegoldást állíthat be a távoli asztali átjárófarmhoz.

1. Hozzon létre egy aláíró tanúsítványt.  [Futtassa a Create-SigningCertificate.ps1 futtatását.](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) Mentse a létrehozott tanúsítvány ujjlenyomatát, jelszavát és Base64 kódolását.
2. TLS/SSL tanúsítvány beszerezni. A TLS/SSL-tanúsítványhoz társított teljes tartománynévnek a megadott tartományhoz kell tartandó. Mentse az ujjlenyomatot, a jelszót és a Base64 kódolást ehhez a tanúsítványhoz. Ujjlenyomat a PowerShell használatával, használja a következő parancsokat.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import(‘path-to-certificate’);
    $hash = $cer.GetCertHashString()
    ```

    A Base64 kódolás powershell használatával történő lefelvételéhez használja a következő parancsot.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(‘path-to-certificate’))
    ```
3. Fájlok letöltése a alkalmazásból. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)

    The template requires access to a few other Resource Manager templates and related resources at the same base URI. Másolja az összes [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) fájlt, és RDGatewayFedAuth.msi egy blob tároló egy tárfiókban.  
4. Telepítse **az azuredeploy.json t.** [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway) A sablon a következő paramétereket veszi figyelembe:
    - adminUsername – Kötelező.  Az átjárógépek rendszergazdai felhasználóneve.
    - adminPassword - Kötelező. Az átjárógépek rendszergazdai fiókjának jelszava.
    - instanceCount – Létrehozandó átjárógépek száma.  
    - alwaysOn – Azt jelzi, hogy a létrehozott Azure Functions alkalmazást meleg állapotban kell-e tartani. Az Azure Functions alkalmazás megtartása elkerüli a késéseket, amikor a felhasználók először próbálnak csatlakozni a laborvirtuális géphez, de költségvonzatai vannak.  
    - tokenLifetime – A létrehozott jogkivonat érvényességi ideje. A formátum: ÓÓ:PP:SS.
    - sslcertificate – Az átjárógép TLS/SSL tanúsítványának Base64 kódolása.
    - sslCertificatePassword – Az átjárógép TLS/SSL tanúsítványának jelszava.
    - sslCertificateThumbprint - A tls/SSL tanúsítvány helyi tanúsítványtárolójában történő azonosításra szolgáló tanúsítvány ujjlenyomata.
    - signCertificate – A Base64 kódolás az átjárógép tanúsítványának aláírásához.
    - signCertificatePassword – Az átjárószámítógép tanúsítványának aláírásához.
    - signCertificateThumbprint – A tanúsítvány ujjlenyomata az aláíró tanúsítvány helyi tanúsítványtárolójában történő azonosításhoz.
    - _artifactsLocation – URI hely, ahol az összes támogató erőforrás megtalálható. Ennek az értéknek teljesen minősített UIR-nek kell lennie, nem relatív elérési útnak.
    - _artifactsLocationSasToken – A megosztott hozzáférésű aláírás (SAS) jogkivonat eléréséhez használt támogató erőforrások, ha a hely egy Azure-tárfiók.

    A sablon az Azure CLI használatával telepíthető a következő paranccsal:

    ```azurecli
    az group deployment create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    Itt vannak a paraméterek leírásai:

    - A(z) {storage-account-endpoint} a `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`futtatásával szerezhető be.  A{storage-acct-name} a feltöltött fájlokat tároló tárfiók neve.  
    - A {container-name} a feltöltött fájlokat tartalmazó {storage-acct-name} tároló neve.  
    - A(z) {sas-token} a `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`futtatásával szerezhető be. 
        - A{storage-acct-name} a feltöltött fájlokat tároló tárfiók neve.  
        - A {container-name} a feltöltött fájlokat tartalmazó {storage-acct-name} tároló neve.  
        - A(z) {utc-expiration-date} az a dátum, utc-ben, amikor a SAS-token lejár, és a SAS-jogkivonat már nem használható a tárfiók eléréséhez.

    Rögzítse a gatewayFQDN és a gatewayIP értékeit a sablon központi telepítési kimenetéről. Emellett mentenie kell az újonnan létrehozott függvény funkciójának értékét, amely a [Függvény alkalmazás beállításai](../azure-functions/functions-how-to-use-azure-function-app-settings.md) lapon található.
5. Állítsa be a DNS-t úgy, hogy a TLS/SSL tanúsítvány teljes tartományszáma az előző lépéstől kezdve a gatewayIP IP-címére irányítson.

    A Távoli asztali átjáró farm létrehozása és a megfelelő DNS-frissítések létrehozása után készen áll arra, hogy a DevTest Labs tesztkörnyezetében lévő tesztkörnyezet használja. Az **átjáró állomásnév** és **átjárótoken titkos** beállításait úgy kell konfigurálni, hogy a telepített átjárógép(ek)et használják. 

    > [!NOTE]
    > Ha a laborgép privát IP-eket használ, az átjárógéptől a laborgépig kell egy hálózati elérési utat, akár ugyanazon virtuális hálózat megosztásán keresztül, akár társviszony-létesített virtuális hálózat használatával.

    Az átjáró és a labor konfigurálása után a kapcsolatfájl, amely akkor jön létre, amikor a tesztkörnyezet felhasználója a **Csatlakozásra** kattint, automatikusan tartalmazza a token hitelesítéssel történő csatlakozáshoz szükséges információkat.     

## <a name="next-steps"></a>További lépések
A Távoli asztali szolgáltatásokról az alábbi [Remote Desktop Services documentation](/windows-server/remote/remote-desktop-services/Welcome-to-rds) cikkben olvashat bővebben.


