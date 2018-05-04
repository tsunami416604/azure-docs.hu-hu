---
title: Az Azure-ban kezelt lemezek áttelepítésére AWS és más platformokra |} Microsoft Docs
description: Virtuális gépek létrehozása az Azure-ban a többi felhőből, például az AWS vagy más virtualizációs platformokról feltöltött virtuális merevlemezek és Azure felügyelt lemezek előnyeit.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/07/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0440eccbdf76fc58fadf46de2508d362c0de6cc3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Az Azure-ban kezelt lemezek áttelepítésére Amazon Web Services (AWS) és más platformokra

Feltöltheti a VHD-fájlok AWS vagy a helyszíni virtualizációs megoldások Azure virtuális gépek előnyeit felügyelt lemezek létrehozásához. Azure-lemezeket felügyelt szükségtelenné storage-fiókok Azure IaaS virtuális gépek kezeléséhez. A (prémium és Standard) típusát és méretét csak megadhat lemez van szüksége, és Azure hoz létre, és az Ön kezeli a lemezt. 

A virtuális általánosított, és speciális feltöltheti. 
- **Virtuális merevlemez általánosítva** -volt-e az összes személyes fiókadatait a Sysprep segítségével távolítja el. 
- **Virtuális merevlemez kifejezetten** -kezeli a felhasználói fiókok, alkalmazások és az eredeti virtuális gép más állapotba adatait. 

