Helyileg redundáns tárolás (LRS) az adatok egy tárolási méretezési egység, amely a régióban, amelyben létrehozta a tárfiók adatközpontban található belül háromszor replikálja. Az írási kérelem sikeresen beolvasása csak egyszer minden három replikákra lett írva. Ezek a három replikák külön tartalék tartományok és egy tárolási méretezési egység frissítési tartományban találhatók.

A tárolási méretezési egység tárolócsomópontok rackszekrények gyűjteménye. A tartalék tartomány (FD) olyan csomóponton, amelyeket egy fizikai egységet kialakulását jelöl, és az ugyanazon fizikai állványra tartozó számítógépen tekinthető. Frissítési tartományok (UD) olyan csomóponton, amelyeket a szolgáltatás frissítése (Bevezetés) folyamata során egyszerre frissítik. A három replikák vannak elosztva UDs és FDs belül egy tárolási méretezési egység győződjön meg arról, hogy érhetők el adatok akkor is, ha hardverhiba egyetlen állvány hatással van, vagy ha a csomópont frissítése a bevezetés alatt.

LRS a legalacsonyabb költséget beállítás, és más beállítások képest legalább tartósságot biztosít. (Fire, elárasztás stb) datacenter szintű katasztrófa esetén minden három replikák elveszett vagy helyreállíthatatlan lehet. Ennek a kockázatnak a mérséklése érdekében a legtöbb alkalmazás földrajzi redundáns tárolás (GRS) ajánlott.

Helyileg redundáns tárolás kívánatos bizonyos esetekben továbbra is lehet:

* Itt a legnagyobb maximális sávszélesség az Azure Storage replikálási beállításaival.
* Ha az alkalmazás, amely könnyen rekonstruálható adatokat tárol, LRS is választhat.
* Adatreplikálás adatok cégirányítási követelmények miatt országon belül csak bizonyos alkalmazások korlátozódnak. Egy párosított régióban lehet egy másik országból. A régió párok további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).