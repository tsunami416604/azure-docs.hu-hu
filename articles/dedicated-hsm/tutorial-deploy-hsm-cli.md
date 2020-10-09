---
title: Oktatóanyag üzembe helyezése egy meglévő virtuális hálózatban az Azure CLI használatával – Azure dedikált HSM | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogyan helyezhet üzembe egy dedikált HSM-t a CLI használatával egy meglévő virtuális hálózatban
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 63cdb27663cb1a2d8de1a97a2f352b05ff57a3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89489884"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Oktatóanyag: HSM üzembe helyezése meglévő virtuális hálózaton a parancssori felület használatával

Az Azure dedikált HSM egy fizikai eszközt biztosít az egyéni felhasználók számára, teljes körű felügyeleti felügyelettel és teljes körű felügyelettel. A fizikai eszközök használata azt a követelményt hozza létre a Microsoft számára, hogy felügyelje az eszközök kiosztását, hogy a kapacitás hatékonyan kezelhető legyen. Ennek eredményeképpen az Azure-előfizetésen belül a dedikált HSM szolgáltatás általában nem jelenik meg az erőforrás-kiépítés során. A dedikált HSM szolgáltatáshoz hozzáférést igénylő Azure-ügyfeleknek először kapcsolatba kell lépniük a Microsoft-fiók Executive-vel, hogy a dedikált HSM szolgáltatás regisztrációját kérhetik. Csak a folyamat sikeres befejezését követően lehet kiépíteni. 

Ez az oktatóanyag egy tipikus kiépítési folyamatot mutat be, ahol:

- Az ügyfél már rendelkezik egy virtuális hálózattal
- Virtuális géppel rendelkeznek
- A meglévő környezetbe hozzá kell adni a HSM-erőforrásokat.

Egy tipikus, magas rendelkezésre állású, több régióból álló üzembe helyezési architektúra a következőképpen nézhet ki:

