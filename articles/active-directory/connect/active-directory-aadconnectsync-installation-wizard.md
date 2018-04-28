---
title: Az Azure AD Connect ismételt futtatásával telepítse a varázsló |} Microsoft Docs
description: Ismerteti a telepítési varázsló működése a második futtatásakor.
keywords: Az Azure AD Connect telepítővarázsló lehetővé teszi, hogy konfigurálja a karbantartási beállításait, a második futtatásakor
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 56cc38275a23eb4529558b876db619768a885a25
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect szinkronizálása: még egyszer a telepítővarázsló futtatása
Az Azure AD Connect telepítővarázsló első futtatásakor az végigvezeti a telepítés konfigurálása. Ha újra futtatni a telepítési varázsló, karbantartási lehetőségeinek kínál.

A start menü nevű megtalálja a telepítési varázsló **az Azure AD Connect**.

![Start menü](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

A telepítési varázsló elindításakor, beállítások lap jelenik meg:

![További feladatok listáját tartalmazó lap](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Ha már telepítette az AD FS az Azure AD Connect, még akkor is több lehetőség van. A további beállításait az AD FS ismertetett [az AD FS felügyeleti](active-directory-aadconnect-federation-management.md#manage-ad-fs).

Válassza ki a feladatok közül, és kattintson a **tovább** a folytatáshoz.

> [!IMPORTANT]
> Miközben a telepítési varázsló megnyitása, a szinkronizálási motor az összes művelet fel vannak függesztve. Győződjön meg arról, amint a konfigurációs módosítások végrehajtása zárja be a telepítővarázslót.
>
>

## <a name="view-current-configuration"></a>Aktuális konfiguráció megtekintése
Ez a beállítás lehetővé teszi a jelenleg konfigurált beállítások gyors áttekintést.

![Az összes beállítás és állapotukra listáját lap](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Kattintson a **előző** térhet vissza. Ha **kilépési**, akkor zárja be a telepítővarázslót.

## <a name="customize-synchronization-options"></a>Szinkronizálási beállítások testreszabása
Ezzel a beállítással a szinkronizálási konfigurációs módosításokat. Egyéni konfiguráció telepítési elérési úton található beállítások egy részét látja. Látja ezt a beállítást, akkor is, ha az Expressz telepítés eredetileg használt.

* [Adja hozzá a további címtárakat](active-directory-aadconnect-get-started-custom.md#connect-your-directories). A könyvtár törlése, lásd: [összekötő törlése](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
* [Módosítsa a tartomány és szervezeti egységek szűrése](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
* Szűrés csoport eltávolítása.
* [Választható szolgáltatások módosítása](active-directory-aadconnect-get-started-custom.md#optional-features).

A többi beállítás a kezdeti telepítés nem módosítható, és nem érhetők el. Ezek a beállítások a következők:

* Módosítsa a userPrincipalName és a sourceAnchor attribútum.
* Másik erdőből származó objektumok csatlakozó módjának módosítása.
* Csoport-alapú szűrés engedélyezése.

## <a name="refresh-directory-schema"></a>Címtárséma frissítése
Ez a beállítás használható, ha a séma módosította az egyik a helyszíni AD DS-erdőkből. Például előfordulhat, hogy telepítette az Exchange vagy a Windows Server 2012 sémára eszköz objektumok frissítése. Ebben az esetben szüksége arra utasítani az Azure AD Connect újból beolvasni a sémát az AD DS-ből, és frissíti a gyorsítótárat. Ezzel a művelettel újragenerálja a szinkronizálási szabályok is. Ha például az Exchange-séma ad hozzá, a konfigurációs kerülnek Exchange szinkronizálási szabályait.

Ha ezt a lehetőséget választja, a konfigurációt a könyvtárak jelennek meg. Megtartani az alapértelmezett beállítást, és frissítse az összes erdőben, és némelyikük törölje.

![A környezet összes könyvtárainak listáját tartalmazó lap](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Átmeneti környezetű üzemmód konfigurálása
Ezzel a beállítással engedélyezheti vagy letilthatja az átmeneti környezetű üzemmód a kiszolgálón. További információt az átmeneti módot, és hogyan használja fel azokat a található [műveletek](active-directory-aadconnectsync-operations.md#staging-mode).

A beállítás jeleníti meg, ha átmeneti jelenleg engedélyezve vagy letiltva:  
![Lehetőség, amely a rendszer azt is jelzi az átmeneti környezetű üzemmód jelenlegi állapotában](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Az állapot módosítására irányuló ezt a beállítást, és válassza ki, vagy törölje a jelölőnégyzet jelölését.  
![Lehetőség, amely a rendszer azt is jelzi az átmeneti környezetű üzemmód jelenlegi állapotában](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Felhasználói bejelentkezés módosítása
Ez a beállítás lehetővé teszi, hogy módosíthatja a felhasználói bejelentkezési módszer, és onnan Jelszókivonat-szinkronizálás, átmenő hitelesítést vagy összevonási. Nem lehet átváltani **ne konfiguráljon**.

Ez a beállítás további információkért lásd: [felhasználói bejelentkezés](active-directory-aadconnect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>További lépések
* További tudnivalók az Azure AD Connect szinkronizálási által alkalmazott konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
