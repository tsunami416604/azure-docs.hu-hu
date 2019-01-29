---
title: 'Azure AD Connect: A telepítés típusának kiválasztása |} A Microsoft Docs'
description: Ez a témakör végigvezeti az Azure AD Connect használata a telepítés típusának kiválasztása
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: d04cf1665d32590620c9b968e3344513db2b5f7b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152119"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Használja az Azure AD Connect telepítési típusának kiválasztása
Az Azure AD Connect két telepítési típussal rendelkezik az új telepítéshez: Az expressz és testre szabható. Ez a témakör segít eldönteni, melyik lehetőség a telepítés során használandó.

## <a name="express"></a>Express
Express a leggyakoribb megoldás, és minden új telepítések körülbelül 90 %-át használják. Adja meg a megfelelő konfigurációt a leggyakrabban használt ügyfél-forgatókönyvekhez készült.

Feltételezi:

- Rendelkezik egy egyetlen Active Directory a helyszíni erdő.
- Van egy vállalati rendszergazdai fiók is használhatja a telepítéshez.
- Kevesebb mint 100 000 objektummal rendelkezik a helyszíni Active Directoryban.

A következőket kínálja:

- [A Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md) a helyszínről az Azure AD egyszeri bejelentkezés.
- A konfiguráció, amely szinkronizálja [felhasználók, csoportok, partnerek és a Windows 10-számítógépeket](concept-azure-ad-connect-sync-default-configuration.md).
- Minden tartomány és az összes szervezeti egység összes jogosult objektumok szinkronizálásának.
- [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md) engedélyezve van, győződjön meg arról, hogy mindig használja az elérhető legújabb verzióra.

Ha továbbra is használhatja az Express beállításai:

- Ha nem szeretné szinkronizálni az összes szervezeti, továbbra is használhatja az Express és az utolsó oldalon törölje **... a szinkronizálási folyamat indítása x. Ezután futtassa újra a telepítővarázslót, és módosítsa a szervezeti [konfigurációs beállítások](how-to-connect-installation-wizard.md#customize-synchronization-options) és ütemezett szinkronizálás engedélyezése.
- Szeretné engedélyezni, például a jelszóvisszaírást az Azure AD Premium szolgáltatásainak egyike. Először próbálja ki expressz beolvasni a kezdeti telepítés befejeződött. Ezután futtassa újra a telepítővarázslót, és módosítsa a [konfigurációs lehetőségek](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Egyéni
Az egyéni elérési út lehetővé teszi, hogy számos további lehetőség, mint az express. Minden olyan esetben, ha expressz előző szakaszban leírt konfiguráció nem a szervezet reprezentatív kell használni.

Használat:

- Nem rendelkezik hozzáféréssel egy vállalati rendszergazdai fiók az Active Directoryban.
- Egynél több erdő, vagy egynél több erdő szinkronizálni a jövőben tervez.
- Tartományok van az erdő nem érhető el a Connect-kiszolgálóról.
- A felhasználói bejelentkezés összevonási vagy átmenő hitelesítés használatát tervezi.
- Több mint 100 000 objektummal rendelkezik, és a teljes SQL Server szükséges.
- Csoportalapú szűrés és nem csupán tartomány vagy szervezeti egység szerinti szűrés használatát tervezi.

## <a name="upgrade-from-dirsync"></a>Frissítés a DirSync szolgáltatásról
Ha a DirSync jelenleg használ, majd kövesse a [frissítésre a dirsyncről](how-to-dirsync-upgrade-get-started.md) a meglévő konfiguráció frissítése. Két különböző frissítési lehetőség van:

- Helyszíni frissítés a Connect telepítése ugyanarra a kiszolgálóra.
- Párhuzamos üzembe helyezés Connect telepítése egy új kiszolgálóra, míg a meglévő DirSync-kiszolgáló még működőképes.

## <a name="upgrade-from-azure-ad-sync"></a>Frissítés az Azure AD-Szinkronizálóról
Ha Azure AD Sync jelenleg használ, akkor kövesse a [ugyanazokat a lépéseket](how-to-upgrade-previous-version.md) , amikor frissít egy Connect-verzióról egy újabb. Két különböző frissítési lehetőség van:

- Helyszíni frissítés a Connect telepítése ugyanarra a kiszolgálóra.
- Párhuzamos-migrálás, egy új kiszolgálón Connect telepítése során a meglévő Azure AD Sync-kiszolgáló még működőképes.

## <a name="migrate-from-fim2010-or-mim2016"></a>FIM2010 vagy MIM2016 a migrálása
Ha a Forefront Identity Manager 2010 vagy a Microsoft Identity Manager 2016 az Azure AD-összekötő jelenleg használt, egyetlen lehetősége egy áttelepítés. Az ismertetett lépéseket követve [párhuzamos-migrálás](how-to-upgrade-previous-version.md#swing-migration). A lépések FIM2010/MIM2016 bármely Dicséretben részesítettünk Azure AD Sync cserélje.

## <a name="next-steps"></a>További lépések
Attól függően, a beállítást választja, használja a bal oldali tartalomjegyzék segítségével keresse meg a cikket a részletes lépésekről.
