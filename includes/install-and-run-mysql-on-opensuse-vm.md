
1. Az ilyen jogosultsággal, írja be:
   
        sudo -s
   
    Írja be a jelszót.
2. Telepítse a MySQL közösségi Server verziót, írja be:
   
        zypper install mysql-community-server
   
    Várjon, amíg a MySQL tölt le és telepít.
3. MySQL induljon indul el, a rendszer beállításához, írja be:
   
        insserv mysql
4. Indítsa el a MySQL-démon (mysqld) manuálisan ezzel a paranccsal:
   
        rcmysql start
   
    A MySQL démon állapotának ellenőrzéséhez írja be:
   
        rcmysql status
   
    A MySQL démon leállításához írja be:
   
        rcmysql stop
   
   > [!IMPORTANT]
   > A telepítés után a MySQL gyökér szintű jelszó alapértelmezés szerint üres. Azt javasoljuk, hogy **mysql\_biztonságos\_telepítési**, egy parancsfájl, amelynek segítségével biztonságos MySQL. A parancsprogram kéri a MySQL gyökér szintű jelszó módosítása, távolítsa el a névtelen felhasználói fiókok, tiltsa le a távoli legfelső szintű bejelentkezéseket, távolítsa el az adatbázisok vizsgálati és töltse be újra a jogosultságok tábla. Azt javasoljuk, hogy ezek közül az összes igennel válaszol, és a gyökér szintű jelszó módosítása.
   > 
   > 
5. Írja be a parancsfájl MySQL telepítési parancsfájl futtatására:
   
        mysql_secure_installation
6. Jelentkezzen be a MySQL:
   
        mysql -u root -p
   
    Adja meg a MySQL gyökér szintű jelszavát (amely módosította az előző lépésben), és akkor lesz jelenik meg a kérdés ahol kiadhatja kommunikál az adatbázis SQL-utasítások.
7. Hozzon létre egy új MySQL-felhasználó, futtassa a következő parancsot a **mysql >** parancssorba:
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Megjegyzés: a pontosvesszővel (;) a sorok végén fontosságúak a befejezési a parancsok.
8. Hozzon létre egy adatbázist, és biztosítson számára a `mysqluser` felhasználó engedélyeit adja ki a következő parancsokat:
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    Vegye figyelembe, hogy adatbázis felhasználónevek és jelszavak csak által használt parancsfájlok az adatbázishoz való kapcsolódás.  Adatbázis-felhasználói fiók neve nem feltétlenül jelenti azt, a rendszer a tényleges felhasználói fiókokat.
9. Jelentkezzen be egy másik számítógépről, írja be:
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    Ha `ip-address` az IP-cím, a számítógép, amelyen MySQL fog csatlakozni.
10. Lépjen ki a MySQL-adatbázis felügyeleti segédprogram, írja be:
    
        quit

## <a name="add-an-endpoint"></a>Végpont hozzáadása
1. MySQL telepítése után meg kell MySQL távoli eléréséhez a végpont konfigurálása. Jelentkezzen be a [a klasszikus Azure portálon][AzurePortal]. Kattintson a **virtuális gépek**, kattintson az új virtuális gép nevét, majd **végpontok**.
2. Kattintson a **Hozzáadás** az oldal alján.
3. Protokoll "MySQL" nevű végpont hozzáadása **TCP**, és **nyilvános** és **titkos** portok "3306" értékre.
4. Távoli kapcsolódás a virtuális géphez a számítógépről, írja be:
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    Például a létrehozott ebben az oktatóanyagban csúcsszintű gép használja, ezt a parancsot:
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
