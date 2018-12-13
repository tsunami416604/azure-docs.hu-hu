---
title: Az oktatóanyag PowerShell – Azure dedikált HSM használata meglévő virtuális hálózat üzembe helyezése |} A Microsoft Docs
description: Történő üzembe helyezése meglévő virtuális hálózatban a PowerShell használatával egy dedikált HSM bemutató oktatóanyag
services: dedicated-hsm
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 3f165b5d372168ef3ce6fea75547513a0148ae5b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086307"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Oktatóanyag – üzembe helyezés HSM-EK virtuális hálózatban a meglévő PowerShell-lel

Az Azure dedikált HSM szolgáltatás biztosít egy fizikai eszköz az egyetlen ügyfél szolgáltatással, teljes körű rendszergazdai felügyeletet és a teljes felügyeleti feladata. Miatt biztosít a fizikai hardver, a Microsoft ellenőrzés kell azokat az eszközöket kiosztás hatékonyan felügyelt kapacitás biztosítása érdekében. Ennek eredményeképpen egy Azure-előfizetésen belül a dedikált HSM-szolgáltatást nem általában lesz látható az erőforrás-kiépítés. A dedikált HSM szolgáltatáshoz való hozzáférést igénylő bármely Azure-ügyfél először kapcsolatba kell lépnie a kérelem regisztráció a Microsoft fiókért a dedikált HSM-szolgáltatás. Csak egyszer, a folyamat sikeresen befejeződött-kiépítés nem lehetséges.
Ez az oktatóanyag célja, hogy egy tipikus kiépítési folyamat megjelenítése ahol:

- Egy ügyfél már rendelkezik egy virtuális hálózatot
- A virtuális gépek rendelkeznek
- HSM-erőforrások hozzáadása a meglévő környezetben van szükségük.

Egy tipikus, magas rendelkezésre állású, több régióban történő üzembe helyezés architektúrája előfordulhat, hogy hasonlítania:

![Több régióban történő üzembe helyezés](media/tutorial-deploy-hsm-powershell/high-availability.png)

Ebben az oktatóanyagban a HSM-EK párjai koncentrál, és a szükséges ExpressRoute-átjárót (lásd a fenti Subnet-1) történő integrálását egy meglévő virtuális hálózat (lásd a fenti 1 virtuális hálózat).  Minden egyéb erőforrás Azure-erőforrások. Integráció eljárást 4-alhálózatot a virtuális hálózatok közötti 3 fenti HSM-eket is használható.

## <a name="prerequisites"></a>Előfeltételek

Az Azure, a dedikált HSM jelenleg nem áll rendelkezésre az Azure Portalon, ezért összes kommunikációt a szolgáltatással parancssori vagy a Powershellen keresztül. Ez az oktatóanyag az Azure Cloud Shell PowerShell fogja használni. Ha még nem ismeri a powershellt, kövesse bevezetés utasításokat itt: [Azure PowerShell első lépései](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-5.0.0).

Előfeltételek:

- A dedikált HSM Azure regisztrációs folyamaton keresztül lettek, és a jóvá lett hagyva, a szolgáltatás használatára. Ha nem, majd a részletekért forduljon a Microsoft-fiók kapcsolattartójához. 
- Létrehozott egy erőforráscsoport ezekhez az erőforrásokhoz, és ebben az oktatóanyagban üzembe helyezett újakat csatlakozni fog az adott csoport.
- A szükséges virtuális hálózat, alhálózat és virtuális gépek megfelelően a fenti már létrehozott, és most alkalmazásaiba kívánják integrálni 2 HSM-EK a telepítés.

Minden, az alábbi utasítások azt feltételezik, hogy az Azure Portalra már megnyit és nyitotta meg a Cloud Shellben (kiválasztása "\>\_" felső részen található, a portál jobb).

## <a name="provisioning-a-dedicated-hsm"></a>A dedikált HSM-kiépítés

A HSM-EK kiépítés és integrálása a meglévő virtuális hálózat ExpressRoute-átjárót érvényesíti használatával az ssh parancssori eszköz lemezekről és a HSM eszközt bármely további konfigurációs tevékenységek alapszintű rendelkezésre állásának biztosításához. A következő parancsokat egy Resource Manager-sablon használatával hozza létre a HSM-erőforrások és a kapcsolódó hálózati erőforrások.

### <a name="validating-feature-registration"></a>A funkció regisztrálásának ellenőrzése

Mint már említettük, a kiépítési tevékenység, hogy a dedikált HSM szolgáltatás regisztrálva van-e az előfizetés szükséges. Ellenőrizze, hogy, a portál Azure cloud shellben futtassa a következő PowerShell-parancsot. 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

A következő parancs ellenőrzi a hálózatkezelési szolgáltatásokat, a dedikált HSM-szolgáltatás szükséges.

```powershell
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowBaremetalServers
```

Mindkét parancsot adja vissza "Regisztrálva" állapottal (lásd alább), mielőtt folytatná bármely.  Ha szeretne regisztrálni ezt a szolgáltatást, forduljon a Microsoft-ügyfélmenedzserhez.

