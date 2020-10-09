---
title: 'Hivatkozás: ismert problémák & hibaelhárítás'
titleSuffix: Azure Data Science Virtual  Machine
description: Az Azure Data Science Virtual Machine ismert problémáinak, megkerülő megoldásainak és hibaelhárítási listájának beolvasása
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78206520"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Ismert problémák és hibaelhárítás az Azure Data Science Virtual Machine

Ez a cikk segít megkeresni és kijavítani az Azure-Data Science Virtual Machine használatakor esetlegesen előforduló hibákat és hibákat.

## <a name="python-package-installation-issues"></a>Python-csomag telepítési problémái

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Csomagok telepítése pip Breaks-függőségekkel Linux rendszeren

`sudo pip install`A `pip install` csomagok telepítése helyett használja.

## <a name="disk-encryption-issues"></a>A Disk Encryptionnel kapcsolatos problémák

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A lemez titkosítása meghiúsul az Ubuntu DSVM

A Azure Disk Encryption (ADE) jelenleg nem támogatott az Ubuntu-DSVM. Áthidaló megoldásként érdemes lehet konfigurálni [Az Azure Managed Disks kiszolgálóoldali titkosítását](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Az eszköz le van tiltva

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>A Hyper-V nem működik a Windows DSVM

A Hyper-V kezdetben nem működik a Windowsban a várt viselkedés. A rendszerindítási teljesítményhez bizonyos szolgáltatások le lettek tiltva. A Hyper-V engedélyezése:

1. A keresősáv megnyitása a Windows DSVM
1. Írja be a "szolgáltatások" kifejezést
1. Az összes Hyper-V szolgáltatás beállítása kézire
1. A Hyper-V virtuális gépek felügyeletének beállítása az "automatikus" értékre

A végső képernyőnek így kell kinéznie:

   ![A Hyper-V engedélyezése](./media/workaround/hyperv-enable-dsvm.png)

