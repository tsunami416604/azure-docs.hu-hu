---
title: Windows Virtual Desktop Linux támogatás – Azure
description: Rövid áttekintés a Windows virtual desktop Linux-támogatásáról.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422419"
---
# <a name="linux-support"></a>Linux-támogatás

A partnerek a Linux SDK for Windows Virtual Desktop segítségével önálló Windows virtual desktop klienst hozhatnak létre. Azt is használhatja, hogy engedélyezze a Windows virtuális asztal támogatását az ügyfélalkalmazásban. Ez a gyors útmutató elmagyarázza, mi a Linux SDK, és hogyan kell használni.

## <a name="connect-with-your-linux-device"></a>Csatlakozás Linux-eszközhöz

A következő partnerek jóváhagyták a Windows Virtuális asztali ügyfeleket Linux-eszközökhöz.

|Partner|Partner dokumentációja|Partnertámogatás|
|:------|:--------------------|:--------------|
|![IGEL logó](./media/partners/igel.png)|[IGEL kliens dokumentáció](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[IGEL támogatás](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>Mi az a Linux SDK?

Az SDK API-k segítségével lekérheti az erőforrás-hírcsatornákat, csatlakozhat asztali vagy távoli alkalmazásmunkamenetekhez, és számos olyan átirányítást használhat, amelyet a külső ügyfelek támogatnak.

> [!NOTE]
> Az SDK jelenleg fejlesztés alatt áll. Ezt a dokumentumot az SDK elérésére vonatkozó utasításokkal frissítjük, ha az általánosan elérhető.

### <a name="supported-linux-distributions"></a>Támogatott Linux-disztribúciók

Az SDK kompatibilis az Ubuntu 18.04-es vagy újabb verzión alapuló legtöbb operációs rendszerrel. Ha van egy másik Linux disztribúció, tudunk dolgozni veled, hogy kitaláljuk, hogyan lehet a legjobban támogatja az Ön igényeinek.

### <a name="feature-support"></a>Szolgáltatás támogatása

Az SDK támogatja az asztali és távoli alkalmazásmunkamenetekhez való több csatlakozást. A következő átirányítások támogatottak:

| Átirányítás       | Támogatott |
| :---------------- | :-------: |
| Billentyűzet          | &#10004;  |
| Egér             | &#10004;  |
| Hang          | &#10004;  |
| Hang ki         | &#10004;  |
| Vágólap (szöveg)  | &#10004;  |
| Vágólap (kép) | &#10004;  |
| Vágólap (fájl)  | &#10004;  |
| Smartcard         | &#10004;  |
| Meghajtó/mappa      | &#10004;  |

Az SDK több monitormegjelenítési konfigurációját is támogatja, feltéve, hogy a munkamenethez kiválasztott monitorok összefüggőek.

Frissítjük ezt a dokumentumot, amikor új funkciókat és átirányításokat adunk hozzá. Ha új funkciókat és egyéb fejlesztéseket szeretne javasolni, látogasson el [uservoice oldalunkra.](https://go.microsoft.com/fwlink/?linkid=2116523)

## <a name="get-started-with-the-linux-sdk"></a>Ismerkedés a Linux SDK-val

A Windows Virtual Desktop Linux-ügyfélprogram fejlesztése előtt a következő dolgokat kell tennie:

1. Windows virtuális asztali környezet létrehozása és üzembe helyezése tesztelésvagy éles környezet teszteléséhez vagy éles használathoz.
2. Tesztelje a rendelkezésre álló külső ügyfeleket, hogy megismerkedjen a Windows Virtual Desktop felhasználói élményével.

## <a name="next-steps"></a>További lépések

Tekintse meg dokumentációnkat a következő ügyfelek számára:

- [Windows Asztali ügyfél](connect-windows-7-and-10.md)
- [Webes ügyfél](connect-web.md)
- [Android-ügyfél](connect-android.md)
- [macOS-ügyfél](connect-macos.md)
- [iOS-ügyfél](connect-ios.md)
