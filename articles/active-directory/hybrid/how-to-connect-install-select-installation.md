---
title: 'Azure AD Connect: Válassza ki a telepítés típusát | Microsoft dokumentumok'
description: Ez a témakör bemutatja, hogyan választhatja ki az Azure AD Connect hez használandó telepítési típust
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
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a624a6b3b4696899af0d8606f653df260cc201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348280"
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Az Azure AD Connect telepítési típusának kiválasztása
Az Azure AD Connect két telepítési típussal rendelkezik az új telepítéshez: expressz és testreszabott. Ez a témakör segít eldönteni, hogy melyik lehetőséget használja a telepítés során.

## <a name="express"></a>Express
Az expressz a leggyakoribb lehetőség, és az összes új telepítés mintegy 90%-a használja. Úgy tervezték, hogy a konfiguráció, amely működik a leggyakoribb ügyfél forgatókönyvek.

Azt feltételezi:

- Egyetlen Active Directory erdővel rendelkezik a helyszínen.
- Rendelkezik egy vállalati rendszergazdai fiókkal, amelyet a telepítéshez használhat.
- A helyszíni Active Directoryban kevesebb mint 100 000 objektum található.

Kapsz:

- [Jelszó-kivonat szinkronizálás](how-to-connect-password-hash-synchronization.md) a helyszíni Azure AD egyszeri bejelentkezéshez.
- [Felhasználók, csoportok, névjegyek és Windows 10-es számítógépek](concept-azure-ad-connect-sync-default-configuration.md)szinkronizálására használható konfiguráció.
- Az összes jogosult objektum szinkronizálása az összes tartományban és az összes ou-ban.
- [Az automatikus frissítés](how-to-connect-install-automatic-upgrade.md) engedélyezve van, hogy mindig a legújabb elérhető verziót használja.

Az Expressz használatára vonatkozó beállítások:

- Ha nem szeretné szinkronizálni az összes ou-t, továbbra is használhatja az Expressz et, és az utolsó oldalon törölje a jelet a **A szinkronizálási folyamat indítása...***. Ezután futtassa újra a telepítővarázslót, és módosítsa a felhasználói példányok [konfigurációs beállításaiban,](how-to-connect-installation-wizard.md#customize-synchronization-options) és engedélyezze az ütemezett szinkronizálást.
- Engedélyezni szeretné az Azure AD Premium egyik szolgáltatását, például a jelszó-visszaírást. Először menjen át expressz, hogy a kezdeti telepítés befejeződött. Ezután futtassa újra a telepítővarázslót, és módosítsa a [konfigurációs beállításokat](how-to-connect-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Egyéni
A testreszabott elérési út sokkal több lehetőséget tesz lehetővé, mint az expressz. Minden olyan esetben használni kell, amikor az expressz előző szakaszában ismertetett konfiguráció nem reprezentatív a szervezet számára.

A következő esetekben használja:

- Nincs hozzáférése vállalati rendszergazdai fiókhoz az Active Directoryban.
- Több erdővel is rendelkezik, vagy a jövőben egynél több erdőszinkronizálását tervezi.
- Az erdőben olyan tartományok vannak, amelyek nem érhetőek el a Connect kiszolgálóról.
- Azt tervezi, hogy a felhasználói bejelentkezéshez összevonási vagy átmenő hitelesítést használ.
- Több mint 100 000 objektummal rendelkezik, és teljes SQL Servert kell használnia.
- Azt tervezi, hogy csoportalapú szűrést használ, és nem csak tartomány- vagy szervezetiegység-alapú szűrést.

## <a name="upgrade-from-dirsync"></a>Frissítés a DirSync szolgáltatásról
Ha jelenleg dirsync- et használ, a meglévő konfiguráció frissítéséhez kövesse a [Frissítés a DirSync szolgáltatásból](how-to-dirsync-upgrade-get-started.md) című részben leírt lépéseket. Két különböző frissítési lehetőség van:

- Helybeni frissítés a Connect ugyanazon a kiszolgálón történő telepítéséhez.
- Párhuzamos telepítéssel a Connect új kiszolgálóra történő telepítéséhez, miközben a meglévő DirSync-kiszolgáló még működik.

## <a name="upgrade-from-azure-ad-sync"></a>Frissítés az Azure AD Sync-ről
Ha jelenleg az Azure AD Sync, majd [kövesse ugyanazokat](how-to-upgrade-previous-version.md) a lépéseket, mint amikor egy Connect verzióról egy újabb frissítésre. Két különböző frissítési lehetőség van:

- Helybeni frissítés a Connect ugyanazon a kiszolgálón történő telepítéséhez.
- Swing-áttelepítés telepíteni Connect egy új kiszolgálón, miközben a meglévő Azure AD Sync-kiszolgáló még mindig működik.

## <a name="migrate-from-fim2010-or-mim2016"></a>Áttelepítés FIM2010 vagy MIM2016
Ha jelenleg a Forefront Identity Manager 2010 vagy a Microsoft Identity Manager 2016 az Azure AD-összekötő, majd az egyetlen lehetőség az áttelepítés. Kövesse a [swing-migration](how-to-upgrade-previous-version.md#swing-migration)című részben leírt lépéseket. A lépésekben cserélje le az Azure AD Sync fim2010/MIM2016-mal való említését.

## <a name="next-steps"></a>További lépések
Attól függően, hogy a kiválasztott beállítást használni, használja a táblázatot a tartalom balra, hogy megtalálja a cikket a részletes lépéseket.
