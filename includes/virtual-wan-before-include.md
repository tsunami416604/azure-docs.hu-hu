---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a3b9160f1fb28c68bdbaa540c78b2890dbcab82
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812737"
---
* Rendelkezik egy virtuális hálózattal, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid [útmutatót.](../articles/virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat átjáróval rendelkezik (VPN vagy ExpressRoute), akkor el kell távolítania az összes átjárót. Ehhez a konfigurációhoz az szükséges, hogy a virtuális hálózatok a virtuális WAN hub-átjáróhoz legyenek csatlakoztatva.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális WAN által létrehozott és használt virtuális hálózat. Az hubhoz megadott címtartomány nem fedi át a meglévő virtuális hálózatait, amelyhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
