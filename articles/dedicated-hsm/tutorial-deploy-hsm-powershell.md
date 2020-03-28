---
title: Oktatóanyag üzembe helyezése egy meglévő virtuális hálózatba a PowerShell használatával - Azure dedikált HSM | Microsoft dokumentumok
description: Oktatóanyag, amely bemutatja, hogyan telepíthet dedikált HSM-et a PowerShell használatával egy meglévő virtuális hálózatba
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: c1a847a315a264591c0d003ff691d9938c2bf0f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474424"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Oktatóanyag – HSM-ek telepítése egy meglévő virtuális hálózatba a PowerShell használatával

Az Azure dedikált HSM szolgáltatás egy fizikai eszköz egyetlen ügyfél számára, teljes körű felügyeleti felügyelet és a teljes felügyeleti felelősség. A fizikai hardverek biztosításának köszönhetően a Microsoftnak szabályoznia kell, hogy ezek az eszközök hogyan legyenek lefoglalva a kapacitás hatékony kezelésének biztosítása érdekében. Ennek eredményeképpen egy Azure-előfizetésen belül a dedikált HSM szolgáltatás általában nem lesz látható az erőforrás-kiépítés. Minden Olyan Azure-ügyfélnek, amelynek hozzáférése van a dedikált HSM-szolgáltatáshoz, először kapcsolatba kell lépnie a Microsoft-fiók végrehajtójával, hogy regisztrációt kérjen a dedikált HSM-szolgáltatáshoz. Csak akkor lehetséges, ha ez a folyamat sikeresen befejeződik.
Ez az oktatóanyag egy tipikus kiépítési folyamatot kíván bemutatni, ahol:

- Az ügyfélnek már van virtuális hálózata
- Van egy virtuális gépük
- HSM-erőforrásokat kell hozzáadniuk a meglévő környezethez.

Egy tipikus, magas rendelkezésre állású, több régióra kiterjedő telepítési architektúra a következőképpen nézhet ki:

![többrégiós telepítés](media/tutorial-deploy-hsm-powershell/high-availability.png)

