---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838164"
---
A felhasználó által megadott útvonalak 0.0.0.0/0 célhoz és NSG a GatewaySubnet **nem támogatottak**. Az ezzel a konfigurációval létrehozott átjárókat a rendszer letiltja a létrehozásból. Az átjárók a megfelelő működés érdekében a felügyeleti vezérlőkhöz való hozzáférést igényelnek.
