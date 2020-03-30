---
title: 'Azure AD Connect szinkronizálása: Véletlen törlés megakadályozása | Microsoft dokumentumok'
description: Ez a témakör ismerteti a véletlen törlések megelőzése (véletlen törlések megelőzése) funkció az Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f3109b4c87e25444629ca25411894eab8a9d56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71827131"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Az Azure AD Connect szinkronizálása: véletlen törlések megakadályozása
Ez a témakör ismerteti a véletlen törlések megelőzése (véletlen törlések megelőzése) funkció az Azure AD Connect.

Az Azure AD Connect telepítése során a rendszer alapértelmezés szerint engedélyezi a véletlen törlések megakadályozására szolgáló beállítást, és konfigurálja, hogy ne engedjen több mint 500 törlést tartalmazó exportálásokat. A funkció célja, hogy meggátolja a konfiguráció és a helyszíni címtár olyan véletlen módosításait, amelyek nagy számú felhasználót és egyéb objektumot érintenek.

## <a name="what-is-prevent-accidental-deletes"></a>Mi az, hogy megakadályozzák a véletlen törlések
A gyakori esetek, amikor sok törlést lát, a következők:

* Szűrés [módosítása,](how-to-connect-sync-configure-filtering.md) ha egy teljes [szervezeti egység](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) vagy [tartomány](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nincs kijelölve.
* Egy adott szervezeti egységben lévő minden objektum törlése.
* Egy szervezeti egység átnevezése, amelynek következtében a rendszer a benne lévő összes objektumot hatókörön kívülinek tekinti a szinkronizálás vonatkozásában.

Az 500 objektum alapértelmezett értéke módosítható a `Enable-ADSyncExportDeletionThreshold`PowerShell használatával, amely az Azure Active Directory Connect szolgáltatással telepített AD Sync modul része. Ezt az értéket úgy kell konfigurálnia, hogy az megfeleljen a szervezet méretének. Mivel a szinkronizálási ütemező 30 percenként fut, az érték a 30 percen belül látható törlések száma.

Ha túl sok az Azure AD-be exportálható törlés, akkor az exportálás leáll, és egy ilyen e-mailt kap:

![Véletlen üzenet törlésének megakadályozása](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hello (technikai kapcsolat). Az identitás-szinkronizálási szolgáltatás (időpont) során észlelte, hogy a törlések száma meghaladta a (szervezet neve) konfigurált törlési küszöbértékét. Ebben az identitásszinkronizálási futtatásban összesen (szám) objektum ot küldött törlésre. Ez elérte vagy meghaladta a (szám) objektumok (szám) konfigurált törlési küszöbértékét. Meg kell erősítenie, hogy ezeket a törléseket fel kell dolgozni, mielőtt folytatnánk. Az e-mail üzenetben felsorolt hibával kapcsolatos további információkért tekintse meg a véletlen törlések megelőzését.*
>
> 

Az állapot `stopped-deletion-threshold-exceeded` akkor is látható, ha az Exportálási profil **Szinkronizálási szolgáltatáskezelő** felhasználói felületén keres.
![Véletlen törlése a Szinkronizáláskezelő felhasználói felületének törléséhez](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Ha ez váratlan volt, vizsgálja meg, és tegye meg a korrekciós intézkedéseket. A törölni készülendő objektumok megtekintéséhez tegye a következőket:

1. Indítsa el **a szinkronizálási szolgáltatást** a Start menüből.
2. Nyissa meg a **Csatlakozókat**.
3. Válassza ki az Azure Active Directory típusú **összekötőt.**
4. A jobb oldali **Műveletek csoportban** válassza a **Keresésösszekötő térben**lehetőséget.
5. A **Hatókör**ablakban válassza a **Leválasztva since lehetőséget,** és válasszon egy időpontot a múltban. Kattintson a **Keresés** gombra. Ez a lap a törölni készülő összes objektum nézetét tartalmazza. Az egyes elemekre kattintva további információkat kaphat az objektumról. **Az Oszlopbeállítás gombra** kattintva további jellemzőket is hozzáadhat a rácsban láthatóvá.

![Összekötő tér keresése](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Ha nem biztos benne, hogy minden törlésre szükség van, és biztonságosabb útvonalat szeretne leválasztani. A PowerShell-parancsmag használatával: `Enable-ADSyncExportDeletionThreshold` új küszöbértéket állíthat be a küszöbérték letiltása helyett, amely nem kívánt törlést tehet lehetővé. 

## <a name="if-all-deletes-are-desired"></a>Ha az összes törlést szeretné késni
Ha az összes törlés reked, akkor tegye a következőket:

1. Az aktuális törlési küszöbérték beolvasásához futtassa `Get-ADSyncExportDeletionThreshold`a PowerShell-parancsmamot. Adjon meg egy Azure AD globális rendszergazdai fiókot és jelszót. Az alapértelmezett érték: 500.
2. Ha ideiglenesen le szeretné tiltani ezt a védelmet, és lehetővé szeretné tenni, hogy a törlések átmenjenek, futtassa a PowerShell-parancsmabát: `Disable-ADSyncExportDeletionThreshold`. Adjon meg egy Azure AD globális rendszergazdai fiókot és jelszót.
   ![Hitelesítő adatok](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Ha az Azure Active Directory-összekötő továbbra is ki van jelölve, válassza a **Futtatás** műveletet, és válassza az **Exportálás**lehetőséget.
4. A védelem újbóli engedélyezéséhez futtassa a `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`PowerShell-parancsmabot: . Cserélje le az 500-at az aktuális törlési küszöbérték beolvasásakor észlelt értékre. Adjon meg egy Azure AD globális rendszergazdai fiókot és jelszót.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
