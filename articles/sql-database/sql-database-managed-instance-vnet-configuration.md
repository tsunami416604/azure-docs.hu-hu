---
title: Az Azure SQL Database felügyelt példányok Vnetjének konfigurálásával |} A Microsoft Docs
description: Ez a témakör ismerteti a virtuális hálózat (VNet) egy Azure SQL Database felügyelt példánya a konfigurációs beállításokat.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 0fea91fb067a6d78ef25cb0ff8014b65a8b6a916
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258100"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Virtuális hálózat konfigurálása az Azure SQL Database felügyelt példány

Az Azure SQL Database felügyelt példány (előzetes verzió) kell üzembe helyezni egy Azure-ban [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md). A központi telepítés lehetővé teszi, hogy a következő esetekben: 
- Felügyelt példány csatlakozik közvetlenül egy a helyszíni hálózatból 
- Felügyelt példány csatlakozik a csatolt kiszolgáló vagy egy másik helyszíni adattár 
- Felügyelt példány csatlakozik az Azure-erőforrások  

## <a name="plan"></a>Felkészülés

Tervezze meg, hogyan kívánja üzembe helyezni egy virtuális hálózat, az alábbi kérdésekre adott válaszai segítségével felügyelt példány: 
- Tervezi egy vagy több felügyelt példányok üzembe helyezéséhez? 

  A felügyelt példányok száma a felügyelt példányok lefoglalni az alhálózat minimális mérete határozza meg. További információkért lásd: [alhálózat méretét határozza meg a felügyelt példány](#create-a-new-virtual-network-for-managed-instances). 
- Nem kell a felügyelt példány üzembe egy meglévő virtuális hálózaton, vagy hoz létre egy új hálózati? 

   Ha azt tervezi, használja a meglévő virtuális hálózattal, módosítania, hogy a felügyelt példány megfeleljen a hálózati konfigurációt. További információkért lásd: [meglévő virtuális hálózat módosítása a felügyelt példány](#modify-an-existing-virtual-network-for-managed-instances). 

   Ha azt tervezi, hozzon létre új virtuális hálózatot, [új virtuális hálózat létrehozása felügyelt példány](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Követelmények

A felügyelt példány létrehozásához az alábbi követelményeknek kell dedikált alhálózatot, amely megfelel a virtuális hálózatán:
- **Üres**: az alhálózat nem tartalmazhat más felhőalapú szolgáltatás társítva van hozzá, és nem lehet átjáró-alhálózatot. Nem felügyelt példány létrehozása felügyelt példány naplóátvitelen kívüli egyéb erőforrásokra tartalmazó alhálózathoz, vagy később az alhálózaton belüli más erőforrások hozzáadásához.
- **Nincs hálózati biztonsági csoport**: az alhálózathoz társított hálózati biztonsági csoport nem lehet.
- **Rendelkezik az adott útválasztási táblázat**: az alhálózat rendelkeznie kell egy felhasználó útválasztási tábla (UDR) 0.0.0.0/0 következő ugrási típusú internettel, az egyetlen útvonal rendelve. További információkért lásd: [hozzon létre a szükséges útválasztási táblázatot, és társítsa azt](#create-the-required-route-table-and-associate-it)
3. **Nem kötelező egyéni DNS**: Ha egyéni DNS a virtuális hálózaton van megadva, az Azure rekurzív feloldók IP-címet (például a 168.63.129.16) hozzá kell adni a listához. További információkért lásd: [egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
4. **Nincs szolgáltatásvégpont**: az alhálózat nem rendelkeznie kell egy végpontot (Storage- vagy Sql) társítva van hozzá. Győződjön meg arról, hogy a Szolgáltatásvégpontok lehetőség nem érhető el virtuális hálózat létrehozásakor.
5. **Elegendő IP-címek**: az alhálózatnak rendelkeznie kell legalább 16 IP-címet. További információkért lásd: [alhálózat méretét határozza meg a felügyelt példány](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Meg nem fog tudni új felügyelt példány üzembe helyezése, ha a cél alhálózat nem kompatibilis az összes fenti követelményt. A cél virtuális hálózat és az alhálózathoz kell tartani a felügyelt példány követelményeknek megfelelően (előtt és üzembe helyezés után), bármilyen megsértése oka lehet, hogy a példány hibás állapotba, és már nem érhető el. Utáni helyreállítás állapot megköveteli, hogy hozzon létre új példányt a megfelelő hálózati szabályzatok rendelkező virtuális hálózaton, hozza létre újra a példányok szintű adatait, és az adatbázisok visszaállítása. Ez vezet be, jelentős állásidő alkalmazásai számára.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Alhálózat méretét határozza meg a felügyelt példány

Felügyelt példány létrehozásakor az Azure virtuális gépek kiépítése során választott szint méretétől függően több foglal le. Mivel ezek a virtuális gépek társítva az alhálózat, IP-címek igényelnek. Magas rendelkezésre állásának biztosításához a normál működést és a szolgáltatás karbantartás alatt, az Azure további virtuális gépeket is kioszthat. Ennek eredményeképpen az alhálózat szükséges IP-címek száma, az alhálózat által felügyelt példányok száma nagyobb. 

A kialakításból fakadóan felügyelt példány legalább 16, az alhálózat IP-címet kell, és előfordulhat, hogy legfeljebb 256 IP-cím használata. Ennek eredményeképpen alhálózati maszkok/28-as, /24 használja, az alhálózati IP-címtartományok meghatározásakor. 

Ha azt tervezi, az alhálózaton belül több felügyelt példány üzembe helyezése és az alhálózat méretét optimalizálására, a számítás használni ezeket a paramétereket: 

- Az Azure az alhálózat öt IP-címet használ a saját igényei szerint 
- Egyes általános célú példányok van szüksége a két cím 
- Egyes üzletileg kritikus példányok kell négy címét

**Példa**: tervezett három általános célú és két üzleti az kritikus fontosságú felügyelt példányok. Hogy azt jelenti, hogy 5 + 3 * 2 + 2 * 4 = 19 kell IP-címek. IP-címtartományok 2 hatványa határozzák meg, mint az IP-címtartományt 32 kell (2 ^ 5) IP-címeket. Ezért kell lefoglalni az alhálózat/27-eset a alhálózati maszkkal. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Felügyelt példányok egy új virtuális hálózat létrehozása 

Az Azure virtuális hálózat létrehozása előfeltétele a felügyelt példány létrehozása. Használhatja az Azure Portalon [PowerShell](../virtual-network/quick-create-powershell.md), vagy [Azure CLI-vel](../virtual-network/quick-create-cli.md). A következő szakasz bemutatja a lépéseket az Azure portal használatával. Mindkét módszerhez a részleteit az itt tárgyalt vonatkoznak.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg és kattintson a **Virtuális hálózat** elemre. Ellenőrizze, hogy a **Resource Manager** üzembehelyezési mód van-e kiválasztva, majd kattintson a **Létrehozás** lehetőségre.

   ![virtuális hálózat létrehozása](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Adja meg a virtuális hálózat űrlapján a szükséges információkat, például az alábbi képernyőfelvételen látható módon:

   ![virtuális hálózat létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Kattintson a **Create** (Létrehozás) gombra.

   A címtér és alhálózati meg van adva, a CIDR-jelölésrendszerben. 

   > [!IMPORTANT]
   > Az alapértelmezett értékeket, amely a virtuális hálózat címtere alhálózat létrehozása. Ha ezt a lehetőséget választja, nem lehet létrehozni a felügyelt példány nem virtuális hálózatban lévő összes többi erőforrást. 

   Az ajánlott módszer a következő lenne: 
   - Alhálózat méretének kiszámítása a következő [alhálózat méretét határozza meg a felügyelt példány](#determine-the-size-of-subnet-for-managed-instances) szakasz  
   - A virtuális hálózat a többi igények felmérése 
   - Ennek megfelelően adja meg a virtuális hálózat és alhálózat-címtartományok 

   Győződjön meg arról, hogy a Szolgáltatásvégpontok marad lehetőséget **letiltott**. 

   ![virtuális hálózat létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>Hozzon létre a szükséges útválasztási táblázatot, és társítsa azt

1. Bejelentkezés az Azure Portal webhelyre  
2. Keresse meg az **Útvonaltábla** elemet, kattintson rá, majd az Útvonaltábla lapon kattintson a **Létrehozás** gombra.

   ![útvonaltábla létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Hozzon létre egy 0.0.0.0/0 Internet következő ugrási típusú útvonalat az alábbi képernyőfelvételnek megfelelően hasonló módon:

   ![útvonaltábla hozzáadása](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![útvonal](./media/sql-database-managed-instance-tutorial/route.png)

4. Ez az útvonal társítása az alhálózat a felügyelt példány oly módon, például az alábbi képernyőfelvételnek megfelelően:

    ![alhálózat](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![útvonaltábla beállítása](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![útvonaltábla beállítása – mentés](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


A virtuális hálózat létrehozása után készen áll a felügyelt példány létrehozása.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Felügyelt példányok egy meglévő virtuális hálózat módosítása 

Kérdések és válaszok ebben a szakaszban bemutatják, hogyan felügyelt példány hozzáadása meglévő virtuális hálózattal. 

**Használ-e a meglévő virtuális hálózat klasszikus vagy Resource Manager üzemi modell?** 

Felügyelt példány csak a Resource Manager virtuális hálózatot hozhat létre. 

**Hozzon létre új SQL felügyeltpéldány-alhálózatot, vagy a meglévőt szeretne használni?**

Ha szeretne, hozzon létre újat: 

- Az alábbi az irányelveket, az alhálózat méretének kiszámítása a [alhálózat méretét határozza meg a felügyelt példányok](#determine-the-size-of-subnet-for-managed-instances) szakaszban.
- Kövesse lépéseket [hozzáadása, módosítása vagy törlése egy virtuális hálózat alhálózatához](../virtual-network/virtual-network-manage-subnet.md). 
- Hozzon létre egy útválasztási táblázatot, amely tartalmaz egy bejegyzést, **0.0.0.0/0**, ahogy a következő Ugrás az interneten, és társíthatja azt az alhálózatot a felügyelt példány esetében.  

Abban az esetben, ha szeretne egy meglévő alhálózaton belül a felügyelt példány létrehozása: 
- Ellenőrizze, hogy ha az alhálózat üres – felügyelt példány nem hozható létre más erőforrások, például az átjáró-alhálózat tartalmazó alhálózathoz 
- Az alábbi az irányelveket, az alhálózat méretének kiszámítása a [alhálózat méretét határozza meg a felügyelt példányok](#determine-the-size-of-subnet-for-managed-instances) szakaszt, és győződjön meg arról, hogy megfelelően van-e méretezve. 
- Ellenőrizze, hogy a Szolgáltatásvégpontok az alhálózaton nincsenek engedélyezve.
- Győződjön meg arról, hogy nincsenek-e az alhálózathoz társított hálózati biztonsági csoportok 

**Egyéni DNS-kiszolgáló konfigurálva van?** 

Ha igen, tekintse meg [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md). 

- Hozzon létre a szükséges útválasztási táblázatot, és társítsa azt: lásd: [hozzon létre a szükséges útválasztási táblázatot, és társítsa azt](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md)
- Hozzon létre egy Vnetet, létrehoz egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból történő bemutató oktatóanyagot, tekintse meg a [létrehozása egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-create-tutorial-portal.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)
