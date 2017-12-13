---
title: "Azure Portal: Azure Database for PostgreSQL-kiszolgáló létrehozása | Microsoft Docs"
description: "Rövid útmutató Azure Database for PostgreSQL-kiszolgáló létrehozásához és felügyeletéhez az Azure Portal felhasználói felületén."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/03/2017
ms.openlocfilehash: b78009a4b2683bb7ee881808ddbbc792d66dea6c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure Portalon

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre nagyjából öt perc alatt Azure Database for PostgreSQL-kiszolgálót az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a webböngészőjét, és lépjen a [portálra](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Az Azure-adatbázis PostgreSQL-kiszolgálóhoz [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül jön létre.

Azure Database for PostgreSQL-kiszolgáló létrehozásához kövesse az alábbi lépéseket:
1. A portál bal felső sarkában válassza az **Új** (+) gombot.

2. Válassza az **Adatbázisok** > **Azure Database for PostgreSQL** lehetőséget.

    ![Az „Azure Database for PostgreSQL” lehetőség](./media/quickstart-create-database-portal/1-create-database.png)

3. Töltse ki az új kiszolgáló adatainak űrlapját az alábbi információkkal az előző képen látható módon:

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Kiszolgálónév |*mypgserver-20170401*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Legalább 3, és legfeljebb 63 karakterből állhat.
    Előfizetés|Az Ön előfizetése|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport|*myresourcegroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Kiszolgáló-rendszergazdai bejelentkezés |*mylogin*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest,** vagy **public.** Nem kezdődhet a következővel: **pg_**.
    Jelszó |A választása szerint | Egy új jelszó a kiszolgálói rendszergazdai fiók számára. 8–128 karakter hosszúságú lehet. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Hely|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.
    PostgreSQL-verzió|A legújabb verzió| A legújabb verzió, ha nincsenek egyedi igényei.
    Tarifacsomag | **Alapszintű**, **50 számítási egység**, **50 GB** | Az új adatbázis szolgáltatás- és teljesítményszintje. Válassza a **Tarifacsomag** lehetőséget, majd az **Alapszintű** lapot. Ezután válassza a **Számítási egységek** csúszka bal oldali végét az ebben a rövid útmutatóban elérhető legkisebb mennyiség kiválasztásához. A tarifacsomag beállításának mentéséhez kattintson az **OK** gombra. További információkért tekintse meg a következő képernyőképet. 
    Rögzítés az irányítópulton | Jelölőnégyzet | Egyszerűen nyomon követheti a kiszolgálót a portál irányítópultjának első lapján.

    > [!IMPORTANT]
    > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

    ![A „Tarifacsomag” panel](./media/quickstart-create-database-portal/2-service-tier.png)

4. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. Az üzembe helyezés akár 20 percet is igénybe vehet.

5. Az eszköztáron válassza az **Értesítések** szimbólumot az üzembe helyezési folyamat megfigyeléséhez.

    ![Az „Értesítések” panel](./media/quickstart-create-database-portal/3-notifications.png)
   
  Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure Database for PostgreSQL kiszolgálószinten hoz létre egy tűzfalat. Ez megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan szabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat. 

1. Az üzembe helyezés végeztével keresse meg a kiszolgálót. Ha szükséges, használja a keresési funkciót. Például a bal oldali menüben válassza a **Minden erőforrás** elemet. Ezután írja be a kiszolgáló nevét (például: **mypgserver-20170401**) az újonnan létrehozott kiszolgáló megkereséséhez. Válassza ki a keresési eredmények listájában a kiszolgálónevet. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.
 
    ![Kiszolgálónév keresése](./media/quickstart-create-database-portal/4-locate.png)

2. A kiszolgáló lapján válassza a **Kapcsolatbiztonság** elemet.

    ![A „Kapcsolatbiztonság” beállítás](./media/quickstart-create-database-portal/5-firewall-2.png)

3. A **Tűzfalszabályok** szakasz **Szabály neve** oszlopában válassza az üres mezőt egy új tűzfalszabály létrehozásának megkezdéséhez. 

    Ebben a rövid útmutatóban engedélyezünk minden bemenő IP-címet a kiszolgálón. Adjuk meg az egyes oszlopok szövegmezőiben a következő értékeket:

    Szabály neve | Kezdő IP-cím | Záró IP-cím 
    ---|---|---
    AllowAllIps | 0.0.0.0 | 255.255.255.255

4. A **Kapcsolatbiztonság** lap felső eszköztárában kattintson a **Mentés** gombra. A folytatás előtt várjon, amíg meg nem jelenik a kapcsolatbiztonsági frissítés sikeres befejezését jelző értesítés.

    > [!NOTE]
    > A PostgreSQL-kiszolgálóhoz készült Azure-adatbázis kapcsolatai az 5432-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 5432-es portot.
    >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Az Azure Database for PostgreSQL-kiszolgáló létrehozásakor egy **postgres** nevű alapértelmezett adatbázis jön létre. Az adatbázis-kiszolgálóhoz való csatlakozáshoz a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra van szüksége. Ezeket a rövid útmutatóban korábban már feljegyezhette. Ha nem jegyezte fel, a kiszolgáló nevét és bejelentkezési adatait megtalálja a kiszolgáló **Áttekintés** lapján a portálon.

Nyissa meg kiszolgáló **Áttekintés** lapját. Jegyezze fel a **kiszolgálónevet** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. Vigye az egérmutatót az egyes mezők fölé. Ekkor a szövegtől jobbra megjelenik a másolási szimbólum. Az értékek másolásához kattintson a másolási szimbólumra.

 ![A kiszolgáló „Áttekintés” lapja](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával a Cloud Shellben

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Először használjuk a psql parancssori segédprogramot a kiszolgálóhoz való csatlakozás bemutatásához. Az itt leírtak szerint használhatja a webböngészőt és az Azure Cloud Shellt, és nem szükséges további szoftvert telepítenie. Ha a gépén helyileg telepítve van a psql segédprogram, onnan is csatlakozhat.

1. A felső navigációs panelen válassza a terminál szimbólumot a Cloud Shell megnyitásához.

   ![Azure Cloud Shell terminál szimbóluma](./media/quickstart-create-database-portal/7-cloud-console.png)

2. A Cloud Shell megnyílik a böngészőben, és lehetővé teszi Bash felületi parancsok beírását.

   ![Cloud Shell Bash promptja](./media/quickstart-create-database-portal/8-bash.png)

3. A Cloud Shell promptnál csatlakozzon az Azure Database for PostgreSQL-kiszolgálón lévő adatbázishoz a psql parancssor beírásával.

    A következő formátum használatával csatlakozhat Azure Database for PostgreSQL-kiszolgálóhoz a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Például a következő parancs egy példakiszolgálóhoz csatlakozik:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql paraméter |Ajánlott érték|Leírás
    ---|---|---
    --host | Kiszolgálónév | Az a kiszolgálónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor használt. Az itt látható példakiszolgáló a **mypgserver-20170401.postgres.database.azure.com**. Használja a teljes tartománynevet (**\*.postgres.database.azure.com**), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    --port | 5432 | Az Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáskor használt port. 
    --username | Kiszolgáló-rendszergazdai bejelentkezési név |A kiszolgáló-rendszergazdai bejelentkezési felhasználónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    --dbname | *postgres* | A rendszer által az első csatlakozáshoz létrehozott, alapértelmezett adatbázisnév. Később létrehozhatja a saját adatbázisát.

    Miután a saját paraméterértékeivel futtatta a psql parancsot, a rendszer felkéri, hogy adja meg a kiszolgáló-rendszergazdai jelszót. Ez ugyanaz az a jelszó, amelyet a kiszolgáló létrehozásakor adott meg. 

    psql paraméter |Ajánlott érték|Leírás
    ---|---|---
    jelszó | Az Ön rendszergazdai jelszava | A beírt jelszókarakterek nem jelennek meg a Bash promptban. Miután beírta az összes karaktert, nyomja le az **Enter** billentyűt a hitelesítéshez és a csatlakozáshoz.

    A csatlakozás után a psql segédprogram egy postgres promptot jelenít meg, ahová az SQL-parancsokat beírhatja. A rendszer a kezdeti kapcsolati kimeneten egy figyelmeztetést jeleníthet meg, mivel a Cloud Shell psql-jének verziója eltérhet az Azure Database for PostgreSQL-kiszolgáló verziójától. 
    
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
    > Ha a tűzfal nincs konfigurálva a Cloud Shell IP-címének engedélyezésére, a következő hiba jelenik meg:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL: SSL connection is required. Adja meg az SSL-beállításokat, és próbálkozzon újra.
    > 
    > A hiba elhárításához győződjön meg róla, hogy a kiszolgáló konfigurációja megegyezik a cikk „Kiszolgálószintű tűzfalszabály konfigurálása” részében található lépésekkel.

4. Hozzon létre egy üres adatbázist a következő parancs beírásával a parancssorba:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    A parancs teljesítése néhány percet vehet igénybe. 

5. Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
    ```bash
    \c mypgsqldb
    ```

6. A psql-ből való kilépéshez írja be a `\q` karakterláncot, majd nyomja le az **Enter** billentyűt. Miután végzett, bezárhatja a Cloud Shellt.

Ezzel csatlakozott az Azure Database for PostgreSQL-kiszolgálóhoz, és létrehozott egy üres felhasználói adatbázist. Folytassa a következő szakasszal, amelyből megtudhatja, hogyan csatlakozhat egy másik ismert eszköz, a pgAdmin segítségével.

## <a name="connect-to-the-postgresql-database-by-using-pgadmin"></a>Csatlakozás a PostgreSQL-adatbázishoz a pgAdmin használatával

Csatlakozás az Azure PostgreSQL-kiszolgálóhoz a pgAdmin GUI eszköz használatával:
1. Nyissa meg a pgAdmin alkalmazást az ügyfélszámítógépen. A pgAdmin alkalmazást a [pgAdmin webhelyről](http://www.pgadmin.org/) telepítheti.

2. Az Irányítópult lap **Gyorshivatkozások** szakaszában válassza az **Új kiszolgáló hozzáadása** szimbólumot.

3. A **Kiszolgáló létrehozása** párbeszédpanel **Általános** lapján adjon meg egy egyedi rövid nevet a kiszolgáló számára, például **Azure PostgreSQL Server**.

    ![Az „Általános” lap](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. A **Kiszolgáló létrehozása** párbeszédpanel **Kapcsolat** lapján használja a megadott beállításokat, majd válassza a **Mentés** lehetőséget.

   ![A „Kapcsolat” lap](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-paraméter |Ajánlott érték|Leírás
    ---|---|---
    Gazdagépnév/-cím | Kiszolgálónév | Az a kiszolgálónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor használt. A példakiszolgáló a **mypgserver-20170401.postgres.database.azure.com**. Használja a teljes tartománynevet (**\*.postgres.database.azure.com**), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    Port | 5432 | Az Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáskor használt port. 
    Karbantartási adatbázis | *postgres* | A rendszer által létrehozott alapértelmezett adatbázisnév.
    Felhasználónév | Kiszolgáló-rendszergazdai bejelentkezési név | A kiszolgáló-rendszergazdai bejelentkezési felhasználónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    Jelszó | Az Ön rendszergazdai jelszava | A rövid útmutatóban a korábbiakban a kiszolgáló létrehozásakor választott jelszó.
    Szerepkör | Hagyja üresen | Itt nem kell megadni szerepkörnevet. Hagyja üresen ezt a mezőt.
    SSL-mód | Kötelező | Alapértelmezés szerint a rendszer minden Azure PostgreSQL-kiszolgálót az SSL-kényszerítés bekapcsolása mellett hoz létre. Részletek az SSL-kényszerítés kikapcsolásáról: [SSL kényszerítése](./concepts-ssl-connection-security.md).
    
5. Kattintson a **Mentés** gombra.

6. A bal oldali **Böngésző** panelen bontsa ki a **Kiszolgálók** csomópontot. Válassza ki például az **Azure PostgreSQL Server** kiszolgálót. Kattintson rá a csatlakozáshoz.

7. Bontsa ki a kiszolgáló-csomópontot, majd bontsa ki az abban található **Adatbázisok** csomópontot is. A listában szerepelnie kell a meglévő *postgres* adatbázisnak, valamint az előző szakaszban esetlegesen újonnan létrehozott felhasználói adatbázisoknak (például a **mypgsqldb** adatbázisnak) is. Vegye figyelembe, hogy az Azure Database for PostgreSQL segítségével kiszolgálónként több adatbázist is létrehozhat.

8. Kattintson a jobb gombbal az **Adatbázisok** elemre, majd válassza a **Létrehozás** menü **Adatbázis** elemét.

9. Írja be a választott adatbázisnevet az **Adatbázis** mezőbe (a példánkban ez a **mypgsqldb**).

10. A listából válassza ki az adatbázis **tulajdonosát**. Válassza ki a kiszolgáló-rendszergazdai bejelentkezési nevet (a példánkban ez a **mylogin**).

11. Egy új, üres adatbázis létrehozásához válassza a **Mentés** lehetőséget.

12. A **Böngésző** panelen tekintse meg az Ön által létrehozott adatbázist az adatbázisok listájában a kiszolgáló nevét viselő területen.

    ![A „Böngésző” panel](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak egy kiszolgálói erőforrást töröljön.

> [!TIP]
> Az ebben a gyűjteményben található többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy folytatja ezen rövid útmutatók használatát, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti a rövid útmutató segítségével létrehozott erőforrásokat a portálon.

Ha az újonnan létrehozott kiszolgálót is magában foglaló teljes erőforráscsoportot törölni szeretné:
1. Keresse meg az erőforráscsoportot a portálon. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget. Válassza ki az erőforráscsoport nevét (ebben a példában **myresourcegroup**).

2. Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Írja be a törölni kívánt erőforrás nevét (ebben a példában **myresourcegroup**) a szövegmezőbe a törlés megerősítéséhez. Válassza a **Törlés** elemet.

Csak az újonnan létrehozott kiszolgáló törlése:
1. Keresse meg a kiszolgálót a portálon, ha nincs megnyitva. A bal oldali menüben válassza a **Minden erőforrás** elemet. Ezután keressen rá az Ön által létrehozott kiszolgálóra.

2. Az **Áttekintés** oldalon válassza a **Törlés** elemet.

    ![A „Törlés” gomb](./media/quickstart-create-database-portal/12-delete.png)

3. Erősítse meg a törölni kívánt kiszolgáló nevét, és tekintse meg az érintett adatbázisokat. Írja be a kiszolgáló nevét (ebben a példában **mypgserver-20170401**) a szövegmezőbe. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
