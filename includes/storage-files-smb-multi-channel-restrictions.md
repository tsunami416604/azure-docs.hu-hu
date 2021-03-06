---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995478"
---
Az Azure-fájlmegosztás többcsatornás SMB-je jelenleg a következő korlátozásokkal rendelkezik:
- Csak helyileg redundáns FileStorage-fiókokkal használható.
- Csak Windows-ügyfelek esetén támogatott. 
- A csatornák maximális száma négy.
- Az SMB Direct nem támogatott.
- A helyszíni Active Directory tartományi szolgáltatások (AD DS) vagy az Azure AD DS [identitás-alapú hitelesítéssel](../articles/storage/files/storage-files-active-directory-overview.md) rendelkező Storage-fiókok esetében a Azure Files esetében az SMB-ügyfelek nem tudják a Windows fájlkezelővel KONFIGURÁLNI az NTFS-engedélyeket a címtárakban és fájlokban.
    - Az engedélyek konfigurálása helyett használja a Windows [icacls](/windows-server/administration/windows-commands/icacls) eszközt vagy a [set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) parancsot.

