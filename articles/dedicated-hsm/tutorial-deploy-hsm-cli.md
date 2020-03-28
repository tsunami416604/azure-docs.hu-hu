---
title: Oktatóanyag üzembe helyezése egy meglévő virtuális hálózatba az Azure CLI - Azure dedikált HSM | Microsoft dokumentumok
description: Oktatóanyag, amely bemutatja, hogyan telepíthet dedikált HSM-et a CLI használatával egy meglévő virtuális hálózatba
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
ms.openlocfilehash: 76b7a97a5be5e7952b0ac11d93bd68656ff8f1ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79454312"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Oktatóanyag: HSM-ek telepítése egy meglévő virtuális hálózatba a CLI használatával

Az Azure dedikált HSM fizikai eszközt biztosít az egyetlen ügyfél számára, teljes körű felügyeleti felügyelettel és teljes körű felügyeleti felelősséggel. A fizikai eszközök használata szükségessé teszi, hogy a Microsoft szabályozza az eszközkiosztást a kapacitás hatékony kezelése érdekében. Ennek eredményeképpen egy Azure-előfizetésen belül a dedikált HSM szolgáltatás általában nem lesz látható az erőforrás-kiépítés. Minden Olyan Azure-ügyfélnek, amelynek hozzáférése van a dedikált HSM-szolgáltatáshoz, először kapcsolatba kell lépnie a Microsoft-fiók végrehajtójával, hogy regisztrációt kérjen a dedikált HSM-szolgáltatáshoz. Csak akkor lehetséges, ha ez a folyamat sikeresen befejeződik. 

Ez az oktatóanyag egy tipikus kiépítési folyamatot mutat be, ahol:

- Az ügyfélnek már van virtuális hálózata
- Van egy virtuális gépük
- HSM-erőforrásokat kell hozzáadniuk a meglévő környezethez.

Egy tipikus, magas rendelkezésre állású, több régióra kiterjedő telepítési architektúra a következőképpen nézhet ki:

