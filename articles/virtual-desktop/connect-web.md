---
title: Csatlakozás a Windows virtuális asztal előzetes webes ügyféllel – Azure
description: Hogyan lehet a Windows virtuális asztal előzetes HTML5 webes ügyfél csatlakozni.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 231dc2b777ce7fa37f445215863a55e93562e411
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403748"
---
# <a name="connect-with-the-web-client"></a>Csatlakozás a webes ügyféllel

A webes ügyfél lehetővé teszi a Windows Virtual Desktop előzetes verziójához erőforrások eléréséhez a hosszadalmas telepítést nélkül egy webböngészőből.

>[!NOTE]
>A webes ügyfél jelenleg nem rendelkezik a mobil operációs rendszer támogatja.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Bármely HTML5-komptibilis böngészőre működnie kell, amíg hivatalosan támogatott a következő operációs rendszereket és böngészőket.

| Böngésző           | Támogatott operációs rendszer                     | Megjegyzések               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55 vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Hírcsatorna távoli erőforrások eléréséhez

Egy böngészőben navigáljon a [Windows virtuális asztali webes ügyfél](https://rdweb.wvd.microsoft.com/webclient) , és jelentkezzen be a felhasználói fiókjával.

>[!NOTE]
>Ha már bejelentkezett egy eltérő fut, a Windows virtuális asztal használni kívánt Azure AD-fiókot használ, szükség lehet jelentkezzen ki, vagy használjon egy inkognitóablakban.

Bejelentkezés után a most már megjelenik azon erőforrások listáját. Válassza ki, mint a szokásos alkalmazások az erőforrások indíthatja el a **összes erőforrás** fülre.
