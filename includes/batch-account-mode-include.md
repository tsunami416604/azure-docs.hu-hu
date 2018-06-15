---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 62bb91a2e51c39caf31719f72d68a6edab9205bc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33903330"
---
> [!NOTE]
> Batch-fiók létrehozásakor kétféle *készletfoglalási* mód közül választhat: **felhasználói előfizetés** és **Batch szolgáltatás**. A legtöbb esetben az alapértelmezett „Batch szolgáltatás” mód használata javasolt, ahol a készleteket a rendszer a háttérben foglalja le Azure által felügyelt előfizetésekben. A szintén választható „Felhasználói előfizetés” mód esetében a Batch virtuális gépei és egyéb erőforrásai közvetlenül az előfizetésben jönnek létre egy készlet létrehozásakor. A „Felhasználói előfizetés” módot akkor kell használnia, ha az [Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/) szolgáltatással szeretne Batch-készleteket létrehozni. Ha felhasználói előfizetési módban szeretne létrehozni Batch-fiókot, az előfizetését az Azure Batchben is regisztrálnia kell, és egy Azure Key Vaulttal is társítania kell.