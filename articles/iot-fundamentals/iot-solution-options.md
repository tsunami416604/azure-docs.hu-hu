---
title: Az Azure Internet of Things (IoT) megoldásbeállításai
description: Útmutató a platformszolgáltatások vagy a felügyelt alkalmazásplatform-megközelítés közötti választáshoz az IoT-megoldás létrehozásához. A platformszolgáltatás-megközelítés olyan szolgáltatásokat használ, mint az IoT Hub és a Digital Twins építőelemekként. A felügyelt alkalmazásplatform-megközelítés az IoT Central segítségével gyorsan elindítja.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 046116addf643d6c0eb0faf2f821b6ef78d440c6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77050339"
---
# <a name="choose-the-right-iot-solution"></a>A megfelelő IoT-megoldás kiválasztása

IoT-megoldás létrehozásához a vállalkozás általában úgy dönt, hogy használja a *platformszolgáltatások* vagy a *felügyelt alkalmazás platform* megközelítés.

A platformszolgáltatások a testreszabott és rugalmas IoT-alkalmazások építőköveit biztosítják. Az eszközök csatlakoztatásakor, valamint az adatok betöltésekor, tárolásakor és elemzésében további lehetőségek közül választhat. Az Azure IoT platformszolgáltatásai közé tartoznak az Azure IoT Hub és az Azure Digital Twins termékek.

A felügyelt alkalmazásplatform lehetővé teszi az alkalmazások gyorsabb készítését, mint a platformszolgáltatások az eredmények eléréséhez szükséges döntések számának csökkentésével. A felügyelt alkalmazásplatform gondoskodik a megoldás legtöbb eleméről, így az iparági ismeretek bővítésére, valamint az eszközök méretezésére és csatlakoztatására összpontosíthat. Az Azure IoT Central egy felügyelt alkalmazásplatform.

A két megközelítés közül való választáshoz vegye figyelembe a következőket:

- Hogyan szeretné kezelni a megoldást.
- Milyen szintű testreszabási és ellenőrzési kívánt felett a megoldást.
- Milyen árképzési struktúrát szeretne.

## <a name="management"></a>Kezelés

Hol szeretné eltölteni a rendszerfelügyeleti időt és erőforrásokat? 

- Válassza ki a platformszolgáltatások megközelítését, hogy teljes mértékben szabályozhassa a megoldás ban lévő mögöttes szolgáltatásokat. Például a következőket szeretné:

    - Igény szerint kezelheti a skálázást és a szolgáltatások biztonságossá tétele szolgáltatást.
    - Használja ki a házon belüli vagy partneri szakértelmet a fedélzeti eszközök és a szolgáltatások nyújtásához.

- Válassza ki a felügyelt alkalmazásplatform-megközelítést, hogy kihasználhassa az IoT-alkalmazások és -eszközök méretezését, biztonságát és felügyeletét kezelő platform előnyeit.

## <a name="control"></a>Vezérlés

A megoldás milyen elemeit szeretné testreszabni?

- Válassza ki a platformszolgáltatások megközelítését a teljes testreszabáshoz és a megoldásarchitektúra vezérléséhez.

- Válassza ki a felügyelt alkalmazásplatform-megközelítést a márkajelzés, az irányítópultok, a felhasználói szerepkörök, az eszközök és a telemetria testreszabásához. Azonban nem szeretné kezelni az alapul szolgáló IoT-rendszerfelügyeleti többletterhelést.

## <a name="pricing"></a>Díjszabás

Milyen árképzési struktúra felel meg leginkább az Igényeinek?

- Válassza ki a platform szolgáltatások megközelítés finomhangolásához szolgáltatások és az én teljes költségek.

- Válassza ki a felügyelt alkalmazásplatform-megközelítést egy egyszerű, kiszámítható árképzési struktúrához.

## <a name="summary"></a>Összefoglalás

A platformszolgáltatások megközelítése megfelelő egy olyan vállalkozás számára, amely felhőalapú megoldással és eszközszakértelemmel rendelkezik, és a következőket szeretné:

- Finomítsa a szolgáltatásokat a megoldásban.
- A megoldásban található szolgáltatások magas szintű ellenőrzése alatt áll.
- Teljesen testreszabhatja a megoldást.

A felügyelt alkalmazásplatform-megközelítés megfelelő egy olyan vállalkozás számára, amely:

- Nem akar kiterjedt erőforrásokat a rendszer tervezésére, fejlesztésére és kezelésére fordítani.
- Kiszámítható árképzési struktúrát szeretne.
- Szeretne néhány testreszabási képességet.

## <a name="next-steps"></a>További lépések

A különböző szolgáltatások és platformok átfogóbb magyarázatát, valamint használatuk módját az [Azure IoT-szolgáltatások és -technológiák](iot-services-and-technologies.md)című témakörben tetthet.

Ha többet szeretne megtudni a sikeres IoT-megoldások legfontosabb jellemzőiről, tekintse meg a [sikeres IoT-megoldások tanulmányának 8 attribútumát.](https://aka.ms/8attributes)

Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Microsoft Azure IoT-referenciaarchitektúra).
