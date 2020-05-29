---
title: Nyílt bankolás (PSD2) és erős ügyfél-hitelesítés (SCA) Azure-ügyfeleknek
description: Ez a cikk ismerteti, hogy miért szükséges a többtényezős hitelesítés egyes Azure-vásárlásokhoz, és bemutatja a hitelesítés menetét.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 847e9b692ddbd7880c37d1068d61447dcccb9c80
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747150"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Nyílt bankolás (PSD2) és erős ügyfél-hitelesítés (SCA) Azure-ügyfeleknek

2019. szeptember 14-től az [Európai Gazdasági Térség](https://en.wikipedia.org/wiki/European_Economic_Area) 31 országának/régiójának bankjai kötelesek ellenőrizni az online vásárlást végző személy személyazonosságát a fizetés feldolgozása előtt. Az ellenőrzéshez többtényezős hitelesítés szükséges, amely biztosítja az online vásárlások biztonságát és védelmét. Az ellenőrzési követelmény bevezetése egyes országokban vagy régiókban későbbi dátumra esik. További információkért lásd a [Microsoft PSD2-vel kapcsolatos GYIK-oldalát](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Mit jelent a PSD2 az Azure-ügyfelek szempontjából?

Ha az [Európai Gazdasági Térség](https://en.wikipedia.org/wiki/European_Economic_Area) területén működő bank által kiállított hitelkártyával vásárolja meg az Azure szolgáltatásait, többtényezős hitelesítésre lehet szükség a fiókjához megadott fizetési mód használatakor. Előfordulhat, hogy az Azure-fiók regisztrálása vagy az Azure-fiók frissítése során is el kell végeznie a többtényezős hitelesítést – akkor is, ha éppen nem vásárol semmit. A többtényezős hitelesítés szükséges lehet az Azure-fiók fizetési módjának megváltoztatása, a költségkorlát eltávolítása vagy az Azure Portalról végzett azonnali fizetés – például a tartozások kiegyenlítése vagy az Azure-kreditek vásárlása – során is.

Ha a bank elutasítja az Azure-havidíjait, az Azure e-mailt küld a lejárt esedékességről és a megoldáshoz szükséges lépésekről. Az Azure Portalon elvégezheti a többtényezős hitelesítést és kiegyenlítheti a tartozásokat.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>A többtényezős hitelesítés elvégzése az Azure portálon

Az alábbi szakaszok ismertetik, hogyan végezheti el a többtényezős hitelesítést az Azure portálon. Használja az Önre vonatkozó információkat.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Az Azure-fiók aktív fizetési módjának módosítása

Az Azure-fiók aktív fizetési módjának módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) fiókadminisztrátorként, és lépjen a **Költségkezelés + számlázás** oldalra.
2. Az **Áttekintés** lapon válassza ki a megfelelő előfizetést a **Saját előfizetések** táblázatból.
3. A Számlázás területen válassza a **Fizetési módok** elemet. Hozzáadhat új hitelkártyát vagy beállíthat egy meglévő kártyát az előfizetés aktív fizetési módjaként. Ha a bank többtényezős hitelesítést ír elő, a folyamat során a rendszer kéri, hogy adjon választ egy hitelesítési kérdésre.

További részletekért lásd a [Hitelkártya hozzáadása, frissítése vagy eltávolítása az Azure-ban](change-credit-card.md) szakaszt.

### <a name="settle-outstanding-charges-for-azure-services"></a>Azure-szolgáltatások kiegyenlítetlen díjainak rendezése

Ha a bank elutasítja a terhelést, az Azure-fiók állapota **Lejárt** lesz az Azure Portalon. A fiók állapotának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) fiókadminisztrátorként.
2. Végezzen keresést a **Költségkezelés + számlázás** oldalon.
3. A **Költségkezelés + Számlázás** terület **Áttekintés** lapján ellenőrizze a **Saját előfizetések** táblázat Állapot oszlopát.
4. Ha az előfizetés állapota **Lejárt**, kattintson az **Egyenleg rendezése** hivatkozásra. A rendszer kéri, hogy végezze el a többtényezős hitelesítést a folyamat során.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>A Marketplace- és foglalásvásárlások kiegyenlítetlen díjainak rendezése

A Marketplace- és foglalásvásárlásokat az Azure-szolgáltatásoktól külön számlázzuk. Ha a bank elutasítja a Marketplace- vagy foglalásvásárlások terheléseit, a számla lejárt esedékességű lesz, és az Azure Portalon megjelenik az **Azonnali fizetés** lehetőség. A Marketplace- és foglalásvásárlásokról lejárt esedékességű számlák kifizetéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) fiókadminisztrátorként.
2. Végezzen keresést a **Költségkezelés + számlázás** oldalon.
3. A Számlázás területen válassza a **Számlák** elemet.
5. Az előfizetés legördülő szűrőjében válassza ki a Marketplace- vagy foglalásvásárláshoz tartozó előfizetést.
6. A számlák táblázatában tekintse át a Típus oszlopot. Ha a típus **Azure Marketplace és foglalások**, és a számla esedékes vagy lejárt, megjelenik az **Azonnali fizetés** lehetőség. Ha nem jelenik meg az **Azonnali fizetés**, az azt jelenti, hogy a számla már rendezve lett. A rendszer kéri, hogy végezze el a többtényezős hitelesítést az azonnali fizetés során.

## <a name="next-steps"></a>További lépések
- Az Azure-számlák befizetéséről további információt talál [Az Azure-előfizetés lejárt esedékességű tartozásának rendezése](resolve-past-due-balance.md) című szakaszban.
