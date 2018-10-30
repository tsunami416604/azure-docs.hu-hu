---
title: HANA SAP HANA az Azure-ban (nagyméretű példányok) telepítése |} A Microsoft Docs
description: Hogyan telepíthető HANA SAP HANA az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4629894933507bda7359fb034c4079d38100029
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231406"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Az SAP HANA az Azure-ban (nagyméretű példányok) HANA telepítése

HANA telepíteni az SAP HANA az Azure-ban (nagyméretű példányok), akkor először tegye a következőket:
- A Microsoft, az SAP HANA nagyméretű példányok telepítendő összes adatot adja meg.
- Az SAP HANA nagyméretű példányok kapni a Microsofttól.
- Létrehozhat egy Azure virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
- Az ExpressRoute-kapcsolatcsoport HANA nagyméretű példányok az azonos Azure virtuális hálózathoz csatlakozzon.
- Az Azure virtuális gép a HANA nagyméretű példányok használni a jump boxon telepítenie.
- Akkor győződjön meg arról, hogy képes csatlakozni a jump boxon a nagyméretű HANA-példány egység, és ez fordítva is igaz.
- Ellenőrizze, hogy a szükséges csomagokat és azok javításait telepítve vannak.
- Olvassa el az SAP-megjegyzések és az operációs rendszer használata esetén a HANA-telepítési dokumentációt. Győződjön meg arról, hogy a választott HANA kiadás támogatott operációs rendszer kiadására.

A következő szakaszban látható egy példa a HANA-telepítési csomagok letöltése a gyorselérési be virtuális géphez. Ebben az esetben az operációs rendszer Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Az SAP HANA telepítése bits letöltése
A nagyméretű HANA-példány egységek közvetlenül nem csatlakoznak az internethez. Nem közvetlenül letölthető telepítőcsomagok SAP HANA nagyméretű példányok virtuális géphez. Ehelyett, töltse le a csomagokat a jump be virtuális géphez.

Egy SAP-S-felhasználó vagy a többi felhasználó számára, amely lehetővé teszi, hogy az SAP-piactér elérésére van szüksége.

1. Jelentkezzen be, és nyissa meg [SAP Service Marketplace-en](https://support.sap.com/en/index.html). Válassza ki **letölthető a szoftver** > **telepítés és frissítés** > **betűrend szerinti rendezés Index által**. Válassza ki **a H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **telepítési**. Töltse le a fájlokat, az alábbi képernyőképen látható.

   ![Képernyőkép a fájlok letöltése](./media/hana-installation/image16_download_hana.PNG)

2. Ebben a példában az SAP HANA 2.0 telepítőcsomagok letöltött. Az Azure-beli jump jelölését a virtuális gép, bontsa ki az önkicsomagoló archívumok a könyvtárba, ahogy az alábbi.

   ![Képernyőkép a önkicsomagoló archívum](./media/hana-installation/image17_extract_hana.PNG)

3. Az archívum ki kell olvasni, ahogy másolja a könyvtárat, a kivonás (ebben az esetben az 51052030) által létrehozott. Másolja a könyvtárat a nagyméretű HANA-példány egység /hana/shared kötetről, létrehozott egy olyan könyvtárba.

   > [!Important]
   > Nem másolja telepítőcsomagok azokat a legfelső szintű vagy LUN-t, mert terület korlátozva, és egyéb eljárásokkal kell használni.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>A nagyméretű HANA-példány egységen az SAP HANA telepítése
Az SAP HANA telepítése bejelentkeznie a felhasználó legfelső szintű. Csak a legfelső szintű számára elegendő az SAP HANA telepítése. A könyvtár /hana/shared keresztül másolt engedélyeket.

```
chmod –R 744 <Installation bits folder>
```

Ha azt szeretné, az SAP HANA telepítése a grafikus felhasználói felület beállítás használatával, a gtk2 csomag telepítve kell lennie a nagyméretű HANA-példányokhoz. Ellenőrizze, hogy telepítve van-e, futtassa a következő parancsot:

```
rpm –qa | grep gtk2
```

(A későbbi lépésekben bemutatjuk a SAP HANA beállítása a grafikus felhasználói felülettel.)

Nyissa meg a telepítési könyvtárba, és keresse meg a sub HDB_LCM_LINUX_X86_64 könyvtárba. 

Kezdő kívül könyvtárban:

```
./hdblcmgui 
```
At a pont, folyamatban, amelyben az adatok biztosít a telepítési képernyők sorozatát keresztül. Ebben a példában az SAP HANA-kiszolgáló és az SAP HANA összetevőinek telepíti azt. A kijelölés ezért **SAP HANA-adatbázis**.

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő, az SAP HANA-adatbázis kijelölve](./media/hana-installation/image18_hana_selection.PNG)

