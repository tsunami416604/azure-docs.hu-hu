---
title: 'Hivatkozás: Ismert problémák & hibaelhárítás'
titleSuffix: Azure Data Science Virtual  Machine
description: Az Azure Data Science virtuális gép ismert problémáinak, kerülő megoldásainak és hibaelhárításának listája
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206520"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Ismert problémák és az Azure Data Science virtuális gép hibaelhárítása

Ez a cikk segít megtalálni és kijavítani azokat a hibákat vagy hibákat, amelyek az Azure Data Science virtuális gép használatakor találkozhatnak.

## <a name="python-package-installation-issues"></a>Python-csomag telepítési problémái

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>A pip csomagok telepítése megszakítja a függőségeket Linuxon

A `sudo pip install` csomagok `pip install` telepítésekor használja a használata helyett.

## <a name="disk-encryption-issues"></a>A Disk Encryptionnel kapcsolatos problémák

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>A lemeztitkosítás sikertelen az Ubuntu DSVM-en

Az Azure Disk Encryption (ADE) jelenleg nem támogatott az Ubuntu DSVM. Kerülő megoldásként fontolja meg [az Azure által felügyelt lemezek kiszolgálóoldali titkosításának](../../virtual-machines/windows/disk-encryption.md)konfigurálását.

## <a name="tool-appears-disabled"></a>Az eszköz letiltva jelenik meg

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>A Hyper-V nem működik a Windows DSVM rendszeren

Ez a Hyper-V kezdetben nem működik a Windows várható viselkedés. A rendszerindítási teljesítmény érdekében letiltottunk néhány szolgáltatást. A Hyper-V engedélyezése:

1. A keresősáv megnyitása a Windows DSVM rendszeren
1. Írja be a "Szolgáltatások" kifejezést
1. Az összes Hyper-V szolgáltatás beállítása "Kézi"
1. Állítsa a "Hyper-V virtuálisgép-kezelés" beállítását "Automatikus"

A végső képernyőnek így kell kinéznie:

   ![A Hyper-V engedélyezése](./media/workaround/hyperv-enable-dsvm.png)

