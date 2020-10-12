---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282735"
---
A tömörített hang kezelésére a használatával kerül sor [`GStreamer`](https://gstreamer.freedesktop.org) . Licencelési okokból a `GStreamer` bináris fájlok nincsenek lefordítva és csatolva a SPEECH SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük, lásd: [telepítés Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)rendszeren. `GStreamer` a bináris fájlokat a rendszer elérési útjában kell megadni, hogy a Speech SDK betöltse a bináris fájlokat a futtatókörnyezetben. Ha a Speech SDK megkeresi a `libgstreamer-1.0-0.dll` futtatókörnyezetet, az azt jelenti, hogy a bináris fájlok a rendszer elérési útjában vannak.

