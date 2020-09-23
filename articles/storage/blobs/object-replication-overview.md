---
title: Objektum-replikáció áttekintése
titleSuffix: Azure Storage
description: Az objektum-replikáció aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálásával csökkentheti az olvasási kérelmek késését, növelheti a számítási feladatok hatékonyságát, optimalizálhatja az adatterjesztést, és csökkentheti a költségeket.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ee76c1692049d5b25e85b6780fbcf78f7ebfdd2f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987047"
---
# <a name="object-replication-for-block-blobs"></a>Objektum-replikálás blokk-blobokhoz

Az objektum-replikáció aszinkron módon másolja a blokk blobokat egy forrás Storage-fiók és egy cél fiók között. Az objektumok replikálása által támogatott egyes forgatókönyvek a következők:

- **Minimalizálja a késést.** Az objektumok replikálása csökkentheti az olvasási kérések késését azáltal, hogy lehetővé teszi az ügyfelek számára, hogy a szorosabb fizikai közelségben lévő régióból származó adatok felhasználását használják.
- **A számítási feladatok hatékonyságának növelése.** Az objektumok replikálásával a számítási feladatok különböző régiókban is feldolgozhatják ugyanezeket a blokk-blobokat.
- **Az adateloszlás optimalizálása.** Egyetlen helyen feldolgozhatja vagy elemezheti az adatforrásokat, majd csak az eredményeket replikálhatja további régiókba.
- **A költségek optimalizálása.** Az adatok replikálása után csökkentheti a költségeket az archiválási szintre való áthelyezéssel a életciklus-kezelési házirendek használatával.

Az alábbi ábra azt mutatja be, hogy az objektum-replikáció hogyan replikálja a blokk-blobokat az egyik régióban található forrás-Storage-fiókból két különböző régióban.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Az objektum-replikáció működését bemutató ábra":::

Az objektumok replikálásának konfigurálásáról az objektum- [replikáció konfigurálása](object-replication-configure.md)című cikk nyújt tájékoztatást.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Az objektumreplikáció előfeltételei

Az objektum-replikációhoz a következő Azure Storage-funkciók is engedélyezve vannak:

- [Módosítási hírcsatorna](storage-blob-change-feed.md): engedélyezni kell a forrásoldali fiókon. További információ a változási csatornák engedélyezéséről: [a módosítási hírcsatorna engedélyezése és letiltása](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Blob verziószámozása](versioning-overview.md): engedélyezni kell a forrás-és a célhelyen is. A verziószámozás engedélyezéséről a [blob verziószámozásának engedélyezése és kezelése](versioning-enable.md)című témakörben olvashat bővebben.

A módosítási hírcsatorna és a blob verziószámozásának engedélyezése további költségeket eredményezhet. További részletekért tekintse meg az [Azure Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/).

## <a name="object-replication-policies-and-rules"></a>Objektum-replikációs házirendek és szabályok

Az objektumok replikálásának konfigurálásakor létre kell hoznia egy replikációs házirendet, amely megadja a forrás Storage-fiókot és a célként megadott fiókot. A replikációs házirend egy vagy több olyan szabályt tartalmaz, amely a forrás tárolót és a célhelyet adja meg, és jelzi, hogy a rendszer melyik blokk blobokat replikálja a forrás tárolóban.

Az objektumok replikálásának konfigurálása után az Azure Storage rendszeresen ellenőrzi a forrás fiók változási csatornáját, és aszinkron módon replikálja az írási vagy törlési műveleteket a célhelyre. A replikációs késés a replikált blokk blob méretétől függ.

> [!IMPORTANT]
> Mivel a blob-adatblokkokat a rendszer aszinkron módon replikálja, a forrás-és a célcím nem azonnal szinkronizálva van. Jelenleg nincs SLA arra vonatkozóan, hogy mennyi időt vesz igénybe az adatreplikálás a célkiszolgálóra.

### <a name="replication-policies"></a>Replikációs házirendek

Az objektumok replikálásának konfigurálásakor a rendszer az Azure Storage erőforrás-szolgáltatón keresztül létrehoz egy replikációs házirendet a forrás és a cél fiókon is. A replikációs házirendet egy házirend-azonosító azonosítja. A forrás-és a célhelyen lévő házirendnek ugyanazzal a házirend-AZONOSÍTÓval kell rendelkeznie ahhoz, hogy a replikálás megtörténjen.

A Storage-fiók legfeljebb két célobjektum forrásaként használható. Előfordulhat, hogy a forrás-és a cél fiók ugyanabban a régióban vagy különböző régiókban található. Emellett különböző előfizetésekben és különböző Azure Active Directory (Azure AD-) bérlőben is lehetnek. Csak egy replikációs házirend hozható létre az egyes forrásoldali fiókokhoz vagy a cél fiókokhoz tartozó párokhoz.

### <a name="replication-rules"></a>Replikációs szabályok

A replikációs szabályok azt határozzák meg, hogy az Azure Storage hogyan replikálja a blobokat a forrás tárolóból a célként megadott tárolóba. Az egyes replikációs házirendekhez legfeljebb 10 replikációs szabályt adhat meg. Mindegyik replikációs szabály egyetlen forrás-és cél tárolót határoz meg, és minden forrás és cél tároló csak egy szabályban használható.

A replikációs szabályok létrehozásakor alapértelmezés szerint csak a forrás-tárolóba felvenni kívánt új blokk-blobokat másolja a rendszer. Megadhatja, hogy az új és a meglévő blokk blobokat is másolja, vagy megadhat egy egyéni másolási hatókört, amely a megadott időpontból létrehozott blokk-blobokat másolja.

Egy vagy több szűrőt is megadhat egy replikációs szabály részeként a blokk Blobok előtag alapján történő szűréséhez. Egy előtag megadásakor a rendszer csak a forrás tárolóban lévő előtaggal egyező blobokat másolja a célhelyre.

A forrás-és a cél tárolóknak is léteznie kell, mielőtt megadhatja őket egy szabályban. A replikációs szabályzat létrehozása után a céltároló csak olvashatóvá válik. A céltárolóba történő írásra tett kísérlet sikertelen lesz a következő hibakóddal: 409 (ütközés). Meghívhatja azonban a [blob szint beállítása](/rest/api/storageservices/set-blob-tier) műveletet a cél tárolóban lévő blobon az archív szintre való áthelyezéshez. Az archiválási szinttel kapcsolatos további információkért lásd [: Azure Blob Storage: gyors, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="billing"></a>Számlázás

Az objektum-replikálás további költségekkel jár a forrás-és a célhelyek olvasási és írási műveleteinél, valamint a kimenő adatforgalomért a forrás-fiókból a cél fiókba történő adatreplikálási költségekkel, valamint a változási adatcsatorna feldolgozásával kapcsolatos költségek beolvasásával.

## <a name="next-steps"></a>Következő lépések

- [Objektumreplikáció konfigurálása](object-replication-configure.md)
- [A hírcsatornák támogatásának módosítása az Azure-ban Blob Storage](storage-blob-change-feed.md)
- [BLOB-verziószámozás engedélyezése és kezelése](versioning-enable.md)
