---
title: Rétegezést és bővítmény adatcsomópontok az SAP Hana az Azure-ban (nagyméretű példányok) |} A Microsoft Docs
description: Rétegezést és bővítmény adatcsomópontok az SAP Hana az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87c2cb3e373b76685fca09eb0cfeefdc9216df77
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028436"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA rétegezést és bővítmény csomópontok használata

SAP az SAP BW a különböző SAP NetWeaver-kiadások és az SAP BW/4HANA rétegezési adatmodellt támogatja. Az adatmodell rétegezési kapcsolatos további információkért lásd: az SAP-dokumentum [SAP BW/4hana-t és az SAP BW on HANA, SAP HANA-bővítmény csomóponttal rendelkező](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Nagyméretű HANA-példány, az SAP HANA-bővítmény csomópontok konfigurációja – 1. lehetőség – gyakori kérdések és az SAP blog dokumentumok leírtak is használhatja. 2. lehetőség konfigurációk beállíthatja az a következő HANA nagyméretű példány termékváltozatok: S72m, S192, S192m, S384 és S384m. 

Ha a dokumentáció, azt az előnyt esetleg nem láthatók azonnal. De ha az SAP méretezési irányelveket tekinti meg, megjelenik egy nagy előnnyel jár beállítás-1. és 2. lehetőség SAP HANA-bővítmény csomópontok használatával. Az alábbiakban példákat:

- SAP HANA méretezési irányelvek összeg adatmennyiség általában szükséges memória. A gyakran használt adatokkal futtatja az SAP HANA-példány, ha csak 50 %-os rendelkezik, vagy kevesebb, a memória adatokkal feltöltött. A fennmaradó memória ideális tárolt az SAP Hana végez a műveletet.
- A 2 TB memóriával, az SAP BW-adatbázist futtató HANA nagyméretű példány S192 egységben jelenti csak rendelkezik 1 TB-os adatok kötetként.
- Egy másik SAP HANA bővítmény csomópontjára: 1. lehetőség használatakor is egy S192 HANA nagyméretű példányok Termékváltozatban, biztosít egy további 2 TB kapacitást adatmennyiség. A 2. lehetőség a konfigurációban egy további 4 TB-os meleg adatmennyiség kap. A gyakori elérésű csomópont képest, a teljes memória-kapacitás, a "meleg" kiterjesztése csomópont adatok tárolására, az 1. lehetőség használható. A 2. lehetőség az SAP HANA bővítmény csomópont-konfiguráció adatmennyiség kétszeres memória is használható.
- Végül 3 TB kapacitású az adataihoz, és ritkáról gyakori elérésű-az-meleg 1:1. lehetőség 2-es. 5 TB-nyi adat és a 2. lehetőség bővítmény csomópont-konfiguráció az 1:4 aránya rendelkezik.

Minél nagyobb adatmennyiség képest a memória, annál nagyobb az esélye, hogy a meleg adatokat kér, a lemezes tárolás tárolja.

**Következő lépések**
- Tekintse meg [Azure-beli SAP HANA (nagyméretű példányok) architektúra](hana-architecture.md)