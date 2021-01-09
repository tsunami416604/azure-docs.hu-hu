---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052927"
---
Az Azure-fájlmegosztás többcsatornás SMB-je jelenleg a következő korlátozásokkal rendelkezik:
- Csak helyileg redundáns FileStorage-fiókokkal használható.
- Csak Windows-ügyfelek esetén támogatott. 
- A csatornák maximális száma négy.
- Az SMB Direct nem támogatott.
- A Storage-fiókokhoz tartozó magánhálózati végpontok nem támogatottak.
- A helyszíni Active Directory tartományi szolgáltatások (AD DS) vagy az Azure AD DS [identitás-alapú hitelesítéssel](../articles/storage/files/storage-files-active-directory-overview.md) rendelkező Storage-fiókok esetében a Azure Files esetében az SMB-ügyfelek nem tudják a Windows fájlkezelővel KONFIGURÁLNI az NTFS-engedélyeket a címtárakban és fájlokban.
    - Az engedélyek konfigurálása helyett használja a Windows [icacls](/windows-server/administration/windows-commands/icacls) eszközt vagy a [set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) parancsot.

