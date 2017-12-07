## <a name="customize-and-extend-the-device-management-actions"></a>Testreszabására és kibővítésére az eszköz felügyeleti műveletei

Az IoT-megoldások bontsa ki az eszköz felügyeleti minták meghatározott készletét, vagy egyéni minták engedélyezése az eszköz iker és a felhő eszközre metódus primitívek használatával. Más eszközök felügyeleti műveletei közé a gyári beállítások visszaállítása, belső vezérlőprogram frissítése, szoftverfrissítés, az energiagazdálkodás, hálózat és kapcsolatok kezelése és az adattitkosítás.

## <a name="device-maintenance-windows"></a>Eszköz karbantartási időszakok

Általában konfigurálnia eszközök ütemezze, hogy a megszakítások számát és az állásidő minimálisra csökkenti a műveletek elvégzéséhez. Eszköz karbantartási időszakok határozzák meg, ha egy eszköz konfigurációjában frissítenie kell a gyakran használt mintát. A háttér-megoldások az eszköz iker kívánt tulajdonságainak segítségével határozza meg, és az eszközön, amely lehetővé teszi, hogy a karbantartási időszak egy házirendet aktiválja. Ha az eszköz a karbantartási ablak házirendet kap, azt az eszköz iker jelentett tulajdonságának segítségével jelenteni a házirend állapota. A háttér-alkalmazás majd eszköz iker lekérdezések segítségével eszközről és minden egyes házirend megfelelőségi igazolnia.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban használt közvetlen módszer a indul el, a távoli újra kell indítani az eszközön. A jelentésben szereplő tulajdonságok használatával jelentés az utolsó újraindítás ideje az eszközről, és az eszköz iker felderíteni az eszköz a felhőre a legutóbbi újraindítás lekérdezett.

A folytatáshoz, a légkondicionáló frissítést keresztül Ismerkedés az IoT-központ és az eszköz felügyeleti minták például távolról, lásd:

[Oktatóanyag: Módjáról a belső vezérlőprogram frissítése][lnk-fwupdate]

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

Ismerkedés az IoT-központ a folytatáshoz tekintse meg a [Ismerkedés az IoT-Edge][lnk-iot-edge].

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-edge/tutorial-simulate-device-linux.md