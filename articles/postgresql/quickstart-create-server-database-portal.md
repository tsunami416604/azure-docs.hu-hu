---
title: "Azure Portal: Azure-adatbázis létrehozása PostgreSQL-hez | Microsoft Docs"
description: "Rövid útmutató az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához és kezeléséhez az Azure Portal felhasználói felület használatával."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 06/19/2017
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 2b81b9e1ae2abfd332bd6048b7643b4245ca374b
ms.contentlocale: hu-hu
ms.lasthandoff: 07/13/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Azure-adatbázis létrehozása PostgreSQL-hez az Azure Portalon

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist PostgreSQL-kiszolgálóhoz az Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Azure-adatbázis létrehozása PostgreSQL-hez

Az Azure-adatbázis PostgreSQL-kiszolgálóhoz [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül jön létre.

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1.  Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
2.  Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
 ![Azure-adatbázis PostgreSQL-hez - Az adatbázis létrehozása](./media/quickstart-create-database-portal/1-create-database.png)

3.  Töltse ki az új kiszolgáló adatai űrlapot a következő információkkal az előző képen látható módon:
    - A kiszolgáló neve: **mypgserver-20170401** (a kiszolgáló neve leképez a DNS-névbe, ezért globálisan egyedinek kell lennie) 
    - Feliratkozás: Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van.
    - Erőforráscsoport: **myresourcegroup**
    - Az Ön által választott kiszolgálói rendszergazdai bejelentkezési név és jelszó
    - Hely
    - PostgreSQL-verzió

  > [!IMPORTANT]
  > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

4.  Kattintson a **Tarifacsomag** parancsra az új adatbázis szolgáltatás- és teljesítményszintjének megadásához. Ehhez a rövid útmutatóhoz válassza az **Alapszintű** szolgáltatásszintet, **50 Számítási egységet**, és a szolgáltatási keretbe foglalt **50 GB** tárterületet.
 ![Azure-adatbázis PostgreSQL-hez - válassza ki a szolgáltatásszintet](./media/quickstart-create-database-portal/2-service-tier.png)
5.  Kattintson az **OK** gombra.
6.  A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig.

  > [!TIP]
  > A **Rögzítés az irányítópulton** lehetőséggel egyszerűen nyomon követheti az üzembe helyezést.

7.  Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
 ![Azure-adatbázis PostgreSQL-hez - Értesítések megtekintése](./media/quickstart-create-database-portal/3-notifications.png)
   
  Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure-adatbázis PostgreSQL-hez szolgáltatás a kiszolgáló szintjén hoz létre tűzfalat. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan tűzfalszabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat. 

1.  Miután befejeződött a telepítés, kattintson az **Összes erőforrás** lehetőségre a bal oldali menüben, és írja be az újonnan létrehozott kiszolgáló nevét **mypgserver-20170401**. Kattintson a keresési eredményekben listázott kiszolgálónévre. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.
 
 ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló keresés ](./media/quickstart-create-database-portal/4-locate.png)

2.  A kiszolgáló panelen válassza a **Kapcsolatbiztonság** elemet. 
3.  Kattintson a **Szabálynév** alatti szövegmezőbe, és adjon hozzá egy új tűzfalszabályt az IP-címtartomány összekapcsolhatóságának engedélyezéséhez. Ebben a rövid útmutatóban engedélyezzünk minden IP-címet, írja be a következőket: **Rule Name = AllowAllIps**, **Start IP = 0.0.0.0** és **End IP = 255.255.255.255**, majd kattintson a **Mentés** gombra. Beállíthat egy olyan tűzfalszabályt, amely lefed egy IP-címtartományt, annak érdekében, hogy csatlakozni tudjon a saját hálózatából.

 ![Azure-adatbázis PostgreSQL-hez - Tűzfalszabály létrehozása](./media/quickstart-create-database-portal/5-firewall-2.png)

4.  Kattintson a **Mentés** gombra, majd kattintson az **X-re** a **Kapcsolatbiztonság** oldal bezárásához.

  > [!NOTE]
  > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben addig nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, amíg az informatikai részleg nem nyitja meg az 5432-es portot.
  >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásakor, az alapértelmezett **postgres** adatbázis is létrejön. A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

1. Az Azure Portal baloldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá az újonnan létrehozott kiszolgálóra **mypgserver-20170401**.

  ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló keresés ](./media/quickstart-create-database-portal/4-locate.png)

