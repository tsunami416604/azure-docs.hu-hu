---
title: Operációs rendszer indítása – a számítógép váratlanul újraindult, vagy váratlan hibát észlelt.
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a virtuális gép váratlan újraindítást vagy hibát tapasztal a Windows telepítésekor.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: daefaca45adb061295928c64b6a0e328a12d8a3e
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85269061"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Operációs rendszer indítása – a számítógép váratlanul újraindult, vagy váratlan hibát észlelt.

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a virtuális gép (VM) váratlan újraindítást vagy hibát tapasztal a Windows telepítésekor.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használatával tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőképen a Windows telepítése a következő hibával meghiúsul:

**A számítógép váratlanul újraindult, vagy váratlan hibába ütközött. A Windows-telepítés nem folytatható. A Windows telepítéséhez kattintson az "OK" gombra a számítógép újraindításához, majd indítsa újra a telepítést.**

![Hiba történt a Windows telepítésének végrehajtása közben: a számítógép váratlanul újraindult, vagy váratlan hibát észlelt. A Windows-telepítés nem folytatható. A Windows telepítéséhez kattintson az "OK" gombra a számítógép újraindításához, majd indítsa újra a telepítést.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Hiba történt, amikor a Windows telepítő elindítja a szolgáltatásokat: a számítógép váratlanul újraindult, vagy váratlan hibát észlelt. A Windows-telepítés nem folytatható. A Windows telepítéséhez kattintson az "OK" gombra a számítógép újraindításához, majd indítsa újra a telepítést.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Ok

A gép egy [általánosított rendszerkép](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)kezdeti indítását kísérli meg, de a feldolgozás alatt álló egyéni válaszfájl (unattend.xml) miatt problémát tapasztal. Az egyéni válaszfájlok nem támogatottak az Azure-ban. 

A válaszfájl egy speciális XML-fájl, amely a Windows Server operációs rendszer telepítése során automatizálni kívánt konfigurációs beállítások definícióit és értékeit tartalmazza. A konfigurációs beállítások közé tartozik a lemezek particionálásával kapcsolatos utasítások, a telepítendő Windows-rendszerkép, az alkalmazandó termékkulcs és a futtatni kívánt egyéb parancsok.

Az Azure-ban az egyéni válaszfájlok nem támogatottak. Ha a lehetőség használatával egyéni **Unattend.xml** fájlt adott meg `sysprep /unattend:<your file’s name>` , akkor ez a hiba történhet.

Az Azure-ban használja a **Sysprep.exe**a beépített **felhasználói élmény (OOBE)** beállítást, vagy használja a `sysprep /oobe` Unattend.xml fájl helyett.

Ez a probléma leggyakrabban akkor jön létre, amikor a **Sysprep.exe** helyszíni virtuális géppel feltölt egy ÁLTALÁNOSÍTOTT virtuális gépet az Azure-ba. Ebben az esetben érdemes lehet egy általánosított virtuális gép megfelelő feltöltését is érdekelni.

## <a name="solution"></a>Megoldás

### <a name="replace-unattended-answer-file-option"></a>A felügyelet nélküli válaszfájl cseréje lehetőség

Ez a helyzet akkor fordul elő, ha egy képet készítettek az Azure-ban való használatra, de egyéni válaszfájlt használtak, amelyet az Azure nem támogat, és a **sysprept** a következő parancshoz hasonló jelzővel használta:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- Az előző parancsban cserélje le a helyére a `<NameOfYourAnswerFile.XML>` fájl nevét.

A probléma megoldásához kövesse [Az Azure-útmutatást a rendszerkép előkészítéséhez/rögzítéséhez](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed) és egy új általánosított rendszerkép előkészítéséhez. A Sysprep alatt ne használja a `/unattend:<answerfile>` jelzőt. Ehelyett csak az alábbi jelzőket használja:

`sysprep /oobe /generalize /shutdown`

- A beépített **felhasználói élmény** (OOBE) az Azure-beli virtuális gépek támogatott beállítása.

Használhatja a **rendszerelőkészítő eszköz grafikus felhasználói felületét** is, hogy ugyanazt a feladatot használja, mint a fenti parancs, és válassza az alább látható beállításokat:

- A beépített felhasználói élmény megadása
- Általánossá tétel
- Leállítás
 
![Rendszer-előkészítő eszköz ablaka, amelyen az OOBE, az általánosítás és a Leállítás lehetőség van kiválasztva.](./media/unexpected-restart-error-during-vm-boot/3.png)
