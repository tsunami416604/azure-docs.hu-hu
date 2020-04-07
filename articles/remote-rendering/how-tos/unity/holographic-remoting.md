---
title: Holografikus távoli átmatozás és távoli renderelés használata egységben
description: Hogyan használható a holografikus átmatoló előnézet az Azure távoli rendereléssel együtt?
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681206"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Holografikus távoli átmatozás és távoli renderelés használata egységben

[A holografikus távoli renderelés](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) és az Azure távoli renderelés kölcsönösen kizárják egymást egy alkalmazáson belül. Mint ilyen, [Egység játék mód](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) van is nem elérhető.

Az Egység szerkesztő minden egyes futtatásához csak az egyik használható a kettő közül. A másik használatához először indítsa újra az Unityt.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>A Unity lejátszási mód dalával megtekintheti a Hololens 2 előnézetét

 A Unity lejátszási mód továbbra is használható, például az alkalmazás felhasználói felületének teszteléséhez. Azonban létfontosságú, hogy az ARR soha ne inicializálva. Különben összeomlik.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR headset használata az asztali előnézethez

Ha windows Mixed Reality VR headset van jelen, akkor a Unity előnézetére használható. Ebben az esetben az ARR inicializálása rendben van, de a WMR headset használat közben nem lehet munkamenethez csatlakozni.
