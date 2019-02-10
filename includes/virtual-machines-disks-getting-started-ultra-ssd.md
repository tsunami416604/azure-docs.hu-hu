---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e55058d6b1f76b4afcb847b946df85d5ab69971b
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985432"
---
# <a name="enabling-azure-ultra-ssds"></a>Az Azure Ultranagy SSD-k engedélyezése

Azure Ultranagy SSD kínál a nagy teljesítményű, magas iops-érték és egységes közel valós idejű lemezes tárolás az Azure IaaS virtuális gépek. Az új ajánlat felső részén a meglévő lemezek ajánlataink, ugyanazon rendelkezésre állási szinten sor teljesítményt biztosít. Ultranagy SSD további előnyei a következők dinamikusan megváltoztathatja a lemezes célok mellett a virtuális gépek újraindítása nélkül a számítási feladatok teljesítményére. Ultranagy SSD adatok-igényes számítási feladatokhoz, például az SAP HANA, a felső szintű adatbázisok és a tranzakciós adatokat használó munkaterhelések esetén használhatók.

Jelenleg Ultranagy SSD-k előzetes verzióban érhető el, és meg kell [regisztrálása](https://aka.ms/UltraSSDPreviewSignUp) annak érdekében, hogy elérhesse azokat az előzetes verzióban érhető el.

Amint jóváhagyják, határozza meg, melyik zónát, az USA keleti RÉGIÓJA 2, a Ultranagy SSD való üzembe helyezéséhez a következő parancsok egyikét futtatja:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

PARANCSSORI FELÜLET: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

A válasz formáját az alábbi, ahol X a zóna használatára az USA keleti RÉGIÓJA 2 üzembe helyezéséhez hasonló lesz. X 1, 2 vagy 3 lehet.

|ResourceType  |Name (Név)  |Hely  |Zóna  |Korlátozás  |Képesség  |Érték  |
|---------|---------|---------|---------|---------|---------|---------|
|Lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Ha a parancs nem válaszolt, azt jelenti, hogy regisztrációját, hogy a szolgáltatás még mindig vagy folyamatban lévő, vagy nincs még jóváhagyva.

Most, hogy tudja, hogy melyik zónában való üzembe helyezéséhez, kövesse az ebben a cikkben az első Ultranagy SSD-lemezeket a telepített virtuális gépek beolvasni a telepítés lépéseit.

## <a name="deploying-an-ultra-ssd"></a>Az Ultranagy SSD üzembe helyezése

Először határozza meg, a virtuális gép méretét, üzembe helyezéséhez. Ez az előzetes verzió része csak a DsV3 és EsV3 virtuális gép operációsrendszer-család támogatottak. A második táblázat tartalmazza ezen [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) ezen Virtuálisgép-méretekkel kapcsolatos további részletekért.
Emellett tekintse meg a mintát [virtuális gép létrehozása több Ultranagy SSD-lemez](https://aka.ms/UltraSSDTemplate), amely bemutatja, hogyan hozzon létre egy virtuális gép több Ultranagy SSD-lemez.

Az alábbiakban ismertetjük azok a Resource Manager-sablon új vagy módosított módosítások: **API-verzió** a `Microsoft.Compute/virtualMachines` és `Microsoft.Compute/Disks` kell beállítani: `2018-06-01` (vagy újabb).

Adja meg a lemezt az UltraSSD_LRS termékváltozat, lemezkapacitás, IOPS és átviteli sebesség (Mbps), hozzon létre egy Ultra SSD-lemez. Az alábbiakban egy példa, amely egy lemezt hoz létre 1 024 GB a (GiB = 2 ^ 30 bájt), 80 000 IOPS és 1200 MB/s (MBps = 10 ^ 6 bájt / másodperc):

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Adjon hozzá egy további lehetőség jelzi a Ultranagy SSD engedélyezve van a virtuális gép tulajdonságait (lásd a [minta](https://aka.ms/UltraSSDTemplate) a teljes Resource Manager-sablon):

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Után a virtuális gép ki van építve, particionálja és formázza az adatlemezeket, és konfigurálja azokat a számítási feladatokhoz.

## <a name="additional-ultra-ssd-scenarios"></a>További Ultranagy SSD-forgatókönyvek

- Virtuális gép létrehozásakor a Ultranagy SSD-k is implicit módon hozhatók létre is. Ezeket a lemezeket, kap egy alapértelmezett értéket (500) IOPS és átviteli sebesség (8 MiB/s).
- További Ultranagy SSD-k Ultranagy SSD-kompatibilis virtuális gépek csatolhatók.
- Ultranagy SSD támogatja a lemez teljesítménye (IOPS és átviteli sebesség) attribútumok beállítása futási időben anélkül, hogy a lemez leválasztása a virtuális gép. Ha egy lemez teljesítménye átméretezési művelet a lemezen van kiadva, is igénybe vehet a módosítás érvénybe valójában egy órát.
- A lemezkapacitás növekvő, egy virtuális gép felszabadítva nem igényel.

## <a name="next-steps"></a>További lépések

Ha még nem regisztrált az előzetes verzióra, és próbálja ki az új lemez típust szeretné [bejelentheti hozzáférési igényét a kérdőív](https://aka.ms/UltraSSDPreviewSignUp).
