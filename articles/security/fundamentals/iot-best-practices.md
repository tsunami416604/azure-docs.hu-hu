---
title: eszközök internetes hálózata biztonság – ajánlott eljárások | Microsoft Docs
description: A cikk a Microsoft eszközök internetes hálózata ajánlott biztonsági eljárásainak és általános ajánlásainak a válogatott listáját tartalmazza.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 14f1905b1acd60acdf7b3dad0686e3fa34629953
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727056"
---
# <a name="internet-of-things-security-best-practices"></a>eszközök internetes hálózata ajánlott biztonsági eljárások

A eszközök internetes hálózata-(IoT-) infrastruktúra biztonságossá tétele kritikus fontosságú vállalkozás a IoT-megoldásokkal foglalkozó felhasználók számára. Az érintett eszközök száma és az eszközök elosztott jellege miatt a több millió IoT-eszköz sérülésével kapcsolatos biztonsági események nem triviálisak, és széleskörű hatással lehetnek a szolgáltatásra.

Emiatt a IoT biztonságának részletes megközelítésre van szüksége. Az adatnak biztonságosnek kell lennie a felhőben, és a magán-és nyilvános hálózatokon halad át. A IoT-eszközök biztonságos kiépítéséhez szükség van a metódusokra. Az eszközökről, a hálózatra és a Felhőbeli háttérrendszer minden rétegének erős biztonsági garanciára van szüksége.

A IoT ajánlott eljárásai a következő módon foglalhatók le:

* IoT hardver gyártója vagy integrátora
* IoT-megoldás fejlesztője
* IoT-megoldás telepítője
* IoT-megoldás operátora

Ez a cikk [eszközök internetes hálózata ajánlott biztonsági eljárásokat](/azure/iot-fundamentals/iot-security-best-practices)foglalja össze. Részletesebb információkért tekintse meg ezt a cikket.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT hardver gyártója vagy integrátora

Ha Ön IoT hardveres vagy hardveres integrátor, kövesse az alábbi ajánlott eljárásokat:

* A **minimális követelmények hatókörének hardvere**: a hardver kialakításának tartalmaznia kell a hardver működéséhez szükséges minimális funkciókat, és semmi más. 
* **Hardveres illetéktelenség igazolása**: a hardver fizikai illetéktelen módosításának észleléséhez, például az eszköz fedõlapjának megnyitásához, az eszköz egy részének eltávolításához stb. 
* A **biztonságos hardverek kiépítése**: Ha az [ELÁBÉ](https://en.wikipedia.org/wiki/Cost_of_goods_sold) lehetővé teszi, olyan biztonsági funkciókat építhet ki, mint például a biztonságos és titkosított tárolási és platformmegbízhatósági modul (TPM) alapú rendszerindítási funkció.
* **Frissítések biztonságossá tétele**: a belső vezérlőprogram frissítése az eszköz élettartama során elkerülhetetlen.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztője

Ha Ön egy IoT-megoldás fejlesztője, kövesse az alábbi ajánlott eljárásokat:

* A **biztonságos**szoftverfejlesztés módszerének követése: a biztonságos szoftverek fejlesztése megköveteli a projekt kezdetétől való felépítését, amely az alkalmazás megvalósítására, tesztelésére és üzembe helyezésére irányul.
* **Válassza ki a nyílt forráskódú szoftvereket: a**nyílt forráskódú szoftverek lehetőséget biztosítanak a megoldások gyors fejlesztésére.
* **Integráció a Care szolgáltatással**: a különböző szoftveres biztonsági hibák a könyvtárak és API-k határán találhatók. 

## <a name="iot-solution-deployer"></a>IoT-megoldás telepítője

Ha Ön egy IoT-megoldás telepítője, kövesse az alábbi ajánlott eljárásokat:

* **Hardver biztonságos üzembe helyezése**: A IoT üzemelő példányokhoz hardvert kell telepíteni a nem biztonságos helyeken, például a nyilvános helyeken vagy a nem felügyelt területi beállításokban.
* **Hitelesítő kulcsok biztonságos megőrzése**: az üzembe helyezés során minden eszközön a Cloud Service által generált eszköz-azonosítók és társított hitelesítési kulcsok szükségesek. Ezeket a kulcsokat a telepítés után is fizikailag biztonságban kell tartani. A kártékony eszköz feltört kulcsát meglévő eszközként való maszkolásra is használhatja.

## <a name="iot-solution-operator"></a>IoT-megoldás operátora

Ha Ön egy IoT-megoldás operátora, kövesse az alábbi ajánlott eljárásokat:

* A **rendszerek**naprakészen tartása: gondoskodjon arról, hogy az eszközök operációs rendszerei és az összes eszközillesztő frissítve legyen a legújabb verzióra. 
* **Rosszindulatú tevékenység elleni védelem**: Ha az operációs rendszer engedélyezi, helyezze a legújabb víruskereső és kártevő szoftverek elleni képességeket minden eszköz operációs rendszerre. 
* **Gyakori ellenőrzés**: a biztonsági incidensekre adott válasz esetén a IoT-infrastruktúra naplózása a biztonsággal kapcsolatos problémák esetén kulcsfontosságú.
* **A IoT-infrastruktúra fizikai védelme**: az IoT-infrastruktúra legrosszabb biztonsági támadásait az eszközök fizikai elérésével lehet elindítani.
* **Felhőbeli hitelesítő adatok biztosítása**: a IoT-telepítés konfigurálásához és üzemeltetéséhez használt Felhőbeli hitelesítési hitelesítő adatok valószínűleg a legegyszerűbben a IoT rendszer elérésének és sérülésének megtámadására szolgálnak. 

