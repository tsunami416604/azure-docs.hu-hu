---
title: Windows rendszerű virtuális asztali MSIX alkalmazás – GYIK – Azure
description: Gyakran ismételt kérdések a Windows rendszerű virtuális asztali MSIX alkalmazással kapcsolatban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556271"
---
# <a name="msix-app-attach-faq"></a>MSIX-alkalmazás csatolása – GYIK

Ez a cikk a Windows rendszerű virtuális asztali MSIX alkalmazással kapcsolatos gyakori kérdésekre ad választ.

## <a name="does-msix-app-attach-use-fslogix"></a>A MSIX-alkalmazás csatolja az FSLogix-t?

A MSIX-alkalmazás csatolása nem használja a FSLogix. Az alkalmazás csatolása és FSLogix azonban úgy lett kialakítva, hogy zökkenőmentes felhasználói élményt nyújtsanak.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Használhatom a MSIX alkalmazást a Windows rendszerű virtuális asztali gépeken kívül?

Igen, a MSIX-alkalmazás csatolása egy, a Windows 10 Enterprise rendszer részét képező szolgáltatás, amely a Windows virtuális asztalon kívül is használható. Azonban nincs olyan felügyeleti sík a MSIX alkalmazáshoz, amely a Windows rendszerű virtuális asztalon kívülre van csatolva.

## <a name="how-do-i-get-an-msix-package"></a>Hogyan lekérni egy MSIX-csomagot?

A szoftver gyártójától MSIX-csomagot fog adni. A nem MSIX csomagok MSIX is átalakíthatók. További információ a [meglévő telepítők MSIX való áthelyezéséről](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Mely operációs rendszerek támogatják a MSIX-alkalmazás csatolását?

Windows 10 Enterprise és Windows 10 Enterprise multi-session.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MSIX-alkalmazás csatlakoztatásáról, tekintse meg az [Áttekintés](what-is-app-attach.md) [szószedetet](app-attach-glossary.md). Ellenkező esetben Ismerkedjen [meg az alkalmazások csatlakoztatásának beállítása](app-attach.md)című lépéssel.