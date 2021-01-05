---
title: Cherwell összekötése IT-szolgáltatásmenedzsmenti csatoló
description: Ez a cikk azt ismerteti, hogyan Cherwell a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) Azure Monitor a ITSM-munkaelemek központilag figyeléséhez és kezeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 73fc13cf2a49d7cacd7540d06c6d0afd9cea68e5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729686"
---
# <a name="connect-cherwell-with-it-service-management-connector"></a>Cherwell összekötése IT-szolgáltatásmenedzsmenti csatoló

Ez a cikk azt ismerteti, hogyan konfigurálható a Cherwell-példány és a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) közötti kapcsolat a munkaelemek központilag felügyelhető Log Analyticsban.

> [!NOTE]
> Javasoljuk, hogy a Cherwell és a felkínált ügyfeleinknek a [webhook műveleteit](./action-groups.md#webhook) a Cherwell és az Előa végpontot használják egy másik megoldásként az integrációhoz.

A következő szakaszokban részletesen ismertetjük, hogyan csatlakoztathatók a Cherwell-termékek az Azure-beli ITSMC.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

- A ITSMC telepítve van. További információ: [a it-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása](./itsmc-definition.md#add-it-service-management-connector).
- Ügyfél-azonosító létrehozva. További információ: [a Cherwell ügyfél-azonosítójának előállítása](#generate-client-id-for-cherwell).
- Felhasználói szerepkör: rendszergazda.

## <a name="connection-procedure"></a>Csatlakoztatási eljárás

Cherwell-kapcsolatok létrehozásához kövesse az alábbi eljárást:

1. A Azure Portalban lépjen a **minden erőforrás** elemre, és keresse meg a **ügyfélszolgálati (YourWorkspaceName)**

2. A **munkaterület-ADATforrások** területen kattintson az **ITSM-kapcsolatok** elemre.
    ![Új kapcsolat](media/itsmc-connections/add-new-itsm-connection.png)

3. A jobb oldali ablaktábla tetején kattintson a **Hozzáadás** gombra.

4. Adja meg az adatokat az alábbi táblázatban leírtak szerint, majd kattintson az **OK** gombra a kapcsolódás létrehozásához.

> [!NOTE]
> Ezeket a paramétereket kötelező megadni.

| **Mező** | **Leírás** |
| --- | --- |
| **Kapcsolat neve**   | Írja be annak a Cherwell-példánynak a nevét, amelyhez csatlakozni szeretne a ITSMC.  Ezt a nevet később is használhatja, ha munkaelemeket konfigurál ebben a ITSM/részletes log Analytics nézetben. |
| **Partner típusa**   | Válassza a **Cherwell lehetőséget.** |
| **Felhasználónév**   | Írja be azt a Cherwell-felhasználónevet, amely tud csatlakozni a ITSMC. |
| **Jelszó**   | Írja be a felhasználónévhez tartozó jelszót. **Megjegyzés:** A rendszer csak a hitelesítési jogkivonatok létrehozásához használja a felhasználónevet és a jelszót, és a ITSMC szolgáltatásban bárhol nem tárolja őket.|
| **Kiszolgáló URL-címe**   | Írja be annak a Cherwell-példánynak az URL-címét, amelyhez csatlakozni szeretne a ITSMC. |
| **Ügyfél-azonosító**   | Adja meg az ügyfél-azonosítót a Cherwell-példányban létrehozott, a kapcsolatok hitelesítéséhez.   |
| **Adatszinkronizálási hatókör**   | Válassza ki azokat a Cherwell munkaelemeket, amelyeket szinkronizálni szeretne a ITSMC-on keresztül.  Ezeket a munkaelemeket a rendszer a log analyticsbe importálja.   **Beállítások:**  Incidensek, módosítási kérelmek. |
| **Adatszinkronizálás** | Adja meg az elmúlt napok számát, amelyből az adatok származnak. **Maximális korlát**: 120 nap. |
| **Új konfigurációs elemek létrehozása a ITSM-megoldásban** | Akkor válassza ezt a lehetőséget, ha a ITSM termékben szeretné létrehozni a konfigurációs elemeket. Ha be van jelölve, a ITSMC létrehozza az érintett CIs-t konfigurációs elemekként (nem létező CIs esetén) a támogatott ITSM-rendszeren. **Alapértelmezett**: letiltva. |

![Cherwell-kapcsolatok](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Sikeres csatlakozás és szinkronizálás**:

- A Cherwell-példány kiválasztott munkaelemeit az Azure **log Analyticsba** importálja a rendszer. A munkaelemek összegzését a IT-szolgáltatásmenedzsmenti csatoló csempén tekintheti meg.

- Incidenseket hozhat létre Log Analytics riasztásokból, illetve a naplóbejegyzésekből vagy az Azure-riasztásokból ebben az Cherwell-példányban.

További információ: [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Ügyfél-azonosító előállítása a Cherwell

Az ügyfél-azonosító/-kulcs Cherwell való létrehozásához kövesse az alábbi eljárást:

1. Jelentkezzen be a Cherwell-példányba rendszergazdaként.
2. Kattintson a **Biztonság**  >  **szerkesztése REST API ügyfél beállításai** elemre.
3. Válassza az **új ügyfél**-  >  **titkos kulcs** létrehozása lehetőséget.

    ![Cherwell-felhasználói azonosító](media/itsmc-connections/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>További lépések

* [ITSM-csatoló áttekintése](itsmc-overview.md)
* [ITSM-munkaelemek létrehozása az Azure-riasztásokból](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)