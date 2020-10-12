---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422090"
---
A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. Licencelési okokból a GStreamer bináris fájlok nincsenek lefordítva és csatolva a Speech SDK-hoz. A fejlesztőknek több függőséget és beépülő modult kell telepíteniük, lásd: [telepítés Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c)rendszeren. A GStreamer bináris fájljainak a rendszer elérési útjában kell lenniük, így a beszédfelismerési SDK a futtatókörnyezet során képes betölteni a GStreamer bináris fájlokat. Ha a Speech SDK képes megkeresni a libgstreamer-1.0-0.dll futás közben, az azt jelenti, hogy a GStreamer bináris fájlok a rendszer elérési útjában vannak.

