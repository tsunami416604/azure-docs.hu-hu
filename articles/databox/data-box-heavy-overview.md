---
title: A Microsoft Azure Data Box Heavy áttekintése | Microsoft Docs in data
description: Ismerteti az Azure Data Box nevű hibrid megoldást, amellyel nagy mennyiségű adat továbbítható az Azure-ba.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 8ecef86841bcf13a469f9c0dc81f114bd54acdba
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946516"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Mi az az Azure Data Box Heavy? (Előzetes verzió)

A Microsoft Azure Data Box hibrid megoldással gyorsan, olcsón és megbízható módon küldhet több száz terabájtnyi adatot az Azure-ba. Az adatok gyors és biztonságos átviteléhez egy 1 PB tárolási kapacitású, saját fejlesztésű tárolóeszközt biztosítunk Önnek. Az eszköz ütésálló tokkal rendelkezik, hogy az adatok az átvitel során védve legyenek.

A Data Box Heavy jelenleg előzetes verzióban érhető el, és az Azure Portalon regisztrálva igényelheti az eszközt. Miután az eszköz megérkezett az adatközpontba, a helyi webes felhasználói felületen konfigurálhatja. Másolja át a kiszolgálóin található adatokat az eszközre, és küldje vissza az eszközt az Azure-nak. Az Azure-adatközpontban az adatok automatikusan feltöltődnek az eszközről az Azure-ba. A teljes folyamatot végigkövetheti a Data Box szolgáltatásban, az Azure Portalon.


