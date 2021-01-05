---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821553"
---
A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. Licencelési okokból a GStreamer bináris fájlok nincsenek lefordítva és csatolva a Speech SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük, lásd: [telepítés Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) rendszeren vagy [Linux rendszeren való telepítés](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). A GStreamer bináris fájljainak a rendszer elérési útjában kell lenniük, így a beszédfelismerési SDK a futásidejű környezetekben is betöltheti a bináris fájlokat. Windows rendszeren például, ha a Speech SDK képes megtalálni a `libgstreamer-1.0-0.dll` futtatókörnyezetet, az azt jelenti, hogy a GStreamer bináris fájlok a rendszer elérési útjában vannak.

