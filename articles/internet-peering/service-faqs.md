---
title: Társviszony-létesítési szolgáltatás – gyakori kérdések
titleSuffix: Azure
description: Társviszony-létesítési szolgáltatás – gyakori kérdések
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775471"
---
# <a name="peering-service---faqs"></a>Társviszony-létesítési szolgáltatás – gyakori kérdések

Az általános kérdésekért tekintse át az alábbi információkat.

**A szolgáltató használhatja a meglévő közvetlen társviszony-létesítés a Microsoft, hogy támogassa társviszony-létesítési szolgáltatás?**

Igen, a szolgáltató a meglévő PNI-t használhatja a társviszony-létesítési szolgáltatás támogatásához. A társviszony-létesítési szolgáltatás PNI sokszínűséget igényel a HA támogatásához. Ha a meglévő PNI már rendelkezik sokféleséggel, akkor nincs szükség új infrastruktúrára. Ha a meglévő PNI-nek sokszínűségre van szüksége, akkor bővíthető.

**Használhatja egy szolgáltató az új Közvetlen társviszony-létesítést a Microsofttal a társviszony-létesítési szolgáltatás támogatására?**

Igen, ez is lehetséges. A Microsoft a Carrierrel együttműködve új közvetlen társviszony-létesítést hoz létre a társviszony-létesítési szolgáltatás támogatására.  

**Miért van a közvetlen társviszony-létesítés követelmény, hogy támogassa társviszony-létesítési szolgáltatás?**

A társviszony-létesítési szolgáltatás egyik elsődleges illesztőprogramja, hogy egy jól összekapcsolt SP-n keresztül biztosítson kapcsolatot a Microsoft online szolgáltatásaihoz. A PNI mindig Gbps tartományban van, és így alapvető építőköve a szállító és a Microsoft közötti nagy átviteli sebességű kapcsolatnak.

**Milyen sokszínűségi követelmények vonatkoznak a közvetlen társviszony-létesítésre a társviszony-létesítési szolgáltatás támogatásához?**

A PNI-nek támogatnia kell a helyi redundanciát és a georedundanciát. A helyi redundancia egy adott társviszony-létesítési hely két különböző elérési útja. A georedundancia megköveteli, hogy a Carrier további kapcsolattal rendelkezik egy másik Microsoft peremhálózati helyen, ha az elsődleges hely meghibásodik. A rövid hiba időtartama fuvarozó irányíthatja a forgalmat a biztonsági mentési helyen keresztül.

**A fuvarozó már kínál SLA és a vállalati minőségű internet, miben más ez kínál?**

Egyes szolgáltatók sla-t és nagyvállalati szintű internetet kínálnak a hálózat jukon. A Társviszony-létesítési szolgáltatásban a Microsoft sla-forgalmat kínál a hálózat Microsoft-részén. A Társviszony-létesítési szolgáltatás kiválasztásával az ügyfél végpontok között sla-t kap. Az sla-t a webhelyről a Microsoft edge-re az isp-hálózaton az isp lefedheti. SLA a Microsoft Global Network a Microsoft edge a végfelhasználók alkalmazás most a Microsoft által lefedett.

**Ha egy szolgáltató már a PNI-t használva is együttműködik a Microsofttal, akkor milyen módosításokra van szükség a társviszony-létesítési szolgáltatás támogatásához?**

* Szoftvermódosítások a társviszony-létesítő szolgáltatás felhasználójának és forgalmának azonosítására. Szükség lehet útválasztási házirend módosításai a felhasználó forgalmat a legközelebbi Microsoft edge keresztül társviszony-létesítési szolgáltatás.
* Győződjön meg arról, hogy a kapcsolat helyi redundanciával és georedundanciával rendelkezik.
