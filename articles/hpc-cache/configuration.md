---
title: Az Azure HPC gyorsítótár beállításainak konfigurálása
description: Ez a cikk azt ismerteti, hogyan konfigurálhat további beállításokat a gyorsítótárhoz, például az MTU-t és a nem root-fallabda-t, valamint az expressz Pillanatképek elérését az Azure Blob Storage-célokból.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2020
ms.author: v-erkel
ms.openlocfilehash: 88aea7e58aacd9a630771948c6dbc6ed5712a674
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85505307"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>További Azure HPC-gyorsítótár beállításainak konfigurálása

A Azure Portal **konfigurációs** lapja több beállítás testreszabására szolgáló lehetőségeket tartalmaz. A legtöbb felhasználónak nem kell módosítania ezeket a beállításokat az alapértelmezett értékükre.

Ez a cikk azt is ismerteti, hogyan használható az Azure Blob Storage-célok pillanatkép-szolgáltatása. A pillanatkép-szolgáltatáshoz nem tartozik konfigurálható beállítás.

A beállítások megjelenítéséhez nyissa meg a gyorsítótár **konfigurációs** lapját a Azure Portal.

![képernyőkép a Azure Portal konfigurációs oldaláról](media/configuration.png)

> [!TIP]
> Az [Azure HPC cache-gyorsítótárának kezelése videó](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) megjeleníti a konfigurációs oldalt és annak beállításait.

## <a name="adjust-mtu-value"></a>MTU értékének módosítása
<!-- linked from troubleshoot-nas article -->

A gyorsítótár maximális átviteli egységének mérete a legördülő menüben az **MTU-méret**lehetőség kiválasztásával adható meg.

Az alapértelmezett érték 1500 bájt, de módosíthatja azt 1400-ra.

> [!NOTE]
> Ha csökkenti a gyorsítótár MTU-méretét, győződjön meg arról, hogy a gyorsítótárral kommunikáló ügyfelek és tárolók azonos MTU-beállítással vagy alacsonyabb értékkel rendelkeznek.

A gyorsítótár MTU-értékének csökkentése segíthet a csomagok méretének korlátozásában a gyorsítótár hálózatának többi részén. Egyes VPN-EK például nem tudnak teljes méretű, 1500 bájtos csomagokat továbbítani. A VPN-en keresztül küldött csomagok méretének csökkentése feltehetőleg ezt a problémát kizárja. Vegye figyelembe azonban, hogy egy alacsonyabb gyorsítótár-MTU-beállítás azt jelenti, hogy a gyorsítótárral kommunikáló más összetevők, beleértve az ügyfeleket és a tárolási rendszereket is, alacsonyabb MTU-beállítással kell rendelkezniük a kommunikációs problémák elkerüléséhez.

Ha nem szeretné módosítani az MTU-beállításokat más rendszerösszetevőkön, ne csökkentse a gyorsítótár MTU-beállítását. A VPN-csomagok méretére vonatkozó korlátozások más megoldásokkal is használhatók. A probléma diagnosztizálásával és kezelésével kapcsolatos további információkért olvassa el a [VPN-csomagok méretére vonatkozó korlátozásokat](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) a NAS kiszolgáló hibaelhárítási cikkében.

További információ az Azure Virtual Networks MTU-beállításairól: [TCP/IP teljesítmény-hangolás az Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md)-beli virtuális gépekhez.

## <a name="configure-root-squash"></a>A root squash konfigurálása
<!-- linked from troubleshoot -->

A **root squash engedélyezése** beállítás azt szabályozza, hogy az Azure HPC-gyorsítótár hogyan kezeli az ügyfélszámítógépeken a legfelső szintű felhasználótól érkező kéréseket.

Ha a root squash engedélyezve van, a rendszer automatikusan leképezi a felhasználókat az ügyfélről, amikor az Azure HPC cache-n keresztül küldenek kéréseket. Azt is megakadályozza, hogy az ügyfelek kérései a set-UID engedélyezési biteket használják.

Ha a root squash le van tiltva, a rendszer az ügyfél gyökérszintű felhasználójának (UID 0) kérelmét átadja egy háttérként szolgáló NFS Storage rendszernek. Ez a konfiguráció a nem megfelelő fájlok elérését is lehetővé teheti.

A gyorsítótárban lévő legfelső szintű squash beállításával kompenzálhatja a szükséges ``no_root_squash`` beállítást a tárolási célként használt NAS-rendszereken. (További információ az [NFS-tárolási cél előfeltételeiről](hpc-cache-prereqs.md#nfs-storage-requirements).) Emellett az Azure Blob Storage-célokkal való használatkor is javíthatja a biztonságot.

Az alapértelmezett beállítás az **Igen**. (A 2020 áprilisa előtt létrehozott gyorsítótárak esetében az alapértelmezett beállítás **nem**.)

## <a name="view-snapshots-for-blob-storage-targets"></a>BLOB Storage-célokhoz tartozó Pillanatképek megtekintése

Az Azure HPC cache automatikusan menti az Azure Blob Storage-célok tárolási pillanatképeit. A pillanatképek gyors hivatkozási pontot biztosítanak a háttérbeli tároló tartalmához.

A pillanatképek nem helyettesítik az adatok biztonsági mentését, és nem tartalmaznak semmilyen információt a gyorsítótárazott adatok állapotáról.

> [!NOTE]
> Ez a pillanatkép-szolgáltatás eltér a NetApp vagy a Isilon Storage szoftverben található pillanatkép-szolgáltatástól. Ezek a pillanatkép-implementációk a pillanatkép elkészítése előtt ürítik a gyorsítótárból a háttérbeli tárolási rendszerre vonatkozó változásokat.
>
> A hatékonyság érdekében az Azure HPC gyorsítótár-pillanatképe nem üríti ki először a módosításokat, és csak a blob-tárolóba írt adatokat rögzíti. Ez a pillanatkép nem tükrözi a gyorsítótárazott adatok állapotát, ezért előfordulhat, hogy nem tartalmazza a legutóbbi módosításokat.

Ez a funkció csak az Azure Blob Storage-célokhoz érhető el, és a konfigurációja nem módosítható.

A pillanatképeket nyolc óránként, UTC 0:00, 08:00 és 16:00 nyelven kell elvégezni.

Az Azure HPC cache napi, heti és havi pillanatképeket tárol, amíg újakat nem helyettesítenek. A korlátok a következők:

* akár 20 napi pillanatkép
* legfeljebb 8 heti pillanatkép
* legfeljebb 3 havi pillanatkép

A pillanatképeket a `.snapshot` blob Storage-tároló névterében lévő könyvtárból érheti el.
