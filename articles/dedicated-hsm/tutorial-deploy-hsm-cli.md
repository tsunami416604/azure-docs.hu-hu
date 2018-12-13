---
title: Az oktatóanyag egy meglévő virtuális hálózatot az Azure CLI - Azure dedikált HSM használatával üzembe helyezése |} A Microsoft Docs
description: 'Oktatóanyag: hogyan kell telepíteni a parancssori felület használatával a meglévő virtuális hálózatban egy dedikált HSM'
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
ms.openlocfilehash: d8171a3c506ed53c986db6cddd959411f0a146aa
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080316"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Oktatóanyag: HSM-EK telepítése egy meglévő virtuális hálózatban, CLI-vel

Az Azure, a dedikált HSM biztosít egy fizikai eszköz az egyetlen ügyfél szolgáltatással, teljes körű rendszergazdai felügyeletet és a teljes felügyeleti feladata. Fizikai eszközök használata szükséges a Microsoft szabályozhatók az eszközök foglalási kapacitás hatékonyan felügyelt hoz létre. Ennek eredményeképpen egy Azure-előfizetésen belül a dedikált HSM-szolgáltatást nem általában lesz látható az erőforrás-kiépítés. A dedikált HSM szolgáltatáshoz való hozzáférést igénylő bármely Azure-ügyfél először kapcsolatba kell lépnie a kérelem regisztráció a Microsoft fiókért a dedikált HSM-szolgáltatás. Csak egyszer, a folyamat sikeresen befejeződött-kiépítés nem lehetséges. 

Ez az oktatóanyag bemutatja egy tipikus kiépítési folyamat ahol:

- Egy ügyfél már rendelkezik egy virtuális hálózatot
- A virtuális gépek rendelkeznek
- HSM-erőforrások hozzáadása a meglévő környezetben van szükségük.

Egy tipikus, magas rendelkezésre állású, több régióban történő üzembe helyezés architektúrája előfordulhat, hogy hasonlítania:

