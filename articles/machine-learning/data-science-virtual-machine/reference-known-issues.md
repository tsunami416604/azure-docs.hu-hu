---
title: Ismert problémák & hibaelhárítás során
titleSuffix: Azure Data Science Virtual  Machine
description: Az Azure Data Science Virtual Machine ismert problémáinak, megkerülő megoldásainak és hibaelhárítási listájának beolvasása
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301918"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Ismert problémák és hibaelhárítás az Azure Data Science Virtual Machine

Ez a cikk segít megkeresni és kijavítani az Azure-Data Science Virtual Machine használatakor észlelt hibákat és hibákat.

## <a name="python-package-installation-issues"></a>Python-csomag telepítési problémái

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Csomagok telepítése pip Breaks-függőségekkel Linux rendszeren

Csomagok telepítésekor a `sudo pip install` helyett használja a `pip install` értéket.

## <a name="disk-encryption-issues"></a>Lemezes titkosítási problémák

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A lemez titkosítása meghiúsul az Ubuntu DSVM

A Azure Disk Encryption (ADE) jelenleg nem támogatott az Ubuntu-DSVM. Áthidaló megoldásként érdemes lehet [Az Azure Storage-titkosítást az ügyfél által felügyelt kulcsokkal](../../storage/common/storage-encryption-keys-portal.md)konfigurálni.

## <a name="tool-appears-disabled"></a>Az eszköz le van tiltva

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>A Hyper-V nem működik a Windows DSVM

Ez a várt viselkedés, ahogy a rendszerindítási teljesítmény esetében bizonyos szolgáltatások le vannak tiltva. Az ismételt engedélyezéshez nyissa meg a keresési sávot a Windows DSVM, írja be a "szolgáltatások" kifejezést, majd állítsa be az összes Hyper-V szolgáltatást a "Manual" értékre, és állítsa a "Hyper-V virtuális gép kezelése" értéket "automatikus" értékre.

A végső képernyőnek így kell kinéznie:

   ![A Hyper-V engedélyezése](./media/workaround/hyperv-enable-dsvm.png)

