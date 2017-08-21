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
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 8bba5a97fdc4bbc15fe996ee68daf5b796d1bfac
ms.contentlocale: hu-hu
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Azure-adatbázis létrehozása PostgreSQL-hez az Azure Portalon

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre nagyjából öt perc alatt egy Azure-adatbázist PostgreSQL-kiszolgálóhoz az Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.
Nyissa meg a webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-postgresql"></a>Azure-adatbázis létrehozása PostgreSQL-hez

Az Azure-adatbázis PostgreSQL-kiszolgálóhoz [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül jön létre.

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1.  Kattintson az Azure Portal bal felső sarkában található **Új** gombra (+).
2.  Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
 ![Azure-adatbázis PostgreSQL-hez - Az adatbázis létrehozása](./media/quickstart-create-database-portal/1-create-database.png)

3.  Töltse ki az új kiszolgáló adatai űrlapot a következő információkkal az előző képen látható módon:

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Kiszolgálónév |*mypgserver-20170401*|Válasszon egy egyedi nevet, amely azonosítja a PostgreSQL-kiszolgálóhoz készült Azure-adatbázist. A rendszer hozzáfűzi a *postgres.database.azure.com* tartománynevet ahhoz a kiszolgálónévhez, amelyet az alkalmazások számára megad a csatlakozáshoz. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, és 3–63 karakter hosszú lehet.
    Előfizetés|*Az Ön előfizetése*|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás terhelve van.
    Erőforráscsoport|*myresourcegroup*| Meghatározhat egy új erőforráscsoport-nevet, vagy használhat egy meglévőt az előfizetéséből.
    Kiszolgáló-rendszergazdai bejelentkezés |*mylogin*| Hozzon létre saját bejelentkezési fiókot a kiszolgálóhoz való csatlakozáshoz. A rendszergazdai bejelentkezési név nem lehet „azure_superuser”, „azure_pg_admin”, „admin”, „administrator”, „root”, „guest” vagy „public”, és nem kezdődhet a „pg_” előtaggal.
    Jelszó |*A választása szerint* | Hozzon létre egy új jelszót a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakter hosszúságú lehet. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Hely|*A felhasználókhoz legközelebb eső régió*| Válassza ki a felhasználókhoz legközelebb eső helyet.
    PostgreSQL-verzió|*Válassza a legújabb verziót*| Válassza a legújabb verziót, ha nincsenek egyedi igényei.
    Tarifacsomag | **Alapszintű**, **50 számítási egység** **50 GB** | Kattintson a **Tarifacsomag** parancsra az új adatbázis szolgáltatás- és teljesítményszintjének megadásához. Válassza a felső fülön az Alapszintű tarifacsomagot. Kattintson a Számítási egységek csúszka bal oldali végére az ebben a rövid útmutatóban elérhető legkisebb mennyiség kiválasztásához. Kattintson az **OK** gombra a tarifacsomag-beállítás mentéséhez. Tekintse meg a következő képernyőképet.
    | Rögzítés az irányítópulton | Jelölőnégyzet | A **Rögzítés az irányítópulton** lehetőség kiválasztásával egyszerűen nyomon követheti a kiszolgálót az Azure Portal irányítópultjának első lapján.

  > [!IMPORTANT]
  > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

    ![PostgreSQL-hez készült Azure-adatbázis – tarifacsomag kiválasztása](./media/quickstart-create-database-portal/2-service-tier.png)

4.  A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig, de legfeljebb 20 percet vesz igénybe.

5.  Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
 ![Azure-adatbázis PostgreSQL-hez - Értesítések megtekintése](./media/quickstart-create-database-portal/3-notifications.png)
   
  Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure-adatbázis PostgreSQL-hez szolgáltatás a kiszolgáló szintjén hoz létre tűzfalat. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan tűzfalszabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat. 

1.  Az üzembe helyezés befejeződése után keresse meg a kiszolgálót. Ha szükséges, használja a keresési funkciót. Például kattintson a **Minden erőforrás** lehetőségre a bal oldali menüben, és írja be a kiszolgáló nevét (például: *mypgserver-20170401*) az újonnan létrehozott kiszolgáló megkereséséhez. Kattintson a kiszolgálónévre a találatok listájában. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.
 
    ![PostgreSQL-hez készült Azure-adatbázis – Kiszolgálónév keresése](./media/quickstart-create-database-portal/4-locate.png)

2.  A kiszolgáló lapján válassza a **Kapcsolatbiztonság** elemet. 
    ![PostgreSQL-hez készült Azure-adatbázis – Tűzfalszabály létrehozása](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  A **Tűzfalszabályok** szakaszban kattintson az üres mezőbe a **Szabály neve** oszlopban új tűzfalszabály létrehozásának megkezdéséhez. 

    Ebben a rövid útmutatóban engedélyezzünk minden bemenő IP-címet a kiszolgálón. Ehhez adjuk meg az egyes oszlopokban a következő értékeket:

    Szabály neve | Kezdő IP-cím | Záró IP-cím 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. A Kapcsolatbiztonság lap felső eszköztárában kattintson a **Mentés** gombra. Várjon néhány pillanatot a Kapcsolatbiztonság frissítésének sikeres befejezését jelző értesítés megjelenéséig, mielőtt folytatná a műveletet.

    > [!NOTE]
    > A PostgreSQL-kiszolgálóhoz készült Azure-adatbázis kapcsolatai az 5432-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja az 5432-es portot.
    >

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A PostgreSQL-kiszolgálóhoz készült Azure-adatbázis létrehozásakor egy **postgres** nevű alapértelmezett adatbázis jön létre. Az adatbázis-kiszolgálóhoz való csatlakozáshoz meg kell jegyeznie a teljes kiszolgálónevet és a rendszergazdai bejelentkezési hitelesítő adatokat. Ezeket a rövid útmutatóban korábban már feljegyezhette. Ha nem jegyezte fel, a kiszolgáló nevét és bejelentkezési adatait megtalálhatja a kiszolgáló Áttekintés lapján az Azure Portalon.

1. Nyissa meg kiszolgáló **Áttekintés** lapját. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
    Helyezze az egérmutatót az egyes mezők fölé. Ekkor a szövegtől jobbra megjelenik a másolási ikon. Az értékek másolásához kattintson igény szerint a másolási ikonra.

 ![Azure-adatbázis PostgreSQL-hez - Kiszolgáló-rendszergazdai bejelentkezés](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával a Cloud Shell-ben

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Először használjuk a psql parancssori segédprogramot a kiszolgálóhoz való csatlakozás bemutatásához.  Az itt leírtak szerint használhatja a webböngészőt és az Azure Cloud Shellt, és nem szükséges más szoftvert telepítenie. Ha a gépén helyileg telepítve van a psql segédprogram, onnan is csatlakozhat.

1. Indítsa el az Azure Cloud Shell-t a felső navigációs ablakban található terminálikonnal.

   ![Azure-adatbázis PostgreSQL-hez - Azure Cloud Shell terminálikon](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Az Azure Cloud Shell megnyílik a böngészőben, ami lehetővé teszi Bash rendszerhéjparancsok beírását.

   ![Azure-adatbázis PostgreSQL-hez - Azure Shell Bash parancssor](./media/quickstart-create-database-portal/8-bash.png)

3. A Cloud Shell parancssornál csatlakozzon a PostgreSQL-kiszolgálóhoz készült Azure-adatbázisban lévő adatbázishoz. Ehhez írja be a psql parancsot a zöld színű parancssorba.

    A következő formátum segítségével kapcsolódhat egy [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal rendelkező Azure-adatbázis PostgreSQL-kiszolgálóhoz:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Például a következő parancs egy példakiszolgálóhoz csatlakozik:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql paraméter |Ajánlott érték|Leírás
    ---|---|---
    --host | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a PostgreSQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a mypgserver-20170401.postgres.database.azure.com. Használja a teljes tartománynevet (\*.postgres.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    --port | **5432** | A PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáskor mindig használja az 5432-es portot. 
    --username | *kiszolgáló-rendszergazdai bejelentkezési név* |Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a PostgreSQL-hez készült Azure-adatbázis létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  A formátum *username@servername*.
    --dbname | **postgres** | Az első kapcsolathoz használja a rendszer által létrehozott alapértelmezett *postgres* adatbázisnevet. Később létrehozhatja a saját adatbázisát.

    Miután a saját paraméterértékeivel futtatta a psql parancsot, a rendszer felkéri, hogy adja meg a kiszolgáló-rendszergazdai jelszót. Ez ugyanaz az a jelszó, amelyet a kiszolgáló létrehozásakor adott meg. 

    psql paraméter |Ajánlott érték|Leírás
    ---|---|---
    jelszó | *az Ön rendszergazdai jelszava* | Vegye figyelembe, hogy a beírt jelszókarakterek nem jelennek meg a Bash parancssorban. Írja be az összes karaktert, majd nyomja le az Enter billentyűt a hitelesítéshez és a csatlakozáshoz.

    Ha csatlakoztatva van, a psql segédprogram postgres parancssort jelenít meg, ahová az SQL-parancsokat beírhatja. A rendszer a kezdeti kapcsolati kimeneten egy figyelmeztetést jeleníthet meg, mivel az Azure Cloud Shell psql-jének verziója eltérhet a PostgreSQL-kiszolgálóhoz készült Azure-adatbázis verziószámától. 
    
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
    > Ha a tűzfal nincs konfigurálva az Azure Cloud Shell IP-címének engedélyezésére, a következő hiba jelenik meg:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL:  SSL connection is required. Please specify SSL options and retry.
    > 
    > A hiba elhárításához győződjön meg róla, hogy a kiszolgáló konfigurációja megegyezik a cikk *Kiszolgálószintű tűzfalszabály konfigurálása* részében található lépésekkel.

4.  Hozzon létre egy üres adatbázist a következő parancs beírásával a parancssorba:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    A parancs teljesítése pár percet is igénybe vehet. 

5.  Amikor a rendszer kéri, hajtsa végre a következő parancsot, a kapcsolat átváltásához az újonnan létrehozott adatbázisra **mypgsqldb**.
    ```bash
    \c mypgsqldb
    ```

6.  A psql-ből való kilépéshez írja be a \q karakterláncot, majd nyomja le az ENTER billentyűt. Miután végzett, bezárhatja az Azure Cloud Shellt.

Ezzel csatlakozott a PostgreSQL-hez készült Azure-adatbázishoz, és létrehozott egy üres felhasználói adatbázist. Folytassa a következő szakasszal, amelyből megtudhatja, hogyan csatlakozhat egy másik ismert eszköz, a pgAdmin segítségével.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Csatlakozás a PostgreSQL-adatbázishoz a pgAdmin használatával

Kapcsolódás az Azure PostgreSQL-kiszolgálóhoz a _pgAdmin_ GUI-eszköz használatával
1.  Indítsa el a MySQL _pgAdmin_ alkalmazást az ügyfélszámítógépen. A _pgAdmin-t_ http://www.pgadmin.org/ oldalról telepítheti.
2.  Kattintson az Irányítópult lap közepén lévő **Gyorshivatkozások** szakasz **Új kiszolgáló hozzáadása** ikonjára.
3.  A **Kiszolgáló létrehozása** párbeszédpanel **Általános** lapján adjon egy egyedi rövid nevet a kiszolgálónak, például **Azure PostgreSQL Server**.
![pgAdmin tool - create - server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  A **Kiszolgáló létrehozása** párbeszédpanel **Kapcsolat** lapján használja a beállításokat, ahogyan meg vannak adva, majd kattintson a **Mentés** gombra.
   ![pgAdmin - Create - Server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-paraméter |Ajánlott érték|Leírás
    ---|---|---
    Gazdagépnév/-cím | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a PostgreSQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a mypgserver-20170401.postgres.database.azure.com. Használja a teljes tartománynevet (\*.postgres.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    Port | **5432** | A PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáskor mindig használja az 5432-es portot.  
    Karbantartási adatbázis | **postgres** | Használja a rendszer által létrehozott alapértelmezett *postgres* adatbázisnevet.
    Felhasználónév | *kiszolgáló-rendszergazdai bejelentkezési név* | Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a PostgreSQL-hez készült Azure-adatbázis létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    Jelszó | *az Ön rendszergazdai jelszava* |  A kiszolgáló létrehozásakor választott jelszó.
    Szerepkör | *hagyja üresen* | Itt nem kell megadni szerepkörnevet. Hagyja üresen ezt a mezőt.
    SSL-mód | Kötelező | Alapértelmezés szerint a rendszer minden Azure PostgreSQL-kiszolgálót az SSL-kényszerítéssel bekapcsolva hoz létre. Részletek az SSL-kényszerítés kikapcsolásáról: [SSL kényszerítése](./concepts-ssl-connection-security.md).
    
5.  Kattintson a **Save** (Mentés) gombra.
6.  A baloldali böngészőpanelen bontsa ki a **Kiszolgálók** csomópontot. Válassza ki például az **Azure PostgreSQL Server** kiszolgálót, és kattintson rá a csatlakozáshoz.
7. Bontsa ki a kiszolgáló-csomópontot, majd bontsa ki az abban található **Adatbázisok** csomópontot is. A listában szerepelnie kell a meglévő *postgres* adatbázisnak, valamint az előző szakaszban esetlegesen újonnan létrehozott felhasználói adatbázisoknak (például a *mypgsqldb* adatbázisnak) is. Vegye figyelembe, hogy a PostgreSQL-hez készült Azure-adatbázis segítségével kiszolgálónként több adatbázist is létrehozhat.
8. Kattintás a jobb gombbal az **Adatbázisok** elemre, majd kattintson a **Létrehozás** menü **Adatbázis** elemére.
9.  Írja be a választott adatbázisnevet az **Adatbázis** mezőbe (a példánkban ez a *mypgsqldb*). 
10. A legördülő mezőben válassza ki az adatbázis **Tulajdonosát**. Válassza ki a kiszolgáló-rendszergazda bejelentkezési nevét (a példánkban ez a *mylogin*).
10. Egy új, üres adatbázis létrehozásához kattintson a **Mentés** gombra.
11. A **Böngésző** panelen tekintse meg az Ön által létrehozott adatbázist az adatbázisok listájában, a kiszolgáló nevét viselő területen.
 ![pgAdmin - Create - Database](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Távolítsa el az Ön által a rövid útmutató során létrehozott erőforrásokat. Ezt vagy az erőforráscsoportban lévő összes erőforrást magában foglaló [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md), vagy az egyetlen kiszolgáló-erőforrás törlésével teheti meg, ha a többi erőforrást érintetlenül szeretné hagyni.

> [!TIP]
> Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

Ha az újonnan létrehozott kiszolgálót is magában foglaló teljes erőforráscsoportot törölni szeretné:
1.  Keresse meg az erőforráscsoportot az Azure Portalon. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** elemre, majd kattintson az erőforráscsoport nevére, ami a példánkban a **myresourcegroup**.
2.  Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Írja be a törölni kívánt erőforrás nevét (a példánkban: **myresourcegroup**) a szövegmezőbe a törlés megerősítéséhez, majd kattintson a **Törlés** gombra.

Ha az újonnan létrehozott kiszolgálót szeretné törölni:
1.  Keresse meg a kiszolgálóját az Azure Portalon, amennyiben az nincs megnyitva. Az Azure Portal baloldali menüjében kattintson a **Minden erőforrás** elemre, és keressen rá az Ön által létrehozott kiszolgálóra.
2.  Az **Áttekintés** lap felső paneljén kattintson a **Törlés** gombra.
![PostgreSQL-hez készült Azure-adatbázis – Kiszolgáló törlése](./media/quickstart-create-database-portal/12-delete.png)
3.  Erősítse meg a törölni kívánt kiszolgáló nevét, és jelenítse meg az érintett adatbázisokat. Írja be a kiszolgáló nevét (a példánkban: **mypgserver-20170401**) a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

