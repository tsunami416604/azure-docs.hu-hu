---
title: Frissítés egyszerű nyilvánosról standard nyilvánosra – Azure load balancer
description: Ez a cikk bemutatja, hogyan frissítheti az Azure Basic belső terheléselosztót standard nyilvános terheléselosztóra
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 346fc3d5a4e7b165caafd9847b9797abae0c9113
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659985"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Az Azure belső terheléselosztójának frissítése – kimenő kapcsolat szükséges
[Az Azure Standard Load Balancer](load-balancer-overview.md) a funkciók és a magas rendelkezésre állás széles készletét kínálja a zónaredundancia révén. Ha többet szeretne megtudni a terheléselosztó termékváltozatáról, tekintse meg az [összehasonlító táblázatot.](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) Mivel a standard belső terheléselosztó nem biztosít kimenő kapcsolatot, megoldást kínálunk egy szabványos nyilvános terheléselosztó létrehozására.

A frissítésnek négy szakasza van:

1. A konfiguráció áttelepítése standard nyilvános terheléselosztóra
2. Virtuális gépek hozzáadása a szabványos nyilvános terheléselosztó háttérkészleteihez
3. Kimenő szabály létrehozása a terheléselosztón kimenő kapcsolathoz
4. NSG-szabályok beállítása olyan alhálózatokhoz/virtuális gépekhez, amelyektől tartózkodni kell az internettől

