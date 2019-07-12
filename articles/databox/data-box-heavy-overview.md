---
title: A Microsoft Azure Data Box Heavy áttekintése | Microsoft Docs in data
description: Ismerteti az Azure Data Box nevű hibrid megoldást, amellyel nagy mennyiségű adat továbbítható az Azure-ba.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 0f4657cdd71a104ca111f62a6e9757b5a33b46e8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592301"
---
# <a name="what-is-azure-data-box-heavy"></a>Mi az az Azure Data Box Heavy?

Az Azure Data Box (nagy erőforrásigényű) lehetővé teszi, hogy több száz terabájt adat gyors, költségkímélő és az Azure és megbízható módon küldhet. Az adatok az Azure-bA kerüljenek elküldünk Önnek az 1-PB tárolási kapacitás, amely adja meg az adatait, és a Microsoftnak egy Data Box nehéz eszközt. Az eszköz rendelkezik egy rugged kis-és védelemmel való ellátásához és az adatok védelme az átvitel során.

Miután az eszköz megkapta a helyi adatközpontban, állítsa be a helyi webes felhasználói felületen. Másolja át a kiszolgálóin található adatokat az eszközre, és küldje vissza az eszközt az Azure-nak. Az Azure-adatközpontban az adatok Azure Storage-fiókjában tároltuk van feltöltve. A teljes körű folyamat az Azure Portalon követheti.