> [!IMPORTANT]
> Kövesse a virtuális merevlemez feltöltése az Azure-ba, mielőtt [Windows VHD vagy VHDX az Azure-bA feltöltendő előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Forgatókönyv                                                                                                                         | Dokumentáció                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Rendelkezik meglévő AWS EC2, amelyet a felügyelt lemezekkel Azure virtuális gépek áttelepítése                              | [Amazon Web Services (AWS) az Azure virtuális gép áthelyezése](aws-to-azure.md)                           |
| Egy virtuális Gépet, amely több Azure virtuális gépek létrehozásához képként használni szeretne egy másik virtualizációs platformról van. | [Egy általánosított virtuális merevlemez feltöltéséhez, és annak segítségével új virtuális gép létrehozása az Azure-ban](upload-generalized-managed.md) |
| Rendelkezik egy egyedi módon testreszabott virtuális Gépet, amely szeretné hozza létre újra az Azure-ban.                                                      | [Speciális virtuális merevlemez feltöltése az Azure-ba, és hozzon létre egy új virtuális Gépet](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Felügyelt lemez – áttekintés

Azure-lemezeket felügyelt virtuális gép felügyelet leegyszerűsíti storage-fiókok kezeléséhez szükség. Által kezelt lemezeken is előnye a rendelkezésre állási csoport a virtuális gépek nagyobb megbízhatóságot. Ez biztosítja, hogy a rendelkezésre állási csoport másik virtuális lemezek megfelelően különítve egymástól a hibaérzékeny pontok kialakulását elkerülése érdekében. A rendelkezésre állási csoport a különböző tárolási méretezési egységeit (bélyegek) egy méretezési egység tárolóhibák miatt a hardver és szoftver-hibák hatásának korlátozó különböző virtuális lemezek automatikusan helyezi. A igények alapján választhat a két típusú tárolási lehetőségek közül választhat: 
 
- [Prémium szintű felügyelt lemez](premium-storage.md) tartós állapot meghajtót (SSD)-alapú tárolási adathordozókon, nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása rendszerrel. Telepítse át a prémium szintű felügyelt lemez is igénybe vehet a sebesség előnyeit, és ezek a lemezek teljesítményét.  

- [Standard szintű felügyelt lemez](standard-storage.md) használják a merevlemez-meghajtó (HDD)-alapú tárolási adathordozókon, és a legalkalmasabb fejlesztési és tesztelési célú és egyéb gyakori hozzáférés munkaterhelések kevesebb érzékeny teljesítmény variancia.  

## <a name="plan-for-the-migration-to-managed-disks"></a>Felügyelt lemezek az áttelepítés megtervezése

Ez a szakasz segítséget nyújt a legjobb döntést a virtuális gép és a lemez típusok.

Ha azt tervezi, a nem felügyelt lemezekről történő felügyelt lemezek, amelyeket ajánlott ismernie a felhasználók által a [virtuális gép közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör nem fogja tudni módosítani a Virtuálisgép-méretet (mivel azok sikerült az átalakítás előtti). Ennek az az oka felügyelt lemezzel rendelkező virtuális gépek a felhasználónak engedéllyel kell rendelkeznie a Microsoft.Compute/disks/write az OS lemezeken.

### <a name="location"></a>Hely

Jelölje ki a helyet, ahol Azure felügyelt lemezek érhetőek el. Ha az áttelepítés prémium felügyelt lemezekre, is ellenőrizze, hogy prémium szintű storage elérhető a régióban, ahol szeretne áttelepíteni. Lásd: [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services) elérhető helyről naprakész tájékoztatást.

### <a name="vm-sizes"></a>A virtuális gépek mérete

Ha áttelepítés prémium szintű felügyelt lemez, hogy frissítse a virtuális gép méretét prémium szintű Storage képes a rendelkezésre álló terület a régióban, ahol a virtuális gép. Tekintse át a Virtuálisgép-méretek, amelyek a prémium szintű Storage-kompatibilis. Az Azure virtuális gép mérete paramétereknek szereplő [virtuális gépek méretei](sizes.md).
Tekintse át a virtuális gépek, amely együttműködik a prémium szintű Storage, és válassza ki a leginkább megfelelő virtuális gép méretét, amely a legjobban megfelel a számítási feladatok teljesítményétől. Győződjön meg arról, hogy nincs elegendő sávszélesség érhető el a virtuális Gépet, a lemez forgalom alapjául.

### <a name="disk-sizes"></a>Lemezméretek

**Prémium szintű felügyelt lemez**

Háromféle együtt a virtuális gép prémium szintű felügyelt lemez van, és mindegyik rendelkezik-e adott iops-érték és átviteli korlátok. Vegye figyelembe a működés felső korlátjának kiválasztása a prémium szintű lemez a virtuális gép a kapacitás, a teljesítmény, méretezhetőség az alkalmazás igényeinek megfelelően, és maximális tölti be.

| Prémium szintű lemezek típusa  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Lemezméret           | 128 GB            | 512 GB            | 1024 GB (1 TB)    |
| IOPS-érték lemezenként       | 500               | 2300              | 5000              |
| Adattovábbítás lemezenként | 100 MB / s | 150 MB / s | 200 MB / s |

**Standard szintű felügyelt lemez**

Standard szintű felügyelt lemez, amely a virtuális gép használható öt típusa van. Azok a különböző kapacitással rendelkeznek, de azonos IOPS és átviteli sebességének korlátai. Válassza ki a standard szintű felügyelt lemez az alkalmazás a kapacitásigények alapján.

| Standard lemez típusa  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   |
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              | 500              |
| Adattovábbítás lemezenként | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s |

### <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend 

**Prémium szintű felügyelt lemez**

Alapértelmezés szerint a gyorsítótárazási házirend lemez van *csak olvasható* prémium adatok lemezein, és *írható-olvasható* az a prémium szintű operációsrendszer-lemez csatolva a virtuális gép. A konfigurációs beállítás ajánlott az alkalmazás IOs rendszerhez az optimális teljesítmény eléréséhez. Írási műveleteket vagy a csak írható adatlemezek (köztük SQL Server) tiltsa le a lemezt gyorsítótárazás, hogy az alkalmazás jobb teljesítményt érhet el.

### <a name="pricing"></a>Díjszabás

Tekintse át a [kezelt lemezek árképzési](https://azure.microsoft.com/pricing/details/managed-disks/). Prémium szintű felügyelt lemez árképzési legyen, mint a nem felügyelt Premium lemezek. Azonban a standard szintű felügyelt lemez árképzési más nem felügyelt Standard lemezeknél.


## <a name="next-steps"></a>További lépések

- Kövesse a virtuális merevlemez feltöltése az Azure-ba, mielőtt [Windows VHD vagy VHDX az Azure-bA feltöltendő előkészítése](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
