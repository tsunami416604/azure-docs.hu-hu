---
title: Hana telepítése sap hana-ra az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Hana telepítése az SAP HANA az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617212"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Hana telepítése SAP HANA-ra az Azure-on (nagy példányok)

A HANA telepítése az SAP HANA az Azure-ban (nagy példányok), először a következőket kell tennie:
- A Microsoft az sap HANA nagy példányon üzembe helyezhető összes adatot.
- Az SAP HANA nagy példány a Microsofttól kap.
- Hozzon létre egy Azure virtuális hálózat, amely csatlakozik a helyszíni hálózathoz.
- A HANA nagy példányok ExpressRoute-áramkörét ugyanahhoz az Azure virtuális hálózathoz csatlakoztatja.
- Telepíti az Azure virtuális gép, amely a HANA nagy példányok ugródobozként használható.
- Győződjön meg arról, hogy az ugródobozból csatlakozhat a HANA nagy példány egységhez, és fordítva.
- Ellenőrizze, hogy minden szükséges csomag és javítás telepítve van-e.
- Elolvasta az SAP-megjegyzéseket és dokumentációt a HANA telepítéséről a használt operációs rendszeren. Győződjön meg arról, hogy a hana kiadás a választás támogatott az operációs rendszer kiadása.

A következő szakasz ban egy példa a HANA telepítőcsomagok letöltése a jump box virtuális gépre. Ebben az esetben az operációs rendszer a Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Az SAP HANA telepítési bitek letöltése
A HANA nagy példány egységek nem közvetlenül csatlakozik az internethez. A telepítőcsomagok közvetlenül nem tölthetők le az SAP-ból a HANA nagy példány virtuális gépre. Ehelyett letölti a csomagokat a jump box virtuális gépre.

Szüksége van egy SAP S-felhasználó vagy más felhasználó, amely lehetővé teszi az SAP Piactér eléréséhez.

1. Jelentkezzen be, és nyissa meg az [SAP Service Marketplace-t.](https://support.sap.com/en/index.html) Válassza a**Szoftvertelepítések** >  >  **letöltése**és a Frissítés**betűrendes index szerint**lehetőséget. Ezután válassza **a H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **telepítés lehetőséget.** Töltse le a következő képernyőképen látható fájlokat.

   ![Képernyőkép a letöltandó fájlokról](./media/hana-installation/image16_download_hana.PNG)

2. Ebben a példában az SAP HANA 2.0 telepítőcsomagokat töltöttük le. Az Azure jump box virtuális gép, bontsa ki az önkicsomagoló archívumok a címtárban az alábbiak szerint.

   ![Képernyőkép az önkicsomagoló archívumról](./media/hana-installation/image17_extract_hana.PNG)

3. Az archívum okainak kinyerése után másolja a kinyerés által létrehozott könyvtárat (ebben az esetben az 51052030-at). Másolja a könyvtárat a HANA nagypéldány egység /hana/megosztott kötet egy létrehozott könyvtárba.

   > [!Important]
   > Ne másolja a telepítőcsomagokat a gyökér- vagy rendszerindító fősugárzóba, mert a hely korlátozott, és más folyamatoknak is használniuk kell.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Telepítse az SAP HANA-t a HANA nagy példányegységre
Az SAP HANA telepítéséhez jelentkezzen be felhasználói gyökérként. Csak a gyökér rendelkezik elegendő engedéllyel az SAP HANA telepítéséhez. Állítsa be az engedélyeket a könyvtárba, amelyet átmásolt a /hana/shared könyvtárba.

```
chmod –R 744 <Installation bits folder>
```

Ha az SAP HANA-t a grafikus felhasználói felület beállításával szeretné telepíteni, a gtk2 csomagot telepíteni kell a HANA nagy példányokra. Annak ellenőrzéséhez, hogy telepítve van-e, futtassa a következő parancsot:

```
rpm –qa | grep gtk2
```

(A későbbi lépésekben az SAP HANA telepítőjét a grafikus felhasználói felülettel jelenítjük meg.)

Lépjen be a telepítési könyvtárba, és navigáljon az alkönyvtárba HDB_LCM_LINUX_X86_64. 

Ebből a könyvtárból, indítsa el:

```
./hdblcmgui 
```
Ezen a ponton egy képernyősorozaton keresztül halad, amelyben megadja a telepítéshez tartozó adatokat. Ebben a példában telepítjük az SAP HANA adatbázis-kiszolgálót és az SAP HANA ügyfélösszetevőket. Ezért a választék unk AZ **SAP HANA Adatbázis**.

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, amelyen az SAP HANA-adatbázis van kiválasztva](./media/hana-installation/image18_hana_selection.PNG)

