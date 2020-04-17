---
title: Az Azure HPC-gyorsítótár beállításainak konfigurálása
description: Bemutatja, hogyan konfigurálhat további beállításokat a gyorsítótárhoz, például az MTU-hoz és a root-squash-hoz, és hogyan érheti el az Azure Blob storage-tárolók gyors pillanatképeit.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538816"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>További Azure HPC-gyorsítótár-beállítások konfigurálása

Az Azure Portal **konfigurációs** lapján számos beállítás testreszabása lehetőségeket. A legtöbb felhasználónak nem kell ezeket az alapértelmezett értékekről módosítania.

Ez a cikk azt is ismerteti, hogyan használhatja a pillanatkép-szolgáltatás az Azure Blob storage-tároló célok. A pillanatkép funkció nem rendelkezik konfigurálható beállításokkal.

A beállítások megtekintéséhez nyissa meg a gyorsítótár **konfigurációs** lapját az Azure Portalon.

![képernyőkép az Azure Portal konfigurációs lapjáról](media/configuration.png)

## <a name="adjust-mtu-value"></a>MTU értékének módosítása
<!-- linked from troubleshoot-nas article -->

A gyorsítótár maximális átviteli egységméretét az **MTU-méret**feliratú legördülő menüsegítségével választhatja ki.

Az alapértelmezett érték 1500 bájt, de 1400-ra módosítható.

> [!NOTE]
> Ha csökkenti a gyorsítótár MTU-méretét, győződjön meg arról, hogy a gyorsítótárral kommunikáló ügyfelek és tárolórendszerek azonos MTU-beállítással vagy alacsonyabb értékkel rendelkeznek.

A gyorsítótár MTU-értékének csökkentése segíthet a csomagméret-korlátozások megoldásában a gyorsítótár hálózatának többi részében. Egyes VPN-ek például nem tudják sikeresen továbbítani a teljes méretű 1500 bájtos csomagokat. A VPN-en keresztül küldött csomagok méretének csökkentése kiküszöbölheti ezt a problémát. Azonban vegye figyelembe, hogy az alacsonyabb cache MTU beállítás azt jelenti, hogy minden más összetevő, amely kommunikál a gyorsítótár - beleértve az ügyfelek és a tárolórendszerek - is kell egy kisebb beállítást, hogy elkerüljék a kommunikációs problémák a gyorsítótárral.

Ha nem szeretné módosítani az MTU-beállításokat más rendszerösszetevőkön, ne csökkentse a gyorsítótár MTU-beállítását. Vannak más megoldások is a VPN-csomagméret-korlátozások megkerülésére. Olvassa el [a VPN-csomagméret-korlátozások módosítása](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) című témakört a NAS hibaelhárítási cikkében, ha többet szeretne megtudni a probléma diagnosztizálásáról és kezeléséről.

Az Azure virtuális hálózatainak MTU-beállításairól az [Azure virtuális gépek TCP/IP-teljesítményhangolásának](../virtual-network/virtual-network-tcpip-performance-tuning.md)olvasásával olvashat bővebben.

## <a name="configure-root-squash"></a>Root squash konfigurálása
<!-- linked from troubleshoot -->

A **root squash engedélyezése** beállítás szabályozza, hogy az Azure HPC-gyorsítótár lehetővé teszi a root hozzáférést. Root squash segít megakadályozni root-szintű hozzáférést a jogosulatlan ügyfelek.

Ez a beállítás lehetővé teszi a felhasználók számára a ``no_root_squash`` gyökérhozzáférés vezérlését a gyorsítótár szintjén, ami segíthet ellensúlyozni a tárolási célként használt NAS-rendszerek szükséges beállítását. (További információ az [NFS-tárolási célelőfeltételekről.)](hpc-cache-prereqs.md#nfs-storage-requirements) Az Azure Blob storage-tárolókkal való használat során is növelheti a biztonságot.

Az alapértelmezett beállítás **igen**. (A 2020 áprilisa előtt létrehozott gyorsítótárak alapértelmezett beállítása **:)** Ha engedélyezve van, ez a szolgáltatás megakadályozza a set-UID engedélybitek használatát a gyorsítótárba irányuló ügyfélkérelmekben.

## <a name="view-snapshots-for-blob-storage-targets"></a>Blob storage-tárolók pillanatképeinek megtekintése

Az Azure HPC cache automatikusan menti a tárolási pillanatképeket az Azure Blob storage-tároló célokhoz. A pillanatképek rövid referenciapontot nyújtanak a háttértároló tartalmához. A pillanatképek nem helyettesítik az adatbiztonsági mentéseket, és nem tartalmaznak semmilyen információt a gyorsítótárazott adatok állapotáról.

> [!NOTE]
> Ez a pillanatkép funkció eltér a NetApp, Isilon vagy ZFS tárolószoftverben található pillanatkép-funkciótól. Ezek a pillanatkép-implementációk kiüríti a gyorsítótárból a háttértároló rendszer, mielőtt a pillanatkép módosításait.
>
> A hatékonyság érdekében az Azure HPC cache pillanatkép nem kiüríti a módosításokat először, és csak a Blob-tárolóba írt adatokat rögzíti. Ez a pillanatkép nem a gyorsítótárazott adatok állapotát jelöli, ezért a legutóbbi módosítások kizárhatók.

Ez a funkció csak az Azure Blob storage-célok hoz érhető el, és a konfiguráció nem módosítható.

A pillanatképek nyolcóránként, UTC 0:00, 08:00 és 16:00 órakor készülnek.

Az Azure HPC cache tárolja a napi, heti és havi pillanatképek, amíg azok helyébe újakat. A határértékek a következők:

* akár 20 napi pillanatképek
* akár 8 heti pillanatfelvétel
* akár 3 havi pillanatfelvétel

A pillanatképek a `.snapshot` blob storage-tároló névterében lévő könyvtárból.
