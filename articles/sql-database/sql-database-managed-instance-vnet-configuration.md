---
title: Az Azure SQL adatbázis felügyelt példány VNet konfigurációja |} Microsoft Docs
description: Ez a témakör ismerteti a konfigurációs beállításokat egy virtuális hálózathoz (VNet), ha egy Azure SQL adatbázis-felügyelt példánya.
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: a51923738642b0e6a8ffd420b3cf433f7e869f59
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309333"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>A virtuális hálózat konfigurálása az Azure SQL adatbázis felügyelt példány

Az Azure SQL adatbázis felügyelt példány (előzetes verzió) telepítenie kell egy Azure-ban [virtuális hálózathoz (VNet)](../virtual-network/virtual-networks-overview.md). A központi telepítés lehetővé teszi, hogy a következő esetekben: 
- Egy felügyelt példányhoz kapcsolódva közvetlenül egy helyszíni hálózatból 
- A felügyelt példánya csatlakozik a csatolt kiszolgáló vagy egy másik helyi adattár 
- A felügyelt példánya csatlakozik Azure-erőforrások  

## <a name="plan"></a>Felkészülés

Tervezze meg, hogyan telepítsék központilag az alábbi kérdésekre adott válaszai segítségével a virtuális hálózatban egy felügyelt példány: 
- Tervezi egy vagy több felügyelt példányok telepítése? 

  A felügyelt példányok száma a felügyelt példányok lefoglalni az alhálózat minimális méretét határozza meg. További információkért lásd: [határozza meg az alhálózat méretét felügyelt példány](#create-a-new-virtual-network-for-managed-instances). 
- Nem kell telepíteni a felügyelt példányát létrehozni meglévő virtuális hálózatban, vagy hoz létre egy új hálózati? 

   Ha azt tervezi, meglévő virtuális hálózat használatára, szüksége, hogy a felügyelt példányát olyan hálózati konfiguráció módosítása. További információkért lásd: [módosíthatja a meglévő virtuális hálózat felügyelt példány](#modify-an-existing-virtual-network-for-managed-instances). 

   Ha azt tervezi, új virtuális hálózat létrehozásához, lásd: [új virtuális hálózat létrehozása kezelt példány](#create-a-new-virtual-network-for-managed-instances).

## <a name="requirements"></a>Követelmények

A felügyelt példány létrehozásához a következő követelményeknek kell fordítsanak alhálózatot, amely megfelel a Vneten belül:
- **Üres**: az alhálózat nem tartalmazhat minden hozzá társított más felhőalapú szolgáltatás, és átjáróalhálózatot nem lehet. Ön nem fog tudni hozható létre példány által felügyelt erőforrások kívül felügyelt példányt tartalmazó alhálózat vagy más erőforrásokat később az alhálózaton belül.
- **Nincs NSG**: az alhálózat nem rendelkeznie kell egy hozzá társított hálózati biztonsági csoport.
- **Rendelkezik az adott útválasztási táblázatot**: az alhálózat rendelkezniük kell egy felhasználó útvonal tábla (UDR) 0.0.0.0/0 következő ugrásaként internetes az egyetlen útvonal rendelve. További információkért lásd: [szükséges útvonaltábla létrehozása, és társítsa azt](#create-the-required-route-table-and-associate-it)
3. **Nem kötelező egyéni DNS**: Ha egyéni DNS meg van adva a virtuális hálózaton, Azure rekurzív feloldókat IP-cím (például 168.63.129.16) hozzá kell adni a listához. További információkért lásd: [egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
4. **Nincs végpont**: az alhálózat nem rendelkeznie kell egy végpontot (tároló vagy Sql) társítva. Győződjön meg arról, hogy az Szolgáltatásvégpontok lehetőség le van tiltva virtuális hálózat létrehozásakor.
5. **Elegendő IP-címek**: az alhálózat rendelkeznie kell legalább 16 IP-címet. További információkért lásd: [határozza meg az alhálózat méretét felügyelt példányok](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> Új kezelt példányt telepíthet, ha a cél alhálózat nem kompatibilis az összes előző követelményt fogunk. A cél virtuális hálózat és az alhálózati kell tartani e példány felügyelt követelményeknek megfelelően (előtt és után a központi telepítés), mivel bármilyen megsértése esetén okozhat a példány, írja be a hibás állapotban, és már nem érhető el. Végezze el a állapot megköveteli, hogy új példányt létrehozni a megfelelő hálózati házirendek a Vneten belül, hozza létre újra a példányok szintjén adatait, és az adatbázisok visszaállítása. Az azzal, hogy az alkalmazások jelentős állásidőt.

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>Határozza meg az alhálózat méretét felügyelt példányok

Egy felügyelt példány létrehozásakor az Azure virtuális gépek kiépítése során választott réteg méretétől függően több foglal le. Mivel ezek a virtuális gépek társítva az alhálózat, IP-címek igényelnek. Magas rendelkezésre állásának biztosításához a normál működést és a szolgáltatás karbantartása során, Azure további virtuális gépeket is kioszthat. Ennek eredményeképpen a száma alhálózat IP-címet, a felügyelt példányok száma alhálózat nagyobb. 

Úgy lett kialakítva a felügyelt példánya kell legalább egy alhálózaton 16 IP-címet, és legfeljebb 256 IP-címeket használhatnak. Ennek eredményeképpen alhálózati maszkok /28 való /24 használja, az alhálózati IP-címtartományok meghatározásakor. 

Ha tervezi az alhálózaton belül több felügyelt példányok telepítése és az alhálózat méretét optimalizálására, ezeket a paramétereket használhatja egy számítás: 

- Azure az alhálózat öt IP-címeket használó saját igényei szerint 
- Általános célú feltünteti van szüksége a két címet 

**Példa**: nyolc felügyelt példányok tervezi. Hogy azt jelenti, hogy 5 + 8 * 2 = 21 kell IP-címek. IP-címtartományok 2 hatványa határozzák meg, mivel szüksége van-e az IP-címtartomány 32 (2 ^ 5) IP-címeket. Ezért kell lefoglalni az alhálózat /27 alhálózati maszkkal. 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>Új virtuális hálózat létrehozása kezelt példányok 

Egy Azure virtuális hálózat létrehozása előfeltétele egy felügyelt példányának létrehozásakor. Használhatja az Azure-portálon [PowerShell](../virtual-network/quick-create-powershell.md), vagy [Azure CLI](../virtual-network/quick-create-cli.md). Az alábbi szakasz az Azure portál használatával lépéseit mutatja be. Az itt tárgyalt részleteit a fenti módszerek vonatkozik.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg és kattintson a **Virtuális hálózat** elemre. Ellenőrizze, hogy a **Resource Manager** üzembehelyezési mód van-e kiválasztva, majd kattintson a **Létrehozás** lehetőségre.

   ![virtuális hálózat létrehozása](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Töltse ki a virtuális hálózati űrlapot a kért információt, például az alábbi képernyőfelvételen módon:

   ![virtuális hálózat létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Kattintson a **Create** (Létrehozás) gombra.

   A címterület és alhálózati meg van adva, a CIDR-formátumban. 

   > [!IMPORTANT]
   > Az alapértelmezett értékeit. Hozzon létre, amely a virtuális hálózat címterület-alhálózatot. Ha ezt a lehetőséget választja, nem lehet létrehozni a virtuális hálózaton kívül felügyelt példány bármely más erőforrásokat. 

   Az ajánlott módszer a következő lenne: 
   - A következő alapján számítja ki az alhálózat méretét [határozza meg az alhálózat méretét felügyelt példány](#determine-the-size-of-subnet-for-managed-instances) szakasz  
   - A többi virtuális hálózat igényeinek felmérése 
   - Ennek megfelelően adja meg a virtuális hálózat és az alhálózati címtartományt 

   Győződjön meg arról, hogy a szolgáltatás végpontok marad lehetőséget **letiltott**. 

   ![virtuális hálózat létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>A szükséges útvonaltábla létrehozása, és társítsa azt

1. Jelentkezzen be az Azure Portalra  
2. Keresse meg az **Útvonaltábla** elemet, kattintson rá, majd az Útvonaltábla lapon kattintson a **Létrehozás** gombra.

   ![útvonaltábla létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. Hozzon létre 0.0.0.0/0 következő ugrásaként Internet útvonalat, például a képernyőképek a következő módon:

   ![útvonaltábla hozzáadása](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![útvonal](./media/sql-database-managed-instance-tutorial/route.png)

4. Ez az útvonal társítása az alhálózat felügyelt-példány, például a képernyőképek a következő módon:

    ![alhálózat](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![útvonaltábla beállítása](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![útvonaltábla beállítása – mentés](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


A virtuális hálózat létrehozása után készen áll a felügyelt példányt létrehozni.  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>Módosítsa a meglévő virtuális hálózat felügyelt példányok 

Kérdések és válaszok ebben a szakaszban megtudhatja, hogy miként hozzáadása egy felügyelt példány már meglévő virtuális hálózatot. 

**Használ klasszikus és Resource Manager üzembe helyezési modellben a meglévő virtuális hálózat?** 

Erőforrás-kezelő virtuális hálózatok csak a felügyelt példánya hozhat létre. 

**Hozzon létre új alhálózatot kezelt SQL-példány, vagy a meglévőt használja?**

Ha azt szeretné, hogy hozzon létre újat: 

- A következő alhálózati méretének kiszámítása a [határozza meg az alhálózat méretét felügyelt példányok](#determine-the-size-of-subnet-for-managed-instances) szakasz.
- Kövesse lépéseket [hozzáadása, módosítása vagy törlése a virtuális hálózati alhálózat](../virtual-network/virtual-network-manage-subnet.md). 
- Hozzon létre egy útválasztási táblázatot, amely tartalmaz egy bejegyzésnek **0.0.0.0/0**, mert a következő ugrásaként Internet, és rendelje hozzá azt az alhálózati felügyelt példány.  

Abban az esetben, ha szeretne egy létező alhálózatot belül felügyelt példány létrehozása: 
- Ha az alhálózatot üres – a felügyelt példánya nem hozható létre az alhálózat más erőforrásokat, beleértve az átjáró alhálózatának tartalmazó ellenőrzése 
- A következő alhálózati méretének kiszámítása a [határozza meg az alhálózat méretét felügyelt példányok](#determine-the-size-of-subnet-for-managed-instances) szakaszt, és győződjön meg arról, hogy megfelelő méretű-e. 
- Ellenőrizze, hogy végpontok nincsenek engedélyezve az alhálózaton.
- Győződjön meg arról, hogy vannak-e nélkül az alhálózathoz társított hálózati biztonsági csoportok 

**Egyéni DNS-kiszolgáló konfigurálva van?** 

Ha igen, tekintse meg [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md). 

- A szükséges útvonaltábla létrehozása, és társítsa azt: lásd: [szükséges útvonaltábla létrehozása, és társítsa azt](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>További lépések

- Megtudhatja, [Mi az, hogy a felügyelt példánya](sql-database-managed-instance.md)
- Az oktatóanyag bemutatja, hogyan hozhat létre egy Vnetet, hozzon létre egy felügyelt példányt és egy adatbázis visszaállítása egy adatbázis biztonsági másolatból, lásd: [egy Azure SQL adatbázis-felügyelt példány létrehozása](sql-database-managed-instance-create-tutorial-portal.md).
- DNS problémákkal kapcsolatban lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)
