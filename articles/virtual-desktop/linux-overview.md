---
title: Windows rendszerű virtuális asztali Linux Support – Azure
description: A Windows rendszerű virtuális asztali Linux-támogatás rövid áttekintése.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008695"
---
# <a name="linux-support"></a>Linux-támogatás

A Linux-eszközökről elérheti a Windows rendszerű virtuális asztali erőforrásokat a következő támogatott ügyfelekkel, amelyeket a Linux rendszerű, vékony ügyfél partnereink biztosítanak. Számos partnerrel dolgozunk, hogy a támogatott Windowsos virtuális asztali ügyfelek több Linux-alapú operációs rendszeren és eszközön is elérhetők legyenek. Ha azt szeretné, hogy a Windows rendszerű virtuális asztali támogatás olyan linuxos platformon is használható legyen, amely nem szerepel itt, kérjük, tudassa velünk a [UserVoice oldalon](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>A Linux-eszköz csatlakoztatása

A következő partnerek jóváhagyták a Windows rendszerű virtuális asztali ügyfeleket a linuxos eszközökhöz.

|Partner|Partneri dokumentáció|Partneri támogatás|
|:------|:--------------------|:--------------|
|![IGEL embléma](./media/partners/igel.png)|[IGEL-ügyfél dokumentációja](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL-támogatás](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Mi a Linux SDK?

A Linux-alapú vékony ügyfelek a Windows rendszerű virtuális asztali linuxos SDK API-kkal lekérhetik az erőforrás-hírcsatornákat, csatlakozhatnak asztali vagy távoli alkalmazás-munkamenetekhez, és számos átirányítást használhatnak, amelyeket az első féltől származó ügyfelek támogatnak. Az SDK az Ubuntu 18,04-es vagy újabb verzióján alapuló legtöbb operációs rendszerrel kompatibilis.

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

## <a name="next-steps"></a>További lépések

Tekintse meg a következő ügyfelek dokumentációját:

- [Windows asztali ügyfél](connect-windows-7-10.md)
- [Webes ügyfél](connect-web.md)
- [Android-ügyfél](connect-android.md)
- [macOS-ügyfél](connect-macos.md)
- [iOS-ügyfél](connect-ios.md)