![többrégiós telepítés](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Ez az oktatóanyag egy hsm-párra összpontosít, és a szükséges ExpressRoute-átjáróra (lásd a fenti 1. alhálózatot) egy meglévő virtuális hálózatba integrálva (lásd a fenti VNET 1-et).  Minden más erőforrás szabványos Azure-erőforrások. Ugyanaz az integrációs folyamat használható a 4-es alhálózat HSM-jeihez a fenti VNET 3-on.

## <a name="prerequisites"></a>Előfeltételek

Az Azure dedikált HSM jelenleg nem érhető el az Azure Portalon. A szolgáltatással való minden interakció parancssoron keresztül vagy PowerShell használatával lesz. Ez az oktatóanyag az Azure Cloud Shell parancssori (CLI) felületét fogja használni. Ha most ismerkedik az Azure CLI-vel, kövesse az első lépésekre vonatkozó utasításokat: [Azure CLI 2.0 Első lépések.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

Előfeltételek:

- Befejezte az Azure dedikált HSM regisztrációs folyamatát
- Ön a szolgáltatás használatára kapott jóváhagyást. Ha nem, a részletekért forduljon a Microsoft-fiók képviselőjéhez.
- Létrehozott egy erőforráscsoportot ezekhez az erőforrásokhoz, és az oktatóanyagban üzembe helyezett újak is csatlakoznak ehhez a csoporthoz.
- Már létrehozta a szükséges virtuális hálózatot, alhálózatot és virtuális gépeket a fenti ábrán látható módon, és most 2 HSM-et szeretne integrálni az üzembe helyezésbe.

Az alábbi utasítások feltételezik, hogy már navigált az Azure Portalon,\>\_és megnyitotta a Cloud Shellt (válassza a " " lehetőséget a portál jobb felső sarokja felé).

## <a name="provisioning-a-dedicated-hsm"></a>Dedikált HSM kiépítése

A HSM-ek kiépítése és egy meglévő virtuális hálózatba való integrálása az ExpressRoute-átjárón keresztül az ssh használatával lesz érvényesítve. Ez az ellenőrzés segít biztosítani a HSM-eszköz elérhetőségét és alapvető elérhetőségét a további konfigurációs tevékenységekhez. A következő parancsok egy Azure Resource Manager-sablont fognak használni a HSM-erőforrások és a kapcsolódó hálózati erőforrások létrehozásához.

### <a name="validating-feature-registration"></a>Szolgáltatásregisztráció érvényesítése

Mint már említettük, minden kiépítési tevékenység megköveteli, hogy a dedikált HSM szolgáltatás regisztrálva van az előfizetéshez. Ennek ellenőrzéséhez futtassa a következő parancsokat az Azure Portal felhőbeli rendszerhéjában.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

A következő parancs a dedikált HSM-szolgáltatáshoz szükséges hálózati funkciókat ellenőrzi.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Mindkét parancsnak "Regisztrált" állapotot kell visszaadnia (lásd alább). Ha a parancsok nem a "Regisztrált" parancsot adják vissza, regisztrálnia kell erre a szolgáltatásra, forduljon a Microsoft-fiók képviselőjéhez.

![előfizetés állapota](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

A HSM ki van építve az ügyfelek virtuális hálózatába, így virtuális hálózatra és alhálózatra van szükség. A virtuális hálózat és a fizikai eszköz közötti kommunikációt lehetővé tevő HSM-függőség egy ExpressRoute-átjáró, és végül egy virtuális gépre van szükség a HSM-eszköz Gemalto ügyfélszoftver használatával történő eléréséhez. Ezeket az erőforrásokat egy sablonfájlba gyűjtöttük, a megfelelő paraméterfájllal együtt, a könnyű használat érdekében. A fájlok a Microsoft közvetlen HSMrequest@Microsoft.comkapcsolatfelvételével érhetők el.

Miután rendelkezik a fájlokkal, a paraméterfájlt kell szerkesztenie az erőforrások előnyben részesített nevének beszúrásához. "Érték" szöveggel rendelkező sorok szerkesztése: "".

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

A társított Azure Resource Manager-sablonfájl 6 erőforrást hoz létre a következő információkkal:

- A megadott virtuális hálózat hsm-jeinek alhálózata
- A virtuális hálózati átjáró alhálózata
- Virtuális hálózati átjáró, amely a virtuális hálózatot a HSM-eszközökhöz csatlakoztatja
- Az átjáró nyilvános IP-címe
- HSM az 1-es bélyegzőben
- HSM a 2-es bélyegzőben

A paraméterértékek beállítása után a fájlokat fel kell tölteni az Azure Portal felhőbeli rendszerhéj-fájlmegosztásra a használatra. Az Azure Portalon kattintson a "\>\_" felhőhéj szimbólum jobb felső sarkában, és ez teszi a képernyő alsó részét parancskörnyezetté. A lehetőségek ehhez a BASH és a PowerShell, és válassza a BASH, ha még nem állította be.

A parancshéj nak van egy feltöltési/letöltési lehetősége az eszköztáron, és ezt kell választania, hogy feltölthesse a sablont és a paraméterfájlokat a fájlmegosztásba:

![fájlmegosztás](media/tutorial-deploy-hsm-cli/file-share.png)

A fájlok feltöltése után készen áll az erőforrások létrehozására. Új HSM-erőforrások létrehozása előtt van néhány szükséges erőforrás, amelyet biztosítania kell. A számítási, HSM-ek és átjáróalhálózati tartományokkal rendelkező virtuális hálózatnak kell rendelkeznie. A következő parancsok példaként szolgálnak arra, hogy mi hozna létre egy ilyen virtuális hálózatot.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>A virtuális hálózat legfontosabb konfigurációja az, hogy a HSM-eszköz alhálózatának "Microsoft.HardwareSecurityModules/dedicatedHSMs" delegálásokkal kell rendelkeznie.  A HSM-kiépítés nem fog működni anélkül, hogy ez a beállítás be van állítva.

Miután az összes előfeltétel a helyén van, futtassa a következő parancsot az Azure Resource Manager sablon használatához, amely biztosítja, hogy az egyedi nevekkel frissítettértékeket (legalább az erőforráscsoport nevét):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Ez a telepítés körülbelül 25–30 percet vesz igénybe, és az idő nagy része a HSM-eszközök

![kiépítés állapota](media/tutorial-deploy-hsm-cli/progress-status.png)

Amikor a központi telepítés sikeresen befejeződik "provisioningState": "Sikeres" jelenik meg. Csatlakozhat a meglévő virtuális géphez, és ssh használatával biztosíthatja a HSM-eszköz rendelkezésre állását.

## <a name="verifying-the-deployment"></a>A telepítés ellenőrzése

Az eszközök kiépítésének ellenőrzéséhez és az eszközattribútumok megtekintéséhez futtassa a következő parancskészletet. Győződjön meg arról, hogy az erőforráscsoport megfelelően van beállítva, és az erőforrás neve pontosan megegyezik a paraméterfájlban.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![kitermelés](media/tutorial-deploy-hsm-cli/progress-status2.png)

Most antól az [Azure erőforrás-kezelővel](https://resources.azure.com/)is láthatja az erőforrásokat.   Miután a felfedező, bontsa ki az "előfizetések" a bal oldalon, bontsa ki az adott előfizetés dedikált HSM, bontsa ki az "erőforráscsoportok", bontsa ki a használt erőforráscsoportot, és végül válassza ki az "erőforrások" elemet.

## <a name="testing-the-deployment"></a>A központi telepítés tesztelése

A központi telepítés tesztelése esetén olyan virtuális géphez csatlakozik, amely hozzáfér a HSM(ek)hez, majd közvetlenül csatlakozik a HSM-eszközhöz. Ezek az intézkedések megerősítik, hogy a HSM elérhető.
Az ssh eszköz a virtuális géphez való csatlakozásra szolgál. A parancs hasonló lesz a következőhöz, de a paraméterben megadott rendszergazdai névvel és DNS-névvel.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A virtuális gép IP-címe is használható a DNS-név helyett a fenti parancsban. Ha a parancs sikeres, jelszót kér, és meg kell adnia azt. Miután bejelentkezett a virtuális gépre, bejelentkezhet a HSM-be a hsm-hez társított hálózati adapter erőforrás portálján található privát IP-cím használatával.

![összetevők listája](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Figyelje meg a "Rejtett típusok megjelenítése" jelölőnégyzetet, amely a bejelöléskor megjeleníti a HSM-erőforrásokat.

A fenti képernyőképen a "HSM1_HSMnic" vagy a "HSM2_HSMnic" gombra kattintva megjelenik a megfelelő privát IP-cím. Ellenkező esetben `az resource show` a fenti parancs a megfelelő IP-cím azonosításának egyik módja. 

Ha a megfelelő IP-címmel rendelkezik, futtassa a következő parancsot a cím helyettesítésével:

`ssh tenantadmin@10.0.2.4`

Ha sikeres, a rendszer jelszót kér. Az alapértelmezett jelszó a JELSZÓ, és a HSM először kérni fogja, hogy változtassa meg a jelszavát, így állítson be egy erős jelszót, és használja azt a mechanizmust, amelyet a szervezet a jelszó tárolásához és a veszteség megelőzéséhez előnyben részesít.

>[!IMPORTANT]
>ha elveszíti ezt a jelszót, a HSM-et alaphelyzetbe kell állítani, és ez azt jelenti, hogy elveszíti a kulcsokat.

Ha ssh használatával csatlakozik a HSM-hez, futtassa a következő parancsot, hogy megbizonyosodjon arról, hogy a HSM működőképes.

`hsm show`

A kimenetnek az alábbi képen látható módon kell kinéznie:

![összetevők listája](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Ezen a ponton egy magas rendelkezésre állású, két HSM-telepítéshez és ellenőrzött hozzáférési és működési állapothoz lefoglalt a forrásokat. Minden további konfiguráció vagy tesztelés magában foglalja a hsm-eszközzel végzett további munkát. Ehhez kövesse a Gemalto Luna Network HSM 7 felügyeleti útmutató 7. Minden dokumentáció és szoftver közvetlenül a Gemalto-tól tölthető le, miután regisztrált a Gemalto ügyfélszolgálati portáljára, és rendelkezik ügyfélazonosítóval. Töltse le az Ügyfélszoftver 7.2-es verzióját az összes szükséges összetevő beszerezéséhez.

## <a name="delete-or-clean-up-resources"></a>Erőforrások törlése vagy törlése

Ha befejezte csak a HSM-eszköz, majd el lehet hagyni erőforrásként, és vissza a szabad készletbe. A nyilvánvaló aggodalomra ad okot, ha ezt teszi, az eszközön lévő bizalmas ügyféladatok. A legjobb módja annak, hogy "nullázja" a készülék, hogy a HSM admin jelszó rossz 3-szor (megjegyzés: ez nem készülék admin, ez a tényleges HSM admin). A kulcsanyag védelme biztonsági intézkedésként az eszköz nem törölhető Azure-erőforrásként, amíg nullázta állapotban van.

> [!NOTE]
> ha bármilyen Probléma van a Gemalto eszköz konfigurációjával, vegye fel a kapcsolatot a [Gemalto ügyfélszolgálatával.](https://safenet.gemalto.com/technical-support/)


Ha az erőforráscsoport összes erőforrásával végzett, akkor a következő paranccsal távolíthatja el őket:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>További lépések

Az oktatóanyag lépései után dedikált HSM-erőforrások vannak kiépítve, és rendelkezik egy virtuális hálózattal, amely rendelkezik a szükséges HSM-ekkel és további hálózati összetevőkkel a HSM-mel való kommunikáció engedélyezéséhez.  Most már abban a helyzetben van, hogy a kívánt telepítési architektúra által igényelt több erőforrással egészül ki a központi telepítéshez. A telepítés megtervezéséről a Concepts dokumentumok című témakörben talál további információt.
Ajánlott egy olyan kialakítás, amelyben két HSM van egy elsődleges régióban, amelyek az állvány szintű rendelkezésre állást kezelik, és két HSM egy másodlagos régióban, amely a regionális rendelkezésre állást kezeli. Az oktatóanyagban használt sablonfájl könnyen használható két HSM-telepítés alapjául, de módosítani kell a paramétereit, hogy megfeleljen a követelményeknek.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Megfigyelő](monitoring.md)
