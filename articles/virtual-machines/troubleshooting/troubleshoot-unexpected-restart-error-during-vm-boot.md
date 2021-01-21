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
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632690"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Operációs rendszer indítása – a számítógép váratlanul újraindult, vagy váratlan hibát észlelt.

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a virtuális gép (VM) váratlan újraindítást vagy hibát tapasztal a Windows telepítésekor.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztika](./boot-diagnostics.md) használatával tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőképen a Windows telepítése a következő hibával meghiúsul:

**A számítógép váratlanul újraindult, vagy váratlan hibába ütközött. A Windows-telepítés nem folytatható. A Windows telepítéséhez kattintson az "OK" gombra a számítógép újraindításához, majd indítsa újra a telepítést.**

![Hiba történt a Windows telepítésének végrehajtása közben: a számítógép váratlanul újraindult, vagy váratlan hibát észlelt. A Windows-telepítés nem folytatható. A Windows telepítéséhez kattintson az "OK" gombra a számítógép újraindításához, majd indítsa újra a telepítést.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Hiba történt, amikor a Windows telepítő elindítja a szolgáltatásokat: a számítógép váratlanul újraindult, vagy váratlan hibát észlelt. A Windows-telepítés nem folytatható. A Windows telepítéséhez kattintson az "OK" gombra a számítógép újraindításához, majd indítsa újra a telepítést.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Ok

A gép egy [általánosított rendszerkép](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)kezdeti indítását kísérli meg, de a feldolgozás alatt álló egyéni válaszfájl (Unattend.xml) miatt problémát tapasztal. **Az egyéni válaszfájlok nem támogatottak az Azure-ban**. 

A válaszfájl egy speciális XML-fájl, amely a Windows Server operációs rendszer telepítése során automatizálni kívánt konfigurációs beállítások definícióit és értékeit tartalmazza. A konfigurációs beállítások közé tartozik a lemezek particionálásával kapcsolatos utasítások, a telepítendő Windows-rendszerkép, az alkalmazandó termékkulcs és a futtatni kívánt egyéb parancsok.

Az egyéni válaszfájlok nem támogatottak az Azure-ban. Ezért ez a helyzet akkor fordul elő, ha egy rendszerképet készítettek az Azure-ban való használatra, de egyéni Unattend.xml-fájlt adott meg a **SYSPREP** használatával a következő parancshoz hasonló jelzővel:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

Az Azure-ban használja a **rendszerelőkészítő eszköz grafikus felhasználói felületének** **beírása (System out-of-box) (OOBE)** beállítását, vagy használja `sysprep /oobe` a Unattend.xml fájl helyett.

Ez a probléma leggyakrabban akkor jön létre, amikor a sysprept egy helyszíni virtuális géppel használja az általános virtuális gép Azure-ba való feltöltéséhez. Ebben az esetben érdemes lehet egy általánosított virtuális gép megfelelő feltöltését is érdekelni.

## <a name="solution"></a>Megoldás

### <a name="do-not-use-unattendxml"></a>Ne használja a Unattend.xml

> [!TIP]
> Ha a virtuális gép nemrég készült biztonsági másolattal rendelkezik, a rendszerindítási probléma megoldásához próbálja meg [visszaállítani a virtuális gépet a biztonsági mentésből](../../backup/backup-azure-arm-restore-vms.md) .

A probléma megoldásához kövesse [Az Azure-útmutatást a rendszerkép előkészítéséhez/rögzítéséhez](../windows/upload-generalized-managed.md) és egy új általánosított rendszerkép előkészítéséhez. A Sysprep alatt ne **használja a `/unattend:<your file’s name>` jelzőt**. Ehelyett csak az alábbi jelzőket használja:

`sysprep /oobe /generalize /shutdown`

- A beépített felhasználói élmény (OOBE) az Azure-beli virtuális gépek támogatott beállítása.

Használhatja a **rendszerelőkészítő eszköz grafikus felhasználói felületét** is, hogy ugyanazt a feladatot használja, mint a fenti parancs, és válassza az alább látható beállításokat:

- A beépített felhasználói élmény megadása
- Általánossá tétel
- Leállítás
 
![Rendszer-előkészítő eszköz ablaka, amelyen az OOBE, az általánosítás és a Leállítás lehetőség van kiválasztva.](./media/unexpected-restart-error-during-vm-boot/3.png)
