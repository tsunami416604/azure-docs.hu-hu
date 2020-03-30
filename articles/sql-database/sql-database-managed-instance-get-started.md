---
title: 'Azure Portal: Felügyelt példány létrehozása'
description: Hozzon létre egy SQL Database felügyelt példány, hálózati környezet és az ügyfél virtuális gép a hozzáféréshez.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: danimir
ms.author: danil
ms.reviewer: sstein, carlrab
ms.date: 09/26/2019
ms.openlocfilehash: 25128442cd922f6b9130586e245695b6880f661c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257614"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>Rövid útmutató: Hozzon létre egy Azure SQL Database felügyelt példányt

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure SQL Database [felügyelt példányt](sql-database-managed-instance.md) az Azure Portalon.

> [!IMPORTANT]
> A korlátozásokért lásd: [Támogatott régiók](sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott előfizetési típusok.](sql-database-managed-instance-resource-limits.md#supported-subscription-types)

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-managed-instance"></a>Felügyelt példány létrehozása

A következő lépések bemutatják, hogyan hozhat létre felügyelt példányt:

1. Válassza az **Azure SQL** az Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure **SQL** kifejezést a keresőmezőbe.
2. Válassza a **+Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása lehetőséglap** megnyitásához. Az Azure SQL Database által felügyelt példányokkal kapcsolatos további információkat a **Felügyelt példányok** csempéjének **Részletek megjelenítése** parancsára kattintva tekintheti meg.
3. Kattintson a **Létrehozás** gombra.

   ![Felügyelt példány létrehozása](./media/sql-database-managed-instance-get-started/create-managed-instance.png)

4. Az Azure SQL **Database felügyelt példányok** létrehozása kiépítési űrlap lapjaival adja hozzá a szükséges és a nem kötelező információkat. A következő szakaszok ezeket a lapokat ismertetik.

### <a name="basics"></a>Alapvető beállítások

- Töltse ki az Alapok lapon kötelezően szükséges **adatokat.** Ez a felügyelt példány kiépítéséhez szükséges minimális információkészlet.

   !["Alapok" lap felügyelt példány létrehozásához](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-basics.png)

   Az alábbi táblázat ot használja referenciaként az ezen a lapon szükséges információkért.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Előfizetés** | Az előfizetése. | Olyan előfizetés, amely engedélyt ad új erőforrások létrehozására. |
   | **Erőforráscsoport** | Új vagy meglévő erőforráscsoport.|Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Felügyelt példány neve** | Bármilyen érvényes név.|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket.|
   | **Régió** |Az a régió, amelyben létre szeretné hozni a felügyelt példányt.|A régiókról az [Azure-régiók](https://azure.microsoft.com/regions/)című témakörben talál további információt.|
   | **Felügyelt példány rendszergazdai bejelentkezési neve** | Bármely érvényes felhasználónév. | Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. Ne használja a "serveradmin" kifejezést, mert az egy fenntartott kiszolgálószintű szerepkör.|
   | **Jelszó** | Bármilyen érvényes jelszó.| A jelszónak legalább 16 karakter hosszúságúnak kell lennie, és teljesítenie kell [a meghatározott összetettségi követelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|

- Válassza **a Felügyelt példány konfigurálása** számítási és tárolási erőforrások méretezéséhez és a tarifacsomagok áttekintéséhez lehetőséget. A csúszkák vagy a szövegmezők segítségével adja meg a tárterület méretét és a virtuális magok számát. Ha végzett, válassza az **Alkalmaz** lehetőséget a kijelölés mentéséhez. 

   ![Felügyelt példány képernyő](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-configure-performance.png)

- Ha a felügyelt példány létrehozása előtt át szeretné tekinteni a beállításokat, válassza a **Véleményezés + létrehozás**lehetőséget. Vagy konfigurálja a hálózati beállításokat a **Tovább: Hálózat lehetőséget**választva.

### <a name="networking"></a>Hálózat

- Töltse ki a választható információkat a **Hálózat** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat alkalmazza.

   !["Hálózat" lap felügyelt példány létrehozásához](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-networking.png)

   Az alábbi táblázat ot használja referenciaként az ezen a lapon szükséges információkért.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Virtuális hálózat** | Válassza **az Új virtuális hálózat létrehozása** vagy egy érvényes virtuális hálózat és alhálózat lehetőséget.| Ha egy hálózat vagy alhálózat nem érhető el, [módosítani kell, hogy megfeleljen a hálózati követelményeknek,](sql-database-managed-instance-configure-vnet-subnet.md) mielőtt kijelölné az új felügyelt példány célként. A felügyelt példány hálózati környezetének konfigurálásának követelményeiről a [Felügyelt példány virtuális hálózatának konfigurálása](sql-database-managed-instance-connectivity-architecture.md)című témakörben talál. |
   | **Kapcsolat típusa** | Válasszon a proxy és az átirányítási kapcsolat típusa közül.|A kapcsolattípusokról az [Azure SQL Database kapcsolatszabályzata című témakörben](sql-database-connectivity-architecture.md#connection-policy)talál további információt.|
   | **Nyilvános végpont**  | Válassza az **Engedélyezés** lehetőséget. | Ahhoz, hogy egy felügyelt példány elérhető legyen a nyilvános adatvégponton keresztül, engedélyeznie kell ezt a beállítást. | 
   | **Hozzáférés engedélyezése** (ha a **Nyilvános végpont** engedélyezve van) | Válasszon a lehetőségek közül.   |A portálfelület lehetővé teszi egy biztonsági csoport konfigurálását nyilvános végponttal. </br> </br> A forgatókönyv alapján válasszon az alábbi lehetőségek közül: </br> <ul> <li>**Azure-szolgáltatások:** Ezt a lehetőséget javasoljuk, amikor a Power BI-ból vagy egy másik több-bérlős szolgáltatásból csatlakozik. </li> <li> **Internet**: Tesztelési célokra használható, ha gyorsan fel szeretne pörgetni egy felügyelt példányt. Éles környezetben nem javasoljuk. </li> <li> **Nincs hozzáférés**: Ez a beállítás **megtagadása** biztonsági szabályt hoz létre. Módosítsa ezt a szabályt, hogy a felügyelt példány nyilvános végponton keresztül is elérhető legyen. </li> </ul> </br> A nyilvános végpontbiztonságról további információt az [Azure SQL Database felügyelt példányának biztonságos használata nyilvános végponttal](sql-database-managed-instance-public-endpoint-securely.md)című témakörben talál.|

- Válassza **a Véleményezés + létrehozás** lehetőséget a beállítások áttekintéséhez, mielőtt létrehozna egy felügyelt példányt. Vagy konfiguráljon további egyéni beállításokat a **Tovább: További beállítások**lehetőség kiválasztásával.

### <a name="additional-settings"></a>További beállítások

- Töltse ki a választható információkat a **További beállítások** lapon. Ha kihagyja ezt az információt, a portál az alapértelmezett beállításokat alkalmazza.

   !["További beállítások" lap felügyelt példány létrehozásához](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-additional-settings.png)

   Az alábbi táblázat ot használja referenciaként az ezen a lapon szükséges információkért.

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   | **Rendezés** | Válassza ki a felügyelt példányhoz használni kívánt rendezést. Ha adatbázisokat telepít át az SQL Server kiszolgálóról, ellenőrizze a forrásrendezést az érték használatával, `SELECT SERVERPROPERTY(N'Collation')` és használja azt.| A rendezésekről a [Kiszolgáló-rendezés beállítása vagy módosítása](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation)című témakörben talál további információt.|   
   | **Időzóna** | Válassza ki a felügyelt példány által megfigyelni kívánt időzónát.|További információ: [Time Zones](sql-database-managed-instance-timezone.md).|
   | **Feladatátvételmásodlagosként való használat** | Válassza az **Igen** lehetőséget. | Engedélyezze ezt a beállítást, hogy a felügyelt példányt másodlagos feladatátvételi csoportként használja.|
   | **Elsődleges felügyelt példány** (ha **a Feladatátvétel másodlagos példánya** érték **Igen**) | Válasszon egy meglévő elsődleges felügyelt példányt, amely ugyanabban a DNS-zónában csatlakozik a létrehozott felügyelt példányhoz. | Ez a lépés lehetővé teszi a feladatátvevő csoport létrehozás utáni konfigurációját. További információt az [Oktatóanyag: SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md)című témakörben talál.|

### <a name="review--create"></a>Véleményezés + létrehozás

5. Válassza **a Véleményezés + Létrehozás** lapot a beállítások áttekintéséhez a felügyelt példány létrehozása előtt.

   ![A felügyelt példányok véleményezésének és létrehozásának lapja](./media/sql-database-managed-instance-get-started/tabs/mi-create-tab-review-create.png)

6. Válassza a **Létrehozás lehetőséget** a felügyelt példány kiépítésének megkezdéséhez.

> [!IMPORTANT]
> Felügyelt példány telepítése egy hosszú ideig futó művelet. Az első példány üzembe helyezése az alhálózatban általában sokkal tovább tart, mint a meglévő felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő, a [Felügyelt példány kezelési műveletek.](sql-database-managed-instance.md#managed-instance-management-operations)

### <a name="monitor-deployment-progress"></a>A telepítés előrehaladásának figyelése

7. A központi telepítés állapotának megtekintéséhez kattintson az **Értesítések** ikonra.

   ![Felügyelt példány telepítésének folyamata](./media/sql-database-managed-instance-get-started/in-progress/mi-create-deployment-in-progress.png)

8. Válassza a **központi telepítés folyamatban az** értesítésben a felügyelt példány ablakának megnyitásához és a központi telepítés előrehaladásának további figyeléséhez. 

> [!TIP]
> Ha bezárta a webböngészőt, vagy eltávolodott a központi telepítés folyamatának képernyőjéről, az alábbi lépésekkel keresse meg a telepítés folyamatát jelző képernyőt:
> 1. Az Azure Portalon nyissa meg az erőforráscsoportot (az **Alapok** lapon), amelyre egy felügyelt példányt telepít.
> 2. Válassza **a Központi telepítések lehetőséget.**
> 3. Válassza ki a folyamatban lévő felügyelt példány telepítési műveletét.

> [!IMPORTANT]
> Ahhoz, hogy a felügyelt példány létrehozásának állapotát le lehessen adni, **olvasási engedélyekkel** kell rendelkeznie az erőforráscsoporton keresztül. Ha nem rendelkezik ezzel az engedéllyel, vagy visszavonja azt, miközben a felügyelt példány létrehozási folyamatban van, ez azt eredményezheti, hogy a felügyelt példány nem látható az erőforráscsoport-telepítések listájában.
>

## <a name="post-deployment-operations"></a>A telepítés tanait követő műveletek

A létrehozott erőforrások áttekintéséhez finomítsa a hálózati beállításokat, és olvassa be az állomáskapcsolat részleteit (FQDN) kövesse az ebben a szakaszban ismertetett lépéseket.

### <a name="view-resources-created"></a>Létrehozott erőforrások megtekintése

A felügyelt példány sikeres üzembe helyezése kor a létrehozott erőforrások megtekintése:

1. Nyissa meg a felügyelt példány erőforráscsoportját. Tekintse meg az erőforrásokat, amelyeket az Ön számára létrehozott a [Felügyelt példány létrehozása](#create-a-managed-instance) rövid útmutató.

   ![Felügyelt példány erőforrásai](./media/sql-database-managed-instance-get-started/resources.png)

### <a name="view-and-fine-tune-network-settings"></a>Hálózati beállítások megtekintése és finomhangolása

A hálózati beállítások szükség általi finomhangolásához vizsgálja meg az alábbiakat:

1. Válassza ki az útvonaltáblát a felhasználó által definiált útvonal (UDR) áttekintéséhez.

   ![Útválasztási táblázat](./media/sql-database-managed-instance-get-started/route-table.png)

2. Az útvonaltáblában tekintse át a bejegyzéseket a felügyelt példány virtuális hálózatáról és azon belüli forgalom irányításához. Ha manuálisan hozza létre vagy konfigurálja az útvonaltáblát, győződjön meg arról, hogy ezeket a bejegyzéseket a felügyelt példányútvonal-táblában hozza létre.

   ![Felügyelt példány alhálózatának bejegyzése a helyi](./media/sql-database-managed-instance-get-started/udr.png)

3. Térjen vissza az erőforráscsoporthoz, és válassza ki a hálózati biztonsági csoportot.

   ![Hálózati biztonsági csoport](./media/sql-database-managed-instance-get-started/network-security-group.png)

4. Tekintse át a bejövő és kimenő biztonsági szabályokat. 

   ![Biztonsági szabályok](./media/sql-database-managed-instance-get-started/security-rules.png)

> [!IMPORTANT]
> Ha a felügyelt példányhoz nyilvános végpontot konfigurált, portokat kell megnyitnia, hogy a hálózati forgalom lehetővé tegye a kapcsolatok számára a felügyelt példányt a nyilvános internetről, további információt a [Nyilvános végpont konfigurálása felügyelt példányhoz](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group) című témakörben talál.
>

### <a name="retrieve-connection-details-to-managed-instance"></a>Kapcsolat részleteinek beolvasása a felügyelt példányba

A felügyelt példányhoz való csatlakozáshoz kövesse az alábbi lépéseket az állomásnév és a teljesen minősített tartománynév (FQDN) beolvasásához:

1. Térjen vissza az erőforráscsoporthoz, és válassza ki a felügyelt példányt.

   ![Felügyelt példány az erőforráscsoportban](./media/sql-database-managed-instance-get-started/managed-instance.png)

2. Az **Áttekintés** lapon keresse meg a **Host** tulajdonságot. Másolja a felügyelt példány állomásnevét a következő rövid útmutatóban való használatra.

   ![Állomásnév](./media/sql-database-managed-instance-get-started/host-name.png)

   A másolt érték egy teljesen minősített tartománynevet (FQDN) jelöl, amely a felügyelt példányhoz való csatlakozáshoz használható. Hasonló a következő címpéldához: *your_host_name.a1b2c3d4e5f6.database.windows.net*.

## <a name="next-steps"></a>További lépések

A felügyelt példányokhoz való csatlakozásról:
- Az alkalmazások csatlakozási lehetőségeinek áttekintését az [Alkalmazások csatlakoztatása felügyelt példányhoz című témakörben](sql-database-managed-instance-connect-app.md)találja.
- Egy rövid útmutató, amely bemutatja, hogyan csatlakozhat egy felügyelt példány egy Azure virtuális gépről, [lásd: Konfigurálja az Azure virtuálisgép-kapcsolat.](sql-database-managed-instance-configure-vm.md)
- A helyközötti kapcsolat konfigurálása című rövid útmutatót, amely bemutatja, hogyan lehet egy helyszíni ügyfélszámítógépről csatlakozni egy felügyelt példányhoz, a [Pont-hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md)című témakörben található.

Meglévő SQL Server-adatbázis visszaállítása a helyszíni rendszerből felügyelt példányba: 
- Az [Azure Database Migration Service használatával az áttelepítés](../dms/tutorial-sql-server-to-managed-instance.md) egy adatbázis biztonsági másolat fájlból való visszaállításhoz. 
- Az adatbázis biztonsági másolatfájljából történő visszaállításhoz használja a [T-SQL RESTORE parancsot.](sql-database-managed-instance-get-started-restore.md)

A felügyelt példányok adatbázis-teljesítményének speciális figyelése beépített hibaelhárítási intelligenciával az [Azure SQL Database figyelése az Azure SQL Analytics használatával című](../azure-monitor/insights/azure-sql.md)témakörben található.
