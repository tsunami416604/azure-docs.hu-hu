---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419121"
---
A szabványosított virtuálisgép-lemezképek lehetővé teszik a szervezetek számára a felhőbe való Migrálás és az üzemelő példányok egységességének biztosítását. A képek jellemzően előre definiált biztonsági és konfigurációs beállításokat és szükséges szoftvereket tartalmaznak. A saját képfeldolgozási folyamatának beállítása idő, infrastruktúra és beállítás szükséges, de az Azure VM rendszerkép-készítővel egyszerűen egy egyszerű konfigurációt kell megadnia, amely leírja a lemezképet, beküldi a szolgáltatást, és a lemezképet összeállította és terjeszti.
 
Az Azure VM Image Builder (Azure Image Builder) segítségével elkezdheti a Windows vagy Linux-alapú Azure Marketplace-rendszerkép, a meglévő Egyéni rendszerképek vagy a Red Hat Enterprise Linux (RHEL) ISO-fájljának megadását, és megkezdheti saját testreszabásainak hozzáadását. Mivel a rendszerkép-szerkesztő a [HashiCorp Packer](https://packer.io/)-re épül, importálhatja a meglévő csomagoló rendszerhéj-kiépítési parancsfájlokat is. Megadhatja azt is, hogy hol szeretné tárolni a rendszerképeket az [Azure megosztott rendszerkép](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)-katalógusban felügyelt képként vagy virtuális merevlemezként.

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Előzetes verziójú funkciók

Az előzetes verzióban ezek a funkciók támogatottak:

- Az alapszintű rendszerképek létrehozása, amely magában foglalja a minimális biztonsági és vállalati konfigurációkat, és lehetővé teszi a részlegek számára, hogy igényeiknek megfelelően testre szabják azokat.
- A meglévő lemezképek javításához a rendszerkép-készítő lehetővé teszi a meglévő Egyéni rendszerképek folyamatos javítását.
- A rendszerkép-készítő összekapcsolhatók a meglévő virtuális hálózatokkal, így csatlakozhat a meglévő konfigurációs kiszolgálókhoz (DSC, Chef, Puppet stb.), a fájlmegosztás vagy bármely más, az útválasztásra alkalmas kiszolgáló/szolgáltatáshoz.
- Az Azure Shared Image Gallery integrációja lehetővé teszi a lemezképek globális terjesztését, verzióját és méretezését, valamint egy rendszerkép-kezelő rendszer létrehozását.
- Integráció meglévő rendszerkép-létrehozási folyamatokkal, csak a rendszerkép-készítő hívása a folyamatból, vagy az egyszerű előzetes rendszerkép-készítő Azure DevOps feladat használata.
- Meglévő rendszerkép-testreszabási folyamat migrálása az Azure-ba. A lemezképek testreszabásához használja meglévő parancsfájlait, parancsait és folyamatait.
- Lemezképek létrehozása VHD formátumban.
 

## <a name="regions"></a>Régiók
Az Azure rendszerkép-szerkesztő szolgáltatás elérhető lesz az előzetes verzióban ezekben a régiókban. A képeket ezen régiókon kívül is el lehet osztani.
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- Észak-Európa
- Nyugat-Európa

## <a name="os-support"></a>Operációs rendszer támogatása
A AIB az Azure Marketplace alap operációsrendszer-lemezképeit fogja támogatni:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-session/Professional
- Windows 2016
- Windows 2019

A RHEL ISOs-támogatás elavult, további részletekért tekintse át a sablon dokumentációját.

## <a name="how-it-works"></a>Működés


![Az Azure rendszerkép-készítő fogalmi rajza](./media/virtual-machines-image-builder-overview/image-builder.png)

Az Azure rendszerkép-szerkesztő egy teljes körűen felügyelt Azure-szolgáltatás, amelyet egy Azure-erőforrás-szolgáltató is elérhet. Az Azure rendszerkép-készítő folyamat három fő részből áll: forrás, testreszabás és terjesztés, ezek egy sablonban jelennek meg. Az alábbi ábrán az összetevők láthatók a tulajdonságaik némelyikével. 
 


**Rendszerkép-készítő folyamat** 

![Az Azure rendszerkép-készítő folyamatának elméleti rajza](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Hozza létre a rendszerkép sablonját. JSON-fájlként. Ez a. JSON fájl a rendszerkép forrásával, testreszabásával és terjesztésével kapcsolatos információkat tartalmaz. Az [Azure rendszerkép-készítő GitHub-tárházban](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)több példa is van.
1. Küldje el a szolgáltatást, amely létrehoz egy Képsablon-összetevőt az Ön által megadott erőforráscsoporthoz. A háttérben a rendszerkép-szerkesztő szükség szerint letölti a forrás-vagy ISO-és parancsfájl-fájlokat. Ezeket egy külön erőforráscsoport tárolja, amely automatikusan létrejön az előfizetésben, a következő formátumban: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. A rendszerkép-sablon létrehozása után létrehozhatja a rendszerképet. A háttérben futó rendszerkép-készítő a sablon és a forrásfájlok használatával hozza létre a virtuális gépet (alapértelmezett méret: Standard_D1_v2), a hálózat, a nyilvános IP-cím, a\<NSG és\<a tárterületet a IT_ DestinationResourceGroup>_ TemplateName> erőforráscsoporthoz.
1. A rendszerkép létrehozása során a rendszerkép-szerkesztő a sablon alapján osztja el a képet, majd törli a további erőforrásokat a folyamathoz létrehozott IT_\<DestinationResourceGroup>_\<TemplateName> erőforráscsoporthoz.


## <a name="permissions"></a>Engedélyek

Annak engedélyezéséhez, hogy az Azure VM Image Builder lemezképeket terjesszen a felügyelt lemezképekre vagy egy megosztott képkatalógusba, meg kell adnia a "közreműködői" jogosultságokat a (z) "Azure Virtual Machine Image Builder" (App ID: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) szolgáltatáshoz az erőforráscsoportok számára. 

Ha meglévő egyéni felügyelt rendszerképeket vagy rendszerképeket használ, az Azure-rendszerkép-szerkesztőnek legalább "olvasó" hozzáférésre van szüksége az erőforráscsoportok számára.

A hozzáférést az Azure CLI használatával rendelheti hozzá:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

A PowerShell használatával is hozzárendelhet hozzáférést:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Ha a szolgáltatásfiók nem található, az azt jelentheti, hogy az előfizetés, amelyhez hozzáadja a szerepkör-hozzárendelést, még nincs regisztrálva az erőforrás-szolgáltatónál.


## <a name="costs"></a>Költségek
A lemezképek Azure Image Builder szolgáltatással való létrehozásakor, kiépítésekor és tárolásakor a számítási, hálózati és tárolási költségek is felmerülhetnek. Ezek a költségek hasonlóak az egyéni lemezképek manuális létrehozása során felmerülő költségekhez. Az erőforrásokra az Azure díjszabása alapján számítunk fel díjat. 

A rendszerkép-létrehozási folyamat során a `IT_<DestinationResourceGroup>_<TemplateName>` rendszer letölti és tárolja a fájlokat az erőforráscsoporthoz, ami kisebb tárolási költségekkel jár. Ha nem szeretné megtartani ezeket a fájlokat, törölje a **képsablont** a rendszerkép létrehozása után.
 
A rendszerkép-készítő létrehoz egy virtuális gépet egy D1v2 VM-mérettel, valamint a virtuális gép számára szükséges tárterületet és hálózatkezelést. Ezek az erőforrások a létrehozási folyamat időtartama alatt maradnak, és törölve lesznek, ha a rendszerkép-készítő befejezte a rendszerkép létrehozását. 
 
Az Azure-rendszerkép-szerkesztő továbbítja a rendszerképet a kiválasztott régióknak, ami hálózati kimenő forgalomra is felmerülhet.
 
## <a name="next-steps"></a>További lépések 
 
Az Azure Image Builder kipróbálásához tekintse meg a [Linux](../articles/virtual-machines/linux/image-builder.md) -és [Windows](../articles/virtual-machines/windows/image-builder.md) -rendszerképek készítésével foglalkozó cikkeket.
 
 
