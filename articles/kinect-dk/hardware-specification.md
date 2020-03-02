---
title: Az Azure Kinect DK hardverspecifikációi
description: Ismerje meg az Azure Kinect DK összetevőit, specifikációit és képességeit.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specifikációk, hardver, DK, képességek, mélység, szín, RGB, IMO, mikrofon, sor, mélység
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: e0d42a3ce1dd9deb5e73500371c367134ca852e1
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619966"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Az Azure Kinect DK hardverspecifikációi

A cikk részletesen ismerteti, hogyan integrálja az Azure Kinect-hardver a Microsoft legmodernebb érzékelőtechnológiáit egyetlen USB-s kiegészítőbe.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Fogalmak

A cikkben az alábbi rövidítéseket használjuk.

- NFOV (Narrow field-of-view depth mode, keskeny látószögű mélységi üzemmód)
- WFOV (Wide field-of-view depth mode, széles látószögű mélységi üzemmód)
- FOV (field-of-view, látószög)
- FPS (Frames-per-second, képkocka másodpercenként)
- IMU (Inertial Measurement Unit, inerciális mérőegység)
- FoI (Field of Interest, vizsgált terület)

## <a name="product-dimensions-and-weight"></a>A termék méretei és súlya

Az Azure Kinect eszköz az alábbi méretekkel és súllyal rendelkezik.

- **Méretek**: 103 x 39 x 126 mm
- **Súly**: 440 g

