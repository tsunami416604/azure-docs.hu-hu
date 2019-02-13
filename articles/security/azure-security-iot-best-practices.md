---
title: Dolgok ajánlott biztonsági eljárások az eszközök internetes |} A Microsoft Docs
description: A cikk a Microsoft Internet dolgot ajánlott biztonsági eljárások és általános javaslatok rendszerezett listáját.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 9413c0503c1b78550776d1c2f6ab8239205a788b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117867"
---
# <a name="internet-of-things-security-best-practices"></a>Eszközök internetes dolgot ajánlott biztonsági eljárások

Egy kritikus fontosságú vállalkozás bárki érintett az IoT-megoldások az eszközök internetes hálózata (IoT) infrastruktúra biztonságossá tétele. Az érintett eszközök száma, és ezek az eszközök elosztott jellege miatt az akár több millió IoT-eszközök biztonsági sérüléséhez kapcsolódó biztonsági esemény hatását nem triviális, és széles körű hatással lehet.

IoT-biztonság, ezért egy biztonsági jellegű megközelítéssel kell rendelkeznie. Kell biztonságos a felhőben, és áthelyezi azt a privát és nyilvános hálózatokon keresztül. Módszer, amely biztonságosan kiépítése az IoT-eszközök maguknak kell. Minden egyes réteg, az eszközről, a hálózathoz, a háttér-felhőbe kell erős biztonsági intézkedésekkel.

IoT – ajánlott eljárások a következő módon csoportosíthatók:

* IoT-hardvergyártótól vagy rendszerintegrátor
* IoT-megoldás fejlesztői
* IoT-megoldás deployer
* IoT-megoldás operátor

Ez a cikk összegzi [Internet a dolgok ajánlott biztonsági eljárások](../iot-suite/iot-security-best-practices.md). Tekintse meg, hogy a cikk további információt.

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT-hardvergyártótól vagy rendszerintegrátor

Ha Ön egy IoT-hardver gyártójához vagy egy hardveres integráló, kövesse az alábbi gyakorlati tanácsokat:

* **Minimális követelményeknek megfelelő hardver hatókör**: a hardvertervezést tartalmaznia kell a hardver, és semmi további művelethez szükséges minimális funkciók. 
* **Hogy a koncepció meghamisítása hardveres**: hozhat létre a mechanizmus észleli a fizikai illetéktelen módosításának hardverek, például nyissa meg az eszköz cover, eltávolítás, egy részét az eszközön, stb. 
* **Hozhat létre biztonságos hardver körül**: Ha [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) lehetővé teszik, biztonságos és titkosított tároló és a Platformmegbízhatósági modul-alapú rendszerindítás funkció biztonsági funkciókat hozhat létre.
* **Győződjön meg, a frissítések biztonságos**: az eszköz teljes élettartama során belső vezérlőprogram frissítése az elkerülhetetlen.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztői

Ha Ön egy IoT-megoldás fejlesztői, hajtsa végre az alábbi ajánlott eljárásokat:

* **Hajtsa végre a szoftvert biztonságos fejlesztési módszertanába**: biztonságos szoftverfejlesztés földön felfelé szem előtt tartva egészen a megvalósítási kezelésére, tesztelésére és üzembe helyezés, a kezdetektől a projekt biztonsági igényel.
* **Válassza ki a nyílt forráskódú szoftverek körültekintően**: megoldások gyors fejlesztése lehetőséget kínál a nyílt forráskódú szoftver.
* **Körültekintően integrálása**: számos, a szoftver biztonsági hibára derült címen a határt, könyvtárakat és API-k léteznek. 

## <a name="iot-solution-deployer"></a>IoT-megoldás deployer

Ha Ön egy IoT-megoldás deployer, kövesse az alábbi gyakorlati tanácsokat:

* **Hardver biztonságos üzembe**: IoT-telepítések hardver nem biztonságos helyen, például nyilvános szóközöket vagy felügyeletlen területi telepíteni lehet szükség.
* **Hitelesítési kulcsok biztonsága**: üzembe helyezés során az egyes eszközök megköveteli az eszköz azonosítóját, és hozzárendelt hitelesítési kulcsokat, a felhőalapú szolgáltatás által létrehozott. Biztonságban ezeket a kulcsokat fizikailag az üzembe helyezés után is. Feltört kulcs révén egy meglévő eszközt, hogy egy rosszindulatú eszköz is használható.

## <a name="iot-solution-operator"></a>IoT-megoldás operátor

Ha Ön az IoT-megoldás operátor, kövesse az alábbi gyakorlati tanácsokat:

* **Rendszerek naprakészen**: Győződjön meg arról, eszköz-operációsrendszerek és az összes eszközillesztőt frissítve lett, a legújabb verzióra. 
* **Kártékony tevékenységek ellen védelmet biztosító**: Ha engedélyezi az operációs rendszer, helyezze a legújabb víruskereső és kártevőirtó funkciókat, minden eszköz operációs rendszere. 
* **Gyakran az audit**: naplózás IoT-infrastruktúrát, a kapcsolódó biztonsági problémák kulcs akkor, ha a biztonsági incidensekre való válaszadást.
* **Az IoT-infrastruktúrájának védelme a fizikailag**: a legrosszabb biztonsági támadások ellen IoT-infrastruktúrát eszközökhöz való fizikai hozzáférés indul el.
* **Felhőhöz tartozó hitelesítő adatok védelme**: felhőbeli hitelesítő adatok konfigurálása és a egy IoT-környezet működtetése is esetleg hozzáférést és IoT-rendszer veszélyeztetheti a legegyszerűbb módja. 