> [!IMPORTANT]
> - Kérelem egy eszköz, jelentkezzen a [az Azure portal](https://portal.azure.com).


## <a name="use-cases"></a>Használati esetek

Data Box nehéz olyan adatok méretek a több száz terabájt, ahol a hálózati kapcsolat nem elegendő az adatok feltöltése az Azure-bA. Az adatáthelyezés lehet egyszeri, rendszeres, vagy egy kezdeti tömeges adatátvitelt követően rendszeres adatátvitel is. Íme a különböző forgatókönyvek, amelyekben a Data Box Heavy adatátvitelhez használható.

 - **Egyszeri migrálás** – nagy mennyiségű helyszíni adat áthelyezése az Azure-ba.
     - Helyezze át a médiatár offline szalagok egy online médiatár létrehozása Azure-bA.
     - Telepítse át a virtuális gép farm, az SQL server és az alkalmazások Azure-bA.
     - Részletes elemzés és a jelentés a HDInsight használatával helyezze át a korábbi adatok alapján az Azure-bA.

 - **Kezdeti tömeges átvitel** – Amikor a kezdeti tömeges átvitel a Data Box Heavy használatával történik (kiindulás), majd növekményes átvitelek történnek a hálózaton.
     - Például Data Box (nagy erőforrásigényű) és a biztonsági mentési megoldások partnerek segítségével helyezze át a kezdeti nagy korábbi biztonsági mentés az Azure-bA. Miután ez befejeződött, a növekményes adatok a hálózaton keresztül lesznek továbbítva az Azure Storage-ba.

 - **Rendszeres feltöltések** – ha rendszeresen keletkezik nagy mennyiségű adat, és azt az Azure-ba szükséges áthelyezni. Ez előfordulhat például az energiafeltárási munkálatoknál, ahol az olajfúrótornyok és a szélenergia-farmok kapcsán nagy mennyiségű videóanyag készül.

## <a name="benefits"></a>Előnyök

Data Box nehéz célja nagy mennyiségű adat Azure-bA együtt mozognak alig nincs hatással a hálózaton. A megoldás a következő előnyökkel jár:

- **Sebesség** -Data Box nehéz nagy teljesítményű 40-Gbps hálózati adaptereket használ.

- **Biztonsági** -Data Box nagy terhelésnek van az eszközön, adatok és a szolgáltatás beépített védelmet.
    - Az eszköz biztonsági tokkal rendelkezik illetéktelen hozzáférést gátló csavarokkal és az illetéktelen hozzáférést jelző matricákkal.
    - Az eszközön lévő adatokat minden pillanatban 256 bites AES-titkosítás védi.
    - Az eszköz zárolása csak az Azure Portalon megadott jelszóval oldható fel.
    - A szolgáltatást az Azure biztonsági funkciói védik.
    - Miután feltöltötte az adatok az Azure-ba, az eszközön a lemezek törlik a tartalmát tiszta, National Institute of Standards and Technology (NIST) 800-88r1 szabványoknak megfelelően.


## <a name="features-and-specifications"></a>Funkciók és specifikációk

A Data Box nehéz eszközt ebben a kiadásban a következő funkciókkal rendelkezik.

| Specifikációk                                          | Leírás              |
|---------------------------------------------------------|--------------------------|
| Tömeg                                                  | körülbelül 500 lbs. <br>A zárolás átvitel kerekeket eszköz|
| Dimenziók                                              | Szélesség: 26 hüvelyk Height: 28 hüvelyk hossza: 48 hüvelyk |
| Kiszolgálószekrény-terület                                              | Nem lehet az állványra szerelt|
| Szükséges kábelek                                         | 4 földelve 120 V / 10 A tápkábellel (5 – 15 NEMA) tartalmaz <br> Eszköz legfeljebb 240 V power támogatja, és C-13 power konténerek <br> Használja a hálózati kábel kompatibilis [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Energiagazdálkodási                                                    | 4 beépített energiagazdálkodási ellátási egység (PSUs) mindkét eszköz csomópontjai között megosztott <br> 1200 teljesítménymérő tipikus power rajzolása|
| Tárkapacitás                                        | ~ 1 PB nyers, 14 TB 70 lemezek <br> 770-TB felhasználható kapacitás|
| Csomópontok száma                                          | 2 független csomópont eszközönként (500 TB-os) |
| Hálózati adapterek száma csomópontonként                             | 4 hálózati adapterek száma csomópontonként <br><br> MGMT, DATA3 <ul><li> 2 x 1-GbE adapter </li><li> MGMT olyan felügyeleti és a kezdeti beállítás, nem felhasználó által konfigurálható </li><li> DATA3 a felhasználó által konfigurálható, és a Dynamic Host Configuration Protocol (DHCP) alapértelmezés szerint</li><li>Az 1-GbE hálózati adapterek is beállítható 10-GbE adapter</li></ul>Adat1, adat2 Adatfelületek <ul><li>2 x 40-GbE adapter </li><li> Felhasználó által konfigurálható (alapértelmezés) DHCP vagy statikus</li></ul>|


## <a name="components"></a>Összetevők

A Data Box Heavy a következő összetevőket tartalmazza:

* **Data Box Heavy eszköz** – Egy biztonsági tokban lévő fizikai eszköz, amely biztonságosan tárolja az adatokat. Az eszközt 770 TB használható tárolási kapacitása nem.
    
* **Data Box szolgáltatás** – Az Azure Portal bővítménye, amely segítségével a Data Box Heavy eszköz különböző földrajzi helyekről elérhető webes felületről kezelhető. A Data Box szolgáltatás segítségével felügyelheti a Data Box (nagy erőforrásigényű) eszközbe. A szolgáltatásban végrehajtható feladatok a rendelések létrehozását és kezelését, a riasztások megtekintését és kezelését, valamint a megosztások kezelését foglalják magukban.  

* **Helyi webes felhasználói felület** – Egy webes felhasználói felület, amelynek használatával az eszköz a helyi hálózathoz való csatlakozásra konfigurálható, majd regisztrálható a Data Box szolgáltatásban. A helyi webes felhasználói felülettel emellett leállíthatja és újraindíthatja az eszközt, megtekintheti a másolási naplókat, valamint szolgáltatáskérések elküldése érdekében kapcsolatba léphet a Microsoft ügyfélszolgálatával.


## <a name="the-workflow"></a>A munkafolyamat

A munkafolyamat általában az alábbi lépésekből áll:

1. **Megrendelés** – Hozzon létre egy megrendelést az Azure Portalon, adja meg a szállítási adatokat és az adatokhoz célként használandó Azure-tárfiókot. Ha az eszköz elérhető, az Azure előkészíti és postázza az eszközt egy nyomkövetési azonosítóval együtt.

2. **Átvétel** – Miután az eszköz megérkezett, csatlakoztassa a hálózatra és a tápellátáshoz a mellékelt kábelekkel. Kapcsolja be az eszközt, és csatlakozzon hozzá. Konfigurálja a hálózatot az eszközön, majd csatlakoztasson megosztásokat a gazdagépre, amelyről az adatokat másolni szeretné.

3. **Adatok másolása** – Másolja az adatokat a Data Box Heavy-megosztásokra.

4. **Visszaküldés** – Készítse elő, kapcsolja ki, majd küldje vissza az eszközt az Azure-adatközpontba.

5. **Feltöltés** – Az eszközről az adatok automatikusan át lesznek másolva az Azure-ba. Az eszköz lemezeit a National Institute of Standards and Technology (NIST) útmutatásait követve biztonságosan töröljük.

A folyamat során értesítést kap minden állapot módosul az e-mailen keresztül.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Data Box nagy adatátviteli alapján a régiót, amelyben szolgáltatás üzembe helyezése, ország/régió, amelyhez az eszköz tartalmazza a szükséges, és a célként megadott Azure storage-fiókot, ahol az adatok átvitelét.

- **A szolgáltatás rendelkezésre állása** – Ebben a kiadásban a Data Box Heavy a következő régiókban érhető el:
    - Az Egyesült Államok minden nyilvánosfelhő-régiója: az USA nyugati középső régiója, az USA 2. nyugati régiója, az USA nyugati régiója, az USA déli középső régiója, az USA középső régiója, az USA északi középső régiója, az USA keleti régiója és az USA 2. keleti régiója.
    - Európai Unió: Nyugat-Európa és Észak-Európa.
    - Egyesült Királyság: az Egyesült Királyság déli régiója és az Egyesült Királyság nyugati régiója.
    - Franciaország: Közép-Franciaország és Dél-Franciaország.

- **Cél tárfiókok** – Az adatokat tároló tárfiókok az összes olyan Azure-régióban elérhetők, amelyben a szolgáltatás is.

Régiónkénti elérhetőség a legfrissebb információk a Data Box nehéz, Ugrás [az Azure-termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Regisztráció

A következő lépésekkel regisztrálhat Data Box (nagy erőforrásigényű):

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.
2. Kattintson a **+ erőforrás létrehozása** új erőforrás létrehozásához. Keressen az **Azure Data Box** kifejezésre. Válassza az **Azure Data Box** szolgáltatást.
3. Kattintson a **Create** (Létrehozás) gombra.
4. Válassza ki a Data Box nehéz használni kívánt előfizetést. Válassza ki a régiót, ahol üzembe szeretné helyezni a Data Box Heavy-erőforrást. A **Data Box Heavy** lehetőségnél kattintson a **Regisztráció** elemre.
5. Válaszoljon a kérdésekre vonatkozó adatok tartózkodási ország/régió, időkeretet, cél Azure-szolgáltatás adatátvitel, a hálózati sávszélességet, valamint az adatok átvitele gyakorisága. Tekintse át az adatvédelmi és felhasználási feltételeket, és a négyzet bejelölésével járuljon hozzá, hogy a Microsoft használhassa az e-mail-címét az Önnel való kapcsolatfelvételhez.

Miután jelentkezett, egy Data Box nehéz rendezheti.

    
