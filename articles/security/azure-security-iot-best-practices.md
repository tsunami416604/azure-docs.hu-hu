---
title: Dolgot ajánlott biztonsági eljárások az eszközök internetes |} Microsoft Docs
description: A cikk a Microsoft Internet dolgot ajánlott biztonsági eljárások és általános javaslatok válogatott listáját tartalmazza.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 804dd6828931ca2801360ab18be938c8bacf2bfa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185340"
---
# <a name="internet-of-things-security-best-practices"></a>Eszközök internetes dolgot ajánlott biztonsági eljárások

A az eszközök internetes hálózatát (IoT) infrastruktúra védelmének biztosítása a felhasználók az IoT-megoldások szerepet játszó kritikus vállalkozás. Az érintett eszközök száma és az elosztott jellege ezeket az eszközöket biztonsági esemény kapcsolatos hibát okoz az IoT-eszközök millióira hatása nem triviális, és széleskörű hatással lehet.

Emiatt az IoT biztonsági kell a biztonsági jellegű megközelítést. Biztonságos kell a felhőben és a privát és nyilvános hálózatokon átvitel során. Módszerek kell, hogy magukat az IoT-eszközök biztonságosan kiépítéséhez. Minden egyes rétegben, eszköz, a hálózathoz, a háttér-felhőbe erős biztonságot biztosítékok kell.

Az IoT-ajánlott eljárások a következő módon csoportosíthatók:

* Az IoT-hardvergyártó vagy integráló
* Az IoT-megoldás fejlesztői
* Az IoT-megoldás deployer
* Az IoT-megoldás operátor

Ez a cikk összefoglalja [Internet a dolgok ajánlott biztonsági eljárások](../iot-suite/iot-security-best-practices.md). Tekintse meg az adott cikkhez részletes információt.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Az IoT-hardvergyártó vagy integráló

Ha Ön egy IoT hardver gyártás vagy egy hardveres integráló, kövesse az alábbi gyakorlati tanácsokra:

* **Hatókör hardvereszközeit úgy, hogy a minimális követelmények**: a hardver tervezési tartalmaznia kell a hardvert, és semmi további művelethez szükséges minimális funkciók. 
* **Ellenőrizze a hardver igazolása védelmet**: build a mechanizmusok észleléséhez fizikai illetéktelen módosításának hardverek, például a megnyitása az eszköz borítóján eltávolítása egy részét az adott eszköz feloldásához, stb. 
* **Biztonságos hardveren körül Build**: Ha [ELÁBÉ](https://en.wikipedia.org/wiki/Cost_of_goods_sold) lehetővé teszik, készíthetnek biztonsági funkciókat, például a biztonságos és titkosított tárolási és a platformmegbízhatósági modul TPM-alapú rendszerindítási funkcióit.
* **Frissítések biztonságosabbá teheti**: előbb vagy utóbb elkerülhetetlenné belső vezérlőprogram frissítése az eszköz élettartama alatt.

## <a name="iot-solution-developer"></a>Az IoT-megoldás fejlesztői

Az IoT-megoldás fejlesztők, kövesse az alábbi gyakorlati tanácsokat:

* **Hajtsa végre a biztonságos software development módszert**: ground felfelé továbbléphetnek a kezdetektől a projekt a biztonság egészen a végrehajtási, a tesztelés és a telepítése a biztonságos szoftver fejlesztése igényel.
* **A kiválasztott nyílt forráskódú szoftver körültekintően**: gyorsan a megoldások fejlesztése lehetőséget kínál a nyílt forráskódú szoftvereket.
* **Integrálható, így gondossággal járjanak**: a szoftver biztonsági hiányosságokat számos lehet létrehozni a határ könyvtárakat és API-k. 

## <a name="iot-solution-deployer"></a>Az IoT-megoldás deployer

Ha Ön egy IoT-megoldás deployer, kövesse az alábbi gyakorlati tanácsokat:

* **Hardver biztonságos telepítése**: IoT központi telepítések szükség lehet a hardvert, hogy nem biztonságos helyen, például a nyilvános szóközt vagy felügyeletlen területi telepíthető.
* **Hitelesítési kulcsok biztonsága**: a telepítés során minden egyes eszköz eszközazonosítókat igényel, és hozzárendelt hitelesítési kulcsokat, a felhőalapú szolgáltatás által létrehozott. Ezek a kulcsok fizikailag biztonságos tartsa a telepítés után is. Feltört kulcs segítségével egy rosszindulatú eszköz helyettesítő meglévő eszközként.

## <a name="iot-solution-operator"></a>Az IoT-megoldás operátor

Ha IoT-megoldás kezelőként, kövesse az alábbi gyakorlati tanácsokat:

* **Naprakészen rendszerek**: Győződjön meg arról, eszköz-operációsrendszerek és az összes eszközillesztőt frissítése a legújabb verzióra. 
* **Rosszindulatú tevékenységhez elleni**: lehetővé teszi az operációs rendszer, ha minden eszköz operációs rendszere helyezze a legújabb kártevőirtó- és víruskereső-funkciókat. 
* **Naplózási gyakran**: naplózás IoT infrastruktúra pedig biztonsági kapcsolatos problémák esetén kulcs-biztonsági incidensekre válaszol.
* **Az IoT-infrastruktúra fizikailag védelme**: a legrosszabb biztonsági támadások ellen IoT infrastruktúra fizikai hozzáférés eszközökhöz indul el.
* **Felhő hitelesítő adatok védelme**: Felhő hitelesítő adatok konfigurálása és az IoT központi telepítése operációs rendszer valószínűleg a hozzáférést és legegyszerűbben hibát okoz az IoT. 

