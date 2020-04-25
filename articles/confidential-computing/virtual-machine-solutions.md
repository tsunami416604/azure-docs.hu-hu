---
title: Azure bizalmas számítástechnikai megoldások virtuális gépeken
description: Ismerje meg az Azure bizalmas számítástechnikai megoldásait a Virtual Machines szolgáltatásban.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8db477f87f344b28e8941e185c70c81d9b860a72
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149475"
---
# <a name="solutions-on-azure-virtual-machines"></a>Megoldások az Azure Virtual Machines szolgáltatásban

Ez a cikk [az Intel SGX enklávéhoz által támogatott](https://software.intel.com/sgx) Intel-processzorokat futtató Azure bizalmas számítástechnikai virtuális gépek (VM-EK) üzembe helyezésével kapcsolatos információkat ismerteti. 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure bizalmas számítástechnikai VM-méretek

Az Azure bizalmas számítástechnikai virtuális gépek úgy vannak kialakítva, hogy az adatok és a kódok bizalmas kezelését és integritását a felhőben feldolgozzák 

[DCsv2 sorozat](../virtual-machines/dcv2-series.md) A virtuális gépek a legújabb és legutóbb bizalmas számítástechnikai termékcsaládok. Ezek a virtuális gépek több üzembe helyezési képességet is támogatnak, és a DC sorozatú virtuális gépekhez képest nagyobb méretekben vannak, mint az enklávé-oldal gyorsítótára (EPC) és a méretek szélesebb választéka. A [DC sorozatú](../virtual-machines/sizes-previous-gen.md#preview-dc-series) virtuális gépek jelenleg előzetes verzióban érhetők el, és az általánosan elérhetővé válik, és nem tartalmazzák az általános elérhetőséget.

A gyors üzembe helyezési [útmutatót](quick-create-marketplace.md)követve megkezdheti a DCsv2 sorozatú virtuális gép telepítését a Microsoft kereskedelmi Piactérről.

### <a name="current-available-sizes-and-regions"></a>Aktuálisan elérhető méretek és régiók

A rendelkezésre álló régiók és rendelkezésre állási zónák összes általánosan elérhető, bizalmas számítási virtuálisgép-méretéről a következő parancs futtatásával kérheti le az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)-ben:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

Április 2020-ától a következő régiókban és rendelkezésre állási zónákban érhetők el az SKU-ban:

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

A fenti méretek részletesebb megjelenítéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>Telepítési szempontok

Egy rövid útmutató a DCsv2-sorozatú virtuális gépek kevesebb mint 10 perc alatt történő üzembe helyezéséhez. 

- **Azure-előfizetés** – egy bizalmas számítástechnikai virtuálisgép-példány üzembe helyezéséhez vegye fontolóra az utólagos elszámolású előfizetést vagy más vásárlási lehetőséget. Ha [ingyenes Azure-fiókot](https://azure.microsoft.com/free/)használ, a megfelelő mennyiségű Azure-beli számítási magot nem fogja tartalmazni.

- **Díjszabás és regionális elérhetőség** – a DCsv2-sorozatú virtuális gépek díjszabását a [Virtual Machine díjszabási oldalán](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)találja. Tekintse meg az Azure-régiókban rendelkezésre [álló régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) .


- **Magok kvótája** – előfordulhat, hogy a magok kvótáját az alapértelmezett érték alapján kell megnövelni az Azure-előfizetésben. Az előfizetése korlátozhatja az egyes virtuálisgép-méretekben üzembe helyezhető magok számát is, beleértve a DCsv2 sorozatot is. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) díjmentesen. Megjegyzés: az alapértelmezett korlátok az előfizetési kategóriától függően eltérőek lehetnek.

  > [!NOTE]
  > Ha nagy léptékű kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához. Az Azure-kvóták hitelkeretek, nem kapacitási garanciák. A kvótától függetlenül csak a használt magokért kell fizetnie.
  
- **Átméretezés** – a speciális hardverek miatt csak a bizalmas számítástechnikai példányokat lehet átméretezni ugyanazon a méretű családon belül. Például csak egy DCsv2-sorozatú virtuális gépet lehet átméretezni egy DCsv2-sorozatból egy másikra. A nem bizalmas számítástechnikai méretről a bizalmas számítástechnikai méretre való átméretezés nem támogatott.  

- **Lemezkép** – az Intel Software Guard Extension (Intel SGX enklávéhoz) támogatásának biztosítása bizalmas számítási példányok esetén a 2. generációs lemezképeken minden központi telepítést futtatni kell. Az Azure bizalmas számítástechnikai szolgáltatás az Ubuntu 18,04 Gen 2, Ubuntu 16,04 Gen 2 és Windows Server 2016 Gen 2 rendszeren futó munkaterheléseket támogatja. További információ a [2. generációs virtuális gépek támogatásáról az Azure](../virtual-machines/linux/generation-2.md) -ban a támogatott és nem támogatott forgatókönyvek megismeréséhez. 

- **Storage** – az Azure bizalmas számítástechnikai virtuálisgép-adatlemezei és az elmúló operációsrendszer-lemezek a NVMe-lemezeken találhatók. A példányok csak prémium SSD és standard SSD lemezeket támogatják, nem ultra SSD vagy standard HDD. A virtuális gép mérete **DC8_v2** nem támogatja a Premium Storage-t. 

- **Lemez titkosítása** – a bizalmas számítási példányok jelenleg nem támogatják a lemez titkosítását. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állás és vész-helyreállítási megfontolások

Ha virtuális gépeket használ az Azure-ban, a rendszer a magas rendelkezésre állás és a vész-helyreállítási megoldás megvalósítását végzi el a leállás elkerülése érdekében. 

Az Azure bizalmas számítástechnika jelenleg nem támogatja a Zone-redundanciát Availability Zoneson keresztül. A bizalmas számítástechnika legmagasabb rendelkezésre állása és redundancia érdekében használja a [rendelkezésre állási csoportokat](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy). A hardveres korlátozások miatt a bizalmas számítástechnikai példányok rendelkezésre állási csoportjainak száma legfeljebb 10 frissítési tartománnyal rendelkezhet. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Üzembe helyezés Azure Resource Manager sablon használatával 

Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-előfizetésében lévő erőforrások létrehozását, frissítését és törlését. A felügyeleti funkciókat, például a hozzáférés-vezérlést, a zárolásokat és a címkéket használhatja az erőforrások biztonságossá tételéhez és rendszerezéséhez az üzembe helyezés után.

Azure Resource Manager-sablonokkal kapcsolatos további tudnivalókért tekintse meg a [template Deployment áttekintése](../azure-resource-manager/templates/overview.md)című témakört.

Ha egy DCsv2-sorozatú virtuális gépet szeretne üzembe helyezni egy ARM-sablonban, a [virtuális gép erőforrását](../virtual-machines/windows/template-description.md)fogja használni. Győződjön meg arról, hogy a megfelelő tulajdonságokat adja meg a **vmSize** és a **imageReference**.

### <a name="vm-size"></a>Virtuális gép mérete

Adja meg a következő méretek egyikét a ARM-sablonban a virtuális gép erőforrásában. Ez a karakterlánc a tulajdonságok **vmSize** kerül **properties**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 operációsrendszer-rendszerkép

A **Tulajdonságok**területen a **storageProfile**alatt lévő képre is hivatkoznia kell. Csak az alábbi rendszerképek *egyikét* használja a **imageReference**.

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Következő lépések 

Ebben a cikkben a bizalmas számítástechnikai virtuális gépek létrehozásakor szükséges képesítésekről és konfigurációkról olvashat. Most már az Azure Marketplace-en is üzembe helyezhet egy DCsv2-sorozatú virtuális gépet.

[!div class=”nextstepaction”]
[DCsv2-sorozatú virtuális gép üzembe helyezése az Azure Marketplace-en](quick-create-marketplace.md)