A következő képernyőn válassza ki a **telepítése új rendszer**.

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő, és telepíteni, új rendszer kiválasztva](./media/hana-installation/image19_select_new.PNG)

Ezután válassza ki, amelyek telepítése számos további összetevők közötti.

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő, és további összetevők listáját](./media/hana-installation/image20_select_components.PNG)

Itt hogy válassza ki az SAP HANA-ügyfél és az SAP HANA Studio. Azt is telepítheti a vertikális felskálázás példány. Válassza a **egy állomásra rendszer**. 

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő kiválasztva egy gazdagép rendszerrel](./media/hana-installation/image21_single_host.PNG)

Következő lépésként adja meg az adatokat.

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő, és a rendszermezők tulajdonságainak meghatározása](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Mint HANA rendszer azonosítója (SID), meg kell adnia, a Microsoft megadni, ha a nagyméretű HANA-példány üzembe helyezési megrendelt SID AZONOSÍTÓVAL. A telepítés sikertelen lesz, a különböző köteteken hozzáférési engedély problémák miatt eltérő SID AZONOSÍTÓVAL kiválasztása okoz.

A telepítési útvonalat a /hana/shared könyvtárat használja. A következő lépésben a helyek a HANA-adatfájlok és biztosítanak a HANA naplófájlokat.


![Képernyőkép az SAP HANA életciklus-felügyelet képernyő, az adat- és naplófájlok terület mezők](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> A megadott Rendszertulajdonságok (ezelőtt két képernyő) Ha a megadott SID egyeznie kell a biztonsági AZONOSÍTÓJÁT, a csatlakoztatási pontokat. Ha eltérés van, lépjen vissza, és az SID-t a csatlakoztatási pontok értékre módosíthatja.

A következő lépésben tekintse át a gazdagép nevét, és végül javítsa ki. 

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő az állomásnevet](./media/hana-installation/image24_review_host_name.PNG)

A következő lépésben szükség, megadott a Microsoftnak, amikor a nagyméretű HANA-példány üzembe helyezési megrendelt adatainak beolvasása. 

![Képernyőkép az SAP HANA életciklus-felügyelet, a rendszer rendszergazdai mezők definiálása](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Adja meg az azonos **rendszer rendszergazdai felhasználói Azonosítóját** és **felhasználócsoport azonosítója** , az egység üzembe helyezés megrendeli a Microsoft, a megadott módon. Ellenkező esetben a nagyméretű HANA-példány egységen SAP HANA telepítése sikertelen.

A következő két képernyő nem jelennek meg itt. Adja meg a jelszót a rendszer felhasználó, az SAP HANA-adatbázis és a jelszó a sapadm felhasználó számára lehetővé teszik. Az utóbbi szolgál az SAP az SAP HANA-adatbázispéldányt részeként telepített gazdagép ügynök számára.

Után határozza meg a jelszót, megjelenik egy megerősítő képernyő. Ellenőrizze az összes adat szerepel, és a telepítés folytatásához. Eléri a folyamat képernyője, amely dokumentálja a telepítési folyamatot, ehhez hasonló:

![Képernyőkép az SAP HANA életciklus-felügyelet képernyő, a telepítés folyamatjelző](./media/hana-installation/image27_show_progress.PNG)

Mivel a telepítés befejezését követően az alábbihoz hasonló képernyő kell megjelennie:

![Képernyőkép az SAP HANA életciklus-felügyelet képernyőn jelzi a telepítés befejeződött](./media/hana-installation/image28_install_finished.PNG)

Az SAP HANA-példányt kell akár és fut, és már használatra. Meg kell tudni kapcsolódni az SAP HANA Studio. Győződjön meg arról, hogy ellenőrizze, és alkalmazza a legújabb frissítéseket is.


## <a name="next-steps"></a>További lépések

- [SAP HANA nagyméretű példányok magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md)

