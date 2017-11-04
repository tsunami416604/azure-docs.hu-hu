---
title: "SQL Server VMs-tárolási konfigurációját |} Microsoft Docs"
description: "Ez a témakör ismerteti, hogyan Azure storage tartozó konfigurálása SQL Server virtuális gépen (Resource Manager üzembe helyezési modellben) kiépítése során. Azt is bemutatja, hogyan konfigurálhatja a tárolási a meglévő SQL Server virtuális géphez."
services: virtual-machines-windows
documentationcenter: na
author: ninarn
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: ninarn
ms.openlocfilehash: 20fbc21224410456919e82f3a63c506eb6e573e4
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="storage-configuration-for-sql-server-vms"></a>Tárolási konfigurációt az SQL Server virtuális gépen
Amikor konfigurál egy SQL Server virtuálisgép-lemezkép az Azure-ban, a portál segítségével automatizálhatja a tárolási konfigurációt. Ez magában foglalja a tárolási csatolása a virtuális gépek, így tárolási elérhető SQL-kiszolgáló számára, és konfigurálja úgy a optimalizálása ki az adott igények szerint.

Ez a témakör azt ismerteti, hogyan Azure storage tartozó konfigurálása az SQL Server virtuális gépen is kiépítése során, és a meglévő virtuális gépek. Ez a konfiguráció alapján a [teljesítmény gyakorlati tanácsok](virtual-machines-windows-sql-performance.md) SQL Servert futtató Azure virtuális gépekhez.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Az automatizált tárolási konfigurációs beállításokat kívánja használni, a virtuális gép igényel a következő jellemzőkkel:

