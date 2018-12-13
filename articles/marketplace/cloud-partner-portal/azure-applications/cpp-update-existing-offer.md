---
title: Egy Azure-alkalmazás létező ajánlat frissítése |} A Microsoft Docs
description: Hogyan frissíthető egy meglévő Azure-alkalmazás az ajánlat az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 3fbbf688f6de7c3fceb6695a6b085f917dbec242
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196943"
---
# <a name="update-an-existing-azure-application-offer"></a>Egy Azure-alkalmazás létező ajánlat frissítése

Számos különböző típusú frissítések, amelyeket érdemes tennie az ajánlatra, miután közzé lett téve, és azt élő. Az ajánlat új verziójának a módosítás kell menteni és újra közzé, hogy tükrözze a Marketplace-en. Ez a cikk végigvezeti a felügyelt alkalmazás ajánlatot frissítése különböző aspektusainak a [Cloud Partner Portalon](https://cloudpartner.azure.com/).

Miért érdemes az ajánlatot, mint például frissítése több oka is van:

- Egy új lemezkép-verzió hozzáadása meglévő termékváltozatokra.
- Hozzáadás, új termékváltozatokra.
- Az ajánlat vagy egyes termékváltozatok marketplace metaadatainak frissítésekor.

Segítség ezeket a módosításokat, a portál biztosít a **összehasonlítása** és **előzmények** funkciókat.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Egy Azure-alkalmazás az ajánlat és az SKU engedett módosítása

Nincsenek a tároló ajánlat és a Termékváltozat, amely nem módosítható, miután az Azure piactéren az ajánlat élő attribútumainak. A következő beállítások nem módosíthatók:

- Ajánlat és az ajánlat közzétevő-azonosító
- A meglévő termékváltozatok SKU-azonosítója
- Verzió címkék, például: `1.0.1`
- Számlázási és licencelési modell módosítja a meglévő termékváltozatok

## <a name="common-update-operations"></a>Gyakori frissítési műveletek

A következő frissítési műveletek gyakoriak.

### <a name="update-image-version-for-a-sku"></a>A Termékváltozat-es frissítési lemezkép verziója

Célszerű a gyakori rendszerképek rendszeresen frissíteni kell a biztonsági javítások, további funkciók és így tovább. Ebben a forgatókönyvben szeretné frissíteni a lemezképet, amely a Termékváltozat hivatkozik az alábbi lépések segítségével:

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg a frissíteni kívánt ajánlat.
3. Az a **termékváltozatok** lapra, válassza ki a lemezképhez hozzárendelt Termékváltozat frissítése.
4. Válassza ki **+ új lemezkép verziója** új lemezkép hozzáadásához.
5. Adja meg az új lemezkép-verziókat. A rendszerkép verziószámát van szüksége, mint a korábbi verziók ugyanez a címkék az útmutató követéséhez. Verzió címkék az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lenniük. Ellenőrizze, hogy nagyobb, mint a korábbi verziók, adja meg az új verzióra.
6. Válassza ki **közzététel** az új tároló-lemezkép verziója az Azure piactéren közzétenni a munkafolyamat elindításához.

### <a name="add-a-new-sku"></a>Adjon hozzá egy új Termékváltozatot

Használja az alábbi lépéseket, hogy egy új Termékváltozatban érhető el az ajánlatot:

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg a frissíteni kívánt ajánlat.
3. Alatt a **termékváltozatok** lapon jelölje be **adja hozzá az új Termékváltozat** , és adja meg egy **SKU-azonosítója** a felugró ablakban.
4. Tegye közzé újra az ajánlat az ismertetett lépéseket követve [közzététele az Azure application ajánlat](./cpp-publish-offer.md).
5. Válassza ki **közzététel** közzététele az új Termékváltozat a munkafolyamat elindításához.

### <a name="update-offer-marketplace-metadata"></a>Az ajánlat marketplace metaadatok frissítése

A következő lépések segítségével frissíteni az ajánlatot társított marketplace metaadatait. (Például: cég neve, emblémák, stb.)

1. Jelentkezzen be a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
2. A **minden ajánlat**, keresse meg az ajánlat, amelyet szeretne frissíteni.
3. Nyissa meg a **Marketplace** fülre. Kövesse az utasításokat a [közzététele az Azure application ajánlat](./cpp-publish-offer.md) metaadatok módosításokat.
4. Válassza ki **közzététel** a módosítások közzétételéhez a munkafolyamat elindításához.

## <a name="deleting-an-existing-offer"></a>Egy meglévő ajánlat törlése

Dönthet úgy, távolítsa el az ajánlatot a piactérről. Az ajánlat törlése nem befolyásolja a jelenlegi vásárlások az adott ajánlat. Ezen vásárlásokkal továbbra is működnek, mint korábban. Azonban az ajánlat nem érhető el minden olyan új vásárlások a Törlés befejezése után.

Az ajánlat megszüntetése a szolgáltatás és/vagy az Ön és ügyfelei közötti licencszerződés megszüntetését jelenti.
Útmutatás és ajánlatok eltávolítását és megszüntetését házirendek vonatkoznak rájuk a Microsoft Marketplace kiadói szerződésében (lásd 7. szakasz) és a részvételi szabályzatának megfelelően (lásd a szakasz 6.2).

További információkért lásd: [törlés és az Azure Marketplace-ről származó ajánlat/SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>A szolgáltatás összehasonlítása

Ha módosít egy közzétett ajánlatunk, a Compare-funkció segítségével végrehajtott módosítások naplózása.

Az összehasonlítás funkció használatához:

1. A szerkesztési folyamat bármely mozzanata válassza ki az ajánlatban összehasonlítása.
2. Tekintse meg a marketing eszközök és metaadatok verzióinak egymás mellett.

## <a name="history-of-publishing-actions"></a>Közzétételi műveletek előzményei

Közzétételi végzett tevékenység előzményeit, jelölje ki a **előzmények** lap meg a bal oldali navigációs menü sáv a Cloud Partner portálra. Láthatja, hogy az Azure Marketplace-ajánlat teljes élettartama során végrehajtott időbélyegzővel műveletek.

## <a name="next-steps"></a>További lépések

[Azure-alkalmazásajánlat](./cpp-azure-app-offer.md)