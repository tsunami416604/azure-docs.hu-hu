---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943824"
---
A tömörített hang kezelésére a [GStreamer](https://gstreamer.freedesktop.org)használatával kerül sor. Licencelési okokból a GStreamer bináris fájlok nincsenek lefordítva és csatolva a Speech SDK-hoz. Ehelyett az ezeket a függvényeket tartalmazó burkoló kódtárat az SDK használatával kell felépíteni és elszállítani az alkalmazásokkal.

A burkoló könyvtár létrehozásához először töltse le és telepítse a [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)-t. Ezután töltse le a [burkoló könyvtár](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper) **Xcode** -projektjét.

Nyissa meg a projektet a **Xcode** -ben, és hozza létre az **általános iOS-eszközök** céljához – ez *nem* fog működni, hogy egy adott célra felépítse.

A Build lépés dinamikus keretrendszerű köteget hoz létre egy dinamikus könyvtárral a `GStreamerWrapper.framework`nevű összes szükséges architektúrához.

Ezt a keretrendszert minden olyan alkalmazásnak tartalmaznia kell, amely a Speech Service SDK-val tömörített hangstreameket használ.

A következő beállítások alkalmazása a **Xcode** -projektben a következőképpen valósítható meg:

1. Másolja ki az imént felépített `GStreamerWrapper.framework` és a Cognitive Services Speech SDK keretrendszerét, amelyet [innen](https://aka.ms/csspeech/iosbinary)tölthet le a minta projektet tartalmazó könyvtárba.
1. Állítsa be a keretrendszerek elérési útját a *projekt beállításai*között.
   1. A **beágyazott bináris fájlok** fejlécének **általános** LAPJÁN adja hozzá az SDK-tárat keretrendszerként: **beágyazott bináris fájlok hozzáadása** > **továbbiak hozzáadása...** > navigáljon a kiválasztott könyvtárhoz, és válassza ki mindkét keretrendszert.
   1. Lépjen a **Build Settings** lapra, és engedélyezze az **összes** beállítást.
1. Vegye fel a könyvtárat `$(SRCROOT)/..` a keretrendszer-keresési útvonalak közé (_Framework Search Paths_ a **Search Paths** részben).
