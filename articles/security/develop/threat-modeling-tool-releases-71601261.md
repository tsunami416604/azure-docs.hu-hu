---
title: A Microsoft Threat Modeling Tool 1/29/2019 kiadása
titleSuffix: Azure
description: A fenyegetésmodellező eszköz kiadási megjegyzésekének dokumentálása
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269777"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>A fenyegetésmodellezési eszköz frissített kiadása: 7.1.60126.1 – 1/29/2019

A Microsoft Threat Modeling Tool 7.1.60126.1 verziója 2019.

- A .NET minimálisan szükséges verziója [.NET 4.7.1-re](https://go.microsoft.com/fwlink/?LinkId=863262)nőtt.
- A Windows minimálisan szükséges verziója a .NET függőség miatt [a Windows 10 évfordulós frissítésére](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) lett növelve.
- Az eszköz Beállítások menüjében egy modellérvényesítési kapcsolófunkció került hozzáadásra.
- A fenyegetéstulajdonságaiban több hivatkozás is frissült.
- A kisebb felhasználói felület az eszköz kezdőképernyőjén módosul.
- A Fenyegetésmodellezési eszköz mostantól örökli a gazdaoperációs rendszer TLS-beállításait, és a TLS 1.2-es vagy újabb tls-t igénylő környezetekben is támogatott.

## <a name="feature-changes"></a>Funkcióváltozások

### <a name="model-validation-option"></a>Modell érvényesítési lehetősége

Az ügyfelek visszajelzései alapján egy lehetőség lett hozzáadva az eszközhöz a modell-érvényesítés engedélyezéséhez vagy letiltásához. Korábban, ha a sablon egyetlen egyirányú adatfolyamot használt két objektum között, előfordulhat, hogy hibaüzenetet kapott az Üzenetek keretben, amely a következőket tartalmazza: Az ObjectsName legalább egy "Any"-t igényel. A modellellenőrzés letiltása megakadályozza, hogy ezek a figyelmeztetések megjelenjenek a nézetben.

A modellérvényesítés be- és kikapcsolására a Fájl->Beállítások->Beállítások menüben választhat. A beállítás alapértelmezett értéke Letiltva.

![Modell érvényesítési beállítása](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - [Microsoft Windows 10 évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET verzió szükséges
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Az eszköz és a sablonok frissítéseinek fogadásához internetkapcsolat szükséges.

## <a name="known-issues"></a>Ismert problémák

### <a name="unsupported-systems"></a>Nem támogatott rendszerek

#### <a name="issue"></a>Probléma

A .NET 4.7.1 vagy újabb verziót nem telepíthető Windows 10 rendszerek, például a Windows 10 Enterprise LTSB (1507-es verzió) felhasználói nem tudják megnyitni az eszközt a frissítés után. A Windows ezen régebbi verziói már nem támogatottak a Fenyegetésmodellező eszköz számára, és nem telepíthetik a legújabb frissítést.

#### <a name="workaround"></a>Áthidaló megoldás

A legújabb frissítést telepítő Windows 10 Enterprise LTSB (1507-es verzió) felhasználói visszatérhetnek a Fenyegetésmodellező eszköz korábbi verziójához az Alkalmazások & szolgáltatások eltávolítása párbeszédpanelen keresztül.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A fenyegetésmodellező eszköz dokumentációja [a docs.microsoft.com](threat-modeling-tool.md)található, és információkat tartalmaz [az eszköz használatával kapcsolatban.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>További lépések

Töltse le a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)legújabb verzióját.