Ez a cikk a konfiguráció áttelepítésével foglalkozik. Virtuális gépek hozzáadása háttérkészletek hez az adott környezettől függően változhat. Ugyanakkor néhány magas szintű, általános ajánlások [is rendelkezésre állnak.](#add-vms-to-backend-pools-of-standard-load-balancer)

## <a name="upgrade-overview"></a>Frissítés áttekintése

Egy Azure PowerShell-parancsfájl érhető el, amely a következőket teszi:

* Létrehoz egy szabványos termékváltozat nyilvános terheléselosztót a megadott erőforráscsoportban és helyen.
* Zökkenőmentesen másolja az alapszintű termékváltozat belső terheléselosztó konfigurációit az újonnan létrehozott szabványos nyilvános terheléselosztóba.

### <a name="caveatslimitations"></a>Kikötések\Korlátozások

* A parancsfájl támogatja a belső terheléselosztó frissítését, ahol kimenő kapcsolatra van szükség. Ha a kimenő kapcsolat nem szükséges a virtuális gépek bármelyike, olvassa el ezt az [oldalt](upgrade-basicInternal-standard.md) az ajánlott eljárás.
* A standard terheléselosztó új nyilvános címmel rendelkezik. A meglévő belső belső terheléselosztóhoz társított IP-címeket nem lehet zökkenőmentesen áthelyezni a standard nyilvános terheléselosztóba, mivel különböző termékkódokkal rendelkeznek.
* Ha a standard terheléselosztó egy másik régióban jön létre, nem fogja tudni társítani a régi régióban meglévő virtuális gépeket az újonnan létrehozott standard terheléselosztóhoz. A korlátozás megkerülése, győződjön meg róla, hogy hozzon létre egy új virtuális gép az új régióban.
* Ha a terheléselosztó nem rendelkezik előtér-IP-konfigurációval vagy háttérkészlettel, valószínűleg hibát talál el a parancsfájl futtatásakor.  Győződjön meg róla, hogy nem üres.

## <a name="download-the-script"></a>A szkript letöltése

Töltse le az áttelepítési parancsfájlt a [PowerShell-galériából.](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0)
## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításától és beállításaitól függően két lehetőség közül választhat:

* Ha nincs telepítve az Azure Az-modulok, vagy nem bánja az Azure Az-modulok eltávolítását, a `Install-Script` legjobb megoldás a parancsfájl futtatásához.
* Ha meg kell tartania az Azure Az modulokat, a legjobb, ha letölti a parancsfájlt, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve vannak-e `Get-InstalledModule -Name az`az Azure Az modulok, futtassa a futtassa a futtassa a futtassa a programot. Ha nem látja a telepített Az modulokat, akkor `Install-Script` használhatja a módszert.

### <a name="install-using-the-install-script-method"></a>Telepítés a Install-Script metódussal

A beállítás használatához nem kell az Azure Az modulok telepítve a számítógépre. Ha telepítve vannak, a következő parancs hibát jelenít meg. Eltávolíthatja az Azure Az modulokat, vagy a másik lehetőséggel manuálisan letöltheti a parancsfájlt, és futtathatja azt.
  
Futtassa a parancsfájlt a következő paranccsal:

`Install-Script -Name AzurePublicLBUpgrade`

Ez a parancs a szükséges Az modulokat is telepíti.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a parancsfájl használatával

Ha néhány Azure Az-modul telepítve van, és nem tudja eltávolítani őket (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a parancsfájlt a parancsfájl **letöltési hivatkozásának Kézi letöltés** elapjával. A szkript letölthető, mint egy nyers nupkg fájlt. Ha erről a nupkg fájlból szeretné telepíteni a parancsfájlt, olvassa el a Kézi csomag letöltése című [témakört.](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)

A szkript futtatása:

1. Az `Connect-AzAccount` Azure-hoz való csatlakozáshoz használható.

1. Az `Import-Module Az` Az modulok importálására használható.

1. Vizsgálja meg a szükséges paramétereket:

   * **oldRgName: [String]: Kötelező** – Ez a frissíteni kívánt alapszintű terheléselosztó erőforráscsoportja. A karakterlánc-érték megkereséséhez keresse meg az Azure Portalon, válassza ki az alapszintű terheléselosztó forrását, és kattintson a terheléselosztó **áttekintése** elemre. Az erőforráscsoport azon a lapon található.
   * **oldLBName: [String]: Kötelező** – Ez a frissíteni kívánt meglévő alapszintű kiegyensúlyozó neve. 
   * **newrgName: [String]: Kötelező** – Ez az az erőforráscsoport, amelyben a standard terheléselosztó létrejön. Ez lehet egy új erőforráscsoport vagy egy meglévő. Ha egy meglévő erőforráscsoportot választ, vegye figyelembe, hogy a terheléselosztó nevének egyedinek kell lennie az erőforráscsoporton belül. 
   * **newlocation: [String]: Kötelező** – Ez az a hely, ahol a standard terheléselosztó létrejön. Azt javasoljuk, hogy a kiválasztott alapszintű terheléselosztó ugyanazon helyét örökölje a standard terheléselosztóhoz, hogy jobban hozzálegyen hozzávetőlegetve más meglévő erőforrásokhoz.
   * **newLBName: [String]: Kötelező** – Ez a létrehozandó standard terheléselosztó neve.
1. Futtassa a parancsfájlt a megfelelő paraméterek kel. 5-7 percig is eltarthat.

    **Példa**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Virtuális gépek hozzáadása a standard terheléselosztó háttérkészleteihez

Először ellenőrizze, hogy a parancsfájl sikeresen létrehozott-e egy új standard nyilvános terheléselosztót, amelynek pontos konfigurációja átlett telepítve az alapszintű nyilvános terheléselosztóról. Ezt az Azure Portalon ellenőrizheti.

Ügyeljen arra, hogy manuális tesztként kis mennyiségű forgalmat küldjön a Standard Load Balancer-en keresztül.
  
Íme néhány forgatókönyv arról, hogyan adhat hozzá virtuális gépeket az újonnan létrehozott szabványos nyilvános terheléselosztó háttérkészleteihez, és mindegyikre vonatkozó javaslataink:

* **A meglévő virtuális gépek áthelyezése a régi alapszintű nyilvános terheléselosztó háttérkészleteiből az újonnan létrehozott szabványos nyilvános terheléselosztó háttérkészleteibe.**
    1. A gyorsútmutatóban szereplő feladatok elvégzéséhez jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
 
    1. Válassza a bal oldali menü **Összes erőforrás elemét,** majd válassza ki az **újonnan létrehozott standard terheléselosztót** az erőforráslistából.
   
    1. A **Beállítások csoportban**válassza **a Háttérkészletek lehetőséget.**
   
    1. Válassza ki azt a háttérkészletet, amely megfelel az alapterhelés-elosztó háttérkészletének, válassza ki a következő értéket: 
      - **Virtuális gép:** Legördülő menüből válassza ki a virtuális gépeket az alapszintű terheléselosztó megfelelő háttérkészletéből.
    1. Kattintson a **Mentés** gombra.
    >[!NOTE]
    >A nyilvános IP-címekkel rendelkező virtuális gépek esetén először szabványos IP-címeket kell létrehoznia, ahol nem garantált ugyanaz az IP-cím. A virtuális gépek leválaszthatják az alapvető IP-címek, és társítja őket az újonnan létrehozott szabványos IP-címek. Ezután képes lesz követni az utasításokat a virtuális gépek hozzáadása a standard terheléselosztó háttérkészletébe. 

* **Új virtuális gépek létrehozása az újonnan létrehozott szabványos nyilvános terheléselosztó háttérkészleteihez való hozzáadása érdekében.**
    * További utasításokat, hogyan kell létrehozni a virtuális gép és társítani a Standard Load Balancer [megtalálható itt](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Kimenő szabály létrehozása kimenő kapcsolathoz

Kövesse az [utasításokat](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) egy kimenő szabály létrehozásához, hogy
* Adja meg a kimenő nat-t a nulláról.
* A meglévő kimenő hálózati tetszési arány méretezése és finomhangolása.

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>NSG-szabályok létrehozása olyan virtuális gépekhez, amelyek tartózkodnak az internetről vagy az internetről való kommunikációtól
Ha azt szeretné, hogy tartózkodjanak az internetes forgalom elérése a virtuális gépek, létrehozhat egy [NSG-szabályt](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) a virtuális gépek hálózati felületén.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak-e korlátozások az Azure PowerShell-parancsfájl áttelepítése a konfiguráció v1 v2?

Igen. Lásd [a kifogásokat/korlátozásokat.](#caveatslimitations)

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Az Azure PowerShell-parancsfájl is átvált az alapszintű terheléselosztóról az újonnan létrehozott standard terheléselosztóra?

Nem. Az Azure PowerShell-parancsfájl csak a konfiguráció áttelepítése. A tényleges forgalom migráció az Ön felelőssége, és az Ön ellenőrzése.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Belefutottam néhány probléma segítségével ezt a forgatókönyvet. Hogyan kaphatok segítséget?
  
E-mailt küldhet slbupgradesupport@microsoft.coma, megnyithat egy támogatási esetet az Azure-támogatással, vagy mindkettőt megteheti.

## <a name="next-steps"></a>További lépések

[További információ a standard terheléselosztóról](load-balancer-overview.md)
