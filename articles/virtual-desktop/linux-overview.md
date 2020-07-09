---
title: Windows rendszerű virtuális asztali Linux Support – Azure
description: A Windows rendszerű virtuális asztali Linux-támogatás rövid áttekintése.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422419"
---
# <a name="linux-support"></a>Linux-támogatás

A partnerek a Windows rendszerű virtuális asztali Linux SDK használatával létrehozhatnak egy önálló Windowsos virtuális asztali ügyfelet. Azt is megteheti, hogy lehetővé teszi a Windows rendszerű virtuális asztali támogatás használatát az ügyfélalkalmazás számára. Ez a rövid útmutató ismerteti, hogy mi a Linux SDK, és hogyan kezdheti meg a használatát.

## <a name="connect-with-your-linux-device"></a>A Linux-eszköz csatlakoztatása

A következő partnerek jóváhagyták a Windows rendszerű virtuális asztali ügyfeleket a linuxos eszközökhöz.

|Partner|Partneri dokumentáció|Partneri támogatás|
|:------|:--------------------|:--------------|
|![IGEL embléma](./media/partners/igel.png)|[IGEL-ügyfél dokumentációja](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL-támogatás](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Mi a Linux SDK?

Az SDK API-k segítségével lekérheti az erőforrás-hírcsatornákat, csatlakozhat asztali vagy távoli alkalmazás-munkamenetekhez, és számos olyan átirányítást használhat, amelyet az első féltől származó ügyfelek támogatnak.

> [!NOTE]
> Az SDK jelenleg fejlesztés alatt áll. Ezt a dokumentumot az általánosan elérhető SDK-hoz való hozzáférésre vonatkozó utasításokkal frissítjük.

### <a name="supported-linux-distributions"></a>Támogatott Linux-disztribúciók

Az SDK az Ubuntu 18,04-es vagy újabb verzióján alapuló legtöbb operációs rendszerrel kompatibilis. Ha más Linux-disztribúcióval rendelkezik, segítünk Önnek az igényeinek legmegfelelőbb támogatásban.

### <a name="feature-support"></a>Szolgáltatások támogatása

Az SDK több kapcsolatot is támogat az asztali és a távoli alkalmazás-munkamenetekhez. A következő átirányítások támogatottak:

| Átirányítás       | Támogatott |
| :---------------- | :-------: |
| Billentyűzet          | &#10004;  |
| Egér             | &#10004;  |
| Hangbemenet          | &#10004;  |
| Audiokimenet         | &#10004;  |
| Vágólap (szöveg)  | &#10004;  |
| Vágólap (rendszerkép) | &#10004;  |
| Vágólap (fájl)  | &#10004;  |
| Intelligens kártya         | &#10004;  |
| Meghajtó/mappa      | &#10004;  |

Az SDK emellett több figyelő megjelenítési konfigurációt is támogat, feltéve, hogy a munkamenethez kiválasztott figyelők összefüggőek.

Frissítjük a dokumentumot az új funkciók és átirányítások támogatásának hozzáadásakor. Ha új szolgáltatásokat és egyéb fejlesztési funkciókat szeretne javasolni, látogasson el a [UserVoice oldalára](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Ismerkedés a Linux SDK-val

A Linux-ügyfél Windows rendszerű virtuális asztali környezethez való fejlesztése előtt a következőket kell tennie:

1. Windows rendszerű virtuális asztali környezet létrehozása és üzembe helyezése teszteléshez vagy éles környezetben való használatra.
2. Tesztelje az elérhető első féltől származó ügyfeleket a Windows rendszerű virtuális asztali felhasználói élmény megismeréséhez.

## <a name="next-steps"></a>További lépések

Tekintse meg a következő ügyfelek dokumentációját:

- [Windows asztali ügyfél](connect-windows-7-and-10.md)
- [Webes ügyfél](connect-web.md)
- [Android-ügyfél](connect-android.md)
- [macOS-ügyfél](connect-macos.md)
- [iOS-ügyfél](connect-ios.md)
