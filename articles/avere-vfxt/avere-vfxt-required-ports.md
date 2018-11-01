---
title: Az Azure-Avere vFXT port engedélyezett
description: Az Azure-hoz a Avere vFXT által használt portok listája
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 394415ffc7593d058d8d0bf1c0040b88ec25073b
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634176"
---
# <a name="required-ports"></a>Szükséges portok

A portok, a jelen szakaszban felsorolt szolgálnak vFXT bejövő és kimenő kommunikáció.

Soha ne tegye elérhetővé a vFXT fürt vagy a tartományvezérlő-fürtpéldányok közvetlenül a nyilvános interneten.

## <a name="api"></a>API

| Bejövő: | | |
| --- | ---- | --- |
| TCP | 22  | SSH  |
| TCP | 443 | HTTPS|



| Kimenő: |     |       |
|----------|-----|-------|
| TCP      | 443 | HTTPS |

 
## <a name="nfs"></a>NFS

| Bejövő és kimenő  | | |
| --- | --- | ---|
| TCP/UDP | 111  | RPCBIND  |
| TCP/UDP | 2049 | NFS      |
| TCP/UDP | 4045 | NLOCKMGR |
| TCP/UDP | 4046 | MOUNTD   |
| TCP/UDP | 4047 | ÁLLAPOT   |

