---
title: A virtuális gépek ajánlati adatainak konfigurálása az Azure Marketplace-en
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek ajánlati adatait az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284345"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>A virtuális gépek ajánlati listájának konfigurálása – részletek

Az **ajánlati lista** lapon megadhatja az ajánlat részleteit, például az ajánlat nevét, a leírását, a hivatkozásokat és a névjegyeket.

> [!NOTE]
> Az ajánlat tartalmait, például a leírást, a dokumentumokat, a képernyőképeket és a használati feltételeket nem kell angolul megadnia, feltéve, hogy az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak a (z) verzióban érhető el \<non-English language> ." Megadhat egy URL-címet is, amely egy olyan webhelyre mutató hivatkozást tartalmaz, amely nem az ajánlatban szereplő tartalomban használt tartalommal rendelkezik.

## <a name="marketplace-details"></a>Piactér – részletek

### <a name="name"></a>Name

Az itt megadott név jelenik meg az ügyfelek számára az ajánlati lista címeként. Ez a mező az ajánlat **aliasa** mezőben megadott névvel van feltöltve az ajánlat létrehozásakor. A nevet később módosíthatja. A név:

- Lehet védjeggyel ellátott. A védjegyek és a szerzői jogi szimbólumok is megadhatók.
- Legfeljebb 50 karakterből állhat.
- Nem szerepelhetnek hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását, amely az Azure Marketplace keresési eredményei között jelenik meg. Akár 100 karaktert is tartalmazhat.

### <a name="long-summary"></a>Hosszú összefoglalás

Adja meg az ajánlatát, hogy megjelenjen az Azure Marketplace keresési eredményei között. Akár 256 karaktert is tartalmazhat.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

HTML-címkék használatával formázhatja a leírást, így jobban megtarthatja a használatát. Az engedélyezett címkék listáját lásd: [támogatott HTML-címkék](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Adatvédelmi szabályzat hivatkozása

Adja meg a szervezete adatvédelmi szabályzatának webcímet (URL-címét). Győződjön meg arról, hogy az ajánlat megfelel az adatvédelmi törvényeknek és előírásoknak. Érvényes adatvédelmi szabályzatot is közzé kell tennie a webhelyén.

## <a name="useful-links"></a>Hasznos hivatkozások

Adja meg az ajánlatával kapcsolatos kiegészítő online dokumentumokat. Hivatkozás hozzáadásához válassza a **hivatkozás hozzáadása**lehetőséget, majd hajtsa végre a következő mezőket:

- **Név**: az ügyfelek a részleteket tartalmazó oldalon fogják látni a nevet.
- **Hivatkozás (URL)**: adjon meg egy hivatkozást, amely lehetővé teszi az ügyfelek számára az online dokumentum megtekintését.

## <a name="customer-support-links"></a>Ügyfélszolgálati hivatkozások

Adja meg a támogatási webhelyet, ahol az ügyfelek elérheti a támogatási csapatot.

- Azure globális támogatási webhely
- Azure Government támogatási webhely

## <a name="partner-support-contact"></a>Partneri támogatási kapcsolattartó

Adja meg a Microsoft-partnerek számára a támogatási jegy megnyitásakor használandó kapcsolattartási adatokat. Ez az információ nem szerepel az Azure Marketplace-en.

- Name
- E-mail
- Telefon

## <a name="engineering-contact"></a>Mérnöki kapcsolattartó

Adja meg a Microsoft számára az ajánlattal kapcsolatos problémák esetén használandó kapcsolattartási adatokat, beleértve a minősítéssel kapcsolatos problémákat is. Ez az információ nem szerepel az Azure Marketplace-en.

- Name
- E-mail
- Telefon

## <a name="azure-marketplace-media"></a>Az Azure Marketplace adathordozója

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek PNG formátumúnak kell lennie. A homályos képek miatt a rendszer elutasítja a kérelmet.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

### <a name="azure-marketplace-logos"></a>Azure Marketplace-logók

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** és **közepes** embléma létrehozásához. Ezeket később is lecserélheti a különböző rendszerképekre.

- **Nagyméretű** (216 x 216 és 350 x 350 px, kötelező)
- **Közepes** (90 x 90 px, nem kötelező)
- **Kicsi** (48 x 48 px, opcionális)

Ezeket az emblémákat a lista különböző helyein használják:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Képernyőképek

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Minden képernyőképnek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie. Minden képernyőképnek tartalmaznia kell egy feliratot.

### <a name="videos"></a>Videók

Akár öt videót is hozzáadhat, amelyek bemutatják az ajánlatát. A videókat külső videó szolgáltatásban kell tárolni. Adja meg a videó nevét, webcímeit és miniatűr PNG-képét 1280 x 720 képpontban.

További információforrások a piactéren: ajánlott [eljárások a Piactéri ajánlatokhoz](gtm-offer-listing-best-practices.md).

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

- [Csomagok létrehozása](azure-vm-create-plans.md)
