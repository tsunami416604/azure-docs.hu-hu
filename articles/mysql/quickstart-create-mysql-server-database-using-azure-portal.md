---
title: "Első lépések: Azure-adatbázis létrehozása MySQL-kiszolgálóhoz - Azure Portal | Microsoft Docs"
description: "Ez a cikk lépésről lépésre bemutatja, hogyan hozhat létre egy minta Azure-adatbázist MySQL-kiszolgálóhoz az Azure Portal használatával öt perc alatt."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: hu-hu
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával
A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre nagyjából öt perc alatt egy Azure-adatbázist MySQL-kiszolgálóhoz az Azure Portal használatával. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Nyissa meg a webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
A MySQL-kiszolgálóhoz készült Azure-adatbázis [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül jön létre.

Kövesse az alábbi lépéseket egy, a MySQL-kiszolgálóhoz készült Azure-adatbázis létrehozásához:

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra (+).

2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure Database MySQL-kiszolgálóhoz** lehetőséget. Az Új oldal keresőmezőbe be is írhatja a **MySQL** szöveget, hogy gyorsan megtalálja a szolgáltatást.
![Azure Portal - új - adatbázis - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Töltse ki az új kiszolgáló adatai űrlapot a következő információkkal az előző képen látható módon:

    **Beállítás** | **Ajánlott érték** | **Mező leírása** 
    ---|---|---
    Kiszolgálónév | myserver4demo | Válasszon egy egyedi nevet, amely azonosítja a MySQL-kiszolgálóhoz készült Azure-adatbázist. A rendszer hozzáfűzi a *mysql.database.azure.com* tartománynevet ahhoz a kiszolgálónévhez, amelyet az alkalmazások számára megad a csatlakozáshoz. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja, és 3–63 karakter hosszú lehet.
    Előfizetés | Az Ön előfizetése | A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás terhelve van.
    Erőforráscsoport | myResourceGroup | Meghatározhat egy új erőforráscsoport-nevet, vagy használhat egy meglévőt az előfizetéséből.
    Kiszolgáló-rendszergazdai bejelentkezés | myadmin | Hozzon létre saját bejelentkezési fiókot a kiszolgálóhoz való csatlakozáshoz. A rendszergazdai bejelentkezési név nem lehet „azure_superuser”, „admin”, „administrator”, „root”, „guest” vagy „public”.
    Jelszó | *A választása szerint* | Hozzon létre egy új jelszót a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakter hosszúságú lehet. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Jelszó megerősítése | *A választása szerint*| Erősítse meg a rendszergazdafiók jelszavát.
    Hely | *A felhasználókhoz legközelebb eső régió*| Válassza ki a felhasználókhoz vagy más Azure-alkalmazásokhoz legközelebb eső helyet.
    Verzió | *Válassza a legújabb verziót*| Válassza a legújabb verziót, ha nincsenek egyedi igényei.
    Tarifacsomag | **Alapszintű**, **50 számítási egység** **50 GB** | Kattintson a **Tarifacsomag** parancsra az új adatbázis szolgáltatás- és teljesítményszintjének megadásához. Válassza a felső fülön az **Alapszintű tarifacsomagot**. Kattintson a **Számítási egységek** csúszka bal oldali végére az ebben a rövid útmutatóban elérhető legkisebb mennyiség kiválasztásához. Kattintson az **OK** gombra a tarifacsomag-beállítás mentéséhez. Tekintse meg a következő képernyőképet.
    Rögzítés az irányítópulton | Jelölőnégyzet | A **Rögzítés az irányítópulton** lehetőség kiválasztásával egyszerűen nyomon követheti a kiszolgálót az Azure Portal irányítópultjának első lapján.

    > [!IMPORTANT]
    > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.
    > 

    ![Azure Portal - A MySQL létrehozása az űrlap kitöltésével](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  A kiszolgáló üzembe helyezéséhez kattintson a **Létrehozás** elemre. Az üzembe helyezés eltarthat néhány percig, de legfeljebb 20 percet vesz igénybe.
   
5.  Az eszköztáron kattintson az **Értesítések** (harang ikon) elemre az üzembehelyezési folyamat megfigyeléséhez.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az MySQL szolgáltatáshoz készült Azure-adatbázis a kiszolgáló szintjén hoz létre tűzfalat. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha olyan tűzfalszabályt hoz létre, amely adott IP-címek számára megnyitja a tűzfalat. 

1.  Az üzembe helyezés befejeződése után keresse meg a kiszolgálót. Ha szükséges, használja a keresési funkciót. Például kattintson a **Minden erőforrás** elemre a bal oldali menüben, és írja be a kiszolgáló nevét (például: *myserver4demo*) az újonnan létrehozott kiszolgáló megkereséséhez. Kattintson a kiszolgálónévre a találatok listájában. Megnyílik a kiszolgáló **Áttekintés** oldala, amely további konfigurációs lehetőségeket biztosít.

2. A kiszolgáló lapján válassza a **Kapcsolatbiztonság** elemet.

3.  A **Tűzfalszabályok** szakaszban kattintson az üres mezőbe a **Szabály neve** oszlopban új tűzfalszabály létrehozásának megkezdéséhez. 

    Ebben a rövid útmutatóban engedélyezünk minden bemenő IP-címet a kiszolgálón. Ehhez adjuk meg az egyes oszlopokban a következő értékeket:

    Szabály neve | Kezdő IP-cím | Záró IP-cím 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. A **Kapcsolatbiztonság** lap felső eszköztárában kattintson a **Mentés** gombra. Várjon néhány pillanatot a Kapcsolatbiztonság frissítésének sikeres befejezését jelző értesítés megjelenéséig, mielőtt folytatná a műveletet.

    > [!NOTE]
    > A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat a 3306-os porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg nem nyitja meg a 3306-os portot.
    > 

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése
Az adatbázis-kiszolgálóhoz való csatlakozáshoz meg kell jegyeznie a teljes kiszolgálónevet és a rendszergazdai bejelentkezési hitelesítő adatokat. Ezeket a rövid útmutatóban korábban már feljegyezhette. Ha nem jegyezte fel, a kiszolgáló nevét és bejelentkezési adatait megtalálhatja a kiszolgáló **Áttekintés** vagy **Tulajdonságok** lapján az Azure Portalon.

1. Nyissa meg kiszolgáló **Áttekintés** lapját. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. 
    Helyezze az egérmutatót az egyes mezők fölé. Ekkor a szövegtől jobbra megjelenik a másolási ikon. Az értékek másolásához kattintson igény szerint a másolási ikonra.

    Ebben a példában a kiszolgáló neve *myserver4demo.mysql.database.azure.com*, a kiszolgáló-rendszergazdai bejelentkezési név pedig *myadmin@myserver4demo*.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Csatlakozás a MySQL-hez a mysql parancssori eszköz használatával
Számos különféle alkalmazással csatlakozhat a MySQL-kiszolgálóhoz készült Azure-adatbázishoz. Először használjuk a [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) parancssori eszközt a kiszolgálóhoz való csatlakozás bemutatásához.  Az itt leírtak szerint használhatja a webböngészőt és az Azure Cloud Shellt, és nem szükséges más szoftvert telepítenie. Ha a gépén helyileg telepítve van a mysql segédprogram, onnan is csatlakozhat.

1. Indítsa el az Azure Cloud Shellt az Azure Portal jobb felső részén található terminálikonnal ( >_ ).

2. Az Azure Cloud Shell megnyílik a böngészőben, ami lehetővé teszi Bash rendszerhéjparancsok beírását.

    ![Parancssor - mysql parancssor példa](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. A Cloud Shell parancssorában csatlakozzon a MySQL-kiszolgálóhoz készült Azure-adatbázishoz. Ehhez írja be a mysql parancsot a zöld színű parancssorba.

    A következő szintaxissal kapcsolódhat a mysql segédprogrammal egy MySQL-kiszolgálóhoz készült Azure-adatbázishoz:
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Például a következő paranccsal a példakiszolgálónkhoz csatlakozhat:
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql-paraméter |Ajánlott érték|Leírás
    ---|---|---
    --host | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a myserver4demo.mysql.database.azure.com. Használja a teljes tartománynevet (\*.mysql.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    --user | *kiszolgáló-rendszergazdai bejelentkezési név* |Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  A formátum *username@servername*.
    --password | *várjon a hitelesítőadat-kérésig* | A rendszer fel fogja szólítani a jelszó megadására a parancs bevitelét követően. Amikor a rendszer felszólítja, írja be ugyanazt a jelszót, amelyet a kiszolgáló létrehozásakor adott meg.  Vegye figyelembe, hogy a beírt jelszókarakterek nem jelennek meg a Bash parancssorban, amikor beírja őket. Írja be az összes karaktert, majd nyomja le az Enter billentyűt a hitelesítéshez és a csatlakozáshoz.

   Miután csatlakozott, a mysql segédprogram egy `mysql>` parancssort jelenít meg a parancsok beviteléhez. 

    Példa a mysql-kimenetre:
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Ha a tűzfal nincs konfigurálva az Azure Cloud Shell IP-címének engedélyezésére, a következő hiba jelenik meg:
    >
    > HIBA 2003 (28000): A következő IP-címmel rendelkező ügyfél nem rendelkezik hozzáféréssel a kiszolgálóhoz: 123.456.789.0
    >
    > A hiba elhárításához győződjön meg róla, hogy a kiszolgáló konfigurációja megegyezik a cikk *Kiszolgálószintű tűzfalszabály konfigurálása* részében található lépésekkel.

4. A kiszolgáló állapotát megvizsgálva győződjön meg róla, hogy a kapcsolat működik. A csatlakozás után gépelje be a következő parancsot a mysql > parancssorba: `status`.
    ```sql
    status
    ```

   > [!TIP]
   > További parancsokért lásd: [az MySQL 5.7 referenciaútmutatójának 4.5.1-es fejezetét](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Hozzon létre egy üres adatbázist a következő parancs a mysql> parancssorba való beírásával:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    A parancs teljesítése pár percet is igénybe vehet. 

    A MySQL-kiszolgálóhoz létrehozott Azure-adatbázisban egy vagy több adatbázist is létrehozhat. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. A létrehozható adatbázisok száma nincs korlátozva, de több adatbázis ugyanazokon a kiszolgáló-erőforrásokon osztozik. 

6. Jelenítse meg az adatbázisok listáját a következő parancs a mysql> parancssorba való beírásával:

    ```sql
    SHOW DATABASES;
    ```

7.  A mysql segédprogramból való kilépéshez írja be a `\q` karakterláncot, majd nyomja le az ENTER billentyűt. Miután végzett, bezárhatja az Azure Cloud Shellt.

Ezzel csatlakozott a MySQL-hez készült Azure-adatbázishoz, és létrehozott egy üres felhasználói adatbázist. Folytassa a következő szakasszal egy ehhez hasonló gyakorlat megismétléséhez, amelyből megtudhatja, hogyan csatlakozhat ugyanazon kiszolgálóhoz egy másik ismert eszköz, a MySQL Workbench segítségével.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench GUI eszköz használatával
Kapcsolódás az Azure MySQL-kiszolgálóhoz a MySQL Workbench GUI eszköz használatával:

1.  Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench-et [innen](https://dev.mysql.com/downloads/workbench/) töltheti le és telepítheti.

2.  Az **Új kapcsolat létrehozása** párbeszédpanelen adja meg a következő információkat a **Paraméterek** lapon:

    ![új kapcsolat beállítása](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Beállítás** | **Ajánlott érték** | **Mező leírása** |
    |---|---|---|
    |   Kapcsolat neve | Bemutató kapcsolat | Adjon meg egy címkét a kapcsolathoz. |
    | Kapcsolati módszer | Standard (TCP/IP) | A Standard (TCP/IP) elégséges. |
    | Gazdanév | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a myserver4demo.mysql.database.azure.com. Használja a teljes tartománynevet (\*.mysql.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  |
    | Port | 3306 | A MySQL-hez készült Azure-adatbázishoz való csatlakozáskor mindig a 3306-os portot használja. |
    | Felhasználónév |  *kiszolgáló-rendszergazdai bejelentkezési név* | Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor adott meg. A példában szereplő felhasználónév a következő: myadmin@myserver4demo. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    | Jelszó | az ön jelszava | A jelszó mentéséhez kattintson a Tárolás a tárolóban... gombra. |

    Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva. A kapcsolat mentéséhez kattintson az OK gombra. 

    > [!NOTE]
    > Alapértelmezés szerint az SSL kényszerítve van a kiszolgálón, és további konfigurációt igényel a sikeres csatlakozás érdekében. Lásd [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md).  Ha le szeretné tiltani a rövid útmutató során az SSL-t, látogasson el az Azure Portalra, és a Kapcsolatbiztonság lapon használja az SSL-kapcsolat kikényszerítése kapcsológombot a letiltásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Távolítsa el az Ön által a rövid útmutató során létrehozott erőforrásokat. Ezt vagy az erőforráscsoportban lévő összes erőforrást magában foglaló [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md), vagy az egyetlen kiszolgáló-erőforrás törlésével teheti meg, ha a többi erőforrást érintetlenül szeretné hagyni.

> [!TIP]
> Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.
>

Ha az újonnan létrehozott kiszolgálót is magában foglaló teljes erőforráscsoportot törölni szeretné:
1.  Keresse meg az erőforráscsoportot az Azure Portalon. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** elemre, majd kattintson az erőforráscsoport nevére, ami a példánkban a **myresourcegroup**.
2.  Az erőforráscsoport oldalán kattintson a **Törlés** parancsra. Írja be a törölni kívánt erőforrás nevét (a példánkban: **myresourcegroup**) a szövegmezőbe a törlés megerősítéséhez, majd kattintson a **Törlés** gombra.

Ha az újonnan létrehozott kiszolgálót szeretné törölni:
1.  Keresse meg a kiszolgálóját az Azure Portalon, amennyiben az nincs megnyitva. Az Azure Portal baloldali menüjében kattintson a **Minden erőforrás** elemre, és keressen rá az Ön által létrehozott kiszolgálóra.
2.  Az **Áttekintés** lap felső paneljén kattintson a **Törlés** gombra.
![MySQL-hez készült Azure-adatbázis – kiszolgáló törlése](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Erősítse meg a törölni kívánt kiszolgáló nevét, és jelenítse meg az érintett adatbázisokat. Írja be a kiszolgáló nevét (a példánkban: **myserver4demo**) a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első Azure-adatbázis MySQL-adatbázishoz megtervezése](./tutorial-design-database-using-portal.md)