![Az Azure Kinect DK méretei](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Üzemeltetési környezet

Az Azure Kinect DK-t fejlesztőknek és üzleti felhasználóknak szánjuk, a következő környezeti feltételek közötti üzemeltetéshez:

- **Hőmérséklet**: 10–25 °C
- **Páratartalom**: 8–90% (nem kicsapódó) relatív páratartalom

> [!NOTE]
> A fenti környezeti feltételektől eltérő használat az eszköz meghibásodásához és/vagy helytelen működéséhez vezethet. A fenti környezeti feltételek az eszköz közvetlen környezetére vonatkoznak, valamennyi üzemeltetési mód esetén. Külső házban történő használat esetén aktív hőmérséklet-szabályozással és/vagy egyéb hűtési megoldások alkalmazásával javasolt az eszköz paramétereit a megadott tartományokon belül tartani. Az eszközön egy hűtőcsatorna lett kialakítva az elülső rész és a hátsó zseb között. Az eszköz implementálása előtt ellenőrizze a hűtőcsatorna átjárhatóságát.

További információért tekintse meg a termék [biztonsági utasításait](https://support.microsoft.com/help/4023454/safety-information).

## <a name="depth-camera-supported-operating-modes"></a>A mélységi kamera támogatott üzemmódjai

Az Azure Kinect DK tartalmaz egy, a Microsoft által tervezett 1 megapixeles Time-of-Flight (ToF) mélységi kamerát, amely az [ISSCC 2018 konferencián bemutatott képérzékelőt](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018) használja. A mélységi kamera az alábbi üzemmódokat támogatja:

 | Mód            | Megoldás: | FoI       | FPS                | Működési tartomány* | Expozíciós idő |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV, nem kvantált   | 640 x 576    | 75° x 65°   | 0, 5, 15, 30       | 0,5–3,86 m       | 12,8 ms        |
| NFOV 2x2, kvantált (SW) | 320 x 288    | 75° x 65°   | 0, 5, 15, 30       | 0,5–5,46 m       | 12,8 ms        |
| WFOV 2x2, kvantált | 512 x 512    | 120° x 120° | 0, 5, 15, 30       | 0,25–2,88 m      | 12,8 ms        |
| WFOV, nem kvantált   | 1024 x 1024  | 120° x 120° | 0, 5, 15           | 0,25–2,21 m      | 20,3 ms        |
| Passzív infravörös      | 1024 x 1024  | N/A       | 0, 5, 15, 30       | N/A              | 1,6 ms         |

\*15–95%-os visszaverődés 850 nm-en, 2,2 μW/cm<sup>2</sup>/nm, véletlen hiba, szórás ≤ 17 mm, jellemző rendszerszintű hiba < 11 mm + a távolság 0,1%-a, többutas interferencia nélkül. A mélység értéke a fent jelzett működési tartományon kívül is eshet. Ez az objektum visszaverődési értékétől függ.

## <a name="color-camera-supported-operating-modes"></a>A színes kamera támogatott üzemmódjai

Az Azure Kinect DK egy OV12A10 12 megapixeles CMOS „rolling shutter” technológiás érzékelőt tartalmaz. A natív üzemmódok az alábbiakban láthatók:

|             RGB-kamerafelbontás (V x F)  |          Oldalarány  |          Formátumbeállítások   |          FPS  |          Névleges FOV (V x F) (utófeldolgozással)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840 x 2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       2560 x 1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1920 x 1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1280 x 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90° x 59°                              |
|       4096 x 3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90° x 74,3°                            |
|       2048 x 1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90° x 74,3°                            |

Az RGB-kamera USB Video-kompatibilis és a Sensor SDK nélkül is használható. Az RGB-kamera színtere: BT.601 a teljes tartományban [0...255]. 

> [!NOTE]
> A Sensor SDK színes képeket állít elő BGRA képpontformátumban. Ezt az üzemmódot az eszköz nem támogatja natív módon, így használata a processzor többletterheléséhez vezet. A gazdagép processzora konvertálja az eszközről érkező, MJPEG formátumú képeket.

## <a name="rgb-camera-exposure-time-values"></a>Az RGB-kamera expozíciós idői

Alább az RGB-kamera elfogadható kézi expozíciós értékeinek leképezése látható:

| exp| 2^exp | 50 Hz   |60 Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20 000|  16 670 |
|  -6|   15 625|  30000|  33 330 |
|  -5|   31 250|  40 000|  41 670 |
|  -4|   62 500|  50000|  50000 |
|  -3|  125 000|  60000|  66 670 |
|  -2|  250 000|  80 000|  83 330 |
|  -1|  500 000| 100 000| 100 000 |
|   0| 1 000 000| 120 000| 116 670 |
|   1| 2 000 000| 130 000| 133 330 |

## <a name="depth-sensor-raw-timing"></a>Mélységérzékelő nyers időzítése

Mélységi üzemmód | IR <br>impulzusok | Impulzus- <br>szélesség  | Tétlen <br>időszakok| Üresjárati idő | Expozíciós <br> Time
-|-|-|-|-|-
NFOV, nem kvantált <br>  NFOV 2xx, kvantált <br> WFOV 2x2, kvantált | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV, nem kvantált                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Kamera látószöge

A következő kép a mélységet és az RGB-kamera látószögét, vagyis az érzékelők által „látott” képet mutatja. Az ábra az RGB-kamera 4:3-as üzemmódjában készült.

![Kamera FOV](./media/resources/hardware-specs-media/camera-fov.png)

Ez a kép a kamera látószögét mutatja elölről, 2000 mm távolságban.

![Kamera FOV, elöl](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Ha a mélységmérés NFOV módban történik, az RGB-kamera képpontátfedése a 4:3-as oldalaránynál jobb, mint a 16:9-esnél.

## <a name="motion-sensor-imu"></a>Mozgásérzékelő (IMU)

A beépített LSM6DSMUS inerciális mérőegység (IMU) gyorsulásérzékelőt és giroszkópot is tartalmaz. A gyorsulásérzékelő és a giroszkóp mintavételezése egyszerre történik, 1,6 kHz-nél. A minták továbbítása a gazdagép felé 208 Hz-en történik.

## <a name="microphone-array"></a>Mikrofonsor

Az Azure Kinect DK kiváló minőségű, hét mikrofonból álló, kör alakú mikrofonsort tartalmaz, amely egy normál USB 2.0-hangeszköz. Mind a 7 csatorna elérhető. Teljesítményspecifikációk:

- Érzékenység: -22 dBFS (94 dB SPL, 1 kHz)
- Jel-zaj arány > 65 dB
- Akusztikus túlterhelési pont: 116 dB

![Mikrofonbuborék](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Az Azure Kinect DK egy USB3 rendszerű kompozit eszköz, amely az operációs rendszer felé a következő hardvervégpontokat biztosítja:

Szállítói azonosító: 0x045E (Microsoft). A termékazonosítót tartalmazó táblázatot lásd alább:

|    USB-interfész        |    PNP IP    |     Megjegyzések            |
|-------------------------|--------------|----------------------|
|    USB3.1 Gen1 Hub    |    0x097A    |    A fő elosztó    |
|    USB2.0 Hub         |    0x097B    |    HS USB          |
|    Mélységi kamera       |    0x097C    |    USB3.0            |
|    Színes kamera       |    0x097D    |    USB3.0            |
|    Mikrofonok        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Jelzőfények

Az eszköz előlapján jelzőfény mutatja, ha a kamera streamel, amit az eszköz programjában le lehet tiltani a Sensor SDK használatával.

Az eszköz hátulján lévő LED az eszköz állapotát jelzi:

| A jelzőfény színe     | Jelentése                                                   |
|-----------------------|------------------------------------------------------------|
| Folyamatos fehér           | Az eszköz be van kapcsolva és megfelelően működik.                         |
| Villogó fehér        | Az eszköz be van kapcsolva, de nincs USB 3.0-adatkapcsolat.   |
| Villogó borostyánsárga        | Az eszköz tápellátása nem elegendő a működtetéshez.               |
| Villogó borostyánsárga és fehér  | A belső vezérlőprogram frissítése vagy visszaállítása folyamatban van                    |

## <a name="power-device"></a>Az eszköz áramellátása

Az eszköz tápellátása kétféle módon biztosítható:

1. A mellékelt tápegység használatával. Az adatkapcsolat külön USB Type-C–Type-A-kábelen keresztül jön létre.
2. Type-C–Type-C-kábellel, az áramellátás és az adatkapcsolat biztosításához.

Az Azure Kinect DK-hoz gyárilag nincs mellékelve Type-C–Type-C-kábel.

> [!NOTE]
> - Az eszközhöz USB Type-A-tápkábel van mellékelve, amelyet az egyhelyes házcsatlakozóhoz kell csatlakoztatni. Ennek a kábelnek a másik végét a fali aljzathoz kell csatlakoztatni. Az eszköz több áram felvételére képes, mint amennyit két standard USB Type-A-port biztosít.
> - Fontos az USB-kábelek minősége, így javasoljuk jó minőségű kábelek használatát és azok működésének ellenőrzését az egység távolról történő üzembe helyezése előtt.

> [!TIP]
> A megfelelő Type-C–Type-C kábel kiválasztásának szempontjai:
> - Az [USB-tanúsítással rendelkező kábelnek](https://www.usb.org/products) támogatnia kell az adat- és az áramátvitelt is.
> - A passzív kábel legfeljebb 1,5 m-es legyen. Ha ennél hosszabb kábelre van szükség, használjon aktív kábelt. 
> - A kábelnek legalább 1,5 A-esnek kell lennie. Ellenkező esetben külső tápellátás csatlakoztatása szükséges.

A kábel ellenőrzése:

- Csatlakoztassa az eszközt a kábellel a gazdagéphez.
- Ellenőrizze, hogy minden eszköz megjelenik-e a Windows eszközkezelőjében. A mélységi és az RGB-kamerának az alábbi példának megfelelően kell megjelennie.

  ![Az Azure Kinect DK az Eszközkezelőben](./media/resources/hardware-specs-media/device-manager.png)

- Ellenőrizze az Azure Kinect Viewerben, hogy a kábel megbízhatóan képes-e az összes érzékelő adatainak streamelésére, a következő beállításokkal:

  - Mélységi kamera: NFOV, nem kvantált
  - RGB-kamera: 2160p
  - Mikrofonok és IMU engedélyezve

## <a name="what-does-the-light-mean"></a>Mit jelent a jelzőfény?

A tápellátásjelző LED az Azure Kinect DK hátulján található. A LED színe az eszköz állapotától függően változik.

![A kép az Azure Kinect DK hátulját mutatja. A három számozott képfelirat közül egy a LED-es jelzőfényhez, az alatta lévő kettő pedig a kábelekhez tartozik.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Az ábrán címkével azonosított részegységek:

1. Tápellátásjelző
1. Tápkábel (az áramforráshoz csatlakoztatva)
1. USB-C-adatkábel (a számítógéphez csatlakoztatva)

Győződjön meg arról, hogy a kábelek az ábrán látható módon vannak csatlakoztatva. Ezután tekintse meg az alábbi táblázatban, hogy a tápellátás visszajelzőjének különböző állapotai mit jelentenek.

|A jelzőfény színe |Jelentése |Teendő |
| ---| --- | --- |
|Folyamatos fehér |Az eszköz be van kapcsolva és megfelelően működik. |Használja az eszközt. |
|Nem világít |Ez az eszköz nem csatlakozik a számítógéphez. |Ellenőrizze, hogy a kerek tápkábel csatlakoztatva van-e az eszközhöz és az USB-adapterhez.<br /><br />Ellenőrizze, hogy az USB-C-kábel csatlakoztatva van-e az eszközhöz és a számítógéphez. |
|Villogó fehér |Az eszköz be van kapcsolva, de nincs USB 3.0-adatkapcsolat. |Ellenőrizze, hogy a kerek tápkábel csatlakoztatva van-e az eszközhöz és az USB-adapterhez.<br /><br />Ellenőrizze, hogy az USB-C-kábel csatlakoztatva van-e az eszközhöz és a számítógép valamelyik USB 3.0-portjához.<br /><br />Csatlakoztassa az eszközt a számítógép másik USB 3.0-portjához.<br /><br />A számítógépen nyissa meg az Eszközkezelőt (**Start menü** > **Vezérlőpult** > **Eszközkezelő**), és ellenőrizze, hogy a számítógép rendelkezik-e támogatott USB 3.0-gazdagépvezérlővel. |
|Villogó borostyánsárga |Az eszköz tápellátása nem elegendő a működtetéshez. |Ellenőrizze, hogy a kerek tápkábel csatlakoztatva van-e az eszközhöz és az USB-adapterhez.<br /><br />Ellenőrizze, hogy az USB-C-kábel csatlakoztatva van-e az eszközhöz és a számítógéphez. |
|Borostyánsárga, majd villogó fehér |Az eszköz be van kapcsolva, és belsővezérlőprogram-frissítést végez vagy a gyári beállításokat állítja vissza. |Várjon, amíg a lámpa folyamatos fehér színnel kezd el világítani. További információk: [Az Azure Kinect DK alaphelyzetbe állítása](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Áramfogyasztás

Az Azure Kinect DK teljesítményfelvétele legfeljebb 5,9 W; a konkrét áramfogyasztás a használattól függ.

## <a name="calibration"></a>Kalibrálás

Az Azure Kinect DK gyárilag kalibrálva van. A vizuális és inerciális érzékelők kalibrálási paramétereit programozott módon a Sensor SDK-n keresztül le lehet kérdezni.

## <a name="device-recovery"></a>Az eszköz visszaállítása

Az eszköz belső vezérlőprogramja visszaállítható az eredeti változatra a rögzítő alatti gomb használatával.

![Az Azure Kinect DK visszaállítási gombja](./media/resources/hardware-specs-media/recovery.png)

Az eszköz visszaállításához tekintse meg az [itt található utasításokat](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>További lépések

- [Az Azure Kinect Sensor SDK használata](about-sensor-sdk.md)
- [A hardver beállítása](set-up-azure-kinect-dk.md)
