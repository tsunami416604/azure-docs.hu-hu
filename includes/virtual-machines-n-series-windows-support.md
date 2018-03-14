---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 506c2a4cf675a347dc4c45c9ccf8bce95de2f6fc
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-drivers"></a>Hálózati vezérlő által, NCv2, NCv3 és NT-sorozat - NVIDIA Tesla illesztőprogramok

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [385.54](http://us.download.nvidia.com/Windows/Quadro_Certified/385.54/385.54-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

> [!NOTE]
> A Tesla illesztőprogram letöltési hivatkozásai a kiadás időpontjában aktuálisak. A legújabb illesztőprogramokért látogasson el az [NVIDIA](http://www.nvidia.com/) webhelyére.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Portok HV-sorozat - NVIDIA rács illesztőprogramok

| Operációs rendszer | Illesztőprogram |
| -------- |------------- |
| Windows Server 2016 | [5.2 (386.09) rács](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [5.2 (386.09) rács](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |

> [!NOTE]
> Microsoft újraterjeszti NVIDIA rács illesztőprogram telepítők Permanens virtuális gépekhez. Csak rács illesztőprogramok telepítése Azure Permanens virtuális gépeken. Ezeket az illesztőprogramokat tartalmazza, a licencelés rács virtuális GPU szoftver az Azure-ban.
>