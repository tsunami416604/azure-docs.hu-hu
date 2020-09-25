---
title: Az Azure számlázásos fizetési módjának törlése
description: Ismerteti, hogyan törölhet egy Azure-előfizetés által használt fizetési módot.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: b26958037cf7fd752c6cf07078b4a627fed64b2b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91003117"
---
# <a name="delete-an-azure-billing-payment-method-preview"></a>Az Azure számlázásos fizetési módjának törlése (előzetes verzió)

Ez a dokumentum útmutatást nyújt ahhoz, hogyan törölhet egy fizetési módot, például bankkártyát különböző típusú Azure-előfizetésekből. Az alábbiakhoz használt fizetési módokat törölheti:

- Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA)
- Microsoft Online Services Program (MOSP), amely használatalapú fizetés néven is ismert

Az Azure-előfizetést annak típusától függetlenül le kell mondania ahhoz, hogy törölni tudja a hozzá tartozó fizetési módot.

Más Azure-előfizetések, mint például a Microsoft Partnerszerződés és a Nagyvállalati Szerződés fizetési módjának eltávolítása nem támogatott.

## <a name="delete-an-mca-payment-method"></a>MCA fizetési mód törlése

Csak az a felhasználó törölhet fizetési módot, aki a Microsoft Ügyfélszerződést létrehozta.

A Microsoft Ügyfélszerződéshez tartozó fizetési mód törléséhez hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com/ webhelyen.
1. Lépjen a **Cost Management + Számlázás** lapra.
1. Ha szükséges, válasszon ki egy számlázási hatókört.
1. A bal oldali menüben a **Számlázás** szakasz alatt lévő listából válassza a **Számlázási profilok** lehetőséget.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Képernyőkép számlázási profilokról az Azure Portalon" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. A számlázási profilok listájából válassza ki azt a profilt, amelyben a fizetési mód használatban van.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Szemléltető kép a számlázási profilok listájáról" :::
1. A bal oldali menüben a **Beállítások** szakasz alatt lévő listából válassza a **Fizetési módok** lehetőséget.
1. A számlázási profilja fizetési módok lapján a **Saját hitelkártyák** szakasz alatt megjelenik a fizetési módok táblázata. Keresse meg a törölni kívánt hitelkártyát, válassza a három pontot ( **...** ), majd a **Törlés** lehetőséget.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Szemléltető kép arról, hogy hol lehet egy hitelkártyát törölni" :::
1. Megjelenik a Fizetési mód törlése oldal. Az Azure ellenőrzi, hogy a fizetési mód használatban van-e.
    - Ha a fizetési mód nincs használatban, a **Törlés** lehetőség engedélyezve van. Válassza ezt a lehetőséget a hitelkártyaadatok törléséhez.
    - Ha a fizetési mód használatban van, akkor le kell cserélni vagy le kell választani. Ehhez olvassa el a további szakaszokat. Ezek ismertetik, hogyan lehet **leválasztani** az előfizetés által használt fizetési módokat.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Egy MCA számlázási profil által használt fizetési mód leválasztása

Ha a fizetési módot használja egy MCA számlázási profil, az alábbi példához hasonló üzenetet fog látni.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Szemléltető kép arra, amikor egy fizetési módot használ egy Microsoft Ügyfélszerződés" :::

A fizetési módok leválasztásához több feltételnek is teljesülnie kell. Ha a feltételek valamelyike nem teljesül, megjelenik egy útmutatás arról, hogyan lehet teljesíteni az adott feltételt. Egy hivatkozás is megjelenik, amely átirányítja arra a helyre, ahol teljesítheti a feltételt.

Ha minden feltétel teljesült, leválaszthatja a fizetési módot a számlázási profilról.

> [!NOTE]
> Ha az alapértelmezett fizetési mód le van választva, a számlázási profil _inaktív_ állapotba kerül. A folyamat során törölt elemeket nem lehet később visszaállítani. Miután a számlázási profilt inaktívra állította, egy új Azure-előfizetést kell regisztrálnia új erőforrások létrehozásához.

#### <a name="to-detach-a-payment-method"></a>Fizetési mód leválasztása

