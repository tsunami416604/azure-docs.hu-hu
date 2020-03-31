---
title: A Microsoft Threat Modeling Tool 03/22/2020 kiadása – Azure
description: A fenyegetésmodellező eszköz kiadási megjegyzésekének dokumentálása
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146861"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>A Threat Modeling Tool 7.3.00316.1 - 03/22/2020 frissítés

A Microsoft Threat Modeling Tool (TMT) 7.3.00316.1-es verziója 2020.

- Javított kisegítő lehetőségek
- Hibajavítások
- Új Diagramreader-szolgáltatás

## <a name="notable-bug-fixes"></a>Figyelemre méltó hibajavítások

### <a name="exporting-the-threat-list-to-csv"></a>A fenyegetéslista exportálása CSV-be

A CSV-be való exportálás inkonzisztens módon választotta ki, hogy a fenyegetéslistából mely mezőket exportálná a rendszer. Most a fenyegetéslista összes mezője a CSV-fájlba lesz exportálva. 

### <a name="ux-bugs"></a>UX hibák

- Az elsődleges munkafolyamat súgómenüi (létrehozás/megnyitás/elemzés) és a sablonszerkesztő-élmény mostantól egységes menübeállításokkal rendelkeznek.
- A rajzsablonok ablaktáblájának keresősávja most már szabványos kurorral rendelkezik, és megfelelő címkéket adott hozzá.

## <a name="new-features"></a>Új funkciók

### <a name="diagramreader-feature-has-been-added"></a>A Diagramolvasó szolgáltatás hozzáadva

A főmenüben egy új DiagramReader-szolgáltatás került feladásra, amíg a modell meg van nyitva. Ez a funkció a modell grafikus ábrázolását szövegalapú narratívává alakítja. 

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek:
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET verzió szükséges:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények:
  - Internetkapcsolat az eszköz frissítéseinek, valamint a sablonoknak a fogadásához

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A fenyegetésmodellező eszköz dokumentációja [a docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)található, és információkat tartalmaz [az eszköz használatával kapcsolatban.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