A következő képernyőn válassza az **Új rendszer telepítése**lehetőséget.

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, amelyen az Új rendszer telepítése lehetőség van kiválasztva](./media/hana-installation/image19_select_new.PNG)

Ezután válasszon a telepíthető további összetevők közül.

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, további összetevők listájával](./media/hana-installation/image20_select_components.PNG)

Itt választjuk ki az SAP HANA-ügyfelet és az SAP HANA Studio-t. Egy felskálázási példányt is telepítünk. Ezután válassza **az egyállomásos rendszert**. 

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, kijelölve az egyetlen állomásrendszer](./media/hana-installation/image21_single_host.PNG)

Ezután adjon meg néhány adatot.

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, amelynek rendszertulajdonságmezőit kell definiálni](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA-rendszerazonosító (SID) szerint ugyanazt a biztonsági azonosítót kell megadnia, mint a Microsoftnak, amikor megrendelte a HANA nagy példány központi telepítését. Ha másik BIZTONSÁGI azonosítót választ, a telepítés sikertelen lesz a különböző kötetekhozzáférési engedélyproblémái miatt.

A telepítési elérési úthoz használja a /hana/shared könyvtárat. A következő lépésben adja meg a hana adatfájlok és a HANA naplófájlok helyét.


![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, amely adat- és naplóterület-mezőket](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> A rendszertulajdonságok (két képernyővel ezelőtt) definiálásakor megadott sid-nek meg kell egyeznie a csatlakoztatási pontok sid-jével. Ha eltérés van, lépjen vissza, és állítsa be a biztonsági azonosítót a csatlakoztatási pontokon lévő értékhez.

A következő lépésben tekintse át az állomásnevét, és végül javítsa ki. 

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről állomásnévvel](./media/hana-installation/image24_review_host_name.PNG)

A következő lépésben is be kell olvasnia a Microsoftnak a HANA nagy példány központi telepítésének megrendelt adatait. 

![Képernyőkép az SAP HANA életciklus-kezelésről, amelynek definiálandó rendszergazdai mezőit](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Adja meg ugyanazt a **rendszergazdai felhasználói azonosítót** és **felhasználói csoport azonosítóját,** mint amelyet a Microsoft nak megadott, amikor megrendeli az egység központi telepítését. Ellenkező esetben az SAP HANA telepítése a HANA nagy példány egység sikertelen lesz.

A következő két képernyő itt nem jelenik meg. Lehetővé teszik, hogy adja meg a jelszót a RENDSZER felhasználó az SAP HANA adatbázis, és a jelszót a sapadm felhasználó. Ez utóbbi az SAP-kiszolgáló ügynök, amely az SAP HANA adatbázispéldány részeként lesz telepítve.

A jelszó megadása után megjelenik egy megerősítő képernyő. ellenőrizze az összes felsorolt adatot, és folytassa a telepítést. A telepítés előrehaladását dokumentáló folyamatjelző képernyőt érhet el, például ezt:

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, telepítési folyamatjelzőkkel](./media/hana-installation/image27_show_progress.PNG)

A telepítés befejezésekor egy ilyen képernyőt kell látnia:

![Képernyőkép az SAP HANA életciklus-kezelés képernyőjéről, amely azt jelzi, hogy a telepítés befejeződött](./media/hana-installation/image28_install_finished.PNG)

Az SAP HANA-példány nak most már készen kell lennie, és használatra kész. Képesnek kell lennie arra, hogy csatlakozzon hozzá az SAP HANA Studio. Győződjön meg arról is, hogy megkeresi és alkalmazza a legújabb frissítéseket.


## <a name="next-steps"></a>További lépések

- [AZ SAP HANA nagy példányai magas rendelkezésre állású és vészhelyreállítást jelentenek az Azure-ban](hana-overview-high-availability-disaster-recovery.md)

