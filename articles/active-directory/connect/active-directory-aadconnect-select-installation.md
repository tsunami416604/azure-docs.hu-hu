---
title: 'Az Azure AD Connect: A telepítés típusának kiválasztása |} Microsoft Docs'
description: Ez a témakör végigvezeti az Azure AD Connect használatával a telepítés típusának kiválasztása
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 76f1ce12ab149f57ec6e995d132de83105c5e0ca
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Jelölje ki a használatára az Azure AD Connect telepítési típusát
Az Azure AD Connect két tartozik telepítése új telepítés: Express és testre szabható. A témakörben leírtak segítségével eldöntheti, hogy a telepítés során használandó módszer.

## <a name="express"></a>Express
Express a leggyakrabban használt beállítás, és minden új telepítések körülbelül 90 %-át használja. Adja meg a konfigurációt a leggyakoribb forgatókönyvet készült.

Ez azt feltételezi, hogy:

- A helyi erdő egy egyetlen Active Directory rendelkezik.
- A telepítéshez használható vállalati rendszergazdai fiókkal rendelkezik.
- A helyszíni Active Directoryban kisebb, mint 100 000 objektummal rendelkezik.

Elérhetővé:

- [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-hash-synchronization.md) az egyszeri bejelentkezés az Azure AD helyszíni.
- A konfiguráció, amely szinkronizálja [felhasználók, csoportok, névjegyek és Windows 10-es számítógépeken](active-directory-aadconnectsync-understanding-default-configuration.md).
- Az összes tartomány és az összes szervezeti egység összes jogosult objektumok szinkronizálás.
- [Automatikus frissítés](active-directory-aadconnect-feature-automatic-upgrade.md) engedélyezve van-e győződjön meg arról, hogy mindig használja az elérhető legújabb verzióra.

Ha továbbra is használhatja az expressz beállításokat:

- Ha nem szeretné szinkronizálni az összes szervezeti egységek, is Express használja, és az utolsó oldalon törölje ** indítsa el a szinkronizálási folyamat... x. Ezután futtassa újból a telepítési varázsló, és módosítsa a szervezeti egységek az [konfigurációs beállítások](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options) és ütemezett szinkronizálás engedélyezése.
- Például a jelszóvisszaírást az Azure AD Premium szolgáltatásainak egyike a engedélyezni szeretné. Először halad át express lekérni a kezdeti telepítése befejeződött. Ezután futtassa újból a telepítési varázsló, és módosítsa a [konfigurációs beállítások](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Egyéni
Az egyéni elérési út lehetővé teszi, hogy számos további lehetőség express-nál. Minden olyan esetben, ha expressz az előző szakaszban leírt konfiguráció nincs a szervezet reprezentatív használandó.

A következő esetekben használja:

- Az Active Directory nincs egy vállalati rendszergazdai fiók eléréséhez.
- Több erdővel rendelkezik, vagy egynél több erdő a jövőben szinkronizálni szeretne.
- Az erdő a Connect-kiszolgáló nem elérhető tartományok vannak.
- A felhasználói bejelentkezés összevonási vagy átmenő hitelesítés használatát tervezi.
- Több mint 100 000 objektummal rendelkezik, és szeretné használni, a teljes SQL-kiszolgálóról.
- Biztonságicsoport-alapú szűrés és nem csak tartomány vagy szervezeti egység alapú szűrés használatát tervezi.

## <a name="upgrade-from-dirsync"></a>Frissítés a DirSync szolgáltatásról
Ha a DirSync jelenleg használ, majd kövesse a [frissítésre a Dirsyncről](active-directory-aadconnect-dirsync-upgrade-get-started.md) frissítésére a meglévő konfigurációt. Két különböző frissítési lehetőségek közül:

- Frissítés helyben Connect telepíthető ugyanarra a kiszolgálóra.
- Párhuzamos üzembe helyezés a Connect telepítése egy új kiszolgálóra, míg a DirSync meglévő kiszolgáló továbbra is működik.

## <a name="upgrade-from-azure-ad-sync"></a>Frissítés Azure AD-szinkronizálóról
Ha a jelenleg használt Azure AD Sync, majd kövesse a [ugyanazokat a lépéseket](active-directory-aadconnect-upgrade-previous-version.md) , amikor egy csatlakozás verziója rendszerről frissít egy újabb. Két különböző frissítési lehetőségek közül:

- Frissítés helyben Connect telepíthető ugyanarra a kiszolgálóra.
- Mozgó-áttelepítési Connect új kiszolgáló telepítése során a meglévő Azure AD Sync-kiszolgáló egy továbbra is működik.

## <a name="migrate-from-fim2010-or-mim2016"></a>FIM2010 vagy MIM2016 áttelepítése
Ha a jelenleg használt Forefront Identity Manager 2010 vagy a Microsoft Identity Manager 2016 az Azure AD-összekötő használatával, majd egyetlen választása marad: áttelepítés. Az ismertetett lépéseket követve [mozgó-áttelepítési](active-directory-aadconnect-upgrade-previous-version.md#swing-migration). A lépéseket cserélje le az Azure AD Sync bármely hashtagként FIM2010/MIM2016.

## <a name="next-steps"></a>További lépések
Attól függően, hogy a kijelölt használandó beállítás a tartalom a bal oldali tábla használatával a részletes lépéseket a cikk megtalálását.
