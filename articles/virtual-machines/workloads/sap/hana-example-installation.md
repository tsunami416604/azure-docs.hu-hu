---
title: HANA SAP HANA az Azure-ban (nagyméretű példányok) telepítése |} A Microsoft Docs
description: Hogyan lehet HANA az Azure-ban (nagyméretű példányos) SAP HANA telepítése.
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
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164722"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>A példában egy nagyméretű HANA-példányokon futó SAP HANA-telepítés

Ez a szakasz azt ábrázolja, hogyan telepítse az SAP HANA nagyméretű HANA-példány található. Az indítási állapot, hogy kell kinéznie:

- A Microsoft megadott egy SAP HANA nagyméretű példányok üzembe helyezéséhez, az adatokat.
- Az SAP HANA nagyméretű példányok a Microsofttól kapott.
- Létrehozott egy Azure virtuális hálózat, amely a helyszíni hálózathoz csatlakozik.
- A ExpressRotue kapcsolatcsoportot, kapcsolat HANA nagyméretű példányok az Azure vnet felé.
- Telepítette az Azure virtuális Gépekhez, mint a jump boxon HANA nagyméretű példányokhoz használhatja.
- Ellenőrizze, hogy csatlakozhat a jump boxon a nagyméretű HANA-példány egységet, és ez fordítva is igaz.
- Be van jelölve, hogy a szükséges csomagokat és azok javításait telepítve vannak.
- Olvassa el az SAP-megjegyzések és a dokumentációra vonatkozó HANA telepítése az operációs rendszer használ, és ellenőrizze, hogy a választott HANA kiadás esetében támogatott az operációs rendszer kiadási.

A letöltés a HANA-telepítési csomagot kell a jump boxon, ebben az esetben a csomagokat, a nagyméretű HANA-példány egységhez példányát és a feladatütemezés a beállítása egy Windows operációs rendszeren futó virtuális gép mi jelenik meg a következő feladatütemezések.

## <a name="download-of-the-sap-hana-installation-bits"></a>Töltse le az SAP HANA telepítése bitek
A nagyméretű HANA-példány egységek nincs közvetlen kapcsolódás az internethez, mivel, nem közvetlenül letölthető telepítőcsomagok SAP HANA nagyméretű példány virtuális géphez. Kiküszöbölheti a hiányzó közvetlen internetkapcsolattal, a jump boxon van szükség. A jump boxon VM töltse le a csomagokat.

Töltse le a HANA-telepítési csomagokat, egy SAP-S-felhasználó vagy a többi felhasználó számára, amely lehetővé teszi, hogy az SAP-piactér eléréséhez szükséges. A bejelentkezés után a képernyők sorozatát meg:

