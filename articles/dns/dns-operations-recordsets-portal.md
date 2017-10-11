---
title: "DNS-rekordhalmazok és az Azure DNS-rekordok kezelése |} Microsoft Docs"
description: "Az Azure DNS lehetővé teszi a DNS-rekordhalmazok és rekordok kezelése, amikor a tartomány."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: 001b80ccba43beab44f6a598f820df65a85a345f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>DNS-rekordok kezelése és a rekordhalmazok az Azure portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Ez a cikk bemutatja, hogyan rekordhalmazok és rekordokat a DNS-zóna kezelése az Azure-portál használatával.

Fontos a DNS-rekordhalmazok és egyedi DNS-rekordok közötti különbségek megértése. A rekordhalmaz zónában azt jelzi, hogy az azonos nevű és azonos típusú gyűjteménye. További információkért lásd: [hozzon létre DNS-rekordhalmazok és az Azure portál használatával rekordok](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Egy új rekordhalmaz és rekord létrehozása

Az Azure-portálon a rekordhalmaz létrehozásához lásd: [hozzon létre DNS-rekordok az Azure portál használatával](dns-getstarted-create-recordset-portal.md).

## <a name="view-a-record-set"></a>A rekordhalmaz megtekintése

1. Az Azure-portálon lépjen a **DNS-zóna** panelen.
2. Keresse meg a rekordhalmaz, és válassza ki azt. Ekkor megnyílik a rekordhalmaz tulajdonságait.

    ![A rekordhalmaz keresése](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>Adjon hozzá egy új rekordot a rekordhalmaz

Minden rekordhalmaz legfeljebb 20 adhat hozzá. A rekordhalmaz nem tartalmazhat két azonos rekordokat. (A nulla rekord) üres rekordhalmazok hozhatók létre, de nem jelennek meg az Azure DNS névkiszolgálóit. CNAME típusú rekordhalmazok legfeljebb egy bejegyzést tartalmazhat.

1. Az a **rekordhalmaz tulajdonságok** a DNS-zóna panelen kattintson a bővíteni kívánt rekordkészlet.

    ![Válassza ki a rekordhalmaz](./media/dns-operations-recordsets-portal/selectset500.png)

2. Adja meg, a rekordhalmaz tulajdonságok a mezők kitöltésével.

    ![Adjon hozzá egy rekordot](./media/dns-operations-recordsets-portal/addrecord500.png)

3. Kattintson a **mentése** mentse a beállításokat a panel tetején. Zárja be a panelt.
4. Sarokban jelenik meg, hogy a rekord menti.

    ![Rekordhalmaz mentése](./media/dns-operations-recordsets-portal/saving150.png)

A rekord mentését követően, az értékek a a **DNS-zóna** panel fogja tartalmazni az új rekordban.

## <a name="update-a-record"></a>Rekord frissítése

Amikor frissít egy meglévő rekordhalmaz rekord, a mezők frissítheti a típusú rekord dolgozunk függ.

1. Az a **rekordhalmaz tulajdonságok** panel a rekordhalmaz, keressen rá a rekordot.
2. Módosítsa a bejegyzést. Amikor módosít egy rekordot, módosíthatja a rendelkezésre álló beállítások a rekordhoz. A következő példában a **IP-cím** mező be van jelölve, és az IP-cím módosított folyamatban van.

    ![Egy rekord módosítása](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Kattintson a **mentése** mentse a beállításokat a panel tetején. A jobb felső sarkában található megjelenik az értesítés a rekord mentett.

    ![Rekordhalmaz mentése](./media/dns-operations-recordsets-portal/saved150.png)

A rekord mentését követően a rekord értékeit beállítani a **DNS-zóna** panel a frissített rekord fogja tartalmazni.

## <a name="remove-a-record-from-a-record-set"></a>A rekordhalmaz bejegyzés eltávolítása

Az Azure portál segítségével rekordok eltávolítása rekordhalmaz. Vegye figyelembe, hogy a rekordhalmaz utolsó rekordját eltávolítása nem törli a rekordhalmaz.

1. Az a **rekordhalmaz tulajdonságok** panel a rekordhalmaz, keressen rá a rekordot.
2. Kattintson a bejegyzésre, hogy el szeretné távolítani. Válassza ki **eltávolítása**.

    ![Bejegyzés eltávolítása](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Kattintson a **mentése** mentse a beállításokat a panel tetején.
4. A rekord eltávolítását követően a rekord értékeit a **DNS-zóna** panel eltávolításának fogja tartalmazni.

## <a name="delete"></a>A rekordhalmaz törlése

1. Az a **rekordhalmaz tulajdonságok** a rekordhalmaz panelen kattintson a **törlése**.

    ![A rekordhalmaz törlése](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Egy üzenet jelenik meg azzal a kérdéssel, ha azt szeretné, a rekordhalmaz törlése.
3. Győződjön meg arról, hogy a neve egyezik-e a rekordhalmaz törlése, és kattintson a kívánt **Igen**.
4. Az a **DNS-zóna** panelen ellenőrizze, hogy a rekordhalmaz már nem látható.

## <a name="work-with-ns-and-soa-records"></a>Az NS és SOA rekordok kezelése

Automatikusan létrehozott NS és SOA rekordokat másképp kezeli az egyéb típusú bejegyzés.

### <a name="modify-soa-records"></a>Módosíthatja a SOA rekordokat

Nem adja hozzá vagy rekordok eltávolítása az automatikusan létrehozott SOA típusú rekordját, állítsa be a zóna felső pontja (név = "@"). Azonban módosíthatja a SOA típusú rekordját (kivéve az "állomás") belül paraméterek egyikét, és a rekordhalmaz TTL-t.

### <a name="modify-ns-records-at-the-zone-apex"></a>A zóna felső pontja a Névkiszolgálói rekordok módosítása

Állítsa be a zóna felső pontja NS-rekord automatikusan létrejön minden DNS-zóna. Az Azure DNS névkiszolgálóit, a zóna nevét tartalmazza.

Hozzáadhat további névhez a kiszolgálók e NS-rekord, támogatja a párhuzamos üzemeltetési tartományok egynél több DNS-szolgáltatónál. A TTL-t és a metaadatok a rekordhalmaz is módosíthatók. Azonban nem lehet eltávolítani, vagy módosítsa az előre megadott Azure DNS névkiszolgálóit.

Vegye figyelembe, hogy csak vonatkozik az NS típusú rekordhalmaz zóna tetején. A zónában (a használt gyermekzónákhoz delegálása) más NS-rekordhalmazok lehet módosítani, korlátozás nélkül.

### <a name="delete-soa-or-ns-record-sets"></a>SOA vagy NS rekordhalmazok törlése

Nem lehet törölni a SOA és NS-rekord beállítja a zóna felső pontja (név = "@"), amely automatikusan jönnek létre a zóna létrehozásakor. Ezek automatikusan törlődnek a zóna törlésekor.

## <a name="next-steps"></a>Következő lépések

* Azure DNS szolgáltatással kapcsolatos további információkért lásd: a [Azure DNS áttekintése](dns-overview.md).
* DNS automatizálásával kapcsolatos további információkért lásd: [létrehozása DNS-zónák és a .NET SDK használatával rekordhalmazok](dns-sdk.md).
* Névkeresési DNS-rekordok kapcsolatos további információkért lásd: [címfeloldási DNS- és támogatás az Azure-ban – áttekintés](dns-reverse-dns-overview.md).
