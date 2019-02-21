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
ms.openlocfilehash: 972a538fab8a2aa84f6a12df48422abb40baac82
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443318"
---
# <a name="enabling-azure-ultra-ssd"></a>Az Azure ultranagy SSD engedélyezése

Azure ultranagy SSD biztosításához nagy teljesítményű, magas iops-érték és egységes közel valós idejű lemezes tárolás az Azure IaaS virtuális gépek. Az új ajánlat felső részén a meglévő lemezek ajánlataink, ugyanazon rendelkezésre állási szinten sor teljesítményt biztosít. Ultranagy SSD további előnyei a következők dinamikusan megváltoztathatja a lemezes célok mellett a virtuális gépek újraindítása nélkül a számítási feladatok teljesítményére. Ultranagy SSD adatok-igényes számítási feladatokhoz, például az SAP HANA, a felső szintű adatbázisok és a tranzakciós adatokat használó munkaterhelések vannak kialakítva.

Jelenleg előzetes verzióként találhatók meg ultranagy SSD és kell [regisztrálása](https://aka.ms/UltraSSDPreviewSignUp) annak érdekében, hogy elérhesse azokat az előzetes verzióban érhető el.

Amint jóváhagyják, melyik zónát, az USA keleti RÉGIÓJA 2, üzembe helyezéséhez a ultranagy lemez meghatározásához a következő parancsok egyikét futtatja:

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

PARANCSSORI FELÜLET: `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

A válasz formáját az alábbi, ahol X a zóna használatára az USA keleti RÉGIÓJA 2 üzembe helyezéséhez hasonló lesz. X 1, 2 vagy 3 lehet.

|ResourceType  |Name (Név)  |Hely  |Zóna  |Korlátozás  |Képesség  |Érték  |
|---------|---------|---------|---------|---------|---------|---------|
|lemezek     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Ha a parancs nem válaszolt, azt jelenti, hogy regisztrációját, hogy a szolgáltatás még mindig vagy folyamatban lévő, vagy nincs még jóváhagyva.

Most, hogy tudja, hogy melyik zónában való üzembe helyezéséhez, kövesse az ebben a cikkben az első ultranagy SSD a telepített virtuális gépek beolvasni a telepítés lépéseit.

## <a name="deploying-an-ultra-ssd"></a>Az ultranagy SSD üzembe helyezése

Először határozza meg, a virtuális gép méretét, üzembe helyezéséhez. Ez az előzetes verzió része csak a DsV3 és EsV3 virtuális gép operációsrendszer-család támogatottak. A második táblázat tartalmazza ezen [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) ezen Virtuálisgép-méretekkel kapcsolatos további részletekért.
Emellett tekintse meg a mintát [virtuális gép létrehozása több ultranagy SSD](https://aka.ms/UltraSSDTemplate), amely bemutatja, hogyan hozzon létre egy virtuális gép több ultranagy SSD.

Az alábbiakban ismertetjük azok a Resource Manager-sablon új vagy módosított módosítások: **API-verzió** a `Microsoft.Compute/virtualMachines` és `Microsoft.Compute/Disks` kell beállítani: `2018-06-01` (vagy újabb).

Adja meg a lemezt az UltraSSD_LRS termékváltozat, lemezkapacitás, IOPS és átviteli sebesség (Mbps), hozzon létre egy ultra lemezt. Az alábbiakban egy példa, amely egy lemezt hoz létre 1 024 GB a (GiB = 2 ^ 30 bájt), 80 000 IOPS és 1200 MB/s (MBps = 10 ^ 6 bájt / másodperc):

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

Adjon hozzá egy további lehetőség jelzi a ultra engedélyezve van a virtuális gép tulajdonságait (lásd a [minta](https://aka.ms/UltraSSDTemplate) a teljes Resource Manager-sablon):

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

## <a name="additional-ultra-ssd-scenarios"></a>További ultranagy SSD-forgatókönyvek

- Virtuális gép létrehozásakor a ultranagy SSD implicit módon létrehozhatók is. Ezeket a lemezeket, kap egy alapértelmezett értéket (500) IOPS és átviteli sebesség (8 MiB/s).
- További ultranagy SSD kompatibilis virtuális gépek csatolhatók.
- Ultranagy SSD támogatja a lemez teljesítménye (IOPS és átviteli sebesség) attribútumok beállítása futási időben anélkül, hogy a lemez leválasztása a virtuális gép. Ha egy lemez teljesítménye átméretezési művelet a lemezen van kiadva, is igénybe vehet a módosítás érvénybe valójában egy órát.
- A lemezkapacitás növekvő, egy virtuális gép felszabadítva nem igényel.

## <a name="next-steps"></a>További lépések

Ha még nem regisztrált az előzetes verzióra, és próbálja ki az új lemez típust szeretné [bejelentheti hozzáférési igényét a kérdőív](https://aka.ms/UltraSSDPreviewSignUp).