2. Kattintson a **mypgserver-20170401** kiszolgálónévre.
3. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.

 ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló-rendszergazdai bejelentkezés](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával a Cloud Shell-ben

Használjuk a psql parancssori segédprogramot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz. 
1. Indítsa el az Azure Cloud Shell-t a felső navigációs ablakban található terminálikonnal.

   ![Azure-adatbázis PostgreSQL-hez - Azure Cloud Shell terminálikon](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Az Azure Cloud Shell megnyílik a böngészőben, amely lehetővé teszi a bash-parancsok beírását.

   ![Azure-adatbázis PostgreSQL-hez - Azure Shell Bash parancssor](./media/quickstart-create-database-portal/8-bash.png)

3. A Cloud Shell parancssornál csatlakozzon az Azure-adatbázis PostgreSQL-kiszolgálóhoz. Ehhez írja be a psql parancsot a parancssorba. A következő formátum segítségével kapcsolódhat egy [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal rendelkező Azure-adatbázis PostgreSQL-kiszolgálóhoz:
   ```bash
   psql --host=<myserver> --port=<port> --username=<server admin login> --dbname=<database name>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a PostgreSQL-kiszolgálón **mypgserver-20170401.postgres.database.azure.com** a hozzáférési hitelesítő adatok használatával. A csatlakozáskor mindig használja az **5432-es** portot. Adja meg a kiszolgáló-rendszergazdai jelszavát, amikor a rendszer kéri. Tegyen szóközöket a parancsban a --kapcsolók közé az itt látható módon, de ne használjon szóközöket az egyenlőségjelek és a paraméterértékek között.

   ```bash
   psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
   ```
4.  Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer kéri.
```bash
CREATE DATABASE mypgsqldb;
```

5.  Amikor a rendszer kéri, hajtsa végre a következő parancsot, a kapcsolat átváltásához az újonnan létrehozott adatbázisra **mypgsqldb**.
```bash
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Csatlakozás a PostgreSQL-adatbázishoz a pgAdmin használatával

Kapcsolódás az Azure PostgreSQL-kiszolgálóhoz a _pgAdmin_ GUI-eszköz használatával
1.  Indítsa el a MySQL _pgAdmin_ alkalmazást az ügyfélszámítógépen. A _pgAdmin-t_ http://www.pgadmin.org/ oldalról telepítheti.
2.  Válassza az **Új kiszolgáló hozzáadása** lehetőséget a **Gyorshivatkozások** menüből.
3.  A **Kiszolgáló létrehozása** párbeszédpanel **Általános** lapján adjon egy egyedi rövid nevet a kiszolgálónak, például **Azure PostgreSQL Server**.
![pgAdmin tool - create - server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  A **Kiszolgáló létrehozása** párbeszédpanel **Kapcsolat** lapján használja a beállításokat, ahogyan meg vannak adva, majd kattintson a **Mentés** gombra.
   ![pgAdmin - Create - Server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Állomás neve/címe**: mypgserver-20170401.postgres.database.azure.com 
        - A teljes kiszolgálónév.
    - **Port:**  5432
        - Az adatbázis-kiszolgáló az 5432-es portszámot használja.
    - **Karbantartási adatbázis**: postgres 
        - A rendszer által létrehozott alapértelmezett adatbázisnév.
    - **Felhasználónév:** mylogin@mypgserver-20170401 
        - A rövid útmutató során korábban beszerzett Kiszolgálói rendszergazdai bejelentkezési név (user@mypgserver).
    - **Jelszó**: A kiszolgáló létrehozásakor választott jelszó.
    - **SSL-mód**: Kötelező
        - Alapértelmezés szerint a rendszer minden Azure PostgreSQL-kiszolgálót az SSL-kényszerítéssel bekapcsolva hoz létre. Részletek az SSL-kényszerítés kikapcsolásáról: [SSL kényszerítése](./concepts-ssl-connection-security.md).
5.  Kattintson a **Save** (Mentés) gombra.
6.  A baloldali Böngésző panelen bontsa ki a **Kiszolgáló csoportok** elemet. Válassza ki az **Azure PostgreSQL Server** kiszolgálót.
7.  Válassza ki a **Kiszolgálót**, amelyhez csatlakozott, majd azon belül válassza az **Adatbázisok** elemet. 
8.  Az adatbázis létrehozásához kattintson jobb gombbal az **Adatbázisok** elemre.
9.  Válasszon egy adatbázisnevet (**mypgsqldb**) és a tulajdonosát kiszolgálói rendszergazdai bejelentkezéssel (**mylogin**).
10. Az üres adatbázis létrehozásához kattintson a **Mentés** elemre.
11. A **Böngészőben** bontsa ki a **Kiszolgáló** elemet. Bontsa ki a létrehozott kiszolgálót, ekkor megjelenik a **mypgsqldb** adatbázis.
 ![pgAdmin - Create - Database](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Távolítsa el a rövid útmutató során létrehozott összes erőforrást az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) törlésével.

> [!TIP]
> Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1.  Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myresourcegroup** elemre.
2.  Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myresourcegroup** szöveget a szövegmezőbe, majd kattintson a Törlés gombra.

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni:
1.  Az Azure Portal bal oldali menüjében kattintson a PostgreSQL-kiszolgálók elemre, és keressen rá az imént létrehozott kiszolgálóra
2.  Az Áttekintés lap felső paneljén kattintson a Törlés gombra![Azure-adatbázis PostgreSQL-hez - Kiszolgáló törlése](./media/quickstart-create-database-portal/12-delete.png)
3.  Erősítse meg a törölni kívánt kiszolgáló nevét, és jelenítse meg az érintett adatbázisokat. Írja be a **mypgserver-20170401** szöveget a szövegmezőbe, és kattintson a Törlés gombra.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

