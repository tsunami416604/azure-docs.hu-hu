---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422262"
---
Kezelése tömörített hang hajtja végre [GStreamer](https://gstreamer.freedesktop.org). Licencelési okokból a GStreamer bináris fájljai nincsenek lefordítva és nem kapcsolódnak a beszédfelismerési SDK-hoz. Ehelyett létre kell építeni és szállítani kell egy burkolókönyvtárat, amely ezeket a funkciókat tartalmazza.

A burkolókönyvtár létrehozásához először töltse le és telepítse a [GStreamer SDK-t.](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg) Ezután töltse le az **Xcode** projektet a [burkolókönyvtárhoz.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)

Nyissa meg a projektet **az Xcode-ban,** és hozza létre az **általános iOS-eszköz** célhoz – *nem* fog működni egy adott célhoz való létrehozása.

A build lépés létrehoz egy dinamikus keretrendszer köteg egy dinamikus könyvtár `GStreamerWrapper.framework`minden szükséges architektúrák a neve.

Ezt a keretrendszert minden olyan alkalmazásban tartalmaznia kell, amely tömörített hangadatfolyamokat használ a beszédfelismerési szolgáltatás Salk-jával.

Ennek érdekében alkalmazza a következő beállításokat az **Xcode** projektben:

1. Másolja `GStreamerWrapper.framework` az imént épített és a Cognitive Services speech SDK keretét, amelyet [innen](https://aka.ms/csspeech/iosbinary)tölthet le, a mintaprojektet tartalmazó könyvtárba.
1. Állítsa be a keretrendszerek elérési útjait a *Projektbeállítások*menüben.
   1. A Beágyazott bináris **fájlok** fejlécének **Általános** lapján adja hozzá az SDK-könyvtárat keretrendszerként: **Beágyazott bináris fájlok** > **hozzáadása: >** Keresse meg a kiválasztott könyvtárat, és jelölje ki mindkét keretrendszert.
   1. Lépjen a **Build Settings** lapra, és engedélyezze az **összes** beállítást.
1. Vegye fel a könyvtárat `$(SRCROOT)/..` a keretrendszer-keresési útvonalak közé (_Framework Search Paths_ a **Search Paths** részben).
