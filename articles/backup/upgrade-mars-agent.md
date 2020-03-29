---
title: A Microsoft Azure Recovery Services (MARS) ügynök frissítése
description: További információ a Microsoft Azure Recovery Services (MARS) ügynök frissítésről.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672878"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services (MARS) ügynök frissítése

Ebben a cikkben megtudhatja, hogyan:

* A MARS-ügynök korábbi verzióival rendelkező kiszolgálók azonosítása
* Mars-telepítések frissítése ezeken a kiszolgálókon

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>A MARS-ügynök korábbi verzióival rendelkező kiszolgálók azonosítása

Az Azure Backup ügynök és az Azure biztonsági mentési kiszolgáló telepítése esetén:

1. Nyissa meg a Helyreállítási szolgáltatások tárolóját, ahol olyan kiszolgálókat regisztrált, amelyekről az ügynök régebbi verziói biztonsági másolatot kaphatnak. Az Azure biztonsági mentési frissítési riasztási riasztásaiban megtalálhatja a régebbi Azure backup-ügynökökkel rendelkező tárolók reprezentatív listáját.
1. A Helyreállítási szolgáltatások tárolóbal oldali **Beállítások** szakaszában válassza a **Biztonsági másolat infrastruktúra lehetőséget** a **Kezelés** szakaszban.
1. Az Azure Backup kiszolgáló telepítésének részeként telepített Azure biztonsági mentési ügynökeinek felderítéséhez nyissa meg a Felügyeleti kiszolgálók csoport **biztonsági mentési felügyeleti kiszolgálóinak** **segédprogramját.** Ez felsorolja azOkat a kiszolgálókat, amelyek rendelkeznek az Azure Backup kiszolgáló telepítésével, valamint a társított Azure Backup-ügynök verziószámát.

    ![Az Azure Backup kiszolgáló telepítésének részeként telepített MARS-ügynökök listája](./media/upgrade-mars-agent/backup-management-servers.png)

1. A Microsoft Azure Recovery Services (MARS) ügynök-telepítéseivagy az Azure Backup ügynök ügynökei ügynökverzióinak ellenőrzéséhez látogasson el a **Felügyelt kiszolgálók** védett **kiszolgálók ra.** Ezután válassza az **Azure Backup ügynök** a Backup Management Type csoportban. Ez felsorolja azOkat a kiszolgálókat, amelyek rendelkeznek az Azure Backup ügynök telepítésével, valamint a telepítés verziószámát.

    ![A MARS-ügynökkel felszerelt kiszolgálók listája](./media/upgrade-mars-agent/protected-servers.png)

1. Rendezze az Azure Backup Agent verzióoszlopát a MARS-ügynök telepítések **ügynöki verziója** oszlopra vagy az **Azure biztonsági másolat ügynökének verziója** oszlopra kattintva az Azure biztonsági másolat készítő kiszolgálóinak telepítéséhez.

1. Az előző lépés ben az Azure Backup-ügynökökkel rendelkező kiszolgálók listáját kapja, amelyek 2.0.9083.0-nál alacsonyabb verzióval vagy üresként felsorolt ügynökverziókkal rendelkeznek. Ezek azok a kiszolgálók, ahol az Azure Backup-ügynököket frissíteni kell.

## <a name="update-the-mars-agent-installation-on-the-server"></a>A MARS-ügynök telepítésének frissítése a kiszolgálón

Miután azonosította az Okat tartalmazó azure-beli biztonságimentési ügynök frissítését igénylő kiszolgálókat, hajtsa végre a következő lépéseket minden egyes azonosított kiszolgálóesetében (az Azure biztonsági mentési kiszolgálójának vagy a MARS-ügynöknek). Az alábbi lépések végrehajtása előtt [töltse le az Azure biztonsági mentési ügynöklegújabb verzióját.](https://aka.ms/azurebackup_agent)

1. Kattintson egy olyan sorra, amelynek Azure Backup ügynöke 2.0.9083.0-nál alacsonyabb vagy üres. Ezzel megnyitja a kiszolgáló részletes képernyőjét.

    ![Védett kiszolgálók elavult ügynökverziókkal](./media/upgrade-mars-agent/old-agent-version.png)

    ![Azure biztonsági mentési kiszolgálók elavult ügynökverziókkal](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Kattintson a **Csatlakozás gombra,** ha távoli asztali kapcsolatfájlt szeretne fogadni a kiszolgálóhoz való csatlakozáshoz, vagy közvetlenül csatlakozhat a kiszolgálóhoz a kiszolgáló távoli asztali kapcsolatán keresztül.

    ![Csatlakozás a kiszolgálóhoz távoli asztali kapcsolaton keresztül](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Ha a felsorolt kiszolgáló nem létezik, vagy le lett szerelve, figyelmen kívül hagyhatja az alábbi lépéseket, és átugorhatja a következő kiszolgálóra.

1. Adja meg a rendszergazdai bejelentkezési adatait, és jelentkezzen be.

1. Ha a kiszolgáló vagy a kiszolgáló proxyja korlátozott internet-hozzáféréssel rendelkezik, győződjön meg arról, hogy a kiszolgáló/proxy tűzfalbeállításai úgy vannak beállítva, hogy engedélyezze a használt Azure-felhőnek megfelelő URL-címet:

    Azure Felhő | URL-cím
    -- | ---
    Azure Cloud (nyilvános) |   `https://login.windows.net`
    Azure China 21Vianet felhő   | `https://login.chinacloudapi.cn`
    Azure AMERIKAI kormányzati felhő |   `https://login.microsoftonline.us`
    Azure német felhő  |  `https://login.microsoftonline.de`

1. Másolja az Azure biztonsági másolat készítőjének frissítőtelepítőjével a kiszolgálóra.

    ![Az Azure biztonságimásolat-készítő kiszolgálóra való másolása](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Indítsa el a telepítőt. Megnyílik a Microsoft Azure Recovery Services Ügynök frissítése varázsló.

    ![A Microsoft Azure Helyreállítási szolgáltatások ügynökének frissítése varázsló](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Kattintson a **Tovább** gombra.

1. Kattintson a **Frissítés** gombra.

    ![A Microsoft Azure helyreállítási szolgáltatások ügynökének telepítése](./media/upgrade-mars-agent/upgrade-installation.png)

1. A végső megerősítő képernyő azt jelzi, hogy az Azure Backup ügynök sikeresen frissítve lett.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>A System Center Data Protection Manager (SC DPM) ügyfelei számára

Ha telepítette az Azure biztonsági mentési ügynökeit a System Center-i adatvédelmi kezelő (SC DPM) kiszolgálókra, akkor az alábbi lépéseket kell végrehajtania annak megállapításához, hogy a DPM-kiszolgálóknak szükségük van-e az Azure biztonsági mentési ügynökének frissítésére:

1. Jelentkezzen be az SC DPM-kiszolgálóra rendszergazdaként.
2. Nyissa meg a DPM konzolt.
3. Kattintson a **menedzsment** a bal alsó navigációs a konzol.
4. A bal oldali navigációs navigációs nézetben megjelenő információk között keresse meg az Azure Backup ügynök verzióadatait.
5. Ha a verzió kisebb, mint a 2.0.9083.0, töltse le a legújabb Azure-biztonsági mentési ügynök telepítőt, és futtassa a telepítőt a DPM-kiszolgálón az Azure Backup-ügynök frissítéséhez.

Ismételje meg a fenti lépéseket a környezetösszes DPM-kiszolgálóján.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [készíthet biztonsági másolatot a Windows-gépekről az Azure Backup MARS ügynök használatával](backup-windows-with-mars-agent.md)