1. A Fizetési mód törlése területen válassza **az aktuális fizetési mód leválasztására** szolgáló hivatkozást.
1. Ha minden feltétel teljesült, válassza a **Leválasztás** lehetőséget. Ha nem, folytassa a következő lépéssel.
1. Ha a Leválasztás nem érhető el, megjelenik egy lista a feltételekről. Végezze el a listázott műveleteket. Válassza az Alapértelmezett fizetési mód leválasztása területen megjelenő hivatkozást. Íme egy példa egy korrekciós műveletről, amely ismerteti a szükséges lépéseket.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Példa az MCA fizetési módjának leválasztásához szükséges korrekciós műveletről" :::
1. Ha kiválasztja a korrekciós művelet hivatkozását, a rendszer átirányítja az Azure-oldalra, ahol elvégezheti a műveletet. Végezze el az összes szükséges műveletet.
1. Ha szükséges, végezze el az összes többi korrekciós műveletet is.
1. Lépjen vissza a **Költségkezelés + Számlázás** > **Számlázási profilok** > **Fizetési módok** lapra. Válassza a **Leválasztás** lehetőséget. Az Alapértelmezett fizetési mód leválasztása oldal alján válassza a **Leválasztás** elemet.

> [!NOTE]
> - Az előfizetés lemondása után az előfizetés törlése akár 90 napig is eltarthat. Ha rövidíteni szeretné a várakozási időt, hozzon létre egy Azure-támogatáskérést, és kérje az előfizetés azonnali törlését.
> - Fizetési mód törlése csak akkor lehetséges, ha a számlázási profilhoz kapcsolódó összes korábbi díj ki lett fizetve. Ha éppen egy aktív számlázási időszakban van, meg kell várnia a számlázási időszak végét a fizetési mód törléséhez. **Amíg a számlázási időszak végére vár, győződjön meg arról, hogy az összes többi leválasztási feltétel teljesül**.

## <a name="delete-a-mosp-payment-method"></a>MOSP fizetési mód törlése

A fizetési mód törléséhez fiókadminisztrátornak kell lennie.

Ha a fizetési módja használatban van egy MOSP-előfizetéshez, végezze el a következő lépéseket.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com/ webhelyen.
1. Lépjen a **Cost Management + Számlázás** lapra.
1. Ha szükséges, válasszon ki egy számlázási hatókört.
1. A bal oldali menüben a **Számlázás** szakasz alatt lévő listából válassza a **Fizetési módok** lehetőséget.
1. A Fizetési módok területen válassza ki azt a _sort_, amelyben a fizetési mód szerepel. Ne válassza ki a fizetési mód hivatkozását. Előfordulhat, hogy nem kap vizuális visszajelzést arról, hogy kiválasztotta a fizetési módot.
1. Válassza a **Törlés** elemet.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Példa az MOSP fizetési módjának leválasztásához szükséges korrekciós műveletről" :::
1. Ha minden feltétel teljesült, a Fizetési mód törlése területen válassza a **Törlés** lehetőséget. Ha a Törlés nem érhető el, folytassa a következő lépéssel.
1. Megjelenik egy lista a feltételekről. Végezze el a listázott műveleteket. Válassza a Fizetési mód törlése területen megjelenő hivatkozást.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Szemléltető kép arról, amikor egy fizetési mód használatban van egy MOSP-előfizetéshez" :::
1. Ha kiválasztja a korrekciós művelet hivatkozását, a rendszer átirányítja az Azure-oldalra, ahol elvégezheti a műveletet. Végezze el az összes szükséges műveletet.
1. Ha szükséges, végezze el az összes többi korrekciós műveletet is.
1. Lépjen vissza a **Költségkezelés + Számlázás** > **Számlázási profilok** > **Fizetési módok** lapra, majd törölje a fizetési módot.

> [!NOTE]
> Az előfizetés lemondása után az előfizetés törlése akár 90 napig is eltarthat. Ha rövidíteni szeretné a várakozási időt, hozzon létre egy Azure-támogatáskérést, és kérje az előfizetés azonnali törlését.

## <a name="next-steps"></a>Következő lépések

- Az Azure-előfizetés lemondásával kapcsolatos további információkért lásd [Az Azure-előfizetés lemondása](cancel-azure-subscription.md) szakaszt.
- Hitelkártya hozzáadásával vagy frissítésével kapcsolatos további információért lásd a [Hitelkártya hozzáadása vagy frissítése az Azure-ban](change-credit-card.md) szakaszt.