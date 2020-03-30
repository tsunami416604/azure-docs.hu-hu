---
title: A Windows Virtual Desktop adathelyei – Azure
description: Rövid áttekintés awindows virtuális asztal adatainak és metaadatainak tárolásáról.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128057"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>A Windows virtuális asztal adathelyei

A Windows virtuális asztal jelenleg minden földrajzi helyen elérhető. Kezdetben a szolgáltatás metaadatai csak az Egyesült Államok (USA) földrajzi helyén tárolhatók. A rendszergazdák kiválaszthatják a felhasználói adatok tárolására szolgáló helyet, amikor létrehozják a gazdakészlet virtuális gépeit és a kapcsolódó szolgáltatásokat, például a fájlkiszolgálókat. További információ az Azure-beli földrajzi területekről az [Azure adatközpont-leképezésen.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>A Microsoft nem szabályozza és nem korlátozza azon régiókat, ahol Ön vagy a felhasználók hozzáférhetnek a felhasználó- és alkalmazásspecifikus adatokhoz.

>[!IMPORTANT]
>A Windows Virtuális asztal globális metaadatokat tárol, például bérlőneveket, állomáskészletneveket, alkalmazáscsoportneveket és egyszerű felhasználóneveket az Egyesült Államokban található adatközpontban. A tárolt metaadatok titkosítva vannak intve, és a georedundáns tükrök az Egyesült Államokban maradnak. Minden ügyféladat, például az alkalmazásbeállítások és a felhasználói adatok azügyfél által választott helyen találhatók, és nem a szolgáltatás kezeli őket.

A szolgáltatás metaadatai a vész-helyreállítási célokra replikálódnak az Egyesült Államokban.