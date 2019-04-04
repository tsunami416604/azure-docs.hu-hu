---
title: TmaxSoft OpenFrame az Azure Virtual machines szolgáltatásban – első lépések
description: Áthelyezési IBM z/OS nagyszámítógépes feladatait TmaxSoft OpenFrame környezet Azure-beli virtuális gépeken (VM-EK) használatával.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896344"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Az Azure-ban TmaxSoft OpenFrame használatának első lépései

A meglévő nagyszámítógépes eszközök igénybe, és helyezze át őket a Microsoft Azure-ban TmaxSoft OpenFrame. A népszerű rehosting megoldás emulációs környezet létrehoz az Azure-ban, így gyorsan alkalmazásokat telepíthet át. Nincs újraformázást nem szükséges.

## <a name="openframe-rehosting-environment"></a>OpenFrame újratárolása környezet

Állítsa be a fejlesztési, bemutatók, tesztelési vagy éles számítási feladatok Azure-beli OpenFrame környezetet. Ahogy az alábbi ábrán látható, OpenFrame tartalmaz több összetevő, amely a nagyszámítógépes emulációs környezet létrehozása az Azure-ban. Online szolgáltatások OpenFrame helyettesítse be például a nagyszámítógépes közbenső például IBM vásárlói adatokat vezérlő rendszer (CICS). OpenFrame Batch, a TJES összetevővel váltja fel az IBM Nagyszámítógépek feladat bejegyzés alrendszer (JES). 

![OpenFrame újratárolása folyamat](media/openframe-01.png)

> [!NOTE]
> Futtassa a OpenFrame környezet az Azure-ban, egy érvényes terméklicenc vagy próbaverziós licencre TmaxSoft kell rendelkeznie.

## <a name="openframe-components"></a>OpenFrame összetevők

Az Azure-ban OpenFrame-környezet részét képező alábbi összetevőket:

- **Áttelepítési eszközök** többek között a OFMiner, olyan megoldás, amely elemzi az Nagyszámítógépek eszközöket, és ezután áttelepíti őket az Azure-bA.
- **Fordítóprogramot**, beleértve a OFCOBOL, fordító, amely értelmezi a nagyszámítógépes COBOL programokat. OFPLI, amely értelmezi a nagyszámítógépes PL / programok; és OFASM, fordító, amely értelmezi a nagyszámítógépes assembler programok.
- **Az előtérbeli** összetevők, beleértve a Java Enterprise felhasználói megoldás (JEUS), egy webalkalmazás-kiszolgáló, amely a Java Enterprise Edition 6.OFGW minősítéssel és a OpenFrame átjáró összetevője, amely egy 3270 figyelő.
- **Alkalmazás** környezetben. OpenFrame alapja a közbenső szoftver, amely a teljes rendszer felügyeli. OpenFrame kiszolgáló típusú C (OSC) a nagyszámítógépes közbenső és IBM CICS váltja fel.
- **Relációs adatbázis**, például a Tibero (látható), Oracle Database, a Microsoft SQL Server, IBM DB2-höz vagy MySQL. A OpenFrame alkalmazások megnyitott adatbázis-kapcsolat (ODBC) protokollt használja az adatbázissal folytatott kommunikációhoz.
- **Biztonsági** keresztül TACF, a szolgáltatás a modul, amely szabályozza a rendszerek és az erőforrások felhasználói hozzáférését. 
- **OFManager** olyan megoldás, amely a webes környezet OpenFrame a művelet és a felügyeleti funkciókat biztosít.

![OpenFrame architektúra](media/openframe-02.png)

## <a name="next-steps"></a>További lépések

- [TmaxSoft OpenFrame telepítése Azure-ban](./install-openframe-azure.md)