> [!IMPORTANT]
> - A Data Box Heavy előzetes verzióban érhető el. A megoldás üzembe helyezése előtt tekintse át [az Azure az előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Eszköz igényléséhez regisztráljon a [Betekintő portálon](http://aka.ms/).
> - Az előzetes verzió ideje alatt a Data Box Heavy az USA és az Európai Unió területén lévő ügyfelek számára szállítható ki. További információt a [Regionális elérhetőség](#region-availability) témakörben talál.

## <a name="use-cases"></a>Használati esetek

A Data Box Heavy ideális megoldás 500 TB-nál nagyobb mennyiségű adat átviteléhez az olyan forgatókönyvek esetében, ahol nincs hálózati kapcsolat, vagy korlátozott. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is. Íme a különböző forgatókönyvek, amelyekben a Data Box Heavy adatátvitelhez használható.

 - **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba. 
     - Médiatár áthelyezése offline szalagokról az Azure-ba és egy online médiatár létrehozása.
     - A VM-farm, az SQL Server és az alkalmazások migrálása az Azure-ba
     - Előzményadatok áthelyezése az Azure-ba a HDInsight használatával történő átfogó elemzéshez és jelentéskészítéshez

 - **Kezdeti tömeges átvitel** – Amikor a kezdeti tömeges átvitel a Data Box Heavy használatával történik (kiindulás), majd növekményes átvitelek történnek a hálózaton. 
     - Például a biztonsági mentési partnerek, mint a Commvault vagy a Data Box Heavy segítségével történik a nagy méretű biztonsági mentési előzményadatok kezdeti átvitele az Azure-ba. Miután ez befejeződött, a növekményes adatok a hálózaton keresztül lesznek továbbítva az Azure Storage-ba.

 - **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.      

## <a name="benefits"></a>Előnyök

A Data Box Heavy nagy mennyiségű adat az Azure-ba való átvitelére szolgál, amely minimális hatással van a hálózat működésére. A megoldás a következő előnyökkel jár:

- **Sebesség** – A Data Box Heavy nagy teljesítményű, 40 Gb/s sebességű hálózati adaptereket használ.

- **Biztonság** – A Data Box Heavy beépített védelmet biztosít az eszköz, az adatok és a szolgáltatás számára.
    - Az eszköz biztonsági tokkal rendelkezik illetéktelen hozzáférést gátló csavarokkal és az illetéktelen hozzáférést jelző matricákkal. 
    - Az eszközön lévő adatokat minden pillanatban 256 bites AES-titkosítás védi.
    - Az eszköz zárolása csak az Azure Portalon megadott jelszóval oldható fel.
    - A szolgáltatást az Azure biztonsági funkciói védik.
    - Az adatok az Azure-ba való feltöltését követően az eszköz lemezeinek tartalmát az NIST 800-88r1 szabványoknak megfelelően töröljük.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Összetevők

A Data Box Heavy a következő összetevőket tartalmazza:

* **Data Box Heavy eszköz** – Egy biztonsági tokban lévő fizikai eszköz, amely biztonságosan tárolja az adatokat. Az eszköz hasznos tárkapacitása 800 TB. 

    
* **Data Box szolgáltatás** – Az Azure Portal bővítménye, amely segítségével a Data Box Heavy eszköz különböző földrajzi helyekről elérhető webes felületről kezelhető. A Data Box szolgáltatással végezheti a Data Box Heavy eszköz napi adminisztrálását. A szolgáltatásban végrehajtható feladatok a rendelések létrehozását és kezelését, a riasztások megtekintését és kezelését, valamint a megosztások kezelését foglalják magukban.  

* **Helyi webes felhasználói felület** – Egy webes felhasználói felület, amelynek használatával az eszköz a helyi hálózathoz való csatlakozásra konfigurálható, majd regisztrálható a Data Box szolgáltatásban. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja az eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.


## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Megrendelés** – Hozzon létre egy megrendelést az Azure Portalon, adja meg a szállítási adatokat és az adatokhoz célként használandó Azure-tárfiókot. Ha az eszköz elérhető, az Azure előkészíti és postázza az eszközt egy nyomkövetési azonosítóval együtt.

2. **Átvétel** – Miután az eszköz megérkezett, csatlakoztassa a hálózatra és a tápellátáshoz a mellékelt kábelekkel. Kapcsolja be az eszközt, és csatlakozzon hozzá. Konfigurálja a hálózatot az eszközön, majd csatlakoztasson megosztásokat a gazdagépre, amelyről az adatokat másolni szeretné.

3. **Adatok másolása** – Másolja az adatokat a Data Box Heavy-megosztásokra.

4. **Visszaküldés** – Készítse elő, kapcsolja ki, majd küldje vissza az eszközt az Azure-adatközpontba.

5. **Feltöltés** – Az eszközről az adatok automatikusan át lesznek másolva az Azure-ba. Az eszköz lemezeit a National Institute of Standards and Technology (NIST) útmutatásait követve biztonságosan töröljük.

A folyamat alatt minden lépéséről e-mailben kap értesítést. 

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Data Box Heavy az adatok átvitelét a szolgáltatás üzembehelyezési régiója, az eszköz szállítási célországa és az adatátvitel célját képező Azure-tárfiók alapján végzi. 

- **A szolgáltatás rendelkezésre állása** – Ebben a kiadásban a Data Box Heavy a következő régiókban érhető el:
    - Az Egyesült Államok minden nyilvánosfelhő-régiója: az USA nyugati középső régiója, az USA 2. nyugati régiója, az USA nyugati régiója, az USA déli középső régiója, az USA középső régiója, az USA északi középső régiója, az USA keleti régiója és az USA 2. keleti régiója.
    - Európai Unió: Nyugat-Európa és Észak-Európa.
    - Egyesült Királyság: az Egyesült Királyság déli régiója és az Egyesült Királyság nyugati régiója.
    - Franciaország: Közép-Franciaország és Dél-Franciaország.

- **Cél tárfiókok** – Az adatokat tároló tárfiókok az összes olyan Azure-régióban elérhetők, amelyben a szolgáltatás is. 

## <a name="sign-up"></a>Regisztráció

A Data Box Heavy előzetes verzióban érhető el, és a használatához regisztráció szükséges. A Data Box Heavy szolgáltatáshoz való regisztrációhoz hajtsa végre a következő lépéseket:

1. Jelentkezzen be az Azure Portalra a https://aka.ms/azuredatabox címen.
2. A **+** elemre kattintva hozzon létre egy új erőforrást. Keressen az **Azure Data Box** kifejezésre. Válassza az **Azure Data Box** szolgáltatást.

    <!--![The Data Box Heavy sign up 1]()-->

3. Kattintson a **Create** (Létrehozás) gombra.

    <!--![The Data Box Heavy sign up 2]()-->

4. Válassza ki a Data Box Heavy előzetes verziójához használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Heavy-erőforrást. A **Data Box Heavy** lehetőségnél kattintson a **Regisztráció** elemre.

   <!--![The Data Box Heavy sign up 3]()-->

5. Válaszoljon a kérdésekre az adatok tárolási helye szerinti országról, az időkeretről, az adatátviteli cél Azure-szolgáltatásról, a hálózati sávszélességről, valamint az adatátviteli gyakoriságról. Tekintse át az adatvédelmi és felhasználási feltételeket, és a négyzet bejelölésével járuljon hozzá, hogy a Microsoft használhassa az e-mail-címét az Önnel való kapcsolatfelvételhez.

    <!--![The Data Box Heavy sign up 4]()-->

Miután regisztrált, és igénybe veheti az előzetes verziót, megrendelheti a Data Box Heavy eszközt.




