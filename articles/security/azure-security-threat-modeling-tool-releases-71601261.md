---
title: A Threat Modeling Tool kiadásai – Microsoft Threat Modeling Tool – Azure |} A Microsoft Docs
description: Dokumentálja a threat modeling tool kibocsátási megjegyzései
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: 55c781d0c6284de1382c9d3e614621c40963194b
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55303091"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool frissítés kiadása 7.1.60126.1 – 1/29 vagy 2019

A Microsoft Threat Modeling Tool 7.1.60126.1 verziója jelent meg. január 29 verzióját 2019, és tartalmazza a következő módosításokat:

- A minimálisan szükséges verzió .NET nőtt [.Net 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262).
- Windows minimális szükséges verziója nőtt [Windows 10 Évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) a .NET-függőség miatt.
- A modell érvényesítése váltógomb szolgáltatást bővült az eszköz beállítások menüjében.
- Több hivatkozás a fenyegetés tulajdonságok frissítése.
- Kisebb UX módosítja az eszköz kezdőképernyőjére.
- A Threat Modeling Tool mostantól örökli a TLS-beállításokat, a gazdagép operációs rendszere és szükséges a TLS 1.2-es környezetekben támogatott, vagy nagyobb.

## <a name="feature-changes"></a>A szolgáltatás módosítások

### <a name="model-validation-option"></a>Modell érvényesítési lehetőség

Ügyfeleink visszajelzései alapján lehetőség van adva az eszközt, hogy engedélyezi vagy letiltja a modell érvényesítése. Korábban a sablont egy egyetlen egyirányú adatfolyamot két objektum közötti használja, ha lehetséges, hogy kapott hibaüzenetet a következő üzenetek keret: Legalább egy szükséges ObjectsName "Bármely". Modell érvényesítés letiltása megakadályozza, hogy a bemutató. Ez a nézet a figyelmeztetések.

Váltsa át a modell érvényesítése be- és kikapcsolása lehetőséget a fájl megtalálható -> Beállítások -> Beállítások menüjében. Ez a beállítás alapértelmezett értékét le van tiltva.

![Modell érvényesítési lehetőség](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Rendszerkövetelmények

- Támogatott operációs rendszerek
  - [A Microsoft Windows 10 Évfordulós frissítés](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) vagy újabb
- .NET-verzió megadása kötelező
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) vagy újabb
- További követelmények
  - Internetkapcsolat szükséges az eszköz, valamint a sablonok a frissítések fogadásához.

## <a name="known-issues"></a>Ismert problémák

### <a name="unsupported-systems"></a>Nem támogatott rendszerek

#### <a name="issue"></a>Probléma

Nem lehet telepíteni a .NET 4.7.1 vagy újabb, mint a Windows 10 Enterprise LTSB (1507-es verzió), a Windows 10 rendszerek felhasználói nem nyitható meg az eszköz frissítése után lesz. Ezen Windows régebbi verziói nem lesznek a Threat Modeling Tool által támogatott platformok, és ne telepítse a legújabb frissítést.

#### <a name="workaround"></a>Áthidaló megoldás

Felhasználók a Windows 10 Enterprise LTSB (1507-es verzió), amelyen telepítve van a legújabb frissítést a Threat Modeling Tool segítségével az alkalmazások és szolgáltatások eltávolítása párbeszédpanelje előző verzióját állíthatja vissza.

## <a name="documentation-and-feedback"></a>Dokumentáció és visszajelzés

- A Threat Modeling Tool dokumentációjában található [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), és tartalmaz adatokat [az eszközzel kapcsolatos](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>További lépések

Töltse le a legújabb verzióját a [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).