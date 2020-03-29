---
title: Útmutató a Peering Service szolgáltatáshoz partnerek számára
titleSuffix: Azure
description: Útmutató a Peering Service szolgáltatáshoz partnerek számára
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720248"
---
# <a name="peering-service-partner-walkthrough"></a>Útmutató a Peering Service szolgáltatáshoz partnerek számára

Ez a szakasz azokkal a lépésekkel ismerteti a szolgáltatónak, amelyeket a társviszony-létesítési szolgáltatás közvetlen társviszony-létesítésének engedélyezéséhez kell végrehajtania.

## <a name="create-direct-peering-connection-for-peering-service"></a>Közvetlen társviszony-létesítési kapcsolat létrehozása társviszony-létesítési szolgáltatáshoz
A szolgáltatók bővíthetik földrajzi hatókörüket, ha új közvetlen társviszony-létesítést hoznak létre, amely támogatja a társviszony-létesítési szolgáltatást. Ennek elérése érdekében
1. Legyen társviszony-létesítő szolgáltatás partner, ha még nem
1. Kövesse a [közvetlen társviszony-létesítés létrehozásához vagy módosításához a portálon keresztül](howto-direct-portal.md)című utasításokat. Győződjön meg arról, hogy megfelel a magas rendelkezésre állási követelményeknek.
1. Ezután kövesse a [társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítési a portálon.](howto-peering-service-portal.md)

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Örökölt közvetlen társviszony-létesítési kapcsolat használata társviszony-létesítési szolgáltatáshoz
Ha örökölt közvetlen társviszony-létesítési szolgáltatással rendelkezik, amelyet a társviszony-létesítési szolgáltatás
1. Legyen társviszony-létesítési szolgáltatás partner, ha még nem.
1. Kövesse az utasításokat [egy örökölt közvetlen társviszony-létesítés átalakítása az Azure-erőforrás a portálon keresztül.](howto-legacy-direct-portal.md) Ha szükséges, rendeljen további áramköröket a magas rendelkezésre állási követelmények teljesítéséhez.
1. Ezután kövesse a [társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítési a portálon.](howto-peering-service-portal.md)

## <a name="next-steps"></a>További lépések

* További információ [a társviszony-létesítési házirendről.](https://peering.azurewebsites.net/peering)
* A Közvetlen társviszony-létesítés Microsofttal való beállításának lépéseiről a [Közvetlen társviszony-létesítési útmutató című útmutatót is elolvashatja.](walkthrough-direct-all.md)
* Ha többet szeretne tudni az Exchange-társviszony-létesítés Microsofttal való beállításának lépéseiről, kövesse az [Exchange társviszony-létesítési útmutatóját.](walkthrough-exchange-all.md)