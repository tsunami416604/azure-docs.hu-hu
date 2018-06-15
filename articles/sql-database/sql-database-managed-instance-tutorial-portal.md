---
title: 'Azure Portal: Felügyelt SQL Database-példány létrehozása | Microsoft Docs'
description: Felügyelt Azure SQL Database-példány létrehozása egy VNeten, és a Wide World Importers-adatbázis biztonsági másolatának visszaállítása az SSMS használatával.
keywords: oktatóanyag az SQL Database használatához, felügyelt SQL Database-példány létrehozása
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 03/14/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: 774a761465cfd886b85378a35dd43ac656a7ee48
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2018
ms.locfileid: "31004477"
---
# <a name="create-an-azure-sql-database-managed-instance-in-the-azure-portal"></a>Felügyelt Azure SQL Database-példány létrehozása az Azure Portalon

Ez az oktatóanyag bemutatja, hogyan hozhat létre felügyelt Azure SQL Database-példányt (előzetes verzió) az Azure Portalon, egy virtuális hálózat (VNet) dedikált alhálózatán. Ezt követően ismerteti, hogyan csatlakozhat a felügyelt példányhoz az SQL Server Management Studióval egy, ugyanazon a VNeten található virtuális gépen, és hogyan állíthatja vissza egy adatbázis az Azure Blob Storage-ben tárolt biztonsági másolatát a felügyelt példányon.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!IMPORTANT]
> Azon régiók listájáért, amelyekben a felügyelt példány jelenleg elérhető, lásd a cikket, amely [az adatbázisok migrálásával foglalkozik egy teljes mértékben felügyelt szolgáltatásba az Azure SQL Database felügyelt példányával](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="whitelist-your-subscription"></a>Az előfizetés felvétele az engedélyezési listára

A felügyelt példány először sorompós nyilvános előzetes verzióként érhető el, amelyhez az előfizetést engedélyezési listára kell helyezni. Ha az előfizetés még nem szerepel az engedélyezési listán, a következő lépésekkel tekintheti meg és fogadhatja el az előzetes verzió feltételeit, és küldhet engedélyezési listára helyezési kérést.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg a **Felügyelt példány** lehetőséget, és válassza a **Felügyelt Azure SQL Database-példány (előzetes verzió)** elemet.
3. Kattintson a **Create** (Létrehozás) gombra.

   ![felügyelt példány létrehozása](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Válassza ki az előfizetést, kattintson az **Előzetes verzió feltételei** elemre, majd adja meg a kért adatokat.

   ![felügyelt példány előzetes verziójának feltételei](./media/sql-database-managed-instance-tutorial/preview-terms.png)

5. Ha elkészült, kattintson az **OK** gombra.

   ![felügyelt példány előzetes verziójának feltételei](./media/sql-database-managed-instance-tutorial/preview-approval-pending.png)

   > [!NOTE]
   > Miközben az előzetes verzió jóváhagyására várakozik, folytathatja az oktatóanyag következő néhány szakaszát.

## <a name="configure-a-virtual-network-vnet"></a>Virtuális hálózat (VNet) konfigurálása

A következő lépések bemutatják, hogyan hozhat létre új [Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md)-alapú virtuális hálózatot (VNetet) a felügyelt példányhoz. A VNetek konfigurálásával kapcsolatos további információkért tekintse meg a [felügyelt példányok VNetjének konfigurálásával kapcsolatos](sql-database-managed-instance-vnet-configuration.md) cikket.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg és kattintson a **Virtuális hálózat** elemre. Ellenőrizze, hogy a **Resource Manager** üzembehelyezési mód van-e kiválasztva, majd kattintson a **Létrehozás** lehetőségre.

   ![virtuális hálózat létrehozása](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. Adja meg a kért adatokat a virtuális hálózat űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Címtér**|Bármely érvényes címtartomány, például a 10.14.0.0/24|A virtuális hálózat címének neve a CIDR-jelölésrendszerben.|
   |**Előfizetés**|Az Ön előfizetése|Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket.|
   |**Erőforráscsoport**|Bármely érvényes erőforráscsoport (új vagy meglévő)|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Hely**|Bármely érvényes hely| A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket.|
   |**Alhálózat neve**|Bármely érvényes alhálózatnév, például mi_subnet|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Alhálózati címtartomány**|Bármely érvényes alhálózati cím, például 10.14.0.0/28. Használjon a címtérnél kisebb alhálózati címteret, hogy elegendő hely legyen egyéb alhálózatok létrehozására az adott VNeten belül. Ilyenek lehetnek például a teszt-/ügyfélalkalmazásokat üzemeltető alhálózatok, illetve az átjáróalhálózatok, amelyekhez helyszíni vagy más VNetekből lehet csatlakozni.|Az alhálózat címtartománya a CIDR-jelölésrendszerben. A virtuális hálózat címterének tartalmaznia kell.|
   |**Szolgáltatásvégpontok**|Letiltva|Egy vagy több szolgáltatásvégpont engedélyezése ehhez az alhálózathoz.|
   ||||

   ![virtuális hálózat létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-new-route-table-and-a-route"></a>Új útvonaltábla és útvonal létrehozása

A következő lépések azt mutatják be, hogyan hozhat létre egy 0.0.0.0/0 című, Internet következő ugrási típusú útvonalat.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg az **Útvonaltábla** elemet, kattintson rá, majd az Útvonaltábla lapon kattintson a **Létrehozás** gombra. 

   ![útvonaltábla létrehozása](./media/sql-database-managed-instance-tutorial/route-table-create.png)

3. Adja meg a kért adatokat az útvonaltábla űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Előfizetés**|Az Ön előfizetése|Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket.|
   |**Erőforráscsoport**|Válassza ki az előző eljárásban létrehozott erőforráscsoportot.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Hely**|Válassza ki az előző eljárásban megadott helyet.| A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket.|
   |**BCP-útvonalpropagálás letiltása**|Letiltva||
   ||||

   ![útvonaltábla létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

4. Kattintson a **Create** (Létrehozás) gombra.
5. Az útvonaltábla létrehozása után nyissa meg az újonnan létrehozott útvonaltáblát.

   ![útvonaltábla](./media/sql-database-managed-instance-tutorial/route-table.png)

6. Kattintson az **Útvonalak**, majd a **Hozzáadás** elemre.

   ![útvonaltábla hozzáadása](./media/sql-database-managed-instance-tutorial/route-table-add.png)

7.  Adja hozzá a **0.0.0.0/0, Internet következő ugrási típusú** útvonalat az **egyetlen** útvonalként az alábbi táblázatban szereplő információk segítségével:

    | Beállítás| Ajánlott érték | Leírás |
    | ------ | --------------- | ----------- |
    |**Útvonal neve**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
    |**Címelőtag**|0.0.0.0/0|A cél IP-cím, amelyre az útvonal vonatkozik a CIDR-jelölésrendszerben.|
    |**Következő ugrási típus**|Internet|A következő ugrás az útvonal egyező csomagjait kezeli.|
    |||

    ![útvonal](./media/sql-database-managed-instance-tutorial/route.png)

8. Kattintson az **OK** gombra.

## <a name="to-apply-the-route-table-to-the-managed-instance-subnet"></a>Az útvonaltábla alkalmazása a felügyelt példány alhálózatára

Az alábbi lépések bemutatják, hogyan állíthatja be az új útvonaltáblát a felügyelt példány alhálózatán.

1. Az új útvonaltábla a felügyelt példány alhálózatán történő beállításához nyissa meg a korábban létrehozott virtuális hálózatot.
2. Kattintson az **Alhálózatok** elemre, majd a felügyelt példány alhálózatára (a következő képernyőképen **mi_subnet** néven látható).

    ![alhálózat](./media/sql-database-managed-instance-tutorial/subnet.png)

11. Kattintson az **Útvonaltábla** menüpontra, majd válassza a **myMI_route_table** lehetőséget.

    ![útvonaltábla beállítása](./media/sql-database-managed-instance-tutorial/set-route-table.png)

12. Kattintson a **Mentés** gombra.

    ![útvonaltábla beállítása – mentés](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések azt mutatják be, hogyan hozhatja létre a felügyelt példányt az előzetes verzió jóváhagyása után.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keresse meg a **Felügyelt példány** lehetőséget, és válassza a **Felügyelt Azure SQL Database-példány (előzetes verzió)** elemet.
3. Kattintson a **Create** (Létrehozás) gombra.

   ![felügyelt példány létrehozása](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

3. Válassza ki előfizetését, és ellenőrizze, hogy az előzetes verzió feltételeinél az **Elfogadva** állapot látható-e.

   ![felügyelt példány előzetes verziójának feltételei elfogadva](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

4. Adja meg a kért adatokat a felügyelt példány űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Felügyelt példány neve**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Felügyelt példány rendszergazdai bejelentkezési neve**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.| 
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   |**Erőforráscsoport**|A korábban létrehozott erőforráscsoport||
   |**Hely**|Az előzőleg kiválasztott hely|A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket.|
   |**Virtuális hálózat**|A korábban létrehozott virtuális hálózat|

   ![felügyelt példány létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

5. Kattintson a **Tarifacsomag** elemre a számítási és tárolási erőforrások méretezéséhez, valamint a lehetséges tarifacsomagok áttekintéséhez. Alapértelmezés szerint a példány 32 GB ingyenes tárterülettel rendelkezik, amely nem feltétlenül elegendő alkalmazásai számára.
6. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. 
   ![felügyelt példány létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

7. Ha végzett, a beállítások mentéséhez kattintson az **Alkalmaz** gombra.  
8. Kattintson a **Létrehozás** elemre a felügyelt példány üzembe helyezéséhez.
9. Kattintson az **Értesítések** ikonra az üzembe helyezés állapotának megtekintéséhez.
 
   ![üzembe helyezés folyamatban](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

9. Kattintson az **Üzembe helyezés folyamatban** értesítésre a felügyelt példány ablakának megnyitásához, amelyben részletesebben nyomon követheti az üzembehelyezési folyamatot.
 
   ![üzembe helyezés folyamatban 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

Amíg tart az üzembe helyezés, folytassa a következő eljárással.

> [!IMPORTANT]
> Az alhálózat első példánya esetében az üzembe helyezés általában jóval több időt (akár 24 óránál többet is) vesz igénybe, mint a további példányok esetében. Ne szakítsa meg az üzembehelyezési műveletet, ha az a vártnál tovább tart. Az első példány üzembe helyezéséhez csak átmenetileg szükséges ilyen hosszú idő. A nyilvános előzetes verzió használatának megkezdése után jelentős mértékben csökken majd az üzembehelyezési idő.

## <a name="create-a-new-subnet-in-the-vnet-for-a-virtual-machine"></a>Új alhálózat létrehozása virtuális gép VNetjén

A következő lépések bemutatják, hogyan hozhat létre egy második alhálózatot egy virtuális gép VNetjén, amelyen telepíti az SQL Server Management Studiót, és csatlakozik a felügyelt példányhoz.

1. Nyissa meg a virtuális hálózati erőforrást.
 
   ![VNet](./media/sql-database-managed-instance-tutorial/vnet.png)

2. Kattintson az **Alhálózatok** menüpontra, majd a **+Alhálózat** gombra.
 
   ![alhálózat hozzáadása](./media/sql-database-managed-instance-tutorial/add-subnet.png)

3. Adja meg a kért adatokat az alhálózat űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Címtartomány (CIDR-blokk)**|Bármely érvényes címtartomány a VNeten belül (használja az alapértelmezettet)||
   |**Hálózati biztonsági csoport**|None||
   |**Útvonaltábla**|None||
   |**Szolgáltatásvégpontok**|Nincs||

   ![virtuális gép alhálózatának részletei](./media/sql-database-managed-instance-tutorial/vm-subnet-details.png)

4. Kattintson az **OK** gombra.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Virtuális gép létrehozása a VNet új alhálózatában

A következő lépések bemutatják, hogyan hozhat létre virtuális gépet ugyanazon VNeten, amelyen a felügyelt példány létrejön. 

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** vagy a **Windows 10** lehetőséget. Az oktatóanyag ezen szakaszában a Windows Servert használjuk. A Windows 10 konfigurálása alapvetően ehhez hasonló. 

   ![számítás](./media/sql-database-managed-instance-tutorial/compute.png)

3. Adja meg a kért adatokat a virtuális gép űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   | **Virtuális merevlemez típusa**|SSD|Az SSD-k kínálják a legjobb ár/teljesítmény arányt.|   
   |**Felhasználónév**|Bármely érvényes felhasználónév|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.| 
   |**Jelszó**|Bármely érvényes jelszó|A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).| 
   |**Előfizetés**|Az Ön előfizetése|Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket.|
   |**Erőforráscsoport**|A korábban létrehozott erőforráscsoport||
   |**Hely**|Az előzőleg kiválasztott hely||
   |**Már rendelkezik Windows-licenccel**|Nem|Aktív Frissítési garanciával rendelkező Windows-licencek használata esetén az Azure Hybrid Benefittel csökkentheti a számítási költségeket.|
   ||||

   ![virtuális gép létrehozásának űrlapja](./media/sql-database-managed-instance-tutorial/virtual-machine-create-form.png)

3. Kattintson az **OK** gombra.
4. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. Ebben az oktatóanyagban csak egy kisméretű virtuális gépre lesz szüksége.

    ![A virtuális gépek mérete](./media/sql-database-managed-instance-tutorial/virtual-machine-size.png)  

5. Kattintson a **Kiválasztás** gombra.
6. A **Beállítások** űrlapon kattintson az **Alhálózat** menüpontra, majd válassza a **vm_subnet** elemet. Ne válassza azt az alhálózatot, amelyben a felügyelt példány üzembe van helyezve. Válasszon inkább egy másik alhálózatot ugyanabban a VNetben.

    ![Virtuális gép beállításai](./media/sql-database-managed-instance-tutorial/virtual-machine-settings.png)  

7. Kattintson az **OK** gombra.
8. Az összefoglalási lapon tekintse át az ajánlat részleteit, majd a virtuális gép üzembe helyezésének megkezdéséhez kattintson a **Létrehozás** gombra.
 
## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az alábbi lépések bemutatják, hogyan csatlakozhat az újonnan létrehozott virtuális géphez távoli asztali kapcsolattal.

1. Ha az üzembe helyezés elkészült, lépjen a virtuális gép erőforráshoz.

    ![VM](./media/sql-database-managed-instance-tutorial/vm.png)  

2. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. 
4. Amikor a rendszer kéri, kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

5. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

6. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

A Kiszolgálókezelő irányítópultján csatlakozva van a virtuális géphez.

> [!IMPORTANT]
> Csak akkor folytassa az eljárást, ha a felügyelt példány sikeresen üzembe lett helyezve. Az üzembe helyezést követően kérje le a példány gazdagépnevét a felügyelt példány **Áttekintés** lapjának **Felügyelt példány** mezőjében. A név a következőhöz hasonló: **drfadfadsfd.tr23.westus1-a.worker.database.windows.net**.

## <a name="install-ssms-and-connect-to-the-managed-instance"></a>Az SSMS telepítése és csatlakozás a felügyelt példányhoz

A következő lépések azt mutatják be, hogyan töltheti le és telepítheti az SSMS-t, majd hogyan csatlakozhat a felügyelt példányhoz.

1. A Kiszolgálókezelő bal oldali paneljén kattintson a **Helyi kiszolgáló** elemre.

    ![kiszolgálókezelő tulajdonságai](./media/sql-database-managed-instance-tutorial/server-manager-properties.png)  

2. A **Tulajdonságok** panelen az Internet Explorer – Fokozott biztonsági beállítások módosításához kattintson a **Be** lehetőségre.
3. Az **Internet Explorer – Fokozott biztonsági beállítások** párbeszédpanel Rendszergazdák szakaszában kattintson a **Ki** lehetőségre, majd az **OK** gombra.

    ![internet explorer – fokozott biztonsági beállítások](./media/sql-database-managed-instance-tutorial/internet-explorer-security-configuration.png)  
4. Indítsa el az **Internet Explorert** a tálcáról.
5. Válassza az **Ajánlott biztonsági és kompatibilitási beállítások** lehetőséget, majd kattintson az **OK** gombra az Internet Explorer 11 beállításának befejezéséhez.
6. Az URL-cím mezőbe írja be a https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms címet, majd nyomja le az **Enter** billentyűt. 
7. Töltse le az SQL Server Management Studio legújabb verzióját, és amikor a rendszer kéri, kattintson a **Futtatás** gombra.
8. Amikor a rendszer kéri, a kezdéshez kattintson a **Telepítés** gombra.
9. A telepítés befejezése után kattintson a **Bezárás** gombra.
10. Nyissa meg az SSMS-t.
11. A **Connect to Server** (Kapcsolódás kiszolgálóhoz) párbeszédpanelen írja be a felügyelt példány **gazdagépnevét* a **Server name** (Kiszolgáló neve) mezőbe, válassza az **SQL Server Authentication** (SQL Server-hitelesítés) lehetőséget, adja meg felhasználónevét és jelszavát, majd kattintson a **Connect** (Csatlakozás) gombra.

    ![ssms connect](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="download-the-wide-world-importers---standard-backup-file"></a>A Wide World Importers – Standard biztonsági mentési fájl letöltése

Az alábbi lépéseket követve töltse le a Wide World Importers – Standard biztonsági mentési fájlt.

Az Internet Explorerben írja be a https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak címet az URL-cím mezőjébe, majd amikor a rendszer kéri, kattintson a **Mentés** gombra, hogy a fájlt a **Letöltések** mappába mentse.

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Azure Storage-fiók létrehozása és a biztonsági mentési fájl feltöltése

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keressen a **Tár** kifejezésre, majd kattintson a **Tárfiók** elemre a tárfiók űrlapjának megnyitásához.

   ![tárfiók](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Adja meg a kért adatokat a tárfiók űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Üzemi modell**|Erőforrásmodell||
   |**Fióktípus**|Blob Storage||
   |**Teljesítmény**|Standard vagy Prémium|Mágneses meghajtók vagy SSD-k|
   |**Replikáció**|Helyileg redundáns tárolás||
   |**Hozzáférési szint (alapértelmezett)|Ritka vagy Gyakori||
   |**Biztonságos átvitelre van szükség**|Letiltva||
   |**Előfizetés**|Az Ön előfizetése|Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket.|
   |**Erőforráscsoport**|A korábban létrehozott erőforráscsoport|| 
   |**Hely**|Az előzőleg kiválasztott hely||
   |**Virtuális hálózatok**|Letiltva||

4. Kattintson a **Create** (Létrehozás) gombra.

   ![tárfiók részletei](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. A tárfiók üzembe helyezését követően nyissa meg az új tárfiókot.
6. A **Beállítások** területen kattintson a **Közös hozzáférésű jogosultságkód** elemre, és nyissa meg a közös hozzáférésű jogosultságkód (SAS) űrlapját.

   ![sas-űrlap](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Az SAS-űrlapon módosítsa igény szerint az alapértelmezett értékeket. Láthatja, hogy a lejárati dátum/idő értéke alapértelmezés szerint csak 8 óra.
8. Kattintson az **SAS előállítása** gombra.

   ![sas-űrlap kitöltve](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Másolja és mentse az **SAS-azonosítót** és a **Blobkiszolgáló SAS URL-címét**.
10. A **Beállítások** területen kattintson a **Tárolók** lehetőségre.

    ![tárolók](./media/sql-database-managed-instance-tutorial/containers.png)

11. A biztonsági mentési fájl tárolására szolgáló tároló létrehozásához kattintson a **+ Tároló** elemre.
12. Adja meg a kért adatokat a tároló űrlapján az alábbi táblázatban szereplő információk segítségével:

    | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Nyilvános hozzáférés szintje**|Privát (nincs névtelen hozzáférés)||

    ![tároló részletei](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Kattintson az **OK** gombra.
14. A tároló létrehozása után nyissa meg a tárolót.

    ![tároló](./media/sql-database-managed-instance-tutorial/container.png)

15. Kattintson a **Tároló tulajdonságai** lehetőségre, és másolja a tároló URL-címét.

    ![tároló URL-címe](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Kattintson a **Feltöltés** gombra a **Blob feltöltése** űrlap megnyitásához.

    ![feltöltés](./media/sql-database-managed-instance-tutorial/upload.png)

17. Navigáljon a letöltési mappához, és válassza ki az **AdventureWorks2016.bak** fájlt.

    ![feltöltés](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Kattintson a **Feltöltés** gombra.
19. Csak akkor folytassa az eljárást, ha a feltöltés befejeződött.

    ![feltöltés kész](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>A Wide World Importers-adatbázis visszaállítása biztonsági mentési fájlból

Az SSMS segítségével az alábbi lépéseket követve állítsa vissza a Wide World Importers-adatbázist a felügyelt példányon a biztonsági mentési fájlból.

1. Az SSMS-ben nyisson meg egy új lekérdezési ablakot.
2. Ezután a következő szkripttel hozzon létre egy SAS-hitelesítő adatot. Adja meg a tárfiók tárolójának URL-címét és az SAS-kulcsot az ábrán látható módon.

   ```
CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
, SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![hitelesítő adat](./media/sql-database-managed-instance-tutorial/credential.png)

3. A következő szkripttel ellenőrizze az SAS-hitelesítő adat és a biztonsági másolat érvényességét. Ehhez adja meg a biztonsági mentési fájlt tartalmazó tároló URL-címét:

   ```
   RESTORE FILELISTONLY FROM URL = 
   'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/sql-database-managed-instance-tutorial/file-list.png)

4. A következő szkripttel állítsa vissza az Adventure Works 2012-adatbázist egy biztonsági mentési fájlból. Ehhez adja meg a biztonsági mentési fájlt tartalmazó tároló URL-címét:

   ```
   RESTORE DATABASE [Wide World Importers] FROM URL =
  'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![visszaállítás folyamatban](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. A visszaállítás állapotának nyomon követéséhez futtassa az alábbi lekérdezést egy új lekérdezési munkamenetben:

   ```
SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
, dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
FROM sys.dm_exec_requests r 
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![visszaállítás állapota](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Ha a visszaállítás elkészült, tekintse meg az Object Explorerben. 

    ![visszaállítás kész](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>További lépések

- A felügyelt példánnyal kapcsolatos információért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md)
- A VNetek konfigurálásával kapcsolatos további információkért tekintse meg a [felügyelt példányok VNetjének konfigurálásával kapcsolatos](sql-database-managed-instance-vnet-configuration.md) cikket.
- Az alkalmazások csatlakoztatásával kapcsolatos információkat az [alkalmazások csatlakoztatását ismertető](sql-database-managed-instance-connect-app.md) cikkben olvashat.
- Az Azure Database Migration Service migráláshoz való használatát a [felügyelt példány DMS használatával történő migrálását](../dms/tutorial-sql-server-to-managed-instance.md) bemutató oktatóanyag ismerteti.
