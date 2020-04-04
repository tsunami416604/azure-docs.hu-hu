---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637296"
---
Kezelése tömörített hang hajtja végre [GStreamer](https://gstreamer.freedesktop.org). Licencelési okokból a GStreamer bináris fájljai nincsenek lefordítva és nem kapcsolódnak a beszédfelismerési SDK-hoz. A fejlesztőknek számos függőséget és beépülő modult kell telepíteniük, [lásd: Telepítés Windows rendszeren](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). A Gstreamer bináris fájloknak a rendszer elérési útján kell lenniük, hogy a beszédstak működés közben betudja tölteni a gstreamer bináris fájlokat. Ha a beszédstabk-sdk futásidőben képes megtalálni a libgstreamer-1.0-0.dll-t, az azt jelenti, hogy a gstreamer bináris fájlok a rendszer elérési útját képezik.

