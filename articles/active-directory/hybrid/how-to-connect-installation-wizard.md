---
title: Az Azure AD Connect ismételt futtatásával a telepítővarázsló |} A Microsoft Docs
description: Ismerteti, hogyan a telepítési varázsló második futtatásakor.
keywords: Az Azure AD Connect telepítővarázsló lehetővé teszi, hogy konfigurálja a karbantartási beállításait, a második futtatásakor
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e07fc6c4ce7477cd6f1c64d5de1574ae7c4cecff
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168952"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Az Azure AD Connect szinkronizálása: A telepítési varázsló másodszori futtatása
Az Azure AD Connect telepítővarázsló első futtatásakor, bemutatja, hogyan konfigurálhatja a telepítést. Ha újra futtatja a telepítési varázsló, azt a karbantartási lehetőséget kínál.

A telepítővarázsló találhatja meg a start menüben nevű **az Azure AD Connect**.

![Start menü](./media/how-to-connect-installation-wizard/startmenu.png)

A telepítővarázsló indításakor megjelenik egy lap ezekkel a beállításokkal:

![További feladatok listája lap](./media/how-to-connect-installation-wizard/additionaltasks.png)

Ha az Azure AD Connecttel telepített AD FS, még több lehetősége van. A további lehetőségeket az AD FS vannak dokumentálva [AD FS felügyeleti](how-to-connect-fed-management.md#manage-ad-fs).

Válassza ki az egyik feladat, és kattintson a **tovább** folytatásához.

> [!IMPORTANT]
> Miközben a telepítési varázsló megnyitása, a szinkronizálási motor az összes művelet fel vannak függesztve. Ellenőrizze, hogy bezárja a telepítési varázslót, amint befejezte a konfigurációs módosításokat.
>
>

## <a name="view-current-configuration"></a>Aktuális konfiguráció megtekintése
Ez a beállítás hozzáférést biztosít a jelenleg konfigurált beállítások gyors áttekintést.

![Az összes beállítás és az állapotuk listáját lap](./media/how-to-connect-installation-wizard/viewconfig.png)

Kattintson a **előző** térhet vissza. Ha **kilépési**, a telepítővarázsló bezárása.

## <a name="customize-synchronization-options"></a>Szinkronizálási beállítások testreszabása
Ezzel a beállítással a szinkronizálási konfiguráció módosításához. Egyéni konfigurációs telepítési útvonaláról lehetőségek egy részét látja. Ez a beállítás akkor is, ha először használja az Expressz telepítés láthatja.

* [Adjon hozzá további címtárak](how-to-connect-install-custom.md#connect-your-directories). A könyvtár eltávolítása, lásd: [egy összekötő törlése](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Módosítsa a tartomány és szervezeti egységek szűrése](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Csoport eltávolítása a szűrés.
* [Módosítsa a választható funkciók](how-to-connect-install-custom.md#optional-features).

A többi beállítást a kezdeti telepítés nem módosítható és nem érhetők el. Ezek a lehetőségek a következők:

* Módosítsa a sourceAnchor és a userPrincipalName tulajdonsághoz használt attribútumot.
* Objektumok csatlakozó módjának módosítása a másik erdőben találhatók.
* Engedélyezze a Csoportalapú szűrés.

## <a name="refresh-directory-schema"></a>Címtárséma frissítése
Ez a beállítás használható, ha módosította a séma egy a helyszíni AD DS-erdőt. Például, előfordulhat, hogy telepítette az Exchange vagy egy Windows Server 2012 sémára eszköz objektumok frissítése. Ebben az esetben kell újra olvassa el a séma AD DS-ből, és frissíti a gyorsítótárat az Azure AD Connect utasíthatja. Ezzel a művelettel újragenerálja a szinkronizálási szabályok is. Ha például az Exchange-séma ad hozzá, az Exchange-hez a szinkronizálási szabályok vannak része a konfigurációnak.

Ha ezt a lehetőséget választja, a konfigurációban minden címtár jelennek meg. Megtartani az alapértelmezett beállítást, és frissítse minden olyan erdőben, vagy néhányat, törölje.

![A környezet összes könyvtárak listája lap](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Átmeneti környezetű üzemmód konfigurálása
Ezzel a beállítással engedélyezheti vagy letilthatja az átmeneti környezetű üzemmód a kiszolgálón. Átmeneti mód és azok felhasználási módjáról további információ található [műveletek](how-to-connect-sync-operations.md#staging-mode).

A beállítás jeleníti meg, ha átmeneti jelenleg engedélyezve van vagy le van tiltva:  
![A beállítás, amely az átmeneti környezetű üzemmód aktuális állapota is látható](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Ha módosítani szeretné az állapot, ezt a beállítást, és válassza ki, vagy törölje a jelölőnégyzet jelölését.  
![A beállítás, amely az átmeneti környezetű üzemmód aktuális állapota is látható](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Felhasználói bejelentkezés módosítása
Ez a beállítás lehetővé teszi, hogy a felhasználói bejelentkezési módszer módosítását, és a jelszókivonatok szinkronizálása, átmenő hitelesítés vagy összevonás. Nem lehet **ne konfiguráljon**.

Ez a beállítás további információkért lásd: [felhasználói bejelentkezés](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>További lépések
* További információ az Azure AD Connect szinkronizálása által használt konfigurációs modell [ismertetése deklaratív kiépítés](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
