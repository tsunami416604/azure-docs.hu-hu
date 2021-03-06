---
ms.openlocfilehash: 64aee50a8a5a90ea0e8dfb0e7206926f12599944
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691790"
---
Az Azure-erőforrások beállításakor a rendszer átmásolja az autópálya-forgalom rövid videóját az Azure-ban lévő, IoT Edge eszközként használt linuxos virtuális gépre. Ez a rövid útmutató egy élő stream szimulálására használja a videofájl használatával.

Nyisson meg egy alkalmazást, például a [VLC Media Playert](https://www.videolan.org/vlc/). Válassza a CTRL + N billentyűkombinációt, majd illesszen be egy hivatkozást [az országúti metszet minta videóra](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) a lejátszás megkezdéséhez. Itt láthatja az autópálya-forgalomban mozgó számos jármű felvételét.

Ebben a rövid útmutatóban élő videó-elemzéseket fog használni a IoT Edgeon az objektumok, például a gépjárművek és a személyek észleléséhez. A kapcsolódó következtetési eseményeket közzé kell tenni IoT Edge hubhoz.
