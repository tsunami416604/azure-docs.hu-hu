---
title: A holografikus távelérés és a távoli renderelés használata a Unityben
description: Hogyan használható a holografikus távelérési előzetes verzió az Azure távoli rendereléssel együtt
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681206"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>A holografikus távelérés és a távoli renderelés használata a Unityben

A [holografikus](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) távelérés és az Azure távoli renderelés kölcsönösen kizárják egymást egy alkalmazáson belül. Ilyenkor az [egységes lejátszási mód](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) is nem érhető el.

Az Unity Editor minden egyes futtatása esetén csak a kettő közül lehet használni. Ha a másikat szeretné használni, először indítsa újra az egységet.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>A 2. Hololens előzetes verziójának használata Unity lejátszási módban

 Az egység lejátszási módja továbbra is használható, például az alkalmazás felhasználói felületének teszteléséhez. Azonban fontos, hogy az ARR soha ne legyen inicializálva. Ellenkező esetben a rendszer összeomlik.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>WMR VR-fülhallgató használata az asztal előnézetéhez

Ha a Windows vegyes valóság VR headset van jelen, az egységen belül is megtekinthető. Ebben az esetben a rendszer nem tudja inicializálni az ARR-t, azonban a WMR headset használata közben nem lehet csatlakozni a munkamenethez.
