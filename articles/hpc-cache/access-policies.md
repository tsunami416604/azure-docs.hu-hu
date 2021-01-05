---
title: Hozzáférési szabályzatok használata az Azure HPC cache-ben
description: Egyéni hozzáférési házirendek létrehozása és alkalmazása az Azure HPC cache-ben a tárolási célokhoz való hozzáférés korlátozásához
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/28/2020
ms.author: v-erkel
ms.openlocfilehash: 795b194eb7cd31e633128c22ddffe808b32e07da
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802412"
---
# <a name="use-client-access-policies"></a>Ügyfél-hozzáférési házirendek használata

Ez a cikk bemutatja, hogyan hozhat létre és alkalmazhat egyéni ügyfél-hozzáférési házirendeket a tárolási célokhoz.

Az ügyfél-hozzáférési házirendek határozzák meg, hogy az ügyfelek hogyan csatlakozhatnak a tárolási cél exportálásához. Megadhatja a root squash-és olvasási/írási hozzáférést az ügyfél gazdagépén vagy hálózati szintjén.

A hozzáférési házirendek a névtér elérési útjára lesznek alkalmazva, ami azt jelenti, hogy különböző hozzáférési házirendeket használhat két különböző exportáláshoz egy NFS-tárolási rendszeren.

Ez a szolgáltatás olyan munkafolyamatokhoz használható, ahol meg kell határozni, hogy az ügyfelek különböző csoportjai hogyan férhessenek hozzá a tárolási célokhoz.

Ha nincs szüksége a tárolási cél elérésének részletes szabályozására, használhatja az alapértelmezett házirendet, vagy az alapértelmezett szabályzatot további szabályokkal is testreszabhatja.

## <a name="create-a-client-access-policy"></a>Ügyfél-hozzáférési házirend létrehozása

A házirendek létrehozásához és kezeléséhez használja a Azure Portal **ügyfél-hozzáférési házirendek** lapját. <!-- is there AZ CLI for this? -->

[![képernyőkép az ügyfél-hozzáférési házirendek lapról. Több házirend van meghatározva, és néhányat ki kell terjeszteni a szabályok megjelenítésére](media/policies-overview.png)](media/policies-overview.png#lightbox)

Az egyes szabályzatok szabályokból állnak. A szabályokat a rendszer a legkisebb hatókör (gazdagép) és a legnagyobb (alapértelmezett) tartományba tartozó gazdagépekre alkalmazza. A rendszer az első megegyező szabályt alkalmazza, és a későbbi szabályok figyelmen kívül lesznek hagyva.

Új hozzáférési szabályzat létrehozásához kattintson a lista tetején található **+ hozzáférési házirend hozzáadása** gombra. Adja meg az új hozzáférési szabályzat nevét, és adjon meg legalább egy szabályt.

![képernyőkép a hozzáférési szabályzatok szerkesztési paneljéről, amelyekben számos szabály van kitöltve. A szabály mentéséhez kattintson az OK gombra.](media/add-policy.png)

A szakasz további része a szabályokban használható értékeket ismerteti.

### <a name="scope"></a>Hatókör

A hatókör és a Címterület együttesen határozzák meg, hogy mely ügyfelekre van hatással a szabály.

Ezekkel a beállításokkal adhatja meg, hogy a szabály egy adott ügyfélre (gazdagépre), az IP-címek tartományára (hálózat) vagy az összes ügyfélre vonatkozik (alapértelmezés).

Válassza ki a megfelelő **hatókör** -értéket a szabályhoz:

* **Gazdagép** – a szabály egy adott ügyfélre vonatkozik.
* **Hálózat** – a szabály az ügyfelekre vonatkozik az IP-címek egy tartományában.
* **Alapértelmezett** – a szabály az összes ügyfélre vonatkozik.

A szabályzat szabályai ebben a sorrendben vannak kiértékelve. Miután egy ügyfél-csatlakoztatási kérelem megfelel egy szabálynak, a többi figyelmen kívül lesz hagyva.

### <a name="address-filter"></a>Címterület

A **címterület** értéke határozza meg, hogy mely ügyfelek egyeznek a szabállyal.

Ha a hatókört a **gazdagépre** állítja be, csak egy IP-címet adhat meg a szűrőben. A hatókör beállítás **alapértelmezett** értékeként nem adhat meg IP-címeket a **cím szűrő** mezőben, mert az alapértelmezett hatókör minden ügyfélnek megfelel.

A szabályhoz tartozó IP-cím vagy címtartomány megadása. Címtartomány megadásához használja a CIDR-jelölést (például: 0.1.0.0 verziójára/16).

### <a name="access-level"></a>Hozzáférési szint

Állítsa be, hogy milyen jogosultságokat biztosítson a hatókörnek és a szűrőnek megfelelő ügyfelek számára.

A beállítások **olvasási/írási**, **írásvédett** vagy **Nincs hozzáférés**.

### <a name="suid"></a>SUID

Jelölje be a **suid** jelölőnégyzetet, ha engedélyezni szeretné a tárolóban lévő fájlok számára a felhasználói azonosítók beállítását a hozzáféréshez.

A SUID általában a felhasználó jogosultságának ideiglenes növelésére szolgálnak, így a felhasználó elvégezheti az adott fájllal kapcsolatos feladatot.

### <a name="submount-access"></a>Alcsatlakoztatási hozzáférés

Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy a megadott ügyfelek közvetlenül csatlakoztassa az Exportálás alkönyvtárait.

### <a name="root-squash"></a>Gyökér squash

Adja meg, hogy a legfelső szintű squash legyen-e beállítva a szabálynak megfelelő ügyfelek számára.

Ezzel az értékkel engedélyezheti a root squash használatát a tároló exportálási szintjén. [A legfelső szintű squash is beállítható a gyorsítótár szintjén](configuration.md#configure-root-squash).

Ha bekapcsolja a bekapcsolást, a névtelen azonosító felhasználói értékét is be kell állítania a következő lehetőségek egyikére:

* **-2** (senki)
* **65534** (senki)
* **-1** (nincs hozzáférés)
* **65535** (nincs hozzáférés)
* **0** (nem rendszerjogosultságú gyökér)

## <a name="update-access-policies"></a>Hozzáférési szabályzatok frissítése

Az **ügyfél-hozzáférési házirendek** lapon lévő táblából szerkesztheti vagy törölheti a hozzáférési házirendeket.

Kattintson a szabályzat nevére a szerkesztéshez való megnyitásához.

Ha törölni szeretne egy házirendet, jelölje be a neve melletti jelölőnégyzetet a listában, majd kattintson a lista tetején található **Törlés** gombra. A "default" nevű szabályzat nem törölhető.

> [!NOTE]
> Nem törölhet olyan hozzáférési szabályzatot, amely használatban van. Távolítsa el a szabályzatot minden olyan névtérbeli elérési útról, amely tartalmazza azt a törlés megkísérlése előtt.

## <a name="next-steps"></a>További lépések

* A tárolási célok névterének elérési útjain alkalmazza a hozzáférési házirendeket. Olvassa el [az összesített névtér beállítása](add-namespace-paths.md) című témakört.
