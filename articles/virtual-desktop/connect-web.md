---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyféllel – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyfélprogram használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7c97009a69c937863a5776d63bab1c994a8a9160
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007607"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Kapcsolódás a Windows rendszerű virtuális asztalhoz a webes ügyféllel

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/connect-web-2019.md).

A webes ügyfél lehetővé teszi a Windows rendszerű virtuális asztali erőforrások elérését egy webböngészőből a hosszadalmas telepítési folyamat nélkül.

>[!NOTE]
>A webes ügyfél jelenleg nem rendelkezik a Mobile OS-támogatással.

## <a name="supported-operating-systems-and-browsers"></a>Támogatott operációs rendszerek és böngészők

Habár minden HTML5-kompatibilis böngészőnek működnie kell, a következő operációs rendszereket és böngészőket hivatalosan támogatjuk.

| Böngésző           | Támogatott operációs rendszer                     | Megjegyzések               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 11-es vagy újabb verzió |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | 55-es vagy újabb verzió |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Távoli erőforrások hírcsatornájának elérése

Egy böngészőben nyissa meg a Windows rendszerű virtuális asztali webes ügyfélprogram Azure Resource Manager-integrált verzióját, <https://rdweb.wvd.microsoft.com/arm/webclient> és jelentkezzen be a felhasználói fiókjával.

>[!NOTE]
>Ha Azure Resource Manager integráció nélkül használja a Windows rendszerű virtuális asztalt (klasszikus), akkor ehelyett kapcsolódjon az erőforrásokhoz <https://rdweb.wvd.microsoft.com/webclient> .

>[!NOTE]
>Ha már bejelentkezett egy másik Azure Active Directory fiókkal, mint amelyet a Windowsos virtuális asztalhoz kíván használni, akkor ki kell jelentkeznie, vagy egy privát böngészőablakot kell használnia.

A bejelentkezést követően meg kell jelennie az erőforrások listájának. Az erőforrásokat úgy is elindíthatja, ha a **minden erőforrás** lapon a normál alkalmazást szeretné.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a webes ügyfél használatáról, tekintse meg [a webes ügyfélprogram használatának első lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
