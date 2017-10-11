Ebben a lépésben a rendelkezésre állási csoport figyelőjének ugyanazon a hálózaton futtató ügyfél-alkalmazás segítségével tesztelheti.

Ügyfélkapcsolat követelményei a következők:

* A figyelő-ügyfélkapcsolatokat gépeket, amelyek tárolása a másik felhőalapú szolgáltatást, amely futtatja az Always On rendelkezésre állási másodpéldányok kell származnia.
* Ha az Always On replikák külön alhálózatokon vannak, ügyfeleknek meg kell adniuk *MultisubnetFailover = True* a kapcsolati karakterláncban. Ez a feltétel a különböző alhálózatokon replikákra párhuzamos kapcsolódási kísérletek eredményez. Ez az eset tartalmazza a kereszt-régió Always On rendelkezésre állási csoport központi telepítése.

Egy példája egy, az azonos Azure virtuális hálózat (de nem egy, a replikát tartalmazó) a virtuális gépek csatlakozni a figyelő. Ez a teszt elvégzéséhez egyszerűen az SQL Server Management Studio és a rendelkezésre állási csoport figyelőjének csatlakozni próbálnak. Egy másik egyszerű módszer a következő: futtatásához [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), az alábbiak szerint:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Ha a EndpointPort érték *1433*, nem kell adnia a hívást. A korábbi hívás is feltételezi, hogy az ügyfélszámítógép csatlakozik ugyanabban a tartományban, és, hogy a hívó rendelkezik engedéllyel az adatbázis Windows-hitelesítés használatával.
> 
> 

A figyelő tesztelésekor ügyeljen arra, hogy a rendelkezésre állási csoportot, győződjön meg arról, hogy az ügyfelek kapcsolódni tud a figyelő közötti feladatátvétel feladatátvételt.

