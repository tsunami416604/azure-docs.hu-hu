---
title: SharePoint-farm biztonsági és biztonsági másolatot aDPM segítségével az Azure-ba
description: Ez a cikk áttekintést nyújt egy SharePoint-farm Azure-farmDPM/Azure Backup kiszolgálóvédelméről
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054115"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>SharePoint-farm biztonsági és biztonsági másolatot aDPM segítségével az Azure-ba

A SharePoint-farmról a System Center Data Protection Manager (DPM) használatával ugyanúgy biztonsági másolatot kell tartania a Microsoft Azure-ban, mint más adatforrásokról. Az Azure Backup rugalmasságot biztosít a biztonsági mentésütemezésben napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és különböző biztonsági mentési pontok megőrzési házirend-beállításait biztosítja. A DPM lehetővé teszi a helyi lemezmásolatok gyors helyreállítási idő célok (RTO) tárolására, valamint a másolatok tárolására az Azure-ba a gazdaságos, hosszú távú megőrzés érdekében.

A SharePoint azure-ba való biztonsági mentése a DPM-mel nagyon hasonló folyamat, mint a SharePoint helyi biztonsági mentése A DPM-re. Az Azure-ral kapcsolatos szempontokat ebben a cikkben ismertetjük.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek

A támogatott SharePoint-verziók, illetve a biztonsági mentésükhöz szükséges DPM-verziók felsorolását a [Miről tud biztonsági másolatot készíteni a DPM?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup) című cikk tartalmazza.

## <a name="before-you-start"></a>Előkészületek

Néhány dolgot meg kell erősítenie, mielőtt biztonsági másolatot szeretne a SharePoint-farmról az Azure-ba.

### <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, győződjön meg arról, hogy megfelelt a Microsoft Azure Backup használatának minden [előfeltételének](backup-azure-dpm-introduction.md#prerequisites-and-limitations) a számítási feladatok védelme érdekében. Az előfeltételek néhány feladata: hozzon létre egy biztonsági tárolót, töltse le a tároló hitelesítő adatait, telepítse az Azure Backup Agentet, és regisztrálja a DPM/Azure Backup Servert a tárolóval.

További előfeltételek és korlátozások a [SharePoint biztonsági másolatot a DPM-mel](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations) című cikkben találhatók.

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A SharePoint-farm biztonsági mentéséhez konfigurálja a SharePoint-védelmet a ConfigureSharePoint.exe segítségével, majd hozzon létre egy védelmi csoportot a DPM-ben. További információt a [Biztonsági másolat konfigurálása](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) a DPM dokumentációjában talál.

## <a name="monitoring"></a>Figyelés

A biztonsági mentési feladat figyeléséhez kövesse a [DPM biztonsági mentésének figyelése](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring) című útmutatóutasításait.

## <a name="restore-sharepoint-data"></a>SharePoint-adatok helyreállítása

Ha tudni szeretné, hogy miként állíthatja vissza a SharePoint-elemeket a DPM szolgáltatással rendelkező lemezről, olvassa el a [SharePoint-adatok visszaállítása ( SharePoint-adatok visszaállítása ) (SharePoint-elemek visszaállítása).](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data)

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>SharePoint-adatbázis visszaállítása az Azure-ból a DPM használatával

1. SharePoint-tartalomadatbázis helyreállításához tallózzon a különböző helyreállítási pontok között (ahogy azt korábban is látható), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![DPM SharePoint-védelem8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kattintson duplán a SharePoint helyreállítási pontjára a SharePoint-katalógusra vonatkozó rendelkezésre álló információk megjelenítéséhez.

   > [!NOTE]
   > Mivel a SharePoint-farm hosszú távú megőrzési védelem alatt áll az Azure-ban, a DPM-kiszolgálón nem érhetők el katalógusadatok (metaadatok). Ennek eredményeképpen, amikor egy adott sharepoint-tartalom-adatbázist helyre kell állítotton, újra katalogizálnia kell a SharePoint-farmot.
   >
   >
3. Kattintson **az Újrakatalógus gombra.**

    ![DPM SharePoint-védelem10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik **a Felhőrekra katalogizálás** állapotablaka.

    ![DPM SharePoint-védelem11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot *sikeresre*változik. Kattintson a **Bezárás** gombra.

    ![DPM SharePoint-védelem12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. A tartalom-adatbázis szerkezetének lekért menüpontjában a **DPM-helyreállítás** lapon megjelenő SharePoint-objektumra kattintva. Kattintson a jobb gombbal az elemre, majd kattintson a **Helyreállítás parancsra.**

    ![DPM SharePoint-védelem13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikk korábbi helyreállítási lépéseit a SharePoint-tartalom-adatbázis lemezről való helyreállításához.

## <a name="switching-the-front-end-web-server"></a>Az előtér-webkiszolgáló váltása

Ha egynél több előtér-webkiszolgálóval rendelkezik, és a DPM által a farm védelmére használt kiszolgálót szeretné átváltani, kövesse [az Előtér-webkiszolgáló váltása című rész utasításait.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server)

## <a name="next-steps"></a>További lépések

* [Azure Backup Server és DPM – gyakori kérdések](backup-azure-dpm-azure-server-faq.md)
* [A System Center Data Protection Manager hibaelhárítása](backup-azure-scdpm-troubleshooting.md)
