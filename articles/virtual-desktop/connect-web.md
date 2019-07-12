---
title: Csatlakozás a egy webböngésző - Azure Windows virtuális asztal előzetes verzió
description: Hogyan lehet csatlakozni, hogy a Windows virtuális asztal előzetes egy webböngészőből.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 4f6bb7c414c38eaa605d259195218c461ddb0ca7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620169"
---
# <a name="connect-from-a-web-browser"></a>Csatlakozás webböngészőről

A webes ügyfél lehetővé teszi a Windows Virtual Desktop előzetes verziójához erőforrások eléréséhez a hosszadalmas telepítést nélkül egy webböngészőből.

>[!NOTE]
>A webes ügyfél jelenleg nem rendelkezik a mobil operációs rendszer támogatja.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Bármely HTML5-komptibilis böngészőre működnie kell, amíg hivatalosan támogatott a következő operációs rendszereket és böngészőket.

| Browser           | Támogatott operációs rendszer                     | Megjegyzések               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55 vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Hírcsatorna távoli erőforrások eléréséhez

Egy böngészőben navigáljon a [Windows virtuális asztali webes ügyfél](https://rdweb.wvd.microsoft.com/webclient) , és jelentkezzen be a felhasználói fiókjával.

>[!NOTE]
>Ha már bejelentkezett egy másik Azure Active Directory fiókkal fut, a Windows virtuális asztal használni kívánt, használata kell jelentkezzen ki, vagy egy inkognitóablakban használja.

Bejelentkezés után a most már megjelenik azon erőforrások listáját. Válassza ki, mint a szokásos alkalmazások az erőforrások indíthatja el a **összes erőforrás** fülre.
