---
title: 'Rövid útmutató: Kiszolgáló létrehozása – Azure portal – Azure Database for PostgreSQL – Single Server'
description: Rövid útmutató az Azure Database for PostgreSQL – Single Server létrehozásához és kezeléséhez az Azure Portal felhasználói felületének használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/25/2019
ms.openlocfilehash: d93c1d81e1434ffdd41297ed54e874c6b71240d2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75430483"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Rövid útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure Portalon

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre nagyjából öt perc alatt egy Azure Database for PostgreSQL-kiszolgálót az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a webböngészőjét, és lépjen a [portálra](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Az Azure Database for PostgreSQL-kiszolgáló a [számítási és tárolási erőforrások](./concepts-pricing-tiers.md) konfigurált készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül jön létre.

Azure Database for PostgreSQL-kiszolgáló létrehozásához kövesse az alábbi lépéseket:
1. Válassza **az Erőforrás létrehozása** (+) lehetőséget a portál bal felső sarkában.

2. Válassza **az Adatbázisok** > **Azure-adatbázis a PostgreSQL számára**lehetőséget.

    ![Az "Azure Database for PostgreSQL" menü](./media/quickstart-create-database-portal/1-create-database.png)

3. Válassza az **Egykiszolgálós** telepítés lehetőséget.

   ![Válassza az Azure Database for PostgreSQL – Egykiszolgálós telepítési lehetőség kiválasztása](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Töltse ki az **Alapok** űrlapot a következő információkkal:

    ![A kiszolgáló létrehozása](./media/quickstart-create-database-portal/create-basics.png)

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport|*myResourceGroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Legalább 3, és legfeljebb 63 karakterből állhat.
    Adatforrás | *Nincs* | Válassza a *Nincs* lehetőséget, ha teljesen új kiszolgálót szeretne létrehozni. (Ha egy meglévő Azure Database for PostgreSQL-kiszolgáló georedundáns biztonsági mentéséből hoz létre kiszolgálót, válassza a *Biztonsági mentés* lehetőséget).
    Rendszergazdai felhasználónév |*myadmin*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** és **public**. Nem kezdődhet a következővel: **pg_**.
    Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Hely|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.
    Verzió|A legújabb főverzió| A legújabb PostgreSQL-főverzió, hacsak nincsenek más konkrét követelmények.
    Számítás + tárolás | **Általános célú**, **5. generációs**, **2 virtuális mag**, **5 GB**, **7 nap**, **Georedundáns** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza **a Kiszolgáló konfigurálása**lehetőséget. Ezután válassza az Általános **célú** *4 vCores* *lapot.* *100 GB* *7 days* **Compute Generation** **vCore** **Storage** **Backup Retention Period** Ezeket a csúszkákat úgy hagyhatja, ahogy van, vagy beállíthatja őket. A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Biztonsági másolat redundanciabeállításai** területen a **Georedundáns** lehetőséget. A tarifacsomag beállításának mentéséhez válassza az **OK** gombot. A következő képernyőkép ezeket a beállításokat tartalmazza.

   > [!NOTE]
   > Fontolja meg az alapszintű tarifacsomag használatát, ha a könnyű számítási és I/O megfelelő a számítási feladatokhoz. Vegye figyelembe, hogy az alapszintű tarifacsomagban létrehozott kiszolgálók at később nem lehet általános célra vagy memóriaoptimalizáltra méretezni. További információt az [árképzési oldalon](https://azure.microsoft.com/pricing/details/postgresql/) talál.
   > 

    ![A „Tarifacsomag” panel](./media/quickstart-create-database-portal/2-pricing-tier.png)

5. A beállítások áttekintéséhez válassza a **Véleményezés + létrehozás** lehetőséget. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.

6. Az eszköztáron válassza az **Értesítések** ikont (csengő) az üzembehelyezési folyamat megfigyeléséhez. Az üzembe helyezés után kiválaszthatja a **Rögzítés az irányítópulton** lehetőséget, amellyel létrehoz egy csempét a kiszolgálóhoz az Azure Portal irányítópultján. A csempe a kiszolgáló **Áttekintés** oldalának közvetlen megnyitására szolgál. Az **Erőforrás megnyitása** lehetőség kiválasztásával megnyitja a kiszolgáló **Áttekintés** oldalát.

    ![Az „Értesítések” panel](./media/quickstart-create-database-portal/3-notifications.png)
   
   Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. (A másik alapértelmezett adatbázis az **azure_maintenance**. Az a feladata, hogy elkülönítse a felügyelt szolgáltatások folyamatait a felhasználói műveletektől. Ehhez az adatbázishoz nem lehet hozzáférni.)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure Database for PostgreSQL kiszolgálószinten hoz létre egy tűzfalat. Ez megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan szabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat. 

1. Az üzembe helyezés végeztével keresse meg a kiszolgálót. Ha szükséges, használja a keresési funkciót. Például a bal oldali menüben válassza a **Minden erőforrás** elemet. Adja meg a kiszolgáló nevét, például a példát, a **mydemoserver**t az újonnan létrehozott kiszolgáló kereséséhez. Válassza ki a keresési eredmények listájában a kiszolgálónevet. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.
 
    ![Kiszolgálónév keresése](./media/quickstart-create-database-portal/4-locate.png)

2. A kiszolgáló lapján válassza a **Kapcsolatbiztonság** elemet.

3. A **Tűzfalszabályok** szakasz **Szabály neve** oszlopában válassza az üres mezőt egy új tűzfalszabály létrehozásának megkezdéséhez. 

   Adjon meg egy nevet és a kiszolgálóhoz hozzáférni kívánó ügyfelek IP-címtartományának elejét és végét a szövegmezőkben. Ha ez egyetlen IP-cím, használja ugyanazt az értéket a kezdő és a befejező IP-címnek.

   ![Tűzfalszabályok beállítása](./media/quickstart-create-database-portal/5-firewall-2.png)
     

4. A **Kapcsolatbiztonság** lap felső eszköztárában kattintson a **Mentés** gombra. A folytatás előtt várjon, amíg meg nem jelenik a kapcsolatbiztonsági frissítés sikeres befejezését jelző értesítés.

    > [!NOTE]
    > A PostgreSQL-kiszolgálóhoz készült Azure-adatbázis kapcsolatai az 5432-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 5432-es portot.
    >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Az Azure Database for PostgreSQL-kiszolgáló létrehozásakor egy **postgres** nevű alapértelmezett adatbázis jön létre. Az adatbázis-kiszolgálóhoz való csatlakozáshoz a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra van szüksége. Ezeket a rövid útmutatóban korábban már feljegyezhette. Ha nem jegyezte fel, a kiszolgáló nevét és bejelentkezési adatait megtalálja a kiszolgáló **Áttekintés** lapján a portálon.

Nyissa meg kiszolgáló **Áttekintés** lapját. Jegyezze fel a **kiszolgálónevet** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. Vigye az egérmutatót az egyes mezők fölé. Ekkor a szövegtől jobbra megjelenik a másolási szimbólum. Az értékek másolásához kattintson a másolási szimbólumra.

 ![A kiszolgáló „Áttekintés” lapja](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-using-psql"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) helyi példányát az Azure PostgreSQL-kiszolgálóhoz való csatalakozáshoz. Használjuk a psql parancssori segédprogramot az Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql parancsot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz
   ```bash
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Ha a Postgres-hez való csatlakozáshoz URL-elérési utat szeretne használni, `%40`az URL-cím kódolja a @ jelet a felhasználónévben a segítségével. Például a psql kapcsolati karakterlánca a 
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

   A csatlakozás után a psql segédprogram egy postgres promptot jelenít meg, ahová az SQL-parancsokat beírhatja. A rendszer a kezdeti kapcsolati kimenetben egy figyelmeztetést jeleníthet meg, mivel a psql használt verziója eltérhet az Azure Database for PostgreSQL-kiszolgáló verziójától. 

   Példa psql kimenetre:
   ```bash
   psql (9.5.7, server 9.6.2)
   WARNING: psql major version 9.5, server major version 9.6.
    Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=> 
   ```

   > [!TIP]
   > Ha a tűzfal nincs konfigurálva az ügyfél IP-címének engedélyezésére, a következő hiba jelenik meg:
   > 
   > "psql: FATAL: no pg_hba.conf `<IP address>`entry for host , user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Adja meg az SSL-beállításokat, és próbálkozzon újra.
   > 
   > Ellenőrizze, hogy az ügyfél IP-címe engedélyezett-e a tűzfalszabályok ban.

2. Hozzon létre egy mypgsqldb nevű üres adatbázist. Ehhez írja be a következő parancsot a parancssorba:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
    ```bash
    \c mypgsqldb
    ```

4. A psql-ből való kilépéshez írja be a `\q` parancsot, majd nyomja le az Enter billentyűt. 

Ezzel csatlakozott az Azure Database for PostgreSQL-kiszolgálóhoz a psql-en keresztül, és létrehozott egy üres felhasználói adatbázist. Folytassa a következő szakasszal, amelyből megtudhatja, hogyan csatlakozhat egy másik ismert eszköz, a pgAdmin segítségével.

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Csatlakozás a PostgreSQL-kiszolgálóhoz a pgAdmin segítségével

A pgAdmin egy nyílt forráskódú eszköz, amely a PostgreSQL-lel együtt használható. A pgAdmin alkalmazást a [pgAdmin webhelyről](https://www.pgadmin.org/) telepítheti. Elképzelhető, hogy az Ön által használt pgAdmin-verzió eltér a jelen gyors útmutatóban használttól. Ha további információra van szüksége, tekintse meg a pgAdmin dokumentációját.

1. Nyissa meg a pgAdmin alkalmazást az ügyfélszámítógépen.

2. Az eszköztáron kattintson az **Objektum** elemre, vigye az egérmutatót a **Létrehozás** fölé, majd válassza a **Kiszolgáló** lehetőséget.

3. A **Létrehozás – Kiszolgáló** párbeszédpanel **Általános** lapján adjon meg egy egyedi rövid nevet a kiszolgáló számára, például **mydemoserver**.

    ![Az „Általános” lap](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. A **Létrehozás – Kiszolgáló** párbeszédpanel **Kapcsolat** lapján töltse ki a beállítások tábláját.

   ![A „Kapcsolat” lap](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-paraméter |Érték|Leírás
    ---|---|---
    Gazdagépnév/-cím | Kiszolgálónév | Az a kiszolgálónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor használt. A példakiszolgáló a **mydemoserver.postgres.database.azure.com.** Használja a teljesen minősített tartománynevet (**\*.postgres.database.azure.com**), amint az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    Port | 5432 | Az Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáskor használt port. 
    Karbantartási adatbázis | *postgres* | A rendszer által létrehozott alapértelmezett adatbázisnév.
    Felhasználónév | Kiszolgáló-rendszergazdai bejelentkezési név | A kiszolgáló-rendszergazdai bejelentkezési felhasználónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *\@felhasználónév servername*.
    Jelszó | Az Ön rendszergazdai jelszava | A rövid útmutatóban a korábbiakban a kiszolgáló létrehozásakor választott jelszó.
    Szerepkör | Hagyja üresen | Itt nem kell megadni szerepkörnevet. Hagyja üresen ezt a mezőt.
    SSL-mód | *Kötelező* | Az SSL módot a pgAdmin SSL lapján állíthatja be. Alapértelmezés szerint a PostgreSQL-kiszolgálók hozlétre az összes Azure Database for PostgreSQL-kiszolgálók ssl kényszerítés bekapcsolva. Az SSL-kényszerítés kikapcsolása: [SSL-kényszerítés](./concepts-ssl-connection-security.md).
    
5. Kattintson a **Mentés** gombra.

6. A bal oldali **Böngésző** panelen bontsa ki a **Kiszolgálók** csomópontot. Válassza ki a szerver, **például, mydemoserver**, csatlakozni hozzá.

7. Bontsa ki a kiszolgáló-csomópontot, majd bontsa ki az abban található **Adatbázisok** csomópontot is. A listában szerepelnie kell a meglévő *postgres* adatbázisnak, valamint minden más létrehozott adatbázisnak. Az Azure Database for PostgreSQL segítségével kiszolgálónként több adatbázist is létrehozhat.

8. Kattintson a jobb gombbal **az Adatbázisok** **menüre,** válassza a Létrehozás menüt, majd válassza **az Adatbázis parancsot.**

9. Írja be a választott adatbázisnevet az **Adatbázis** mezőbe, például **az mypgsqldb2**mezőbe.

10. A listából válassza ki az adatbázis **tulajdonosát**. Válassza ki a szerver admin bejelentkezési nevét, mint például a példa, **az én admin**.

    ![Adatbázis létrehozása a pgAdmin alkalmazásban](./media/quickstart-create-database-portal/11-pgadmin-database.png)

11. Egy új, üres adatbázis létrehozásához válassza a **Mentés** lehetőséget.

12. A **Böngésző** panelen megtekintheti az Ön által létrehozott adatbázist az adatbázisok listájában a kiszolgáló nevét viselő területen.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak a kiszolgálói erőforrást törölje.

> [!TIP]
> Az ebben a gyűjteményben található többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy folytatja ezen rövid útmutatók használatát, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutató segítségével létrehozott erőforrásokat a portálon.

Ha az újonnan létrehozott kiszolgálót is magában foglaló teljes erőforráscsoportot törölni szeretné:
1. Keresse meg az erőforráscsoportot a portálon. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget. Válassza ki az erőforráscsoport nevét (ebben a példában **myresourcegroup**).

2. Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Írja be az erőforráscsoport nevét, például a példát, a **myresourcegroup**ot a szövegmezőbe a törlés megerősítéséhez. Válassza a **Törlés** elemet.

Csak az újonnan létrehozott kiszolgáló törlése:
1. Keresse meg a kiszolgálót a portálon, ha nincs megnyitva. A bal oldali menüben válassza a **Minden erőforrás** elemet. Ezután keressen rá az Ön által létrehozott kiszolgálóra.

2. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

    ![A „Törlés” gomb](./media/quickstart-create-database-portal/12-delete.png)

3. Erősítse meg a törölni kívánt kiszolgáló nevét, és tekintse meg az érintett adatbázisokat. Írja be a kiszolgáló nevét a szövegmezőbe, például a példa, **mydemoserver**. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
