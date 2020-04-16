---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422090"
---
Kezelése tömörített hang hajtja végre [GStreamer](https://gstreamer.freedesktop.org). Licencelési okokból a GStreamer bináris fájljai nincsenek lefordítva és nem kapcsolódnak a beszédfelismerési SDK-hoz. A fejlesztőknek számos függőséget és beépülő modult kell telepíteniük, [lásd: Telepítés Windows rendszeren](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). A Gstreamer bináris fájloknak a rendszer elérési útján kell lenniük, hogy a beszédstak működés közben betudja tölteni a gstreamer bináris fájlokat. Ha a beszédstabk-sdk futásidőben képes megtalálni a libgstreamer-1.0-0.dll-t, az azt jelenti, hogy a gstreamer bináris fájlok a rendszer elérési útját képezik.