Lépjen a [SAP Service Marketplace-en](https://support.sap.com/en/index.html) > kattintson a letöltés szoftver > telepítés és frissítés > betűrend szerinti rendezés Index által > mellett a H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > telepítési > töltse le a következő fájlok

![Töltse le a HANA telepítése](./media/hana-installation/image16_download_hana.PNG)

A bemutató esetben letöltött SAP HANA 2.0 telepítési csomagokat. A Azure jump boxon virtuális gép, bontsa ki az önkicsomagoló archívumok címtárba alább látható módon.

![Bontsa ki a HANA telepítése](./media/hana-installation/image17_extract_hana.PNG)

Ki kell olvasni az archívumot, másolja a könyvtár hozta létre a kinyerés, abban az esetben a fenti 51052030, a /hana/shared kötet, létrehozott egy olyan könyvtárba, HANA nagyméretű példány egységhez.

> [!Important]
> Ne másolja telepítőcsomagok be azokat a legfelső szintű vagy LUN-t, mivel a hely korlátozva, és egyéb eljárásokkal kell használni.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>A nagyméretű HANA-példány egységen az SAP HANA telepítése
Az SAP HANA telepítése, jelentkezzen be a felhasználó legfelső szintű kell. Csak a legfelső szintű számára elegendő az SAP HANA telepítése.
Az első lépésben kell tennie, hogy engedélyeket állíthat be a másolt keresztül/hana és a megosztott könyvtár. Az engedélyeket kell megadni, például

```
chmod –R 744 <Installation bits folder>
```

Az SAP HANA-t a grafikus telepítés telepíteni szeretné, ha a gtk2 csomag telepítve kell lennie a nagyméretű HANA-példányokhoz. Ellenőrizze, hogy telepítve van-e a parancs

```
rpm –qa | grep gtk2
```

A további lépések azt a SAP HANA beállítása a grafikus felhasználói felülettel is bemutatására. Következő lépésként nyissa meg a telepítési könyvtárba, és keresse meg a sub HDB_LCM_LINUX_X86_64 könyvtárba. Indítás

```
./hdblcmgui 
```
ki a könyvtárhoz. Most már első végigvezeti sorozata képernyők, ahol meg kell adnia az adatokat a telepítéshez. Abban az esetben mutatja be hogy telepíti az SAP HANA-kiszolgáló és az SAP HANA összetevőinek. Ezért az érték a "Az SAP HANA-adatbázis" alább látható módon

![Válassza ki a HANA telepítése](./media/hana-installation/image18_hana_selection.PNG)

A következő képernyőn válassza az "Új rendszer telepítése" lehetőséget

![Válassza ki az új HANA-telepítés](./media/hana-installation/image19_select_new.PNG)

Ebben a lépésben után kell választania számos további összetevők, amelyek emellett telepíthetők, az SAP HANA-kiszolgáló között.

![Válassza ki a további HANA-összetevők](./media/hana-installation/image20_select_components.PNG)

Ez a dokumentáció céljából választottuk az SAP HANA-ügyfelet és az SAP HANA Studio. Azt is telepíti a vertikális felskálázás példány. ezért a következő képernyőn meg kell adnia "Egy állomásra rendszer" 

![Válassza ki a telepítés vertikális felskálázás](./media/hana-installation/image21_single_host.PNG)

A következő képernyőn meg kell adnia néhány adatot

![Adja meg az SAP HANA biztonsági azonosítója](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Mint HANA rendszer azonosítója (SID), meg kell adnia a SID AZONOSÍTÓVAL, a Microsoft megadott, amikor a nagyméretű HANA-példány üzembe helyezési megrendelt. Eltérő SID AZONOSÍTÓVAL kiválasztása lehetővé teszi a telepítést, a különböző köteteken hozzáférési engedély problémák miatt meghiúsul

Telepítésként használja a/hana/directory megosztott könyvtár. A következő lépésben meg kell adnia a helyek a HANA-adatfájlok és a HANA-naplófájlok


![Adja meg a HANA-napló helye](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Kell meghatározni, adatok és a köteteket, amely már a csatlakoztatási pontokat tartalmazó képernyő kiválasztása előtt ezen a képernyőn kiválasztott SID-naplófájljai. A biztonsági azonosító nem egyezik a tartományfejlécben megadott, ha a képernyőn előtt, lépjen vissza, és állítsa be a SID-t a csatlakoztatási pontok értékre.

A következő lépésben tekintse át a gazdagép nevét, és végül javítsa ki. 

![Felülvizsgálat állomás neve](./media/hana-installation/image24_review_host_name.PNG)

A következő lépésben szükség, megadott a Microsoftnak, amikor a nagyméretű HANA-példány üzembe helyezési megrendelt adatainak beolvasása. 

![Adja meg a rendszer felhasználói felhasználó- és CSOPORTAZONOSÍTÓ](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Meg kell adnia a rendszer-felhasználói azonosító és a felhasználói csoport azonosítója, a Microsoft elsajátítania ahhoz az egység üzembe helyezés. Ha nem ad nagyon ugyanazokat az azonosítókat, a nagyméretű HANA-példány egységen SAP HANA telepítése sikertelen.

A következő két képernyő, és azt nem megjelennek ebben a dokumentációban, meg kell adnia a jelszót a rendszer felhasználó, az SAP HANA-adatbázis és a sapadm felhasználó jelszava, a használt az SAP az SAP HANA datab részeként telepített gazdagép ügynök ASE-példány.

Határozza meg a jelszót, miután egy visszaigazoló képernyő jelenik meg. Ellenőrizze az összes adat szerepel, és a telepítés folytatásához. Eléri a folyamat képernyője, amely a telepítési folyamatot, mint például az egyik az alábbi dokumentumok

![Ellenőrizze a telepítési folyamat](./media/hana-installation/image27_show_progress.PNG)

A telepítés befejezését követően a következő egy hasonló képet kell

![Telepítés befejeződött](./media/hana-installation/image28_install_finished.PNG)

Ezen a ponton az SAP HANA-példány lehet akár és fut, és már használatra. Meg kell tudni kapcsolódni az SAP HANA Studio. Győződjön meg arról, hogy ellenőrizze az SAP Hana a legújabb javításokat, és e-javítások alkalmazása a is.


**Következő lépések**

- Tekintse meg [SAP HANA nagyméretű példányok magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).

