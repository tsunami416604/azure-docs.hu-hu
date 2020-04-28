---
title: 'Azure AD Connect szinkronizálás: véletlen törlések megakadályozása | Microsoft Docs'
description: Ez a témakör ismerteti a véletlen törlések (a véletlen törlések megakadályozása) funkció használatát a Azure AD Connectban.
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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "71827131"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Az Azure AD Connect szinkronizálása: véletlen törlések megakadályozása
Ez a témakör ismerteti a véletlen törlések (a véletlen törlések megakadályozása) funkció használatát a Azure AD Connectban.

Az Azure AD Connect telepítése során a rendszer alapértelmezés szerint engedélyezi a véletlen törlések megakadályozására szolgáló beállítást, és konfigurálja, hogy ne engedjen több mint 500 törlést tartalmazó exportálásokat. A funkció célja, hogy meggátolja a konfiguráció és a helyszíni címtár olyan véletlen módosításait, amelyek nagy számú felhasználót és egyéb objektumot érintenek.

## <a name="what-is-prevent-accidental-deletes"></a>A véletlen törlések megakadályozása
Gyakori helyzetek, amikor megjelenik számos törlés:

* A [szűrés](how-to-connect-sync-configure-filtering.md) azon módosításai, amelyekben egy teljes [szervezeti egység](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) vagy [tartomány](how-to-connect-sync-configure-filtering.md#domain-based-filtering) sincs kiválasztva.
* Egy adott szervezeti egységben lévő minden objektum törlése.
* Egy szervezeti egység átnevezése, amelynek következtében a rendszer a benne lévő összes objektumot hatókörön kívülinek tekinti a szinkronizálás vonatkozásában.

A 500-objektumok alapértelmezett értéke a PowerShell használatával `Enable-ADSyncExportDeletionThreshold`módosítható, amely a Azure Active Directory Connect-vel telepített ad-szinkronizáló modul részét képezi. Ezt az értéket úgy kell konfigurálni, hogy az illeszkedjen a szervezet méretéhez. Mivel a szinkronizálási ütemező 30 percenként fut, az érték a 30 percen belül látható törlések száma.

Ha túl sok törlés lett kiválasztva az Azure AD-ba, akkor az Exportálás leáll, és a következőhöz hasonló e-mailt kap:

![E-mailek véletlen törlésének megakadályozása](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Hello (technikai kapcsolattartó). Ekkor a identitásszinkronizálás szolgáltatás azt észlelte, hogy a törlések száma túllépte a (szervezet neve) beállított törlési küszöbértékét. A rendszer a törléshez összesen (szám) objektumot küld el ebben az identitásszinkronizálás futtatásban. Ez megfelelt vagy túllépte a beállított törlési küszöbértéket (szám). A folytatáshoz meg kell erősítenie, hogy ezeket a törléseket fel kell dolgozni. Az e-mail-üzenetben felsorolt hibával kapcsolatos további információkért tekintse meg a véletlen törlés megakadályozása című témakört.*
>
> 

Az állapotot `stopped-deletion-threshold-exceeded` az exportálási profil **synchronization Service Manager** felhasználói felületén is megtekintheti.
![A véletlen törlés megakadályozása Service Manager felhasználói felület szinkronizálása](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Ha ez nem várt, akkor vizsgálja meg, és végezze el a megfelelő műveleteket. Ha szeretné megtekinteni, hogy mely objektumokat szeretné törölni, tegye a következőket:

1. Indítsa el a **szinkronizálási szolgáltatást** a Start menüből.
2. Válassza az **Összekötők**lehetőséget.
3. Válassza ki a **Azure Active Directory**típusú összekötőt.
4. A jobb oldali **műveletek** területen válassza a **Keresés összekötő terület**lehetőséget.
5. A **hatókör**alatt lévő előugró ablakban válassza a **leválasztva** lehetőséget, és válasszon egy korábbi időpontot. Kattintson a **Keresés** gombra. Ezen a lapon látható a törölni kívánt összes objektum áttekintése. Az egyes elemekre kattintva további információkat érhet el az objektumról. Az **oszlop beállítása** lehetőségre kattintva további attribútumokat is hozzáadhat a rácshoz.

![Keresési összekötő területe](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Ha nem biztos benne, hogy minden törlésre szükség van, és szeretne lejárni egy biztonságosabb útvonalon. A PowerShell-parancsmaggal: `Enable-ADSyncExportDeletionThreshold` új küszöbértéket állíthat be a küszöbérték letiltása helyett, ami lehetővé teheti a nemkívánatos törlést. 

## <a name="if-all-deletes-are-desired"></a>Ha minden törlés szükséges
Ha az összes törlés szükséges, tegye a következőket:

1. A jelenlegi törlési küszöbérték lekéréséhez futtassa a PowerShell `Get-ADSyncExportDeletionThreshold`-parancsmagot. Adja meg az Azure AD globális rendszergazdai fiókját és jelszavát. Az alapértelmezett érték: 500.
2. Ha átmenetileg le szeretné tiltani ezt a védelmet, és engedélyezi a törlést, futtassa a `Disable-ADSyncExportDeletionThreshold`következő PowerShell-parancsmagot:. Adja meg az Azure AD globális rendszergazdai fiókját és jelszavát.
   ![Hitelesítő adatok](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Ha a Azure Active Directory-összekötő továbbra is ki van választva, válassza ki **a műveletet,** és válassza az **Exportálás**lehetőséget.
4. A védelem újbóli engedélyezéséhez futtassa a következő PowerShell-parancsmagot: `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`. Cserélje le a 500 értéket az aktuális törlési küszöb beolvasásakor észlelt értékre. Adja meg az Azure AD globális rendszergazdai fiókját és jelszavát.

## <a name="next-steps"></a>További lépések
**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
