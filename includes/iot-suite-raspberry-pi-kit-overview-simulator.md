## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban végezze el a következő lépéseket:

- Telepítse a távoli felügyeleti előkonfigurált megoldás egy példányát az Azure-előfizetéshez. Ebben a lépésben automatikusan telepíti és konfigurálja a több Azure-szolgáltatásokhoz.
- Konfigurálja az eszközt, a számítógép és a távoli felügyeleti megoldás folytatott kommunikációhoz.
- Frissítse a mintakódot eszköz csatlakozni a távoli felügyeleti megoldás, és, amely megtalálható a megoldás irányítópultja szimulált telemetriai adatokat küldhet.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk-free-trial].

### <a name="required-software"></a>Szükséges szoftverek

SSH-ügyfél van szüksége az asztali gépen ahhoz, hogy a parancssor a málna Pi a érheti el távolról.

- A Windows tartalmaz egy SSH-ügyfél. Azt javasoljuk, [PuTTY](http://www.putty.org/).
- A legtöbb Linux terjesztéseket, a Mac OS közé tartoznak az SSH parancssori segédprogramot. További információkért lásd: [SSH használatával Linux és Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-hardware"></a>Szükséges hardver

Ahhoz, hogy a parancssor a málna Pi a távoli csatlakozás egy asztali számítógépen.

[Microsoft IoT Starter Kit málna Pi 3] [ lnk-starter-kits] vagy ezzel egyenértékű összetevőket. Ez az oktatóanyag a csomagot a következő elemeket használja:

- Raspberry Pi 3
- (A NOOBS) MicroSD-kártyán
- A USB Mini-kábellel
- Kábel

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/