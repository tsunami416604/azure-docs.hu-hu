---
title: A Microsoft Azure Data Box Heavy áttekintése | Microsoft Docs in data
description: Ismerteti az Azure Data Box nevű hibrid megoldást, amellyel nagy mennyiségű adat továbbítható az Azure-ba.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 362b7457538a16d389c3cc40fc44da19b073c0b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "70142918"
---
# <a name="what-is-azure-data-box-heavy"></a>Mi az az Azure Data Box Heavy?

Az Azure Data Box Heavy lehetővé teszi, hogy több száz terabájtnyi adatot küldjön az Azure-ba gyors, olcsó és megbízható módon. Az adatok átvitele az Azure-ba egy 1 PB-kapacitású Data Box Heavy eszköz szállításával történik, amelyet ön az adatokkal tölt ki, és visszaküld a Microsoftnak. A készülék masszív burkolattal rendelkezik, amely védi és védi az adatokat az átvitel során.

Miután az eszköz megkapta az adatközpontban, állítsa be a helyi webes felhasználói felület használatával. Másolja át a kiszolgálóin található adatokat az eszközre, és küldje vissza az eszközt az Azure-nak. Az Azure-adatközpontban az adatok feltöltésre kerül az Azure Storage-fiók(oka)ra. Nyomon követheti a teljes körű folyamat az Azure Portalon.


