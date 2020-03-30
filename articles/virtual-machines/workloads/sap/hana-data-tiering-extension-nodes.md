---
title: Adatrétegezés és bővítménycsomópontok az SAP HANA az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Adatrétegezés és bővítmény csomópontok AZ SAP HANA az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617163"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA-adatrétegezés és bővítménycsomópontok használata

Az SAP támogatja a különböző SAP NetWeaver-kiadások sap BW és AZ SAP BW/4HANA adatrétegezési modelljét. Az adatrétegezési modellről további információt az [SAP-dokumentum SAP BW/4HANA és SAP BW a HANA AZ SAP HANA bővítmény csomópontok.](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)
Hana nagy példány, használhatja az SAP HANA bővítmény csomópontok 1-es beállításkonfigurációját a gyIK és az SAP blog dokumentumok magyarázata szerint. Az Option-2 konfigurációk a következő HANA nagypéldány-példányváltozatokkal állíthatók be: S72m, S192, S192m, S384 és S384m. 

Ha megnézi a dokumentációt, előfordulhat, hogy az előny nem lesz azonnal látható. De ha megnézzük az SAP méretezési irányelvek, láthatja az előnyt a option-1 és a 2-es sap HANA-bővítmény csomópontok használatával. Például:

- Az SAP HANA méretezési irányelvei általában az adatmennyiség kétszeresét igénylik memóriaként. Amikor futtatja az SAP HANA-példánya a ritka elérésű adatokkal, csak 50 százaléka vagy kevesebb a memória töltött adatokkal. A memória fennmaradó ideális ansap hana a munkáját végzi.
- Ez azt jelenti, hogy egy HANA nagy példány S192 egység 2 TB memóriával, egy SAP BW adatbázis futtatásával, csak 1 TB adatkötetként.
- Ha egy további SAP HANA-bővítmény csomópontot használ az 1-es lehetőséghez, valamint egy S192 HANA nagypéldány-termékváltozatot, további 2 TB-os kapacitást biztosít az adatkötethez. Az option-2 konfigurációban további 4 TB-ot kap a meleg adatmennyiséghez. A forró csomóponthoz képest a "meleg" bővítménycsomópont teljes memóriakapacitása használható az 1-es beállítás hoz tárolt adatokhoz. A memória kétszerese adatkötethez használható a 2 SAP HANA-bővítmény csomópontkonfigurációjában.
- A végén az adatok kapacitása 3 TB, az 1-es opció esetében pedig 1:2 meleg-meleg arány. 5 TB adattal és 1:4-es aránnyal rendelkezik az option-2 bővítmény csomópont konfigurációjával.

Minél nagyobb az adatmennyiség a memóriához képest, annál nagyobb az esélye annak, hogy a kért meleg adatok a lemeztárolón tárolódnak.

**További lépések**
- SAP [HANA (nagy példányok) architektúrájának ajánlása az Azure-ban](hana-architecture.md)