![Több régióban történő üzembe helyezés](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Ebben az oktatóanyagban a HSM-EK párjai összpontosít, és a szükséges ExpressRoute-átjárót (lásd a fenti Subnet-1) történő integrálását egy meglévő virtuális hálózati (lásd a fenti 1 virtuális hálózat).  Minden egyéb erőforrás Azure-erőforrások. Integráció eljárást 4-alhálózatot a virtuális hálózatok közötti 3 fenti HSM-eket is használható.

## <a name="prerequisites"></a>Előfeltételek

Az Azure, a dedikált HSM jelenleg nem áll rendelkezésre az Azure Portalon. A szolgáltatás való minden interakció parancssori vagy a Powershellen keresztül lesz. Ebben az oktatóanyagban a parancssori felület (CLI) az Azure Cloud Shell fogja használni. Ha most ismerkedik az Azure CLI, kövesse bevezetés utasításokat itt: [Azure CLI 2.0-s Ismerkedés](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Előfeltételek:

- Ha befejezte az Azure dedikált HSM-regisztrációs folyamathoz
- A szolgáltatás már engedélyezett. Ha nem, részletekért forduljon a Microsoft-fiók kapcsolattartójához.
- Hozott létre egy erőforráscsoportot, ezekhez az erőforrásokhoz, és ebben az oktatóanyagban üzembe helyezett újakat csatlakozni fog az adott csoporthoz.
- Már létrehozta a szükséges virtuális hálózat, alhálózat és virtuális gépek megfelelően a fenti ábrán, és most alkalmazásaiba kívánják integrálni 2 HSM-EK a telepítés.

Minden, az alábbi utasítások azt feltételezik, hogy az Azure Portalra már megnyit és nyitotta meg a Cloud Shellben (kiválasztása "\>\_" felső részen található, a portál jobb).

## <a name="provisioning-a-dedicated-hsm"></a>A dedikált HSM-kiépítés

HSM-EK kiépítése, és integrálja az ExpressRoute-átjárót egy meglévő virtuális hálózatban érvényesíti az ssh használata. Az érvényesítés biztosítja, hogy a lemezekről és a HSM eszközt, további konfigurációs tevékenységek alapszintű rendelkezésre állását. A következő parancsokat egy Azure Resource Manager-sablon használatával hozza létre a HSM-erőforrások és a kapcsolódó hálózati erőforrások.

### <a name="validating-feature-registration"></a>A funkció regisztrálásának ellenőrzése

Mint már említettük, a kiépítési tevékenység, hogy a dedikált HSM szolgáltatás regisztrálva van-e az előfizetés szükséges. Ellenőrizze, hogy, a portál Azure cloud shellben futtassa a következő parancsokat.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

A következő parancs ellenőrzi a hálózatkezelési szolgáltatásokat, a dedikált HSM-szolgáltatás szükséges.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Mindkét parancsot egy "Regisztrálva" állapotát adja vissza (ahogy az alább látható). Ha a parancsok nem ad vissza "Regisztrálva" szeretne regisztrálni ezt a szolgáltatást, forduljon a Microsoft-ügyfélmenedzserhez.

![előfizetés állapota](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

Egy HSM-be egy ügyfelei virtuális hálózatban van kiépítve, ezért szükség egy virtuális hálózatot és alhálózatot. A HSM-hez a virtuális hálózat és a fizikai eszköz közötti kommunikáció engedélyezéséhez felvettek egy ExpressRoute-átjárót, és végül egy virtuális gépet a HSM eszközt az Gemalto ügyfélszoftverrel eléréséhez szükséges. Ezeket az erőforrásokat egy sablon fájlt, a kapcsolódó alkalmazásparaméter-fájlt, a használat megkönnyítése érdekében történő összegyűjtése. A fájl áll rendelkezésre, kapcsolatba lép a Microsofttal közvetlen HSMrequest@Microsoft.com.

Miután a fájlokat, szerkesztenie kell a alkalmazásparaméter-fájlt az előnyben részesített nevét erőforrásokhoz beszúrásához. A "value" sorok szerkesztése: "".

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

A társított Azure Resource Manager-sablonfájl 6 erőforrásokat hozza létre ezt az információt:

- A HSM-eket a megadott virtuális hálózat alhálózat
- A virtuális hálózati átjáró-alhálózat
- A hardveres biztonsági modulokhoz a virtuális hálózathoz csatlakozó virtuális hálózati átjáró
- Az átjáró nyilvános IP-cím
- A stamp 1 HSM
- A stamp 2 HSM

Miután beállította a paraméterértékeket, a fájlokat fel kell tölteni a portál Azure cloud shell-megosztás használatra kell. Az Azure Portalon kattintson a "\>\_" cloud shell szimbólum felül jobb oldalon, és ez elvégzi a képernyőn egy parancs környezet alsó részén. A beállítások a BASH és a PowerShell, és meg kell BASH válassza, ha nem már be van állítva.

A parancs-rendszerhéj elemre az eszköztárban feltöltési/letöltési lehetősége van, és ezzel a sablonnal és paraméterfájlokkal fájlok feltöltése a fájlmegosztás választhat:

![fájlmegosztás](media/tutorial-deploy-hsm-cli/file-share.png)

A fájlok feltöltése után készen áll az erőforrások létrehozásához. Új HSM létrehozása előtt biztosítania kell néhány előfeltételként erőforrás erőforrások érvényben vannak. Számítási, a HSM-EK és az átjáró-alhálózat címtartományai rendelkező virtuális hálózaton kell rendelkeznie. A következő parancsokat szolgálja ki, mit kell létrehoznia egy virtuális hálózat egy példa.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
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
>Vegye figyelembe a virtuális hálózathoz, a legfontosabb konfigurációs, hogy az IP-alhálózatot a HSM eszközt kell rendelkeznie a delegálásokat "Microsoft.HardwareSecurityModules/dedicatedHSMs" értékre.  A HSM-kiépítés nélkül nem fog működni a beállítás folyamatban van.

Miután teljesüljenek érvényesítve van, futtassa a következő parancsot annak biztosítása, az egyedi névvel ellátott értékek frissítése az Azure Resource Manager-sablon használata (legalább az erőforráscsoport neve):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

A telepítés körülbelül 25 – 30 percet az adott idő alatt a hardveres biztonsági modulokhoz tömeges megtétele

![kiépítési állapot](media/tutorial-deploy-hsm-cli/progress-status.png)

Amikor befejeződik az üzembe helyezés sikerült "provisioningState": "Sikeres" jelennek meg. Csatlakozás a meglévő virtuális géphez, és az SSH használata a HSM eszközt rendelkezésre állásának biztosításához.

## <a name="verifying-the-deployment"></a>A telepítés ellenőrzése

Az eszközök vannak kiépítve, és tekintse meg az eszköz attribútumait ellenőrzéséhez futtassa a következő parancsot. Győződjön meg arról, megfelelően van-e állítva az erőforráscsoportot, és az erőforrás neve pontosan, ahogy a paraméterfájlban rendelkezik.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![regisztrációs kimenet](media/tutorial-deploy-hsm-cli/progress-status2.png)

Most is kell az erőforrások használatával megtekintheti a [Azure erőforrás-kezelő](https://resources.azure.com/).   Egyszer az a Explorerben bontsa ki a bal oldali "előfizetések", bontsa ki az adott dedikált HSM-előfizetést, bontsa ki a "erőforráscsoportok", bontsa ki a használt erőforráscsoportot, és végül válassza ki a "resources" elemet.

## <a name="testing-the-deployment"></a>A központi telepítés tesztelése

A telepítés tesztelésére egy virtuális gépet, amely hozzáférhet a HSM(s) csatlakozik, majd közvetlenül a HSM eszközt csatlakozzon egy esetet. Ezek a műveletek megerősíti a HSM érhető el.
Az ssh eszköz segítségével csatlakozzon a virtuális géphez. A parancs a következőhöz hasonló lesz, de a rendszergazda nevét és a dns-név, paraméterben megadott.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A virtuális gép IP-címe felhasználható helyett a DNS-név, a fenti parancsban is. A parancs végrehajtása sikeres, ha a rendszer jelszót kér, és meg kell adnia, amely. Miután bejelentkezett a virtuális gép, bejelentkezhet a HSM-be a portálon, a HSM társított hálózati adapter erőforrás található privát IP-cím használatával.

![összetevőinek listája](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Figyelje meg, hogy a "Show rejtett típusok" jelölőnégyzetet, amikor lesz kiválasztva HSM erőforrások megjelenítéséhez.

A fenti képernyőképen kattintson a "HSM1_HSMnic" vagy "HSM2_HSMnic" jelenik meg a megfelelő magánhálózati IP-címet. Ellenkező esetben a `az resource show` fent használt parancs a megfelelő IP-cím azonosítani tudja. 

Ha rendelkezik a megfelelő IP-címet, futtassa a következő parancsot, és cserélje le ezt a címet:

`ssh tenantadmin@10.0.2.4`

Ha sikeres kéri a jelszót. Az alapértelmezett jelszót pedig a JELSZÓT, és a hardveres biztonsági MODULT először megkérdezi, hogy a jelszó módosítása tehát egy erős jelszót, ha a szervezet részesíti előnyben a jelszó tárolásához és az adatvesztés elkerülése érdekében mechanizmus.

>[!IMPORTANT]
>Ha elveszíti a ezt a jelszót, a hardveres biztonsági MODULT kell állítani, és azt jelenti, hogy elveszítenék a kulcsokat.

A HSM-be az ssh használata csatlakoztatott, futtassa a következő parancsot annak biztosítására, hogy működik a hardveres biztonsági MODULT.

`hsm show`

A kimenetnek az alábbi képen látható módon kell kinéznie:

![összetevőinek listája](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

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

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>További lépések

Az oktatóanyagban a lépések elvégzése után a dedikált HSM-erőforrások kiépítése, és szükséges HSM-ekről és a további hálózati összetevők a HSM-kommunikáció engedélyezése a virtuális hálózatot.  Most már Ön olyan helyzetben, hogy kiegészítsék a központi telepítés által előnyben részesített üzemi architektúra szükség szerint több erőforrást. Útmutatás nyújtása a központi telepítésének tervezése a további információkért lásd: a fogalmak dokumentumokat.
Egy kialakítás a két HSM-EK egy elsődleges régióban, rendelkezésre állást, az állvány szintjén addressing és a egy másodlagos régióban régiónkénti rendelkezésre állás címzés két HSM-EK használata javasolt. A sablonfájl, a jelen oktatóanyagban használt két HSM központi telepítés alapjaként könnyen használható, de rendelkeznie kell módosítani az igényeinek paramétereket.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Monitorozás](monitoring.md)
