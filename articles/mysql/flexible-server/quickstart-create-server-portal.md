---
title: 'Gyors útmutató: Azure-adatbázis létrehozása a MySQL-hez rugalmas kiszolgáló – Azure Portal'
description: Ez a cikk végigvezeti a Azure Portal használatával, hogy gyorsan hozzon létre egy Azure Database for MySQL rugalmas kiszolgálót néhány perc alatt.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/29/2020
ms.openlocfilehash: 70f2cf183a9bd93b6066516cb68e99ee21cdc1ac
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91569631"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Gyors útmutató: Azure Database for MySQL rugalmas kiszolgáló létrehozásához használja a Azure Portal

Azure Database for MySQL rugalmas kiszolgáló felügyelt szolgáltatás, amellyel a felhőben futtathatja, kezelheti és méretezheti a magasan elérhető MySQL-kiszolgálókat. Ez a rövid útmutató bemutatja, hogyan hozhat létre rugalmas kiszolgálót többek között a Azure Portal használatával.

> [!IMPORTANT] 
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a webböngészőjét, és keresse fel az [Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL rugalmas kiszolgáló létrehozása

Hozzon létre egy rugalmas kiszolgálót a [számítási és tárolási erőforrások](./concepts-compute-storage.md)egy meghatározott készletével. A kiszolgálót egy [Azure-erőforráscsoporton](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) belül hozza létre.

Egy rugalmas kiszolgáló létrehozásához kövesse az alábbi lépéseket:

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for MySQL**elemet. Megadhatja a **MySQL** kifejezést is a keresőmezőbe a szolgáltatás megtalálásához.

    > :::image type="content" source="./media/quickstart-create-server-portal/navigate-to-mysql.png" alt-text="Az Azure Database for MySQL menüpont":::

3. Központi telepítési lehetőségként válassza a **rugalmas kiszolgáló** lehetőséget.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Az Azure Database for MySQL menüpont":::    

4. Töltse ki az **alapok** űrlapot a következő információkkal: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Az Azure Database for MySQL menüpont"::: 
                                    
    |**Beállítás**|**Javasolt érték**|**Leírás**|
    |---|---|---|
    Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amelyben az erőforrásért díjat kell fizetnie.|
    Erőforráscsoport|*myresourcegroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.|
    Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely a rugalmas kiszolgálót azonosítja. A rendszer hozzáfűzi a *MySQL.database.Azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Legalább 3, és legfeljebb 63 karakterből állhat.|
    Rendszergazdai felhasználónév |*mydemouser*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **Admin**, **Administrator**, **root**, **Guest**vagy **Public**.|
    Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).|
    Region|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.|
    Verzió|5.7| MySQL főverzió.|
    Számítás + tárolás | **Feltört**, **Standard_B1ms**, **10 GIB**, **7 nap** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása**lehetőséget. A **számítási szint**, a **számítási méret**, a **tárolás**és a **biztonsági másolatok megőrzési időszaka**alapértelmezett értékei a következők: *feltört*, *Standard_B1ms*, *10 GIB*és *7 nap* . Ezeket a csúszkákat meghagyhatja, vagy módosíthatja is azokat. A számítás és a tároló kiválasztásának mentéséhez válassza a **Mentés** lehetőséget a konfigurációk folytatásához. Az alábbi képernyőképen a számítási és tárolási lehetőségek láthatók.|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Az Azure Database for MySQL menüpont":::

5. Hálózati beállítások konfigurálása

    A hálózatkezelés lapon megadhatja, hogyan legyen elérhető a kiszolgáló. Azure Database for MySQL rugalmas kiszolgáló két lehetőséget kínál a kiszolgálóhoz való csatlakozásra *nyilvános hozzáférés (engedélyezett IP-címek)* és *privát hozzáférés (VNet-integráció)* használatával. A *nyilvános hozzáférés (engedélyezett IP-címek)* esetében a kiszolgálóhoz való hozzáférés a tűzfalszabály számára engedélyezett IP-címekre korlátozódik. Megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan szabályt hoz létre, amely egy adott IP-cím vagy tartomány számára megnyitja a tűzfalat. A *privát hozzáféréssel (VNet-integrációval)* a kiszolgálóhoz való hozzáférés a virtuális hálózatra korlátozódik. Ebben a rövid útmutatóban bemutatjuk, hogyan engedélyezheti a nyilvános hozzáférést a kiszolgálóhoz való kapcsolódáshoz. További információ a kapcsolati módszerekről a [fogalmakat ismertető cikkben](./concepts-networking.md).

    > [!NOTE]
    > A kapcsolódási módszer nem módosítható a kiszolgáló létrehozása után. Ha például a létrehozás során a *nyilvános hozzáférés (engedélyezett IP-címek)* lehetőséget választotta, akkor a létrehozás után nem lehet *privát hozzáférésre váltani (VNet-integráció)* . Javasoljuk, hogy hozzon létre egy olyan kiszolgálót, amely privát hozzáféréssel rendelkezik a kiszolgáló biztonságos eléréséhez a VNet-integráció használatával. További információ a privát hozzáférésről a [fogalmakat ismertető cikkben](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Az Azure Database for MySQL menüpont":::  

6. A rugalmas kiszolgáló konfigurációjának áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.

7. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A kiépítés néhány percet is igénybe vehet.

8. Az eszköztáron válassza az **Értesítések** elemet (a harang ikont) az üzembehelyezési folyamat nyomon követéséhez. Az üzembe helyezés után kiválaszthatja a **rögzítés az irányítópulton**lehetőséget, amely létrehoz egy csempét a Azure Portal irányítópultján a kiszolgáló **Áttekintés** lapjára mutató hivatkozásként. Az **Erőforrás megnyitása** lehetőség kiválasztásával megnyitja a kiszolgáló **Áttekintés** oldalát.

Alapértelmezés szerint a következő adatbázisok jönnek létre a kiszolgáló alatt: **information_schema**, **mysql**, **performance_schema** és **sys**.

> [!NOTE]
> Ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 3306-as porton keresztül, amelyet a Azure Database for MySQL rugalmas kiszolgáló használ a kapcsolódási problémák elkerüléséhez.  

## <a name="connect-to-using-mysql-command-line-client"></a>Kapcsolódás a MySQL parancssori ügyfél használatával

Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet.

Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára.

Az [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy a [MySQL Workbench](./connect-workbench.md) közül választhat, ha a helyi környezetből kíván csatlakozni a kiszolgálóhoz. 

A mysql.exe használatával kapcsolódjon az alábbi paranccsal. Cserélje le az értékeket a tényleges kiszolgáló nevére és jelszavára. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Sikeresen létrehozott egy Azure Database for MySQL rugalmas kiszolgálót egy erőforráscsoporthoz.  Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport törlésével vagy csak a MySQL-kiszolgáló törlésével. Az erőforráscsoport törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok**elemet.
1. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.
1. Az erőforráscsoport áttekintés lapján válassza az **erőforráscsoport törlése**elemet.
1. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

A kiszolgáló törléséhez kattintson a kiszolgáló **Áttekintés** lapján található **delete (Törlés** ) gombra az alábbi ábrán látható módon:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Az Azure Database for MySQL menüpont":::

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [PHP-alapú (Laravel-) Webalkalmazás létrehozása MySQL-sel](tutorial-php-database-app.md)
