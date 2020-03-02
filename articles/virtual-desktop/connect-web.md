---
title: A Windows rendszerű virtuális asztali webes ügyfélprogram összekötése – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyfélprogram használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
ms.openlocfilehash: 9c738c5b7f233ffa34296eed67eb0f5497f737fc
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206673"
---
# <a name="connect-with-the-web-client"></a>Kapcsolódás a webügyféllel

A webes ügyfél lehetővé teszi a Windows rendszerű virtuális asztali erőforrások elérését egy webböngészőből a hosszadalmas telepítési folyamat nélkül.

>[!NOTE]
>A webes ügyfél jelenleg nem rendelkezik a Mobile OS-támogatással.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Habár minden HTML5-kompatibilis böngészőnek működnie kell, a következő operációs rendszereket és böngészőket hivatalosan támogatjuk.

| Böngésző           | Támogatott operációs rendszer                     | Megjegyzések               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55-es vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Távoli erőforrások hírcsatornájának elérése

Egy böngészőben nyissa meg a [Windows rendszerű virtuális asztali webes ügyfelet](https://rdweb.wvd.microsoft.com/webclient) , és jelentkezzen be a felhasználói fiókjával.

>[!NOTE]
>Ha már bejelentkezett egy másik Azure Active Directory fiókkal, mint amelyet a Windowsos virtuális asztalhoz kíván használni, akkor ki kell jelentkeznie, vagy egy privát böngészőablakot kell használnia.

A bejelentkezést követően meg kell jelennie az erőforrások listájának. Az erőforrásokat úgy is elindíthatja, ha a **minden erőforrás** lapon a normál alkalmazást szeretné.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a webes ügyfél használatáról, tekintse meg [a webes ügyfélprogram használatának első lépéseit](https://go.microsoft.com/fwlink/?linkid=2120256).
