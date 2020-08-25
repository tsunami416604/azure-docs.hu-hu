---
title: A Microsoft Azure Recovery Services-(MARS-) ügynök frissítése
description: Ismerje meg, hogyan frissítheti a Microsoft Azure Recovery Services (MARS) ügynököt.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 1f4fd5074b0c6195742935e0cf0e742a00a3549e
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827324"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS-) ügynök frissítése

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

* A MARS-ügynök korábbi verzióit tartalmazó kiszolgálók azonosítása
* A MARS-telepítések frissítése ezeken a kiszolgálókon

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>A MARS-ügynök korábbi verzióit tartalmazó kiszolgálók azonosítása

Azure Backup-ügynök és az Azure Backup Server telepítése esetén:

1. Navigáljon ahhoz a Recovery Services-tárolóhoz, amelyen regisztrálta azokat a kiszolgálókat, amelyek biztonsági mentése az ügynök régebbi verzióival lehetséges. A régebbi Azure Backup ügynökökkel rendelkező tárolók listáját a Azure Backup frissítési riasztások az Azure-ban című részében találja.
1. A Recovery Services-tároló bal oldali **Beállítások** szakaszában válassza a **kezelés** szakasz **biztonsági mentési infrastruktúra** elemét.
1. Ha Azure Backup kiszolgáló telepítése során telepített Azure Backup-ügynököket szeretné felderíteni, ugorjon a **felügyeleti kiszolgálók**területen lévő **biztonságimásolat-kezelési kiszolgálók** elemre. Ez a lista azokat a kiszolgálókat sorolja fel, amelyeken Azure Backup Server-telepítések szerepelnek a társított Azure Backup-ügynök verziószámával együtt.

    ![A Azure Backup Server-telepítések részeként telepített MARS-ügynökök listája](./media/upgrade-mars-agent/backup-management-servers.png)

1. A Microsoft Azure Recovery Services-(MARS-) ügynök telepítéséhez vagy a Azure Backup-ügynökhöz tartozó ügynökök verzióinak vizsgálatához a **felügyeleti kiszolgálók**területen válassza a **védett kiszolgálók** lehetőséget. Ezután válassza ki **Azure Backup ügynököt** a biztonsági mentési felügyelet típusa területen. Ez a lista azokat a kiszolgálókat sorolja fel, amelyeken Azure Backup ügynök telepítései szerepelnek, valamint a telepítés verziószáma.

    ![Azon kiszolgálók listája, amelyeken telepítve van a MARS-ügynök](./media/upgrade-mars-agent/protected-servers.png)

1. A Azure Backup ügynök verzió oszlopának rendezéséhez **kattintson a Mars** -ügynök telepítése vagy az **Azure Backup ügynök verziója** oszlopra az Azure Backup Server telepítésekor.

1. Az előző lépéssel megadhatja azoknak a Azure Backup kiszolgálóknak a listáját, amelyeken a 2.0.9083.0-nál alacsonyabb verziójú, illetve az ügynök verziója üresként szerepel. Ezek azok a kiszolgálók, amelyeken frissíteni kell a Azure Backup ügynököket.

## <a name="update-the-mars-agent-installation-on-the-server"></a>A MARS-ügynök telepítésének frissítése a kiszolgálón

Miután azonosította a Azure Backup ügynök frissítését igénylő kiszolgálókat, hajtsa végre a következő lépéseket az egyes azonosított kiszolgálókon (az Azure Backup Server vagy a MARS-ügynök használatával). Az alábbi lépéseket követve [töltse le az Azure Backup-ügynök legújabb verzióját](https://aka.ms/azurebackup_agent) .

1. Kattintson egy olyan sorra, amelynek Azure Backup ügynöke alacsonyabb, mint 2.0.9083.0 vagy üres. Ekkor megnyílik a kiszolgáló részletei képernyő.

    ![Elavult ügynök-verziókkal rendelkező védett kiszolgálók](./media/upgrade-mars-agent/old-agent-version.png)

    ![Elavult ügynök-verziókkal rendelkező Azure Backup-kiszolgálók](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Kattintson a **Kapcsolódás** lehetőségre, hogy egy távoli asztali kapcsolati fájlt kapjon a kiszolgálóhoz való kapcsolódáshoz, vagy közvetlenül kapcsolódjon a kiszolgálóhoz a kiszolgáló távoli asztal kapcsolatán keresztül.

    ![Kapcsolódás a kiszolgálóhoz távoli asztali kapcsolaton keresztül](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Ha a felsorolt kiszolgáló nem létezik vagy már le van szerelve, figyelmen kívül hagyhatja az alábbi további lépéseket, és kihagyhatja a következő kiszolgálót.

1. Adja meg a rendszergazdai bejelentkezési adatait, és jelentkezzen be.

1. Ha a kiszolgáló vagy a kiszolgáló proxyja korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a kiszolgálón vagy proxyn a tűzfal beállításai úgy vannak konfigurálva, hogy engedélyezzék az Ön által használt Azure-felhőnek megfelelő URL-címet:

    Azure-felhő | URL-cím
    -- | ---
    Azure Cloud (nyilvános) |   `https://login.windows.net`
    Azure China 21Vianet-felhő   | `https://login.chinacloudapi.cn`
    Azure USA-beli kormányzati felhő |   `https://login.microsoftonline.us`
    Azure German Cloud  |  `https://login.microsoftonline.de`

1. Másolja az Azure Backup-ügynök frissítési telepítőjét a kiszolgálóra.

    ![Az Azure Backup-ügynök frissítési telepítőjének másolása kiszolgálóra](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Indítsa el a telepítőt. Megnyílik a Microsoft Azure Recovery Services ügynök frissítése varázsló.

    ![A Microsoft Azure Recovery Services ügynök frissítése varázsló](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Frissítés** gombra.

    ![Az Microsoft Azure Recovery Services-ügynök telepítése](./media/upgrade-mars-agent/upgrade-installation.png)

1. A végső megerősítési képernyő azt jelzi, hogy a Azure Backup ügynök frissítése sikeresen megtörtént.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>A System Center Data Protection Manager (SC DPM) ügyfelei számára

Ha telepítette az Azure Backup-ügynököket a System Center Data Protection Manager (SC DPM) kiszolgálókra, az alábbi lépéseket követve azonosíthatja, hogy a DPM-kiszolgálóknak szüksége van-e az Azure Backup-ügynök frissítésére:

1. Jelentkezzen be rendszergazdaként az SC DPM-kiszolgálóra.
2. Nyissa meg a DPM-konzolt.
3. A konzol bal alsó navigációs sávján kattintson a **felügyelet** elemre.
4. A bal oldali navigációs sávon megjelenő információk között keresse meg a Azure Backup ügynök verziójának adatait.
5. Ha a verzió alacsonyabb, mint a 2.0.9083.0, töltse le a legújabb Azure Backup Agent-telepítőt, és futtassa a telepítőt a DPM-kiszolgálón a Azure Backup-ügynök frissítéséhez.

Ismételje meg a fenti lépéseket a környezetben lévő összes DPM-kiszolgálón.

## <a name="next-steps"></a>További lépések

[A Windows rendszerű gépek biztonsági mentésének ismertetése a Azure Backup Mars-ügynök használatával](backup-windows-with-mars-agent.md)
