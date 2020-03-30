---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 00fc3a01e6f42c2704af9dbc807dce193ff2971c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116902"
---
A szabványosított virtuálisgép-lemezképek lehetővé teszik a szervezetek számára a felhőbe való áttelepítést, és biztosítják a központi telepítések konzisztenciáját. A képek általában előre meghatározott biztonsági és konfigurációs beállításokat, valamint szükséges szoftvereket tartalmaznak. A saját képkezelő folyamat beállítása időt, infrastruktúrát és telepítést igényel, de az Azure VM Image Builder, csak egy egyszerű konfigurációt a lemezkép, küldje el a szolgáltatásba, és a lemezkép jön létre, és elosztott.
 
Az Azure VM Image Builder (Azure Image Builder) lehetővé teszi, hogy egy Windows vagy Linux-alapú Azure Marketplace-lemezkép, meglévő egyéni lemezképek vagy red hat Enterprise Linux (RHEL) ISO, és elkezdi hozzáadni a saját testreszabások. Mivel az Image Builder a [HashiCorp Packer-re](https://packer.io/)épül, importálhatja a meglévő Packer-rendszerhéj-kiépítési parancsfájlokat is. Azt is megadhatja, hogy a képek házigazdája, az [Azure megosztott képtár,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)felügyelt lemezképként vagy virtuális merevlemezként.

> [!IMPORTANT]
> Az Azure Image Builder jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="preview-features"></a>Előzetes verziójú funkciók

Az előzetes verzióban ezek a funkciók támogatottak:

- Arany alapképek létrehozása, amely magában foglalja a minimális biztonságot és a vállalati konfigurációkat, és lehetővé teszi a részlegek számára, hogy tovább szabják az igényeiknek megfelelően.
- A meglévő képek javítása, az Image Builder lehetővé teszi a meglévő egyéni képek folyamatos javítását.
- Csatlakoztassa a képszerkesztőt a meglévő virtuális hálózatokhoz, így csatlakozhat a meglévő konfigurációs kiszolgálókhoz (DSC, Chef, Puppet stb.), fájlmegosztásokhoz vagy bármely más irányítható kiszolgálóhoz/szolgáltatáshoz.
- Integráció az Azure megosztott képtár, lehetővé teszi a képek globális terjesztését, verzióját és méretezését, és egy lemezképkezelő rendszert biztosít.
- Integráció a meglévő lemezkép-build folyamatok, csak hívja Image Builder a folyamatból, vagy használja az egyszerű Preview Image Builder Azure DevOps feladat.
- Meglévő lemezkép-testreszabási folyamat áttelepítése az Azure-ba. A meglévő parancsfájlok, parancsok és folyamatok segítségével testreszabhatja a képeket.
- Képek létrehozása VHD formátumban.
 

## <a name="regions"></a>Régiók
Az Azure Image Builder szolgáltatás lesz elérhető előzetes verzió ezekben a régiókban. A képek ezeken a régiókon kívül is terjeszthetők.
- USA keleti régiója
- USA 2. keleti régiója
- USA nyugati középső régiója
- USA nyugati régiója
- USA nyugati régiója, 2.
- Észak-Európa
- Nyugat-Európa

## <a name="os-support"></a>Operációs rendszer támogatása
Az AIB támogatja az Azure Marketplace alap operációsrendszer-lemezképeit:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CENTOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Professional/Enterprise virtuális asztali (EVD) 
- Windows 2016
- Windows 2019

Az RHEL ICO-k támogatása elavult, kérjük, olvassa el a sablon dokumentációját a további részletekért.

## <a name="how-it-works"></a>Működés


![Az Azure Image Builder fogalmi rajza](./media/virtual-machines-image-builder-overview/image-builder.png)

Az Azure Image Builder egy teljes körűen felügyelt Azure-szolgáltatás, amely elérhető egy Azure-erőforrás-szolgáltató által elérhető. Az Azure Image Builder folyamat három fő részből áll: forrás, testreszabása és terjesztése, ezek egy sablonban jelennek meg. Az alábbi ábra az összetevőket mutatja, néhány tulajdonságukkal együtt. 
 


**Képszerkesztő folyamat** 

![Az Azure Image Builder folyamat fogalmi rajza](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. A képsablon létrehozása .json fájlként. Ez a .json fájl a lemezkép forrására, testreszabására és terjesztésére vonatkozó információkat tartalmaz. Több példa is van az [Azure Image Builder GitHub-tárházban.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)
1. Küldje el a szolgáltatásnak, ez létrehoz egy képsablon-összetevőt a megadott erőforráscsoportban. A háttérben az Image Builder letölti a forrásképet vagy az ISO-t, és szükség szerint parancsfájlokat. Ezek egy külön erőforráscsoportban tárolódnak, amely automatikusan létrejön\<az előfizetésben, a következő formátumban: IT_ DestinationResourceGroup>_\<TemplateName>. 
1. A képsablon létrehozása után létrehozhatja a lemezképet. A háttérben a Képszerkesztő a sablon és a forrásfájlok segítségével hoz létre virtuális gép (alapértelmezett méret:\<Standard_D1_v2),\<hálózat, nyilvános IP, NSG és tároló a IT_ DestinationResourceGroup>_ TemplateName> erőforráscsoport.
1. A lemezkép létrehozásának részeként a Képszerkesztő a sablon nak megfelelően osztja el\<a lemezképet, majd törli a további erőforrásokat a IT_ DestinationResourceGroup>_\<TemplateName> a folyamathoz létrehozott erőforráscsoportban.


## <a name="permissions"></a>Engedélyek

Ahhoz, hogy az Azure VM Image Builder képeket terjeszthet a felügyelt lemezképekre vagy egy megosztott képtárba, meg kell adnia "Közreműködői" engedélyeket az "Azure Virtual Machine Image Builder" szolgáltatáshoz (alkalmazásazonosító: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) az erőforráscsoportokon. 

Ha egy meglévő egyéni felügyelt lemezképet vagy lemezképet használ, majd az Azure Image Builder szüksége lesz egy minimális "Olvasó" hozzáférést ezekhez az erőforráscsoportokhoz.

Az Azure CLI használatával rendelhet hozzáférést:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

A PowerShell használatával hozzárendelheti a hozzáférést:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Ha a szolgáltatásfiók nem található, az azt jelentheti, hogy az az előfizetés, amelyhez hozzáadja a szerepkör-hozzárendelést, még nincs regisztrálva az erőforrás-szolgáltatóhoz.


## <a name="costs"></a>Költségek
Az Azure Image Builder segítségével a lemezképek létrehozásakor, létrehozásakor és tárolásakor számítási, hálózati és tárolási költségek merülnek fel. Ezek a költségek hasonlóak az egyéni lemezképek manuális létrehozása során felmerülő költségekhez. Az erőforrásokért az Azure-díjakat számítjuk fel. 

A lemezkép-létrehozási folyamat során a `IT_<DestinationResourceGroup>_<TemplateName>` fájlok letöltése és tárolása az erőforráscsoportban lesz, ami kis tárolási költségekkel jár. Ha ezeket nem szeretné megtartani, törölje a **képsablont** a lemezkép létrehozása után.
 
A Képszerkesztő egy Virtuális gép egy D1v2 virtuális gép mérete, valamint a virtuális gép hez szükséges tároló és hálózat használatával hoz létre. Ezek az erőforrások a létrehozási folyamat időtartama alatt maradnak, és törlődnek, amint a Képszerkesztő befejezte a lemezkép létrehozását. 
 
Az Azure Image Builder a rendszerképet a kiválasztott régiók, amelyek hálózati kimenő díjakat.
 
## <a name="next-steps"></a>További lépések 
 
Az Azure Image Builder kipróbálásához tekintse meg a [Linux](../articles/virtual-machines/linux/image-builder.md) vagy [Windows-lemezképek](../articles/virtual-machines/windows/image-builder.md) létrehozásáról szóló cikkeket.
 
 