* A kiépített egy [SQL Server-gyűjtemény rendszerképet](virtual-machines-windows-sql-server-iaas-overview.md#option-1-create-a-sql-vm-with-per-minute-licensing).
* Használja a [Resource Manager üzembe helyezési modellben](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Használja a [prémium szintű Storage](../premium-storage.md).

## <a name="new-vms"></a>Új virtuális gépek
Az alábbi szakaszok azt ismertetik, hogyan konfigurálja a tárhelyet az új SQL Server virtuális gépek számára.

### <a name="azure-portal"></a>Azure Portal
Ha egy Azure virtuális gép az SQL Server-gyűjtemény lemezkép használatával, dönthet úgy, automatikusan konfigurálja a tárhelyet az új virtuális gép. Megadhatja, hogy a tárhely méretét, teljesítménykorlátok és munkaterhelésének típusát. Az alábbi képernyőfelvételen látható kiépítése a tárolási konfiguráció panel során SQL virtuális gép használja.

![SQL Server virtuális gép tárolási konfigurációt kiépítése során](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

A választott alapján, Azure hajtja végre a következő tárolókonfigurálás feladatait a virtuális gép létrehozása után:

* Hoz, és a prémium szintű storage adatlemezt csatol a virtuális géphez.
* Konfigurálja az adatlemezek SQL Server elérhetőnek kell lennie.
* Az adatlemezek konfigurálja azokat a tárolókészlethez, a megadott méret és a teljesítmény (iops-érték és átviteli) követelmények alapján.
* A tárolókészlet társít egy új meghajtót a virtuális gépen.
* Az új meghajtó (az adatraktározás terén, a tranzakciós feldolgozását, vagy általános) a megadott munkaterhelés típusától függően optimalizálja.

További tájékoztatást talál a hogyan Azure tárolási beállítások konfigurálása, lásd: a [tárolási konfigurációs szakasz](#storage-configuration). Az SQL Server virtuális gép létrehozása az Azure portálon teljes útmutatást lásd: [az üzembe helyezési oktatóanyag](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Erőforrás-kezelés sablonok
Ha használja a következő Resource Manager-sablonok, a két premium adatok lemezek vannak csatolva hozzá alapértelmezés szerint a tárolókészlet konfigurációját nem. Ezek a sablonok a virtuális géphez csatolt prémium adatlemezek számának módosítása azonban testre szabható.

* [Virtuális gép létrehozása az automatikus biztonsági mentés](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Virtuális gép létrehozása az automatikus javítás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Virtuális gép létrehozása az AKV-integráció](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Meglévő virtuális gépek
Meglévő SQL Server virtuális gépek módosíthatja az egyes tárolási beállításokat, az Azure portálon. Válassza ki a virtuális Gépet, a beállítások terület, és válassza az SQL Server-konfigurációs. Az SQL Server-beállítások panelen látható a virtuális gép aktuális tárolási felhasználásának mértéke. A virtuális gép meglévő összes meghajtó jelennek meg ezen a diagramon. Minden meghajtó a tárolóhely jeleníti meg a négy részből áll:

* SQL-adatok
* SQL-napló
* Másik (az SQL tároló)
* Elérhető

![Meglévő SQL Server rendszerű virtuális Géphez a tárolás konfigurálása](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Adjon hozzá egy új meghajtót, vagy egy meglévő meghajtó bővítésével tárolási konfigurálásához kattintson a fenti diagram szerkesztési hivatkozáshoz.

A konfigurációs beállítások, melyek megjelennek a függ attól függően, hogy ez a szolgáltatás előtt használt. Az első alkalommal való használatakor, új meghajtóra a tárolási követelményeit is megadhat. Ha ez a szolgáltatás egy meghajtó létrehozásához korábban használt, dönthet úgy, a lemezes tárolást kiterjeszteni.

### <a name="use-for-the-first-time"></a>Az első alkalommal használja
Ha most először ezzel a szolgáltatással, a tárolási méretét és a teljesítmény korlátok egy új meghajtót is megadhat. Ez a felület mi mutatunk be: kiépítés idő hasonlít. A fő különbség, hogy nincs engedélye a munkaterhelés típus megadásához. Ez a korlátozás megakadályozza, hogy a virtuális gépen meglévő SQL Server konfigurációk megszakítása.

![Konfigurálja az SQL Server Storage csúszkák](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

A specifikációk alapján új meghajtót az Azure létrehoz. Ebben a forgatókönyvben az Azure a következő tárolókonfigurálás feladatait végzi el:

* Hoz, és a prémium szintű storage adatlemezt csatol a virtuális géphez.
* Konfigurálja az adatlemezek SQL Server elérhetőnek kell lennie.
* Az adatlemezek konfigurálja azokat a tárolókészlethez, a megadott méret és a teljesítmény (iops-érték és átviteli) követelmények alapján.
* A tárolókészlet társít egy új meghajtót a virtuális gépen.

További tájékoztatást talál a hogyan Azure tárolási beállítások konfigurálása, lásd: a [tárolási konfigurációs szakasz](#storage-configuration).

### <a name="add-a-new-drive"></a>Adjon hozzá egy új meghajtót
Ha már konfigurálta az SQL Server virtuális gép tárolási, két új beállítások tárolási bővülő megjelenik. Az első lehetőség új meghajtóra, ami növelheti a virtuális gép teljesítményszintjének hozzáadni.

![Adjon hozzá egy új meghajtót SQL virtuális gép](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Azonban a meghajtó adásakor konfigurációt kell végrehajtania néhány extra manuális elérése a teljesítmény növelése érdekében.

### <a name="extend-the-drive"></a>A meghajtó bővítésével
A tárolási bővített a másik lehetőség egy meglévő meghajtó bővítésével. Ez a beállítás növeli a rendelkezésre álló tár a meghajtó, de nem növeli a teljesítményt. A tárolókészletekkel az oszlopok száma nem módosítható, a tárolókészlet létrehozása után. Az oszlopok száma párhuzamos írási műveletekről, amelyek is szétteríti a az adatlemezek számát határozza meg. Ezért bármely hozzáadott adatlemezek nem teljesítmény növelése érdekében. Az adatok írása csak további tárhelyet is biztosítanak. Ez a korlátozás is jelenti, hogy a meghajtó bővítésekor az oszlopok számát határozza meg, hogy a minimális adatlemez adható. Ezért ha négy adatlemezek tárolókészletet hoz létre, az oszlopok számát is négy. Kiterjeszti a tárhely, amikor hozzá kell adnia legalább négy adatlemezek.

![A meghajtó bővítésével egy SQL virtuális gép számára](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Tároló konfigurálása
Ez a témakör egy hivatkozást a tárolási konfigurációs módosítások Azure automatikusan SQL Virtuálisgép-létrehozásnál vagy az Azure portálon konfiguráció során hajtja végre.

* Ha kevesebb mint két több TB-nyi tárhelyet a virtuális Gépet jelölt ki, az Azure nem hoz létre tárolókészlet.
* Ha legalább két több TB-nyi tárhelyet a virtuális Gépet jelölt ki, Azure egy tárolókészlet konfigurálását jelenti. Ez a témakör következő szakaszának leírja a tárolókészlet konfigurációját.
* Mindig automatikus tárolási konfigurációt használja [prémium szintű storage](../premium-storage.md) P30 adatlemezek. Ebből következően nincs leképezést eredményez a kijelölt terabájt és a számát a virtuális Géphez csatlakozik, adatlemezek között 1:1.

Díjszabási információkért tekintse meg a [Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage) lapot a **lemezegységet** fülre.

### <a name="creation-of-the-storage-pool"></a>A tárolókészlet létrehozása
Azure beállításai a következő SQL Server virtuális gépekre a tárolókészlet létrehozásához.

| Beállítás | Érték |
| --- | --- |
| Paritásos mérete |(Az adatraktározás terén); 256 KB 64 KB-os (tranzakciós) |
| Lemezméretek |1 TB |
| Gyorsítótár |Olvasás |
| Foglalási mérete |64 KB-os NTFS foglalásiegység-méret |
| Azonnali fájlinicializálása |Engedélyezve |
| A memóriában memórialapok zárolása |Engedélyezve |
| Helyreállítás |Egyszerű helyreállítási (rugalmasság nélküli) |
| Az oszlopok száma |Adatlemezek száma<sup>1</sup> |
| A TempDB helye |Az adatlemezek tárolása<sup>2</sup> |

<sup>1</sup> a tárolókészlet létrehozása után nem változtathatja meg a tárolókészletben található oszlopok számát.

<sup>2</sup> a beállítás csak az első meghajtó hoz létre, a tárolási konfiguráció szolgáltatással vonatkozik.

## <a name="workload-optimization-settings"></a>Munkaterhelés optimalizálási beállításai
Az alábbi táblázat a három munkaterhelés típus rendelkezésre álló lehetőségeket, és a megfelelő optimalizálásokat ismerteti:

| Munkaterhelésének típusát | Leírás | Optimalizálás. |
| --- | --- | --- |
| **Általános** |Alapértelmezett beállítás, amely támogatja a legtöbb alkalmazás és szolgáltatás |None |
| **Tranzakciós feldolgozást** |Optimalizálja a tárhely az adatbázisok hagyományos OLTP számítási |Nyomkövetési jelző 1117<br/>Nyomkövetési jelző 1118 |
| **Adatraktározás** |Optimalizálja a tárhely az elemzési és jelentéskészítési számítási |Nyomkövetési jelző 610<br/>Nyomkövetési jelző 1117 |

> [!NOTE]
> A terhelés típusa csak adja meg, amikor kijelöli a a tárolási konfigurációs lépés egy SQL-virtuális gép.
>
>

## <a name="next-steps"></a>Következő lépések
Egyéb Azure virtuális gépeken futó SQL Server kapcsolatos témaköröket, lásd: [SQL Server Azure virtuális gépeken](virtual-machines-windows-sql-server-iaas-overview.md).
