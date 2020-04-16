---
title: Rendszerkövetelmények
description: Az Azure távoli renderelés rendszerkövetelményeinek listája
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411130"
---
# <a name="system-requirements"></a>Rendszerkövetelmények

> [!IMPORTANT]
> **Az Azure távoli renderelés** jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Ez a fejezet az Azure *remote rendering* (ARR) használatával való munka minimális rendszerkövetelményeit sorolja fel.

## <a name="development-pc"></a>Fejlesztési PC

* Windows 10 1903-as vagy újabb verzió.
* Naprakész grafikus illesztőprogramok.
* Nem kötelező: H265 hardveres videodekóder, ha a távolról megjelenített tartalom helyi előnézetét szeretné használni (például unity).

> [!IMPORTANT]
> A Windows update nem mindig biztosítja a legújabb GPU-illesztőprogramokat, ellenőrizze a GPU gyártójának webhelyét a legújabb illesztőprogramok:
>
> * [AMD-illesztőprogramok](https://www.amd.com/en/support)
> * [Intel illesztőprogramok](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA illesztőprogramok](https://www.nvidia.com/Download/index.aspx)

Az alábbi táblázat felsorolja, hogy mely GPU-k támogatják a H265 hardveres videodekódolást.

| GPU-gyártó | Támogatott modellek |
|-----------|:-----------|
| Nvidia | Ellenőrizze az **NVDEC támogatási mátrixot** [a lap alján](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). A GPU-nak IGEN-re van szüksége a **H.265 4:2:0 8 bites** oszlopban. |
| Amd | GPU-k legalább 6-os verziójú AMD [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6). |
| Intel | Skylake és újabb CPU-k |

Annak ellenére, hogy a megfelelő H265 kodek lehet telepítve, a biztonsági tulajdonságok a codec DLLs okozhat codec inicializálási hibákat. A [hibaelhárítási útmutató](../resources/troubleshoot.md#h265-codec-not-available) a probléma megoldásának lépéseit ismerteti. A DLL-probléma csak akkor fordulhat elő, ha a szolgáltatást asztali alkalmazásban, például unity ben használja.

## <a name="devices"></a>Eszközök

Az Azure Remote Rendering jelenleg csak a **HoloLens 2** és a Windows asztali rendszert támogatja céleszközként. Tekintse meg a [platform korlátozások](../reference/limits.md#platform-limitations) szakaszban.

Fontos, hogy a legújabb HEVC kodek, az újabb verziók jelentős javulást a késés. Annak ellenőrzése, hogy melyik verzió van telepítve az eszközre:

1. Indítsa el a **Microsoft Store áruházat**.
1. Kattintson a jobb felső sarokban található **"..."** gombra.
1. Válassza **a Letöltések és frissítések lehetőséget.**
1. Keressen a **HEVC videobővítmények listájában az eszköz gyártójától.**
1. Győződjön meg arról, hogy a felsorolt kodek legalább **az 1.0.21821.0 verzióval**rendelkezik.
1. Kattintson a **Frissítések beszedése** gombra, és várja meg a telepítését.

## <a name="network"></a>Network (Hálózat)

A stabil, alacsony késleltetésű hálózati kapcsolat kritikus fontosságú a jó felhasználói élmény érdekében.

Lásd a hálózati követelményeket című külön [fejezetet.](../reference/network-requirements.md)

A hálózati problémák elhárításához olvassa el a [Hibaelhárítási útmutatót.](../resources/troubleshoot.md#unstable-holograms)

## <a name="software"></a>Szoftverek

A következő szoftvereket kell telepíteni:

* A Visual **Studio 2019** legújabb verziója [(letöltés)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(letöltés)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(letöltés)](https://git-scm.com/downloads)
* Nem kötelező: A videostream asztali számítógépen történő megtekintéséhez szükség van a **HEVC Video Extensions** [(Microsoft Store link) elemre.](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)

## <a name="unity"></a>Unity

A Unity fejlesztéséhez telepítse a

* Unity 2019.3.1 [(letöltés)](https://unity3d.com/get-unity/download)
* Telepítse ezeket a modulokat unity:
  * **UWP** – Univerzális Windows-platformbuild-támogatás
  * **IL2CPP** – Windows buildtámogatás (IL2CPP)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Modell renderelése unityvel](../quickstarts/render-model.md)