> [!IMPORTANT]
> - Eszköz igényléséhez regisztráljon az [Azure Portalon.](https://portal.azure.com)


## <a name="use-cases"></a>Használati esetek

A Data Box Heavy leginkább a több száz terabájtban lévő adatméretekhez a legmegfelelőbb, ahol a hálózati kapcsolat nem elegendő az adatok Azure-ba való feltöltéséhez. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is. Íme a különböző forgatókönyvek, amelyekben a Data Box Heavy adatátvitelhez használható.

 - **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba.
     - A médiatár áthelyezése az offline szalagokból az Azure-ba online médiatár létrehozásához.
     - Telepítse át a virtuálisgép-farmot, az SQL-kiszolgálót és az alkalmazásokat az Azure-ba.
     - Korábbi adatok áthelyezése az Azure-ba a HDInsight használatával végzett részletes elemzéshez és jelentéshez.

 - **Kezdeti tömeges átvitel** – Amikor a kezdeti tömeges átvitel a Data Box Heavy használatával történik (kiindulás), majd növekményes átvitelek történnek a hálózaton.
     - Például a Data Box Heavy és egy biztonsági mentési megoldások partnere a kezdeti nagy előzménybiztonsági mentés azure-ba való áthelyezéséhez használatos. Miután ez befejeződött, a növekményes adatok a hálózaton keresztül lesznek továbbítva az Azure Storage-ba.

 - **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.

## <a name="benefits"></a>Előnyök

A Data Box Heavy úgy lett kialakítva, hogy hatalmas mennyiségű adatot helyezzen át az Azure-ba, és kevés vagy semmilyen hatással nem lesz a hálózatra. A megoldás a következő előnyökkel jár:

- **Speed** - A Data Box Heavy nagy teljesítményű, 40 Gbit/s-os hálózati interfészeket használ.

- **Biztonság** – A Data Box Heavy beépített biztonsági védelemmel rendelkezik az eszköz, az adatok és a szolgáltatás számára.
    - Az eszköz biztonsági tokkal rendelkezik illetéktelen hozzáférést gátló csavarokkal és az illetéktelen hozzáférést jelző matricákkal.
    - Az eszközön lévő adatokat minden pillanatban 256 bites AES-titkosítás védi.
    - Az eszköz zárolása csak az Azure Portalon megadott jelszóval oldható fel.
    - A szolgáltatást az Azure biztonsági funkciói védik.
    - Az adatok Azure-ba való feltöltését követően az eszközön lévő lemezek et a National Institute of Standards and Technology (NIST) 800-88r1 szabványainak megfelelően törli a rendszer.


## <a name="features-and-specifications"></a>Funkciók és specifikációk

A Data Box Heavy eszköz a következő funkciókkal rendelkezik ebben a kiadásban.

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Tömeg                                                  | - 500 font. <br>A rögzítőkerekekre szolgáló eszköz szállításhoz|
| Dimenziók                                              | Szélesség: 26 hüvelyk Magasság: 28 hüvelyk Hossz: 48 hüvelyk |
| Kiszolgálószekrény-terület                                              | Nem lehet rackbe szerelni|
| Szükséges kábelek                                         | 4 földelt 120 V / 10 A tápkábel (NEMA 5-15) mellékelve <br> A készülék akár 240 V-os teljesítményt is támogat, és C-13 tápellátással rendelkezik <br> [Mellanox MCX314A-BCCT-vel](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) kompatibilis hálózati kábelek használata  |
| Power                                                    | 4 beépített tápegység (PSUs) megosztva mindkét eszközcsomóponton <br> 1200 wattos tipikus teljesítményhúzás|
| Tárkapacitás                                        | ~ 1-PB nyers, 70 lemezek 14 TB minden <br> 770-TB felhasználható kapacitás|
| Csomópontok száma                                          | 2 független csomópont eszközönként (500 TB egyenként) |
| Hálózati adapterek csomópontonként                             | 4 hálózati adapter csomópontonként <br><br> MGMT, ADAT3 <ul><li> 2 X 1-GbE interfészek </li><li> Az MGMT a kezeléshez és a kezdeti beállításhoz használható, nem a felhasználó által konfigurálható </li><li> A DATA3 alapértelmezés szerint felhasználó által konfigurálható és dinamikus állomáskonfigurációs protokoll (DHCP)</li></ul>DATA1, DATA2 adatinterfészek <ul><li>2 X 40-GbE interfészek </li><li> A felhasználó konfigurálható DHCP-hez (alapértelmezett) vagy statikus</li></ul>|


## <a name="components"></a>Összetevők

A Data Box Heavy a következő összetevőket tartalmazza:

* **Data Box Heavy eszköz** – Egy biztonsági tokban lévő fizikai eszköz, amely biztonságosan tárolja az adatokat. Ez a készülék 770 TB felhasználható tárolókapacitással rendelkezik.
    
* **Data Box szolgáltatás** – Az Azure Portal bővítménye, amely segítségével a Data Box Heavy eszköz különböző földrajzi helyekről elérhető webes felületről kezelhető. A Data Box szolgáltatás segítségével felügyelheti a Data Box Heavy eszközt. A szolgáltatásban végrehajtható feladatok a rendelések létrehozását és kezelését, a riasztások megtekintését és kezelését, valamint a megosztások kezelését foglalják magukban.  

* **Helyi webes felhasználói felület** – Egy webes felhasználói felület, amelynek használatával az eszköz a helyi hálózathoz való csatlakozásra konfigurálható, majd regisztrálható a Data Box szolgáltatásban. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja az eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.


## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Megrendelés** – Hozzon létre egy megrendelést az Azure Portalon, adja meg a szállítási adatokat és az adatokhoz célként használandó Azure-tárfiókot. Ha az eszköz elérhető, az Azure előkészíti és postázza az eszközt egy nyomkövetési azonosítóval együtt.

2. **Átvétel** – Miután az eszköz megérkezett, csatlakoztassa a hálózatra és a tápellátáshoz a mellékelt kábelekkel. Kapcsolja be az eszközt, és csatlakozzon hozzá. Konfigurálja a hálózatot az eszközön, majd csatlakoztasson megosztásokat a gazdagépre, amelyről az adatokat másolni szeretné.

3. **Adatok másolása** – Másolja az adatokat a Data Box Heavy-megosztásokra.

4. **Visszaküldés** – Készítse elő, kapcsolja ki, majd küldje vissza az eszközt az Azure-adatközpontba.

5. **Feltöltés** – Az eszközről az adatok automatikusan át lesznek másolva az Azure-ba. Az eszköz lemezeit a National Institute of Standards and Technology (NIST) útmutatásait követve biztonságosan töröljük.

A folyamat során e-mailben értesítjük az összes állapotváltozásról.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Data Box Heavy adatokat továbbíthat a szolgáltatás üzembe helyezésének régiójától, az on-t az eszköz szállításának országa/régiója és a cél Azure-tárfiók alapján, ahol az adatokat továbbítja.

- **A szolgáltatás rendelkezésre állása** – Ebben a kiadásban a Data Box Heavy a következő régiókban érhető el:
    - Az Egyesült Államok minden nyilvánosfelhő-régiója: az USA nyugati középső régiója, az USA 2. nyugati régiója, az USA nyugati régiója, az USA déli középső régiója, az USA középső régiója, az USA északi középső régiója, az USA keleti régiója és az USA 2. keleti régiója.
    - Európai Unió: Nyugat-Európa és Észak-Európa.
    - Egyesült Királyság: az Egyesült Királyság déli régiója és az Egyesült Királyság nyugati régiója.
    - Franciaország: Közép-Franciaország és Dél-Franciaország.

- **Cél tárfiókok** – Az adatokat tároló tárfiókok az összes olyan Azure-régióban elérhetők, amelyben a szolgáltatás is.

A Data Box Heavy régióelérhetőségével kapcsolatos legfrissebb információkért látogasson el az [Azure-termékek régiónkénti elérhetőségére.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

## <a name="sign-up"></a>Regisztráció

A Data Box Heavy-ra való regisztrációhoz tegye a következő lépéseket:

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.
2. Új erőforrás létrehozásához kattintson **a + Erőforrás létrehozása** elemre. Keressen az **Azure Data Box** kifejezésre. Válassza az **Azure Data Box** szolgáltatást.
3. Kattintson **a Létrehozás gombra.**
4. Válassza ki a Data Box Heavy-hoz használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Heavy-erőforrást. A **Data Box Heavy** lehetőségnél kattintson a **Regisztráció** elemre.
5. Válaszoljon az adatok szerinti lakóhely országával/régiójával, az időkerettel, az adatátvitelre, a hálózati sávszélességre és az adatátviteli gyakoriságra vonatkozó célAzure-szolgáltatással kapcsolatos kérdésekre. Tekintse át az adatvédelmi és felhasználási feltételeket, és a négyzet bejelölésével járuljon hozzá, hogy a Microsoft használhassa az e-mail-címét az Önnel való kapcsolatfelvételhez.

Miután feliratkozott, megrendelheti a Data Box Heavy-t.

    