![többrégiós üzembe helyezés](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Ez az oktatóanyag egy HSM és kötelező ExpressRoute-átjáróra koncentrál (lásd a fenti 1. alhálózatot) egy meglévő virtuális hálózatba (lásd a fenti 1. VNET).  Minden más erőforrás szabványos Azure-erőforrások. Ugyanez az integrációs folyamat a 4. alhálózatban található HSM is használható a fenti 3. VNET.

## <a name="prerequisites"></a>Előfeltételek

Az Azure dedikált HSM jelenleg nem érhető el a Azure Portalban. A szolgáltatással való interakció a parancssoron keresztül vagy a PowerShell használatával történik. Ez az oktatóanyag a parancssori (CLI) felületet fogja használni a Azure Cloud Shell. Ha még nem ismeri az Azure CLI-t, kövesse az első lépéseket ismertető útmutatót itt: [Azure cli 2,0 első lépések](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Előfeltételek:

- Elvégezte az Azure dedikált HSM regisztrációs folyamatát
- Ön jóváhagyta a szolgáltatás használatát. Ha nem, a részletekért forduljon a Microsoft-fiók képviselőjéhez.
- Ehhez az erőforráshoz létrehozott egy erőforráscsoportot, és az ebben az oktatóanyagban üzembe helyezett új eszközök csatlakoznak ehhez a csoporthoz.
- Már létrehozta a szükséges virtuális hálózatot, alhálózatot és virtuális gépeket a fenti ábrán látható módon, és most integrálni szeretné a 2 HSM az adott üzembe helyezésbe.

Az alábbi utasítások feltételezik, hogy már elindította a Azure Portal, és megnyitotta a Cloud Shell (a portál jobb felső részén válassza a " \> \_ " lehetőséget).

## <a name="provisioning-a-dedicated-hsm"></a>Dedikált HSM kiépítés

A HSM kiépítése és a meglévő virtuális hálózatba való integrálása a ExpressRoute-átjárón keresztül történik az SSH-val. Ez az ellenőrzés segít biztosítani a HSM-eszköz elérhetőségét és alapszintű rendelkezésre állását minden további konfigurációs tevékenységhez. A következő parancsok egy Azure Resource Manager sablont használnak a HSM-erőforrások és a társított hálózati erőforrások létrehozásához.

### <a name="validating-feature-registration"></a>Szolgáltatás regisztrációjának ellenőrzése

A fentiekben leírtaknak megfelelően a kiépítési tevékenységekhez a dedikált HSM szolgáltatás regisztrálása szükséges az előfizetéséhez. Ennek ellenőrzéséhez futtassa a következő parancsokat a Azure Portal Cloud shellben.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

A parancsoknak a "regisztrált" állapotot kell visszaadniuk (az alább látható módon). Ha a parancsok nem adják vissza a "regisztrált" értéket, akkor regisztrálnia kell a szolgáltatást, ha kapcsolatba lép a Microsoft-fiók képviselőjével.

![előfizetés állapota](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

A HSM üzembe helyezése egy ügyfél virtuális hálózatában történik, így a virtuális hálózat és az alhálózat szükséges. A HSM-függőség, amely lehetővé teszi a virtuális hálózat és a fizikai eszköz közötti kommunikációt, egy ExpressRoute-átjáró, és végül egy virtuális gépnek kell hozzáférnie a HSM-eszközhöz a Gemalto-ügyfélszoftver használatával. Ezeket az erőforrásokat a rendszer egy, a megfelelő paramétert tartalmazó fájlba gyűjtötte a könnyű használat érdekében. A fájlok közvetlenül a Microsofttal való kapcsolatfelvételsel érhetők el HSMrequest@Microsoft.com .

Miután megtörtént a fájlok használata, szerkesztenie kell a paramétert, hogy beszúrja az erőforrások előnyben részesített nevét. Sorok szerkesztése a "value": "" értékkel.

- `namingInfix` HSM-erőforrások nevének előtagja
- `ExistingVirtualNetworkName` A HSM használt virtuális hálózat neve
- `DedicatedHsmResourceName1` HSM-erőforrás neve az adatközpont Stamp 1
- `DedicatedHsmResourceName2` HSM-erőforrás neve az adatközpont Stamp 2
- `hsmSubnetRange` Alhálózat IP-címtartomány a HSM
- `ERSubnetRange` Alhálózat IP-címtartomány a VNET-átjáróhoz

Ilyen változások például a következők:

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

A társított Azure Resource Manager sablonfájl 6 erőforrást hoz létre a következő információkkal:

- A megadott VNET lévő HSM alhálózata
- A virtuális hálózati átjáró alhálózata
- Egy virtuális hálózati átjáró, amely a VNET csatlakoztatja a HSM-eszközökhöz
- Az átjáróhoz tartozó nyilvános IP-cím
- Egy HSM az 1. stampben
- Egy HSM a 2. stampben

A paraméterek beállítása után a fájlokat fel kell tölteni Azure Portal Cloud Shell-fájlmegosztás használatára. A Azure Portal kattintson a " \> \_ " Cloud Shell Symbol jobb felső sarokban, így a képernyő alsó részén egy parancssori környezet jelenik meg. Az ehhez tartozó beállítások a BASH és a PowerShell, és ha még nincs beállítva, válassza a BASH lehetőséget.

A parancs rendszerhéjának feltöltési/letöltési lehetősége van az eszköztáron, és ezt a beállítást kell választania a sablon és a paraméter fájljainak a fájlmegosztás számára való feltöltéséhez:

![fájlmegosztás](media/tutorial-deploy-hsm-cli/file-share.png)

A fájlok feltöltése után készen áll arra, hogy erőforrásokat hozzon létre. Az új HSM-erőforrások létrehozása előtt meg kell győződnie néhány előfeltételt jelentő erőforrásról. A számítási, a HSM és az átjáróhoz tartozó alhálózati tartományokkal rendelkező virtuális hálózatnak kell lennie. Az alábbi parancsok példaként szolgálnak a virtuális hálózat létrehozásához.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
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
>A virtuális hálózatra vonatkozó legfontosabb konfiguráció, hogy a HSM-eszköz alhálózatának delegálással kell rendelkeznie a "Microsoft. HardwareSecurityModules/dedicatedHSMs" értékre.  A HSM-kiépítés nem fog működni anélkül, hogy ez a beállítás be legyen állítva.

Ha az összes előfeltétel teljesült, futtassa a következő parancsot a Azure Resource Manager sablon használatához, hogy az egyedi nevekkel (legalább az erőforráscsoport nevével) frissítette az értékeket:

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Az üzemelő példány körülbelül 25 – 30 percet vesz igénybe, hogy a HSM-eszközök az adott idő nagy részével befejeződjön

![kiépítési állapot](media/tutorial-deploy-hsm-cli/progress-status.png)

Ha az üzembe helyezés sikeresen befejeződött, a "provisioningState": "sikeres" üzenet jelenik meg. Csatlakozhat a meglévő virtuális géphez, és az SSH használatával biztosíthatja a HSM-eszköz rendelkezésre állását.

## <a name="verifying-the-deployment"></a>A központi telepítés ellenőrzése

Az eszközök kiépített állapotának ellenőrzéséhez és az eszköz attribútumainak megtekintéséhez futtassa a következő parancsot. Győződjön meg arról, hogy az erőforráscsoport megfelelően van beállítva, és az erőforrás neve pontosan megegyezik a paraméter fájljával.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![kiépítés kimenete](media/tutorial-deploy-hsm-cli/progress-status2.png)

Ekkor az [Azure Resource Explorer](https://resources.azure.com/)használatával is megtekintheti az erőforrásokat.   Egyszer a Explorerben bontsa ki az "előfizetések" elemet a bal oldalon, bontsa ki a dedikált HSM-hez tartozó előfizetést, bontsa ki az "erőforráscsoportok" csomópontot, bontsa ki a használt erőforráscsoportot, és végül válassza ki az "erőforrások" elemet.

## <a name="testing-the-deployment"></a>A központi telepítés tesztelése

Az üzembe helyezés tesztelése olyan virtuális géphez való csatlakozás, amely hozzáférhet a HSM-hez, majd közvetlenül a HSM-eszközhöz csatlakozik. Ezek a műveletek megerősítik, hogy a HSM elérhető.
Az SSH-eszköz a virtuális géphez való kapcsolódásra szolgál. A parancs a következőhöz hasonló lesz, de a paraméterben megadott rendszergazdai névvel és DNS-névvel.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A virtuális gép IP-címe a DNS-név helyett is használható a fenti parancsban. Ha a parancs sikeres, a rendszer kérni fogja a jelszót, és adja meg a következőt:. Miután bejelentkezett a virtuális gépre, bejelentkezhet a HSM-be a HSM-hez társított hálózati adapter-erőforráshoz tartozó privát IP-cím használatával.

![összetevők listája](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Figyelje meg a "rejtett típusok megjelenítése" jelölőnégyzetet, amely akkor jelenik meg, ha a kijelöléskor a HSM-erőforrások jelennek meg.

A fenti képernyőképen a "HSM1_HSMnic" vagy a "HSM2_HSMnic" elemre kattintva megjelenik a megfelelő magánhálózati IP-cím. Ellenkező esetben a `az resource show` fenti parancs a megfelelő IP-cím azonosítására szolgál. 

Ha a megfelelő IP-címmel rendelkezik, futtassa a következő parancsot a cím behelyettesítésével:

`ssh tenantadmin@10.0.2.4`

Ha a művelet sikeres, a rendszer jelszót kér. Az alapértelmezett jelszó jelszó, és a HSM először megkéri, hogy változtassa meg a jelszavát, hogy erős jelszót állítson be, és használja a szervezete által előnyben részesített bármely mechanizmust a jelszó tárolásához és a veszteség elkerüléséhez.

>[!IMPORTANT]
>ha elveszíti ezt a jelszót, a HSM alaphelyzetbe kell állítani, és ez a kulcs elvesztését jelenti.

Ha SSH-val csatlakozik a HSM-hez, futtassa a következő parancsot a HSM működésének biztosításához.

`hsm show`

A kimenetnek az alábbi képen látható módon kell kinéznie:

![összetevők listája](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Ezen a ponton az összes erőforrást lefoglalta egy nagy rendelkezésre állású, két HSM üzembe helyezési és érvényesített hozzáférés és működési állapot számára. Minden további konfiguráció vagy tesztelés több munkát is magában foglal a HSM-eszközzel. Ehhez kövesse a Gemalto Luna hálózati HSM 7 felügyeleti útmutató 7. fejezetének utasításait a HSM inicializálásához és a partíciók létrehozásához. Az összes dokumentáció és szoftver közvetlenül a Gemalto érhető el, ha a Gemalto ügyfélszolgálati portálon regisztrálva van, és rendelkezik ügyfél-AZONOSÍTÓval. Töltse le az ügyfélszoftver 7,2-es verzióját az összes szükséges összetevő lekéréséhez.

## <a name="delete-or-clean-up-resources"></a>Erőforrások törlése vagy eltávolítása

Ha csak a HSM-eszközzel fejeződött be, akkor azt erőforrásként is törölheti, és visszaküldheti az ingyenes készletbe. Ha ezt a problémát tapasztalja, az eszközön található bizalmas vásárlói adatok is megtalálhatók. A legjobb módszer a "zeroize", hogy az eszköz a HSM-rendszergazda jelszavának rossz 3 alkalommal való beszerzését kéri (Megjegyzés: ez nem a berendezés rendszergazdája, hanem a tényleges HSM-rendszergazda). A kulcsfontosságú anyagok védelme érdekében az eszköz nem törölhető Azure-erőforrásként, amíg a nulla állapotba nem kerül.

> [!NOTE]
> Ha bármilyen Gemalto-eszköz konfigurációval kapcsolatos problémát tapasztal, vegye fel a kapcsolatot a [Gemalto ügyfélszolgálatával](https://safenet.gemalto.com/technical-support/).


Ha befejezte az erőforráscsoport összes erőforrását, akkor a következő paranccsal távolíthatja el őket:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>További lépések

Az oktatóanyag lépéseinek elvégzése után a dedikált HSM-erőforrások kiépítve vannak, és rendelkezik egy olyan virtuális hálózattal, amely a szükséges HSM és további hálózati összetevőkkel teszi lehetővé a kommunikációt a HSM használatával.  Most már olyan helyzetben van, hogy ezt az üzembe helyezést az előnyben részesített üzembe helyezési architektúrához szükséges további erőforrásokkal kiegészítse. Az üzembe helyezés megtervezésével kapcsolatos további információkért tekintse meg a fogalmakat ismertető dokumentumokat.
Az elsődleges régió két HSM rendelkező kialakítás az állvány szintjén kezeli a rendelkezésre állást, a másodlagos régióban pedig két HSM ajánlott. Az oktatóanyagban használt sablonfájl egyszerűen használható két HSM-telepítés alapjaként, de a hozzájuk tartozó paramétereket módosítani kell, hogy megfeleljenek a követelményeinek.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)
