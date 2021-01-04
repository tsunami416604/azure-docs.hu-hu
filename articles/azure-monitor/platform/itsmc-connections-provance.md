---
title: A IT-szolgáltatásmenedzsmenti csatoló összekapcsolásával
description: Ez a cikk azt ismerteti, hogyan lehet megfigyelni a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) Azure Monitor a ITSM-munkaelemek központilag figyelésére és felügyeletére.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 19d2ef1d4d711738d60938aac502dfd490e021a1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729677"
---
# <a name="connect-provance-with-it-service-management-connector"></a>A IT-szolgáltatásmenedzsmenti csatoló összekapcsolásával

Ez a cikk azt ismerteti, hogyan konfigurálható a kapcsolat a következővel: a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) és a Log Analytics a munkaelemek központilag kezelhetők.

> [!NOTE]
> Javasoljuk, hogy a Cherwell és a felkínált ügyfeleinknek a [webhook műveleteit](./action-groups.md#webhook) a Cherwell és az Előa végpontot használják egy másik megoldásként az integrációhoz.

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztatható a ITSMC az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](./itsmc-definition.md#add-it-service-management-connector).
- Az Azure AD-ben regisztrálni kell az alkalmazást, és az ügyfél-azonosítót is elérhetővé kell tenni. Részletes információk: [az Active Directory-hitelesítés konfigurálása](../../app-service/configure-authentication-provider-aad.md).

- Felhasználói szerepkör: rendszergazda.

## <a name="connection-procedure"></a>Csatlakoztatási eljárás

A következő eljárással hozhat létre egy elővance-kapcsolatokat:

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2. A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok** elemre.
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás** gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

> [!NOTE]
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Adja meg a ITSMC-vel összekapcsoláshoz használni kívánt elővance-példány nevét.  Ezt a nevet később is használhatja, ha munkaelemeket konfigurál ebben a ITSM/részletes log Analytics nézetben. |
| **Partner típusa**   | Válassza az **elővance** elemet. |
| **Felhasználónév**   | Írja be azt a felhasználónevet, amely csatlakozhat a ITSMC.    |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** A rendszer csak a hitelesítési jogkivonatok létrehozásához használja a felhasználónevet és a jelszót, és a ITSMC szolgáltatásban bárhol nem tárolja őket.|
| **Kiszolgáló URL-címe**   | Írja be a ITSMC-hez csatlakozni kívánt Előa-példány URL-címét. |
| **Ügyfél-azonosító**   | Adja meg az ügyfél-azonosítót a-kiszolgáló hitelesítéséhez, amelyet a elővizsgálati példányban hozott létre.  További információ az ügyfél-AZONOSÍTÓról: [Active Directory-hitelesítés konfigurálása](../../app-service/configure-authentication-provider-aad.md). |
| **Adatszinkronizálási hatókör**   | Válassza ki a ITSMC-on keresztül szinkronizálni kívánt munkaelemeket az Azure Log Analyticshoz.  Ezeket a munkaelemeket a rendszer a log analyticsbe importálja.   **Beállítások:**   Incidensek, módosítási kérelmek.|
| **Adatszinkronizálás** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC létrehozza az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva.|

![Képernyőfelvétel a kapcsolat neve és a partner típusa listáról.](media/itsmc-connections/itsm-connections-provance-latest.png)

**Sikeres csatlakozás és szinkronizálás**:

- A rendszer az Azure **log Analyticsba** importálja a jelen Előa-példány kiválasztott munkaelemeit. A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Az incidenseket Log Analytics riasztásokból, illetve a naplófájlokból, illetve az Azure-riasztásokból is létrehozhatja ebben a megtekintő példányban.

## <a name="next-steps"></a>További lépések

* [ITSM-csatoló áttekintése](itsmc-overview.md)
* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)