![előfizetés állapota](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

Egy HSM-eszközre van kiépítve egy ügyfelei virtuális hálózatban. Ez azt jelenti, hogy a követelmény egy alhálózathoz. A HSM-hez a virtuális hálózat és a fizikai eszköz közötti kommunikáció engedélyezéséhez felvettek egy ExpressRoute-átjárót, és végül egy virtuális gépet a HSM eszközt az Gemalto ügyfélszoftverrel eléréséhez szükséges. Ezeket az erőforrásokat egy sablon fájlt, a kapcsolódó alkalmazásparaméter-fájlt, a használat megkönnyítése érdekében történő összegyűjtése. A fájlok elérhetők közvetlenül a Microsofthoz fordulva HSMrequest@Microsoft.com.

Miután a fájlokat, szerkesztenie kell a alkalmazásparaméter-fájlt az előnyben részesített nevét erőforrásokhoz beszúrásához. Ez azt jelenti, hogy a "value" sorok szerkesztésével: "".

- `namingInfix` HSM-erőforrások nevének előtagja
- `ExistingVirtualNetworkName` A HSM-eket használja a virtuális hálózat neve
- `DedicatedHsmResourceName1` Az Adatközpont bélyegzőjében 1 HSM-erőforrás neve
- `DedicatedHsmResourceName2` Az Adatközpont bélyegzőjében 2 HSM-erőforrás neve
- `hsmSubnetRange` Alhálózati IP-címtartományt a HSM-EK
- `ERSubnetRange` Alhálózati IP-címtartományt a virtuális hálózati átjáró

Ezek a változások, például a következőképpen történik:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

A társított Resource Manager-sablonfájl 6 erőforrásokat hozza létre ezt az információt:

- A HSM-eket a megadott virtuális hálózat alhálózat
- A virtuális hálózati átjáró-alhálózat 
- A hardveres biztonsági modulokhoz a virtuális hálózathoz csatlakozó virtuális hálózati átjáró
- Az átjáró nyilvános IP-cím
- A stamp 1 HSM
- A stamp 2 HSM

Miután beállította a paraméterértékeket, a fájlokat fel kell tölteni a portál Azure cloud shell-megosztás használatra kell. Az Azure Portalon kattintson a "\>\_" cloud shell szimbólum felül jobb oldalon, és ez elvégzi a képernyőn egy parancs környezet alsó részén. A beállítások a BASH és a PowerShell, és meg kell BASH válassza, ha nem már be van állítva.

A parancs-rendszerhéj elemre az eszköztárban feltöltési/letöltési lehetősége van, és ezzel a sablonnal és paraméterfájlokkal fájlok feltöltése a fájlmegosztás választhat:

![fájlmegosztás](media/tutorial-deploy-hsm-powershell/file-share.png)

A fájlok feltöltése után készen áll az erőforrások létrehozásához.
Új HSM létrehozása előtt biztosítania kell néhány előfeltételként erőforrás erőforrások érvényben vannak. Számítási, a HSM-EK és az átjáró-alhálózat címtartományai rendelkező virtuális hálózaton kell rendelkeznie. A következő parancsokat szolgálja ki, mit kell létrehoznia egy virtuális hálózat egy példa.

```powershell
$compute = New-AzureRmVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzureRmDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzureRmVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzureRmVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzureRmVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>Vegye figyelembe a virtuális hálózathoz, a legfontosabb konfigurációs, hogy az IP-alhálózatot a HSM eszközt kell rendelkeznie a delegálásokat "Microsoft.HardwareSecurityModules/dedicatedHSMs" értékre.  A HSM-kiépítés e nélkül nem működnek.

Miután teljesüljenek érvényesítve van, futtassa a következő parancsot használata a Resource Manager-sablon biztosítja az egyedi névvel ellátott értékek frissítése (legalább az erőforráscsoport neve):

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Ez a parancs körülbelül 20 percet vesz igénybe. A "-verbose" beállítás biztosítja, folyamatosan állapota.

![kiépítési állapot](media/tutorial-deploy-hsm-powershell/progress-status.png)

Sikeresen befejeződött, ha látható a "provisioningState": "Sikeres", jelentkezzen be a meglévő virtuális gépet és SSH-val a HSM eszközt rendelkezésre állásának biztosításához.

## <a name="verifying-the-deployment"></a>A telepítés ellenőrzése

Az eszközök vannak kiépítve, és tekintse meg az eszköz attribútumait ellenőrzéséhez futtassa a következő parancsot. Győződjön meg arról, megfelelően van-e állítva az erőforráscsoportot, és az erőforrás neve pontosan, ahogy a paraméterfájlban rendelkezik.

```powershell

$subid = (Get-AzureRmContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzureRmResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![Kiépítés állapota](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Most is kell az erőforrások használatával megtekintheti a [Azure erőforrás-kezelő](https://resources.azure.com/).   Egyszer az a Explorerben bontsa ki a bal oldali "előfizetések", bontsa ki az adott dedikált HSM-előfizetést, bontsa ki a "erőforráscsoportok", bontsa ki a használt erőforráscsoportot, és végül válassza ki a "resources" elemet.

## <a name="testing-the-deployment"></a>A központi telepítés tesztelése

A telepítés tesztelésére egy virtuális gépet, amely hozzáférhet a HSM(s) csatlakozik, majd közvetlenül a HSM eszközt csatlakozzon egy esetet. Ezek a műveletek megerősíti a HSM érhető el.
Az ssh eszköz segítségével csatlakozzon a virtuális géphez. A parancs a következőhöz hasonló lesz, de a rendszergazda nevét és a dns-név, paraméterben megadott.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A jelszó használata pedig a paraméter-fájlból.
Miután bejelentkezett a Linux rendszerű virtuális gép a HSM-be a magánhálózati IP-cím található az erőforrás a portál használatával jelentkezzen be <prefix>hsm_vnic.

```powershell

(Get-AzureRmResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Ha az IP-címet, futtassa a következő parancsot:

`ssh tenantadmin@<ip address of HSM>`

Ha sikeres kéri a jelszót. Az alapértelmezett jelszót pedig a JELSZÓT. A HSM ekkor megkérdezi, hogy a jelszó módosítása tehát egy erős jelszót, ha a szervezet részesíti előnyben a jelszó tárolásához és az adatvesztés elkerülése érdekében mechanizmus.  

>[!IMPORTANT]
>Ha elveszíti a ezt a jelszót, a hardveres biztonsági MODULT kell állítani, és azt jelenti, hogy elveszítenék a kulcsokat.

A HSM eszközt, az ssh használata a csatlakoztatott, futtassa a következő parancsot annak biztosítására, hogy működik a hardveres biztonsági MODULT.

`hsm show`

A kimenetnek az alábbi képhez hasonlóan kell kinéznie:

![Kiépítés állapota](media/tutorial-deploy-hsm-powershell/output.png)

Ezen a ponton minden erőforrások magas rendelkezésre állású, két HSM üzembe helyezési és ellenőrzött hozzáférési és működési állapotot van lefoglalva. Minden további konfigurációs vagy a tesztelés magában foglalja a több munkát magát a HSM eszközzel. Ehhez kövesse a Gemalto Luna hálózati HSM 7 felügyeleti útmutató inicializálása a hardveres biztonsági MODULT, és hozzon létre partíciókat 7. fejezet utasításait. Az összes dokumentáció, szoftvereszközök érhetők el közvetlenül letölthető Gemalto regisztrálva lettek a Gemalto Ügyfélportál-támogatás a, és van egy ügyfél-azonosító. Töltse le az ügyfélszoftvert verzió 7.2 beolvasni minden szükséges összetevőt.

## <a name="delete-or-clean-up-resources"></a>Törölje vagy az erőforrások törlése

Ha ezzel végzett, az csak a HSM eszközt, ezután képes lehet erőforrásként törlése és a szabad készlethez adott vissza. A nyilvánvaló kérdés, ha ez az eszköz bizalmas felhasználói adatok. Eltávolítása bizalmas felhasználói adatok az eszköz gyári alaphelyzetbe az Gemalto ügyfél kell lennie. Tekintse meg a Gemalto rendszergazdák útmutatóját az SafeNet hálózati Luna 7 eszköz, és vegye figyelembe a következő parancsok sorrendben.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `network interface delete -device eth0`
4. `network interface delete -device eth1`
5. `network interface delete -device eth2`
6. `network interface delete -device eth3`
7. `my file clear -f`
8. `my public-key clear -f`
9. `syslog rotate`


>[!NOTE]
Ha bármely Gemalto eszköz konfigurációs probléma, forduljon [Gemalto ügyfél-támogatási](https://safenet.gemalto.com/technical-support/).

Ha ezzel végzett, az erőforrások az erőforráscsoportban, majd eltávolíthatja őket a a következő parancsot:

```powershell

$subid = (Get-AzureRmContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzureRmResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>További lépések

Az oktatóanyagban a lépések elvégzése után a dedikált HSM erőforrások üzemképes állapotának és a virtuális hálózaton rendelkezésre állnak. Most már Ön olyan helyzetben, hogy kiegészítsék a központi telepítés által előnyben részesített üzemi architektúra szükség szerint több erőforrást. Útmutatás nyújtása a központi telepítésének tervezése a további információkért lásd: a fogalmak dokumentumokat. Egy kialakítás a két HSM-EK egy elsődleges régióban, rendelkezésre állást, az állvány szintjén addressing és a egy másodlagos régióban régiónkénti rendelkezésre állás címzés két HSM-EK használata javasolt. A sablonfájl, a jelen oktatóanyagban használt két HSM központi telepítés alapjaként könnyen használható, de rendelkeznie kell módosítani az igényeinek paramétereket.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Monitorozás](monitoring.md)
* [Támogatási lehetőségek](supportability.md)