Ez az oktatóanyag egy hsm-párra és a szükséges ExpressRoute-átjáróra összpontosít (lásd a fenti 1. alhálózatot), amelyet egy meglévő virtuális hálózatba integrálnak (lásd a fenti VNET 1-et).  Minden más erőforrás szabványos Azure-erőforrások. Ugyanaz az integrációs folyamat használható a 4-es alhálózat HSM-jeihez a fenti VNET 3-on.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure dedikált HSM jelenleg nem érhető el az Azure Portalon, ezért a szolgáltatással való minden interakció parancssori vagy PowerShell használatával történik. Ez az oktatóanyag a PowerShellt fogja használni az Azure Cloud Shellben. Ha most ismerkedik a PowerShell használatával, kövesse az első lépésekre vonatkozó utasításokat: [Azure PowerShell – első lépések.](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Előfeltételek:

- Az Azure dedikált HSM regisztrációs folyamaton ment keresztül, és a szolgáltatás használatára jóváhagyták. Ha nem, akkor a részletekért forduljon a Microsoft-fiók képviselőjéhez. 
- Ezeket az erőforrásokat erőforráscsoportot hozott létre, és az oktatóanyagban üzembe helyezett újak is csatlakoznak ehhez a csoporthoz.
- Már létrehozta a szükséges virtuális hálózatot, alhálózatot és virtuális gépeket a fenti ábrán látható módon, és most 2 HSM-et szeretne integrálni az üzembe helyezésbe.

Az alábbi utasítások feltételezik, hogy már navigált az Azure Portalon,\>\_és megnyitotta a Cloud Shellt (válassza a " " lehetőséget a portál jobb felső sarokja felé).

## <a name="provisioning-a-dedicated-hsm"></a>Dedikált HSM kiépítése

A HSM-ek kiépítése és egy meglévő virtuális hálózatba való integrálása az ExpressRoute Gateway-en keresztül az ssh parancssori eszközzel érvényesítve lesz, hogy biztosítsa a HSM-eszköz elérhetőségét és alapvető elérhetőségét a további konfigurációs tevékenységekhez. A következő parancsok egy Erőforrás-kezelő sablont használnak a HSM-erőforrások és a kapcsolódó hálózati erőforrások létrehozásához.

### <a name="validating-feature-registration"></a>Szolgáltatásregisztráció érvényesítése

Mint már említettük, minden kiépítési tevékenység megköveteli, hogy a dedikált HSM szolgáltatás regisztrálva van az előfizetéshez. Ennek ellenőrzéséhez futtassa a következő PowerShell-parancsot az Azure Portal felhőbeli rendszerhéjában. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

A következő parancs a dedikált HSM-szolgáltatáshoz szükséges hálózati funkciókat ellenőrzi.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowBaremetalServers
```

Mindkét parancsnak "Regisztrált" állapotot kell visszaadnia (lásd alább), mielőtt továbbhaladna.  Ha regisztrálnia kell erre a szolgáltatásra, forduljon a Microsoft-fiók képviselőjéhez.

![előfizetés állapota](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

A HSM-eszköz ki van építve az ügyfelek virtuális hálózatába. Ez magában foglalja az alhálózat követelményét. A virtuális hálózat és a fizikai eszköz közötti kommunikációt lehetővé tevő HSM-függőség egy ExpressRoute-átjáró, és végül egy virtuális gépre van szükség a HSM-eszköz Gemalto ügyfélszoftver használatával történő eléréséhez. Ezeket az erőforrásokat egy sablonfájlba gyűjtöttük, a megfelelő paraméterfájllal együtt, a könnyű használat érdekében. A fájlok a Microsoft közvetlen HSMrequest@Microsoft.commegkeresésére érhetők el a kapcsolaton: .

Miután rendelkezik a fájlokkal, a paraméterfájlt kell szerkesztenie az erőforrások előnyben részesített nevének beszúrásához. Ez azt jelenti, hogy "érték": "".

- `namingInfix`A HSM-erőforrások nevének előtagja
- `ExistingVirtualNetworkName`A HSM-ekhez használt virtuális hálózat neve
- `DedicatedHsmResourceName1`HSM-erőforrás neve az 1.
- `DedicatedHsmResourceName2`HSM-erőforrás neve az adatközpont 2-es bélyegzőjében
- `hsmSubnetRange`A HSM-ek alhálózati IP-címtartománya
- `ERSubnetRange`A virtuális hálózat átjárójának alhálózati IP-címtartománya

Ezekre a változásokra példa a következő:

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

A társított Erőforrás-kezelő sablonfájl 6 erőforrást hoz létre a következő információkkal:

- A megadott virtuális hálózat hsm-jeinek alhálózata
- A virtuális hálózati átjáró alhálózata 
- Virtuális hálózati átjáró, amely a virtuális hálózatot a HSM-eszközökhöz csatlakoztatja
- Az átjáró nyilvános IP-címe
- HSM az 1-es bélyegzőben
- HSM a 2-es bélyegzőben

A paraméterértékek beállítása után a fájlokat fel kell tölteni az Azure Portal felhőbeli rendszerhéj-fájlmegosztásra a használatra. Az Azure Portalon kattintson a "\>\_" felhőhéj szimbólum jobb felső sarkában, és ez teszi a képernyő alsó részét parancskörnyezetté. A lehetőségek ehhez a BASH és a PowerShell, és válassza a BASH, ha még nem állította be.

A parancshéj nak van egy feltöltési/letöltési lehetősége az eszköztáron, és ezt kell választania, hogy feltölthesse a sablont és a paraméterfájlokat a fájlmegosztásba:

![fájlmegosztás](media/tutorial-deploy-hsm-powershell/file-share.png)

A fájlok feltöltése után készen áll az erőforrások létrehozására.
Új HSM-erőforrások létrehozása előtt van néhány szükséges erőforrás, amelyet biztosítania kell. A számítási, HSM-ek és átjáróalhálózati tartományokkal rendelkező virtuális hálózatnak kell rendelkeznie. A következő parancsok példaként szolgálnak arra, hogy mi hozna létre egy ilyen virtuális hálózatot.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>A virtuális hálózat legfontosabb konfigurációja az, hogy a HSM-eszköz alhálózatának "Microsoft.HardwareSecurityModules/dedicatedHSMs" delegálásokkal kell rendelkeznie.  A HSM-kiépítés enélkül nem fog működni.

Miután az összes előfeltétel a helyén van, futtassa a következő parancsot az Erőforrás-kezelő sablon használatához, amely biztosítja, hogy az egyedi nevekkel frissítettértékeket (legalább az erőforráscsoport nevét):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

A parancs körülbelül 20 percet vesz igénybe. A használt "-verbose" beállítás biztosítja, hogy az állapot folyamatosan megjelenjen.

![kiépítés állapota](media/tutorial-deploy-hsm-powershell/progress-status.png)

Ha sikeresen befejeződött, a "provisioningState": "Succeeded" felirat tal együtt, bejelentkezhet a meglévő virtuális gépre, és ssh használatával biztosíthatja a HSM-eszköz rendelkezésre állását.

## <a name="verifying-the-deployment"></a>A telepítés ellenőrzése

Az eszközök kiépítésének ellenőrzéséhez és az eszközattribútumok megtekintéséhez futtassa a következő parancskészletet. Győződjön meg arról, hogy az erőforráscsoport megfelelően van beállítva, és az erőforrás neve pontosan megegyezik a paraméterfájlban.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![tartalék állapota](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Most antól az [Azure erőforrás-kezelővel](https://resources.azure.com/)is láthatja az erőforrásokat.   Miután a felfedező, bontsa ki az "előfizetések" a bal oldalon, bontsa ki az adott előfizetés dedikált HSM, bontsa ki az "erőforráscsoportok", bontsa ki a használt erőforráscsoportot, és végül válassza ki az "erőforrások" elemet.

## <a name="testing-the-deployment"></a>A központi telepítés tesztelése

A központi telepítés tesztelése esetén olyan virtuális géphez csatlakozik, amely hozzáfér a HSM(ek)hez, majd közvetlenül csatlakozik a HSM-eszközhöz. Ezek az intézkedések megerősítik, hogy a HSM elérhető.
Az ssh eszköz a virtuális géphez való csatlakozásra szolgál. A parancs hasonló lesz a következőhöz, de a paraméterben megadott rendszergazdai névvel és DNS-névvel.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A használandó jelszó a paraméterfájlból származik.
Miután bejelentkezett a Linux virtuális gépbe, bejelentkezhet a HSM-be az \<erőforrás-előtag portálján található privát IP-cím használatával,>hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Ha rendelkezik az IP-címmel, futtassa a következő parancsot:

`ssh tenantadmin@<ip address of HSM>`

Ha sikeres, a rendszer jelszót kér. Az alapértelmezett jelszó a PASSWORD. A HSM kérni fogja, hogy változtassa meg a jelszavát, így állítson be egy erős jelszót, és használja a szervezet által a jelszót tároló mechanizmust, és megakadályozza a veszteséget.  

>[!IMPORTANT]
>ha elveszíti ezt a jelszót, a HSM-et alaphelyzetbe kell állítani, és ez azt jelenti, hogy elveszíti a kulcsokat.

Ha ssh használatával csatlakozik a HSM-eszközhöz, futtassa a következő parancsot, hogy megbizonyosodjon arról, hogy a HSM működőképes.

`hsm show`

A kimenetnek úgy kell kinéznie, mint az alábbi képen:

![tartalék állapota](media/tutorial-deploy-hsm-powershell/output.png)

Ezen a ponton egy magas rendelkezésre állású, két HSM-telepítéshez és ellenőrzött hozzáférési és működési állapothoz lefoglalt a forrásokat. Minden további konfiguráció vagy tesztelés magában foglalja a hsm-eszközzel végzett további munkát. Ehhez kövesse a Gemalto Luna Network HSM 7 felügyeleti útmutató 7. Minden dokumentáció és szoftver közvetlenül a Gemalto-tól tölthető le, miután regisztrált a Gemalto ügyfélszolgálati portáljára, és rendelkezik ügyfélazonosítóval. Töltse le az Ügyfélszoftver 7.2-es verzióját az összes szükséges összetevő beszerezéséhez.

## <a name="delete-or-clean-up-resources"></a>Erőforrások törlése vagy törlése

Ha befejezte csak a HSM-eszköz, majd el lehet hagyni erőforrásként, és vissza a szabad készletbe. A nyilvánvaló aggodalomra ad okot, ha ezt teszi, az eszközön lévő bizalmas ügyféladatok. A legjobb módja annak, hogy "nullázja" a készülék, hogy a HSM admin jelszó rossz 3-szor (megjegyzés: ez nem készülék admin, ez a tényleges HSM admin). A kulcsanyag védelme biztonsági intézkedésként az eszköz nem törölhető Azure-erőforrásként, amíg nullázta állapotban van.

> [!NOTE]
> ha bármilyen Probléma van a Gemalto eszköz konfigurációjával, vegye fel a kapcsolatot a [Gemalto ügyfélszolgálatával.](https://safenet.gemalto.com/technical-support/)

Ha el szeretné távolítani a HSM-erőforrást az Azure-ban, használhatja a következő parancsot, amely lecseréli a "$" változókat az egyedi paraméterekre:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>További lépések

Az oktatóanyag lépései után dedikált HSM-erőforrások vannak kiépítve, és elérhetők a virtuális hálózatban. Most már abban a helyzetben van, hogy a kívánt telepítési architektúra által igényelt több erőforrással egészül ki a központi telepítéshez. A telepítés megtervezéséről a Concepts dokumentumok című témakörben talál további információt. Ajánlott egy olyan kialakítás, amelyben két HSM van egy elsődleges régióban, amelyek az állvány szintű rendelkezésre állást kezelik, és két HSM egy másodlagos régióban, amely a regionális rendelkezésre állást kezeli. Az oktatóanyagban használt sablonfájl könnyen használható két HSM-telepítés alapjául, de módosítani kell a paramétereit, hogy megfeleljen a követelményeknek.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Megfigyelő](monitoring.md)
* [Támogatási lehetőségek](supportability.md)
